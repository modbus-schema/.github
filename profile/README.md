# What is this?
This is a set of projects that aim to set a standard way of defining a schema for modbus devices in such a way that software to read the information from these devices can be generated automatically from the provided schema.

With a `schema` we mean the mapping from the raw modbus registers to meaningful values in a programming language agnostic way.

Intended effects:
- The schema for a specific device only needs to be written ONCE.
- The code generator + runtime for a specific language only needs to be written ONCE.
- For all devices it is now trivial to generate code that maps the registers into usable values.
- A new device is immediately available in all programming languages.
- A new language immediately can provide tooling for all defined devices.

So languages like Java, Go, Python, PHP, C#, ... should all be possible.

# Overall status
**Work in progress. Not yet usable.**

# More information
Go to the website I have created: https://modbus.basjes.nl

# Who is doing all of this?
Just me: [Niels Basjes](https://github.com/nielsbasjes)
