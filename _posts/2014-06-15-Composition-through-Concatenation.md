# Composition through Concatenation
#### *How editing in Vim is like writing a program in Forth*

Composition is a key aspect of successful design, both in art and engineering.  Breaking creations down into smaller, reusable patterns lends familiarity to novel designs, providing both a starting point and guiding path for evolving new user experiences.  The value of composition is especially apparent in software engineering, where both the costs of developing as well as maintaining and improving an application over time have a significant impact on the lifetime profitability of the product.  A particular type of composition, which I will refer to as Concatenative Composition, has proven to be particularly flexible and concise.

## Types of composition

There are several types of composition commonly used in software development, which are easily expressed in most modern languages.
The first, *aggregation*, forms new components by assembling sub-components, allowing the sub-components to be referenced elsewhere in the program.  Dictionaries and structs are examples of aggregations.  In contrast, the sub-components of a *conglomeration* lose their identity (to an extent) within the composition.  An implementation of multiple interfaces is a conglomeration because overlapping signatures within the interfaces are elevated to a single concrete implementation.  In an aggregation, the individual sub-components can persist outside of the composition, whereas the sub-components of a conglomeration exist only within the composition itself.

```cs
/* C# */
// Aggregation - Subcomponents retain identity
public class Person {
    public string Name {get; set}
    public Address HomeAddress {get; set;}
}
// Conglomeration - Subcomponents lose their identity within the composition
public class ImAConglomeration : ThingA, ThingB, ThingC {
    @Override
    public A MethodA(){ /* Implementation details */ }
}
```

The next two types of composition follow a more functional style.  Functions that return the same type as the primary parameter they operate on enable *chained* composition, allowing several functions which operate on a single base value to be chained together to build up the desired effect.  The second type, *applicative* composition, defines programs through nested function calls.  In this style, programs are executed from the inside out; first the parameters of a function are evaluated if necessary, then the function itself is called.

```cs
/* C# */
// Chained - One value, the string, is passed through several transformations
var result = "this-is-a-chain".Replace('-', ' ').ToUpper();

// Application - Arguments are evaluated, then passed to the outer function
public static class ApplicativeExample {
    public static void Main(string[] args){
        Console.WriteLine(
           String.Join(", ", args.Select((x) => x.ToLower()).ToArray()));
    }
}
```

## Concatenative Composition

The applicative style described above is one type of function composition, where functions are evaluated with concrete parameters, passing their result as parameters to the next function, and so on.  It is possible, however, to compose functions without mentioning the parameters at all.  To illustrate this, consider the basic task of defining a function which filters a list then counts up the results.  In C#, it might look something like the following.

```cs
/* C# */
public static int CountWhere<A>(IEnumerable<A> col, Func<A, bool> pred){
    return col.Where(pred).Count();
}
```

Notice how the function specifies not only what to do (filter then count), by how to do it.  The function explicitly names all of the parameters, maps which parameters belong to each function call, and calls the functions in the right order.  Expanding the task described above   to filter and stringify instead results in very similar code

```cs
/* C# */
public static string printWhere<A>(IEnumerable<A> col, Func<A, bool> pred {
    return col.Where(pred).ToString();
}
```

The majority of the code is exactly the same, specifying how to map parameters and outputs.  Each new composition we define requires this same pattern to be repeated.  We could define a helper function to map the parameters appropriately

```cs
/* C# */
public static Func<T1, T2, T4> Compose<T1, T2, T3>(Func<T1, T2, T3> f1, Func<T3, T4> f2){
    return (paramT1, paramT2) => f2(f1(paramT1, paramT2));
}
// If When and Count were defined nicely as Funcs
var countWith = Compose(When, Count);
countWith(new List(){1, 2, 3, 4}, (x) -> x%2==0);
// returns 2
```

But we would have to redefine this helper function to compose functions with different numbers of parameters, for example to compose two unary functions.

```cs
public static Func<T1,T2, T3> Compose<T1, T2, T3>(Func<T1, T2> f1, Func<T2, T3> f2) {
    return (paramT1) => f2(f1(paramT1))
}
```

over and again for every combination of arities.  Certain types of programming languages, called concatenative languages, simplify this process.

```factor
// Cat
define countWith { filter count }
[ 1 2 3 4 ] list [ 2 mod 0 eq ] countWith
// returns 2
```

In concatenative languages, function composition is represented by simply placing the function names next to each other (or concatenating them, hence the name).  Because of how functions are defined and executed in these languages, no mention of parameters is necessary; the mapping between parameters and outputs is completely inferred.

There are obvious benefits to reducing code duplication, as mentioned above, using concatenative programming, but the same benefit could be achieved with some clever code generation to produce the necessary Compose functions or  by defining Compose using reflection to allow it to work with any combination of arguments (at the cost of compile-time type safety).  So, why write concatenative programs?

## Example: Editing in Vim

The Vim text editor is a perfect example of where concatenative composition provides clear benefit.  It is undeniable that, despite many technological advances and inspite of any  internal flaws, Vim has and continues to be a very successful tool for editing code, in no small part because of it’s command system.

Vim is built around one core ideal: efficiency.  Vim and its predecessor, Vi, were designed to work efficiently over slow terminal connections, which might have significant latency when responding to individual keystrokes.  Due to this restriction, Vim incorporates a command mode, allow users to operate on arbitrary chunks of text, rather than on a character-by-character basis.  For example, the command `ciw` deletes from the previous word (or newline) to the next word (or newline), leaving the white-space boundaries intact, and places the cursor where the beginning of where the word would have begun.
This command, which is seemingly arbitrary to new users, is actually made up of three sub-components, `c`, `i`, and `w` respectively, which have been composed with concatenation to form the resulting transformation.  The first component, `c`, describes what we want to do; change the text.  The second component, `i`, describes what portion of the text we want to effect, in this case the inside, preserving any white space boundaries.  Lastly, the `w` specifies how to select the text from the current position, in this case by selecting the current “word”.

Most commands in Vim follow this pattern, specifying an Action, Description, and Target to act on.  By adhering to this pattern, Vim sub-commands benefit from unprecedented reusability.  To illustrate this, consider adding three new subcommands: the `d` Action, which deletes but does not move the cursor, the `a` Description, which specifies that we want to include the surrounding whitespace, and lastly the `s` Target, which represents a sentence from one punctuation marking or newline to the next.  We can now concatenate any combination of available Actions, Descriptions, and Targets for predictable outcomes.

| Action | Description | Target | Result |
|--------|-------------|--------|--------|
| C | I | W | Change inside word |
| C | I | S | Change inside sentence |
| C | A | W | Change around word |
| C | A | S | Change around sentence |
| D | I | W | Delete inside word |
| D | I | S | Delete inside sentence |
| D | A | W | Delete around word |
| D | A | S | Delete around sentence |

So, with two actions, descriptions, and targets we can create eight separate commands.  Extrapolating this example, with three of each we would have 27 distinct commands, and with ten of each, 1,000 commands.  By learning the meaning of 30 “words” in Vim (Actions, Descriptions and Targets) we can construct 1,000 distinct “phrases” describing how we want to transform the text.  Even though modern terminal connections are fast enough to easily support much more complicated forms of editing, Vim is still a popular choice of text editor because of the flexibility of its command pattern .

In order to support this kind of combinatorial growth of commands, Vim utilizes concatenative combination as an evaluation strategy.  The evaluation loop in Vim waits to execute until a Target sub-component is input, optionally modifying the execution behavior according to some Description and Action by composing their effects.  This kind of evaluation strategy would quickly become cumbersome if either end-users or the developers of the system had to explicitly map parameters between compositions.  Instead, by relying on concatenative programming, the compositions become trivial to infer.

## The dark side

Concatenative composition does have some drawbacks.  Inferring the types of the functions involved and how to map inputs and outputs between them greatly reduces the amount of boilerplate code necessary define new functions, but also means that the programmer no longer has the visual cues provided by type information to describe what a composition does, relying instead on descriptive naming.

Further, most languages supporting concatenative programming do so using postfix stack-based transformations, which allow for a unified execution model and efficient execution.  In applicative languages, the order of parameters is given explicitly when a function is called.  In stack-based languages, all functions operate on a single parameter, the current stack, which contains the parameters needed by the function in order; that is, functions in stack-based languages assume the stack is in the correct order when they’re called.  This means that code must be organized around maintaining the proper stack order or, more commonly, must be decorated with additional code for the sole purpose of re-ordering the stack to meet the argument order expected by the next function call.

These two points, together, can lead to obfuscated code, especially for programmers new to concatenative and stack-based languages.  In these languages, the programmer is forced to keep track of how a series of functions composes in an abstract way, rather than how an individual function affects its inputs.  In the programming language Factor, the mathematical function `f( x y z ) = y2 + x2 − |y|`` becomes

```factor
 // Factor
: f [ drop dup dup × swap abs rot3 dup × swap − + ]
```

due to the y parameter being used multiple times, the `z` parameter not being used at all, and the inconsistency between the order the parameters are provided and the order they are used in the execution.  The majority of the definition is spent manipulating the stack, as opposed to performing any useful computation, making the function difficult to read.

To mitigate the first issue, many concatenative languages support stack effect annotations; a form of documentation for noting what the top of the stack looks like before and after a function call.  This idea has been expanded on in the Cat programming language to include support for full-fledged type inference, allowing programmers to either specify a desired type signature or to infer it.  For the second issue, many languages, including Factor, support named local parameters, allowing designers to selectively break the arguments-by-order convention on a case by case bases.  Using named local parameters, our example above becomes

```factor
// Factor
:: f | x y z | y y * x x * + y abs - ;
```

Still not an exact match for the mathematical notation, but at least it’s clear what parameters are used with a given operation.

## Too long; Didn’t read

Like other forms of composition, concatenative composition can greatly reduce code duplication for certain types of programs.  Most popular modern languages, like Java and C#, can only emulate this style of composition through complex code generation or self-reflective runtime analysis.  Utilizing a concatenative programming language makes it possible to easily implement nested function compositions in a clear and concise way, enabling a distinct style of programming focused on how functions are combined, rather than how data flows between them.  By emulating the concatenative style, the Vim programming editor is able to implement commands in a combinatorial fashion, such that adding simple, new definitions of Actions, Descriptions, and Targets can lead to an exponential growth in the total number of commands recognized by the system.  Modern concatenative languages still require a significant shift in how programs are written and reasoned about, but continue to experiment with ways of easing the transition for new users.