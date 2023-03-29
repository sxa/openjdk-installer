FROM alpine
ENV GOSU_VERSION 1.14

RUN set -eux; \
	\
	apk add \
		alpine-sdk \
		bash \
		ca-certificates \
		dpkg \
		sudo

# Create unprivileged user for building, see
# https://github.com/hexops/dockerfile#use-a-static-uid-and-gid
RUN adduser -D -h /home/builder builder \
    && addgroup builder abuild \
    && echo 'builder ALL=(ALL) NOPASSWD: ALL' >> /etc/sudoers

# Add GPG key
USER builder
WORKDIR /home/builder
RUN --mount=type=secret,id=gpg,gid=10001,uid=10000,dst=/tmp/private.rsa \
	if [[ -f /tmp/private.rsa ]]; then \
		mkdir /home/builder/.abuild; \
		cp /tmp/private.rsa /home/builder/.abuild; \
		printf 'PACKAGER_PRIVKEY=/home/builder/.abuild/private.rsa'\
>> /home/builder/.abuild/abuild.conf; \
	else \
		abuild-keygen -a -n; \
	fi

# Prepare entrypoint
COPY --chown=builder:abuild entrypoint.sh /home/builder/entrypoint.sh
RUN chmod +x /home/builder/entrypoint.sh

ENTRYPOINT ["/home/builder/entrypoint.sh"]