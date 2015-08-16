
```
struct inst {                   /* instruction */
    short   type;
    short   line;
    union {
        ....
        struct muf_socket *sock;        /* A ProtoMUCK socket               */
        ....
    } data;
};

struct muf_socket {             /* struct for MUF socket data */
   int socknum;                 /* The descriptor number for the socket */
   int connected;               /* Set to 1 if ever connected */
   int listening;               /* Set to 1 if successfully opened listening */
   int links;                   /* Number of instances of the socket. */
   int host;                    /* the host address integer */
   char *raw_input;             /* recieve queue for telnet connections. */
   char *raw_input_at;          /* for use in handling the recieve queue */
   int  inIAC;                  /* For correct telnet negotiations. */
   time_t connected_at;         /* Systime connection was made. */
   time_t last_time;            /* last time command recieved. */
   const char *hostname;        /* string host name for incoming cons */
   const char *username;        /* string user name for incoming cons */
   unsigned short userport;     /* userport number */
   struct huinfo *hu;           /* the new host/user resolver system */
   int commands;                /* number of commands entered. */
   int port;                    /* port number that LSOCKET is listening on */
   int usequeue;                /* toggles recieve buffer behavior */
   int usesmartqueue;           /* makes the socket completely telnet savy */
   int is_player;               /* means to not close the socket when clearing*/
   int rawmode;                 /* no queues */
   int readWaiting;             /* to support socket events */
#if defined(SSL_SOCKETS) && defined(USE_SSL)
   SSL *ssl_session;            /* SSL session data                             */
   int sslStatus;                /* flag set when SSL channel has collapsed */
   char *sslError;            /* error message associated with sslAbort */
#endif
#ifdef IPV6
   int ipv6;                    /* Defined if this socket is ipv6 */
   struct in6_addr *host6;      /* ipv6 host address */
#endif
};

struct huinfo { /* host/user information */
    struct hostinfo *h; /* hostname */
    struct husrinfo *u; /* username */
};

struct hostinfo { /* linked-list struct for the new host cacher */
    time_t           wupd;      /* time of last update */
    int              a;         /* ip */
#ifdef IPV6
    struct in6_addr  a6;        /* Version 6 IP */
#endif
    char             *name;     /* hostname */
    unsigned short   links;     /* number of links to this hostname */
    unsigned short   uses;      /* times used */
#if defined(HAVE_PTHREAD_H)
    pthread_t        thread;    /* if threading resolver is enabled, this is the thread doing the work (or NULL if finished) */
#endif
#if defined(HAVE_PTHREAD_H) || defined(WIN_VC)
    time_t           thread_start; /* POSIX time of when the thread started */
#endif
    struct hostinfo *next;
    struct hostinfo *prev;
};

struct husrinfo { /* linked-list struct for usernames (userports) */
    int              a;         /* ip address */
#ifdef IPV6
    struct in6_addr  a6;        /* Version 6 IP */
#endif
    unsigned short   uport;     /* the userport number */
    char            *user;      /* username */
    struct husrinfo *next;
    struct husrinfo *prev;
};

struct in6_addr
str2ip6(const char *ipstr)
{
    struct in6_addr x;
    inet_pton(AF_INET6, ipstr, &x);
    return x;
}


```

  * The `in6addr` struct type is defined inline inside interface.c, contrary to most other type definitions live.
  * The `SSL` type is defined by the OpenSSL C library. <ssl.h>