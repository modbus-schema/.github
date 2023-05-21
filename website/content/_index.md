+++
title = "Modbus Schema Toolkit"
linkTitle = "Modbus Schema"
+++

## What is this?
The intent of these projects is
1. creating a generic way of defining the extraction of data from a modbus device that maps the raw registers into meaning full values.
2. doing it in such a way that a code generator for any (common) programming language can be created that generates the code needed to read the data from the device and actually execute the transformation into the meaning full fields.
3. actually do this for the devices I have at home and generate the Java code needed to really do this.

So essentially the intent of this project is to create a machine-readable standard schema/format specification for modbus devices that can be used to generate code.

Intended effect:
- The schema for a specific device only needs to be written ONCE.
- The code generator + runtime for a specific language only needs to be written ONCE.
- For all devices it is now trivial to generate code that maps the registers into usable values.
- A new device is immediately available in all programming languages.
- A new language immediately can provide tooling for all defined devices.

---

## Overall status
**Work in progress. Not yet usable.**

---

## Steps and progress ...
- [x] Define raw schema format
  - [x] Basic device parameters
  - [x] Fields
    - [x] Meta data
    - [x] How to get to the value of this field (i.e. which registers and how to map it all)
- [x] Create parser and prove it can be used to do some kind of code generation.
  - [x] Create maven plugin so we can automate that process.
- [x] Define reference testing next to the schema
  - [x] A way of setting the expected values for each field for those bytes.
  - [ ] Tool to extract raw bytes from real device using the schema.
    - This must use the schema as the basis.
    - It was found that retrieving partial values (i.e. only some of the registers of a value) from a device results in garbage register values.
- [ ] Define for a specific device the schema (i.e. the devices I actually have go first).
  - [ ] SDM630 : Simple: All fields are 2 registers IEEE754 32bit Floating point numbers
  - [ ] Thermia: Medium: Many field formats with varying multipliers
  - [ ] SunSpec: Hard  : Multiple blocks, scaling factors and different data types.
- [ ] Implement Java code generator
  - [ ] Core code generator with runtime library.
  - [ ] Code generator for unit test that is based on the reference data and expected values.

---

## A schema for modbus ?
Yes, this is a sample of what my current idea of a schema looks like.

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

---

## Who is doing all of this?
Just me: [Niels Basjes](https://github.com/nielsbasjes)

---

## Donations
If this project has business value for you then don't hesitate to support me with a small donation.

[![Donations via PayPal](https://img.shields.io/badge/Donations-via%20Paypal-blue.svg)](https://www.paypal.me/nielsbasjes)

---

## License

    Copyright (C) 2023 Niels Basjes

    Licensed under the Apache License, Version 2.0 (the "License");
    you may not use this file except in compliance with the License.
    You may obtain a copy of the License at

    https://www.apache.org/licenses/LICENSE-2.0

    Unless required by applicable law or agreed to in writing, software
    distributed under the License is distributed on an "AS IS" BASIS,
    WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
    See the License for the specific language governing permissions and
    limitations under the License.
