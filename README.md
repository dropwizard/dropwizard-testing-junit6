# Dropwizard Testing for JUnit 6

This library provides test helpers for [Dropwizard](https://www.dropwizard.io/) applications using [JUnit 6](https://junit.org/junit6/).

## Usage

Add the `dropwizard-testing-junit6` dependency to your `pom.xml`:

```xml
<dependency>
    <groupId>io.dropwizard.modules</groupId>
    <artifactId>dropwizard-testing-junit6</artifactId>
    <version>5.0.0-SNAPSHOT</version>
    <scope>test</scope>
</dependency>
```

Then, use the `DropwizardAppExtension` to test your Dropwizard application:

```java
import io.dropwizard.core.Application;
import io.dropwizard.core.Configuration;
import io.dropwizard.core.setup.Environment;
import io.dropwizard.testing.junit6.DropwizardAppExtension;
import jakarta.ws.rs.GET;
import jakarta.ws.rs.Path;
import jakarta.ws.rs.client.Client;
import jakarta.ws.rs.core.Response;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.RegisterExtension;

import static org.assertj.core.api.Assertions.assertThat;

public class ExampleTest {

    @RegisterExtension
    public static final DropwizardAppExtension<TestConfiguration> EXTENSION =
        new DropwizardAppExtension<>(TestApplication.class, "config.yml");

    @Test
    void testMyApplication() {
        final Client client = EXTENSION.client();
        final Response response = client.target(
            String.format("http://localhost:%d/my-resource", EXTENSION.getLocalPort()))
            .request()
            .get();

        assertThat(response.getStatus()).isEqualTo(200);
        assertThat(response.readEntity(String.class)).isEqualTo("Hello, world!");
    }

    public static class TestApplication extends Application<TestConfiguration> {
        @Override
        public void run(TestConfiguration configuration, Environment environment) {
            environment.jersey().register(new MyResource());
        }
    }

    public static class TestConfiguration extends Configuration {
    }

    @Path("/my-resource")
    public static class MyResource {
        @GET
        public String get() {
            return "Hello, world!";
        }
    }
}
```

## License

This project is licensed under the Apache License, Version 2.0. See the [`LICENSE`](LICENSE) file for details.
