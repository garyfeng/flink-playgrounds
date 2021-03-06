###############################################################################
#  Licensed to the Apache Software Foundation (ASF) under one
#  or more contributor license agreements.  See the NOTICE file
#  distributed with this work for additional information
#  regarding copyright ownership.  The ASF licenses this file
#  to you under the Apache License, Version 2.0 (the
#  "License"); you may not use this file except in compliance
#  with the License.  You may obtain a copy of the License at
#
#      http://www.apache.org/licenses/LICENSE-2.0
#
#  Unless required by applicable law or agreed to in writing, software
#  distributed under the License is distributed on an "AS IS" BASIS,
#  WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
#  See the License for the specific language governing permissions and
# limitations under the License.
###############################################################################

###############################################################################
# Build Click Count Job
###############################################################################

FROM maven:3.6-jdk-8-slim AS builder

# Get Click Count job and compile it
COPY ./java/flink-playground-clickcountjob /opt/flink-playground-clickcountjob
WORKDIR /opt/flink-playground-clickcountjob
RUN mvn clean install


###############################################################################
# Build Operations Playground Image
###############################################################################

FROM flink:1.10.0-scala_2.11

# Install Python
RUN apt-get update && \
    apt-get -y install curl python3 python3-distutils && \
    ln -s /usr/bin/python3 /usr/bin/python; 
# install pyFlink & kafka-python 
RUN curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py
RUN python ./get-pip.py
RUN python -m pip install apache-flink kafka-python

# to fix #1: Kafka connector not working for pylink
# https://github.com/garyfeng/flink-playgrounds/issues/1
COPY ./python/flink-connector-kafka-base_2.11-1.10.0.jar /usr/local/lib/python3.7/dist-packages/pyflink/lib/flink-connector-kafka-base_2.11-1.10.0.jar

WORKDIR /opt/flink/bin

# Copy Click Count Job
COPY --from=builder /opt/flink-playground-clickcountjob/target/flink-playground-clickcountjob-*.jar /opt/ClickCountJob.jar

# Copy pyflink word count example
COPY ./python/WordCount.py WordCount.py
COPY ./python/input.txt /tmp/input

# Copy pyflink click count example
COPY ./python/ClickEventGenerator.py ClickEventGenerator.py
COPY ./python/ClickEventCount.py ClickEventCount.py
