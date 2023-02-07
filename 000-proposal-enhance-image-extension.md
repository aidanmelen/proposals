<!-- This template is provided as an example with sections you may wish to comment on with respect to your proposal. Add or remove sections as required to best articulate the proposal. -->

# Enhance Image Extension for custom authentication
  
Running Kafka can be a large responsibility for many teams. Even when Kafka is automated and managed by a best in-class operator like Strimzi. For this reason, many have the desire to host their Kafka cluster using a cloud provider and use strimzi to managed the other components surrounding the cluster.

Enhance image extension by easily allowing teams to add custom jars (already complete) and provide custom configuration properties for those jars.

## Current situation
  
The `kafka-base` image provided by strimzi can easily be extended for any custom Jar. However, the config generate scripts in the `kafka-base` image prevent overriding the `security.protocol` and adding custom properties without also overriding the entire generate script making it difficult to configure a custom jar.

## Motivation

The strimzi operator does not easiy allow components (i.e. kakfa connect, mm2) to authenitcate with a kafka cluster hosted by cloud provider. Sometimes this means authenticating using a custom mechansim, such as SASL/IAM, which is far outside the scope of the Strimzi operator due the need to install custom JARs and configure arbitrary mechansims that collide with existing authentication mechanism config generation

## Proposal

Update the generate scripts to interpolate a <COMPONENT>_ADDITIONAL_CONFIGURATION and append to the bottom of the properties file. The value of this environment variable would be a multi-line string of ini formatted properties. The properties supplied by this environment variable would not be overriden by Strimzi.
  
For example: https://github.com/strimzi/strimzi-kafka-operator/pull/8064

## Affected/not affected projects
  
the generate script for the following components would be modified.

- Kafka Connect
- MirrmorMaker2
- MirrmorMakerProducer
- MirrmorMakerConsumer
- Kafka Bridge

## Rejected alternatives

A `.spec.authentication.mechansim.custom` option has been floated around in a number of proposals. This would be the prefered method for allowing authentication overrides. However, these focus on the CRD for providing an option for custom authentication whereas I am proposing this be solved by the customer using image extension since the strimzi project should focus on strimzi kafka and simply provide a valid extension path.
