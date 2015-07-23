FROM ubuntu:14.04

RUN curl -sL https://deb.nodesource.com/setup | bash -

RUN apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 3FA7E0328081BFF6A14DA29AA6A19B38D3D831EF
RUN echo "deb http://download.mono-project.com/repo/debian wheezy main" | sudo tee /etc/apt/sources.list.d/mono-xamarin.list

# Install Java, Install libuv for Kestrel from source code (binary is not in wheezy and one in jessie is still too old)
RUN \
  apt-get update && \
  apt-get install -y --force-yes \
  openjdk-7-jre \
  unzip \
  git \
  nodejs \
  autoconf \
  automake \
  build-essential \
  libtool \  
  vim \   
  mono-complete \
  curl \
  wget && \
  rm -rf /var/lib/apt/lists/*

RUN LIBUV_VERSION=1.4.2 \
	&& curl -sSL https://github.com/libuv/libuv/archive/v${LIBUV_VERSION}.tar.gz | tar zxfv - -C /usr/local/src \
	&& cd /usr/local/src/libuv-$LIBUV_VERSION \
	&& sh autogen.sh && ./configure && make && make install \
	&& rm -rf /usr/local/src/libuv-$LIBUV_VERSION \
	&& ldconfig

ENV JAVA_HOME /usr/lib/jvm/java-7-openjdk-amd64

RUN echo PATH="/var/go/.dnx/runtimes/default/bin:/var/go/.dnx/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games" > environment

# set GO_SERVER env variable to go-server (defined in /etc/hosts)
ENV GO_SERVER go-server

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

USER go

RUN touch /var/go/.bashrc

ENV DNX_VERSION 1.0.0-beta4
ENV DNX_USER_HOME /var/go/.dnx

RUN curl -sSL https://raw.githubusercontent.com/aspnet/Home/dev/dnvminstall.sh | DNX_BRANCH=dev sh 
RUN bash -c "source $DNX_USER_HOME/dnvm/dnvm.sh \
		&& dnvm install $DNX_VERSION -a default \
		&& dnvm alias default | xargs -i ln -s $DNX_USER_HOME/runtimes/{} $DNX_USER_HOME/runtimes/default"


ENV PATH /var/go/.dnx/runtimes/default/bin:$PATH

CMD /etc/init.d/go-agent start