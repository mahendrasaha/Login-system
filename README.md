
ecommerce website coding in python, mysql, html,css, javascript, testing

Yes. You can build a complete e-commerce website using:

Frontend: HTML, CSS, JavaScript
Backend: Python (Flask)
Database: MySQL
Testing: Pytest + browser/API testing
Tools: VS Code + Git/GitHub

A good project structure is:

ecommerce/
│
├── app.py
├── config.py
├── requirements.txt
│
├── database/
│   └── ecommerce.sql
│
├── models/
│   ├── user.py
│   ├── product.py
│   └── order.py
│
├── routes/
│   ├── auth.py
│   ├── products.py
│   ├── cart.py
│   └── orders.py
│
├── templates/
│   ├── base.html
│   ├── index.html
│   ├── login.html
│   ├── register.html
│   ├── products.html
│   ├── product_detail.html
│   ├── cart.html
│   └── checkout.html
│
├── static/
│   ├── css/
│   │   └── style.css
│   ├── js/
│   │   └── app.js
│   └── images/
│
└── tests/
    ├── test_auth.py
    ├── test_products.py
    ├── test_cart.py
    └── test_orders.py
Main features

Customer side

User registration/login/logout
Product listing
Product search
Category filtering
Product details
Add to cart
Update/remove cart items
Checkout
Order creation
Order history
User profile

Admin side

Admin login
Add products
Edit products
Delete products
Manage categories
View customers
View orders
Update order status
MySQL database

A basic database could contain:

users
products
categories
cart
cart_items
orders
order_items
payments

For example:

CREATE DATABASE ecommerce;
USE ecommerce;

CREATE TABLE users (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    email VARCHAR(150) UNIQUE NOT NULL,
    password_hash VARCHAR(255) NOT NULL,
    role ENUM('customer', 'admin') DEFAULT 'customer',
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE categories (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100) NOT NULL
);

CREATE TABLE products (
    id INT AUTO_INCREMENT PRIMARY KEY,
    category_id INT,
    name VARCHAR(200) NOT NULL,
    description TEXT,
    price DECIMAL(10,2) NOT NULL,
    stock INT DEFAULT 0,
    image VARCHAR(255),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (category_id) REFERENCES categories(id)
);
Python/Flask connection

For example:

from flask import Flask
import mysql.connector

app = Flask(__name__)

db = mysql.connector.connect(
    host="localhost",
    user="root",
    password="your_password",
    database="ecommerce"
)

@app.route("/")
def home():
    return "E-commerce website is running!"

if __name__ == "__main__":
    app.run(debug=True)
Testing

You should test each layer:

Unit Testing
    ↓
API/Backend Testing
    ↓
Database Testing
    ↓
Frontend Testing
    ↓
Integration Testing
    ↓
Security Testing

Example with pytest:

def test_home_page(client):
    response = client.get("/")
    assert response.status_code == 200

For login, test cases should include:

✓ Valid email + valid password
✓ Valid email + wrong password
✓ Invalid email
✓ Empty email
✓ Empty password
✓ Duplicate registration
✓ SQL injection attempts
✓ Unauthorized admin access
✓ Logout
Recommended development order
1. Install Python, MySQL, VS Code
          ↓
2. Create Flask project
          ↓
3. Create MySQL database
          ↓
4. Connect Flask → MySQL
          ↓
5. Create registration/login
          ↓
6. Create product management
          ↓
7. Build HTML/CSS frontend
          ↓
8. Add JavaScript
          ↓
9. Build shopping cart
          ↓
10. Build checkout/orders
          ↓
11. Build admin panel
          ↓
12. Add testing
          ↓
13. Git + GitHub
          ↓
14. Deploy the application

If you're learning this as a real portfolio project, I recommend building it incrementally rather than receiving one huge code file. We can start with Step 1: complete Flask + MySQL project setup, then build registration/login → products → cart → checkout → admin → testing with working code at each stage.

Great. Let’s build this as a real portfolio-level e-commerce project, step by step, rather than putting everything into one file.

Project roadmap

We’ll build:

Flask + MySQL setup
Database design
User registration/login
Product & category management
Product search/filter
Shopping cart
Checkout & orders
Admin dashboard
HTML/CSS/JavaScript improvements
Testing with pytest
Security improvements
Git + GitHub
Deployment
Step 1 — Create the project

Create this folder:

ecommerce/
│
├── app.py
├── config.py
├── requirements.txt
│
├── templates/
│   └── index.html
│
├── static/
│   ├── css/
│   │   └── style.css
│   └── js/
│       └── app.js
│
└── tests/
1. Create virtual environment

Open the VS Code terminal:

mkdir ecommerce
cd ecommerce

python -m venv venv

Activate it on Windows:

venv\Scripts\activate

You should see something similar to:

(venv) C:\...\ecommerce>
2. Install Python packages
pip install flask mysql-connector-python python-dotenv werkzeug pytest

Then:

pip freeze > requirements.txt
3. Create MySQL database

Open MySQL Workbench or MySQL command line:

CREATE DATABASE ecommerce;

USE ecommerce;

We'll add the tables after establishing the Flask connection.

4. Create .env

Don't put your database password directly into your Python code.

Create:

.env

Example:

DB_HOST=localhost
DB_USER=root
DB_PASSWORD=your_mysql_password
DB_NAME=ecommerce
SECRET_KEY=change_this_to_a_secure_random_key

Also create .gitignore:

venv/
.env
__pycache__/
.pytest_cache/
*.pyc

This is important when you eventually push the project to GitHub.

5. Create config.py
import os
from dotenv import load_dotenv

load_dotenv()

DB_CONFIG = {
    "host": os.getenv("DB_HOST"),
    "user": os.getenv("DB_USER"),
    "password": os.getenv("DB_PASSWORD"),
    "database": os.getenv("DB_NAME")
}

SECRET_KEY = os.getenv("SECRET_KEY")
6. Create app.py
from flask import Flask, render_template
import mysql.connector

from config import DB_CONFIG, SECRET_KEY

app = Flask(__name__)
app.config["SECRET_KEY"] = SECRET_KEY


def get_db_connection():
    return mysql.connector.connect(**DB_CONFIG)


@app.route("/")
def home():
    return render_template("index.html")


@app.route("/test-db")
def test_db():
    try:
        connection = get_db_connection()
        cursor = connection.cursor()

        cursor.execute("SELECT DATABASE();")
        database = cursor.fetchone()

        cursor.close()
        connection.close()

        return f"Database connected successfully: {database[0]}"

    except mysql.connector.Error as error:
        return f"Database connection failed: {error}"


if __name__ == "__main__":
    app.run(debug=True)
7. Create templates/index.html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">

    <title>My E-Commerce Store</title>

    <link rel="stylesheet"
          href="{{ url_for('static', filename='css/style.css') }}">
</head>

<body>

    <header>
        <h1>My E-Commerce Store</h1>

        <nav>
            <a href="/">Home</a>
            <a href="#">Products</a>
            <a href="#">Login</a>
            <a href="#">Cart</a>
        </nav>
    </header>

    <main>
        <h2>Welcome to My Store</h2>
        <p>Our e-commerce website is running successfully.</p>
    </main>

    <script src="{{ url_for('static', filename='js/app.js') }}"></script>

</body>
</html>
8. Create static/css/style.css
* {
    box-sizing: border-box;
    margin: 0;
    padding: 0;
}

body {
    font-family: Arial, sans-serif;
    background: #f5f5f5;
    color: #333;
}

header {
    background: #222;
    color: white;
    padding: 20px;
}

header h1 {
    display: inline-block;
}

nav {
    float: right;
}

nav a {
    color: white;
    text-decoration: none;
    margin-left: 20px;
}

main {
    padding: 50px;
    text-align: center;
}
9. Create static/js/app.js
console.log("E-commerce JavaScript loaded successfully.");
10. Run the application

From the project directory:

python app.py

You should get something like:

* Running on http://127.0.0.1:5000

Open:

http://127.0.0.1:5000

Then test:

http://127.0.0.1:5000/test-db

If everything is configured correctly, you'll see:

Database connected successfully: ecommerce
What we've achieved

You now have the foundation:

HTML
  ↓
CSS
  ↓
JavaScript
  ↓
Flask/Python
  ↓
MySQL

The next major part is the database + authentication system:

Registration
     ↓
Password hashing
     ↓
MySQL users table
     ↓
Login
     ↓
Flask session
     ↓
Logout
     ↓
Protected pages

That gives you a strong portfolio foundation before we move to products, cart, checkout, and admin functionality.

Which should we build next: Login/Register, Database tables, or Product system?

Login/Register, Database tables, or Product system

I recommend building them in this order:

1. Database tables → 2. Login/Register → 3. Product system

The database is the foundation, authentication depends on the users table, and the product system can then use the same database structure.

Step 1 — Database tables

Use this MySQL schema:

CREATE DATABASE ecommerce;
USE ecommerce;

-- Users
CREATE TABLE users (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    email VARCHAR(150) NOT NULL UNIQUE,
    password_hash VARCHAR(255) NOT NULL,
    role ENUM('customer', 'admin') DEFAULT 'customer',
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Categories
CREATE TABLE categories (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100) NOT NULL UNIQUE
);

-- Products
CREATE TABLE products (
    id INT AUTO_INCREMENT PRIMARY KEY,
    category_id INT,
    name VARCHAR(200) NOT NULL,
    description TEXT,
    price DECIMAL(10,2) NOT NULL,
    stock INT DEFAULT 0,
    image VARCHAR(255),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,

    FOREIGN KEY (category_id)
        REFERENCES categories(id)
        ON DELETE SET NULL
);

-- Orders
CREATE TABLE orders (
    id INT AUTO_INCREMENT PRIMARY KEY,
    user_id INT NOT NULL,
    total_amount DECIMAL(10,2) NOT NULL,
    status ENUM(
        'pending',
        'confirmed',
        'shipped',
        'delivered',
        'cancelled'
    ) DEFAULT 'pending',
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,

    FOREIGN KEY (user_id)
        REFERENCES users(id)
);

-- Order items
CREATE TABLE order_items (
    id INT AUTO_INCREMENT PRIMARY KEY,
    order_id INT NOT NULL,
    product_id INT NOT NULL,
    quantity INT NOT NULL,
    price DECIMAL(10,2) NOT NULL,

    FOREIGN KEY (order_id)
        REFERENCES orders(id)
        ON DELETE CASCADE,

    FOREIGN KEY (product_id)
        REFERENCES products(id)
);

-- Cart
CREATE TABLE cart (
    id INT AUTO_INCREMENT PRIMARY KEY,
    user_id INT NOT NULL UNIQUE,

    FOREIGN KEY (user_id)
        REFERENCES users(id)
        ON DELETE CASCADE
);

-- Cart items
CREATE TABLE cart_items (
    id INT AUTO_INCREMENT PRIMARY KEY,
    cart_id INT NOT NULL,
    product_id INT NOT NULL,
    quantity INT NOT NULL DEFAULT 1,

    FOREIGN KEY (cart_id)
        REFERENCES cart(id)
        ON DELETE CASCADE,

    FOREIGN KEY (product_id)
        REFERENCES products(id)
);
Step 2 — Insert sample categories/products
INSERT INTO categories (name)
VALUES
('Electronics'),
('Clothing'),
('Books'),
('Shoes');

INSERT INTO products
(category_id, name, description, price, stock)
VALUES
(1, 'Laptop', 'Business laptop', 85000.00, 10),
(1, 'Wireless Mouse', 'Wireless optical mouse', 1500.00, 50),
(2, 'T-Shirt', 'Cotton T-Shirt', 1200.00, 30),
(3, 'Python Programming Book', 'Learn Python programming', 2500.00, 20),
(4, 'Running Shoes', 'Comfortable running shoes', 4500.00, 15);
Step 3 — Build Register/Login

We'll use Werkzeug to hash passwords, rather than storing plain-text passwords.

Installations are already covered:

pip install flask mysql-connector-python python-dotenv werkzeug

Create:

routes/
└── auth.py

routes/auth.py:

from flask import Blueprint, render_template, request, redirect, url_for, session
from werkzeug.security import generate_password_hash, check_password_hash

from app import get_db_connection

auth = Blueprint("auth", __name__)


@auth.route("/register", methods=["GET", "POST"])
def register():

    if request.method == "POST":

        name = request.form["name"]
        email = request.form["email"]
        password = request.form["password"]

        password_hash = generate_password_hash(password)

        connection = get_db_connection()
        cursor = connection.cursor()

        try:
            cursor.execute(
                """
                INSERT INTO users
                (name, email, password_hash)
                VALUES (%s, %s, %s)
                """,
                (name, email, password_hash)
            )

            connection.commit()

        except Exception:
            connection.rollback()
            return "Email already exists."

        finally:
            cursor.close()
            connection.close()

        return redirect(url_for("auth.login"))

    return render_template("register.html")


@auth.route("/login", methods=["GET", "POST"])
def login():

    if request.method == "POST":

        email = request.form["email"]
        password = request.form["password"]

        connection = get_db_connection()
        cursor = connection.cursor(dictionary=True)

        cursor.execute(
            "SELECT * FROM users WHERE email = %s",
            (email,)
        )

        user = cursor.fetchone()

        cursor.close()
        connection.close()

        if user and check_password_hash(
            user["password_hash"],
            password
        ):

            session["user_id"] = user["id"]
            session["user_name"] = user["name"]
            session["role"] = user["role"]

            return redirect(url_for("home"))

        return "Invalid email or password."

    return render_template("login.html")


@auth.route("/logout")
def logout():

    session.clear()

    return redirect(url_for("home"))

Then register the blueprint in app.py:

from routes.auth import auth

app.register_blueprint(auth)
Register page

Create:

templates/register.html
<!DOCTYPE html>
<html>
<head>
    <title>Register</title>
</head>

<body>

<h2>Create Account</h2>

<form method="POST">

    <label>Name</label>
    <input type="text" name="name" required>

    <br><br>

    <label>Email</label>
    <input type="email" name="email" required>

    <br><br>

    <label>Password</label>
    <input type="password" name="password" required>

    <br><br>

    <button type="submit">
        Register
    </button>

</form>

<p>
    Already have an account?
    <a href="{{ url_for('auth.login') }}">Login</a>
</p>

</body>
</html>
Login page

Create:

templates/login.html
<!DOCTYPE html>
<html>
<head>
    <title>Login</title>
</head>

<body>

<h2>Login</h2>

<form method="POST">

    <label>Email</label>
    <input type="email" name="email" required>

    <br><br>

    <label>Password</label>
    <input type="password" name="password" required>

    <br><br>

    <button type="submit">
        Login
    </button>

</form>

<p>
    Don't have an account?
    <a href="{{ url_for('auth.register') }}">
        Register
    </a>
</p>

</body>
</html>
Your current architecture
                 E-COMMERCE
                     │
          ┌──────────┴──────────┐
          │                     │
       Frontend              Backend
          │                     │
   HTML/CSS/JS              Python Flask
                                │
                         ┌──────┴──────┐
                         │             │
                    Authentication   Products
                         │             │
                         └──────┬──────┘
                                │
                              MySQL
                                │
       ┌─────────┬─────────┬────┴────┬─────────┐
       │         │         │         │         │
     users   categories products   cart      orders

Next, the Product System should add product listing, product details, search, categories, stock management, and an admin CRUD panel. That will make the project start looking like a real e-commerce application.

the Product System  

Absolutely. Let's build the Product System next. This will give your e-commerce website a real product catalog with MySQL CRUD, categories, search, stock, product details, and an admin product panel.

1. Project structure

Add these files:

ecommerce/
│
├── app.py
├── config.py
│
├── routes/
│   ├── auth.py
│   └── products.py
│
├── templates/
│   ├── index.html
│   ├── products.html
│   ├── product_detail.html
│   └── admin/
│       ├── products.html
│       └── add_product.html
│
└── static/
    ├── css/
    │   └── style.css
    └── js/
        └── app.js
2. Product routes

Create:

routes/products.py
from flask import Blueprint, render_template, request, redirect, url_for, session
from app import get_db_connection

products = Blueprint("products", __name__)


# Product listing
@products.route("/products")
def product_list():

    search = request.args.get("search", "")
    category = request.args.get("category", "")

    connection = get_db_connection()
    cursor = connection.cursor(dictionary=True)

    query = """
        SELECT
            p.id,
            p.name,
            p.description,
            p.price,
            p.stock,
            p.image,
            c.name AS category_name
        FROM products p
        LEFT JOIN categories c
            ON p.category_id = c.id
        WHERE 1=1
    """

    parameters = []

    if search:
        query += """
            AND (
                p.name LIKE %s
                OR p.description LIKE %s
            )
        """

        parameters.extend([
            f"%{search}%",
            f"%{search}%"
        ])

    if category:
        query += " AND p.category_id = %s"
        parameters.append(category)

    query += " ORDER BY p.id DESC"

    cursor.execute(query, parameters)

    product_data = cursor.fetchall()

    cursor.execute(
        "SELECT * FROM categories ORDER BY name"
    )

    categories = cursor.fetchall()

    cursor.close()
    connection.close()

    return render_template(
        "products.html",
        products=product_data,
        categories=categories,
        search=search,
        selected_category=category
    )


# Product details
@products.route("/product/<int:product_id>")
def product_detail(product_id):

    connection = get_db_connection()
    cursor = connection.cursor(dictionary=True)

    cursor.execute(
        """
        SELECT
            p.*,
            c.name AS category_name
        FROM products p
        LEFT JOIN categories c
            ON p.category_id = c.id
        WHERE p.id = %s
        """,
        (product_id,)
    )

    product = cursor.fetchone()

    cursor.close()
    connection.close()

    if not product:
        return "Product not found", 404

    return render_template(
        "product_detail.html",
        product=product
    )


# Admin product list
@products.route("/admin/products")
def admin_products():

    if session.get("role") != "admin":
        return "Unauthorized", 403

    connection = get_db_connection()
    cursor = connection.cursor(dictionary=True)

    cursor.execute(
        """
        SELECT
            p.*,
            c.name AS category_name
        FROM products p
        LEFT JOIN categories c
            ON p.category_id = c.id
        ORDER BY p.id DESC
        """
    )

    product_data = cursor.fetchall()

    cursor.close()
    connection.close()

    return render_template(
        "admin/products.html",
        products=product_data
    )


# Add product
@products.route("/admin/products/add", methods=["GET", "POST"])
def add_product():

    if session.get("role") != "admin":
        return "Unauthorized", 403

    connection = get_db_connection()
    cursor = connection.cursor(dictionary=True)

    if request.method == "POST":

        name = request.form["name"]
        description = request.form["description"]
        price = request.form["price"]
        stock = request.form["stock"]
        category_id = request.form["category_id"]
        image = request.form.get("image", "")

        cursor.execute(
            """
            INSERT INTO products
            (category_id, name, description, price, stock, image)
            VALUES (%s, %s, %s, %s, %s, %s)
            """,
            (
                category_id,
                name,
                description,
                price,
                stock,
                image
            )
        )

        connection.commit()

        cursor.close()
        connection.close()

        return redirect(
            url_for("products.admin_products")
        )

    cursor.execute(
        "SELECT * FROM categories ORDER BY name"
    )

    categories = cursor.fetchall()

    cursor.close()
    connection.close()

    return render_template(
        "admin/add_product.html",
        categories=categories
    )


# Delete product
@products.route(
    "/admin/products/delete/<int:product_id>",
    methods=["POST"]
)
def delete_product(product_id):

    if session.get("role") != "admin":
        return "Unauthorized", 403

    connection = get_db_connection()
    cursor = connection.cursor()

    cursor.execute(
        "DELETE FROM products WHERE id = %s",
        (product_id,)
    )

    connection.commit()

    cursor.close()
    connection.close()

    return redirect(
        url_for("products.admin_products")
    )
3. Register the product blueprint

In app.py, add:

from routes.products import products

app.register_blueprint(products)

Your app.py will now contain:

from flask import Flask, render_template
import mysql.connector

from config import DB_CONFIG, SECRET_KEY
from routes.auth import auth
from routes.products import products


app = Flask(__name__)

app.config["SECRET_KEY"] = SECRET_KEY


def get_db_connection():
    return mysql.connector.connect(**DB_CONFIG)


app.register_blueprint(auth)
app.register_blueprint(products)


@app.route("/")
def home():
    return render_template("index.html")


if __name__ == "__main__":
    app.run(debug=True)
4. Product listing page

Create:

templates/products.html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">

    <title>Products</title>

    <link rel="stylesheet"
          href="{{ url_for('static', filename='css/style.css') }}">
</head>

<body>

<header>

    <h1>My Store</h1>

    <nav>
        <a href="/">Home</a>
        <a href="/products">Products</a>

        {% if session.get("user_id") %}
            <a href="/logout">Logout</a>
        {% else %}
            <a href="/login">Login</a>
        {% endif %}
    </nav>

</header>


<main>

    <h2>Products</h2>


    <!-- Search -->

    <form method="GET"
          action="{{ url_for('products.product_list') }}">

        <input
            type="text"
            name="search"
            placeholder="Search products..."
            value="{{ search }}"
        >


        <!-- Category -->

        <select name="category">

            <option value="">
                All Categories
            </option>

            {% for category in categories %}

                <option
                    value="{{ category.id }}"
                    {% if selected_category|string == category.id|string %}
                        selected
                    {% endif %}
                >

                    {{ category.name }}

                </option>

            {% endfor %}

        </select>


        <button type="submit">
            Search
        </button>

    </form>


    <!-- Products -->

    <div class="product-grid">

        {% for product in products %}

            <div class="product-card">

                {% if product.image %}

                    <img
                        src="{{ product.image }}"
                        alt="{{ product.name }}"
                    >

                {% endif %}


                <h3>
                    {{ product.name }}
                </h3>


                <p>
                    {{ product.description }}
                </p>


                <h4>
                    Rs. {{ product.price }}
                </h4>


                <p>
                    Category:
                    {{ product.category_name or "Uncategorized" }}
                </p>


                <p>
                    Stock:
                    {{ product.stock }}
                </p>


                <a href="{{ url_for(
                    'products.product_detail',
                    product_id=product.id
                ) }}">
                    View Details
                </a>

            </div>

        {% else %}

            <p>
                No products found.
            </p>

        {% endfor %}

    </div>

</main>

</body>
</html>
5. Product detail page

Create:

templates/product_detail.html
<!DOCTYPE html>
<html lang="en">

<head>

    <meta charset="UTF-8">

    <title>{{ product.name }}</title>

    <link rel="stylesheet"
          href="{{ url_for('static', filename='css/style.css') }}">

</head>


<body>

<header>

    <h1>My Store</h1>

    <nav>

        <a href="/">
            Home
        </a>

        <a href="/products">
            Products
        </a>

    </nav>

</header>


<main class="product-detail">

    {% if product.image %}

        <img
            src="{{ product.image }}"
            alt="{{ product.name }}"
        >

    {% endif %}


    <div>

        <h2>
            {{ product.name }}
        </h2>


        <p>
            {{ product.description }}
        </p>


        <h3>
            Rs. {{ product.price }}
        </h3>


        <p>
            Category:
            {{ product.category_name or "Uncategorized" }}
        </p>


        <p>
            Available stock:
            {{ product.stock }}
        </p>


        {% if product.stock > 0 %}

            <button>
                Add to Cart
            </button>

        {% else %}

            <button disabled>
                Out of Stock
            </button>

        {% endif %}

    </div>

</main>

</body>
</html>

The Add to Cart button doesn't work yet because we'll build the cart system next.

6. Admin product page

Create:

templates/admin/products.html
<!DOCTYPE html>
<html>

<head>

    <title>Admin Products</title>

</head>

<body>

<h1>Product Management</h1>

<a href="{{ url_for('products.add_product') }}">
    Add New Product
</a>

<br><br>


<table border="1" cellpadding="10">

    <thead>

        <tr>

            <th>ID</th>
            <th>Name</th>
            <th>Category</th>
            <th>Price</th>
            <th>Stock</th>
            <th>Action</th>

        </tr>

    </thead>


    <tbody>

        {% for product in products %}

        <tr>

            <td>
                {{ product.id }}
            </td>

            <td>
                {{ product.name }}
            </td>

            <td>
                {{ product.category_name }}
            </td>

            <td>
                Rs. {{ product.price }}
            </td>

            <td>
                {{ product.stock }}
            </td>

            <td>

                <form
                    method="POST"
                    action="{{ url_for(
                        'products.delete_product',
                        product_id=product.id
                    ) }}"
                >

                    <button type="submit">
                        Delete
                    </button>

                </form>

            </td>

        </tr>

        {% endfor %}

    </tbody>

</table>

</body>

</html>
7. Add-product page

Create:

templates/admin/add_product.html
<!DOCTYPE html>
<html>

<head>

    <title>Add Product</title>

</head>

<body>

<h1>Add Product</h1>


<form method="POST">

    <label>
        Product Name
    </label>

    <br>

    <input
        type="text"
        name="name"
        required
    >

    <br><br>


    <label>
        Description
    </label>

    <br>

    <textarea
        name="description"
        required
    ></textarea>

    <br><br>


    <label>
        Price
    </label>

    <br>

    <input
        type="number"
        name="price"
        step="0.01"
        min="0"
        required
    >

    <br><br>


    <label>
        Stock
    </label>

    <br>

    <input
        type="number"
        name="stock"
        min="0"
        required
    >

    <br><br>


    <label>
        Category
    </label>

    <br>

    <select name="category_id" required>

        {% for category in categories %}

            <option value="{{ category.id }}">
                {{ category.name }}
            </option>

        {% endfor %}

    </select>

    <br><br>


    <label>
        Image URL
    </label>

    <br>

    <input
        type="text"
        name="image"
    >

    <br><br>


    <button type="submit">
        Add Product
    </button>

</form>

</body>

</html>
8. Improve the CSS

Add this to style.css:

.product-grid {
    display: grid;
    grid-template-columns: repeat(
        auto-fit,
        minmax(220px, 1fr)
    );

    gap: 20px;
    padding: 30px;
}


.product-card {
    background: white;
    padding: 20px;
    border-radius: 8px;
    box-shadow: 0 2px 8px rgba(0,0,0,0.1);
}


.product-card img {
    width: 100%;
    height: 200px;
    object-fit: cover;
}


.product-card h3 {
    margin: 15px 0;
}


.product-card a {
    display: inline-block;
    margin-top: 10px;
    padding: 10px 15px;
    background: #222;
    color: white;
    text-decoration: none;
}


.product-detail {
    display: flex;
    gap: 40px;
    padding: 50px;
}


.product-detail img {
    width: 400px;
    max-width: 100%;
}


button {
    padding: 10px 20px;
    cursor: pointer;
}
9. Test the Product System

Start Flask:

python app.py

Visit:

http://127.0.0.1:5000/products

You should see your products.

Test search:

http://127.0.0.1:5000/products?search=laptop

Test a category:

http://127.0.0.1:5000/products?category=1

Product details:

http://127.0.0.1:5000/product/1
Important: create an admin account

The current registration system creates:

role = customer

For development, you can temporarily promote a user through MySQL:

UPDATE users
SET role = 'admin'
WHERE email = 'your_email@example.com';

Then log out and log back in.

Go to:

http://127.0.0.1:5000/admin/products

You should now have access to the admin product management page.

Current system
                 E-COMMERCE
                     │
        ┌────────────┴────────────┐
        │                         │
     CUSTOMER                   ADMIN
        │                         │
   ┌────┴────┐              ┌─────┴─────┐
   │         │              │           │
 Products  Login        Add Product   Delete
   │                       │
 Search                    │
   │                       │
 Category                 MySQL
   │
 Details
Next: Shopping Cart

The next component should connect the Product System → Cart → Checkout → Orders:

Product
   ↓
Add to Cart
   ↓
cart
   ↓
cart_items
   ↓
Checkout
   ↓
orders
   ↓
order_items
   ↓
Order History

After that, we'll add JavaScript validation, secure authorization, pytest testing, and Git/GitHub, so this becomes a complete portfolio project rather than just a basic Flask demo





If you're learning this as a real portfolio project, I recommend building it incrementally rather than receiving one huge code file. We can start with Step 1: complete Flask + MySQL project setup, then build registration/login → products → cart → checkout → admin → testing with working code at each stage.
