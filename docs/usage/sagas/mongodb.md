# MongoDB

MongoDB is an easy to use saga repository, because setup is easy. There is no need for class mapping, the saga instances can be persisted easily using a MongoDB collection.

To use MongoDB, an additional interface and property are required on saga instances. The interface, `IVersionedSaga`, contains a `Version` property that is used to support optimistic concurrency versioning.

```cs {3,10}
public class OrderState :
    SagaStateMachineInstance,
    IVersionedSaga
{
    public Guid CorrelationId { get; set; }
    public string CurrentState { get; set; }

    public DateTime? OrderDate { get; set; }

    public int Version { get; set; }
}
```

### Container Integration

To configure MongoDB as the saga repository for a saga, use the code shown below using the _AddMassTransit_ container extension. This will configure MongoDB to connect to the local MongoDB instance on the default port using Optimistic concurrency.

```cs {4}
container.AddMassTransit(cfg =>
{
    cfg.AddSagaStateMachine<OrderStateMachine, OrderState>()
        .MongoDbRepository(r =>
        {
            r.Connection = "mongodb://127.0.0.1";
            r.DatabaseName = "orderdb";
        });
});
```

In the example above, saga instances are stored in a collection named `order.states`. The collection name can be specified using the _CollectionName_ property. Alternatively, a collection name formatter can be specified using the _CollectionNameFormatter_ method.

```cs {9}
container.AddMassTransit(cfg =>
{
    cfg.AddSagaStateMachine<OrderStateMachine, OrderState>()
        .MongoDbRepository(r =>
        {
            r.Connection = "mongodb://127.0.0.1";
            r.DatabaseName = "orderdb";

            r.CollectionName = "orders";
        });
});
```
