import requests
from bs4 import BeautifulSoup
import pandas as pd

# The URL of the e-commerce page you want to scrape
URL = "https://www.example.com/products"  # Replace with the actual URL
HEADERS = {
    "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/91.0.4472.124 Safari/537.36"
}

def get_html(url):
    """Fetches the HTML content of a page."""
    response = requests.get(url, headers=HEADERS)
    if response.status_code == 200:
        return response.text
    else:
        print("Failed to retrieve the page.")
        return None

def parse_product_page(html):
    """Extract product information (name, price, rating) from HTML."""
    soup = BeautifulSoup(html, "html.parser")
    
    products = []
    
    # Find all the product containers (Adjust based on website structure)
    product_elements = soup.find_all("div", class_="product-item")  # Example class, modify for the actual page structure
    
    for product in product_elements:
        name = product.find("h2", class_="product-name")  # Example, adjust for the actual tag/class
        price = product.find("span", class_="price")  # Example, adjust for the actual tag/class
        rating = product.find("span", class_="rating")  # Example, adjust for the actual tag/class

        # Extract text and clean up if necessary
        if name and price and rating:
            name = name.get_text(strip=True)
            price = price.get_text(strip=True)
            rating = rating.get_text(strip=True)
            products.append([name, price, rating])

    return products

def save_to_csv(data, filename="products.csv"):
    """Save the product data to a CSV file."""
    df = pd.DataFrame(data, columns=["Product Name", "Price", "Rating"])
    df.to_csv(filename, index=False, encoding="utf-8")
    print(f"Data saved to {filename}")

def main():
    # Step 1: Get the HTML content of the page
    html = get_html(URL)
    
    if html:
        # Step 2: Parse the page and extract product information
        products = parse_product_page(html)
        
        # Step 3: Save the data to a CSV file
        if products:
            save_to_csv(products)
        else:
            print("No products found.")
    else:
        print("Failed to retrieve the page.")

if __name__ == "__main__":
    main()
