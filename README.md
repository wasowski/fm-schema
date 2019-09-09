# fm-schema
A proposal for JSON/YAML schema for feature models.
A. Wąsowski, M. Acher

YAML (and JSON) are human readable formats for storing structured data, with very good tool support for many programming languages.  YAML does not seem to have a well-established schema definition language, but JSON Schema (https://json-schema.org/) are compatible with YAML.

JSON schema can represent structured objects with properties that are simple values, arrays, other objects, or references to other objects.  Also strings can be easily embedded in YAML (if there is need to store arbitrary constraints and comments).  In this sense, JSON schema is a reasonable modeling language, but with much larger user base and support than ecore (and with fewer dependencies).  Also YAML files are easier to read and create for humans than ecore files.

We propose to define feature models meta-model using JSON schema, and use YAML (and possibly JSON) to represent instance models (feature models).  This repository contains a simple definition of schema in the file `fm-schema.json`.  This schema says that a feature model is flat list of features which have names, and an (optional) reference to parent.

The file `fm.yaml` contains a simple instance (inlined here for convenience):

```yaml
- feature: &car
    parent: null
- feature: &engine
    parent: *car
- feature: &chasis
    parent: *car
```

The model defines three features, with `car` being the root feature, and `engine` and `chasis` being subfeatures.  The identifiers preceded by `&` are YAML object anchors. We are also using them as feature names.  (In the schema the property name is added as a string, for names that do not satisfy YAML object naming conventions).  The feature `car` is the root feature as it has no parent.  Other properties (optionalities, groups) can easily be added by defining more properties.

To check whether this file conforms to the schema use the following command:

```
any-json --input-format=yaml fm.yaml | ajv --verbose --errors=text -s fm.json -d /dev/stdin
```

Get the two simple command lines here (or using your favourite package manager):
* https://github.com/any-json/any-json
* https://github.com/jessedc/ajv-cli
The first tool translates the input file to json, the second one validates the schema conformance (because of this setup, we can also use JSON to represent feature models conforming to the same schema).

Incidentally, we can also validate whether our feature models schema (`fm-schema.json`) conforms to the schema for JSON schema files:
```
ajv -d fm-schema.json -s schema
```
where the file `schema` is obtained from: http://json-schema.org/schema#

The details of representation, of course, need to be ironed out, once the modeling team agrees on the key concepts of feature models that we want to support.
