## Values validation with Illuminate Validator

### Versions

This library is in fact an extention for **0.x** versions (**only!**) of the [https://github.com/lbacik/value-object](https://github.com/lbacik/value-object) package!

### Usage

The validation rules for keys values can be added with the
[value-object-illuminate-validation](https://github.com/lbacik/value-object-illuminate-validation) extension,
which introduce support for Illuminate Validation class - more details can be found at
https://github.com/lbacik/value-object-illuminate-validation.

### Example of Illuminate Validator use

Lets consider below example:

{% gist 7b4d815454ff01b929ab1beab2947a7e %}

The static `create` function seems to be a good way to force the types of stored values - but, unfortunately,
it is not (or, at least, it doesn't solve this problem at whole) - the reason is the function `set`, which also can
be used to "instantiate" (via clone action) new ValueObject and it obviously does not respect the types set
in `create`.

The solution can be to pass the information about types of particular values in `keys` table - this table can be
checked in "validation" function which is invoked in ValueObject's `__construct` and `set` methods - such a solution
has been implemented in validator: `IlluminateValidationValidator` which is available as a separate package:
[value-object-illuminate-validation](https://github.com/lbacik/value-object-illuminate-validation).

Of course nesting the Value Objects should also work (the comparison function - implemented by `isEqual` method -
can be checked in test: [ValueObjectEqualTest](https://github.com/lbacik/value-object/blob/master/tests/Sushi/ValueObjectEqualTest.php)):

{% gist 79ca11146ca17501f7e025f2c887f42c %}
