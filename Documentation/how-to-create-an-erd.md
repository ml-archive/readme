# How to create an entity relationship diagram

To get a better overview of our database structures we use ERDs. This small guide explains how to easily and automatically generate it from the command line.

## Install Graphviz

##### source: https://www.norbauer.com/rails-consulting/notes/erd-diagrams-from-sequel-pro.html
```bash
brew install graphviz
```

## Export database structure from Sequel Pro
`File > Export > Dot`

**Note:** uncheck "Use case-insentive [sic] links"

## Create a diagram as PNG
```
dot -Tpng myDatabase.dot > erd.png
```
