### How to build a package

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
