# How To create an entity relation diagram

## Install Graphviz
##### source: https://www.norbauer.com/rails-consulting/notes/erd-diagrams-from-sequel-pro.html
```bash
brew install graphviz
```

## Export Database from Sequel-Pro
Edit > Export > Dot

**Note:** uncheck "use case insensitivity links"

## Create a diagram as PNG
```
dot -Tpng myDatabase.dot > erd.png
```