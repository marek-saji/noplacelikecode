/\* there's no place like code \*/
----------------------------------

Solutions to my problems.

## Running

Note to self, since GitHub pages uses old Jekyll version which requires
old Ruby version, which requires old OpenSSL:

1. Install `rbenv`: https://github.com/rbenv/rbenv
2. Compile OpenSSL 1.x: https://github.com/rbenv/ruby-build/discussions/1940#discussioncomment-2663209 (use latest 1.x available)
3. Install ruby (use latest 2.x) using that OpenSSL:
   `RUBY_CONFIGURE_OPTS="--with-openssl-dir=/opt/openssl-1.1.1s" rbenv install 2.7.6`
4. Switch to that version: `eval "$( rbenv init - )"`
5. Install ruby bundler: `gem install bundler`
6. Install dependencies: `bundle install`
7. `bundle exec jekyll serve --drafts`
