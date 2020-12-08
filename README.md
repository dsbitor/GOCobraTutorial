# GOCobraTutorial
Tutorial describing the use of the Cobra package for creating commands associated with CLI programs.
## Cobra - What is it
Cobra provides a quick and very flexible facility to generate Command Line Interface (CLI) code, for an application or program. It is also a Go Package again called cobra, which is included in the generated code, or can be invoked without using the generated code. Parameters are entered when the program is executed.  Parameters follow the program name. The program name and parameters can be entered via the terminal interface, be embedded in a script, or called via another program. The program name and parameters are called the CLI. 

Cobra is a command-line program that creates go source for another GO program or programs. The created source forms the basis for accepting the CLI parameters and executing the required activities. Parameters consist of commands, sub-commands, flags, flag values, and arguments.  

Cobra itself has three commands:

- init - creates a new CLI environment for a new Go program.
- add -  adds commands and sub-commands to the CLI environment
- help - provides help and command syntax information for cobra and the commands init and add.

After completing your specification through the use of the init and one or more add commands, you have a series of GO source components created in an appropriate directory structure, which you can develop and use to construct your overall application and program(s).

### Cobra and Viper

Cobra is often used or discussed with another package called Viper. Viper has virtually nothing to do with Cobra. Viper is a package used to manage program or application configuration parameters that could be associated with environment Variables, configuration files, program flags, remote configuration systems. The only real association between the two packages is that Cobra when generating the initialization for the CLI processing environment (`cobra init`) generates some code to access configuration files via Viper capability. For the purposes of this tutorial, Viper is not discussed, but will have its own tutorial in the future.

### This Tutorial

The Cobra documentation on the Cobra GitHub site is not particularly suitable for beginners, and most other tutorials and examples miss out steps, exclude important points regarding set-up, implementation, usage and testing. Some even have poor English and have not discovered Grammerly.com or other similar sites.

These notes aim to provide guidance and advice on the use of Cobra, but there are three interlinked packages in this tutorial, namely Cobra and two packages used by Cobra and also worthy of use on their own, namely Viper and pflag. The pflag package is a drop-in replacement for Go's flag package, with the addition of implementing POSIX/GNU-style  `--`flags.

The notes are based on using Cobra on a Mac running OS X Mojave, (Version 10.14.6).

##  CLI Briefly Explained

A program executed via the CLI (Command Line Interface) consists of a program name, commands, flags and arguments. Taking an example from the GO compile and execute environment, the command `go install 'package'` where package is the folder containing the package required. 

In the Go documentation, the command is described as `go install [-i] [build flags] [packages]`. To complete this example, here are the components: 
- `go` is the  named program to be executed
- `install` is the command
- `-i` is a flag, and packages is an argument
- `build flags` are numerous and listed if you execute the command `go help build` 

The command `go help build`, also serves as a second example of a CLI action, where go is the program, help the command, and build the sub-command, but there are no flags or arguments.

Groups of flags or a single flag are described often as options.   

Flags provide further control, change predefined parameters, or supplying additional parameters to the program or command. Examples of flags are `-e`, `-V 42`, `--print`, `--font courier` etc. Flags starting with `--` are sometimes referred to as LongOpt format. Flag values are `42` and `courier`. Flag values can be strings, integer or real values, booleans, etc. 

If you are not sure about all the basic syntax, rules and conventions for CLI options (flags), here is a summary based on the POSIX standard, GNU conventions and flags often seen in Mac OS X, Unix and MS Windows environments:

- An option is a hyphen followed by a single alphanumeric character, like this: -o.
- An option may also be a double hyphen (GNU style flag) followed by an alphanumeric string of two or more characters, like --list,  --help or --filetype.
- An option may require an argument (which must appear immediately after the option); for example, -o argument or -oargument or --list 60.
- Options that do not require arguments, and are a single alphanumeric character, can be grouped after a hyphen, so, for example, -lst is equivalent to -t -l -s.
- Options can appear in any order; thus -lst is equivalent to -tls.
- Options can appear multiple times.
- Options precede other non-option arguments: -lst non-option.
- The -- argument terminates options.
- The - option can be used to represent one of the standard input streams.

Using Cobra the commands are handled by cobra package, Viper handles the initialization of applications, and flags (options) are handled by pflag packages. Note that both Viper and pflag are loaded by Cobra as part of the Cobra package.

** This description deals mainly with the command components created by Cobra**, and will provide some information about initialization and flags. A further tutorial dealing with Viper initialization, and more general information flag handling using pflags, is planned.

## Example Implementation Using Cobra

The notes expand on the building of a simple example application that includes key Cobra, Viper and flag components. Cobra functionality is described here. Viper is described in a separate tutorial, which also deals with flags in greater detail. 

The example application creates Cobra commands and sub-commands, creates code to initialize the configuration file programming environment for Viper and implements several CLI flags.

The application provides four functions. They all deal with the creation of directories (folders) and files. The four functions are:

- Create and add a file to the current directory using the command `mkfile`.
- Create a sub-directory below the current directory using the command `mkdir`.
- Create a sub-directory below the current directory, and create and add a file to that sub-directory using the command `mkdirfile`.
- Create a sub-directory below the current directory, and a sub-sub-directory using the sub-command `mksubdir` of the command `mkdir`.

We now will round out the specification with the user interface, and some brief notes on implementing the functions and supporting environment.

#### User Interface
The user interface consists of:

    program-name command [sub-command] [flags] arguments
The components sub-command and flags are optional, and there are one (or more) arguments. An example would be:

    add3 mkdir mksubdir -d dirname, -s subdirname --list 
Here the command and sub-command are specified along with flags for the dirname and sub-dirname and  --list to invoke a listing of the current directory.

An exception would be when the user requests help, when the command would likely be one of the following:

    add3 -h 
    add3 mkdir -h
    add3 mkdir --help

#### Implementing add3 Funcionality
add3 relies on standard operating system interfaces provided through the GO os and io/ioutil packages to implement the functions within each command:


- `os.Mkdir` and `os.MkdirAll` will be used to create directories and subdirectories when using the `mkdir`, `mkdirfile`commands and associated subcommand. 
- `os.Create` will be used to create files, for example command `mkfile` `Create temp.txt` `os.Create` creates or truncates the named file. If the file already exists, it is truncated. 
- `os.Chdir` changes the current working directory.
- `io/ioutil.ReadDir` reads the contents of the directory. 

Refer to the formal os package documentation for further information.

#### Supporting Environment

- Flags are implemented to allow dynamic switching of debug code and debug logging.
-  Arguments and paramters are not tested for syntax, or OS implementation rules. 
-  The application has not been tested and may not work on Microsoft Windows.
-  The Viper initialization of a configuration file is created, but no configuration file is provided and the overall effect is null functionality.

## Use of Go Modules

Go 1.11 and 1.12 included preliminary support for Go modules, the new dependency system that makes dependency management and version information explicit and easier to manage. Go Modules are especially important when running applications loaded from external sites such as GitHub. 

Packages from external sources such as GitHub are subject to versioning that does not necessarily follow the Go base language versions. The versions available may include numerous intermediate versions and no synchronization with versions of the Go language. Programs using external packages which once worked, now fail, due to code changes in packages, often where there was also a version change.  The developer may have no idea of what changes were made between versions, and the external consequences for users of that package. 

While packages supplied as part of the base Go language are remarkably compatible, they occasionally document changes and compatibility issues.

Securing specific package versions used to build an application in the Module system, Modules help to shield a program developer from package changes, that could cause unknown consequences when the program is recompiled or updated.

We recommend the use of Go versions 1.13 and 1.14. 

Adding module management, while adding some complication to this description, adds a great deal of improved functionality when compiling a program(s) with multiple packages and dependencies.

We are therefore going to use Go Modules and Go 1.14.3.

## Cobra Set-up
To get the Cobra package and its two amigos Viper and pflag, issue the following commands in the terminal window:

    go get github.com/spf13/cobra/cobra
    go get github.com/spf13/viper
    go get github.com/spf13/pflag

You will now have the packages available for use locally.

The Cobra CLI application, should be created in its own folder. The folder can be located anywhere on your computer, it no longer must be part of the \$GOPATH/src, and indeed when using modules, you should ensure that you have not created the folder within \$GOPATH/src. Modules will be used in this example, and we recommend that you use Modules for any work that uses external packages.

Create a folder/directory in a suitable location. For the purposes of this document the folder `add3` was located at  `~/username/go. Note that I do not store Go  code in my Mac Documents folder.

## Cobra Code Generation

Prior to generating code, it is recommended that you create a YAML parameter file for Cobra. In order to maintain a logcal sequence it is described before the code generation components. If you decide not to use a YAML parameter file move down to the next section.

### YAML Parameter File
Prior to starting to create the command based application or program, it is an advantage to create a Cobra yaml parameter file. This file contains a couple of parameters which will be used throughout this and perhaps other CLI based programs. It saves a lot of typing over the development and testing activities, as you will surely have to do the work described below more than once as you gain experience.

The contents of this yaml file is:

```go
author: I.T. Operational Risk Ltd., Toronto, ON <consultant dot itor at gmail dot com>
license: apache
```

The file is saved with the name `.cobra.yaml` in `/Users/username`. Note the file starts with a period which on unix based systems, including Linux and OS X, makes the file invisible in the directory. You must switch on visibility. On OS X, in the Finder, open up your Macintosh HD folder. Press Command+Shift+Period. Your hidden files will become visible. Press Command+Shift+Period again to hide them.  

If you do not like the MIT licence conditions pick one of the other predefined ones (GPLv2, GPLv3, LGPL, AGPL, MIT, 2-Clause BSD or 3-Clause BSD). For more information on custom licences see 'Configuring the cobra generator' section of the web page https://github.com/spf13/cobra/blob/master/cobra/README.md .

### Cobra Directory Environment
Next, create a module management component for that newly created directory by
moving to the directory, and running the go module iniialization command.

    cd add3
    go mod init add3
`go mod init add3` adds a single file to the folder called `go.mod`.

__**IMPORTANT WARNING**__

It is important to get you plans for code generation, commands, flags and help and usage guide information completed and laid out **prior to generating code**. The reason is that immediately upon completing the generation of the base code with the Cobra command, you will start to edit those generated files, and add code, data and text. If you then decide that the generated code was not what you wanted after starting the edits, regenerating code will wipe-out your edit changes and you will have to start again from scratch with your editing.  You can add additional flags safely using the Cobra command, but try and get the planning right before starting.

### Initialization Code Generation

Now create a new instance of the Cobra environment by issuing the `init` command. The `init` command has the following aliases: `init`, `initialize`, `initialise`, `create`.

There is one required parameter and that is the name of the command you are going to create. This is specified as  `--pkg-name add3`.

The complete command is:

    cobra init --pkg-name add3 
    
Because we have created the .cobra.yaml file above, your details and the appropriate Licence information will be added without more typing. Otherwise, the command becomes

        cobra init --pkg-name add3 --author I.T. Operational Risk Ltd., Toronto, ON <consultant.itor@gmail.com>" --licence MIT
This command if successful will reply with a message that starts with `Your Cobra application is ready at ...`.

The directory and file structure created by the  `mod init` and `cobra init` commands is shown below:

    ├── LICENSE
    ├── cmd
    │    └── root.go
    ├── go.mod
    └── main.go

The file `root.go` contains most of the code generated in init step. The contents of this file will be discussed in detail later.  `main.go` is basically a stub file which as the name shows contains the `main package` with the `main` function as expected in all Go programs. `main.go` calls `cmd.Execute()` which invokes the CLI processor.

### Command Processing Initialization and Generation

Here we deal with the initialization of code within the command and general program (or application).

Any code in `main.go` prior to the `cmd.Execute()` Appendix A main.go line25 will run prior to the command line processor (Cobra code) and can conduct set-up activity. But there is more, `root.go` also has capability to run initiation activities, using the Go init capability. This means that you can run initialization code for your commands ahead of the `main` function being executed. 

The actual sequence of programming actions are as follows:

    In root.go inside init function ahead of calling cobra.OnInitialize(initConfig)
    Begining main.go prior to calling cmd.Execute()
    In root.go inside initConfig function
    In root.go - Run here any code associated with bare command
    In main.go following calling cmd.Execute()
    
The above sequence was created by adding `fmt.Println` statements in `main.go` (lines 24 and 26) and `root.go` (lines 41 uncommented, 55 and 71) which are shown in their entirity in Appendix A.

The use of Go's initialization (`init`) capabilities can be found in the tutorial
'Understanding init in Go' at https://www.digitalocean.com/community/tutorials/understanding-init-in-go

The initialization of command processing his handled by `root.go`

### Cobra Commandd Data Structure

Cobra builds commands off each other. At the start of it is the
root command in root.go:

      var rootCmd = &cobra.Command{

Within the Command data structure there is at line 41, `Run: func(cmd *cobra.Command, args []string) {fmt.Println("In root.go - Run here any code associated with bare command")},`. This line allows code to be inserted between the curly brackets that will be run after `rootCmd` is initiated. It was used to show the order of execution of initializtion above. The code normally included here would not be run and is delivered commented out. In some cases there may be a need to run code after both `init` and `initConfig` initialization and prior to executing the next instructions in main following command initialization. 

### root function init

The `init` function initializes the command processing environment and does three major actions:

1. It calls the initialization of the command and program environment on line 56 by calling `cobra.OnInitialize(initConfig)`. Note that function initConfig is defined in root,go between lines 70 and 94.
2. It sets up any Persistent flags that will be available to all defined commands and subcommands. 
3. It sets up any Local flags that will only available to the root command. 

While the use of a common flag across all commands is a likely scenario, the use of a local flag for root is less likely.

### root function initConfig

This function deals with loading a configuration file, if one exists and any environment variables that the program uses. The work is done by calling Viper functions. In this tutorial example no activity takes place.

### Editing the root.go templated code.

There are several parameters and opportunities to enable or disable functionality in root.go.

## Creating Commands
Commands are added usind the `cobra add` function. For `add3`, we will start with the three required capabilities.

To define the three possible commands, and the sub-command the following are executed next:

    cobra add mkfile
    cobra add mkdir
    cobra add mkdirfile
Adding the sub-command called `mksubdir` to `mkdir`  requires:

    cobra add mksubdir -p 'mkdirCmd'

__**Notes**__

1. The Cobra generator documentation states "Use camelCase (not snake_case/kebab-case) for command names. Otherwise, you will encounter errors. For example, cobra add add-user is incorrect, but cobra add addUser is valid." 
2. It is useful to give names to commands that are verbs or verb/noun combinations. above we used an abbreviated verb/noun combination mk (make) and name of the entities. 
3. The sub command specifies the parent using `-p 'mkdirCmd' (mkdir is the parent command). Note also that the parent command name has Cmd appended to the name, and that this is specified in camelCase. 


After issuing these four Cobra commands the file structure has grown to:

    ├── LICENSE
    ├── cmd
    │    ├── mkdir.go
    │    ├── mkdirfile.go
    │    ├── mkfile.go
    │    ├── mksubdir.go
    │    └── root.go
    ├── go.mod
    └── main.go

Note that each of the three commands, and one sub command have been created and added as a go source file in the `cmd` directory.

The complete code can be compiled and executed using `go install add3`.

At this point another Module file will appear in your source directory called `go.sum`. This file records all dependency details associated with your use of packages called by the application and will change if other packages are added as the application changes or grows.

The fledgling CLI application will run by executing `add3 mkfile`. 
It returns:

    mkfile called

The added file mkfile.go contains similar code to root.go, but provides controls only for the mkfile command while root.go initializes the overall command structure. 
The difference in the generated code for the sub-command compared to its parent command is at line 40 in the appropriate code file.

The commandName.go files created by `cobra add` all have the same format and require editing to add appropriate, Short and Long descriptions. An example for our example root.go file is shown in 'Appendix B - Example Use, Short and Long Descriptions' below.

The following test cases should all work:

    add3 mkfile
    add3 mkdir
    add3 mkdirfile
    add3 mkdir mksubdir
Each returning a message like `mkdirfile called` saying which commaand was entered.

It is now time to add parameters namely arguments and flags.

## Flags (or Options) and Arguments 
There are 2 types of flags  available for use in Cobra created code, namely Persistent Flags and Local Flags.

- Persistent Flags: 
A Persistent Flag will be available in the command in which it is assigned (created),  as well as all the child or subcommands of that command.
- Local Flags: 
This flag is only available to the command in which it is assigned (created).

Cobra uses the Pflags library (package). 

### Using Flags

Building on the arguments that were demonstrated above we will now add GLobal and Local flags to our demonstration program.

The flag `--list` which is a boolean Persistent Flag is to be added at `cmd/root.go`, and will be used to control whether a listing of the directory will be produced. Note that by defining the flag as `BoolP` the flag can be defined in the call using either `--list` and a single character flag `-l`. If defined as `Bool` the only flag option would be `--list`, and the parameter `"l",` in the definition would create a syntax error. By putting it in root it will be available to all commands and sub-commands. 

The flags for the various commands are:

**mkfile**

`-f` or `--file` followed by filename  e.g.  `-f cobra.yaml`   -  default is an empty string `""`, and description string `"Provide a syntactically correct file name"`. Local flag.

Declaration:

    mkfileCmd.Flags().StringP("file", "f", "", "Provide a syntactically correct file name")

**mkdir**

`-d` or `--dir` followed by directory name e.g. `--dir data`   -  default is an empty string `""`, and description string `"Provide a syntactically correct directory name"`.
Global flag as it is used also in the child (sub-command `mksubdir`).

Declaration:

    mkdirCmd.PersistentFlags().StringP("dir", "d", "", "Provide a syntactically correct directory name")

**mkdirfile**

-f or --file followed by filename  e.g.  -f cobra.yaml   -  default is an empty string "", and description string "Provide a syntactically correct file name". Local flag.

Declaration:

    mkdirfileCmd.Flags().StringP("dir", "d", "", "Provide a syntactically correct directory name")

-d or --dir followed by directory name e.g. --dir data   -  default is an empty string "", and description string "Provide a syntactically correct directory name". Local flag.

Declaration:

    mkdirfileCmd.Flags().StringP("file", "f", "", "Provide a syntactically correct file name")

**mksubdir subcommand**

-s or --subdir followed by directory name e.g. --subdir input   -  default is an empty string "", and description string "Provide a syntactically correct sub-directory name". Local flag.

Declaration:

    mksubdirCmd.Flags().StringP("subdir", "s", "", "Provide a syntactically correct sub-directory name")

Note that this is a sub-command of mkdir, so the -d or --dir flag is also allowed.

### Using Arguments
This application environment has no arguments, only commands with associated flags. If there were arguments, they would be available to the code for each command and accessed as shown below.

The arguments are accessed as a string slice.

Enter the command `add3 mkdir -d dfile --list arg1 arg2 arg3`

The output showing the result of the command, its two flags (-d and --list) and the three arguments is:

 **Output:**
 
    mkdir called
    value of dirname is: dfile
    value of list boolean is: true
    arguments presented [arg1 arg2 arg3]

The output above was generated by adding lines 3 through 7 of the code below immediately after the `Run: ...` and `fmt.Println("mkdir called")` code in the file `mkdir.go`.

```go
Run: func(cmd *cobra.Command, args []string) {
	fmt.Println("mkdir called")
	dname, _ := cmd.Flags().GetString("dirname")
	fmt.Println("value of dirname is: " + dname)
	dlist, _ := cmd.Flags().GetBool("list")
	fmt.Println("value of list boolean is:", dlist)
	fmt.Println("len(args),arguments presented",args)
},
```

### Completing the Commands 

Here is the code for the three commands and the sub command. This code is pasted into the appropriate mkxxxxxxx.go source file immediately after the line:

    Run: func(cmd *cobra.Command, args []string) { 
Be careful! The code must be terminated with `},` as the code is embedded in a slice of strings.

#### mkdir

The -d or --dir flag parameter returns the directory name. The code to implement the mkdir function is:

```go
	fmt.Println("mkdir called")
	dname, _ := cmd.Flags().GetString("dir")
	fmt.Println("value of flag dir is: " + dname)
	dlist, _ := cmd.Flags().GetBool("list")
	mt.Println("value of flag list boolean is:", dlist)
	os.Mkdir(dname, os.ModePerm)
	if dlist {
		 files, err := ioutil.ReadDir(".")
			if err != nil {
			fmt.Println("Error - Unable to List Current Directory" ,err)
		}
		for _, file := range files {
			fmt.Println(file.Name())
		 }
	}
	},

```

Notes:
1. This code has been kept very simple, and with additional print statements, so that the reader and user can see what is happening.
2. Lines 1, 3, and 5 should be commented out after exploring the code's operation.
3. The code to list the directory, lines 8 through 14 above, should be moved to a global function.
 

#### mkfile

The -f or --file flag parameter returns the file name. The code to implement the mkfile function is:

```go
	fmt.Println("mkfile called")
	fname, _ := cmd.Flags().GetString("file")
	fmt.Println("value of flag file is: " + fname)
	dlist, _ := cmd.Flags().GetBool("list")
	fmt.Println("value of flag list boolean is:", dlist)
	os.Create(fname)
	if dlist {
		 files, err := ioutil.ReadDir(".")
			if err != nil {
			fmt.Println("Error - Unable to List Current Directory" ,err)
		}
		for _, file := range files {
			fmt.Println(file.Name())
		 }
	}
	},
```

Notes:
1. Notes from mkdir above also apply here.
3. Create creates or truncates the named file. If the file already exists, it is truncated. If the file does not exist, it is created with mode 0666.

#### mkdirfile

The -d or --dir flag parameter returns the directory name. 

The -f or --file flag parameter returns the file name. 

The code to implement the mkdirfile function is:

```go
		fmt.Println("mkdirfile called")
		dname, _ := cmd.Flags().GetString("dir")
		fmt.Println("value of flag dir is: " + dname)
		fname, _ := cmd.Flags().GetString("file")
		fmt.Println("value of flag file is: " + fname)
		dlist, _ := cmd.Flags().GetBool("list")
		fmt.Println("value of flag list boolean is:", dlist)
		// code below checks errors when creating direcory and file
		direrr := os.Mkdir(dname, os.ModePerm)
		if direrr != nil {
			fmt.Println("Error creating directory:", dname, "error =", direrr)
		}
		os.Chdir(dname)
		_, err := os.Create(fname)
		if err != nil {
			fmt.Println("Error creating file:", fname, "error =", err)
		}
		if dlist {
			files, err := ioutil.ReadDir(".")
			if err != nil {
				fmt.Println("Error - Unable to List Current Directory" ,err)
			}
			for _, file := range files {
				fmt.Println(file.Name())
			}
		}
	},

```

Notes:
1. Notes from mkdir above also apply here.
2. Lines 7 should also be commented out after exploring the code's operation.
3. After creating the new sub-directory, the working directory is changed to that new sub-directory, and the file created. `os.Chdir` changes the current working directory.
3 `os.Create` creates or truncates the named file. If the file already exists, it is truncated. If the file does not exist, it is created with mode 0666.
4. Note that lines 9 through 17 above contain example code to check for errors during the creation of the directory and file. This code could be incorporated as appropriate into mkdir and mkfile above.

#### mksubdir

This is a sub-command of `mkdir`. 

The -d or --dir flag parameter returns the directory name. 

The -s or --subdir flag parameter returns the sub-directory name. 

```go
fmt.Println("mkdir called with sub command mksubdir")
dname, _ := cmd.Flags().GetString("dir")
fmt.Println("value of flag dir is: " + dname)
subdname, _ := cmd.Flags().GetString("subdir")
fmt.Println("value of flag subdname is: " + subdname)
dlist, _ := cmd.Flags().GetBool("list")
fmt.Println("value of flag list boolean is:", dlist)
os.Mkdir(dname, os.ModePerm)
os.Chdir(dname)
os.Mkdir(subdname, os.ModePerm)
if dlist {
	files, err := ioutil.ReadDir(".")
	if err != nil {
		fmt.Println("Error - Unable to List Current Directory" ,err)
	}
	for _, file := range files {
		fmt.Println(file.Name())
	}
}
},
```

Notes:
1. Notes from mkdir above also apply here.
2. Lines 7 should also be commented out after exploring the code's operation.
3. After creating the new sub-directory, the working directory is changed to that new sub-directory, and the sub-sub-directory is created. `os.Chdir` changes the current working directory, and os.Mkdir creates the sub-sub-directory.

## Final Thoughts

1. The strings entered as parameters to the flag should be fully validated and this is currently omitted for clarity. A comment in the code is provided where each validation should occur.
2. The planning and annotation shown  above, for each command such as `mkdir`, makes the definition, creation and insertion of the correct source easier.
3. The Cobra documentation contains a discussion and examples of using Cobra without generating code with the `cobra` command. In some cases with experience under your belt this may be more efficient in some cases, especially if you are using a source code management tool.
4. Consider a hybrid solution where the `cobra` command is used just once for init and once for add to get started and then add all other commands and flags without the code generator but by copying.

## Appendix A - Initialization in Cobra

### main.go

```go
/*
Copyright © 2020 NAME HERE <EMAIL ADDRESS>

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
*/
package main

import (
		"fmt"
		"my-calc/cmd"
)

func main() {
	fmt.Println("Begining main.go prior to calling cmd.Execute()")
	cmd.Execute()
	fmt.Println("In main.go following calling cmd.Execute()")
}

```

### root.go

```go
/*
Copyright © 2020 NAME HERE <EMAIL ADDRESS>

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
*/
package cmd

import (
	"fmt"
	"github.com/spf13/cobra"
	"os"

	homedir "github.com/mitchellh/go-homedir"
	"github.com/spf13/viper"
)

var cfgFile string

// rootCmd represents the base command when called without any subcommands
var rootCmd = &cobra.Command{
	Use:   "add3",
	Short: "A brief description of your application",
	Long: `A longer description that spans multiple lines and likely contains
examples and usage of using your application. For example:

Cobra is a CLI library for Go that empowers applications.
This application is a tool to generate the needed files
to quickly create a Cobra application.`,
	// Uncomment the following line if your bare application
	// has an action associated with it:
	Run: func(cmd *cobra.Command, args []string) { 
		fmt.Println("In root.go - Run here any code associated with a bare command")},
	}

// Execute adds all child commands to the root command and sets flags appropriately.
// This is called by main.main(). It only needs to happen once to the rootCmd.
func Execute() {
	if err := rootCmd.Execute(); err != nil {
		fmt.Println(err)
		os.Exit(1)
	}
}

func init() {
	fmt.Println("In root.go inside init function ahead of calling cobra.OnInitialize(initConfig)" )
	cobra.OnInitialize(initConfig)

	// Here you will define your flags and configuration settings.
	// Cobra supports persistent flags, which, if defined here,
	// will be global for your application.

	rootCmd.PersistentFlags().StringVar(&cfgFile, "config", "", "config file (default is $HOME/.my-calc.yaml)")

	// Cobra also supports local flags, which will only run
	// when this action is called directly.
	rootCmd.Flags().BoolP("toggle", "t", false, "Help message for toggle")
}

// initConfig reads in config file and ENV variables if set.
func initConfig() {
	fmt.Println("In root.go inside initConfig function ")
	if cfgFile != "" {
		// Use config file from the flag.
		viper.SetConfigFile(cfgFile)
	} else {
		// Find home directory.
		home, err := homedir.Dir()
		if err != nil {
			fmt.Println(err)
			os.Exit(1)
		}

		// Search config in home directory with name ".add3" (without extension).
		viper.AddConfigPath(home)
		viper.SetConfigName(".add3")
	}

	viper.AutomaticEnv() // read in environment variables that match

	// If a config file is found, read it in.
	if err := viper.ReadInConfig(); err == nil {
		fmt.Println("Using config file:", viper.ConfigFileUsed())
	}
}

```

## Appendix B - Example Use, Short and Long Descriptions

	  Use:   "add3",
	  Short: "Adds files and subdirectories",
	  Long: `add3 - is an example command line application that utilizes 
	  the Go Cobra  package. It provides three commands each of which 
	  has a simple option or flag   parameter(s).
	  
	  The three commands provide the following functionality:
	  - mkfile: add a file to the current working directory
	  - mkdir: add a subdirectory under the current working directory
	  - mkdirfile: add a subdirectory with a file within that 
	    subdirectory, to the current working directory
	  
    A sub-command provides an additional level of subdirectories, when 
    used with mkdir:

	  - mksubdir adds a sub-sub directory to the sub-directory at time 
	    the sub-directory is created.`,
	  

# Appendix C - Legal File and Directory Names

If you're using a POSIX-compliant operating system, the legal characters in a file name are a-z, A-Z, 0-9, period, underscore, and hyphen. The regex to match 'illegal' characters would therefore be

    [^-_.A-Za-z0-9]

Addendum: This is if you want a fully-portable file name. As I was corrected in Josip's comment below, POSIX itself actually allows more characters.

The ^ inside the [] is part of the regex syntax it means: do not match any chars inside [].
