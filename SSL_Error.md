`connect': SSL_connect returned=1 errno=0 state=SSLv3 read server certificate B: certificate verify failed (OpenSSL::SSL::SSLError)

Replicate Error:

     require 'open-uri'

     link = "https://gist.github.com/raw/4310046/c5bbd0adc4bf1d97d825e3c376ed408fde22340f/generic_file"
     open( link ).read


Solution using macports [ruby19-and-the-ssl-error][].
ENV["SSL_CERT_FILE"] = "/usr/local/rvm/usr/ssl/certs/cacerts"

http://railsapps.github.com/openssl-certificate-verify-failed.html

[ruby19-and-the-ssl-error]: http://martinottenwaelter.fr/2010/12/ruby19-and-the-ssl-error/
