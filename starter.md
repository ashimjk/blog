# Starter

## Setup project

> jekyll new .

## Serve project

> bundle exec jekyll serve

* or

> jekyll serve

Browse project

> http://localhost:4000

## Use Chirpy

* Copy files

``` 

_data
_plugins
_tabs
_config.yml
index.html
```

* Remove index.markdown and about.markdown
* Run `bundle`

## Create new post

> jekyll post <file-name>

## Install plantuml.jar
Then, make sure [PlantUML](http://plantuml.sourceforge.net/download.html) is installed on your build machine, and can be executed with a simple plantuml command.

For Linux user, you could create a /usr/bin/plantuml with contents:
```shell
#!/bin/bash
java -jar $(pwd)/plantuml.jar "$1" "$2"
```

Remember to change the path to plantuml.jar file.

Then set executable permission.
```shell
cp plantuml /usr/bin/plantuml
chmod +x /usr/bin/plantuml
```
## References

* [Jekyll Requirement](https://jekyllrb.com/docs/installation/)

* [Jekyll Quick-start](https://jekyllrb.com/docs/)

* [Jekyll Chirpy Theme](https://github.com/cotes2020/jekyll-theme-chirpy/)

* [Chirpy Starter](https://github.com/cotes2020/chirpy-starter)

* [Jekyll Compose](https://github.com/jekyll/jekyll-compose)

* [Jekyll Troubleshoot](https://docs.github.com/en/pages/setting-up-a-github-pages-site-with-jekyll)

* [Jekyll PlantUML](https://github.com/yegor256/jekyll-plantuml)