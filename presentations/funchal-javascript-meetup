# Olá todos!

In my day job, I am the Director of Engineering for a tiny non-profit. Our 100% distributed team builds [a SaaS product](https://web.hypothes.is) that lets you annotate on the open web.

I’m also an advisor at [a coding school](https://pdxcodeguild.com/) in my hometown of [Portland, Oregon](https://goo.gl/maps/ei592c3S6zN2).

I’ve given a talk there a couple of times covering the highlights of the many cool features of ES6. I built a little site to go along with the presentation. You can find it at [andrew.hedges.name/es6](https://andrew.hedges.name/es6/)

Tonight, I’m going to go more in-depth on one of those features: template literals.

JavaScript (ECMAScript, technically) has always had a bunch of ways to create strings.

    let a = 'a string'
    let b = "another string"
    let c = new String('don’t try this one at home')

ECMAScript 2015 (ES6) introduced yet another way.

    let d = `ooh, the new shiny`

Those funny looking marks are called “backticks”, (no, not [back ticks](https://www.vetformacion.com/ficheros/noticias/19.jpg)) and they bring with them some interesting and powerful capabilities.

One of the nice things about them is that you can put whatever other kinds of quotes you want inside them.

    let e = `'"‘’“”`

Another really nice thing is that you can (finally) create multi-line strings.

    let f = `this
    is
    a
    string
    on
    multiple
    lines`

You may already know that they also act like templates.

    let g = 'todos'
    
    // logs: "Olá todos"
    console.log(`Olá ${g}`)

In the example above, I used a simple variable, but you can actually use any expression inside the `${}` and JavaScript will attempt to evaluate and replace it.

    let h = 1
    let i = 2
    
    // logs: "1 + 2 = 3"
    console.log(`${h} + ${i} = ${h + i}`)

Of course, return values from functions can be expressions as well (when they resolve to a value).

    function j() {
        return 'todos'
    }
    
    // logs: "Olá todos"
    console.log(`Olá ${j()}`)

But that’s all probably stuff most of you already knew.

Did you know you can nest backtick-delimited expressions? This kind of blew my mind the first time I saw it.

    function composeName(title, fname, lname, degree) {
        return `${title} ${fname} ${lname}${
        degree ? `, ${degree}`
               : ''
        }`
    }
    
    // outputs: "Rear Admiral Grace Hopper, PhD"
    composeName('Rear Admiral', 'Grace', 'Hopper', 'PhD')
    
    // outputs: "Ms Betty Holberton"
    composeName('Ms', 'Betty', 'Holberton')

Even more interesting is that you can create a function that acts as a formatter for what’s called a “tagged” template. I first learned about this capability at a previous job when I was working on a system and we were considering localizing it.

I wanted to be able to create templates in advance for use later rather than having them inline right when they’re used. I ended up with [a solution that was a little…overengineered](https://gist.github.com/segdeha/670ad8345ccb2773fad16bf5e6a43b96).

Let’s look at a simpler example. The following is the most basic possible tagged template formatter.

    function formatter(literals) {
        return {
            format: (...substitutions) => {
                let output = []
                for (let i = 0; i < literals.length; i += 1) {
                    output.push(literals[i])
                    output.push(substitutions[i])
                }
                return output.join('')
            }
        }
    }
    
    // outputs: "Hello, world. This is a test."
    formatter`Hello, ${0}. This is a ${0}.`.format("world", "test")

It looks a little more complicated than it is, so let’s break it down.

The formatter function takes an array of strings (the `literals` argument to the outermost function) that represent the parts separated by the substitution tokens.

    ["Hello, ", ". This is a ", "."]

We do a spread operator on the arguments passed in to the format function to create an array of strings we can use to compose our output.

    ["world", "test"]

The way I’ve written this contains a subtle bug. Can you spot it?

    // outputs: "Hello, world. This is a test.boom" (oops)
    formatter`Hello, ${0}. This is a ${0}.`.format("world", "test", "boom")

It’s also not very smart. It would be more useful to be able to reuse substitution strings at various positions like in the example below.

    // should output: "pizza is amazing. yes, pizza."
    formatter`${0} is ${1}. yes, ${0}.`.format("pizza", "amazing")

Let’s change our formatter function to make this work.

    function formatter(literals, ...keys) {
        // literals = ["", " is ", ". yes, ", "."]
        // keys = [0, 1, 0]
        return {
            format: (...substitutions) => {
                // substitutions = ["pizza", "amazing"]
                let output = []
                for (let i = 0; i < literals.length; i += 1) {
                    output.push(literals[i])
                    output.push(substitutions[keys[i]])
                }
                return output.join('')
            }
        }
    }
    
    // outputs: "pizza is amazing. yes, pizza."
    formatter`${0} is ${1}. yes, ${0}.`.format("pizza", "amazing")

Let’s make our formatter even smarter. Let’s have it take an argument for the language we want to see the resulting string rendered in.

    const strings = {
        en: {
            'pizza': 'pizza',
            'amazing': 'amazing'
        },
        pt: {
            'pizza': 'pizza',
            'amazing': 'surpreendente'
        }
    }
    
    function formatter(literals, ...keys) {
        return {
            format: (lang, ...substitutions) => {
                let output = []
                for (let i = 0; i < literals.length; i += 1) {
                    output.push(literals[i])
                    output.push(strings[lang][substitutions[keys[i]]])
                }
                return output.join('')
            }
        }
    }
    
    // outputs: 'pizza is amazing. yes, pizza.'
    formatter`${0} is ${1}. yes, ${0}.`.format('en', 'pizza', 'amazing')
    
    // outputs: 'pizza é surpreendente. sim, pizza.'
    formatter`${0} é ${1}. sim, ${0}.`.format('pt', 'pizza', 'amazing')

Another way to do our localization would be to use a tagged templated with named substitutions, as follows.

    const strings = {
        en: {
            'pizza': 'pizza',
            'amazing': 'amazing'
        },
        pt: {
            'pizza': 'pizza',
            'amazing': 'surpreendente'
        }
    }
    
    function formatter(literals, ...keys) {
        // keys = ["pizza", "amazing", "pizza"]
        return {
            format: (strings) => {
                let output = []
                for (let i = 0; i < literals.length; i += 1) {
                    output.push(literals[i])
                    output.push(strings[keys[i]])
                }
                return output.join('')
            }
        }
    }
    
    // outputs: 'Pizza is amazing. Yes, pizza.'
    formatter`${'pizza'} is ${'amazing'}. Yes, ${'pizza'}.`.format(strings.en)
    
    // outputs: 'Pizza é surpreendente. Sim, pizza.'
    formatter`${'pizza'} é ${'amazing'}. sim, ${'pizza'}.`.format(strings.pt)

Another cool possibility is to be able to pass in a function for the tagged template to use to transform some of the input.

    // left pad numbers smaller than 10
    let pad = num => num.toString().padStart(2, '0')
    
    function time12(date) {
        let ampm = 'am'
        let hour = date.getHours()
        if (hour > 12) {
            hour -= 12
            ampm = 'pm'
        }
        let minute = pad(date.getMinutes())
        return `${hour}:${minute}${ampm}`
    }
    
    function time24(date) {
        let hour = pad(date.getHours())
        let minute = pad(date.getMinutes())
        return `${hour}:${minute}`
    }
    
    function formatter(literals, ...keys) {
        return {
            format: (transform, data) => {
                let output = literals[0]
                output += transform(data)
                output += literals[1]
                return output
            }
        }
    }
    
    let date = new Date('2018-04-17 19:00')
    
    // outputs: "The Meetup started at 7:00pm."
    formatter`The Meetup started at ${0}.`.format(time12, date)
    
    // outputs: "The Meetup started at 19:00."
    formatter`The Meetup started at ${0}.`.format(time24, date)

So far, we’ve been calling `.format` on our strings immediately, but our formatter just returns a function, which means we can store the tagged templates for later use. That’s where the real power of these things comes in.

Using the formatter function above, we can create functions that are primed for formatting that we can pass around our program and reuse.

    let whenDidItStart = formatter`The Meetup started at ${0}.`
    
    // outputs: "The Meetup started at 19:00."
    whenDidItStart.format(time24, date)

## Resources

* [Template literals](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template_literals) on MDN
* [Getting Literal With ES6 Template Strings](https://developers.google.com/web/updates/2015/01/ES6-Template-Strings) by Addy Osmani
* [ES6 Template Literals in Depth](https://ponyfoo.com/articles/es6-template-strings-in-depth) by Nicolás Bevacqua
* [Understanding ECMAScript 6: Template Literals](https://leanpub.com/understandinges6/read#leanpub-auto-template-literals) by Nicholas Zakas

If we haven’t already, let’s connect on [Twitter](https://twitter.com/segdeha) and [LinkedIn](https://www.linkedin.com/in/andrewhedges/)!
