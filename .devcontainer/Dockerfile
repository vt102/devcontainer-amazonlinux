FROM public.ecr.aws/amazonlinux/amazonlinux:2023

SHELL ["/bin/bash", "-c"]

RUN yum update && \
    yum upgrade -y && \
    yum install -y jq git sudo unzip tar which findutils && \
    groupadd -g 1000 ec2-user && \
    adduser -d /home/ec2-user  -g ec2-user -u 1000  -s /bin/bash ec2-user && \
    usermod -G adm ec2-user && \
    usermod -G wheel ec2-user

COPY <<EOF /etc/sudoers.d/91-ec2-user
# User rules for ec2-user
ec2-user ALL=(ALL) NOPASSWD:ALL
EOF

RUN curl "https://awscli.amazonaws.com/awscli-exe-linux-$(uname -m).zip" -o "awscliv2.zip" && \
    unzip awscliv2.zip && \
    ./aws/install

USER ec2-user

RUN curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash
RUN export NVM_DIR="$HOME/.nvm" && \
    [ -s "$NVM_DIR/nvm.sh" ] && \
    . "$NVM_DIR/nvm.sh" && \
    nvm install --lts
RUN source ~/.bashrc && \
    npm install -g aws-cdk

WORKDIR /workspace

