# CoAL

The Cosmo Tech Acceleration Library (CoAL) provides a comprehensive set of tools and utilities to accelerate the development of solutions based on the Cosmo Tech platform. It offers a unified interface for interacting with Cosmo Tech APIs, managing data, and integrating with various cloud services.

The main components of CoAL are:

- `csm-data`: a powerful CLI tool designed to help Cosmo Tech solution modelers and integrators interact with multiple systems. It provides ready-to-use commands to send and retrieve data from various systems where a Cosmo Tech API could be integrated.

``` title="Example of csm-data commands"
# Get help on available commands
csm-data --help

# Get help on specific command groups
csm-data api --help
```

- `datastore`: provides a way to maintain local data during simulations and comes with `csm-data` commands to easily send those data to target systems.

``` sql title="Example of datastore commands"
from cosmotech.coal.store.store import Store
from cosmotech.coal.store.native_python import store_pylist

# Initialize and reset the data store
my_datastore = Store(reset=True)

# Create and store data
my_data = [{"foo": "bar"}, {"foo": "barbar"}, {"foo": "world"}, {"foo": "bar"}]
store_pylist("my_data", my_data)

# Query the data
results = my_datastore.execute_query("SELECT foo, count(*) as line_count FROM my_data GROUP BY foo").to_pylist()
print(results)
# > [{'foo': 'bar', 'line_count': 2}, {'foo': 'barbar', 'line_count': 1}, {'foo': 'world', 'line_count': 1}]
```

## Integration with run templates

All CoAL commands can be used in the run templates, by replacing complex custom code with standardized commands:

- Use `csm-data run-load-data` instead of custom data fetching.
- Use the `datastore` commands for intermediate processing.
- Use export commands for the final data output.

CoAL commands can be used to provide shortcuts for common operations, such as:

| Command            | Description                                   |
| ------------------ | --------------------------------------------- |
| `fetch-data`       | Standard data download template               |
| `send-to-adx`      | Export results to Azure Data Explorer (ADX)   |
| `validate-data`    | Common validation patterns                    |

## Best practices

### Performance

- Use `datastore` for intermediate processing instead of files.
- Leverage SQL for data transformations.
- Use batch operations for large datasets.
- Cache frequently accessed data.

### Code Organization

- Use CoAL commands instead of custom implementations.
- Standardize on CoAL patterns across projects.
- Document any custom extensions.
- Keep custom code minimal.

### Error Handling

- Leverage CoAL's built-in error handling.
- Use proper logging for debugging.
- Implement appropriate retry logic.
- Handle network timeouts gracefully.

## References

For more detailed information on CoAL, please refer to:

- [CoAL documentation](https://cosmo-tech.github.io/CosmoTech-Acceleration-Library/latest/)
