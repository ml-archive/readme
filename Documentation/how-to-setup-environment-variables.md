# How to setup environment variables

We use environment variables heavily in our projects. It allows us to have personalized configurations, but it also makes it easy to deploy our projects without having to store sensitive values in our codebase.



## A short introduction to the config file

A config file in Vapor (`Config/someconfig.json`) is basically a JSON file with keys and values, like this:



```json
{
    "name": "my project",
    "mySecretApiKey": "topsecretapikey"
}
```





To set the values of your config file, you have a couple of options:



### Hardcode the values into the config

```json
{
    "name": "my project",
    "mySecretApiKey": "topsecretapikey"
}
```



Hardcoding the values directly into the config means that the values goes hand in hand with the codebase. This can be convenient for values that doesn't change across environments, and for values that are not secret nor personal. In other words, if a fellow developer pulls down the codebase, it would make sense for that developer to keep those values in the config.



### Use environment variables

```json
{
    "name": "my project",
    "mySecretApiKey": "$MY_API_KEY"
}
```



Setting the values in a config using environment values can sometimes be preferred. It is useful when the values are somewhat secret, such as API keys for third party integrations or passwords to databases, since they will not be readable even though an outsider should get access to the codebase. Using environment variables is also useful for values that might be dependent on the machine that are running the project. E.g. connection credentials for a database might be different from one developer to another. Also, when the project is later deployed to be released, the credentials will most likely be different as well. Environment variables allows us to swap out values when running the project, so that we can run the project on multiple machines, but with the different values for some of our configurations.



### Use environment variables with a default value

```json
{
    "name": "my project",
    "mySecretApiKey": "$MY_API_KEY:myfallback"
}
```



Sometimes it's convenient to have the option to supply a hardcoded value in a configuration file, but still being able to override that value using an environment value. This can be done by referring to the environment variable, followed by a `:` and then the hardcoded value. A good example of this could be the port of the webserver you're running or the name of the database. The name of the database will most likely be the same on each machine, and it can be annoying to have to change the values when switching projects. Having a fallback value for the database name can solve this, if each developer makes sure to name their databases accordingly.



### Set the values on the scheme

```json
{
    "name": "my project",
    "mySecretApiKey": "$MY_API_KEY"
}
```



There's one last option to set the values of the environment variables, which can be convenient in cases where you need to add some values for a specific project, but without changing the config files or changing your global set of environment variables on your machine. In this case, the config will look the same and will refer to your environment variables. To set the values, you edit the scheme of your project (using the api-template, the scheme is named `Run`), and you set any key values you might need under the section called "Environment Variables". To add a value to the environment variable used in the example above, one would simply add a row for the name `MY_API_KEY` with whatever value is needed.



## How to setup your own environment variables

You could export your variables in your `~/.bash_profile` and then let Xcode load these by adding a build phase, but there's a more convenient way which doesn't require adding build phases each time to your project. If you download and install [EnvPane](https://github.com/hschmidt/EnvPane) then you will get an extra preference pane in your System Preferences which will allow you to add environment variables. However, please note that after adding/changing a variable, Xcode needs to be restarted.

When a project is ready to be deployed, remember that environment variables also needs to be setup on the deployment environment. Have a look at Vapor Cloud's [documentation](https://docs.vapor.cloud/toolbox/managing-your-apps/#custom-environment-variables) on how to add this. Remember that some environment variables (e.g. MySQL variables) will automatically be created by Vapor Cloud.
