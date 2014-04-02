# gonexmo [![GoDoc](https://godoc.org/github.com/njern/gonexmo?status.png)](https://godoc.org/github.com/njern/gonexmo)

This is my fork of gonexmo that works with Appengine. This is a "scratch-my-own-itch" project and is not by any means complete or supported. Currently only SMS is supported.

gonexmo is a [Go](http://golang.org/) library tailored for sending SMS's with 
[Nexmo](https://www.nexmo.com/).


## Installation

Assuming you have a working Go environment, installation is simple:

    go get github.com/njern/gonexmo

You can take a look at the documentation locally with:

	godoc github.com/njern/gonexmo

The included tests in `gonexmo_test.go` also illustrate usage of the package.

**Note:** You must enter valid API credentials and a valid phone number in 
`gonexmo_test.go` or the tests will fail! I didn't feel like draining my own 
Nexmo account or receiving thousands of test SMS's - sorry :)


## Usage
    import "github.com/njern/gonexmo"
    
    nexmo, _ := nexmo.NexmoWithKeyAndSecret("API_KEY_GOES_HERE", "API_SECRET_GOES_HERE")
    
    // Test if it works by retrieving your account balance
    balance, err := Nexmo.Account.GetBalance()
    
    // Send an SMS
    // See https://docs.nexmo.com/index.php/sms-api/send-message for details.
	message := &SMSMessage{
		From:           "go-nexmo", 	
        To:              "00358123412345",
		Type:            Text,
		Text:            "Gonexmo test SMS message, sent at " + time.Now().String(),
		ClientReference: "gonexmo-test " + strconv.FormatInt(time.Now().Unix(), 10),
		Class:           Standard,
	}

	messageResponse, err := nexmo.SMS.Send(message)

## Receiving inbound messages

    import (
        "github.com/njern/gonexmo"
        "log"
        "net/http"
    )

    func main() {
        messages := make(chan *nexmo.RecvdMessage)
        h := nexmo.NewMessageHandler(messages)

        go func() {
            for {
                msg := <-messages
                log.Printf("%v\n",msg)
            }
        }()

        // Set your Nexmo callback url to http://<domain or ip>:8080/get/
        http.HandleFunc("/get/", h)
        if err := http.ListenAndServe(":8080", nil); err != nil {
            log.Fatal("ListenAndServe: ", err)
        }

    }


## Future plans

* Implement the rest of the Nexmo API
* Release a few more Go libraries - once I clean them up :)

## How can you help?

* Let me know if you're using gonexmo by dropping me a line at 
  [github user name] at walkbase.com
* Let me know about any bugs / annoyances the same way
