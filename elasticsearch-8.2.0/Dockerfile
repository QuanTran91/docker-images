ARG ELASTIC_VERSION
FROM ubuntu:20.04 as builder
# https://github.com/elastic/dockerfiles/blob/v8.2.0/elasticsearch/Dockerfile

ADD https://github.com/miczone/coccoc-tokenizer/archive/refs/heads/master.zip /
RUN apt-get update -y && apt-get install -y unzip cmake g++ default-jdk


RUN unzip master.zip && chmod 755 coccoc-tokenizer-master 

RUN cd coccoc-tokenizer-master && mkdir build && cd build && \
    cmake -DBUILD_JAVA=1 -DCMAKE_INSTALL_PREFIX=/usr/ .. && \
    make install

# https://www.docker.elastic.co/
FROM docker.elastic.co/elasticsearch/elasticsearch:${ELASTIC_VERSION}

# Add your elasticsearch plugins setup here
# Example: RUN elasticsearch-plugin install analysis-icu
WORKDIR /usr/local/share/tokenizer/dicts
COPY --from=builder /coccoc-tokenizer-master/dicts/tokenizer .
COPY --from=builder /coccoc-tokenizer-master/dicts/vn_lang_tool .
COPY --from=builder /coccoc-tokenizer-master/build/libcoccoc_tokenizer_jni.so /usr/lib
COPY --from=builder /coccoc-tokenizer-master/build/multiterm_trie.dump .
COPY --from=builder /coccoc-tokenizer-master/build/nontone_pair_freq_map.dump .
COPY --from=builder /coccoc-tokenizer-master/build/syllable_trie.dump .

COPY ./elasticsearch-analysis-vietnamese-8.2.0.zip /usr/share/elasticsearch/plugin/elasticsearch-analysis-vietnamese-8.2.0.zip

RUN echo "Y" | /usr/share/elasticsearch/bin/elasticsearch-plugin install --batch file:./plugin/elasticsearch-analysis-vietnamese-8.2.0.zip && \
    /usr/share/elasticsearch/bin/elasticsearch-plugin install analysis-icu

