require 'yaml'

ruby_versions = %w(2.5.7 2.6.5)

matrix = [
  {
    rails_version: '~> 4.2',
    activeadmin_versions: ['1.4.3'],
    sqlite_version: '< 1.4'
  },
  {
    rails_version: '~> 5.2.3',
    activeadmin_versions: %w(2.3.1 2.4.0),
    sqlite_version: '< 1.4'
  },
  {
    rails_version: '~> 6.0.0',
    activeadmin_versions: %w(2.3.1 2.4.0),
    sqlite_version: '>= 1.4'
  },
]

matrix.each do |rails_version:, activeadmin_versions:, sqlite_version:|
  activeadmin_versions.each do |activeadmin_version|
    appraise "rails_#{rails_version}_activeadmin_#{activeadmin_version}" do
      gem 'rails', "#{rails_version}"
      gem 'activeadmin', "#{activeadmin_version}"
      gem 'sqlite3', "#{sqlite_version}"
    end
  end
end

travis = ::YAML.dump(
  'language' => 'ruby',
  'matrix' => {
    'include' => ruby_versions.flat_map do |ruby_version|
      Dir['gemfiles/*.gemfile'].map do |gemfile_name|
        {
          'rvm' => ruby_version,
          'gemfile' => gemfile_name
        }
      end
    end
  },
  'before_install' => [
    'gem uninstall -i $(rvm gemdir)@global -ax bundler || true',
    'gem install bundler -v 1.17.3',
  ],
  'env' => 'RAILS_ENV=test',
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
