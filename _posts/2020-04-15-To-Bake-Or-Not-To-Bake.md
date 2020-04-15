---
layout: post
title: To Bake Or Not To Bake
---

Weird times, huh? I bet you had your fair share of posts about the COVID-19 already, so let me shine some light on you with some old-school nerd approach to solving problems you should never have in real life!

## So I've started baking bread

Actually, it started with pizza - most of the folks at my job already knows about my obsession about a proper slice, but my second, yeast-and-flour-infused love is bread - which is not really surprising, as I'm Polish. The biggest issue I had in UK is finding the proper loaf - the one which has the best crust-to-density ratio and dries out within a day or two and doesn't transform into a sponge and stays alive for years.

## But, you need flour

To bake bread. Nowadays, it's not that easy to get your hands on a proper flour, but I was lucky enough to see the black clouds coming, so I've stacked up a few kgs of the good, white stuff. But I've used most of it, so I wanted to order some online. I've narrowed down the game to two main mills - [Shipton](https://www.shipton-mill.com) and [Matthews](https://fwpmatthews.co.uk).
Both have introduced limitations to opening hours and how many orders they're picking up - and being the nerd I'm, I don't want to manually check the pages. So what can we do?

# LET'S AUTOMATE IT

What we need to do?

* Check the page for the stuff I need
* Save the page state
* Check if it has changed
* Notify me if it's changed

Let's dive down.

### 1. Check the page

That's easy - a simple `GET` and check the body. I've used a really, really nice lib called `goquery` here, which allows me to check the body according to the class/id which, I presume, has the info I need:

```go
	doc, err := goquery.NewDocumentFromReader(res.Body)
	if err != nil {
		log.Fatal(err)
	}

	var result string

	if mill == SHIPTON {
		doc.Find(".well").Each(func(i int, s *goquery.Selection) {
			result = s.Find("p").Text()
		})
	}
	if mill == MATTHEWS {
		doc.Find(".storeclosing_popup").Each(func(i int, s *goquery.Selection) {
			result = s.Find("div").Text()
		})
	}
	return result
}
```

If you check the code, I can do a lot more with my selectors, but for now, I've identified what I need, and just rolled with it. The trick here was to check what it being changed on the page and what indicates if the shop's open.

### 2. Save the page state

```go
func updateMill(mill string) {
	sel := getMill(mill)

	if mill == SHIPTON {
		_ = ioutil.WriteFile("shiptonmill.txt", []byte(sel), 0644)
		log.Printf("updated shipton mill file\n")
	}
	if mill == MATTHEWS {
		_ = ioutil.WriteFile("matthewsmill.txt", []byte(sel), 0644)
		log.Printf("updated matthews mill file\n")
	}
}
```

Quick and dirty. All that I need in these days!

### 3. Check if it has changed

Again, nothing spectacular:

```go
func checkMill(mill string) {
	sel := getMill(mill)

	if mill == SHIPTON {
		b, _ := ioutil.ReadFile("shiptonmill.txt")

		state := string(b)

		log.Printf("SHIPTON MILL\n")
		if sel != state {
			log.Printf("The page has changed!\n")
			err := notify(SHIPTON)
			if err != nil {
				log.Fatal(err)
			}
			log.Fatalf("This is a hard exit to not overflood me with notifications\n")
		} else {
			log.Printf("Still the same\n")
    }
    ...
```

Just getting the page, checking my reference file and notifying me if something changed.

### 4. Notify if changed

And that's the part I'm really proud of. It's not much code, but it's nice and cohesive and, well, doesn't crumble.

```go
func notify(mill, state, content string) error {
	from := os.Getenv("NOTIFICATION_EMAIL_SEND")
	password := os.Getenv("NOTIFICATION_EMAIL_SEND_PASSWORD")

	receiver := []string{os.Getenv("NOTIFICATION_EMAIL_RECEIVER")}

	smtpServer := smtpServer{host: "smtp.gmail.com", port: "587"}

	var message []byte

	if mill == SHIPTON {
		message = []byte(fmt.Sprintf("shipton mill has changed something!\n\npreviously: %v\nnow: %v\n", state, content))
	}

	if mill == MATTHEWS {
		message = []byte(fmt.Sprintf("matthews mill has changed something!\n\n%vpreviously: %v\nnow: %v\n", state, content))
	}

	auth := smtp.PlainAuth("", from, password, smtpServer.host)

	return smtp.SendMail(smtpServer.Address(), auth, from, receiver, message)
}
```

So, what's going on here? I'm getting my `NOTIFICATION_EMAIL` send address and password and the `RECEIVER`. Host is pre-set (might be abstracted to environmental variables as well maybe...); I'm parsing my `mill` input to get the host only (just for having nicer emails) and then I'm just sending the email with both the memorized state and the current state of the site. Easy-peasy!

Actually, a lot of this is in-built and ready for you in Go, which makes this even more fun!

## Have you bought the flour yet?

Well, no. For the first few days, I actually forgot to run it...

Will this code change the world? Probably not.

Will this code change my baking game? Probably yes!

## Can I use that?

Of course you can - go to my github [HERE](https://github.com/youshy/get-me-my-flour). All input and ideas and stars are more than welcome!

Hope to see you all when the world goes back to having bread flour in every shop! Meanwhile, stay safe, stay strong, [stay the fuck home!](https://staythefuckhome.com/)
