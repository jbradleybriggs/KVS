# KVS
## Overview
The primary point of the KVS syntax is to create a very simple and lite way of storing or sending structured data. The exact type of the data is not part of the KVS syntax. The type of the data can be determined by the specific code that reads or builds the data. Another way to determine data types is to use key prefixes or postfixes. But that is completely up to the developer to decide.
## Compared to JSON

The KVS syntax compares to JSON in a lot of ways, with most of the types removed and only supporting a string type and an Object type. Arrays are automatically created if an Object's values are keyless. To type out the KVS syntax using a standard US keyboard is also a lot easier compared to JSON since the SHIFT button is not needed for the reserved characters as well as having less reserved characters in general (only 5 characters being: =;][~ ). It also uses the keys often used by developers in general in most languages, so hitting these keys would be quick and easy for most developers, making the creation of KVS structures very easy and fast.

The KVS syntax also supports chaining of multiple separate KVS structures directly after one another without using any additional separators. And a newly acquired KVS structure can be appended to an existing KVS structure and still keep the entire string's syntax in tact, creating a super KVS structure as a result. Whereas appending one JSON structure to another JSON structure would not create a super structure, and would break the general rules of JSON. This can be very handy when appending to files or database cells as a way of logging transactions, events or history.
## General Syntax

The KVS syntax is a multi dimensional structure that contains a list of key-value pairs. With some values having the ability to be a "Structure" and act as another list of key-value pairs. Where the key is simply a name for the value. The syntax is signature based and not length based. Thus values and keys would terminate at a specific character and not at a specific length. This would make it a lot easier to parse and enable a low memory footprint for use on very primitive and low end devices. This will also make it easier to inject data in the middle of an already existing encoded string, without the need to go back and modify length values of higher level structures.

The point of the KVS syntax is to act as a generic data carrier between multiple languages and platforms. It can also be stored in a file or a database. The initial languages that would need implementation for the syntax is:
- PHP
- Java
- C
- JavaScript
- Python

The language specific implementation requires the use of native structures inside the language (PHP arrays, Java LinkedHashMaps, C n/a, JavaScript Objects) to assist the developer in setting up these structured data carriers. These structures does NOT have to store the data exactly as the KVS string is structured. Storing multiple flat structures inside KVS classes can also be considered. It is of utmost importance, that the native structures used must preserve the order of the key-value pairs as they were inserted initially, or the same order as they were found inside the KVS string. After a structure has been set up, the implementation needs a function to serialise the entire structure to this KVS syntax. Another function is also needed to take the serialised KVS syntax string and turn it back into native structured data.

This serialising process from a native data structure into a string, will be called encoding. And the deserializing process from a string to a native data structure will be called decoding.
## General Structure
The KVS syntax will basically list an infinite list of key and value pairs. Each key must be followed by a value, and each value must be followed by a key. The syntax string would end in a value. A value can be one of two types, a primitive type or a structure.
# Keys
## Reading from a string - decoding
The first thing read from a KVS syntax string would be a key. The key must be read one character at a time, until a terminator is found ( [ or =  ). Once a terminator is found the key can be saved into a native variable for later use. And reading of the value can commence.

The key can contain any characters except the 5 reserved ones ][;=~ . The key would act like a variable name, which simply act as a reference to the value.

If reading of a key commences and a whitespace character is found (space, tab, \n or \r), it must be ignored, and reading must continue until a non-whitespace character is found. From that point reading the actual key's data must continue until a key terminator is found ( [ = ~ ). If the key terminator ~ was found, recording of the key's data must end, then reading must continue even further until a [ or = is found, any characters read in that time must be ignored, this will be reserved additional metadata for future versions of the syntax. If any whitespace is tailing the key it must be removed before the key is saved as a native variable. Effectively the common "trim(key)" method must be run on the key to remove all leading and tailing whitespace.
## Null Keys
There are occasions where a key would have nothing in it. This is referred to as a null key. So the first character read from a KVS string can be a = or [ or ~, rendering the key a null. In this case the key must default to 0. If another key in the same structure is also null, it must default to 1. This pattern can continue and the default key must be incremented by one each time a null key is found. This only holds true if a null key is found in the same structure. If a sub-structure is encountered inside one of the values, a new counter must be initialised to 0, and incremented for that sub-structure only. If the sub-structure exits, and another null key is found on the main structure, the initial counter used in the first structure must be incremented again and used as default. Thus, each structure should have its own auto incrementing key, and an auto incrementing key is not shared with sub-structures.

Also note that the implementation must allow for non-null keys that are set between the auto incrementing keys.
# Value
## Reading from a string - decoding
The terminator for the key will act as a type for the value. If the key was terminated with a = then the value is a primitive type, meaning it's basically a string. If the key was terminated with a [ then the value is a structure, simply meaning that the value is another list of key-value pairs.
### Primitive
A primitive value's data starts directly after the = character. Any whitespace after the = character would be considered part of the value's data. If the value is a primitive = it will be terminated by a semicolon character ;. Any whitespace before the semicolon character ; would be regarded as part of the data. If the value originally contains a semicolon character somewhere inside the value, the semicolon character must be repeated. This will signal the decoder that the semicolon character is actually part of the value's data and that the value must not terminate. This the way of escaping the ; character is simply to repeat it.
### Structure
If the value is a structure [ it will be terminated with a ]. This will typically only be found after a semicolon character ; or a [. Please note that the last value (of key-value pairs) inside the structure must be terminated first with a semicolon character ; before the structure can be terminated with ]. It might appear like a good shorthand way of terminating the value and the structure at the same time by omitting the semicolon character, but this is strictly not allowed! The reason it's not allowed is that there is no way to escape the structures terminating character ] inside the value, since it is possible for the value to contain the structure terminating character ] as data. Thus the structure terminating character ] usually goes along with a semicolon character ; like so: ;]

The only other two exceptions would be if the structure contains no key-value pairs (an empty structure), or if another sub-structure also terminated. Then the structure terminating character ] would directly follow the structure starting character [ or the structure ending character, like so: [] or ]]
## Binary Data
Binary data can be inserted into the value as a Base64 encoded filename safe RFC 4648 string.

