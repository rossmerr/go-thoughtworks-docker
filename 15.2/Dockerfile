FROM travcoltd/aspnet-grunt-docker

# Install Java.
RUN \  
  apt-get install -y \
  openjdk-7-jre \
  wget && \
  rm -rf /var/lib/apt/lists/*

# Define commonly used JAVA_HOME variable
ENV JAVA_HOME /usr/lib/jvm/java-7-openjdk-amd64

ENV GO_DOWNLOAD_URL http://download.go.cd/gocd-deb/go-agent-15.2.0-2248.deb

RUN wget -O /tmp/go-agent.deb ${GO_DOWNLOAD_URL}
RUN dpkg -i /tmp/go-agent.deb
RUN rm -f /tmp/go-agent.deb

# get rid of GO_SERVER AND GO_SERVER_PORT defaults. We'll use env variables.
RUN sed -i '/.*GO_SERVER.*/d' /etc/default/go-agent

# make sure container doesn't exit after starting agent
RUN sed -i 's/DAEMON=Y/DAEMON=N/' /etc/default/go-agent

# make sure that init.d script passes through environment variables
RUN sed -i 's/su -/su -p/' /etc/init.d/go-agent

# set GO_SERVER env variable to go-server (defined in /etc/hosts)
ENV GO_SERVER go-server

USER go

# Set the DNU PATH for the agent
ENV PATH /opt/dnx/runtimes/default/bin:$PATH

CMD /etc/init.d/go-agent start