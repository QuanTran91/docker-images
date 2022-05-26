ARG ELASTIC_VERSION

FROM ubuntu as builder

ADD https://github.com/miczone/coccoc-tokenizer/archive/refs/heads/master.zip /
RUN apt-get update -y && apt-get install -y unzip cmake g++ default-jdk

RUN unzip master.zip && chmod 755 coccoc-tokenizer-master && \
    cd coccoc-tokenizer-master && mkdir build && cd build && \
    cmake -DBUILD_JAVA=1 -DCMAKE_INSTALL_PREFIX=/usr .. && \
    make install && \
    cp libcoccoc_tokenizer_jni.so /usr/lib/ && \
    cd / && rm -f master.zip && \
    rm -rf coccoc-tokenizer-master

# https://www.docker.elastic.co/
FROM docker.elastic.co/elasticsearch/elasticsearch:${ELASTIC_VERSION}

# Add your elasticsearch plugins setup here
# Example: RUN elasticsearch-plugin install analysis-icu
WORKDIR /usr/share/tokenizer/dicts
COPY coccoc-tokenizer/dicts/tokenizer .
COPY coccoc-tokenizer/dicts/vn_lang_tool .
RUN mkdir -p /usr/local/share/tokenizer/dicts
COPY --from=builder /coccoc-tokenizer/build/libcoccoc_tokenizer_jni.so /usr/lib
COPY --from=builder /coccoc-tokenizer/build/multiterm_trie.dump /usr/share/tokenizer/dicts
COPY --from=builder /coccoc-tokenizer/build/nontone_pair_freq_map.dump /usr/share/tokenizer/dicts
COPY --from=builder /coccoc-tokenizer/build/syllable_trie.dump /usr/share/tokenizer/dicts
COPY --from=builder /usr/lib/ /usr/lib/

COPY ./elasticsearch-analysis-vietnamese-8.2.0.zip /usr/share/elasticsearch/plugin/elasticsearch-analysis-vietnamese-8.2.0.zip

# RUN whoami
# USER root
# RUN whoami
RUN elasticsearch-plugin install --batch file:./plugin/elasticsearch-analysis-vietnamese-8.2.0.zip
RUN elasticsearch-plugin install --batch analysis-icu

# USER elasticsearch
# RUN whoami

