+++
title = "It's not performance that makes software bad, it's developer fungibility"
date = 2026-06-02
draft = true
tags = ["enterprise software", "frameworks", "development", "quality", "fungibility"]
+++
Lately, I've been learning how to work with Spring and it's associated software within the springboot ecosystem in Java and it's been a bit of an eye opening experience coming from how I typically run my personal projects and how my prior jobs working in embedded C, C#, go and Rust.

The core of the spring Ecosystem is how everything is loosely coupled together with via magical `@` _at_ tributes (get it? :P). The spring boot runtime takes all these tags and through a complex series of reflection turns strings together a bunch of disconnected blobs of code into something that resembles a program.

Unfortunately, it has been _rough_ to adjust to coming from a more simple and sane way of viewing the world. Whether it be helping to clean up hibernate sending 5 select queries per asset by removing two way `@OneToOne`  bindings so lazy loading can be a thing; pressing `gd` to navigate to the next one line function overload that redirects to an 18th layer of abstraction to reach a `JpaRepositoryBean` with a single oneline `findBySomething()` function without a function body; or reading through microslop level (in a bad way) documentation that explains _absolutely nothing_ about the expectations of how you need to set up `Spring Securities` Oauth to work I think I finally have a grasp on what really makes enterprise projects _absolutely_ dumpster fires.

Using JPA/hibernate vs SQL as an example, consider the following hypothetical java class

```java
// Foo.java
@Entity
public class Foo {
    @Id
    @GeneratedValue
    private int id;

    @OneToOne(mappedBy = "foo", fetch = FetchType.LAZY)
    private Bar bar;
}

// Bar.java
@Entity
public class Bar {
    @Id
    @GeneratedValue
    private int id;

    @OneToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "foo_id")
    private Foo foo;
}
```

These are simple classes with a one to one two way relationship. This is kind of _more or less_ how you would want to structure things with Spring's ORMs for object modeling purposes. If you have a Bar, you want to be able to get it's foo, and vice versa. It seems sensible on the surface. Also, for performance, you would also be prone to thinking "Hey, let's lazy load, no sense in loading both unless we need to access it, right?

At first it all seems like magic, because you just define your interfaces, let Hibernate/JPA write all that pesky SQL for you and -- as some might say -- Bob's your uncle. That is... until it hits production and you start to learn that _lazy is a lie_ in this case.

That's because your expectations of hybernate are too high.

```sql
-- What you probably expected it would write if you needed it this way. 
SELECT f.id, b.id FROM Foo f JOIN Bar b ON f.bar_id = b.id;

-- What Hibernate generates instead with the bidirectional mapping:
SELECT f.id, f.bar_id FROM Foo f;
SELECT b.id FROM Bar b WHERE b.foo_id = ?;  -- repeated for every @OnetoOne relationship
```

In the code base that I'm onboarding into, one of core objects had _five_ such relations, which means that every query to our Entity _emitted 6 queries_ when a single join + coerce would have sufficed.

Further more, something simple like:

```sql
-- It should be this
SELECT a.id, b.id, c.id, d.id FROM A a
JOIN B b ON a.b_id = b.id
JOIN C c ON a.c_id = c.id
JOIN D d ON a.d_id = d.id
ORDER BY b.id;
```

Has to go through their awkward SQL, but not `Specification`, `CriteriaBuilder` and `SpecificationQuery` (I might be misremembering the exact class) with such excessively wordy and untestable java code that it makes me wonder _what's the freaking point_? At this point, learning postgres's dialetc of SQL is not only more easy and portable, _but easier_ than learning Spring's madness of classes!!!

So why the heck did the java ecosystem end up here? Well, to think about that, it would be useful to consider ideas of enterprise and Bob Martin's "clean code" paradigms. As you may be aware, a few years back there was abit of a debate surronding the hidden costs of clean code. These criticisms were almost entirely focused on performance and -- while entirely valid -- doesn't really explain the whole picture. One of the biggest purposes behind the clean code archtectures -- and Spring in particular -- is developer fungibility.

As springs attributes and reflection oriented component design heavily isolates components it enables teams to heavily silo their application and increases developer productivity within their own sections of the application _without_ any real understanding of how the rest of the application is structured or maintained. It trades technical depth in understanding of the core technology and programming langauges for framework understanding. While this invariably has major performance ramifications, it serves as a project manager's dream of using engineers as interchangeable cogs where experience is immaterial because you can flexibly scale your workforce to meet your demand.

Granted this benefits organizations, but it ultimately comes at the expense of the technical depth of an organization. I can confidently say that the code you write is less important than ensuring you have the correct infrastructure for your project. In that regards, spring boot can allow a project lead with strong archtecture sense and fundimentals to effectively _vibe code_ their way through with disposable contract workers (rather than LLMs), but the maintanability suffers just as much.

Partially due to how exhausting it is to work in a codebase that embraces the "form" over embracing the "function" of the application. At the end of the day, it's really just another example of how software engineers are always striving to write more code faster, without realizing that the velocity for a software project is rarely measured on the lines of code output and much more on the debugability.
