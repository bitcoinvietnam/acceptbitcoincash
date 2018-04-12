require 'rubocop/rake_task'
require 'jekyll'

task default: %w[verify rubocop proof]
task external: %w[verify rubocop proof_external]

task :build do
  config = Jekyll.configuration(
    'source' => './',
    'destination' => './_site'
  )
  site = Jekyll::Site.new(config)
  Jekyll::Commands::Build.build site, config
end

task proof: 'build' do
  require 'html-proofer'
  HTMLProofer.check_directory(
    './_site', \
    assume_extension: true, \
    check_html: true, \
    disable_external: true, \
    url_ignore: ['/add'], \
    hydra: { max_concurrency: 50 }
  ).run
end

task proof_external: 'build' do
  require 'html-proofer'
  HTMLProofer.check_directory(
    './_site', \
    assume_extension: true, \
    # check_html: true, \
    external_only: true, \
    url_ignore: ['/add'], \
    http_status_ignore: [0, 301, 302, 403, 503], \
    cache: { timeframe: '1w' }, \
    hydra: { max_concurrency: 20 }
  ).run
end

task :verify do
  ruby './verify.rb'
end

task :clean do
  rm_rf './_site'
end

RuboCop::RakeTask.new

namespace :docker do
  desc 'build docker images'
  task :build do
    puts 'Generating static files for nginx'
    puts `bundle exec jekyll build`
    puts 'Building acceptbitcoincash docker image'
    puts `docker build -t acceptbitcoincash/acceptbitcoincash .`
  end
end
