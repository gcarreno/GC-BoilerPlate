# Go `CLI` Colours

## 🎨 **Popular Color Packages for CLI in Go**

### 1. **[github.com/fatih/color](https://github.com/fatih/color)**

* **🌟 THE go-to color lib** — battle-tested and widely used.
* Easy to use and cross-platform.
* Supports automatic no-color mode if output isn't a TTY.
* Example:

  ```go
  color.Red("This is red!")
  color.New(color.FgGreen).Println("Green text here")
  ```

---

### 2. **[github.com/mgutz/ansi](https://github.com/mgutz/ansi)**

* **Style + Theme** support.
* Great for customizing output using named styles (like “info”, “warning”).
* Can disable color easily with an env var (`ANSI_COLORS_DISABLED=1`).
* Example:

  ```go
  fmt.Println(ansi.Color("hello", "red+b"))
  ```

---

### 3. **[github.com/logrusorgru/aurora](https://github.com/logrusorgru/aurora)**

* No global state; supports method chaining.
* Good for libs where you don't want to mutate global color settings.
* Example:

  ```go
  fmt.Println(aurora.Red("Red alert!"))
  ```

---

### 4. **[github.com/gookit/color](https://github.com/gookit/color)**

* Big boy features: RGB, styles, themes, rich formatting.
* Works great on Unix and Windows (thanks to VT100 support).
* Supports 256 colors and TrueColor (24-bit RGB).
* Example:

  ```go
  color.FgLightGreen.Println("Success!")
  color.Style{color.FgCyan, color.OpBold}.Println("Styled text")
  ```

---

### 5. **[github.com/aybabtme/rgbterm](https://github.com/aybabtme/rgbterm)**

* Use RGB directly, not just ANSI colors.
* Good for terminals that support full 24-bit color.
* Not super actively maintained, but still useful.
* Example:

  ```go
  fmt.Println(rgbterm.String("Fancy RGB!", 255, 100, 0, 0, 0, 0))
  ```

---

### 6. **[github.com/mitchellh/colorstring](https://github.com/mitchellh/colorstring)**

* Compact format with inline tags like `[red]ERROR[reset]`.
* Simpler DSL for defining strings.
* Example:

  ```go
  colorstring.Print("[green]✓ Success![reset]\n")
  ```

---

### 7. **[github.com/charmbracelet/lipgloss](https://github.com/charmbracelet/lipgloss)**

* Used in **Bubble Tea**, the TUI framework.
* CSS-like approach to terminal styles. Very *aesthetic*.
* Good if you're doing fancy UI things.
* Example:

  ```go
  style := lipgloss.NewStyle().Foreground(lipgloss.Color("205")).Bold(true)
  fmt.Println(style.Render("Stylish!"))
  ```

---

### 8. **[github.com/muesli/termenv](https://github.com/muesli/termenv)**

* Detects terminal capabilities: ANSI, 256-color, TrueColor, etc.
* Very precise. Perfect for when you want to look good on all terminals.
* Low-level compared to `lipgloss`, but pairs nicely with it.
* Example:

  ```go
  p := termenv.ColorProfile()
  fmt.Println(termenv.String("Looks good").Foreground(p.Color("#FF00FF")))
  ```

---

## 👑 TL;DR — "What should I use?"

| Use Case                    | Recommendation                         |
| --------------------------- | -------------------------------------- |
| Simple CLI color            | `fatih/color` or `aurora`              |
| Full control (RGB, themes)  | `gookit/color`                         |
| Fancy UI output             | `lipgloss` + `termenv`                 |
| Inline tagged color strings | `colorstring`                          |
| RGB and low-level           | `rgbterm` or `termenv`                 |
| Prettify logs               | Combine with `logrus`, `zerolog`, etc. |

---
