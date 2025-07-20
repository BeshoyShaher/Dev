# AppDynamics JMX Configuration Setup

This document provides instructions for configuring AppDynamics Java Agent to collect JMX metrics from the ESP Pipeline system.

## Configuration Files

- `custom-jmx-config.xml` - Main JMX configuration file for AppDynamics
- Original Prometheus configuration patterns have been converted to AppDynamics format

## Setup Instructions

### 1. Place Configuration File

Copy the `custom-jmx-config.xml` file to your AppDynamics agent configuration directory:

```bash
# Typical location for AppDynamics agent
cp custom-jmx-config.xml /path/to/appdynamics-agent/conf/
```

### 2. Update AppDynamics Agent Configuration

Add the following JVM arguments to your Java application startup:

```bash
# Basic AppDynamics agent configuration
-javaagent:/path/to/appdynamics-agent/javaagent.jar
-Dappdynamics.agent.applicationName=YourApplicationName
-Dappdynamics.agent.tierName=YourTierName
-Dappdynamics.agent.nodeName=YourNodeName

# Enable custom JMX configuration
-Dappdynamics.jmx.config.file=/path/to/appdynamics-agent/conf/custom-jmx-config.xml
```

### 3. Alternative: Update controller-info.xml

You can also configure JMX collection in the `controller-info.xml` file:

```xml
<controller-info>
    <!-- Other configuration -->
    <jmx-config-file>custom-jmx-config.xml</jmx-config-file>
</controller-info>
```

## Metric Hierarchy

The metrics will appear in AppDynamics under the following hierarchy:

```
Application Infrastructure Performance
└── ESP
    └── Pipeline
        ├── Clinical ResultsV2
        │   ├── Events Per Message
        │   ├── Event Size
        │   ├── Events Per Sequence
        │   ├── Completed
        │   └── Remaining
        ├── Log Stage
        │   └── Event Chain
        │       ├── Events Per Message Max
        │       ├── Event Size Max
        │       ├── Issue Count
        │       └── Remaining
        └── Sequence Stage
            ├── Events Per Sequence
            ├── Remaining
            ├── Work Avoidance Count
            ├── Issue Count
            ├── Completed Count
            ├── Failed Count
            └── Events Read Count
```

## Metric Types Captured

Based on your Prometheus configuration, the following metric types are captured:

- **Value**: Standard metric values
- **Count**: Counter metrics (cumulative)
- **Max**: Maximum values over time

## Verification

After deployment, you can verify the metrics are being collected by:

1. Checking the AppDynamics Controller UI
2. Looking in the Application Infrastructure Performance section
3. Navigating to the ESP|Pipeline metric hierarchy
4. Reviewing agent logs for any JMX collection errors

## Troubleshooting

- Ensure the JMX MBeans are registered and accessible
- Check AppDynamics agent logs for JMX configuration errors
- Verify the object name patterns match your actual MBean names
- Confirm the AppDynamics agent has permission to access JMX metrics

## Notes

- The configuration preserves all the patterns from your original Prometheus setup
- Wildcard patterns (`*`) are supported in AppDynamics object names
- Metric names use the pipe (`|`) separator for hierarchical organization in AppDynamics