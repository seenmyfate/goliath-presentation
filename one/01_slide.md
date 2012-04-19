!SLIDE 
# Test Driving Goliath
### Tom Clements
### _Senior Developer, On The Beach_
<br />
<br />
<br />
### tom-clements.com | github.com/seenmyfate
### @Seenmyfate
!SLIDE incremental
# Goliath is

_an open source non-blocking asynchronous Ruby web server framework_


!SLIDE incremental bullets
# powered by

  * an EventMachine reactor
  * a high-performance HTTP parser
  * Rack
  * Ruby 1.9 runtime (MRI, JRuby Rubinius)

!SLIDE bullets incremental
# High fibers

  * Each HTTP request within Goliath is executed in its own Ruby fiber
* all asynchronous I/O operations can suspend and resume without additional code

!SLIDE  
# Hello, world

    $ gem install goliath

    
!SLIDE  

      @@@ ruby
      class HelloWorld < Goliath::API
        def response(env)
          [200, {}, "Hello, world"]
        end
      end

    
!SLIDE commandline
# Start the server
    $ ruby hello_world.rb -sv

!SLIDE incremental
# Read Later
## A demo

* Single Endpoint
* Fire and forget
* Validate params are correct
* Store a url

!SLIDE commandline incremental
# Tree 

    $    .
        ├── Gemfile
        ├── config
        │   └── read_later.rb
        ├── read_later.rb
        └── spec
            ├── read_later_spec.rb
            └── spec_helper.rb
   
!SLIDE smaller
# spec_helper.rb

      @@@ ruby
      Goliath.env = :test

      RSpec.configure do |c|
        c.include Goliath::TestHelper, example_group: {
          file_path: /spec/
        }
      end

!SLIDE smaller
# spec/read\_later\_spec.rb
      
      @@@ ruby
        it 'returns OK' do
          with_api ReadLater do
            get_request(query: {url: '/test'}) do |request|
              response = Yajl::Parser.parse(request.response)
              response.should eq 'OK'
            end
          end
        end


!SLIDE smaller
# config/read_later.rb

      @@@ ruby
      config['db'] =
       EM::Synchrony::ConnectionPool.new(size: 20) do
        Mysql2::EM::Client.new(ENV['DB_CONFIG'])
      end

!SLIDE smaller
# read_later.rb
    @@@ ruby
    class ReadLater < Goliath::API
      
      use Goliath::Rack::Params
      use Goliath::Rack::DefaultMimeType
      use Goliath::Rack::Render, 'json'
      
      def response(env)
        db.aquery("INSERT INTO `articles`(`url`)
        VALUES ('#{params[:url]}"))
        [200, {}, 'OK']
      end

    end

!SLIDE smaller
# spec/read_later.rb

    @@@ ruby
    it "returns an error" do
      with_api ReadLater do
        get_request(query: {}) do |request|
          response = Yajl::Parser.parse(request.response)
          response['error'].should eq 'Url identifier missing'
        end
      end
    end

!SLIDE smaller
# read_later.rb

    @@@ ruby
    use Goliath::Rack::Validation::RequiredParam,
      {key: 'url', type: 'Url'}

!SLIDE incremental 
## and that's it!

* github.com/postrank-labs/goliath
* igvita.com/
* github.com/seenmyfate/read_later


!SLIDE
# gem install goliath
<br />
<br />
<br />
<br />
### tom-clements.com | github.com/seenmyfate
### @Seenmyfate



