# Exercise 9: Ambiguity and Ordering

Up until this point, we've mostly been dealing with macros with a single rule.
We saw earlier that macros can require more than one rule; but so far we've never
had ambiguity in which rule should be followed.

There are, however, multiple circumstances where rules could have ambiguity,
so it's important to understand how macros actually work.

The following is adapted from the [rust documentation on
macros](https://doc.rust-lang.org/reference/macros-by-example.html#transcribing):

 - When a macro is invoked (i.e. someone writes `my_macro!()`), the compiler
   looks for a macro with that name, and tries each rule in turn.
 - To try a rule, it reads through each token in the parser in turn. There are
   three possibilities:
 
   1. the token found matches the matcher. In this case, it keeps parsing the
      next token. If there are no tokens left, and the matcher is complete, then
      the rule matches.
   2. the token found does not match the matcher. In this case, rust tries the
      next rule. If there are no rules left, an error is raised as the macro
      cannot be expanded.
   3. the rule is ambiguous. In other words, it's not clear from *just this
      token* what to do. If this happens, this is an error.
   
 - If it finds a rule that matches the tokens inside the brackets; it starts
   transcribing. *Once a match is found, no more rules are examined*.

Let's have a look at some examples:


``` rust
macro_rules! ambiguity {
    ($($i:ident)* $j:ident) => { };
}

ambiguity!(error); 
```

This example fails because rust is not able to determine what `$j` should be just by looking at
the current token. If rust could look forward, it would see that `$j` must be followed by a `)`,
but it cannot; so it causes an error.

``` rust
macro_rules! ordering {
    ($j:expr) => { "This was an expression" };
    ($j:literal) => { "This was a literal" };
}

let ordering!('a');  // => "This was an expression".
let ordering!(3 + 5);  // => "This was an expression".
```

This example demonstrates an example where rust macros can behave strangely due to
ordering rules -- even though `literal` is a much stricter condition than `expr`,
because `literal`s are `expr`s; the first rule will always match.

# Starting Exercise 9

You're now ready to complete `09_ambiguity_and_ordering`.

This task is a little bit different to previous tasks -- we have given you 
a partially functional macro already; along with some invocations of that macro.

You should adjust the macro's rules and syntax to make sure that you 
achieve the correct behaviour without any ambiguity.