# Dad's Garage

Dad's Garage is a container that has all of the build tools you need to build, test, and deploy a cloud-native application to Kubernetes. The idea is to

1. Be able to develop your application on a computer that has nothing but an IDE and a container runtime (Docker, Podman, etc) by using Dad's Garage as the "developer toolbox".
2. Use Dad's Garage in your CI/CD pipeline to build, test, secure, deliver, and deploy your application to Kubernetes.

It's the containerized version of your dad's garage. It's full of tools, you spend lots of time in it, and you use it to build great things.

Dad's Garage is very new, and many of the tools that you will need to use aren't there yet. Stay tuned, or contribute by submitting a pull request!

## What Dad's Garage is (or will be)

- A container full of tools that follows best practices for security and usability
- A Jenkins JNLP Agent
- A Selenium Server
- Maybe more???

## What Dad's Garage is not (and never will be)

- A small container. As tools are added it is likely the container will grow to several gigabytes. This is expected and should not be seen as an issue, since this container will act as the main toolkit for develoeprs to use to develop with.
- A container that is used to run applications in production. Don't do this! Production containers should be tiny, only installing what is absolutely necessary to run.
- A windows container. You won't be able to build .NET Framework apps with this. (.NET Core should work great though!)

## Contributing

Pull requests are welcome! If you are going to contribute to this project you should

- Have [pre-commit](https://pre-commit.com/) installed
* Install the pre-commit hooks by running `pre-commit install` in this repo
* Run `pre-commit run -a` if the CI build isn't passing. The CI pipeline runs `pre-commit run -a` and fails if any of the checks fail.
