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
Vapor 2 projects cannot run in the newest version of Xcode. You should therefor install [Xcode 10.1 or a previous version](https://developer.apple.com/download/more/). No need to add any addition toolchains, just open your project in the newly install Xcode.  

Once you have the older version of Xcode installed, you should set the local Swift version to 4.1.2. The easiest way to do this is via [Swiftenv](https://github.com/kylef/swiftenv). Just run the following command in your terminal:

```
swiftenv local 4.1.2
```
