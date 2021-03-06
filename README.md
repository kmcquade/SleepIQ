SleepIQ is a Go package for accessing the SleepIQ API for SleepNumber beds.

# Installation
```
go get -u github.com/danpenn/SleepIQ
```

# Usage
Here is a simple example to get you started. This example retrieves information about all beds.

	// Create a new instance of SleepIQ
	siq := sleepiq.New()

	// Login
	_, err := sleepiq.Login("email@live.com", "password")
	if err != nil {
		fmt.Println("login failed - ", err)
		return
	}

	// Get information about all the beds
	beds, err := siq.Beds()
	if err != nil {
		fmt.Println("could not get beds - ", err)
		return
	}

	// Display some bed information
	for _, bed := range beds.Beds {
		fmt.Printf("%s (%s)\n", bed.Size, bed.Name)
	}

This example sets the bed position to "WatchTV"

	// Create a new instance of SleepIQ
	siq := sleepiq.New()

	// Login
	_, err := siq.Login("email@live.com", "password")
	if err != nil {
		fmt.Println("login failed - ", err)
		return
	}

	// Get the beds so we can retrieve the bedID
	beds, err := siq.Beds()
	if err != nil {
		fmt.Println("could not get beds - ", err)
		return
	}

	// Set the right side of the bed to the 'WatchTV' preset position
	bedStatus, err := siq.ControlBedPosition(beds.Beds[0].BedID, "Right", sleepiq.WatchTV)
	if err != nil {
		fmt.Println("could not set bed position - ", err)
		return
	}

	// Display a confirmation showing the new position
	fmt.Printf("Position: %s", bedStatus.CurrentPositionPreset)

This example accesses data from the "Insights" service. This is a separate service and therefore requires a different login. It uses the same credentials as the SleepIQ service. I could have combined both logins into one method call but I decided that some people may only want to access one or the other and may not want the overhead of calling both APIs to authenticate.

	// Create a new instance of SleepIQ
	siq := sleepiq.New()

	// Login in the Insights service - Note that this is a separate service
	_, err := siq.Login("email@live.com", "password")
	if err != nil {
		fmt.Println("login failed - ", err)
		return
	}

	// Get insights for people like me
	now := time.Now()
	likeMe, err := siq.InsightsLikeMe(response.SleeperID, now.AddDate(-2, 0, 0), now)
	if err != nil {
		fmt.Println("could not get Insights Like Me - ", err)
		return
	}

	// Display data for each item
	for _, item := range likeMe.Data {
		fmt.Printf("Date: %s, SleepIQ Score: %d\n", item.Date, item.SiqScore)
	}

# Disclaimer
While I have taken caution in developing this code, consumption of it is at your own risk. Usage of this package is of your own volition and I take no resposiblity for potential damage caused to your bed.

# Development Notes
SleepNumber has not published formal documentation for their SleepIQ API. All development here is based from observations made using Chrome developer tools, Postman and Charles Web Debugging Proxy to sniff HTTP traffic made from an iPhone and desktop browser.  There are many APIs that are included here that I have no idea what the inforation is actually used for (i.e., BedNodes).

All development and Testing is based upon my SleepNumber 360 I8 King Smart Bed with FlexFit 3, foot warmer and underbed lighting.

# Contributions
Contributions to this project are welcome. Please ensure that all tests are passing and that the code complies with all 'golint' recommendations.

# Testing
The tests utilize some environment settings for certain values. Please ensure that these environment variables are set prior to running the tests.

```
sleepiq_username = SleepIQ API username
sleepiq_password = SleepIQ API password
sleepiq_namesearch = Name of a sleeper (person) to retrieve sleep activity for
```
