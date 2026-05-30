# Getting Web Data Without Writing a Scraper

A lot of useful data lives on websites with no download button and no official API. This notebook covers three ways to get it into a DataFrame anyway, without building a full scraper from scratch. Each method is suited to a different situation, and together they cover most of what you'll run into as a journalist or researcher working with web data.

The examples use real sources: Serbian minimum wage statistics, Europol press releases, and FIFA world rankings.

---

## What's in the notebook

### 1. Reading a table directly from a page using pandas

If the data you need is already in an HTML `<table>` on the page, you often don't need to scrape anything at all. `pd.read_html()` fetches the page and pulls out every table it finds, returning them as a list of DataFrames. You just pick the one you want.

The example here grabs the minimum wage history table from a Serbian legal statistics site and exports it to Excel in a few lines. This is the fastest method when it works, and it's worth trying first before reaching for anything more complex.

---

### 2. Downloading raw HTML and parsing it locally

Some websites block automated requests, or they rely on JavaScript to render content, which means `pd.read_html()` and standard request-based approaches won't work. The workaround is low-tech: save the page manually from your browser using "Save As", then load that file locally in Python.

The example here processes a folder of saved HTML files from Europol's newsroom. The pages embed their data as a JSON object inside a `<script>` tag (a pattern called `window.SERVER_DATA`), so the notebook uses `re.search()` to extract that JSON block and then parses it normally. The result is a clean DataFrame of press release titles, dates, and URLs exported to Excel.

This approach is slower since you're saving pages by hand, but it works on sites that would otherwise be impossible to automate, and it's perfectly fine for one-off data collection.

---

### 3. Pulling data through network requests

When you load a page in your browser, a lot is happening in the background. Your browser makes dozens of requests to fetch content, and some of those requests go to internal APIs that return clean JSON directly, even if the site has no public API or download option.

You can find these by opening DevTools in your browser, going to the Network tab, and watching what requests fire as the page loads. The example here does exactly that with the FIFA world rankings page: the site loads ranking data from an internal API endpoint that returns structured JSON with rank, points, confederation, and movement for every national team.

To replicate a request like this in Python, copy it from DevTools as cURL and paste it into [curlconverter.com](https://curlconverter.com/python/) to get a working Python snippet with all the headers and parameters already filled in. The notebook then parses the JSON response into a DataFrame and exports it to Excel.

Worth noting: this doesn't work everywhere. Some APIs use authentication tokens that expire quickly, and some sites actively block non-browser requests. But when it does work, it's often the cleanest and fastest way to get structured data.

---

## What this is useful for

- Extracting tables from any website that uses standard HTML tables
- Getting data from JavaScript-heavy or bot-protected sites by saving pages manually
- Tapping into undocumented internal APIs that return JSON
- Getting structured data out of pages that have no official export or API

---

## Requirements

```
pandas
requests
beautifulsoup4
```

`json`, `re`, `datetime`, and `pathlib` are part of the Python standard library.

---

Made by [Teodora Curcic](https://www.linkedin.com/in/teodora-curcic-27a93884/) for [Dataharvest, The European Investigative Journalism Conference](https://dataharvest.eu/)
