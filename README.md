# book-example

An example of a book written with Pandoc, Ruby, Node and Python.


## Environment Setup

```
$ brew install \
    pandoc \
    librsvg \
    mactex \
    translate-shell \
    ghc \
    cabal-install
```

Node.js latest version using NVM:

```
$ nvm install v18.15.0
```

Install `gramma`:

```
$ npm install --global gramma
```

Install latest Python version using `pyenv`:

```
$ pyenv install 3.11.2
$ pyenv global 3.11.2
```

Install `pandoc-include` Python egg:

```
$ pip3 install pandoc-include
```

Install latest Ruby version using `rvm`:

```
$ rvm install 3.2.2
$ rvm use 3.2.2
```

Installing `Eisvogel` Pandoc template:

```
mkdir -p ~/.pandoc/templates/eisvogel && cd ~/.pandoc/templates
curl -L https://github.com/Wandmalfarbe/pandoc-latex-template/releases/download/v2.3.0/Eisvogel-2.3.0.tar.gz \
  --output eisvogel.tar.gz
tar jxvf eisvogel.tar.gz eisvogel
```

