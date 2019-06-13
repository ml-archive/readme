# How to work with Vapor 2 projects

## MySQL

MySQL's current version is 8 but for Vapor 2 projects you need to have mysql 5.7 installed:

```
brew install mysql@5.7
```

If you have MySQL 8 installed as well you will get CMySQL related build errors. To solve this you will first have to tell Homebrew to use 5.7 by default:

```
brew link mysql@5.7 --force
```

But this is not quite enough because there is a file `/usr/local/opt/mysql` which is a symlink to MySQL 8 and this is where Swift looks for the MySQL header files. You can remove this file and create a link to 5.7 like so:

```
cd /usr/local/opt
rm mysql
ln -s mysql@5.7 mysql
```

After this clean and rebuild your project.

## Xcode
Vapor 2 projects cannot be built in Xcode 10.2 and later. You should therefore install [Xcode 10.1](https://developer.apple.com/download/more/) under `/Application/Xcode10.1.app` (or to a name of your choosing). No need to add any addition toolchains, just open your project in the newly installed Xcode.

Once you have the older version of Xcode installed, you should set the local Swift version to 4.1.2. This can be done in Xcode through Settings -> Locations -> Command Line tools (Choose Xcode 10.1) or in the command line using `xcode-select -s /Applications/Xcode10.1.app` (or adjusted to the location where you installed Xcode 10.1).
