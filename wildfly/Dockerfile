FROM jboss/wildfly

USER root

RUN mv /etc/localtime /etc/localtime.backup
RUN ln -s /usr/share/zoneinfo/America/New_York /etc/localtime

USER jboss

COPY configuration/standalone-i2b2.xml /opt/jboss/wildfly/standalone/configuration/
COPY deployments/*	 /opt/jboss/wildfly/standalone/deployments/

#RUN /opt/jboss/wildfly/bin/add-user.sh admin Admin --silent
#CMD ["/opt/jboss/wildfly/bin/standalone.sh", "-b", "0.0.0.0", "-bmanagement", "0.0.0.0", "-c", "standalone-i2b2.xml"]

CMD ["/opt/jboss/wildfly/bin/standalone.sh", "-b", "0.0.0.0", "-c", "standalone-i2b2.xml"]