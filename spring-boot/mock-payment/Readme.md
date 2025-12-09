# Unified Payments Gateway — Senior Engineer Spring Boot Case Study

## High Level Architecture

```text
                   +-----------------------------+
                   |         REST Client         |
                   |  (cURL/Postman/Frontend)    |
                   +--------------+--------------+
                                  |
                                  v
                    +---------------------------+
                    |     PaymentController     |
                    +---------------------------+
                                  |
                                  v
                    +---------------------------+
                    |     PaymentService        |
                    |  - validation completed   |
                    |  - add traceId to MDC     |
                    |  - delegates to processor |
                    +---------------------------+
                                  |
           -------------------------------------------------
           |                                               |
           v                                               v
+---------------------------+               +---------------------------+
|   StripePaymentProcessor  |               | RazorpayPaymentProcessor |
+---------------------------+               +---------------------------+
           |                               |
           |                               |
           -----------+-----------+---------
                       |
                       v
                +--------------+
                | PaymentResponse DTO
                +--------------+

```

| Requirement                                              | Where It Appears                                                                                    |
| -------------------------------------------------------- | --------------------------------------------------------------------------------------------------- |
| **Scheduling & Background Tasks**                        | `SettlementJob`, runs nightly to summarize processed payments                                       |
| **Logging & Observability (JSON logs + MDC TraceId)**    | `logback-spring.xml`, `RequestCorrelationFilter`, payment service                                   |
| **Actuator + Custom Endpoint + Custom Health Indicator** | `/actuator/payment-status`, `PaymentProviderHealthIndicator`                                        |
| **Multiple Profiles**                                    | `application-dev.yml`, `application-prod.yml` (processor switches between Fake / Stripe / Razorpay) |
| **REST + Validation**                                    | `PaymentController`, DTOs with `@Valid`, custom exception handler                                   |
| **Bean Management + Auto-selected PaymentProcessor**     | Strategy Pattern + conditional beans based on property `payment.provider=stripe`                    |
| **ConfigurationProperties**                              | `PaymentConfigProperties` for API keys, retry counts                                                |
| **Jackson Customization**                                | Date formatting, enum serialization, custom `ObjectMapper` configuration                            |

**Project Structure**

```text
unified-payments-gateway
 ├── src
 │   ├── main
 │   │   ├── java
 │   │   │   └── com.example.payments
 │   │   │       ├── controller
 │   │   │       │   └── PaymentController.java
 │   │   │       ├── service
 │   │   │       │   └── PaymentService.java
 │   │   │       ├── processor
 │   │   │       │   ├── PaymentProcessor.java
 │   │   │       │   ├── StripePaymentProcessor.java
 │   │   │       │   ├── RazorpayPaymentProcessor.java
 │   │   │       │   └── FakePaymentProcessor.java
 │   │   │       ├── config
 │   │   │       │   ├── PaymentConfigProperties.java
 │   │   │       │   ├── ProcessorConfig.java
 │   │   │       │   ├── LoggingConfig.java
 │   │   │       │   └── JacksonConfig.java
 │   │   │       ├── scheduler
 │   │   │       │   └── SettlementJob.java
 │   │   │       ├── actuator
 │   │   │       │   ├── PaymentProviderHealthIndicator.java
 │   │   │       │   └── PaymentStatusEndpoint.java
 │   │   │       ├── dto
 │   │   │       │   ├── PaymentRequest.java
 │   │   │       │   └── PaymentResponse.java
 │   │   │       ├── exception
 │   │   │       │   ├── GlobalExceptionHandler.java
 │   │   │       │   └── InvalidPaymentException.java
 │   │   │       └── filter
 │   │   │           └── RequestCorrelationFilter.java
 │   │   └── resources
 │   │       ├── application.yml
 │   │       ├── application-dev.yml
 │   │       ├── application-prod.yml
 │   │       ├── logback-spring.xml
 │   │       └── banner.txt
 │   └── test (not used per requirement)
 └── pom.xml
```
