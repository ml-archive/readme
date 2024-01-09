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

## OpenSSL

Homebrew has removed OpenSSL 1.0 which Vapor 2 depends on. In order to install version 1.0 in addition to 1.1 you'll need to get an old formula for it. To get version 1.0.2t use:

```
curl -X GET https://raw.githubusercontent.com/Homebrew/homebrew-core/13a4fb05a3147d429416425c00142880c86e6f4e/Formula/openssl.rb > openssl.rb
```

> Note: we have to download the file with the formula because Homebrew disabled support for installing from a Github link directly.

Then install it by doing:

```
brew install openssl.rb -f
```

Confirm that openssl is now linked correctly by verifying that `openssl` below points to the 1.0 version.

```
> ls -l /usr/local/opt/openssl*
lrwxr-xr-x  1 siemen  admin  24 Oct 19 11:44 /usr/local/opt/openssl -> ../Cellar/openssl/1.0.2t
lrwxr-xr-x  1 siemen  admin  28 May 25 08:44 /usr/local/opt/openssl@1.1 -> ../Cellar/openssl@1.1/1.1.1g
```

If this is not the case you can override it by doing:

```
cd /usr/local/opt
rm openssl
ln -s ../Cellar/openssl/1.0.2t openssl
```

## Xcode
Vapor 2 projects cannot be built in Xcode 10.2 and later. You should therefore install [Xcode 10.1](https://developer.apple.com/download/more/) under `/Application/Xcode10.1.app` (or to a name of your choosing). No need to add any addition toolchains, just open your project in the newly installed Xcode.

Once you have the older version of Xcode installed, you should set the local Swift version to 4.1.2. This can be done in Xcode through Settings -> Locations -> Command Line tools (Choose Xcode 10.1) or in the command line using `xcode-select -s /Applications/Xcode10.1.app` (or adjusted to the location where you installed Xcode 10.1).
