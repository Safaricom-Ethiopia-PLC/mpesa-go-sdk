# `mpesa-go-sdk`: The Best M-Pesa SDK for Golang

![coverage](https://img.shields.io/badge/coverage-100.0%25-brightgreen)
[![Go Reference](https://pkg.go.dev/badge/github.com/Safaricom-Ethiopia-PLC/mpesa-go-sdk.svg)](https://pkg.go.dev/github.com/Safaricom-Ethiopia-PLC/mpesa-go-sdk)
[![Go Report Card](https://goreportcard.com/badge/github.com/Safaricom-Ethiopia-PLC/mpesa-go-sdk)](https://goreportcard.com/report/github.com/Safaricom-Ethiopia-PLC/mpesa-go-sdk) [![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://mit-license.org/Safaricom-Ethiopia-PLC)

## Overview

`mpesa-go-sdk` is a Go SDK for interacting with Safaricom's M-Pesa API. This SDK simplifies integration with M-Pesa's services, enabling operations such as B2C payments, C2B URL registration, USSD Push payments, transaction status queries, account balance checks, and transaction reversals.

## Documentation

For further detailed documentation and api keys you can access it [here.](https://developer.safaricom.et/documentation)

## Features

- **Authentication**: Retrieve OAuth2 access tokens.
- **B2C Payments**: Transfer funds from a business account to a customer account.
- **C2B URL Registration**: Register URLs for payment notifications.
- **USSD Push**: Initiate USSD-based payment requests.
- **Transaction Status**: Query the status of transactions.
- **Account Balance**: Retrieve M-Pesa account balances.
- **Transaction Reversal**: Reverse a completed M-Pesa transaction.

## Requirements

- Go (Golang) Installed: The SDK is a Go module and requires a Go development environment. Make sure that Go is installed on your system. You can download it from the official Go website: [https://golang.org/dl/](https://golang.org/dl/).

- M-Pesa Developer Account: You need access to the Safaricom Ethiopia Developer ([https://developer.safaricom.et/documentation](https://developer.safaricom.et/documentation)) Portal to obtain the following credentials:
  - `CONSUMER_KEY`
  - `CONSUMER_SECRET`

## Installation

To install the SDK, use the following command:

```bash
go get github.com/Safaricom-Ethiopia-PLC/mpesa-go-sdk
```

## Configuration

### Quick Start - Initialize the MpesaClient

```go
package main

import (
 "fmt"
 "log"

 "github.com/Safaricom-Ethiopia-PLC/mpesa-go-sdk"
 "github.com/Safaricom-Ethiopia-PLC/mpesa-go-sdk/config"
)

func main() {
 // Load configuration from environment variables
 cfg, err := config.NewFromEnv()
 if err != nil {
  log.Fatal(err.Error())
 }

 // Create a new Mpesa client
 app := mpesa-go-sdk.New(cfg)
 fmt.Println("Application is created with: ", *app)
}
```

### Environment variables

To configure this application you can either create the config struct by yourself or you can create .env file
this package depends on [joho/godotenv/autoload](https://github.com/joho/godotenv/) package this will load the .env
to the program.
Simple .env looks like this

```bash
# Required credentials
CONSUMER_SECRET=your_consumer_secret_here
CONSUMER_KEY=your_consumer_key_here

# Optional settings with default values if not provided
MAX_CONCURRENT_CONN=1000         # Optional: defaults to 1000
MAX_RETRIES=3                    # Optional: defaults to 3
TIMEOUT=5                        # Optional: defaults to 5
LOG_LEVEL=DEBUG                  # Optional: defaults to DEBUG
ENVIROMENT=SANDBOX               # Optional: defaults to SANDBOX
```

## Usage

### Register C2B URL

```go
// Register C2B URL Example
res, err := app.RegisterNewURL(
    c2b.RegisterC2BURLRequest{
        ShortCode:       "802000",
        ResponseType:    "Completed",
        CommandID:       "RegisterURL",
        ConfirmationURL: "https://www.myservice:8080/confirmation",
        ValidationURL:   "https://www.myservice:8080/validation",
    })
if err != nil {
    log.Printf("failed to register C2B URL: %v\n", err)
}
fmt.Println("C2B URL Registration Response: ", res)
```

### Simulate C2B Payment

```go
// Simulate C2B Payment Example
res, err := app.SimulateCustomerInitiatedPayment(c2b.SimulateCustomerInititatedPayment{
    CommandID:     "CustomerPayBillOnline",
    Amount:        110,
    Msisdn:        "251945628580",
    BillRefNumber: "091091",
    ShortCode:     "443443",
})
if err != nil {
    log.Printf("failed to simulate C2B payment: %v\n", err)
}
fmt.Println("C2B Payment Simulation Response: ", res)
```

### Make B2C Payment

```go
id := uuid.New().String()

// B2C Payment Example
response, err := app.MakeB2CPaymentRequest(b2c.B2CRequest{
    InitiatorName:            "apiuser",
    SecurityCredential:       "PU8f0AptZr16W28uzZy8+Ke4ww+HDk6/WXGurNcKREm7ihjUHL0TGWBxWbIzhftZkEms6LHhZlzh36LtAjLLxLiCRXHIW5Fv6oqOIsrl9pMw0F5pfEPMzDEXNlotjMpaFcEFS1GpnHWkIOaguXMNaf0Uev49rjzER495LMP3Z9EIPJmOuOI5QUZ6h3udctyyKIeUBdab0vf0zATY66Zm9XZc2CHHx3NsyU7i680s1OWreZ7SobuXsEyjZlh4hb1G0HNICFt/kp0PZN8Pt09qBeLX5BE1Tre0bb4v66AatJEuXQA39VJCZ6A+UldKyb5HLsdQHn+eZvd/K2yLtwpCxA==",
    OriginatorConversationID: id,
    CommandID:                types.BusinessPaymentCommand,
    Occasion:                 "Occasion",
    Amount:                   1030,
    PartyA:                   600000,
    PartyB:                   251700404709,
    Remarks:                  "Salary Payment",
    QueueTimeOutURL:          "https://yourdomain.com/timeout",
    ResultURL:                "https://yourdomain.com/result",
})
if err != nil {
    log.Fatalf("failed to make b2c payment: %v", err)
}
fmt.Println("B2C Payment Response: ", response)
```

### Transaction Status Query

```go
// Transaction Status Query Example
res, err := app.MakeTransactionStatusQuery(transaction.TransactionStatusRequest{
    Initiator:                "apitest",
    SecurityCredential:       "lMhf0UqE4ydeEDwpUskmPgkNDZnA6NLi7z3T1TQuWCkH3/ScW8pRRnobq/AcwFvbC961+zDMgOEYGm8Oivb7L/7Y9ED3lhR7pJvnH8B1wYis5ifdeeWI6XE2NSq8X1Tc7QB9Dg8SlPEud3tgloB2DlT+JIv3ebIl/J/8ihGVrq499bt1pz/EA2nzkCtGeHRNbEDxkqkEnbioV0OM//0bv4K++XyV6jUFlIIgkDkmcK6aOU8mPBHs2um9aP+Y+nTJaa6uHDudRFg0+3G6gt1zRCPs8AYbts2IebseBGfZKv5K6Lqk9/W8657gEkrDZE8Mi78MVianqHdY/8d6D9KKhw==",
    CommandID:                "TransactionStatusQuery",
    TransactionID:            "0",
    OriginatorConversationID: "AG-20190826-0000777ab7d848b9e721",
    PartyA:                   "1020",
    IdentifierType:           "4",
    ResultURL:                "https://webhook.site/7ed4b055-fa4d-45f3-ae1f-328c52aa4d7d",
    QueueTimeOutURL:          "https://webhook.site/7ed4b055-fa4d-45f3-ae1f-328c52aa4d7d",
    Remarks:                  "Trans Status",
    Occasion:                 "Query trans status",
})
if err != nil {
    log.Printf("failed to make transaction status query: %v\n", err)
}
fmt.Println("Transaction Status Query Response: ", res)
```

### Account Balance Query

```go
// Account Balance Query Example
id = uuid.New().String()
resp, err := app.MakeAccountBalanceQuery(account.AccountBalanceRequest{
    Initiator:                "apiuser",
    IdentifierType:           types.ShortCodeIdentifierType,
    SecurityCredential:       "PU8f0AptZr16W28uzZy8+Ke4ww+HDk6/WXGurNcKREm7ihjUHL0TGWBxWbIzhftZkEms6LHhZlzh36LtAjLLxLiCRXHIW5Fv6oqOIsrl9pMw0F5pfEPMzDEXNlotjMpaFcEFS1GpnHWkIOaguXMNaf0Uev49rjzER495LMP3Z9EIPJmOuOI5QUZ6h3udctyyKIeUBdab0vf0zATY66Zm9XZc2CHHx3NsyU7i680s1OWreZ7SobuXsEyjZlh4hb1G0HNICFt/kp0PZN8Pt09qBeLX5BE1Tre0bb4v66AatJEuXQA39VJCZ6A+UldKyb5HLsdQHn+eZvd/K2yLtwpCxA==",
    OriginatorConversationID: id,
    CommandID:                types.BusinessPaymentCommand,
    PartyA:                   600000,
    Remarks:                  "Salary Payment",
    QueueTimeOutURL:          "https://yourdomain.com/timeout",
    ResultURL:                "https://yourdomain.com/result",
})
if err != nil {
    log.Fatalf("failed to make account balance query: %v", err)
}
fmt.Println("Account Balance Response: ", resp)
```

### Transaction Reversal

```go
// Transaction Reversal Example
id = uuid.New().String()
res, err := app.MakeTransactionReversalRequest(transaction.TransactionReversalRequest{
    Initiator:                "appuser",
    TransactionID:            "LKXXXX1234",
    SecurityCredential:       "PU8f0AptZr16W28uzZy8+Ke4ww+HDk6/WXGurNcKREm7ihjUHL0TGWBxWbIzhftZkEms6LHhZlzh36LtAjLLxLiCRXHIW5Fv6oqOIsrl9pMw0F5pfEPMzDEXNlotjMpaFcEFS1GpnHWkIOaguXMNaf0Uev49rjzER495LMP3Z9EIPJmOuOI5QUZ6h3udctyyKIeUBdab0vf0zATY66Zm9XZc2CHHx3NsyU7i680s1OWreZ7SobuXsEyjZlh4hb1G0HNICFt/kp0PZN8Pt09qBeLX5BE1Tre0bb4v66AatJEuXQA39VJCZ6A+UldKyb5HLsdQHn+eZvd/K2yLtwpCxA==",
    PartyA:                   "600000",
    CommandID:                types.TransactionReversalCommand,
    IdentifierType:           types.ShortCodeIdentifierType,
    OriginatorConversationID: id,
    Amount:                   1000,
    Remarks:                  "Reversing transaction",
    ResultURL:                "https://yourdomain.com/result",
    QueueTimeOutURL:          "https://yourdomain.com/timeout",
})
if err != nil {
    log.Fatalf("failed to make transaction reversal request: %v", err)
}
fmt.Println("Transaction Reversal Response: ", res)
```

### USSD Push Payment

```go
// USSD Payment Example
id = uuid.New().String()
ressss, err := app.USSDPaymentRequest(c2b.USSDPaymentRequest{
    MerchantRequestID: id,
    BusinessShortCode: "1020",
    Password:          "M2VkZGU2YWY1Y2RhMzIyOWRjMmFkMTRiMjdjOWIwOWUxZDFlZDZiNGQ0OGYyMDRiNjg0ZDZhNWM2NTQyNTk2ZA==",
    Timestamp:         "20240918055823",
    TransactionType:   "CustomerPayBillOnline",
    Amount:            20,
    PartyA:            "251710404709",
    PartyB:            "1020",
    PhoneNumber:       "251700404709",
    CallBackURL:       "https://www.myservice:8080/result",
    AccountReference:  "Partner Unique ID",
    TransactionDesc:   "Payment Reason",
    ReferenceData: []c2b.ReferenceDataRequest{
        {
            Key:   "ThirdPartyReference",
            Value: "Ref-12345",
        },
    },
})
if err != nil {
    log.Printf("failed to make USSD payment request: %v", err)
} else {
    fmt.Println("USSD Payment Response: ", ressss)
}
```

## Contributing

Contributions are welcome! Please follow these steps:

1. **Fork the repository** on GitHub.
2. **Clone your fork** to your local machine.

   ```bash
   git clone https://github.com/Safaricom-Ethiopia-PLC/mpesa-python-sdk.git
   ```

3. **Create a new feature branch**.

   ```bash
   git checkout -b feature/<your-feature-name>
   ```

4. **Make your changes** and commit them.

   ```bash
   git commit -am "Add new <short feature description>"
   ```

5. **Push your branch** to your fork.

   ```bash
   git push origin feature/<your-feature-name>
   ```

6. **Open a pull request** from your branch to the main repository.

## License

This project is licensed under the [MIT License](https://mit-license.org/Safaricom-Ethiopia-PLC). See the [LICENSE](LICENSE) file for more details.

---

_Happy coding with M-Pesa Go SDK!_
