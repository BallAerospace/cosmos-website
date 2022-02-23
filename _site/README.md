# Development

1. bundle install
1. bundle exec jekyll serve

## Building the v4 content

The v4 documentation is currently not re-built because it is assumed to be static. To enable building the v4 website:

1. Set your path to include Ruby 2.5

    > set PATH=C:\Ruby2.5\bin;%PATH%

1. Ensure COSMOS4 is built:

    > cd C:\COSMOS4
    > rake build

1. Comment out the _config.yml exclude and keep_files section:

    ```
    exclude:
      - Gemfile
      - Rakefile
      #- _docs_v4/* # No longer automatically generate v4 content
    #keep_files:
      #- docs/v4 # Thus we keep the existing content

1. Now you can build with `bundle exec jekyll build`

# Deployment

To deploy the website to github

1. bundle exec rake build
1. git commit -am "Build website"
1. git push
1. bundle exec rake deploy