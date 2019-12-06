# Some Clean Code Tips and Tricks
## Clear Naming
Variables, methods, and classes should have a clear naming. Clear naming makes anyone who read the code have a good hint of what it means at first sight. Clear does not mean short, clear means it tells you what the intentions are.
For example below is a function to get first name and last name from fullname and append it with “.”, the purpose of this function is to get username.

```javascript
// code 1
function getName(text) {
	const len = text.split(" ").length;
	return len === 1 ? text : text.split(" ")[0] + text.split(" ")[len-1];
}
```


As we see function above looks correct  and straightforward, but for user who are new to this function, looking at the function name without checking the inner implementation will not get you anywhere. What is `getName()` what is `text` supposed to be? Some reader who don’t really know javascript, they need to search for what is `split` function, thus the readability is not really straightforward.

Now let’s compare with this function:

```javascript
// code 2
function getUsername(fullname) {
	const names = fullname.split(" ");
	const nameLength = text.split(" ").length;
	const username = names[0] + "." + names[nameLength-1]; // <firstname>.<lastname> format
	return nameLength === 1 ? fullname : username;
}
```

From the function name reader can see directly, that this function will generate `username` from a `fullname`.
User can easily skim what results yielded  on each line because it is assigned to a clear named variables, plus added comment. It is also relatively easier to debug and less error prone, reader can check what values each line produced.

Key takeaways:

* Use clear, make-sense naming.
* Name your variables and methods/functions with intent to explain your code.

## Keep It Small
Software development is about breaking one system into smaller features, develops them, and combines them into a system that work each other.  Generally, smaller unit makes it easier for us to read and understand the algorithm used or what it actully does under the hood.

### Class
Smaller Class makes it to be single responsible, it is focused on solving specific features or spec. Several best practices:
* Between 100 or 200 lines of code (Sandy Metz and Uncle bob)
* Set a max line widths of your Class, for example 100-120 characters.

### Methods / Functions
Smaller method / function makes it easy to read, debug and update. Several best practices:
* Maximum of 4 parameters, hash options are parameters. (Sandy Metz’s Rule)
* No longer than 20 lines of code (Clean Code)

## Keep It Organized

Organized by functionality or domain, separate package, modules or class based on domain.

For backend service project, usually we have controllers (api and routers), service, and, database accessing module (DAO, active record, repository pattern). It is a good practice if we don't separate based on classification of patter like this. Instead, we separate by domain model, and inside that domain model

```
src
	authentication
		controllers
		service
		repository
		utils
		tests
	search
		controllers
		service
		repository
		utils
		tests
```

For frontend project we can separate by components or modules.

```
	modules
		homescreen
			forms
			utils
			tests
		users
			list
			assets
			tests
	// for generic components that is used throughout the project
	components
		buttons
		forms
		tests
```


## Keep it to be Singly Responsible

*"Do one thing, and do it well"* -Unix Philosophy

Large software is built from many pieces that works well. Most complexities and bugs come from one thing, that should do just one thing, but more things instead.

Consider this example:

```typescript
class PersonDTO {
	// some params
}

class Person {
	// some params
}


class PersonUtil {
	personCache: PersonCache;
	public createNewPerson(dto: PersonDTO): Person {
		const person = new Person(dto);
		this.personCache.flushAll(); // Will produce Buggy code! (1)
		return person;
	}

  public fastGetPerson(personId: string): Person {
		return this.personCache.findById(personId);
	}
}

class PersonService {
	constructor(
		personUtil: PersonUtil
	) {}

	async createPerson(personDto) {
		const person: Person = this.personUtil.createNewPerson(personDto);
		await this.repo.save(person);
		this.cacher.cache(person);
		return person;
	}

	getCachedPerson(id: string): Person {
		// by then time this is called  after createPerson, cached other person is gone! (2)
		return this.personUtil.fastGetPerson(id);
	}
}

```

We can see that on createNewPerson method, after Person is created, there’s a command to flush allCache. This will cause a bug whenever (2) is called, since there will be no data.

This will lead to one rule, if the method is not a business logic class, do only one and one thing only.

### Pure Function
In functional programming paradigm, there is a terminology called pure function, what does that mean.
Let’s see this simple basic function like this:

```
function double(x number) {
	return 2 * x;
}
```

what we see is a function signature that has a function that has one parameter and returning a value.

It only has 3 main super basic computation steps:
* Input
* Process
* Output

Why this simple process matters? If we see this pure function does not have any side effects (changin states), its just input, process, output.

This function can run on 1 machine instance, or load balanced to many instances without worry about any error.

## Don’t Repeat Yourself: Open Closed Principle

Class or function should be made easy by others to be used later in the future.

Check for other classes or functions in the project, if other engineer has done it, why reinvent the wheel.

If a module or package in a repo can be used by others, we need to split it and make it a libraries or dependencies of it.


## Inversion of Control

Use Dependency Injection for pure functions in OOP world. Like this:

```typescript
class Integer {
	constructor(num number){
		this.num = number
	}
	public double(): number {
		return 2 * this.number
	}
}

const number = new Integer(2)
const doubled = number.double()
```

Depencency injection will input number as constructor to Integer class, this way will make sure it is stay pure as long as you don’t change `this.num` value.

## Test, Test, and Test
Always test your code, and make it automated by writing a unit test for testing the unit, and for the whole business logics by using integration test.

Several tips:
* Unit test should only test input, process, output, network dependencies should be mocked.
* Integration test should test real world business case,  by trying it black-boxed from its service API.

## Immutability is King

Immutability means no mutable state.
Why?  Immutability leads to no shared state, no side effects, leads to scalable systems. Pure functions is one way to introduce no shared state.

Several tips:
* No mutable GLOBAL variables, always use Constants if global
* Use immutable data structures.
* Careful with Singleton patterns, it introduce mutability
* Use persistence (Redis, Memcached, ACID compliante database) for storing state.

## Reference
* [Curly’s Law: Do One Thing](https://blog.codinghorror.com/curlys-law-do-one-thing/)
* [Sandy Metz’s Rule](https://thoughtbot.com/blog/sandi-metz-rules-for-developers)

#blog #guidelines

