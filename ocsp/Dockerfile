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
RUN cd openssl-3.1.4 && ./config enable-ssl-trace && make && make install
ENV LD_LIBRARY_PATH /usr/local/lib/:/usr/local/lib64/

EXPOSE 80
ENTRYPOINT ["openssl ocsp -port 80 -index /apps/intermediate/index.txt -CA /apps/intermediate/intermediate_cert.pem -rkey /app/private/ocsp_keypair.pem -rsigner ocsp /app/ocsp_cert.pem"]
