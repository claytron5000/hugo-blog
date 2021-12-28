---
author: Clayton Salem
date: "2021-12-27T00:00:00Z"
id: 40
title: Migrate to Hugo
---

This is a series of posts inspired by my own [reaction](https://twitter.com/ClaytonSalem/status/1475432712928477191?s=20) to a tweet about not blogging. In this post I'll migrate my blog to Hugo, which is something I've been wanting to try for a while.

# Step One; Install Hugo

```
brew install hugo
hugo version
```
Okay, easy peasey.

# Step Two; Migrate!

There are a few tools to migrate, but there's a built-in CLI that looks super easy:

> Import your site from other web site generators like Jekyll.

> Import requires a subcommand, e.g. `hugo import jekyll jekyll_root_path target_path`

Looks easy, but when I run the command nothing appears to happen. I run in a bunch of times, adding the `--debug` flag, which appears to do nothgin also. Then I notice there's a `public` directory, but it's empty. I try a few other flags but nothing seems to do anything else. Maybe I'm doing it on the wrong directory.

I install the site, `hugo new site`. Then I run the `import` command with the `--force` flag. This works! Note to self: PR to the Hugo repo to update their documentation: "import from another site __to an installed site__. 

# Check out the site locally

I start with the `hugo server` command. The site is served, but it's just a blank page. I install a theme used in the example, but the instructions tell me I need to install it as a git-submodule, this is getting, uh, interesting.

I install the theme as a sub-module and run the `hugo server` command. Again, white screen. So far I've been trying to run this blog "blind." I.e. I haven't opened it in a text editor. It might be time.

I add the `theme = "annake"` line to the config.toml file, but this throws an error. (Computers are cursed, run to the woods where you belong.)

I mess around with the toml file, which results in an error I can't seem to get out of.

```
 error calling markdownify: unable to cast maps.Params{"email":"ben@example.com", "name":"Ben", "uri":"http://ben.balter.com"} of type maps.Params to string
 ```

 When I remove the theme line, the error goes away, when I add it, the `server` command fails. Googling errors while installing a theme is not where I wanted this blog to go, but maybe it's exactly what I should have expected.

 I took a longer look at the migrated site, and that seems to be the problem. The import imported what looks like a bunch of examples posts from `content/post/vendor/bundle/ruby/2.6.0/gems/jekyll-feed-0.10.0/spec/fixtures`. It put these in with my real posts. I guess by enabling the theme, the server went in and tried to actually build the posts.

 Removing that directory of posts, adding the theme back to the config file, the server starts, runs, and serves. Phew.

 # Theming

 I don't want a redesign, but I also don't want to spend too much time on these. I'll probably keep the current site. Re-thememing, or migrating my current theme to a new platform is certainly within the scope of this blog, but I also know I have limited patience and time, so for the moment, I'll skip that and instead work on the next step.

 # Building and serving the site

 First step is to create a new repository. I'm sticking with github because it's what I know. In the process I remember all the images are statically stored in git... not great. That'll have to be something I fix before doing this again. However, onward!

 I haven't tried github actions yet, and the Hugo site offers a simple looking set up, so copy-paste away.

 My end goal was to host this as a subdomain to my current site, and add a robots.txt to hide the redundant site. But I'm not sure it's worth the effort, so, github pages here we go.

 It dies on the Build step : `Error building site: TOCSS: failed to transform "ananke/css/main.css" something, something, SCSS`. Ugh, I hate Sass. This looks like a problem with the version of go included in the build container. So, do I figure out how to write a minimal theme, or fix the build container.

 Using a new container seems like the easier, but now I'm running into the limits of github pages. After trying to tag the build with a hugo-specific container I get a  `Waiting for a self-hosted runner to pickup this job...` error. I guess what this means is I can't run any container on github actions, but only the ubuntu image.

 So without getting deep into github, I'm trying this shortcut. On the line where the build happens, I'm installing the hugo dependency. `sudo apt-get install hugo && hugo --minify`




