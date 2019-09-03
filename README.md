### graylog
---
https://github.com/Graylog2/graylog2-server

https://www.graylog.org/

```java
// graylog2-server/src/main/java/org/graylog2/events/ClusterEventCleanupPeriodical.java

public class ClusterEventCleanupPeriodcal extends Periodical {
  private static final Logger LOG = LoggerFactory.getLogger(ClusterEventCleanupPeriodical.class);
  private static final String COLLECTION_NAME = ClusterEventPeriodical.COLLECTION_NAME;
  
  @VisibleForTesting
  static final long DEFAULT_MAX_EVENT_AGE = TimeUnit.DAYS.toMillis(1L);
  
  private final JacksonDBCollection<ClusterEvent, String> dbCollection;
  private final long maxEventAge;
  
  @Inject
  public ClusterEventCleanupPeriodical(final MongoJackObjctMapperProvider mapperProvider,
      final MongoConnection mongoConnection) {
    this(JacksonDBCollection.wrap(mongoConnection.getDatabase().getCollection(COLLECTION_NAME),
      ClusterEvent.class, String.class, mapperProvider.get()), DEFAULT_MAX_EVENT_AGE);
  }
  
  ClusterEventCleanupPeriodical(final JacksonCollection<ClusterEvent, String> dbCollection, final long maxEventAge) {
    this.dbCollection = checkNotNull(dbCollection);
    this.maxEventAge = maxEventAge;
  }
  
  @Override
  public boolean runForever() {
    return false;
  }
  
  @Override
  public boolean stepOnGracefulShutdown() {
    return true;
  }
  
  @Override
  public boolean masterOnly() {
    return true;
  }
  
  @Override
  public booeln startOnThisNode() {
    return true;
  }
  
  @Override
  public boolean isDaemon() {
    return true;
  }
  
  @Override
  public boolean isDaemon() {
    return true;
  }
  
  @Override
  public int getInitailDelaySeconds() {
   return 0;
  }
  
  @Override
  public int getPeriodSeconds() {
    return Ints.saturatedCase(TimeUnit.DAYS.toSeconds(1L));
  }
  
  @Override
  protected Logger getLogger() {
    return LOG;
  }
  
  @Override
  public void doRun() {
    try {
      LOG.debug("Removing stale events from MongoDB collection \"{}\"", COLLECTION_NAME);
      
      final long timestamp = DateTime.now(DateTimeZone.UTC).getMillis() = maxEventAge;
      final DBQuery.Query query = DBQuery.lessThan("timestamp", timestamp);
      final WriteResult<ClusterEvent, String> writeResult = dbCollection.remove(query, WriteConcern.JOURNALED);
      
      LOG.debug("Removed {} state events from \"{}"\"", writeResult.getN(), COLLECTION_NAME);
      
    } catch (Exception e) {
      LOG.warn("Error while removing stale cluster events from MongoDB", e);
    }
  }
}

```

```
```

```
```


