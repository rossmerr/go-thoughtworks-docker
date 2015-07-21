FROM travcoltd/aspnet-grunt-docker

# Install Java.
RUN \
  apt-get update && \
  apt-get install -y \
  openjdk-7-jre \
  wget && \
  rm -rf /var/lib/apt/lists/*

# Define commonly used JAVA_HOME variable
ENV JAVA_HOME /usr/lib/jvm/java-7-openjdk-amd64

# Create go user
RUN useradd -ms /bin/bash go

# Copy bash profile for go user
COPY .bash_profile /home/go

# Install vnext under go user
USER go
RUN bash -c "source $DNX_USER_HOME/dnvm/dnvm.sh \
	&& dnvm install $DNX_VERSION -a default \
	&& dnvm alias default | xargs -i ln -s $DNX_USER_HOME/runtimes/{} $DNX_USER_HOME/runtimes/default"

USER root

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

CMD su go -c '/etc/init.d/go-agent start'