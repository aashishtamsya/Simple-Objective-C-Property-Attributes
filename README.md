# Simple Objective-C Property Attributes

[![Platform](https://img.shields.io/badge/platform-ios-lightgrey.svg)]()
[![Programming Language](https://img.shields.io/badge/language-objective--c-ff69b4.svg)]()
[![Scrutinizer Build](https://img.shields.io/scrutinizer/build/g/filp/whoops.svg?maxAge=2592000)]()

A simple way to understand what all the different attributes of a property means in Objective-C.

# Contents

-	[List of Property Attributes](#list-of-property-attributes)
-	[atomic](#atomic)
	-	[Example of atomic attribute](#example-of-atomic-attribute)
-	[nonatomic](#nonatomic)
	-	[Example of nonatomic attribute](#example-of-nonatomic-attribute)
	-	[Explaination of nonatomic attribute](#explaination-of-nonatomic-attribute)
-	[strong](#strong)
	-	[Example of strong attribute](#example-of-strong-attribute)
-	[weak](#weak)
	-	[Example of weak attribute](#example-of-weak-attribute)
	-	[Explaination of weak attribute](#explaination-of-weak-attribute)
-	[retain = strong](#retain-=-strong)
	-	[Example of retain attribute](#example-of-retain-attribute)
-	[assign](#assign)
	-	[Example of assign attribute](#example-of-assign-attribute)
-	[unsafe_unretained](#unsafe_unretained)
	-	[Example of unsafe_unretained attribute](#example-of-unsafe_unretained-attribute)
-	[copy](#copy)
	-	[Example of copy attribute](#example-of-copy-attribute)
-	[readonly](#readonly)
	-	[Example of readonly attribute](#example-of-readonly-attribute)
-	[readwrite](#readwrite)
	-	[Example of readwrite attribute](#example-of-readwrite-attribute)		
-	[Installation](#installation)
-	[Contribution](#contribution)
-	[Credits](#credits)
-	[License](#license)


## List of Property Attributes

*	atomic //default
*	nonatomic
*	strong=retain //default
*	weak
*	retain
*	assign //default
*	unsafe_unretained
*	copy
*	readonly
*	readwrite //default

## atomic
 
*	Atomic means only one thread access the variable(static type).
*	Atomic is thread safe.
*	but it is slow in performance
*	atomic is default behavior
*	Atomic accessors in a non garbage collected environment (i.e. when using retain/release/autorelease) will use a lock to 
	 ensure that another thread doesn't interfere with the correct setting/getting of the value.
*	it is not actually a keyword.
	
#### Example of atomic attribute

```objective-c

@property (retain) NSString *name;

@synthesize name;

```

## nonatomic

*	Nonatomic means multiple thread access the variable(dynamic type).
*	Nonatomic is thread unsafe.
*	but it is fast in performance
*	Nonatomic is NOT default behavior,we need to add nonatomic keyword in property attribute.
*	it may result in unexpected behavior, when two different process (threads) access the same variable at the same time.

#### Example of nonatomic attribute

```objective-c

@property (nonatomic, retain) NSString *name;

@synthesize name;

```

#### Explaination of nonatomic attribute

Suppose there is an atomic string property called "name", and if you call [self setName:@"A"] from thread A, 
call [self setName:@"B"] from thread B, and call [self name] from thread C, then all operation on different thread will be performed serially which means if one thread is executing setter or getter, then other threads will wait. This makes property "name" read/write safe but if another thread D calls [name release] simultaneously then this operation might produce a crash because there is no setter/getter call involved here. Which means an object is read/write safe (ATOMIC) but not thread safe as another threads can simultaneously send any type of messages to the object. Developer should ensure thread safety for such objects.


If the property "name" was nonatomic, then all threads in above example - A,B, C and D will execute simultaneously producing any unpredictable result. In case of atomic, Either one of A, B or C will execute first but D can still execute in parallel.


## strong

*	it says "keep this in the heap until I don't point to it anymore"
*	in other words " I'am the owner, you cannot dealloc this before aim fine with that same as retain"
*	You use strong only if you need to retain the object.
*	By default all instance variables and local variables are strong pointers.
*	We generally use strong for UIViewControllers (UI item's parents)
*	strong is used with ARC and it basically helps you , by not having to worry about the retain count of an object. ARC automatically releases it for you 	 when you are done with it.Using the keyword strong means that you own the object.

#### Example of strong attribute

```objective-c
@property (strong, nonatomic) ViewController *viewController;

@synthesize viewController;
```

## weak

*	it says "keep this as long as someone else points to it strongly"
*	the same thing as assign, no retain or release
*	A "weak" reference is a reference that you do not retain.
*	We generally use weak for IBOutlets (UIViewController's Childs).This works because the child object only 
	 needs to exist as long as the parent object does.
*	a weak reference is a reference that does not protect the referenced object from collection by a garbage collector.
*	Weak is essentially assign, a unretained property. Except the when the object is deallocated the weak pointer is automatically set to nil

#### Example of weak attribute
```objective-c
@property (weak, nonatomic) IBOutlet UIButton *myButton;

@synthesize myButton;
```

#### Explaination of weak attribute

Imagine our object is a dog, and that the dog wants to run away (be deallocated).
Strong pointers are like a leash on the dog. As long as you have the leash attached to the dog, the dog will not run away. If five people attach their leash to one dog, (five strong pointers to one object), then the dog will not run away until all five leashes are detached.
Weak pointers, on the other hand, are like little kids pointing at the dog and saying "Look! A dog!" As long as the dog is still on the leash, the little kids can still see the dog, and they'll still point to it. As soon as all the leashes are detached, though, the dog runs away no matter how many little kids are pointing to it.
As soon as the last strong pointer (leash) no longer points to an object, the object will be deallocated, and all weak pointers will be zeroed out.
When we use weak?
The only time you would want to use weak, is if you wanted to avoid retain cycles 
(e.g. the parent retains the child and the child retains the parent so neither is ever released).


## retain = strong

*	it is retained, old value is released and it is assigned
*	retain specifies the new value should be sent -retain on assignment and the old value sent -release
*	retain is the same as strong.
*	apple says if you write retain it will auto converted/work like strong only.
*	methods like "alloc" include an implicit "retain"

#### Example of retain attribute

```objective-c
@property (nonatomic, retain) NSString *name;

@synthesize name;
```

## assign 

*	assign is the default and simply performs a variable assignment
*	assign is a property attribute that tells the compiler how to synthesize the property's setter implementation
*	I would use assign for C primitive properties and weak for weak references to Objective-C objects.

#### Example of assign attribute

```objective-c
@property (nonatomic, assign) NSString *address;

@synthesize address;
```

## unsafe_unretained

*	unsafe_unretained is an ownership qualifier that tells ARC how to insert retain/release calls
*	unsafe_unretained is the ARC version of assign.

#### Example of unsafe_unretained attribute

```objective-c
@property (nonatomic, unsafe_unretained) NSString *nickName;

@synthesize nickName;
```

## copy

*	copy is required when the object is mutable.
*	copy specifies the new value should be sent -copy on assignment and the old value sent -release.
*	copy is  like retain returns an object which you must explicitly release (e.g., in dealloc) in non-garbage collected environments.
*	if you use copy then you still need to release that in dealloc.
*	Use this if you need the value of the object as it is at this moment, and you don't want that value to reflect any changes made by other 
	 owners of the object. You will need to release the object when you are finished with it because you are retaining the copy.

#### Example of copy attribute

```objective-c
@property (nonatomic, copy) NSArray *myArray;

@synthesize myArray;
```

## readonly
	
*	declaring your property as readonly you tell compiler to not generate setter method automatically.
*	Indicates that the property is read-only.
*	If you specify readonly, only a getter method is required in the @implementation block. If you use the @synthesize directive in 
	 the @implementation block, only the getter method is synthesized. Moreover, if you attempt to assign a value using the dot syntax, 
	 you get a compiler error.

#### Example of readonly attribute

```objective-c
@property (nonatomic, readonly) NSString *name;

@synthesize name;
```

## readwrite 

*	setter and getter generated.
*	Indicates that the property should be treated as read/write.
*	This attribute is the default.
*	Both a getter and setter method are required in the @implementation block. If you use the @synthesize directive in the implementation 
	 block, the getter and setter methods are synthesized.

#### Example of readwrite attribute

```objective-c
@property (nonatomic, readwrite) NSString *name;

@synthesize name;
```


## Installation

1. Download the zip or clone the repo to your desired directory.

```sh
$ git clone https://github.com/aashishtamsya/Simple-Objective-C-Property-Attributes.git
```

## Contribution

1. Fork it!
2. Create your feature branch: `git checkout -b my-new-feature`
3. Commit your changes: `git commit -am 'Add some feature'`
4. Push to the branch: `git push origin my-new-feature`
5. Submit a pull request 😉 😊


## Credits

Aashish Tamsya [@ChiefAashish](https://www.twitter.com/chiefaashish),
aashish.tamsya@gmail.com

## License

The content of [*Weather App*](https://github.com/aashishtamsya/Weather-App) itself is licensed under the [Creative Commons Attribution 3.0 license](https://creativecommons.org/licenses/by/3.0/us/deed.en_US), and the underlying source code used to format and display that content is licensed under the [MIT license](https://opensource.org/licenses/mit-license.php).

See the [LICENSE](LICENSE.md) file for more info.