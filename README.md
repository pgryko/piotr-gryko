
# Intro

Personal blog for Dr Piotr Gryko

## Build Instructions:


```console
$ sudo apt-get install ruby ruby-dev make build-essential

$ gem install bundler jekyll

$ bundle install

$ jekyll serve

$ jekyll build
```

## Generating integrity hashes for css and js files

```console
$ cat highlightjs.piperita.scss | openssl dgst -sha384 -binary | openssl enc -base64 -A

OB6RhmazgXzOVpTtSZznaIjQtE32yI73u20f3CZF9Bx8CYgcWE+quTt74oa91Auk
```


## Hosting
This source code is hosted on [gitlab](https://gitlab.com/pgryko/piotr-gryko) and automatically deployed onto the web using [Netlify](https://netlify.com)

## Theme
Theme is forked from [carte-noire](https://github.com/jacobtomlinson/carte-noire), 

![Homepage](http://i.imgur.com/xlmHArV.png)

### Article
![Article](http://i.imgur.com/8rD8FfC.png)


### Posts grouped by year
![All Posts](http://i.imgur.com/9bNs2Sc.png)

### JavaScript Search
![Search](http://i.imgur.com/yQqMeSl.png)

### Menu by mmenu
![Menu](http://i.imgur.com/SClrNSH.png)


## Contribution
Pull requests are very welcome.

## Tools and Libraries
The following tools and libraries are used in this theme

### JavaScript
 * [jQuery](http://jquery.com/)
 * [MMenu](http://mmenu.frebsite.nl/)
 * [HighlightJS](https://highlightjs.org/)
 * [Simple Jekyll Search](https://github.com/christian-fei/Simple-Jekyll-Search)

### CSS
 * [Bootstrap](http://getbootstrap.com/)
 * [Font Awesome](http://fortawesome.github.io/Font-Awesome/)

### Social
 * [AddThis](http://www.addthis.com/)
 * [Disqus](https://disqus.com/)

### Other
 * [Real Favicon Generator](http://realfavicongenerator.net/)
 * [Google Analytics](http://www.google.com/analytics/)

## License
The jekyll theme, HTML, CSS and JavaScript is licensed under GPLv3 (unless stated otherwise in the file).

Theme is forked from carte-noire.jacobtomlinson.co.uk/
https://github.com/jacobtomlinson/carte-noire