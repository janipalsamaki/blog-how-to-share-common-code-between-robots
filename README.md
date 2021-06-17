# How to share reusable code and functionality between your robot projects using Git submodules?

![How to share reusable code](header-image.png)

After developing several robots, you might find you need the exact same functionality (custom keywords and libraries) in many of them. Instead of copying & pasting the same code into each robot project, isolating and sharing the common code between the robots makes sense!

## A separate Git repository for the shared code

One option for code-sharing is to store the shared code in a separate Git repository and then include the code into the robots that need it using [Git submodules](https://git-scm.com/book/en/v2/Git-Tools-Submodules).

> It often happens that while working on one project, you need to use another project from within it. Perhaps it's a library that a third party developed or that you're developing separately and using in multiple parent projects. - Git documentation

Let's consider this shared robot code repository:

[https://github.com/robocorp/example-shared-robot-framework-keywords-and-libraries](https://github.com/robocorp/example-shared-robot-framework-keywords-and-libraries)

This example robot code repository contains shared code that other robot projects can import and use.

## The shared code repository looks like a normal robot project. Why is that?

The shared robot code repository looks like a normal robot project repository because it is a normal robot project. :)

The shared code project was created using [RCC](https://github.com/robocorp/rcc) and the extended Robot Framework template. The project is self-contained and can be run as a normal robot project. The shared keywords and libraries can be developed and tested in isolation.

When developing shared code, you want to be able to test it. Having the shared code as a normal robot project makes it easy to execute the keywords, call the library methods, etc., without unnecessary external dependencies. The project documents its dependency requirements in the `conda.yaml` file.

An alternative approach would be to set up another project for testing the shared code. That test project would include the shared code repository as a Git submodule. Then you would have something like this:

- The shared code repository.
- A test repository that includes the shared code repository as a Git submodule.
- A real production robot repository that includes the shared code repository as a Git submodule.
- Another real robot repository using the shared code.
- One more production robot repository using the shared code.
- ...

**Whatever the repository configuration, make sure to test the shared code in isolation to make sure it works!**

## An example of including the shared code using Git submodules

The shared code repository is used as a Git submodule in another example repository: [https://github.com/robocorp/example-use-git-submodule-for-shared-code](https://github.com/robocorp/example-use-git-submodule-for-shared-code).

Here's what the file structure looks like in the project that includes the shared code as a Git submodule:

```bash
shared @ 17dff6b
.gitignore
.gitmodules
README.md
conda.yaml
robot.yaml
tasks.robot
```

> The `shared` directory in the above file structure is the shared robot code repository included as a Git submodule.

The [https://github.com/robocorp/example-use-git-submodule-for-shared-code](https://github.com/robocorp/example-use-git-submodule-for-shared-code) repository that includes the shared code repository was created using [RCC](https://github.com/robocorp/rcc) and the standard Robot Framework template.

The `shared` Git submodule in this project was created with the `git submodule add` command:

```bash
git submodule add https://github.com/robocorp/example-shared-robot-framework-keywords-and-libraries shared
```

The `.gitmodules` file defines the path and the URL to the included repository:

```bash
[submodule "shared"]
    path = shared
    url = https://github.com/robocorp/example-shared-robot-framework-keywords-and-libraries
```

See the [`tasks.robot` file in the repository](https://github.com/robocorp/example-use-git-submodule-for-shared-code/blob/main/tasks.robot) for examples of importing and using the shared code.

## How to clone this example project, including the Git submodule?

To clone this project, including the Git submodule, use the following command:

```bash
git clone --recurse-submodules git@github.com:robocorp/example-use-git-submodule-for-shared-code.git
```

> The `--recurse-submodules` option handles cloning the submodule. See the [Git submodule documentation](https://git-scm.com/book/en/v2/Git-Tools-Submodules) for more information.

## How to pull the upstream changes to the submodule?

To update (pull the upstream changes to) the submodule contents, use the following command:

```bash
git submodule update --remote
```

## Important notes

The Git submodule strategy works for code-sharing, but there are some caveats. **Robocorp Cloud does not automatically clone the submodules** at the time of writing. This means you can not use the Git repository linking feature in Robocorp Cloud for robots that use Git submodules.

However, when using Git submodules, you can still upload your robot to Robocorp Cloud using the upload functionality in Robocorp Lab, VS Code, or RCC. Since the upload creates a zip package with the project contents, the zip also contains the submodule contents!

Remember to keep the shared code version in sync if you have multiple developers working on your robot projects. Since the submodule does not update automatically when you pull changes to your main robot project you need to remember to run the `git submodule update --remote` command manually. Creating a shell script (containing the required pull commands and options) that each developer can run to update the robot code repository might be a good idea.

## Other options for code-sharing

Read the [Sharing libraries](https://robocorp.com/docs/development-guide/qa-and-best-practices/sharing-libraries) article to learn about other code-sharing possibilities!
