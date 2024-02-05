# Building the Website Locally

First you need `ruby-bundler`, which on Ubuntu is installed by running:

```.sh
sudo apt install ruby-dev ruby-bundler
```

Now, I'm not a ruby expert, but AFAIK the first time you download this repo you
need to run:

```.sh
cd docs/
bundle config set path 'vendor/bundle'
bundle install
```

I think that installs the dependencies (feel free to correct this if I'm
wrong). From this point forward you should only need to run:

```.sh
bundle exec jekyll serve
```

and navigate your web browser to the link it suggests (it'll be something like
`http://127.0.0.1:4000`). As you add content the site will auto update, so no
need to do anything else.