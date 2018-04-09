FROM solr:6-alpine

# Copy solr config from the version used by eZ Platform
COPY solr/ /opt/solr/server/tmp

RUN mkdir -p /opt/solr/server/ez

# Prepare config
RUN mkdir -p /opt/solr/server/ez_template \
 && cp -R /opt/solr/server/tmp/* /opt/solr/server/ez_template \
 && cp /opt/solr/server/solr/configsets/basic_configs/conf/currency.xml /opt/solr/server/ez_template \
 && cp /opt/solr/server/solr/configsets/basic_configs/conf/solrconfig.xml /opt/solr/server/ez_template \
 && cp /opt/solr/server/solr/configsets/basic_configs/conf/stopwords.txt /opt/solr/server/ez_template \
 && cp /opt/solr/server/solr/configsets/basic_configs/conf/synonyms.txt /opt/solr/server/ez_template \
 && cp /opt/solr/server/solr/configsets/basic_configs/conf/elevate.xml /opt/solr/server/ez_template \
 && cp /opt/solr/server/solr/solr.xml /opt/solr/server/ez \
 && sed -i.bak '/<updateRequestProcessorChain name="add-unknown-fields-to-the-schema">/,/<\/updateRequestProcessorChain>/d' /opt/solr/server/ez_template/solrconfig.xml \
 && sed -ie 's/${solr.autoSoftCommit.maxTime:-1}/${solr.autoSoftCommit.maxTime:100}/' /opt/solr/server/ez_template/solrconfig.xml
 
 #&& sed -ie 's/${solr.data.dir:}/\/opt\/solr\/server\/ez\/collections\/${solr.core.name}/' /opt/solr/server/ez_template/solrconfig.xml          

RUN chown -R 8983:8983 /opt/solr/server/ez
RUN chmod -R 777 /opt/solr/server/ez

# Set our core config as home
ENV SOLR_HOME /opt/solr/server/ez