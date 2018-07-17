---
layout: post
title: Pro-tips on Contributing to Ruby on Rails from an Absolute Newb
categories: [general]
tags: [conferences, goals]
description: I just startd contributing to rails. Here's a few tricks I've picked up that can help you get started off on a better foot than I started off on.
---
Since I’ve been Funemployed™ for a while, I’ve decided in order to keep from getting rusty I’d start helping out the Ruby on Rails community by triaging some issues and squashing some bugs. Their [guides](http://edgeguides.rubyonrails.org/contributing_to_ruby_on_rails.html#running-tests) have good step by step by step instructions on how to get the environment set up locally so you can start knocking things out but in the last few days I’ve discovered a few hacks to make things run a little more smoothly.

After you fork the [Rails repo](https://github.com/rails/rails) and clone it locally there’s a few things you’ll want to set up.

## Gemfile.local
Inevitably, there will be gems you want to use locally in dev that you won’t want to worry about remembering not to commit later. For example I like `pry` and `pry-byebug.` For this purpose you can set up a `Gemfile.local` and a `Gemfile.local.lock`. Just make a new file called `Gemfile.local` in the project root and include the following:

```
eval File.read('Gemfile')
# whatever random gems you want
```
Then do a
`$ cp Gemfile.lock Gemfile.local.lock`

Now to use your local Gemfile you can just run 
`$ bundle --gemfile Gemfile.local`

## Aliases
Personally I thought running that last command felt a little bulky so I created a local `.zshrc` inside my Rails directory and popped in `alias lb='bundle --gemfile Gemfile.local'`

Now I can just run `$ lb` to do a local bundle. I also have `$ bundle exec` aliased to `$ b` to save a few keystrokes.

## Running Tests
As an rspec user, running tests in minitest was another thing that felt cumbersome to me. To streamline it and so I wouldn’t remember created the following function in my `.zshrc`
```
function test() {
  case $# in
    0)
      bundle exec rake test
      ;;
    1)
      bundle exec ruby -w -Itest $1
      ;;
    2)
      bundle exec ruby -w -Itest $1 -n $2
      ;;
    *)
      echo "A maximum of 3 arguments are allowed"
      exit 1
      ;;
  esac
}
```
Now I can run all rails tests with
```
$ test
```
To run all the tests for a specific part of rails I can do it like so 
```
$ cd activerecord
$ test
```
To run all the tests in a particular file I can do
```
$ cd activerecord
$ test <filename>
```
And for a specific test it’s just
```
$ cd activerecord
$ test <filename> <testname>
```

## Git Excludes
So now you’ve made a few files and when you check your `git status` you are getting warned about them. Adding them to `.gitignore` is no good because you’re just gonna have to commit that or ignore it too. Fortunately there is an easy solution most folks don’t know about. Open up your `.git/info/exclude` file and add all the files you don’t want to track in git. For me it looks like this:
```
.idea/
my_stuff/
Gemfile.local
Gemfile.local.lock
.zshrc
```

That `my_stuff` folder is just a catch all folder for things I don’t want to track. Right now it’s just a temp file with some todos and the reproduce script from an issue I’m verifying but you can throw anything in there and name it whatever you want. Just make sure it’s in your exclude file.

## Running an issue script
When you’re lucky folks report their issues with a [handy executable](http://edgeguides.rubyonrails.org/contributing_to_ruby_on_rails.html#create-an-executable-test-case) that has everything you need to verify for yourself that their issue is legit. 

You can just copy/pasta it into a local `.rb` file and run it like `$ ruby real_bad_bug.rb` and that runs it against the specified env. But if you want to pry in or start coding up a solution you’re probably going to want to run the script against your local environment.

I was slogging about trying to set up the gem path on each executable to refer to my local copy of rails but thanks to the very wonderful [Eileen Uchitel](www.twitter.com/eileencodes) I now know this sweet shortcut. If you have a standard executable of an issue like [this one](https://github.com/rails/rails/blob/master/guides/bug_report_templates/active_record_master.rb) just copy it into your rails directory (I put it in `rails/my_stuff/`), delete all the code before the `require`s start and run the file like `$ bundle exec ruby your_neat_bug.rb` or `$ b ruby your_neat_bug.rb` if you’re using my alias.

### That's it fam. Those are all the things I know. So far, I've triaged a hadful of issues, reviewed a few PRs and submitted a patch. I'm sure I'll be back soon with some other fun discoveries. In the mean time I'm interested to hear if any of you have any favorite aliases or shortcuts you think more people should know about.
