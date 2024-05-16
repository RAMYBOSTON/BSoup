# BSoup
This Repo is a learning one to show step by step how to use Beautifull Soup in Python.

This code first defines some sample HTML content. Then, it uses BeautifulSoup to parse the HTML and creates a tree-like structure in memory.

Next, the code demonstrates various ways to extract data:

Using find('h1'): This finds the first occurrence of the <h1> tag and extracts its text content.
Using find_all('li'): This finds all occurrences of the <li> tag and iterates through them to extract the text content of each list item.
Finally, the code prints the extracted data in a formatted way.


## This code defines a function summarize_website that takes a URL as input. It uses the requests library to fetch the website content and then parses it with Beautiful Soup.

# Key modifications for user input and summary generation:

> The code prompts the user for a website URL and allows them to quit by entering 'q'.
> It performs basic URL validation (appends "http" if not present).
> The summarize_website function analyzes the website content using various techniques:
> Extracts website title from the <title> tag.
> Analyzes headings (h1, h2, h3) for clues about services/products.
> Looks for the meta description tag for a website description.
> Performs basic content analysis by looking for keywords in the body text.
> It filters and limits the extracted points to create a concise summary of 5-10 points describing the website and its services/products.


## Note: This is a basic implementation that might not work perfectly for all websites. More advanced techniques can be used for a more comprehensive analysis.


## Requirement packages to run the app:
BeautifulSoup
requests

## Recommendations:
Create a new Environment specifically for Beautifulsoup as this is a line of coding/apps that uses pacakages which might not be used for other types of apps.

# --> Below is the working code:

from bs4 import BeautifulSoup
import requests

def summarize_website(url):
  """
  This function takes a website URL as input, fetches the HTML content,
  parses it using Beautiful Soup, and returns a brief summary describing 
  the website and its services/products in 5-10 points.
  """
  try:
    # Fetch the website content
    response = requests.get(url)
    response.raise_for_status()  # Raise exception for bad status codes

    # Parse the HTML content
    soup = BeautifulSoup(response.content, 'html.parser')

    # Analyze the website content for summary points
    summary_points = []
    
    # 1. Title: Extract website title from the title tag
    title_tag = soup.find('title')
    if title_tag:
      summary_points.append(f"Title: {title_tag.text.strip()}")

    # 2. Headings: Analyze headings (h1, h2, h3) for clues about services/products
    for heading in soup.find_all(['h1', 'h2', 'h3']):
      summary_points.append(heading.text.strip())

    # 3. Meta Description: Look for meta description tag for website description
    meta_description = soup.find('meta', attrs={'name': 'description'})
    if meta_description:
      summary_points.append(meta_description['content'].strip())

    # 4. Content Analysis (basic): Look for keywords in body content
    body_text = soup.get_text(separator='\n')  # Combine text from all elements
    keywords = ['service', 'product', 'solution', 'sell', 'offer']
    for keyword in keywords:
      if keyword in body_text.lower():
        summary_points.append(f"Offers {keyword.capitalize()}s")

    # 5. Filter and limit summary points to 5-10
    summary_points = list(set(summary_points))[:10]  # Remove duplicates, keep max 10 points

    # Create a summary paragraph
    summary_text = "\n".join(summary_points)
    return f"Summary of {url}:\n{summary_text}"

  except requests.exceptions.RequestException as e:
    return f"Error: Failed to access website ({url}): {e}"

# Get user input for website URL
while True:
  url = input("Enter a website URL (or 'q' to quit): ")
  if url.lower() == 'q':
    break
  # Validate URL format (basic check)
  if not url.startswith("http"):
    url = f"http://{url}"

  # Summarize the website and print the results
  summary = summarize_website(url)
  print(summary)
  print("\n")
