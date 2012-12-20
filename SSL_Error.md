`connect': SSL_connect returned=1 errno=0 state=SSLv3 read server certificate B: certificate verify failed (OpenSSL::SSL::SSLError)

Replicate Error:
--
     require 'open-uri'

     link = "https://gist.github.com/raw/4310046/c5bbd0adc4bf1d97d825e3c376ed408fde22340f/generic_file"
     open( link ).read

Generic solution:
--
 
Get a certificate file

    wget http://curl.haxx.se/ca/cacert.pem
    
Tell Ruby to use this certificate file

     ENV['SSL_CERT_FILE'] = "cacert.pem"
     
     ## The Folowing should now work     
     require 'open-uri'

     link = "https://gist.github.com/raw/4310046/c5bbd0adc4bf1d97d825e3c376ed408fde22340f/generic_file"
     open( link ).read

Sometimes the pem file already exists in certain locations look there first?


    begin
      try_download(download_name, item)
    rescue OpenSSL::SSL::SSLError
      set_ssl_cert
      try_download(download_name, item)
    end



    def try_download( download_name, item )
      link = "https://gist.github.com/raw/4310046/c5bbd0adc4bf1d97d825e3c376ed408fde22340f/generic_file"
      open( link ).read
    end

    def set_ssl_cert
      found_possible_cert = false

      potential_ssl_cert_locations = []
      # RHEL & Cent OS
      potential_ssl_cert_locations << "/etc/pki/tls/certs/ca-bundle.crt" 

      # Ubuntu
      potential_ssl_cert_locations << "/etc/ssl/certs/ca-certificates.crt"
      
      potential_ssl_cert_locations <<  "/usr/local/rvm/usr/ssl/certs/cacerts"
      potential_ssl_cert_locations.each do |loc|
        if File.exist?( loc )
          found_possible_cert  = true
          ENV['SSL_CERT_FILE'] = loc
          break
        end
      end

      potential_ssl_cert_folder = []
      potential_ssl_cert_folder << "/usr/share/ca-certificates/"
      potential_ssl_cert_folder << "/usr/share/curl/"
      potential_ssl_cert_folder << "~/.rvm/usr/ssl/"
      
      potential_ssl_cert_folder.each do |loc|
        if File.exist?( loc )
          ENV['SSL_CERT_DIR'] = loc
          found_possible_cert  = true
          break
        end
      end

      unless found_possible_cert
        download_cert
      end
    end

    def download_cert
      $stderr.puts "OpenSSL::SSL::SSLError Occured no potential SSL Cert certificates found"
      $stderr.puts "  Downloading SSL Certs from http:///curl.haxx.se"
      open( "cacert.pem", 'wb') do |file|
        file << open( "http://curl.haxx.se/ca/cacert.pem" ).read
      end
      ENV['SSL_CERT_FILE'] = "cacert.pem"
    end


More complicated and OS specific solutions.

Solution using macports [ruby19-and-the-ssl-error][].

http://railsapps.github.com/openssl-certificate-verify-failed.html

[ruby19-and-the-ssl-error]: http://martinottenwaelter.fr/2010/12/ruby19-and-the-ssl-error/
