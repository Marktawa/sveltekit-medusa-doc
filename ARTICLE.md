# Bejamas Proposal

Medusa is an open source tool for quickly setting up a commerce backend. Couple that with Sveltekit, a frontend framework capable of  building storefronts. What do you get? A full stack, modular ecommerce app that can support a wide range of use cases. 

What will this tutorial cover?
- Installation and setup of the Medusa server API
- Setup of the Medusa Admin User Interface
- Creation of a Sveltekit app
- Add page to display all products from the Medusa server API
- Add page to display a single product
- Add Cart and Checkout functionality to app
- Add a payment provider (e.g. Stripe, PayPal)
- Deployment of the storefront, admin and backend

Prerequisites:
- Some knowledge of HTML, CSS, and JavaScript
- Knowledge of Sveltekit is a plus but not necessary
- Node.js v16 or higher installed on your system


Support materials:
- A code repository with a well-detailed README to guide users on how to test the app.
- A live version of the app
- A short video demo of the app

# Building an ecommerce store using Medusa and Sveltekit

## Introduction

Medusa is an open source tool that can help you set up a headless ecommerce server backend with relative ease. Couple that with Sveltekit, a frontend framework for building web apps. What do you get? A full stack, modular ecommerce app that can support a wide range of use cases.

What is this tutorial for?

This tutorial will teach you how to set up a simple ecommerce web app using Medusa as your store backend and Sveltekit for the visual storefront. It will showcase the fundamental building blocks required to run the app in development, testing, staging and production stages as well as showcase how you can deploy the app. At the end of this tutorial you should have acquired the overarching knowledge necessary in building ecommerce apps of a composable nature.

Why Medusa?

Medusa is one of the few if not the only open source ecommerce backend that is feature rich allowing developers to make all sorts of ecommerce apps to fit any use case. Medusa is also gaining popularity within the developer community such that it worth taking a look at how you can make an ecommerce app using Medusa. Furthermore, Medusa supports all sorts of ecommerce app architectures. Be it headless, composable, semi-modular you name it, all scenarios work well with Medusa.

Why Sveltekit?

Sveltekit is a framework based on the popular JavaScript library called Svelte. It has also gained a lot of popularity in the frontend community in the past few years. It is simple to understand, it is fast and performant and a useful alternative to the React ecosystem. 

## Prerequisites

To follow along with the tutorial you need to have some knowledge of the following:
- Basic understanding of HTML, CSS, and JavaScript
- Basic understanding of Node.js and npm
- Basic understanding of the command line (Bash)
- Knowledge of Svelte and Sveltekit is a bonus but not a requirement.
- Knowledge of Medusa is a bonus but not a requirement.

In addition to knowing these tools, your computer system should have the following packages installed:
- Node.js (v18 and above) 
- yarn (optional)
- git (optional)

Before proceeding with the tutorial you can check out the following links for useful resources:
- Video demo.
- Live link of the app.
- Git repo containing the project source code.

## Installation and setup of the Medusa server API

In this step you will install and set up the Medusa Server backend. 

Open up your terminal and create a project folder to contain all the source code for the entire project. Name it `medusa-sveltekit`.

```bash
mkdir medusa-sveltekit
```

### Set up PostgreSQL on Neon

If you have PostgreSQL installed locally, you can skip this step. 

Visit the [Neon - Sign Up](https://console.neon.tech/signup) page and create a new account.

[Create a new project](https://console.neon.tech/app/projects) in the Neon console. Give your project a name like `mystore` and your database a name like `storedb` then click **Create project**.

Take note of your connection string which will look something like: `postgresql://dominggobana:JyyuEdr809p@df-hidden-bonus-ertd7sio.us-east-3.aws.neon.tech/storedb?sslmode=require`. It is in the form `postgres://[user]:[password]@[host]/[dbname]`.You will provide connection string as a database URL to your Medusa server.

### Install Medusa CLI

In your terminal, inside the `my-store` folder run the following command to install the Medusa CLI. We will use it to install the Medusa server.

```bash
npm install @medusajs/medusa-cli -g
```

### Create a new Medusa project

```bash
medusa new my-medusa-store
```

You will be asked to specify your PostgreSQL database credentials. Choose "Skip database setup".

A new directory named `my-medusa-store` will be created to store the server files

### Configure Database - Neon Users

If you have PostgreSQL installed locally, you can skip this step. 

Add the connection string as the `DATABASE_URL` to your environment variables. Inside `my-medusa-store` create a `.env` file and add the following:

```
DATABASE_URL=postgresql://dominggobana:JyyuEdr809p@df-hidden-bonus-ertd7sio.us-east-3.aws.neon.tech/mystoredb?sslmode=require
```

### Configure Database - Local PostgreSQL DB

If you have PostgreSQL configured on Neon, you can skip this step. 

Access the PostgreSQL console to create a new user and database for the Medusa server.

```bash
sudo -u postgres psql
```

To create a new user named `medusa_admin` run this command:

```sql
CREATE USER medusa_admin WITH PASSWORD 'medusa_admin_password';
```

Now, create a new database named `medusa_db` and make `medusa_admin` the owner.

```sql
CREATE DATABASE medusa_db OWNER medusa_admin;
```

Last, grant all privileges to `medusa_admin` and exit the PostgreSQL console.

```sql
GRANT ALL PRIVILEGES ON DATABASE medusa_db TO medusa_admin;
```

```sql
exit
```

Add the connection string as the `DATABASE_URL` to your environment variables. Inside `my-medusa-store` create a `.env` file and add the following:

```
DATABASE_URL=postgres://medusa_admin:medusa_admin_password@localhost:5432/medusa_db
```

### Seed Database

Run migrations and seed data to the database by running the following command:

```bash
cd my-medusa-store
medusa seed --seed-file="./data/seed.json"
```

### Start your Medusa backend

```bash
medusa develop
```

The Medusa server will start running on port `9000`.

Test your server:
```bash
curl localhost:9000/store/products
```

If it is working, you should see a list of products.

## Install and Serve Medusa Admin with the Backend

This section explains how to install the admin to be served with the Medusa Backend.

### Install the package

Inside `my-medusa-store` stop your Medusa server, `CTRL + C` and run the following command to install the Medusa Admin Dashboard.

```bash
npm install @medusajs/admin
```

Test your install by re-running your server.
```bash
medusa develop
```

Open up your browser and visit `localhost:7001` to see the Medusa Admin Dashboard. Use the Email `admin@medusa-test.com` and password `supersecret` to log in.

![Medusa Admin Dashboard](https://res.cloudinary.com/craigsims808/image/upload/v1709028079/articles/medusa-sveltekit/medusa-admin-dashboard_yxzt95.png)

## Set Up Sveltekit

### Create a Sveltekit project

Open up a new terminal session inside the `medusa-sveltekit` directory. Run the following commands:

```bash
npm create svelte@latest storefront
cd storefront
npm install
```

Answer the prompts as follows:

- `Which Svelte app template?`, Answer: `Skeleton project (Barebones scaffolding for your new SvelteKit app)`
- `Add type checking with TypeScript?`, Answer: `No`
- `Select additional options (use arrow keys/space bar)` Leave it blank.

This will scaffold a new project in the `storefront` directory.

## Link Storefront To Server

### Configure SvelteKit Storefront URL

To link the Medusa server with the Nuxt storefront, first, open up your Medusa project, `my-medusa-store` in your code editor, then open the `.env` file where all your environment variables are set up.

Add the variable `STORE_CORS` with the value of the URL where your storefront will be running. SvelteKit by default runs on port `5173`.

```
STORE_CORS=http://localhost:5173
```

After this, your Medusa server will be ready to receive a request from your storefront and send back responses if everything works as expected.

### List Products on Svelitekit Storefront

Open up the `storefront` directory in your code editor, and create a new file named `src/routes/+page.js` that exports a load function, the return value of which is available to the page via the `data` prop:

```js
/** @type {import(./$types').PageLoad} */
export async function load({ fetch }) {
    const res = await fetch(`http://localhost:9000/store/products`);
    const payload = await res.json();
    return { payload };
}
```

Replace the existing code in `src/routes/+page.svelte` with the following:

```svelte
<script>
  /** @type {import('./$types').PageData} */
  export let data;
</script>

<h1>Welcome to the Medusa SvelteKit Store</h1>
<h2>Products</h2>
<ul>
    {#each data.payload.products as product}
        <li>{product.title} - ${product.variants[0].prices[0].amount}</li>
    {/each}
</ul>
```

Open up the `storefront` directory in your terminal and start the SvelteKit development server.

```bash
npm run dev
```

Visit [`localhost:5173`](http://localhost:5173) in your browser to see the list of products from your Medusa backend.

![Storefront with list of products](https://res.cloudinary.com/craigsims808/image/upload/v1709033109/articles/medusa-sveltekit/storefront-home_vshahf.png)

## Add Cart

Update the `src/routes/page.js` with the following code, to create a cart on page load:

```js
/** @type {import(./$types').PageLoad} */
export async function load({ fetch }) {
    const res = await fetch(`http://localhost:9000/store/products`);
    const payload = await res.json();
    return { payload };
}
```

Update each product in `src/routes/+page.svelte` with a button to add a product to the cart.

```svelte
<script>
  /** @type {import('./$types').PageData} */
  export let data;

  fetch(`http://localhost:9000/store/carts`, {
        method: "POST",
        credentials: "include",
    })
    .then((response) => response.json())
    .then(({ cart }) => {
        localStorage.setItem("cart_id", cart.id)
        // assuming you have a state variable to store the cart
        // setCart(cart)
    })

  function addProductToCart(variant_id) {
    const id = localStorage.getItem("cart_id");
    if (id) {
        fetch(`http://localhost:9000/store/carts/${id}/line-items`, {
            method: "POST",
            dentials: "include",
            headers: {
                "Content-Type": "application/json",
            },
            body: JSON.stringify({
                variant_id,
                quantity: 1,
            }),
        })
            .then((response) => response.json());
            //.then(({ cart }) => setCart(cart));
    }

  }
</script>

<h1>Welcome to the Medusa SvelteKit Store</h1>
<h2>Products</h2>
<ul>
    {#each data.payload.products as product}
        <li>
            {product.title} - ${product.variants[0].prices[0].amount}
            <button on:click={
                addProductToCart(product.variants[0].id); 
                alert('Added to Cart');
              }
            >
                Add To Cart
            </button>
        </li>
    {/each}
</ul>
```

Create a new folder inside `src/routes` named `cart`. Add a `+page.svelte` and a `+page.js` files to the `cart` folder.

Add the following code to `src/routes/cart/+page.js`:

```js
/** @type {import(./$types').PageLoad} */
export async function load({ fetch }) {
    const id = localStorage.getItem("cart_id")

    const res = await fetch(`http://localhost:9000/store/carts/${id}`, {
        credentials: "include",
    });
    const cart = await res.json();
    return { cart };
}
```

To finally load the cart, when a user navigates to the cart page, create a new file named, `src/routes/cart/+page.svelte`:

```svelte
<script>
  /** @type {import('./$types').PageData} */
  export let data;
</script>

  <h1>Welcome to the Medusa SvelteKit Store</h1>
  <h2>Cart</h2>

  <ul>
    {#each data.cart.items as item}
        <li>
            TITLE: {item.title} PRICE: {item.unit_price} QUANTITY: {item.quantity}
        </li>
    {/each}
</ul>
```

## Add Checkout Flow



## Add Payment Provider

