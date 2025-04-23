source "https://rubygems.org"

# Note: when updating Ruby version update update .ruby-version file as well
# to ensure Cloud.gov environment version of Ruby matches
ruby '3.3.4'

# Version of Jekyll running on Github Pages: https://pages.github.com/versions/
gem "jekyll", "3.10.0"

gem "kramdown-parser-gfm"

# This is the default theme for new Jekyll sites. You may change this to anything you like.
gem "minima", "~> 2.0"

# Fix for build issue on Pages
gem "ffi"

# If you want to use GitHub Pages, remove the "gem "jekyll"" above and
# uncomment the line below. To upgrade, run `bundle update github-pages`.
# gem "github-pages", group: :jekyll_plugins

# If you have any plugins, put them here!
group :jekyll_plugins do
   gem "jekyll-feed", "~> 0.6"
   gem "jekyll-seo-tag"
   gem "jekyll-sitemap"
   gem "webrick"
   gem "github-pages"
end

# Windows does not include zoneinfo files, so bundle the tzinfo-data gem
gem 'tzinfo-data', platforms: [:mingw, :mswin, :x64_mingw, :jruby]
