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

## What would such a schema look like ?
This is a sample of what my current idea of a schema looks like.

Note that `source` is an expression that can be used to generate code in many programming languages.

My intention is to make it possible to generate code for languages like Java, Go, Python, PHP, C#, ...

```yaml
name: 'ParseTest'
modbusSchemaLevel: 1
maxRegistersPerModbusRequest: 125
baseModbusAddress: 30000
modbusFunction: 'input-register'
    #    coil 0
    #    discrete-input 1
    #    input-register   3
    #    holding-register   4
blocks:
- name: 'FirstBlock'
  fields:
  - name : 'SerialNumber'
    type : string
    source: 'utf8(1,20)'
    description : 'The serial number of the device'
    unit: 'BogoUnits'
  - name : 'FieldName1'
    type : float32
    source: 'ieee754_32(21,2) / 100 ^ ScalingFactor - FieldName2'
    description : 'Some field'
    unit: 'BogoUnits'
  - name : 'FieldName2'
    type : float64
    source: 'sint64(25,2) / 100'
    description : 'Some other field'
    unit: 'KBogoUnits'
  - name : 'ScalingFactor'
    type : int64
    source: '10'
    description : 'The scaling factor'

tests:
- name: 'First Test'
  baseModbusAddress: 30000
  # The list of raw register values. First register is '1'
  registers: 1234 5678 90AB CDEF
  description: 'Random test'
  blocks:
  - blockName: 'FirstBlock'
    expected:
      SerialNumber:  '1234567890'
      FieldName1:    3.14159
      FieldName2:    2.71828
      ScalingFactor: 10

```

# Overall status
**Work in progress. Only rough sketches. Not yet usable.**

# More information
Go to the website I have created: https://modbus.basjes.nl

# Who is doing all of this?
Just me: [Niels Basjes](https://github.com/nielsbasjes)
