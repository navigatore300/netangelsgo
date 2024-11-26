# netangeslgo

This is partial implementation of netangels.ru dns provider's API. Any contribution is welcome in the 
form of PR's. Further documentation of the API can be found [here](https://api.netangels.ru/modules/gateway_api.api.dns.records/)).

## Usage 
Add this repository as go dependency.

Create project
```bash
mkdir my-go-project
cd my-go-project
go mod init my-project
```
Create `main.go` file.
```go
package main

import (
	"fmt"
	"log"

	"github.com/navigatore300/netangelsgo"
)

func main() {
	//Create client
	client := netangelsgo.CreateNetangelsClient("__NameClient__", "__API_Key__")

	//Get Token for access (valid in 24h)
	if err := client.GetToken(); err != nil {
		log.Fatal("Error:", err)
	}
	
	var recordid int
	var err error
	//Create TXT record in DNS
	recordid, err = client.AddRecord("wh.example.com", "Hello_txt", "TXT", 400)
	if err != nil {
		log.Fatal("Error:", err)
	} else {
		fmt.Printf("Record created. ID: %d\n", recordid)
	}
	
	//Gets the record ID when the data match is complete Type=TXT Name=test.example.com Value=Hello_txt 
	recordid, err = client.GetRecordID("test.example.com", "Hello_txt", "TXT")
	if err != nil {
		log.Fatal("Error:", err)
	} else {
		fmt.Printf("Find record ID: %d\n", recordid)
	}

    //Gets the ID of the record:
	//1. When Name and Type match if Value=“” returns the ID of the record the first time the Name and Type fields match
	//2. When setting Name,Type and Value values, the record ID is returned exactly matching the passed values
	var text string
	recordid, text, err = client.GetRecord("test.example.com", "Hello_txt", "TXT")
	if err != nil {
		log.Fatal("Error:", err)
	} else {
		fmt.Printf("Find record Text: %s\n", text)
	}

	//Update record with ID.
	var changed bool
	changed, err = client.UpdateRecord(recordid, "test.example.com", "Hello_changed", "TXT", 300)
	if err != nil {
		log.Fatal("Error:", err)
	} else {
		if changed {
			fmt.Printf("Record is updated\n")
		} else {
			fmt.Printf("Record is not updated\n")
		}
	}

	recordid, text, err = client.GetRecord("test.example.com", "", "TXT")
	if err != nil {
		log.Fatal("Error:", err)
	} else {
		fmt.Printf("Find record Text: %s\n", text)
	}

	//Delete record with ID
	if err = client.RemoveRecord(recordid); err != nil {
		log.Fatal("Error:", err)
	} else {
		fmt.Printf("NetangelsClient: %d\n", recordid)
	}
    // Print data client
	fmt.Printf("NetangelsClient: %+v\n", client)
}
```
Edit go.mod
```go
module my-project

go 1.22.2

require github.com/navigatore300/netangelsgo v0.0.2
```
Run in console
```bash
go mod tidy
```
Complite!

## Implemented methods
- [x] GetToken
- [x] GetRecord
- [x] GetRecordID
- [x] AddRecord
- [x] RemoveRecord
- [x] UpdateRecord

*Основа взята с https://github.com/RunnerM/simply-com-client*
