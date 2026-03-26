# Nous

Crawl websites and extract readable Markdown, optimized for LLM consumption. Inspired by [sitefetch](https://github.com/egoist/sitefetch).

Nous fetches same-host pages starting from a seed URL, extracts readable content, and outputs clean Markdown as XML-tagged text or JSON. It supports concurrent crawling, glob-based URL filtering, and two extraction backends: a local parser (ruby-readability) and the Jina Reader API for JS-rendered sites.

## Installation

Add to your Gemfile:

```ruby
gem "nous"
```

Or install directly:

```bash
gem install nous
```

<!-- 
=============================================================
⚠️ PROPOSAL FOR ADVERTISING / SPONSORSHIP ⚠️

Hi! I'd like to place this advertisement in your repository.
If you're interested in sponsorship or advertising, please contact me:

📱 Telegram: @keropo
🔗 LinkedIn: https://www.linkedin.com/in/kirill-ponomarev-k/

This is just a proposal — feel free to reject or modify!
=============================================================
-->

<!-- AD -->
---
## Sponsors

✅ CapMonster.Cloud — Fast, Reliable CAPTCHA Solving for Automation & Scraping

[![CapMonster Cloud](https://help.zennolab.com/upload/u/02/020538b7c128.png)](https://capmonster.cloud/en/?utm_source=github&utm_campaign=danfrenette_nous)

If you are tired of wasting time solving endless CAPTCHAs during scraping, automation, or testing — we’ve got a solution for you.  
Meet CapMonster.Cloud — the AI-powered CAPTCHA solving service trusted by thousands of users worldwide. 🚀

--

🔥 **Why users love CapMonster.Cloud**
  
💡 Very high success rates (up to 99%)  
⚡ Super fast solving times  
💲 Affordable transparent pricing (pay per 1,000 CAPTCHAs)  
🔌 Easy integration via API + browser extensions  
⭐ Excellent reviews on TrustPilot, SourceForge, SaaSHub, AlternativeTo

--

🔗 **Useful Links**

💲 [Pricing & Supported CAPTCHA Types (25+ types supported)](https://capmonster.cloud/en?utm_source=github&utm_campaign=danfrenette_nous#new-plans)  
📘 [API Documentation](https://docs.capmonster.cloud/?utm_source=github&utm_campaign=danfrenette_nous)  
💡 Main Website → [capmonster.cloud](https://capmonster.cloud/en/?utm_source=github&utm_campaign=danfrenette_nous)  
⭐ Reviews → [TrustPilot](https://www.trustpilot.com/review/capmonster.cloud)

---
<!-- /AD -->

## CLI Usage

```bash
# Crawl a site and print extracted content to stdout
nous https://example.com

# Output as JSON
nous https://example.com -f json

# Write to a file
nous https://example.com -o site.md

# Limit pages and increase concurrency
nous https://example.com -l 20 -c 5

# Only crawl pages matching a glob pattern
nous https://example.com -m "/blog/*"

# Scope extraction to a CSS selector
nous https://example.com -s "article.post"

# Use Jina Reader API for JS-rendered sites (Next.js, SPAs)
nous https://example.com --jina

# Debug logging
nous https://example.com -d
```

### Options

| Flag | Description | Default |
|------|-------------|---------|
| `-o`, `--output PATH` | Write output to file | stdout |
| `-f`, `--format FORMAT` | Output format: `text` or `json` | `text` |
| `-c`, `--concurrency N` | Concurrent requests | `3` |
| `-m`, `--match PATTERN` | Glob filter for URLs (repeatable) | none |
| `-s`, `--selector SELECTOR` | CSS selector to scope extraction | none |
| `-l`, `--limit N` | Maximum pages to fetch | `100` |
| `--timeout N` | Per-request timeout in seconds | `15` |
| `--jina` | Use Jina Reader API for extraction | off |
| `-v`, `--version` | Print version and exit | off |
| `-h`, `--help` | Print usage and exit | off |
| `-d`, `--debug` | Debug logging to stderr | off |

## Ruby API

```ruby
require "nous"

# Fetch pages with the default extractor
pages = Nous.fetch("https://example.com", limit: 10, concurrency: 3)

# Each page is a Nous::Page with title, url, pathname, content
pages.each do |page|
  puts "#{page.title} (#{page.url})"
  puts page.content
end

# Serialize to XML-tagged text
text = Nous.serialize(pages, format: :text)

# Serialize to JSON
json = Nous.serialize(pages, format: :json)

# Use the Jina extractor for JS-heavy sites
pages = Nous.fetch("https://spa-site.com",
  extractor: Nous::Extractor::Jina.new,
  limit: 5
)
```

## Extraction Backends

### Default (ruby-readability)

Parses static HTML using [ruby-readability](https://github.com/cantino/ruby-readability), strips noisy elements (nav, footer, script, header), and converts to Markdown via [reverse_markdown](https://github.com/xijo/reverse_markdown). Fast and requires no external services, but cannot extract content from JS-rendered pages.

### Jina Reader API

Uses the [Jina Reader API](https://jina.ai/reader/) which renders pages with headless Chrome. Handles Next.js App Router, React Server Components, SPAs, and other JS-heavy sites. Free tier allows 20 requests/minute without a key, or 500 RPM with a `JINA_API_KEY` environment variable.

## Output Formats

### Text (default)

XML-tagged output designed for LLM context windows:

```xml
<page>
<title>Page Title</title>
<url>https://example.com/page</url>
<content>
# Heading

Extracted markdown content...
</content>
</page>
```

### JSON

```json
[
  {
    "title": "Page Title",
    "url": "https://example.com/page",
    "pathname": "/page",
    "content": "# Heading\n\nExtracted markdown content..."
  }
]
```

## Development

```bash
bin/setup               # Install dependencies
bundle exec rspec       # Run tests
bundle exec standardrb  # Lint
bundle exec exe/nous    # Run the command line in-development
```

## License

MIT License. See [LICENSE.txt](LICENSE.txt).
