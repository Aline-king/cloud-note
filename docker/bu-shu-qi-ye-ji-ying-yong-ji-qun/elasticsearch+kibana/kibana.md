# Kibana

<figure><img src="../../../.gitbook/assets/image (238).png" alt=""><figcaption></figcaption></figure>

```
# docker pull kibana:7.17.0
```

```
# docker run --name kibana -e ELASTICSEARCH_HOSTS=http://192.168.255.157:9200 -p 5601:5601 \
-d kibana:7.17.0
```

```
# docker ps
CONTAINER ID   IMAGE                  COMMAND                  CREATED         STATUS         PORTS                                                                                  NAMES
fb60e73f9cd5   kibana:7.17.0          "/bin/tini -- /usr/l…"   2 minutes ago   Up 2 minutes   0.0.0.0:5601->5601/tcp, :::5601->5601/tcp                                              kibana
```

![image-20220212224524598](file:///G:/game/04\_%E5%AE%B9%E5%99%A8%E7%AE%A1%E7%90%86%E5%B7%A5%E5%85%B7Docker/04\_%E5%AE%B9%E5%99%A8%E7%AE%A1%E7%90%86%E5%B7%A5%E5%85%B7%20Docker/05\_Docker%E5%AE%B9%E5%99%A8%E5%8C%96%E9%83%A8%E7%BD%B2%E4%BC%81%E4%B8%9A%E7%BA%A7%E5%BA%94%E7%94%A8%E9%9B%86%E7%BE%A4/01\_%E7%AC%94%E8%AE%B0/Docker%E5%AE%B9%E5%99%A8%E5%8C%96%E9%83%A8%E7%BD%B2%E4%BC%81%E4%B8%9A%E7%BA%A7%E5%BA%94%E7%94%A8%E9%9B%86%E7%BE%A4.assets/image-20220212224524598.png?lastModify=1718205836)

\
