# Licensed under the Apache License, Version 2.0 (the "License"); you may not
# use this file except in compliance with the License. You may obtain a copy of
# the License at
#
#   http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS, WITHOUT
# WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied. See the
# License for the specific language governing permissions and limitations under
# the License.

FROM debian:8.2

MAINTAINER Daniel Henderson daniel@rawbonetechnologies.co.uk

ENV COUCHDB_VERSION 1.6.1

RUN groupadd -r couchdb && useradd -d /var/lib/couchdb -g couchdb couchdb 
22 
 
23 # download dependencies, compile and install couchdb 
24 RUN apt-get update -y \ 
25   && apt-get install -y --no-install-recommends \ 
26     build-essential ca-certificates curl \ 
27     libmozjs185-dev libmozjs185-1.0 libnspr4 libnspr4-0d libnspr4-dev libcurl4-openssl-dev libicu-dev \ 
28     erlang-nox erlang-dev \ 
29   && curl -sSL http://apache.openmirror.de/couchdb/source/$COUCHDB_VERSION/apache-couchdb-$COUCHDB_VERSION.tar.gz -o couchdb.tar.gz \ 
30   && curl -sSL https://www.apache.org/dist/couchdb/source/$COUCHDB_VERSION/apache-couchdb-$COUCHDB_VERSION.tar.gz.asc -o couchdb.tar.gz.asc \ 
31   && curl -sSL https://www.apache.org/dist/couchdb/KEYS -o KEYS \ 
32   && gpg --import KEYS && gpg --verify couchdb.tar.gz.asc \ 
33   && mkdir -p /usr/src/couchdb \ 
34   && tar -xzf couchdb.tar.gz -C /usr/src/couchdb --strip-components=1 \ 
35   && cd /usr/src/couchdb \ 
36   && ./configure --with-js-lib=/usr/lib --with-js-include=/usr/include/mozjs \ 
37   && make && make install \ 
38   && apt-get purge -y perl binutils cpp make build-essential libnspr4-dev libcurl4-openssl-dev libicu-dev \ 
39   && apt-get autoremove -y \ 
40   && apt-get update && apt-get install -y libicu52 --no-install-recommends \ 
41   && rm -rf /var/lib/apt/lists/* /usr/src/couchdb /couchdb.tar.gz* /KEYS /esl.deb 
42 
 
43 # grab gosu for easy step-down from root 
44 RUN gpg --keyserver pool.sks-keyservers.net --recv-keys B42F6819007F00F88E364FD4036A9C25BF357DD4 \ 
45   && curl -o /usr/local/bin/gosu -SL "https://github.com/tianon/gosu/releases/download/1.2/gosu-$(dpkg --print-architecture)" \ 
46   && curl -o /usr/local/bin/gosu.asc -SL "https://github.com/tianon/gosu/releases/download/1.2/gosu-$(dpkg --print-architecture).asc" \ 
47   && gpg --verify /usr/local/bin/gosu.asc \ 
48   && rm /usr/local/bin/gosu.asc \ 
49   && chmod +x /usr/local/bin/gosu 
50 
 
51 # permissions 
52 RUN chown -R couchdb:couchdb \ 
53     /usr/local/lib/couchdb /usr/local/etc/couchdb \ 
54     /usr/local/var/lib/couchdb /usr/local/var/log/couchdb /usr/local/var/run/couchdb \ 
55   && chmod -R g+rw \ 
56     /usr/local/lib/couchdb /usr/local/etc/couchdb \ 
57     /usr/local/var/lib/couchdb /usr/local/var/log/couchdb /usr/local/var/run/couchdb \ 
58   && mkdir -p /var/lib/couchdb 
59 
 
60 # Expose to the outside 
61 RUN sed -e 's/^bind_address = .*$/bind_address = 0.0.0.0/' -i /usr/local/etc/couchdb/default.ini 
62 
 
63 COPY ./docker-entrypoint.sh /entrypoint.sh 
64 RUN chmod +x /entrypoint.sh 
65 
 
66 # Define mountable directories. 
67 VOLUME ["/usr/local/var/log/couchdb", "/usr/local/var/lib/couchdb", "/usr/local/etc/couchdb"] 
68 
 
69 EXPOSE 5984 
70 WORKDIR /var/lib/couchdb 
71 
 
72 ENTRYPOINT ["/entrypoint.sh"] 
73 CMD ["couchdb"] 
