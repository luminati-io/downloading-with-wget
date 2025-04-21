# Downloading Web pages and Files with Wget and Python

[![Bright Data Promo](https://github.com/luminati-io/LinkedIn-Scraper/raw/main/Proxies%20and%20scrapers%20GitHub%20bonus%20banner.png)](https://brightdata.com/)

This guide explores wget, a robust command-line utility for retrieving files through HTTP, HTTPS, and FTP protocols, highlighting its advantages over Python's requests library.

- [Understanding Wget](#what-is-wget)
- [Advantages of Wget Over Python's requests Package](#advantages-of-wget-over-pythons-requests-package)
- [Executing Command-Line Instructions in Python](#executing-command-line-instructions-in-python)
- [Practical Wget Applications with Python](#practical-wget-applications-with-python)
  - [Retrieving a Single File](#retrieving-a-single-file)
  - [Capturing a Web Page](#capturing-a-web-page)
  - [Conditional File Downloads Based on Modifications](#conditional-file-downloads-based-on-modifications)
  - [Resuming Broken Downloads](#resuming-broken-downloads)
  - [Mirroring an Entire Website](#mirroring-an-entire-website)
- [Benefits and Limitations of Integrating Wget with Python](#benefits-and-limitations-of-integrating-wget-with-python)
- [Enhancing Wget with Proxy Servers](#enhancing-wget-with-proxy-servers)
- [Summary](#summary)

## What Is Wget?

[`wget`](https://www.gnu.org/software/wget/) serves as a versatile command-line tool designed for downloading content from the internet using HTTP, HTTPS, FTP, FTPS, and various other protocols. It comes pre-installed on most Unix-based systems and is also available for Windows environments.

## Advantages of Wget Over Python's requests Package

There are compelling reasons to incorporate `wgen` it into Python projects instead of relying on libraries like [requests](/faqs/python-requests/what-is-requests-used-for):

- Broader protocol support compared to requests
- Ability to continue downloads after interruptions
- Options to limit bandwidth consumption
- Support for wildcard patterns in filenames and network paths
- Multilingual messaging through NLS
- Capability to transform absolute URLs to relative links in retrieved documents
- Integration with HTTP/S proxies
- Maintenance of persistent HTTP connections
- Support for background/unattended download operations
- Smart re-download decisions based on file timestamps during mirroring
- Recursive downloading of linked resources to specified depth levels
- Built-in compliance with robots.txt directives (learn more in our [robots.txt web scraping guide](/blog/how-tos/robots-txt-for-web-scraping-guide))

These features represent just a portion of what makes `wget` exceptionally powerful compared to standard Python HTTP libraries. A particularly noteworthy feature is `wget`'s ability to navigate through HTML pages, following and downloading referenced files. This functionality makes it particularly suitable for web crawling operations.

Let's explore implementing `wget` in Python.

## Executing Command-Line Instructions in Python

Follow this process to create a Python script capable of executing `wget` commands.

### Setup Requirements

Before proceeding, ensure `wget` is properly installed on your system. Installation varies by operating system:

- Linux systems typically include wget by default; if not, install through your distribution's package manager
- Mac users can [install `wget` using Homebrew](https://formulae.brew.sh/formula/wget)
- Windows users should download the [`Wget` Windows binary](https://gnuwin32.sourceforge.net/packages/wget.htm), place it in a directory, then add this location (e.g., C:\\Program Files (x86)\\Wget) to your [system PATH variable](https://www.architectryan.com/2018/03/17/add-to-the-path-on-windows-10/)

You'll also need Python 3+ installed. Obtain it by [downloading the installer](https://www.python.org/downloads/) and completing the installation process.

A development environment such as [PyCharm Community Edition](https://www.jetbrains.com/pycharm/download/) or [Visual Studio Code with Python extensions](https://code.visualstudio.com/docs/languages/python) will enhance your coding experience.

### Creating Your Python Environment

Establish a new Python project with a dedicated [virtual environment](https://docs.python.org/3/library/venv.html):

```sh
mkdir wget-python-demo

cd wget-python-demo

python -m venv env
```

The `wget-python-demo` folder will serve as your project directory.

Open this location in your IDE, create a `script.py` file with this initial content:

```python
print('Hello, World!')
```

This simple script currently outputs "Hello, World!" to the console, but we'll soon expand it with `wget` functionality.

Test your script by clicking the run button in your IDE or by entering:

```sh
python script.py
```

You should see this output:

```
Hello, World!
```

Now let's implement `wget` integration.

**Creating a Function for CLI Command Execution**

The most straightforward approach to running terminal commands from Python involves the [`subprocess`](https://docs.python.org/3/library/subprocess.html) module. This standard library component allows you to launch new processes, connect to their input/output/error streams, and retrieve their exit codes—providing everything needed to execute terminal commands from within Python.

Here's how to leverage the [`Popen()`](https://docs.python.org/3/library/subprocess.html#subprocess.Popen) method from subprocess to run `wget` commands in Python:

```python
import subprocess

def execute_command(command):
    """
    Execute a CLI command and return the output and error messages.
    
    Parameters:
    - command (str): The CLI command to execute.
    
    Returns:
    - output (str): The output generated by the command.
    - error (str): The error message generated by the command, if any.
    """
    try:
        # execute the command and capture the output and error messages
        process = subprocess.Popen(command, shell=True, stdout=subprocess.PIPE, stderr=subprocess.PIPE)
        output, error = process.communicate()
        
        output = output.decode("utf-8")
        error = error.decode("utf-8")
        
        # return the output and error messages
        return output, error
    except Exception as e:
        # if an exception occurs, return the exception message as an error
        return None, str(e)
```

`Popen()` launches your specified command as a new process in your operating system. The `shell=True` parameter ensures the method utilizes your system's default shell environment.

Add this function to your script.py file. You can now execute any command-line instruction in Python like this:

```python
output, error = execute_command("<CLI command string>")

if error:
    print("An error occurred while running the CLI command:", error)
else:
    print("CLI command output:", output)
```

## Practical Wget Applications with Python

The standard `wget` command structure is:

```sh
wget [options] [url]
```

Where:
- `[options]` represents various flags and parameters that modify `wget`'s behavior
- `[url]` specifies the location of the content you want to download, which can be a direct file link or a webpage containing multiple resources

> **Note**:
>
> Windows users should use `wget.exe` instead of `wget`.

Let's explore several practical scenarios using `wget` through Python.

### Retrieving a Single File

To download http://lumtest.com/myip.json using `wget`, the basic command would be:

```sh
wget http://lumtest.com/myip.json
```

In Python, this becomes:

```python
output, error = execute_command("wget http://lumtest.com/myip.json")
```

Printing the output would display something like:

```
--2024-04-18 15:20:59-- http://lumtest.com/myip.json
Resolving lumtest.com (lumtest.com)... 3.94.72.89, 3.94.40.55
Connecting to lumtest.com (lumtest.com)|3.94.72.89|:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 266 [application/json]
Saving to: 'myip.json.1'

myip.json.1 100%[=================================================>] 266 --.-KB/s in 0s

2024-04-18 15:20:59 (5.41 MB/s) - 'myip.json.1' saved [266/266]
```

This output reveals that:
1. The URL resolves to the server's IP address
2. A connection is established with an HTTP request to the specified resource
3. The server responds with a [200 OK status code](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status)
4. The file downloads and saves to your current directory

Your project folder will now contain the downloaded myip.json file.

To specify a different destination folder, use the `--directory-prefix` or `-P` flag:

```python
output, error = execute_command("wget --directory-prefix=./download http://lumtest.com/myip.json")
```

This saves the file to a "download" subfolder within your project directory. If this folder doesn't exist, `wget` automatically creates it.

To rename the downloaded file, use the `--output-document` or `-O` flag:

```python
output, error = execute_command("wget --output-document=custom-name.json http://lumtest.com/myip.json")
```

This creates a file named custom-name.json instead of using the original filename.

### Capturing a Web Page

The command structure remains identical, but the URL points to a webpage instead:

```python
output, error = execute_command("wget https://brightdata.com/")
```

This downloads an index.html file containing the HTML content from the brightdata.com homepage.

### Conditional File Downloads Based on Modifications

To conserve bandwidth and storage, you might prefer downloading files only when they've changed since your last download. `wget` provides [file timestamping capabilities](https://www.gnu.org/software/wget/manual/html_node/Time_002dStamping.html) for this purpose.

The `--timestamping` option instructs `wget` to compare timestamps between local and server files. If your local file has an identical or newer timestamp than the server version, `wget` skips the download.

The timestamping process works as follows:

1. When using the `--timestamping` or `-N` option, `wget` retrieves the remote file's timestamp
2. It compares this with the local file's timestamp (if present)
3. Downloads occur only when the local file is missing or has an older timestamp than the server version

For HTTP resources, timestamping checks the [Last-Modified](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Last-Modified) header returned after a HEAD request. `wget` also examines the [Content-Length](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Length) header to compare file sizes—if these differ, the file downloads regardless of timestamp information. Note that Last-Modified is optional; without it, `wget` downloads the file automatically.

Implement timestamping in Python like this:

```python
output, error = execute_command("wget --timestamping https://brightdata.com")
```

If you've previously downloaded index.html, you'll see a message like:

```
--2024-04-18 15:55:06-- https://brightdata.com
Resolving brightdata.com (brightdata.com)... 104.18.25.60, 104.18.24.60
Connecting to brightdata.com (brightdata.com)|104.18.25.60|:443... connected.
HTTP request sent, awaiting response... 304 Not Modified
File 'index.html' not modified on server. Omitting download.
```

This same mechanism works with [FTP downloads](https://www.gnu.org/software/wget/manual/html_node/FTP-Time_002dStamping-Internals.html) as well.

### Resuming Broken Downloads

By default, `wget` automatically attempts up to 20 retries if a connection fails during download. To manually resume a partially completed download, use the `--continue` or `-c` option:

```python
output, error = execute_command("wget --continue http://lumtest.com/myip.json")
```

### Mirroring an Entire Website

One of `wget`'s most powerful features is recursive downloading, enabling you to capture an entire website with a single command.

Starting from your specified URL, `wget` parses the HTML and follows links found in `src` and `href` attributes or CSS `url()` values. When these referenced files are HTML/text documents, `wget` continues parsing and following their links until reaching your desired depth limit. This recursive process follows a [breadth-first search pattern](https://www.geeksforgeeks.org/breadth-first-search-or-bfs-for-a-graph/), downloading depth 1 resources before proceeding to depth 2, and so on.

Key options for recursive downloading include:

- `--recursive` or `-r`: Enables recursive downloading of linked resources including images, stylesheets, scripts, etc. Files are organized in a folder named after the target domain.
- `--level=<depth>` or `-l=<depth>`: Defines the maximum recursion depth for following links. For example, `--level=1` only downloads pages directly linked from your starting URL. The default limit is 5 to prevent excessive crawling. Use 0 or 'inf' for unlimited depth. To ensure all display-critical resources are downloaded regardless of depth, add the `-p` or `--page-requisites` option.
- `--convert-links` or `-k`: Adjusts links within downloaded HTML files to reference your local copies instead of original URLs. This enables offline browsing of the downloaded content.

To recursively download the Bright Data website with a depth limit of 1 and convert all links to local references:

```python
output, error = execute_command("wget --recursive --level=1 --convert-links https://brightdata.com")
```

After completion, you'll have a brightdata.com folder containing a local copy of the site with one level of recursive depth.

## Benefits and Limitations of Integrating Wget with Python

Let's consider the advantages and disadvantages of using `wget` with Python.

**Benefits**

- Simple Python integration through the subprocess module
- Extensive feature set including recursive downloading, automatic retries, timestamping, and more
- Ability to mirror entire websites with a single command
- Built-in FTP support
- Proxy server integration
- Capability to resume interrupted downloads

**Limitations**

- Output consists of downloaded files rather than direct string variables usable within Python code
- Requires additional parsers like [Beautiful Soup](https://brightdata.com/blog/how-tos/beautiful-soup-web-scraping) to extract specific elements from downloaded HTML

## Enhancing Wget with Proxy Servers

A common challenge when using `wget` for downloads is potential request blocking. This occurs because `wget` requests are typically identified as automated traffic. To protect against such automated access, many websites implement various restrictions including geographic blocks, rate limits, and anti-scraping measures.

Incorporating a proxy server with `wget` offers an effective solution for bypassing these limitations. A proxy functions as an intermediary between your system and the internet, routing your traffic through alternative IP addresses. This helps conceal your actual IP and circumvent many website restrictions.

For detailed instructions, see our guide on [how to use a proxy with `Wget`](https://brightdata.com/blog/how-tos/wget-proxy).

## Summary

While `wget` has advantages over the Python `requests` library, you still need proxy services to overcome anti-bot measures that websites employ.

For premium proxy services trusted by Fortune 500 companies and over 20,000 customers worldwide, consider Bright Data and their extensive range of [proxy solutions](https://brightdata.com/proxy-types).