FROM nypl/circ-base
ENV LIBSIMPLE_DIR=/var/www/circulation
ENV timezone="US/Eastern"

MAINTAINER Library Simplified <info@librarysimplified.org>

RUN apt-get update && apt-get install -y --no-install-recommends \
        cron \
        supervisor \
    && rm -rf /var/lib/apt/lists/*

# Switch to local timezone
RUN echo $timezone > /etc/localtime

# Add run script to keep scripts from running simultaneously
COPY run /var/www/circulation/bin
RUN chmod 744 /var/www/circulation/bin/run

# Add initialize script. This will only run if the $libsimple_init variable
# has been passed in via `docker run`, with optional $threem_start_date
# variable.
COPY initialize /var/www/circulation/bin
RUN chmod 744 /var/www/circulation/bin/initialize

# Create cron tasks & logfile
RUN touch /var/log/cron.log
COPY libsimple_crontab /etc/cron.d/circulation

WORKDIR bin
VOLUME /var/log/cron.log /var/log/libsimple /var/run/libsimple

COPY supervisord.conf /etc/supervisor/conf.d
CMD ["-c", "/usr/bin/supervisord"]