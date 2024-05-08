<!--
  ~ Copyright 2024 Multiproject DevOps Team
  ~
  ~ Licensed under the Apache License, Version 2.0 (the "License");
  ~ you may not use this file except in compliance with the License.
  ~ You may obtain a copy of the License at
  ~
  ~ http://www.apache.org/licenses/LICENSE-2.0
  ~
  ~ Unless required by applicable law or agreed to in writing, software
  ~ distributed under the License is distributed on an "AS IS" BASIS,
  ~ WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
  ~ See the License for the specific language governing permissions and
  ~ limitations under the License.
-->

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