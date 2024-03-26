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

Update `src/routes/+page.svelte` with the following code, to create a cart on page load:

```svelte
<script>
  /** @type {import('./$types').PageData} */
  export let data;

  import { onMount } from 'svelte';

  	onMount(async () => {
		fetch(`http://localhost:9000/store/carts`, {
        method: "POST",
        credentials: "include",
    })
    .then((response) => response.json())
    .then(({ cart }) => {
        localStorage.setItem("cart_id", cart.id)
        console.log("The cart ID is " + localStorage.getItem("cart_id"));
    });
		
	});
</script>

<h1>Welcome to the Medusa SvelteKit Store</h1>
<h2>Products</h2>
<ul>
    {#each data.payload.products as product}
        <li>{product.title} - ${product.variants[0].prices[0].amount}</li>
    {/each}
</ul>
```

Revisit `localhost:5173` in  your browser and open the `Console` section of your Dev Tools. You should see a message with a cart ID to confirm creation of the cart.

![Confirmation of Cart Creation](/confirmation-of-cart-creation.png)

Update each product in `src/routes/+page.svelte` with a button to add a product to the cart.

```svelte
<script>
  /** @type {import('./$types').PageData} */
  export let data;

  import { onMount } from 'svelte';

  	onMount(async () => {
		fetch(`http://localhost:9000/store/carts`, {
        method: "POST",
        credentials: "include",
    })
    .then((response) => response.json())
    .then(({ cart }) => {
        localStorage.setItem("cart_id", cart.id)
        console.log("The cart ID is " + localStorage.getItem("cart_id"));
    });
		
	});

  function addProductToCart(variant_id) {
    const id = localStorage.getItem("cart_id");
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
</script>

<h1>Welcome to the Medusa SvelteKit Store</h1>
<h2>Products</h2>
<ul>
    {#each data.payload.products as product}
        <li>
            {product.title} - ${product.variants[0].prices[0].amount}
            <button on:click={() => { 
                  addProductToCart(product.variants[0].id); 
                  alert('Added to Cart');
                  }}
            >
                Add To Cart
            </button>
        </li>
    {/each}
</ul>
```

Confirm whether the products are being added to the cart by performing a curl request as follows:

```bash
curl localhost:9000/store/carts/cart_01HRBTB0X79NAGJYY8T6D5BGK6
```

Replace with the specific cart id as listed in your browser console. If all is working your cart should be populated with some products.

The next step is to display the cart by creating a cart page.

Create a new folder inside `src/routes` named `cart`. Add a `+page.svelte` file to the `cart` folder.

Add the following code to `src/routes/cart/+page.svelte`:

```svelte
<script>
    import { onMount } from "svelte";

    let data;
    let total;
    let items = [];

    onMount(async () => {
        const id = localStorage.getItem("cart_id");
        const res = await fetch(`http://localhost:9000/store/carts/${id}`, {
            credentials: "include",
        });
        data = await res.json();
        items = data.cart.items;
        total = data.cart.total;
    });
</script>

<h1>Welcome to the Medusa SvelteKit Store</h1>
<h2>Cart</h2>

<ul>
    {#each items as item}
        <li>
            TITLE: {item.title} PRICE: {item.unit_price} QUANTITY: {item.quantity}
        </li>
    {/each}
</ul>
<p>The total price for your cart is {total}</p>
```

Test your cart page by adding some products to your cart then visiting `localhost:5173/cart` in your browser. You should see a list of products with the quantity and price info as well as the cart total.

![Cart Page](/cart-page.png)

Next, associate your cart with an email address for the user. This is necessary to complete the cart.

```svelte
<script>
    import { onMount } from "svelte";

    let data;
    let email;
    let total;
    let items = [];

    onMount(async () => {
        const id = localStorage.getItem("cart_id");
        const res = await fetch(`http://localhost:9000/store/carts/${id}`, {
            credentials: "include",
        });
        data = await res.json();
        items = data.cart.items;
        total = data.cart.total;
    });

    function addCustomer() {
        const id = localStorage.getItem("cart_id");
        fetch(`http://localhost:9000/store/carts/${id}`, {
            method: "POST",
            credentials: "include",
            headers: {
                "Content-Type": "application/json",
            },
            body: JSON.stringify({
                email: email,
            }),
        })
        .then((response) => response.json())
        .then(({ cart }) => {
            console.log("Customer ID is " + cart.customer_id)
            console.log("Customer email is " + cart.email)
        });
    }
</script>

<h1>Welcome to the Medusa SvelteKit Store</h1>
<h2>Cart</h2>

<ul>
    {#each items as item}
        <li>
            TITLE: {item.title} PRICE: {item.unit_price} QUANTITY: {item.quantity}
        </li>
    {/each}
</ul>
<p>The total price for your cart is {total}</p>
<p>Enter your email to Proceed to Checkout</p>
<input id="email" type="email" bind:value={email}>
<button type="submit" on:click={() => {
    addCustomer();
    alert('Added your Email');
    }}
>
    Submit
</button>

```

## Add Checkout Functionality

Create a page called, `src/routes/checkout/+page.svelte` to load the checkout page.

Add the following code to initialize the payment session:

```svelte
<script>
    import { onMount } from "svelte";

    onMount(async () => {
        const id = localStorage.getItem("cart_id");
        fetch(`http://localhost:9000/store/carts/${id}/payment-sessions`, {
            method: "POST",
            credentials: "include",
        })
        .then((response) => response.json())
        .then(({ cart }) => {
            console.log(cart.payment_sessions)
        })
    });
</script>

<h1>Welcome to the Medusa SvelteKit Store</h1>
<h2>Checkout</h2>
```

Visit `localhost:5173/checkout` in your browser and open up the Console in your Devtools.

![Init Payment Session](init-payment-session.png)

## Add Cart Completion



Next add Cart completion to the checkout process, by adding the following code:

```svelte
<script>
import { onMount } from "svelte";

    onMount(async () => {
        const id = localStorage.getItem("cart_id");
        fetch(`http://localhost:9000/store/carts/${id}/payment-sessions`, {
            method: "POST",
            credentials: "include",
        })
        .then((response) => response.json())
        .then(({ cart }) => {
            console.log(cart.payment_sessions)
        })
    });

function completeCart() {
    const id = localStorage.getItem("cart_id");
    fetch(`http://localhost:9000/store/carts/${id}/complete`, {
        method: "POST",
        dentials: "include",
        headers: {
            "Content-Type": "application/json",
        },
    })
    .then((response) => response.json())
    .then(({ type, data }) => {
        console.log(type, data)
    })
  }
</script>

<h1>Welcome to the Medusa SvelteKit Store</h1>
<h2>Checkout</h2>

<button on:click={() => { 
        completeCart(); 
        alert('Cart Complete');
        }}
>
    Complete Cart
</button>

```

Click on the `Complete Cart` button to test if the order was completed using the manual Medusa payment provider. You should see the following:

![Order Complete](/order-complete.png)


## Add Payment Provider

Stripe is a battle-tested and unified platform for transaction handling. Stripe supplies you with the technical components needed to handle transactions safely and all the analytical features necessary to gain insight into your sales. These features are also available in a safe test environment which allows for a concern-free development process.

Using the `medusa-payment-stripe plugin`, you will set up your Medusa project with Stripe as a payment processor.

Create a Stripe account and retrieve the API Keys and secrets from your account to connect Medusa to your Stripe account. Add them to the environment variables in `.env` in `my-medusa-store`.

```
STRIPE_API_KEY=sk_...
STRIPE_WEBHOOK_SECRET=whsec_...
```

### Install Stripe Plugin

Open up your terminal, in the root of your Medusa backend. Stop your Medusa server and run the following command to install the Stripe plugin:

```bash
npm install medusa-payment-stripe
```

### Configure the Stripe Plugin

Next, you need to add configurations for your Stripe plugin.

In `medusa-config.js`, add the following at the end of the plugins array:

```js
const plugins = [
  // ...
  {
    resolve: `medusa-payment-stripe`,
    options: {
      api_key: process.env.STRIPE_API_KEY,
      webhook_secret: process.env.STRIPE_WEBHOOK_SECRET,
    },
  },
]
```

The Stripe plugin uses two configuration options. The `api_key` is essential to both your development and production environments. As for the `webhook_secret`, it’s essential for your production environment. So, if you’re only using Stripe for development you can skip adding the value for this option at the moment.

### Add Stripe to Region in Medusa Admin

1. Go to Settings → Regions.
2. Select a region to edit.
3. Click on the three dots icon at the top right of the first section on the right.
4. Click on Edit Region Details from the dropdown.
5. Under the providers section, select the payment providers you want to add to the region.
6. Unselect the payment providers you want to remove from the region.
7. Click the "Save and close" button.

### Add Stripe Key to Sveltekit Storefront

Add your Stripe Publishable Key to your Sveltekit storefront environment variables. Open up `.env` in your Sveltekit Storefront and add the following:

```
SVELTEKIT_PUBLIC_STRIPE_KEY=<YOUR_PUBLISHABLE_KEY>
```

### Workflow Overview

1. During checkout when the user reaches the payment section, you should create payment sessions. This will initialize the `payment_sessions` array in the `cart` object received. The `payment_sessions` is an array of available payment processors.
2. If Stripe is available as a payment processor, you should select Stripe as the payment session for the current cart. This will initialize the `payment_session` object in the `cart` object to include data related to Stripe and the current payment session. This includes the payment intent and client secret.
3. After the user enters their card details and submits the form, confirm the payment with Stripe.
4. If the payment is confirmed successfully, complete the cart in Medusa. Otherwise show an error.

### Install Dependencies

Install the necessary dependencies to show the UI and handle the payment confirmation:

```bash
npm install --save @stripe/stripe-js
```

You’ll also use Medusa’s JS Client to easily call Medusa’s REST APIs:

```bash
npm install @medusajs/medusa-js
```

### Initialize Stripe

### Implement the Workflow

### Capture Payments

## Deployment

### Deploy Storefront

### Deploy Medusa Server

### Deploy Medusa Admin

## Conclusion
