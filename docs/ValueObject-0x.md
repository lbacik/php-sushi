# Value Object (versions 0.x)

## Base Information about this particular implementation

Each value object uses Key:Value store (associative array) to store its data. Because on its low level the ValueObject 
class implements ArrayAccess interface these values can be accessed as:

```php
$valueObject[$valueKey];
// or
$valueObject->offsetGet($valueKey);
```

Getters (which use is probably much "cleaner") require to be declared explicitly, e.g.:

```php
class ExampleVO extends ValueObject
{
    public const NAME = 'name';
    
    ...
    
    public function name(): string
    {
        return $this->offsetGet(self::NAME);
    } 
}
```

Currently there are defined Tree "main" Value Object's types:

1. `ValueObject` - base type, uses `arrays` are arguments (however function set can now receive also named arguments)
2. `ValueObjectDKeys`
3. `ValueObjectPKeys`

## Examples


### Value Object with dynamically created keys

You can define the ValueObject like this:

```php
$valueObject = new ValueObjectDKeys(name: "Foo", age: 30);
```

It is totally fine! 

```php
$valueObject2 = $valueObject->set(age: 25);

$result = $valueObject->isEqual($valueObject2); // false

$valueObject3 = $valueObject2->set(age: 30);

$result = $valueObject->isEqual($valueObject3); // true

$valueObject4 = new ValueObjectDKeys(name: "Foo", age: 30, city: "NY");

$result = $valueObject->isEqual($valueObject4); // false
```

It is quite simple and fast but there is no Invariants / Validators used / defined - so, there is also no rules 
regarding the values types etc... e.g.:


```php 
$valueObject5 = new ValueObjectDKeys(name: "Foo", age: "30");

$result = $valueObject->isEqual($valueObject5); // false
```

Should `$valueObject5` be at all considered as a Valid one?


### Invariants

Invariants are a "new" way (in the context of this library) to define the rules that new created Value Object should
pass to be considered as a Valid one (and to be at all created).

```php
class ValueObjectFive extends ValueObjectDKeys
{
    public const NAME = 'name';
    public const AGE = 'age';

    #[Invariant]
    protected function validateName(): void
    {
        assertIsString($this->offsetGet(self::NAME));
    }

    #[Invariant]
    protected function validateAge(): void
    {
        $age = $this->offsetGet(self::AGE);

        assertIsInt($age);
        assertGreaterThanOrEqual(0, $age);
    }
}
```


## Base class Example (with use of validators) 

"Validators" (the same that are used in `ValueObjectPKeys`) allows only to check the allowed keys and their 
existence.

{% gist 33ffb3c1f32b5a8db4de083719fa602c %}

## Validators 

Currently Validators can be defined as an array of strings - fully qualified names of classes that implement 
the interface \Sushi\ValidatorInterface.   

Available validators can be found in https://github.com/lbacik/value-object/tree/master/src/Sushi/Validator

For the time being (however I don't think that there is something missing ;)) the list is as follow:

1. `KeysValidator` - checks whether all keys from key:value table  passed to the VO object during its initialization 
are *legal* keys of this VO (have been declared in its "keys" table). Without turned on this validator each "key:value" 
table can be passed to the VO constructor what means you don't control the VO internal *storage*.  
2. `InitializeValidator` - it require all keys declared in VO's "keys" table being initialize in initial "key:value" 
table (passed to constructor). 

## Keys

This table can be defined as a regular (flat) or associative one. In fact this "base" library does not utilize 
the "values" in case of associative array used here - the meaning of this table is as follow:

1. flat array - values, as in the above example, means the VO keys. 
2. associative array - keys means VO keys, values can be use by custom validators, take a look 
at [value-object-illuminate-validation](https://github.com/lbacik/value-object-illuminate-validation)
