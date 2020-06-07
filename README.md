The project initially used an openLDAP image as the source of authentication.

Later in the project, it was identified that Telefonica uses Active Directory for authentication, which is not 100% compatible with openLDAP.

For this reason, and [Active Directory](../activedirectory/README.md) VM was added to the project, in order to enable development.

Therefore, the openLDAP docker container is no longer used. The files used to start the container are still kept in this folder for reference.
In addition, the container specifications in the docker-compose file are still present, but commented out. Just for reference, this is the configuration used in the docker-compose file:

```yaml
  openldap:
    # https://github.com/osixia/docker-openldap
    image: osixia/openldap:1.2.2
    container_name: openldap
    environment:
      LDAP_LOG_LEVEL: "256"
      LDAP_ORGANISATION: "Example Inc."
      LDAP_DOMAIN: "example.org"
      LDAP_BASE_DN: ""
      LDAP_ADMIN_PASSWORD: "admin" # user: cn=admin,dc=example,dc=org
      LDAP_CONFIG_PASSWORD: "config"
      LDAP_READONLY_USER: "false"
      #LDAP_READONLY_USER_USERNAME: "readonly"
      #LDAP_READONLY_USER_PASSWORD: "readonly"
      LDAP_RFC2307BIS_SCHEMA: "false"
      LDAP_BACKEND: "mdb"
      LDAP_TLS: "true"
      LDAP_TLS_CRT_FILENAME: "ldap.crt"
      LDAP_TLS_KEY_FILENAME: "ldap.key"
      LDAP_TLS_CA_CRT_FILENAME: "ca.crt"
      LDAP_TLS_ENFORCE: "false"
      LDAP_TLS_CIPHER_SUITE: "SECURE256:-VERS-SSL3.0"
      LDAP_TLS_PROTOCOL_MIN: "3.1"
      LDAP_TLS_VERIFY_CLIENT: "demand"
      LDAP_REPLICATION: "false"
      #LDAP_REPLICATION_CONFIG_SYNCPROV: "binddn="cn=admin,cn=config" bindmethod=simple credentials=$LDAP_CONFIG_PASSWORD searchbase="cn=config" type=refreshAndPersist retry="60 +" timeout=1 starttls=critical"
      #LDAP_REPLICATION_DB_SYNCPROV: "binddn="cn=admin,$LDAP_BASE_DN" bindmethod=simple credentials=$LDAP_ADMIN_PASSWORD searchbase="$LDAP_BASE_DN" type=refreshAndPersist interval=00:00:00:10 retry="60 +" timeout=1 starttls=critical"
      #LDAP_REPLICATION_HOSTS: "#PYTHON2BASH:['ldap://ldap.example.org','ldap://ldap2.example.org']"
      KEEP_EXISTING_CONFIG: "false"
      LDAP_REMOVE_CONFIG_AFTER_SETUP: "true"
      LDAP_SSL_HELPER_PREFIX: "ldap"
    tty: true
    stdin_open: true
    volumes:
      - /var/lib/ldap
      - /etc/ldap/slapd.d
      - /container/service/slapd/assets/certs/
      - ./openldap/startup:/container/service/slapd/assets/config/bootstrap/ldif/custom
    ports:
      - "389:389"
      - "636:636"
    domainname: "example.org" # important: same as hostname
    hostname: "example.org"
    command: "--copy-service"
    networks:
      - atwizard.dev
    restart: "always"
```