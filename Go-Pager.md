# Go Pager

Examples of Go packages that implement console pagers, with or without syntax highlight, with or without using `less`.

## Moar

This one does not rely on `less` and it has syntax highlighting.

```go
	buf := new(bytes.Buffer)

	options := m.ReaderOptions{
        ShouldFormat: true,
        Style: styles.Get("native"),
    }

    fmt.Printf(buf, `{"message": "Hello, %s!!"}`, "World")

	reader, err := m.NewReaderFromStream(
		fmt.Sprintf("Block: %d", block.Number),
		buf,
		formatters.TTY,
		options,
	)
	if err != nil {
		fmt.Fatalf("%v\n", err)
	}

	pager := m.NewPager(reader)
	pager.WrapLongLines = true

	err = pager.Page()
	if err != nil {
		fmt.Fatalf("%v\n", err)
	}
```