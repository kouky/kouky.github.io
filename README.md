# kouky.org

Personal website of Michael Koukoullis at [kouky.org](http://kouky.org)

## Local Development

This repo uses `rbenv` for Ruby version management. Gems are installed into `vendor/bundle`.

Install `rbenv` and `ruby-build`:

    brew install rbenv ruby-build

Enable `rbenv` in your shell if you have not already:

    echo 'eval "$(rbenv init - zsh)"' >> ~/.zshrc

Open a new shell, then install the Ruby version from [.ruby-version](.ruby-version):

    rbenv install -s

Install dependencies:

    bundle install

Run the site locally:

    bundle exec jekyll serve --watch --drafts

Build the site without starting the server:

    bundle exec jekyll build
