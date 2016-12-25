# How to build a package

Package.swift file in the root
```
import PackageDescription

let package = Package(
    name: "MyPackage",
    dependencies: [
        // Add dependencies
       .Package(url: "https://github.com/vapor/vapor.git", majorVersion: 1),                      
    ]
)
```

Add your code in 

`Sources/MyPackage/`

Add your tests in

`Tests/`

Add a LinuxMain.swift in Tests, in here you manually have to add all the tests which should be executed in Linux
```
import XCTest
@testable import MyPackageTests

XCTMain([
    testCase(MyPackageTests.allTests)
])

```

Example of test

```
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
Open your package in xcode 
```
vapor xcode -y
```

Build and run tests 
```
cmd + u
```

.travis.yml file
```
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

.codecov.yml
```
coverage:
ignore:
        - "Whatever folder"

```
