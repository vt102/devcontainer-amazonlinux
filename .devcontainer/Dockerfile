FROM public.ecr.aws/amazonlinux/amazonlinux:2023

SHELL ["/bin/bash", "-c"]

##
## Set up ec2-user similarly to an actual Amazon Linux EC2 instance
##
RUN yum update && \
    yum upgrade -y && \
    yum install -y jq git sudo unzip tar which findutils python3-pip && \
    groupadd -g 1000 ec2-user && \
    adduser -d /home/ec2-user  -g ec2-user -u 1000  -s /bin/bash ec2-user && \
    usermod -G adm ec2-user && \
    usermod -G wheel ec2-user

COPY <<EOF /etc/sudoers.d/91-ec2-user
# User rules for ec2-user
ec2-user ALL=(ALL) NOPASSWD:ALL
EOF

##
## Set up our command history
##
RUN SNIPPET="export PROMPT_COMMAND='history -a' && export HISTFILE=/commandhistory/.bash_history" \
    && mkdir /commandhistory \
    && touch /commandhistory/.bash_history \
    && chown -R ec2-user /commandhistory \
    && echo "$SNIPPET" >> "/home/ec2-user/.bashrc"

##
## Set up AWS CLI
##
RUN curl "https://awscli.amazonaws.com/awscli-exe-linux-$(uname -m).zip" -o "awscliv2.zip" && \
    unzip awscliv2.zip && \
    ./aws/install

##
## Set up golang compiler
##
RUN yum install -y golang-bin golang-docs

##
## Done installing packages
##
RUN yum clean packages

USER ec2-user

##
## Set up NodeJS
##
RUN curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash
RUN export NVM_DIR="$HOME/.nvm" && \
    [ -s "$NVM_DIR/nvm.sh" ] && \
    . "$NVM_DIR/nvm.sh" && \
    nvm install --lts
RUN source ~/.bashrc && \
    npm install -g aws-cdk

WORKDIR /workspace
