require "rake/rdoctask"
require "rake/testtask"

spec = Gem::Specification.load(File.expand_path("vanity.gemspec", File.dirname(__FILE__)))

desc "Push new release to gemcutter and git tag"
task :push do
  sh "git push"
  puts "Tagging version #{spec.version} .."
  sh "git tag #{spec.version}"
  sh "git push --tag"
  puts "Building and pushing gem .."
  sh "gem build #{spec.name}.gemspec"
  sh "gem push #{spec.name}-#{spec.version}.gem"
end

desc "Install #{spec.name} locally"
task :install do
  sh "gem build #{spec.name}.gemspec"
  sudo = "sudo" unless File.writable?( Gem::ConfigMap[:bindir])
  sh "#{sudo} gem install #{spec.name}-#{spec.version}.gem"
end

task :default=>:test
desc "Run all tests"
Rake::TestTask.new do |task|
  task.test_files = FileList['test/*_test.rb']
  task.verbose = true
  #task.warning = true
end

Rake::RDocTask.new(:rdoc) do |rdoc|
  rdoc.rdoc_files.include "README.rdoc", "lib/**/*.rb"
  rdoc.options = spec.rdoc_options
  rdoc.options << "-f" << "darkfish"
end

task :report do
  $LOAD_PATH.unshift "lib"
  require "vanity"
  Vanity.playground.load_path = "test/experiments"

  experiment(:null_abc).reset!
  # Control	182	35	19.23%	N/A
  182.times { |i| experiment(:null_abc).count i, nil, :participant }
  35.times  { |i| experiment(:null_abc).count i, nil, :conversion }
  # Treatment A	180	45	25.00%	1.33
  180.times { |i| experiment(:null_abc).count i, :red, :participant }
  45.times  { |i| experiment(:null_abc).count i, :red, :conversion }
  # Treatment B	189	28	14.81%	-1.13
  189.times { |i| experiment(:null_abc).count i, :green, :participant }
  28.times  { |i| experiment(:null_abc).count i, :green, :conversion }
  # Treatment C	188	61	32.45%	2.94
  188.times { |i| experiment(:null_abc).count i, :blue, :participant }
  61.times  { |i| experiment(:null_abc).count i, :blue, :conversion }

  experiment(:age_and_zipcode).reset!
  80.times { |i| experiment(:age_and_zipcode).count i, false, :participant }
  35.times  { |i| experiment(:age_and_zipcode).count i, false, :conversion }
  84.times { |i| experiment(:age_and_zipcode).count i, true, :participant }
  32.times  { |i| experiment(:age_and_zipcode).count i, true, :conversion }

  experiment(:engagement).reset!
  def Time.now ; Time.at(Time.new.to_i - 15 * 60 * 60 * 24) ; end
  experiment(:engagement).milestone "Added smiley face"
  def Time.now ; Time.at(Time.new.to_i - 5 * 60 * 60 * 24) ; end
  experiment(:engagement).milestone "Changed to pastels"
  def Time.now ; Time.at(Time.new.to_i) ; end

  Vanity::Commands.report ENV["OUTPUT"]
end