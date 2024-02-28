ARG ELASTIC_VERSION=8.7.0
FROM ubuntu:20.04 as builder
# https://github.com/elastic/dockerfiles/blob/v8.7.0/elasticsearch/Dockerfile

ADD https://github.com/wokaio/coccoc-tokenizer/archive/refs/tags/v1.0.2.zip /
RUN apt-get update -y && apt-get install -y unzip cmake g++ default-jdk


RUN unzip v1.0.2.zip && mv coccoc-tokenizer-1.0.2 coccoc-tokenizer && chmod 755 coccoc-tokenizer 

RUN cd coccoc-tokenizer && mkdir build && cd build && \
    cmake -DBUILD_JAVA=1 -DCMAKE_INSTALL_PREFIX=/usr/ .. && \
    make install

# https://www.docker.elastic.co/
FROM docker.elastic.co/elasticsearch/elasticsearch:${ELASTIC_VERSION}

# Add your elasticsearch plugins setup here
# Example: RUN elasticsearch-plugin install analysis-icu
WORKDIR /usr/local/share/tokenizer/dicts
COPY --from=builder /coccoc-tokenizer/dicts/tokenizer .
COPY --from=builder /coccoc-tokenizer/dicts/vn_lang_tool .
COPY --from=builder /coccoc-tokenizer/build/libcoccoc_tokenizer_jni.so /usr/lib
COPY --from=builder /coccoc-tokenizer/build/multiterm_trie.dump .
COPY --from=builder /coccoc-tokenizer/build/nontone_pair_freq_map.dump .
COPY --from=builder /coccoc-tokenizer/build/syllable_trie.dump .

COPY ./elasticsearch-analysis-vietnamese-8.7.0.zip /usr/share/elasticsearch/plugin/elasticsearch-analysis-vietnamese.zip

RUN echo "Y" | /usr/share/elasticsearch/bin/elasticsearch-plugin install --batch file:./plugin/elasticsearch-analysis-vietnamese.zip && \
    /usr/share/elasticsearch/bin/elasticsearch-plugin install analysis-icu

