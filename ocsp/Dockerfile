FROM debian:latest
RUN apt-get update && apt-get install gcc build-essential git wget curl vim -y
RUN mkdir /apps/intermediate
RUN mkdir /app/private
ADD ../intermediate /apps/intermediate/index.txt
ADD ../intermediate/intermediate_cert.pem /apps/intermediate/intermediate_cert.pem
ADD ./private/ocsp_keypair.pem apps/private/ocsp_keypair.pem
ADD ./ocsp_cert.pem /app/ocsp_cert.pem

RUN chmod 0700 /app/private

WORKDIR /apps

RUN wget https://www.openssl.org/source/openssl-3.1.4.tar.gz && tar -xzvf openssl-3.1.4.tar.gz
RUN cd openssl-3.1.4 && ./config enable-fips && make && make install
ENV LD_LIBRARY_PATH /usr/local/lib/:/usr/local/lib64/
RUN openssl fipsinstall -out /usr/local/ssl/fipsmodule.cnf -module /usr/local/lib64/ossl-modules/fips.so

RUN rm /usr/local/ssl/openssl.cnf
RUN echo 'openssl_conf = openssl_init\n\
\n\
.include /usr/local/ssl/fipsmodule.cnf\n\
\n\
[openssl_init]\n\
providers = provider_sect\n\
alg_section = algorithm_sect\n\
\n\
[provider_sect]\n\
fips = fips_sect\n\
base = base_sect\n\
\n\
[base_sect]\n\
activate = 1\n\
\n\
[algorithm_sect]\n\
default_properties = fips=yes\n\
' > /usr/local/ssl/openssl.cnf

EXPOSE 80
ENTRYPOINT ["openssl ocsp -port 80 -index /apps/intermediate/index.txt -CA /apps/intermediate/intermediate_cert.pem -rkey /app/private/ocsp_keypair.pem -rsigner ocsp /app/ocsp_cert.pem"]
