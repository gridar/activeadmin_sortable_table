require 'yaml'

matrix = [
  {
    rails_version: '4.2.11.1',
    ruby_versions: %w[2.5.7 2.6.5],
    bundler_version: '1.17.3',
  },
  {
    rails_version: '5.2.3',
    ruby_versions: %w[2.5.7 2.6.5],
    bundler_version: '1.17.3'
  },
  {
    rails_version: '6.0',
    ruby_versions: %w[2.5.7 2.6.5],
    bundler_version: '1.17.3'
  },
]

matrix.each do |rails_version:, **|
  appraise "rails_#{rails_version}" do
    gem 'rails', "~> #{rails_version}"
    gem 'sqlite3', '< 1.4'
  end
end

travis = ::YAML.dump(
  'language' => 'ruby',
  'matrix' => {
    'include' =>
      matrix.flat_map do |rails_version:, ruby_versions:, bundler_version:, **|
        ruby_versions.map do |ruby_version|
          {
            'rvm' => ruby_version,
            'gemfile' => "gemfiles/rails_#{rails_version}.gemfile",
            'env' => "BUNDLER_VERSION=#{bundler_version} RAILS_ENV=test",
          }
        end
      end,
  },
  'before_install' => [
    'gem uninstall -i $(rvm gemdir)@global -ax bundler || true',
    'gem install bundler -v "$BUNDLER_VERSION"',
  ],
  'install' => 'bundle install --retry=3',
  'script' => [
    'bundle exec rake dummy:prepare',
    'bundle exec rspec',
    'bundle exec rubocop',
  ],
  'addons' => {
    'code_climate' => {
      'repo_token' => '085c9fc58ac3efcd9a8b311ee77bcec0b5c048575cde8a2234f7c63550910969'
    }
  }
)

::File.open('.travis.yml', 'w+') do |file|
  file.write(travis)
end
