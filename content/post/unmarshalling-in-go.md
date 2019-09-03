---
date: "2019-08-29T19:48:09+01:00"
draft: false
title: "Unmarshalling to structs in Go - no brainer, eh?"
tags: ["go", "golang", "json", "yaml", "toml", "unmarshalling", "yakshaving"]
---

Those days I ran into the same trap the second time. So I decided to have a short writeup for future me and other Go beginners. The trap was that I wrote nice structs like 

```json
{
    "key1": "value1",
    "key2": "value2"
}
```

And wanted them to be unmarshalled them into structs like this:

```go
type foo struct {
    key1 string
    key2 string
}
```

Doing this regularly with Jackson in Java I named the attributes like the keys in the JSON file. I somewhat expected that it should be a no brainer for any marshalling tool in Go to perform the mapping into the struct without further mapping metadata.

## Visibility and access in Go

I was terribly wrong. ;-D No matter how often I checked my struct and JSON payload for typos and restarted the code/tests there were just no values in the unmarshalled struct! FFS! 

So, why did this not work? If you have a look how visibility modifiers in Go work it's kind of obvious. Only identifiers that start with a capital letter are visible to other packages. And obviously the (un-)marshalling code doesn't reside in the same package as your application code.

Making identifiers public in Go is done via upper-casing the fist letter of the identifier. Consequently I renamed the identifiers in my struct:

```go
type foo struct {
    Key1 string
    Key2 string
}
```

## Visible but still not mappable
But still I did not see any values in my unmarshalled struct. It was kind of obvious why this did not work: unmarshalling is case-sensitive with respect to identifiers. So the JSON should have looked like this:


```json
{
    "Key1": "value1",
    "Key2": "value2"
}
```

This would have been suboptimal for two reasons: I would have modeled the data format after implementation details of the used programming language and mapping technology and personally I don't like non-lowercase attribute names in JSON.


## Tags to the rescue!

So, the general problem is that at the one hand we need to care about visibility of the structs attributes and at the same time need to help the (un-)marshalling tools to map arbitrary attributes of Go structs to their counterparts in the target format (JSON, TOML, YAML, ...). In Java the natural way to help here is annotations. In Go however, there are no annotations (luckily!). The Go language offers a mechanism called 'tags'. Tags are textual hints or metadata in backticks (`) placed behind behind attributes of a struct.

Tools using Go's reflection to access values from structs can use the tags to fine-tune for example the mapping algorithms. So in our case we are mapping JSON and have attributes with names being different from the struct's attribute names. Here is the resulting Go struct:

```go
type foo struct {
    Key1 string `json:"key1"`
    Key2 string `json:"key2"`
}
```

If you are using other target formats you need a different prefix. Most probably something like 'toml' or 'yaml'. Since the prefix depends on the implementation of the used library you'll need to lookup the supported tag format in the documentation of the library in question.

