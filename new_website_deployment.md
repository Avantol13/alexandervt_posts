[_metadata_:created]:- "2019 Jul 02"
[_metadata_:modified]:- "2019 Jul 02"
[_metadata_:category]:- "Programming"
[_metadata_:published]:- "2019 Jul 02"

See my portfolio page about [this site](https://www.alexandervt.com/portfolio/this-website) for some basic info first.

In order to get this site live, I built a deployment pipeline using Circle CI that eventually deploys my entire site and SQLite database to Google App Engine. It is specifically constructed to minimize the cost and ease the addition of new content. However, the way it ended up is *not* the way I planned. In fact, the first iteration of this new Flask site was using a more traditional production database, Postgres. 

Let us recount the adventure of the poor soul that is Past-Alex:

1. Rewrite entire website in a completely different framework that I'm more familiar with
2. Find an alternative to Django's Admin portal (which let me add new blog posts via GUI directly on my site)
  * This ended up being a major hurdle, though I eventually got everything working locally! I could add new posts via `Flask-Admin`
3. Setup Google's Cloud SQL for the database, got App Engine all configured to talk to it
4. Site is live on App Engine, realize that I need to port all my old posts
. Think, "meh, there's not that many. I'll manually enter them into the new GUI." 
6. Attempt to use `Flask-Admin` GUI to add first old post
7. Receive strange error. "No write permission??"
8. Discover that App Engine has ephemeral storage that you cannot write to.
9. See the bill for Google Cloud SQL... for what it is, it's reasonable. But... I was paying less for Heroku...
10. Consider giving up on Google Cloud and going back to Heroku
11. Have an incredible idea. Perhaps I could use SQLite and just push a read-only copy to App Engine?
12. Realize this would require some fancy deployment pipeline that could parse posts from some location and dump it into that SQLite database
13. Write a bunch of code to parse old posts and new posts (with new posts being Markdown files in a separate git repo)
14. Write deployment pipeline in Circle CI that pulls posts repo, runs db migration, adds all old and new posts, and deploys everything (including the resulting SQLite database) to Google App Engine

And that's that. Here we are. App Engine has some quirks (startup time is not so hot for the first person who visits the site in a while) _but_ I am well within the "free tier" quotas on Google now that I'm not using Cloud SQL. So my site is hosted for pennies a month. Literally.

The downside? No fancy admin portal on the site to add new posts. However, adding Markdown files to a GitHub repo ain't so bad. Plus, the posts are version controlled in a more reasonable format should I ever want to change my site or pipeline again. 
