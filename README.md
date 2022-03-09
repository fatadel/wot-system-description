# Web of Things (WoT) System Description

![GitHub Workflow Status](https://img.shields.io/github/workflow/status/tum-esi/wot-system-description/Node.js%20CI)

A proposal to improve the management of Mashups in the Web of Things called the System Description. 
We provide conversion functions to convert a System Description representation of a WoT Mashup into a Sequence Diagram representation and also the other way around. 
Furthermore, this project includes automatic code generation to create a Servient, which implements a given Mashups application logic, utilizing the [node-wot.thingweb](https://github.com/eclipse/thingweb.node-wot) packages.

## Citing this repository

If you use this repository for scientific purposes please cite the paper `Web of Things System Description for Representation of Mashups` that is presented at [COINS2020](https://coinsconf.com/). You can also use the bibtex entry below:

```bibtex
@inproceedings{kkks:2020,
    author           = {Adrian Kast and Ege Korkan and Sebastian Kaebisch and Sebastian Steinhorst},
    title            = {Web of Things System Description for Representation of Mashups},
    booktitle        = {2020 International Conference on Omni-layer Intelligent Systems (COINS)},
    location         = {Barcelona, Spain},
    year             = {2020},
    month            = {9},
    doi              = {10.1109/COINS49042.2020.9191677},
    url              = {https://s-steinhorst.github.io/PDF/2020-COINS-Web%20of%20Things%20System%20Description%20for%20Representation%20of%20Mashups.pdf},
}
```

## General comments

In the code these markers are used:

- TODO to note an important bug/necessary change
- OPT to note an improvement

The underlying research of this project can be found in the paper mentioned above.

When running TSC to transpile TypeScript to JavaScript, the externally generated grammar-validator will throw some errors (it works, future improvement would of course be to get rid of the error messages.)

### Abbreviations

- Thing Description (TD)
- Web of Things (WoT)
- System Description (SD)

### Sequence Diagrams

This project uses Sequence Diagrams saved in `.puml` files that can be presented with a [plantUML](https://plantuml.com/) instance.
They have to be noted using a subset of the plantUML features, which can be validated through a provided validator that was generated by [REx](https://bottlecaps.de/rex/).
The notation is defined with the EBNF grammar given in `./definitions/validateSeqD.ebnf`. To create a validator from this grammar it is necessary to replace the external link:

```ebnf
Char    ::= [https://www.w3.org/TR/REC-xml/#NT-Char] - '"'
```  

with  

```ebnf
Char    ::= (#x9 | #xA | #xD | [#x20-#xD7FF] | [#xE000-#xFFFD] | [#x10000-#x10FFFF]) - '"'
```

### System Description (SD)

Is a representation developed especially to represent WoT Mashups and their application logic. Every given JSON file can be tested whether it is a valid SD, by using the SD JSON-Schema in `./definitions/sdSchema.json`. This project uses the validator [ajv](https://github.com/epoberezkin/ajv) to do so.

## How To

To use this project via the command line follow the commands of the remainder of this section. There will be explained how to give input and convert SDs to Sequence Diagrams or the other way around and generate a code implementation for the Mashup.

To use single parts of the project one can import the function of the corresponding source code file (see `./src`). Their usage can be learned by looking at the comments in the code and the usage in `./src/main.ts`.

- Make sure you have Nodejs and NPM installed.

- If you are executing the code generation or conversion for the first time use `npm run build` to create the executable JavaScript files first.

- create a directory (preferably in example-input directory) and paste your input Data there:

  - for all Sequence Diagrams that share the same TDs (not every TD must be required for every diagram) create a subfolder where you paste them. Then also create a JSON file (e.g. `TDs.json`) where you create an Array and just copy all required TDs there. Once done, you can generate SDs for your Mashups and also Code implementations of these SDs with:  

    ```bash
    node .\dist\src\main.js .\pathToYourSeqdFolder\ .\pathToYourTdsFile\TDs.json
    ```

    You can also use the command `npm start` but keep attention that your Folder path does not end with `\` (at least under windows this will result in an Error).
  
  - for all System Descriptions you want to use as input for Code generation, also create a subfolder and simply paste them in this subfolder. Then you can generate code implementations and also Sequence Diagrams for these SDs by using:  

    ```bash
    node .\dist\src\main.js .\pathToYourSDsFolder\
    ```

    You could also use `npm start` but keep attention to the comment in the preceding list element.

### Converted Representations

The vice-versa representation of your input directory contents will created at:
  
- Sequence Diagram output: `./created-output/SeqDs/` (and the extracted TD-fragments in `./created-output/TDs.json`)
- System Description output: `./created-output/SDs/`

### Code generation

- The Code will be generated at `./created-output/pathToInputWithoutExampleInput/Code/`. It is structured according to the [node-wot.thingweb](https://github.com/eclipse/thingweb.node-wot) recommendations in:

  - a `Prefix_index.js` file that imports all required protocol bindings to host the configured Servers and interact with the involved Things.

  - a `Prefix.ts` file that implements the Mashups application logic.

- To deploy your code to a device (or host it on your machine):

  - Transpile the required source files using  

    ```bash
    tsc -b ./created-output/pathToInputWithoutExampleInput/tsconfig.json
    ```

  - Copy the whole `Code` subfolder (and the tsconfig.json if you want to change it later in place) to your target device/directory

  - Install the node-wot core package `@node-wot/core`

  - Install all required node-wot protocol bindings (e.g. `@node-wot/binding-http`) that are required by the `Prefix_index.js` file.

- Then you can start the Mashup controller with  

    ```bash
    node Prefix_index.js
    ```

## Directory overview

- `./created-output/`-dir, is used to safe created System Descriptions, Thing Description-Arrays, Sequence Diagrams, and Code
- `./definitions/`-dir, contains everything that has is more definition than source code, e.g. typescript definitions, JSON-Schema, ...
- `./dist/`-dir, contains transpiled JavaScript files
- `./example-input/`-dir, contains examples for SDs, and Sequence Diagrams + TDs that can be used as input to convert them / generate code
- `./src/`-dir, contains the typescript source files
- `./NOTICE.md` lists used third-party code and their license
