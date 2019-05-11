# Value Objects

## Instalation

Value Object on packagist: https://packagist.org/packages/lbacik/value-object.
To install it you can use [composer](https://getcomposer.org):

    composer require lbacik/value-object

## Example

```php
use Sushi\ValueObject;

class ExampleValueObject extends ValueObject
{
    protected $keys = [
        'firstname',
        'lastname',
        'city',
        'occupation',
    ]
}
```
 
### Validators 

Currently Validators can be defined as an array of strings - fully qualified names of classes that implement 
the interface \Sushi\ValidatorInterface.   

Available validators can be found in https://github.com/lbacik/value-object/tree/master/src/Sushi/Validator

For the time being (however I don't think that there is something missing ;)) the list is as follow:

1. `KeysValidator` - checks whether all keys from key:value table  passed to the VO object during its initialization 
are *legal* keys of this VO (have been declared in its "keys" table). Without turned on this validator each "key:value" 
table can be passed to the VO constructor what means you don't control the VO internal *storage*.  
2. `InitializeValidator` - it require all keys declared in VO's "keys" table being initialize in initial "key:value" 
table (passed to constructor). 

### Keys

This table can be defined as a regular (flat) or associative one. In fact this "base" library does not utilize 
the "values" in case of associative array used here - the meaning of this table is as follow:

1. flat array - values, as in the above example, means the VO keys. 
2. associative array - keys means VO keys, values can be use by custom validators, take a look 
at [value-object-illuminate-validation](https://github.com/lbacik/value-object-illuminate-validation)

## Values validation

The validation rules for keys values can be added with the 
[value-object-illuminate-validation](https://github.com/lbacik/value-object-illuminate-validation) extension, 
which introduce support for Illuminate Validation class - more details can be found at 
https://github.com/lbacik/value-object-illuminate-validation.

## More Extended Example

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

