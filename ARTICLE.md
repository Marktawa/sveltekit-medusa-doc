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

To link the Medusa server with the Sveltekit storefront, first, open up your Medusa project, `my-medusa-store` in your code editor, then open the `.env` file where all your environment variables are set up.

Add the variable `STORE_CORS` with the value of the URL where your storefront will be running. SvelteKit by default runs on port `5173`.

```
STORE_CORS=http://localhost:5173
```

After this, your Medusa server will be ready to receive a request from your storefront and send back responses if everything works as expected.

### List Products on Svelitekit Storefront

Open up the `storefront` directory in your code editor, and create a new file named `src/routes/+page.js`. This file will export a load function and the return value which is available to the page via the `data` prop:

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

```js
medusa.carts.createPaymentSessions(cartId)
.then(({ cart }) => {
  console.log(cart.payment_sessions)
})
```

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
PUBLIC_STRIPE_KEY=<YOUR_PUBLISHABLE_KEY>
```

### Workflow Overview

1. During checkout when the user reaches the payment section, you should create payment sessions. This will initialize the `payment_sessions` array in the `cart` object received. The `payment_sessions` is an array of available payment processors.
2. If Stripe is available as a payment processor, you should select Stripe as the payment session for the current cart. This will initialize the `payment_session` object in the `cart` object to include data related to Stripe and the current payment session. This includes the payment intent and client secret.
3. After the user enters their card details and submits the form, confirm the payment with Stripe.
4. If the payment is confirmed successfully, complete the cart in Medusa. Otherwise show an error.

### Install Dependencies

Install the necessary dependencies to show the UI and handle the payment confirmation:

```bash
npm install --save stripe @stripe/stripe-js svelte-stripe
```

You’ll also use Medusa’s JS Client to easily call Medusa’s REST APIs:

```bash
npm install @medusajs/medusa-js
```

### Add Stripe

Update the checkout page for the Stripe payment option. Open up `src/routes/checkout/+page.svelte` in your code editor.

```svelte
<script>
  import { onMount } from 'svelte';
  import { loadStripe } from '@stripe/stripe-js';
  import { PUBLIC_STRIPE_KEY } from '$env/static/public';
  import { Elements, PaymentElement, LinkAuthenticationElement, Address } from 'svelte-stripe';
  import Medusa from '@medusajs/medusa-js';

  let stripe = null;
  let clientSecret = null;
  let error = null;
  let elements;
  let processing = false;

  let cartId = null;

  onMount(async () => {
    stripe = await loadStripe(PUBLIC_STRIPE_KEY);
    const client = new Medusa();
    cartId = localStorage.getItem("cart_id");

    try {
      const { cart } = await client.carts.createPaymentSessions(cartId);
      const isStripeAvailable = cart.payment_sessions?.some(
        (session) => session.provider_id === 'stripe'
      );

      if (!isStripeAvailable) return;

      const { cart: updatedCart } = await client.carts.setPaymentSession(cartId, {
        provider_id: 'stripe',
      });

      setClientSecret(updatedCart.payment_session.data.client_secret);
    } catch (error) {
      console.error('Error creating payment session:', error);
    }
  });

  function setClientSecret(secret) {
    clientSecret = secret;
  }

  async function submit() {
    // avoid processing duplicates
    if (processing) return

    processing = true

    // confirm payment with stripe
    const result = await stripe.confirmPayment({
      elements,
      redirect: 'if_required'
    })

    // log results, for debugging
    console.log({ result })

    if (result.error) {
      // payment failed, notify user
      error = result.error
      processing = false
    } else {
      // payment succeeded, redirect to "thank you" page
      const client = new Medusa();
      const response = await client.carts.complete(cartId);
      console.log(response);
    }
  }
</script>

<h1>Welcome to the Medusa SvelteKit Store</h1>
<h2>Checkout</h2>

{#if error}
  <p class="error">{error.message} Please try again.</p>
{/if}

{#if clientSecret}
  <Elements
    {stripe}
    {clientSecret}
    theme="flat"
    labels="floating"
    variables={{ colorPrimary: '#7c4dff' }}
    rules={{ '.Input': { border: 'solid 1px #0002' } }}
    bind:elements
  >
    <form on:submit|preventDefault={submit}>
      <LinkAuthenticationElement />
      <PaymentElement />
      <Address mode="billing" />

      <button disabled={processing}>
        {#if processing}
          Processing...
        {:else}
          Pay
        {/if}
      </button>
    </form>
  </Elements>
{:else}
  Loading...
{/if}

<style>
  .error {
    color: tomato;
    margin: 2rem 0 0;
  }

  form {
    display: flex;
    flex-direction: column;
    gap: 10px;
    margin: 2rem 0;
  }

  button {
    padding: 1rem;
    border-radius: 5px;
    border: solid 1px #ccc;
    color: white;
    background: #7c4dff;
    font-size: 1.2rem;
    margin: 1rem 0;
  }
</style>
```

### Test Payment

### Capture Payments

Visit the Orders section in your Medusa Admin and 

### Add Success Page

Create a new directory named `thanks` in the `src/routes` path. In it add a `+page.svelte` file with the following code:

```svelte
<h1>Success!</h1>
<p>Payment was successfully processed.</p>
```

Update `src/routes/checkout/+page.svelte` so that once the payment is done the store will redirect to the Success page:

```svelte
<script>
  import { goto } from '$app/navigation';
  import { onMount } from 'svelte';
  import { loadStripe } from '@stripe/stripe-js';
  import { PUBLIC_STRIPE_KEY } from '$env/static/public';
  import { Elements, PaymentElement, LinkAuthenticationElement, Address } from 'svelte-stripe';
  import Medusa from '@medusajs/medusa-js';

  let stripe = null;
  let clientSecret = null;
  let error = null;
  let elements;
  let processing = false;

  let cartId = null;

  onMount(async () => {
    stripe = await loadStripe(PUBLIC_STRIPE_KEY);
    const client = new Medusa();
    cartId = localStorage.getItem("cart_id");

    try {
      const { cart } = await client.carts.createPaymentSessions(cartId);
      const isStripeAvailable = cart.payment_sessions?.some(
        (session) => session.provider_id === 'stripe'
      );

      if (!isStripeAvailable) return;

      const { cart: updatedCart } = await client.carts.setPaymentSession(cartId, {
        provider_id: 'stripe',
      });

      setClientSecret(updatedCart.payment_session.data.client_secret);
    } catch (error) {
      console.error('Error creating payment session:', error);
    }
  });

  function setClientSecret(secret) {
    clientSecret = secret;
  }

  async function submit() {
    // avoid processing duplicates
    if (processing) return

    processing = true

    // confirm payment with stripe
    const result = await stripe.confirmPayment({
      elements,
      redirect: 'if_required'
    })

    // log results, for debugging
    console.log({ result })

    if (result.error) {
      // payment failed, notify user
      error = result.error
      processing = false
    } else {
      // payment succeeded, redirect to "thank you" page
      const client = new Medusa();
      const response = await client.carts.complete(cartId);
      console.log(response);
      goto('../thanks')
    }
  }
</script>

<h1>Welcome to the Medusa SvelteKit Store</h1>
<h2>Checkout</h2>

{#if error}
  <p class="error">{error.message} Please try again.</p>
{/if}

{#if clientSecret}
  <Elements
    {stripe}
    {clientSecret}
    theme="flat"
    labels="floating"
    variables={{ colorPrimary: '#7c4dff' }}
    rules={{ '.Input': { border: 'solid 1px #0002' } }}
    bind:elements
  >
    <form on:submit|preventDefault={submit}>
      <LinkAuthenticationElement />
      <PaymentElement />
      <Address mode="billing" />

      <button disabled={processing}>
        {#if processing}
          Processing...
        {:else}
          Pay
        {/if}
      </button>
    </form>
  </Elements>
{:else}
  Loading...
{/if}

<style>
  .error {
    color: tomato;
    margin: 2rem 0 0;
  }

  form {
    display: flex;
    flex-direction: column;
    gap: 10px;
    margin: 2rem 0;
  }

  button {
    padding: 1rem;
    border-radius: 5px;
    border: solid 1px #ccc;
    color: white;
    background: #7c4dff;
    font-size: 1.2rem;
    margin: 1rem 0;
  }
</style>
```

### Test Payment

## UI Design

In the next section, we will design the UI for our storefront. 

### Add Layout

Add a layout file for the common UI elements like the header and footer. Open up your `storefront` folder and add a `src/routes/+layout.svelte` file inside.

Add the following code to `src/routes/+layout.svelte`:

```svelte
<div class="pagewrapper">
    <header id="header">
        <a href="/">HOME</a>
        <h1><a href="/">MY STORE</a></h1>
        <a href="/cart.html">CART</a>
    </header>
    <main id="main">
      <slot />
    </main>
    <footer id="footer">
        <p>Copyright 2024.</p>
        <p>Made Using Sveltekit and Medusa</p>
    </footer>
</div>

<style>
* {
    margin: 0;
    padding: 0;
}

body {
    font-family: Inter, -apple-system, BlinkMacSystemFont, Segoe UI, Roboto, Helvetica Neue, Ubuntu, sans-serif;
}

.pagewrapper {
    max-width: 1440px;
    margin: 0 auto;
}

header {
    padding: 0.6rem 1.2rem;
    display: flex;
    justify-content: space-between;
    text-align: center;
    align-items: center;
}

a {
    list-style: none;
    text-decoration: none;
    color: inherit;
}

#footer {
    font-size: 0.6rem;
    display: flex;
    justify-content: space-between;
    padding: 0.75rem 1.5rem;
}
</style>
```

### Home Page

We will start off with the home page design. The home page will be based on the following design.

![Home Page Design](https://mtm-sveltekit-medusa.pages.dev/Design%20-%20Home%20Page%20V2-0.5x.png)

Update the code for the home page, `src/routes/+page.svelte` as follows:

```svelte
<script>
  /** @type {import('./$types').PageData} */
  export let data;

  import { onMount } from "svelte";

  onMount(async () => {
    fetch(`http://localhost:9000/store/carts`, {
      method: "POST",
      credentials: "include",
    })
      .then((response) => response.json())
      .then(({ cart }) => {
        localStorage.setItem("cart_id", cart.id);
        console.log("The cart ID is " + localStorage.getItem("cart_id"));
      });
  });

  async function addProductToCart(variant_id) {
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

<section id="hero">
  <h2>Welcome to the Medusa SvelteKit Store</h2>
  <a href="#products">View Products</a>
</section>

<section id="products">
  <h3>Products</h3>
  <ul>
    {#each data.payload.products as product}
      <li>
        <img src="{product.thumbnail}" alt="">
        <h4>{product.title}</h4>
        <p>${ (product.variants[0].prices[1].amount / 100).toFixed(2) }</p>
        <button
          on:click={() => {
            addProductToCart(product.variants[0].id);
            alert("Added to Cart");
          }}
        >
          Add To Cart
        </button>
      </li>
    {/each}
  </ul>
</section>

<style>
  #hero {
    border-bottom: 1px #000000 solid;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    gap: 2rem;
    height: 100vh;
  }

  #products {
    padding: 2rem 0;
  }

  ul, li, a {
    list-style: none;
    text-decoration: none;
    color: inherit;
  }

  ul {
    display: grid;
    text-align: center;
    justify-items: center;
    grid-template-columns: repeat(3, 1fr);
    padding: 1.2rem;
    gap: 2rem;
  }

  @media (max-width: 768px) {
    ul {
        grid-template-columns: repeat(2, 1fr);
        padding: 1rem;
        gap: 1.6rem;
    }
 } 

 @media (max-width: 480px) {
    ul {
        grid-template-columns: repeat(1, 1fr);
        padding: 0.8rem;
        gap: 3rem;      
    }
 }

  li {
    max-width: 18rem;
    padding: 1.2rem 0;
  }

  img {
    width: 100%;
  }

  h3 {
    text-align: center;
    padding: 2rem 0;
  }

  button {
    text-decoration: none;
    background: #000;
    color: #FFF;
    width: 100%;
    font-family: inherit;
    padding: 0.5rem 0;
    cursor: pointer;
    border: none;
  }

  button:hover {
    font-weight: 600;
  }
</style>
```

Make sure your Medusa development server and Sveltekit development server are running and then visit the home page, [localhost:5173](http://localhost:5173) to view the changes.

![Updated Storefront Home Page with styling](https://mtm-sveltekit-medusa.pages.dev/homepage-ui.png)

### Cart Page

Update the code for the cart page, `src/routes/cart/+page.svelte` as follows:

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
                console.log("Customer ID is " + cart.customer_id);
                console.log("Customer email is " + cart.email);
            });
    }
</script>

<h2>Cart</h2>
<section id="cart">
    <table>
        <thead>
            <tr>
                <th>Item</th>
                <th>Quantity</th>
                <th>Price</th>
                <th>Total</th>
            </tr>
        </thead>
        <tbody>
            {#each items as item}
                <tr>
                    <td>{item.title}</td>
                    <td>{item.quantity}</td>
                    <td>${(item.unit_price / 100).toFixed(2)}</td>
                    <td>${(item.total / 100).toFixed(2)} </td>
                </tr>
            {/each}
        </tbody>
    </table>
</section>
<section id="summary">
    <h4>SUMMARY</h4>
    <ul>
        <li>
            <p>Subtotal</p>
            <p>${(total / 100).toFixed(2)}</p>
        </li>
        <li>
            <p>Shipping</p>
            <p>$0.00</p>
        </li>
        <li>
            <p>Taxes</p>
            <p>$0.00</p>
        </li>
        <li id="total">
            <p>Total</p>
            <p>${(total / 100).toFixed(2)}</p>
        </li>
    </ul>
    <p>Enter your email to proceed to Checkout</p>
    <input id="email" type="email" bind:value={email} />
    <button
        type="submit"
        on:click={() => {
            addCustomer();
            alert("Added your Email");
        }}
    >
        Submit
    </button>

    <a href="/checkout">Go To Checkout</a>
</section>

<style>
    * {
        margin: 0;
        padding: 0;
    }

    h2 {
        text-align: center;
        padding: 4rem 0 2rem 0;
        font-size: 4rem;
    }

    table {
        margin: 0 auto;
        table-layout: fixed;
        border-collapse: collapse;
        max-width: 16rem;
    }

    thead {
        border-bottom: 1px #000 solid;
    }

    tbody td {
        font-weight: 400;
        text-align: left;
    }

    thead th {
        text-align: left;
    }

    th,
    td {
        padding: 1.2rem;
    }

    @media (max-width: 420px) {
        th,
        td {
            padding: 1.2rem 0.8rem;
        }
    }

    #cart {
        padding: 2rem 0;
    }

    #summary {
        max-width: 16rem;
        margin: 0 auto;
        padding: 2rem 0;
    }

    #summary h4 {
        text-align: center;
        border-bottom: 1px #000 solid;
        font-size: 2rem;
        padding: 1rem;
    }

    #summary li {
        display: flex;
        justify-content: space-between;
        font-weight: 400;
        padding: 1rem 0;
    }

    #summary #total {
        border-top: 1px #000 solid;
        font-weight: 600;
        font-size: 1.2rem;
    }

    #summary a {
        display: block;
        text-decoration: none;
        width: 100%;
        background: #000;
        padding: 0.3rem 0;
        color: #fff;
        text-align: center;
        margin-top: 4rem;
    }

    #summary a:hover {
        font-weight: 600;
    }

    ul ~ p {
        padding-top: 1rem;
        font-weight: 400;
        text-align: justify;
    }

    input {
        width: 98%;
        margin: 0.5rem 0;
        padding: 0.4rem 0;
        padding-left: 0.2rem;
        border: 1px #000 solid;
    }

    button {
        text-decoration: none;
        background: #000;
        color: #fff;
        width: 100%;
        font-family: inherit;
        padding: 0.3rem 0;
        font-size: 1rem;
        cursor: pointer;
        border: none;
    }

    button:hover {
        font-weight: 600;
    }
</style>
```

Make sure your Medusa development server and Sveltekit development server are running and then add some products to your cart. Visit the cart page, [localhost:5173/cart](http://localhost:5173/cart) to view the changes.

![Updated Storefront Cart Page with styling](https://mtm-sveltekit-medusa.pages.dev/cart-page-2.png)

### Checkout Page

Update the code for the checkout page, `src/routes/checkout/+page.svelte` as follows:

```svelte
<script>
  import { goto } from '$app/navigation';
  import { onMount } from 'svelte';
  import { loadStripe } from '@stripe/stripe-js';
  import { PUBLIC_STRIPE_KEY } from '$env/static/public';
  import { Elements, PaymentElement } from 'svelte-stripe';
  import Medusa from '@medusajs/medusa-js';

  let stripe = null;
  let clientSecret = null;
  let error = null;
  let elements;
  let processing = false;

  let cartId = null;

  onMount(async () => {
    stripe = await loadStripe(PUBLIC_STRIPE_KEY);
    const client = new Medusa();
    cartId = localStorage.getItem("cart_id");

    try {
      const { cart } = await client.carts.createPaymentSessions(cartId);
      const isStripeAvailable = cart.payment_sessions?.some(
        (session) => session.provider_id === 'stripe'
      );

      if (!isStripeAvailable) return;

      const { cart: updatedCart } = await client.carts.setPaymentSession(cartId, {
        provider_id: 'stripe',
      });

      setClientSecret(updatedCart.payment_session.data.client_secret);
    } catch (error) {
      console.error('Error creating payment session:', error);
    }
  });

  function setClientSecret(secret) {
    clientSecret = secret;
  }

  async function submit() {
    // avoid processing duplicates
    if (processing) return

    processing = true

    // confirm payment with stripe
    const result = await stripe.confirmPayment({
      elements,
      redirect: 'if_required'
    })

    // log results, for debugging
    console.log({ result })

    if (result.error) {
      // payment failed, notify user
      error = result.error
      processing = false
    } else {
      // payment succeeded, redirect to "thank you" page
      const client = new Medusa();
      const response = await client.carts.complete(cartId);
      console.log(response);
      goto('../thanks')
    }
  }
</script>

<h2>Checkout</h2>

{#if error}
  <p class="error">{error.message} Please try again.</p>
{/if}

{#if clientSecret}
  <Elements
    {stripe}
    {clientSecret}
    variables={{ colorPrimary: '#000' }}
    rules={{ '.Input': { border: 'solid 1px #000' } }}
    bind:elements
  >
    <form on:submit|preventDefault={submit}>
      <PaymentElement />

      <button disabled={processing}>
        {#if processing}
          Processing...
        {:else}
          Pay
        {/if}
      </button>
    </form>
  </Elements>
{:else}
  Loading...
{/if}

<style>
  h2 {
    padding: 4rem 0 2rem 0;
    text-align: center;
    font-size: 3rem;
  }

  .error {
    color: tomato;
    margin: 2rem 0 0;
  }

  form {
    max-width: 24rem;
    display: flex;
    flex-direction: column;
    gap: 0.5rem;
    margin: 0 auto;
  }

  button {
    padding: 0.5rem 0;
    border: solid 1px #000;
    color: #FFF;
    background: #000;
    font-size: 1rem;
    margin: 1rem 0;
    font-family: inherit;
  }

  button:hover {
    font-weight: 600;
    cursor: pointer;
  }
</style>
```

Complete your cart by clicking on `Proceed to Checkout` button and visit the checkout page, [localhost:5173/checkout](http://localhost:5173/checkout) to view the changes.

![Updated Storefront Checkout Page with styling](https://mtm-sveltekit-medusa.pages.dev/checkout-page-ui-2.png)

### Success Page

Make a payment using the Stripe Test card `4242 4242 4242 4242` and this will lead to the success page.

```svelte
<h2>Success!</h2>
<section id="success">
    <p>Payment was successfully processed.</p>
</section>


<style>
    h2 {
        text-align: center;
        font-size: 3rem;
        padding: 4rem 0 2rem 0;
    }

    #success {
        margin-left: auto;
        margin-right: auto;
        margin-bottom: 8rem;
        max-width: 24rem;
        padding: 0 0.5rem;
    }

    p {
        text-align: center;
    }
</style>
```

![Updated Storefront Success Page with Styling](https://mtm-sveltekit-medusa.pages.dev/success-page-ui.png)

## Deployment

In this section we will look into the deployment of the ecommerce app. We will start off with the deployment of the Medusa Server and Admin then look into the deployment of the Sveltekit storefront.

## Deploy Medusa Server

We will deploy the Medusa Backend Server on [Render](https://render.com/). Render offers a free tier which is sufficient enough to host a Medusa Backend app.

A bit about Render. Render is a unified cloud to build and run all apps and websites with free TLS certificates, a global CDN, DDoS protection, a private network, and auto-deploy from Git.

### Set up a Project on Render

Sign up and log in to your Render account and create a project.

### Create the PostgreSQL Database (for non-Neon users)

If you have created a PostgreSQL Database using Neon please skip this step.

### Create Redis Database

Create a Redis database to handle the event queues of your Medusa server. On your Render dashboard, click the **New +** button and the **Redis** button.

![New Redis Database on Render]()

After clicking this, you will be redirected to the next page, where you can name and create the Redis database instance.

![Name and Create Redis Database Instance]()

By clicking the `Create Redis` button, you will be redirected to the Redis general page, where you will see the newly-created Redis.

![Render Redis Instance URL]()

You will use the Redis URL in the next section.

### Prepare Server

Navigate to the Medusa server directory `my-medusa-store` in your local machine. Open the `medusa-config.js` file. Ensure that you have configured your database.

```js
module.exports = {
  projectConfig: {
    redis_url: REDIS_URL,
    database_url: DATABASE_URL,
    database_type: "postgres",
    store_cors: STORE_CORS,
    admin_cors: ADMIN_CORS,
  },
  plugins,
};
```

Next, remove the `Dockerfile` file from your repository's root directory. If it exists, Render searches for this file automatically and use it to deploy your server. However, you will not be using Docker in the deployment process.

Commit your changes, and push them to your remote GitHub repository. Once your repository is ready on GitHub, return to your Render dashboard to proceed with the deployment.

On your Render dashboard, click the **New +** button, then click on the **Web Service** button.

![New Web Service on Render]()

Connect your Medusa server repo. Select or search for your repository and click the **Connect** button.

![Connect repo to Render]()

You will be redirected to the **Settings** page when clicking the **Connect** button

![Render Settings Page]()

Here, you must provide a unique name for your project, specify the region in which your web service will run, the repository branch to be used for your web service, the root directory, the runtime, the build command, and the start command.

Please note that you should replace the original start command with the following:

```bash
medusa migrations run && medusa develop
```

This particular start command enables you to create and execute your migrations or update the Medusa backend. Additionally, it ensures that these migrations are executed before the backend starts, guaranteeing their completion.

### Add Environment Variables

To add an environment variable, click the **Advanced** button above the **Create Web Service button** on the **Settings** page and select **Add Environment Variable** with the following variables:

```
PORT=9000
JWT_SECRET=something
COOKIE_SECRET=something
DATABASE_URL=<<DATABASE_URL>>
REDIS_URL=<<REDIS_URL>>
```

> **NOTE**
>
> It’s recommended to use other values for `JWT_SECRET` and `COOKIE_SECRET` than `something` for better security.

The last properties are the URLs to connect to the databases. You need to add the database URLs you got when you created the Postgres and Redis databases earlier.

Neon users simply need to add the URL from the previous steps.

![Add Database URLs]()

Scroll to the bottom of the **Settings** page and click **Create Web Service** button.

Upon successful deployment, you will observe a status message indicating that the deployment has been successful with the text **deploy succeeded**.

![Deployment Overview in Render Dashboard]()

### Test the Backend

Once the backend has been successfully deployed, you can access the app in your browser using the domain name. For instance, entering the URL `<YOUR_APP_URL>/store/products` will display the currently available products on your backend.

![Successful Deployment]()

## Deploy Medusa Admin

We will deploy the Medusa Admin application on Cloudflare Pages.

### Create GitHub Repo (Optional)

Hosting providers like Cloudflare allow you to deploy your project directly from GitHub. This makes it easier for you to push changes and updates without having to manually trigger the update in the hosting provider.

> **NOTE:**
>
> Even though you are just deploying the admin, you must include the entire Medusa backend project in the deployed repository. The build process of the admin uses many of the backend's repos.

```bash
git init
git remote add <GITHUB_URL>
git add .
git commit -m "Initial commit"
git push
```

### Configure Build Command

In the `package.json` of the Medusa backend, add or change a build script for the admin:

```json
"scripts": {
  //other scripts
  "build:admin": "medusa-admin build --deployment",
}
```

> **NOTE:**
>
> When using `--deployment` option, the backend's URL is loaded from the `MEDUSA_ADMIN_BACKEND_URL` environment variable. You will configure this environment variable in a later step.

### Preparing Deployment

- If your hosting provider supports choosing a Framework Preset, choose "Other" option as the Medusa Admin doesn't follow known framework presets.
- Set the build command of your deployed project to use the `build:admin` command:

```bash
npm run build:admin
```

- Set the output directory of your deployed project to `build`.
- Add the environment variable `MEDUSA_ADMIN_BACKEND_URL` and set its value to the URL of your deployed Medusa backend.
-  If your hosting provider supports URL rewrites, add a configuration to rewrite the `/(.*)` URL pattern to `/index.html`. For example, if you are deploying to Vercel you add the following in `vercel.json`:

```json
{
  "rewrites": [{ "source": "/(.*)", "destination": "/index.html" }]
}
```

### Configure CORS on the Deployed Backend

To send requests from the admin dashboard to the Medusa backend, you must set the `ADMIN_CORS` environment variable on your backend to the admin's URL:

```
ADMIN_CORS=<ADMIN_URL>
```

Where `<ADMIN_URL>` is the URL of your admin dashboard that you just deployed. Then, restart your Medusa backend. Once the backend is running again, you can use your admin dashboard.

### (OPTIONAL) Create Admin User

To log in to your dashboard, you must have an admin user. To create one, run the following command on your deployed backend:

```bash
npx medusa user --email admin@medusa-test.com --password supersecret
```

You can then log in using the specified email and password.

## Deploy Storefront

We will deploy the Sveltekit storefront on Cloudflare Pages. 

### Create a GitHub repo

Create a new GitHub repository by visiting [repo.new](https://repo.new). After creating a new repo, go to your newly created project directory to prepare and push your local application to GitHub by running the following commands in your terminal:

```bash
git init
git remote add origin https://github.com/<your-gh-username>/<repository-name>
git add .
git commit -m "Initial commit"
git branch -M main
git push -u origin main
```

### SvelteKit Cloudflare Configuration

To use SvelteKit with Cloudflare Pages, you need to add the [Cloudflare adapter](https://kit.svelte.dev/docs/adapter-cloudflare) to your application.

Install the Cloudflare Adapter  in the root of your SvelteKit `storefront` folder by running:

```bash
npm i --save-dev @sveltejs/adapter-cloudflare
```

Include the adapter in `svelte.config.js`:

```js
import adapter from '@sveltejs/adapter-cloudflare';

/** @type {import('@sveltejs/kit').Config} */
const config = {
  kit: {
    adapter: adapter(),
    // ... truncated ...
  }
};

export default config;
```

Access the added KV or Durable objects(or generally any binding) in your endpoint with `env`:

```js
export async function post(context) {
  const counter = context.platform.env.COUNTER.idFromName("A");
}
```

### Prepare Deployment via Cloudflare Dashboard

Log in to the [Cloudflare Dashboard](https://dash.cloudflare.com/) and select your account.

In Account Home, select **Workers & Pages** > **Create application** > **Pages** > **Connect to Git**.

You will be asked to authorize access to your GitHub account if you have not already done so. Cloudflare needs this authorization to deploy your projects from your GitHub account. You may narrow Cloudflare's access to specific repositories. However, you will have to manually update this list [within your GitHub settings](https://github.com/settings/installations) when you want to add more repositories to Cloudflare Pages.

Select the new GitHub repository that you create and, in **Set up builds and deployments**, provide the following information:

|Configuration Option|Value|
|---|---|
|Production branch|`main`|
|Build command|`npm run build`|
|Build directory|`.svelte-kit/cloudflare`|

Optionally, you can customize the **Project name** field. It defaults to the GitHub repository's name, but it does not need to match. The **Project name** value is assigned as your `*.pages.dev` subdomain.

After completing configuration, click the **Save and Deploy** button.

You will see your first deploy pipeline in progress. Pages installs all dependencies and builds the project as specified.

Cloudflare Pages will automatically rebuild your project and deploy it on every new pushed commit.

Additionally, you will have access to [preview deployments](https://developers.cloudflare.com/pages/configuration/preview-deployments/), which repeat the build-and-deploy process for pull requests. With these, you can preview changes to your project with a real URL before deploying them to production.

## Conclusion

In this tutorial, we have successfully built a full-featured ecommerce application using Medusa as the backend and SvelteKit as the frontend framework. We covered the entire process, starting from installing and setting up both Medusa and SvelteKit to adding essential features like a cart, checkout process, and payment integration with Stripe.

Throughout the tutorial, we explored the modular and composable nature of Medusa, which allows developers to build ecommerce applications tailored to their specific needs. We also witnessed the power and simplicity of SvelteKit, a modern frontend framework that provides a seamless development experience and excellent performance.

By following this tutorial, you have gained valuable knowledge and practical experience in building an ecommerce application from scratch. You have learned how to integrate Medusa with SvelteKit, implement various ecommerce functionalities, design a user-friendly interface, and deploy the application to a production environment.

This project serves as a solid foundation for further exploration and customization. You can extend the application by adding more features, integrating with other payment gateways, or enhancing the user experience based on your requirements.

The combination of Medusa and SvelteKit offers a powerful and flexible solution for building ecommerce applications. With Medusa's extensive feature set and SvelteKit's efficient rendering and developer experience, you can create scalable and performant ecommerce platforms tailored to your business needs.

Remember, ecommerce is a constantly evolving landscape, and staying up-to-date with the latest trends and best practices is crucial for success. Embrace the open-source nature of Medusa and SvelteKit, and actively participate in their respective communities to learn from others and contribute your own ideas and improvements.

Congratulations on completing this tutorial! You are now equipped with the knowledge and skills to embark on your own ecommerce journey, leveraging the power of Medusa and SvelteKit.

---

## Rough Notes

I want the cart to load asynchronously using await. Maybe like this:

`src/routes/cart/+page.svelte`
```svelte
<!-- src/routes/cart/+page.svelte -->
<script>
    import { onMount } from "svelte";

    let data;
    let email;
    let total;
    let items = [];
    let cartloaded = false;

    onMount(async () => {
        const id = localStorage.getItem("cart_id");
        const res = await fetch(`http://localhost:9000/store/carts/${id}`, {
            credentials: "include",
        });
        data = await res.json();
        items = data.cart.items;
        total = data.cart.total;
        cartloaded = true;
    });

    async function addCustomer() {
        const id = localStorage.getItem("cart_id");
        await fetch(`http://localhost:9000/store/carts/${id}`, {
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

{#if cartloaded}
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
{:else}
  <p>Loading...</p>
{/if}
```

How about adding the following functions into a Utility JS library `util.js`:

- `addCustomer`
- `addProductToCart`
- `fetchCart`


Draw UI interactions for what you want to achieve:
- When customer clicks the `Submit` button after filling the form to provide his email, the cart should take him to the checkout page.
- It should look like this start with the statement: "Please provide us with your email to proceed to checkout"
- The cart should have a `Reset Cart` button which clears the cart and shows the message "The Cart is Empty." and  a link to the store page with the message " Return to the Shop and Add Items to your cart".
- Each time a customer clicks on a product, a popover with the message "{Name of product} has been added to your cart" should appear and disappears once the customer clicks the Close icon or an Empty space.

The currency needs to be converted to its decimal value.