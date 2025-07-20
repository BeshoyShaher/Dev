# Prometheus to AppDynamics JMX Mapping

This document shows how the original Prometheus JMX patterns were converted to AppDynamics format.

## Pattern Conversion Examples

### Clinical Results V2 Patterns

**Prometheus Pattern:**
```yaml
- pattern: com.orchestral.esp.pipeline<space=.*Clinical.ResultsV2.* name=events.per.message>
```

**AppDynamics Equivalent:**
```xml
<jmx>
    <object-name>com.orchestral.esp.pipeline:space=*Clinical.ResultsV2*,name=events.per.message</object-name>
    <metric>
        <name>ESP|Pipeline|Clinical ResultsV2|Events Per Message</name>
        <value>Value</value>
    </metric>
</jmx>
```

### Log Stage Event Chain Patterns

**Prometheus Pattern:**
```yaml
- pattern: com.orchestral.esp.pipeline<stage=.*log.* chain=.*event.* name=events.per.message><>Max
```

**AppDynamics Equivalent:**
```xml
<jmx>
    <object-name>com.orchestral.esp.pipeline:stage=*log*,chain=*event*,name=events.per.message</object-name>
    <metric>
        <name>ESP|Pipeline|Log Stage|Event Chain|Events Per Message Max</name>
        <value>Max</value>
    </metric>
</jmx>
```

### Sequence Stage Patterns

**Prometheus Pattern:**
```yaml
- pattern: com.orchestral.esp.pipeline<stage=.*sequence.* name=completed><>Count
```

**AppDynamics Equivalent:**
```xml
<jmx>
    <object-name>com.orchestral.esp.pipeline:stage=*sequence*,name=completed</object-name>
    <metric>
        <name>ESP|Pipeline|Sequence Stage|Completed Count</name>
        <value>Count</value>
    </metric>
</jmx>
```

## Key Differences

### Pattern Syntax
- **Prometheus**: Uses `<key=value>` format with regex patterns like `.*`
- **AppDynamics**: Uses `key=value` format with wildcards like `*`

### Attribute Access
- **Prometheus**: Uses `<>AttributeName` to access specific attributes
- **AppDynamics**: Uses `<value>AttributeName</value>` in the metric definition

### Metric Names
- **Prometheus**: Typically generates flat metric names
- **AppDynamics**: Uses hierarchical names with pipe (`|`) separators

### Regex vs Wildcards
- **Prometheus**: `.*Clinical.ResultsV2.*` (regex)
- **AppDynamics**: `*Clinical.ResultsV2*` (wildcard)

## Complete Mapping Table

| Original Prometheus Pattern | AppDynamics Object Name | AppDynamics Metric Name |
|------------------------------|-------------------------|-------------------------|
| `com.orchestral.esp.pipeline<space=.*Clinical.ResultsV2.* name=events.per.message>` | `com.orchestral.esp.pipeline:space=*Clinical.ResultsV2*,name=events.per.message` | `ESP\|Pipeline\|Clinical ResultsV2\|Events Per Message` |
| `com.orchestral.esp.pipeline<space=.*Clinical.ResultsV2.* name=event.size>` | `com.orchestral.esp.pipeline:space=*Clinical.ResultsV2*,name=event.size` | `ESP\|Pipeline\|Clinical ResultsV2\|Event Size` |
| `com.orchestral.esp.pipeline<stage=.*log.* chain=.*event.* name=events.per.message><>Max` | `com.orchestral.esp.pipeline:stage=*log*,chain=*event*,name=events.per.message` | `ESP\|Pipeline\|Log Stage\|Event Chain\|Events Per Message Max` |
| `com.orchestral.esp.pipeline<stage=.*sequence.* name=completed><>Count` | `com.orchestral.esp.pipeline:stage=*sequence*,name=completed` | `ESP\|Pipeline\|Sequence Stage\|Completed Count` |

## Notes

1. **Whitelist Equivalent**: AppDynamics doesn't use a whitelist concept like Prometheus. Instead, you explicitly define each metric you want to collect.

2. **Automatic JVM Metrics**: AppDynamics automatically collects standard JVM metrics (heap, GC, threads, etc.) without explicit configuration.

3. **Metric Limits**: AppDynamics has default limits on the number of custom metrics. Ensure your configuration stays within these limits.

4. **Performance Impact**: Both systems have similar performance characteristics, but AppDynamics provides more granular control over collection intervals.