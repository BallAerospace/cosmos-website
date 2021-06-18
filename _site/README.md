# Development

1. bundle install
1. bundle exec jekyll serve


# Deployment

To deploy the website to github

1. bundle exec rake build
1. git commit -am "Build website"
1. git push
1. bundle exec rake deploy