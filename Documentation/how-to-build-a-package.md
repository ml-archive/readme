# How to build a package

## Automatically with Swift Package Manager (SPM)
This is by far the easiest way to get setup. First, create a directory:
```bash
mkdir MyPackage
cd MyPackage
```

Now, use SPM to init your package:
```bash
swift package init --type library
```

All of your files will be generated automatically and you're now ready to setup [Travis and Codecov](#travis-and-codecov)

## Manually

### Package manifest
Package.swift file in the root
```swift
import PackageDescription

let package = Package(
    name: "MyPackage",
    dependencies: [
        // Add dependencies
        .package(url: "https://github.com/vapor/vapor.git", .upToNextMajor(from: 1)),                      
    ]
)
```

### Source
Create and put your sources in the directory
`Sources/MyPackage/`

### Tests

Create and put your tests in the directory
`Tests/MyPackageTests/`

In order to support Linux, add `Tests/LinuxMain.swift` the following file with all of your `XCTestCase`s.
```swift
import XCTest
@testable import MyPackageTests

XCTMain([
    testCase(MyPackageTests.allTests)
])

```

#### Example of TestCase

```swift
import XCTest
@testable import NeededImport

class MyPackageTests: XCTestCase {
    static var allTests = [
        ("test", test)
    ]
    
    func test() {
        XCTAssertEqual("abc", "abc")
    }
}
```

## Travis and Codecov
For `Travis` add the following file to your project root:

### .travis.yml
```yml
os:
  - linux
  - osx
language: generic
sudo: required
dist: trusty
osx_image: xcode8
script:
  - eval "$(curl -sL https://swift.vapor.sh/ci)"
  - eval "$(curl -sL https://swift.vapor.sh/codecov)"
```

For `Codecov` add the following file to your project root:

### .codecov.yml
```yml
coverage:
  ignore:
    - "Whatever folder"

```

## XCode
Generate an Xcode project:
```bash
vapor xcode -y
```
The hotkey to build and run unit tests is `cmd+u`.
