# Building an ecommerce store using Medusa and Sveltekit

![cover](/gh-cover-inter-tinified.png)

## Introduction

Medusa is an open source tool that can help you set up a headless ecommerce server backend with relative ease. Couple that with Sveltekit, a frontend framework for building web apps. What do you get? A full stack, modular ecommerce app that can support a wide range of use cases.

### What is this tutorial for?

This tutorial will teach you how to set up a simple ecommerce web app using Medusa as your store backend and Sveltekit for the visual storefront. It will showcase the fundamental building blocks required to run the app in development and production stages as well as showcase how you can deploy the app. At the end of this tutorial you should have acquired the overarching knowledge necessary in building ecommerce apps of a composable nature.

### Why Medusa?

Medusa is one of the few if not the only open source ecommerce backend that is feature rich allowing developers to make all sorts of ecommerce apps to fit any use case. Medusa is also gaining popularity within the developer community such that it worth taking a look at how you can make an ecommerce app using Medusa. Furthermore, Medusa supports all sorts of ecommerce app architectures. Be it headless, composable, semi-modular you name it, all scenarios work well with Medusa.

### Why Sveltekit?

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
- git

Before proceeding with the tutorial you can check out the following links for useful resources:
- [Video demo]().
- [Live link of the app](https://sveltekit-medusa-storefront.pages.dev/).
- [Git repo containing the project source code](https://github.com/Marktawa/sveltekit-medusa).

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

### List Products on Sveltekit Storefront

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

Replace with the specific `cart id` as listed in your browser console. If all is working your cart should be populated with some products.

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

[Create a Stripe account](https://dashboard.stripe.com/register) and retrieve the [Stripe Secret API Key](https://dashboard.stripe.com/test/apikeys) from your account to connect Medusa to your Stripe account. 

![Stripe Secret key](/stripe-secret-key.png)

Add the key to your environment variables in `.env` in `my-medusa-store`.

```
STRIPE_API_KEY=sk_...
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

### Add Stripe to Region in Medusa Admin

Make sure your Medusa backend server is running, then log in to your Medusa Admin Dashboard.

![Medusa Admin Dashboard - Home Page](/medusa-admin-dashboard.png)

Go to **Settings** then select **Regions**.

![Medusa Settings - Select Regions](/medusa-admin-select-region-settings.png)

Select a region to edit.

![Medusa Regions Page](/medusa-admin-region-page.png)

Click on the three dots icon at the top right of the first section on the right. Click on Edit Region Details from the dropdown.

Under the providers section, add all `Stripe` options to the region. Unselect the payment providers you want to remove from the region. Click the "Save and close" button.

![Add Stripe to Region](/edit-region-details-stripe-add-stripe.png)

### Add Stripe Key to Sveltekit Storefront

Add your Stripe Publishable API Key to your Sveltekit storefront environment variables. Open up `.env` in your Sveltekit Storefront and add the following:

```
PUBLIC_STRIPE_KEY=<YOUR_PUBLISHABLE_KEY>
```

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
    border: solid 1px #000;
    color: white;
    background: #000;
    font-size: 1.2rem;
    margin: 1rem 0;
  }
</style>
```

### Test Payment

Test if the Stripe integration has worked well by making test a payment. If everything is working your checkout page should appear like this.

![Checkout Page](/checkout-no-ui.png)

A successful payment should lead users to the following page:

![Success Page](/success-no-ui.png)

### Capture Payment

Visit the Orders section in your Medusa Admin and click on the order you placed earlier in your storefront to capture it.

![Capture payment in Medusa Dashboard](/capture-payment-02.png)

You can check to see if the payment was successful in your Stripe Dashboard [Payment Section](https://dashboard.stripe.com/test/payments), after capturing it in your Medusa Dashboard.

![Successful payment in Stripe Dashboard](/successful-payment-stripe.png)

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
        <a href="/cart">CART</a>
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
  
  .pagewrapper {
      max-width: 1440px;
      font-family: Inter, -apple-system, BlinkMacSystemFont, Segoe UI, Roboto, Helvetica Neue, Ubuntu, sans-serif;
      margin: 0 auto;
  }
  
  header {
      padding: 0.6rem 1.2rem;
      display: flex;
      justify-content: space-between;
      text-align: center;
      align-items: center;
      border-bottom: 1px solid #000;
  }
  
  a {
      list-style: none;
      text-decoration: none;
      color: inherit;
  }
  
  footer {
      font-size: 0.6rem;
      display: flex;
      justify-content: space-between;
      margin-top: 2rem;
      padding: 0.75rem 1.5rem;
      border-top: 1px solid #000;
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
    }).then((response) => response.json());
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

## Update Storefront Environment Variables

In this step you will replace the hardcoded URL to the Medusa backend with an environment variable, `MEDUSA_BACKEND_URL` in all your storefront files. This will be useful when deploying the storefront in the next steps.

Open up `.env` in  your Sveltekit Storefront and and add the following:

```
MEDUSA_BACKEND_URL=http://localhost:9000
```

Open up all the files in your storefront project folder where the URL to the Medusa backend is hardcoded. Add the following import statement at the top of each file:

```svelte
<script>
import { MEDUSA_BACKEND_URL } from '$env/static/public';
//...
```

Replace every occurence of `http://localhost:9000` with `MEDUSA_BACKEND_URL`. The files are `src/routes/+page.js`, `src/routes/+page.svelte`, and `src/routes/cart/+page.svelte`.

The final result will be as follows:

For `src/routes/+page.js`:

```js
import { MEDUSA_BACKEND_URL } from '$env/static/public'

/** @type {import(./$types').PageLoad} */
export async function load({ fetch }) {
    const res = await fetch(`${MEDUSA_BACKEND_URL}/store/products`);
    const payload = await res.json();
    return { payload };
}
```

The updated `src/routes/+page.svelte` will be as follows:

```svelte

```

## Deployment

In this section we will look into the deployment of the ecommerce app. We will start off with the deployment of the Medusa Server and Admin then look into the deployment of the Sveltekit storefront.


## Deploy Medusa Server

We will deploy the Medusa Backend Server on [Railway](https://railway.app). Railway provides a free trial (no credit card required) that allows you to deploy your Medusa backend along with PostgreSQL and Redis databases. This is useful mainly for development and demo purposes. Sign up for a Railway account and proceed with the following steps.

### Create GitHub Repo

Navigate to the Medusa server directory `my-medusa-store` in your local machine. Duplicate the folder and create a new GitHub repo based on this directory to handle all the config related to the server only.

### Add Railway Configuration File

To avoid errors during the installation process, it's recommended to use `yarn` for installing the dependencies. Alternatively, pass the `--legacy-peer-deps` option to the npm command.

Add in the root of your Medusa server project the file, `railway.toml`, with the content based on the package manager of your choice:


Using `yarn`:
```toml
[build]
builder = "NIXPACKS"

[build.nixpacksPlan.phases.setup]
nixPkgs = ["nodejs", "yarn"]

[build.nixpacksPlan.phases.install]
cmds=["yarn install"]
```

Using `npm`:
```toml
[build]
builder = "NIXPACKS"

[build.nixpacksPlan.phases.setup]
nixPkgs = ["nodejs", "npm"]

[build.nixpacksPlan.phases.install]
cmds=["npm install --legacy-peer-deps"]
```

### Configure Server for Production

Open the `medusa-config.js` file in your new server repo.

Update the following parts to enable caching using Redis.

Uncomment the inner part of the following section:
```js
const modules = {
  /*eventBus:
    resolve: "@medusajs/event-bus-redis",
```

Uncomment the following section as well:
```js
  // Uncomment the following lines to enable REDIS
  // redis_url: REDIS_URL
```

It then becomes:

```js
//...
const modules = {
  eventBus: {
    resolve: "@medusajs/event-bus-redis",
    options: {
      redisUrl: REDIS_URL
    }
  },
  cacheService: {
    resolve: "@medusajs/cache-redis",
    options: {
      redisUrl: REDIS_URL
    }
  },
};

/** @type {import('@medusajs/medusa').ConfigModule["projectConfig"]} */
const projectConfig = {
  jwtSecret: process.env.JWT_SECRET,
  cookieSecret: process.env.COOKIE_SECRET,
  store_cors: STORE_CORS,
  database_url: DATABASE_URL,
  admin_cors: ADMIN_CORS,
  redis_url: REDIS_URL
};
//...
```

Since we are deploying the admin separately, disable the admin plugin's [serve option](https://docs.medusajs.com/admin/configuration#plugin-options).

```js
const plugins = [
  // ...
  {
    resolve: "@medusajs/admin",
    /** @type {import('@medusajs/admin').PluginOptions} */
    options: {
      // only enable `serve` in development
      // you may need to add the NODE_ENV variable
      // manually
      serve: process.env.NODE_ENV === "development",
      // other options...
      autoRebuild: true,
      develop: {
        open: process.env.OPEN_BROWSER !== "false",
      },
    },
  },
]
```

This ensures that the admin isn't built or served in production. You can also change `@medusajs/admin` dependency to be a devdependency in `package.json`.

Also, change the `build` command to remove the command that builds the admin inside the `package.json` file:

```js
"scripts": {
  // ...
  "build": "cross-env npm run clean && npm run build:server",
}
//...
"devDependencies": {
  //...
  "@medusajs/admin": "^7.1.11",
}
```

Commit your changes, and push them to your remote GitHub repository. Once your repository is ready on GitHub, log in to your Railway dashboard.

### Create Project + PostgreSQL Database

If you are using Neon for your PostgreSQL database please skip this step.

On the Railway Dashboard, click on the **New Project** button and choose from the list the **Deploy PostgreSQL** option.

![New PostgreSQL Railway](/new-postgresql-railway.png)

A new database will be created and, after a few seconds, you'll be redirected to the project page where you'll see the newly-created database.

![New Project Page Railway](/railway-new-project.png)

### Create the Redis Database

In the same project view, click on the **Create** button, choose the **Database** option and select **Add Redis**.

![Add Redis to Railway](/railway-add-redis.png)

A new Redis database will be added to the project view in a few seconds. Click on it to open the database sidebar.

![Railway Project with Redis](/railway-redis-added.png)

### Deploy Medusa in Server Mode

In this section, you'll create a Medusa backend instance running in `server` runtime mode.

In the same project view, click on the **Create** button and choose the **GitHub Repo** option. If you still haven't given GitHub permissions to Railway, choose the **Configure GitHub App** option. 

![Add GitHub Repo to Railway Project](/railway-add-github-repo.png)

Choose the repository from the GitHub Repo dropdown.

![GitHub Repo Added to Railway Project](/railway-github-repo-added.png)

### Configure Backend Environment Variables

To configure the environment variables of your Medusa backend, click on the GitHub repo card and choose the **Variables** tab and add the following environment variables:

```
PORT=9000
JWT_SECRET=something
COOKIE_SECRET=something
DATABASE_URL=${{Postgres.DATABASE_URL}}
REDIS_URL=${{Redis.REDIS_URL}}
STORE_CORS=http://localhost:5173
ADMIN_CORS=http://localhost:7001
STRIPE_API_KEY=sk_test_XXXXXXXXXXXX
```

Notice that the values of `DATABASE_URL` and `REDIS_URL` reference the values from the PostgreSQL and Redis databases you created earlier.

For Neon users, insert the URL to your Postgres database as the value to `DATABASE_URL`.

>**NOTE**
>
>The values for `STORE_CORS` and `ADMIN_CORS` will be updated after deploying the admin and storefront.
>Use strong, randomly generated secrets for `JWT_SECRET` and `COOKIE_SECRET`.

![Add Environment Variables to Railway](/railway-add-env-var.png)

### Change Backend's Start Command

The start command is the command used to run the backend. You’ll change it to run any available migrations, then run the Medusa backend. This way if you create your own migrations or update the Medusa backend, it's guaranteed that these migrations run first before the backend starts.

Click on the GitHub repository's card, select the **Settings** tab and scroll down to the **Deploy** section. Click on the **Start** command button and paste the following command:

```bash
medusa migrations run && medusa start
```

![Change Start command in Railway](/railway-change-start.png)


### Add Domain Name

Click on the Medusa server runtime card and select the **Settings** tab and scroll down to the **Networking** section. Either select **Custom Domain** or select **Generate Domain** to generate a random button.

![Add Domain Name to Railway Project](/railway-generate-domain.png)

### Deploy Changes

At the top left of your project's dashboard page, there's a **Deploy** button that deploys all the changes you've made so far.

![Railway Deploy button](/railway-deploy-button.png)

Click on the button to trigger the deployment. The deployment will take a few minutes before it's ready.

### Test the Backend

Once the deployment is finished, you can access the Medusa backend on the custom domain/domain you've generated.

For example, you can open the URL `<YOUR_APP_URL>/store/products` which returns the products available on your backend.

![Railway Deployment Test](/railway-deploy-test.png)

### Health Route

Access `<YOUR_APP_URL>/health` to get health status of your deployed backend.

![Raliway Deployment Health](/railway-deploy-health.png)

### Create Admin User

[Railway’s CLI tool](https://docs.railway.app/develop/cli) allows you to run commands locally, but using environment variables from your projects.

To create an admin user in your deployed backend, run the following commands in the root of your Medusa project.

Install the CLI tool as follows:
```bash
npm i -g @railway/cli
```

Login into your Railway account:
```bash
railway login --browserless
```

This will print a URL and a Pairing Code to the Terminal, which you can use to authenticate your CLI session. Follow the instructions to complete the authentication process.

Associate your Medusa server project, environment and service with your current directory:
```bash
railway link
```

Run this to create an admin
```bash
railway run npx medusa user --email prodadmin@medusa-test.com --password supersecret
```

## Deploy Medusa Admin

We will deploy the Medusa Admin application on Cloudflare Pages.

### Create GitHub Repo

Hosting providers like Cloudflare allow you to deploy your project directly from GitHub. This makes it easier for you to push changes and updates without having to manually trigger the update in the hosting provider.

> **NOTE:**
>
>*Even though you are just deploying the admin, you must include the entire Medusa backend project in the deployed repository. The build process of the admin uses many of the backend's repos.*

Navigate to the Medusa server directory `my-medusa-store` of your project folder in your local machine. Duplicate the directory and create a new GitHub repo based on this directory to handle all the config related to the admin only.

### Configure Build Command

In the `package.json` file of your new Medusa Admin repo, add or change the build script for the admin:

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

Log in to your [Cloudflare Dashboard](https://dash.cloudflare.com/) and select **Workers & Pages**.

![Cloudflare Dashboard](/cloudflare-dashboard.png)

Select **Create application** then **Pages** then **Connect to Git**.

You will be prompted to sign in with your preferred Git provider.

Next, select the GitHub project for your Medusa Admin repo.

Once you have selected a repository, select **Install & Authorize** and **Begin setup**. 

![Select GitHub repo](/select-github-repo-cloudflare.png)

You can then customize your deployment in **Set up builds and deployments**.

Your **project name** will be used to generate your project's hostname.

### Configure Build settings

Set the build command of your deployed project to use the `build:admin` command:

```bash
npm run build:admin
```

Set the output directory of your deployed project to `build`.

Add the environment variable `MEDUSA_ADMIN_BACKEND_URL` and set its value to the URL of your deployed Medusa backend, that is the URL you got in the previous step on Railway.

![Cloudflare Set up builds and deployments](/build-settings-cloudflare.png)

### Save Configuration and Deploy Admin

After you have finished setting your build configuration, select **Save and Deploy**. Your project build logs will output as Cloudflare Pages installs your project dependencies, builds the project, and deploys it to Cloudflare’s global network.

When your project has finished deploying, you will receive a unique URL to view your deployed site.

![Cloudflare Page Admin URL](/cloudflare-admin-deploy-success.png)

### Configure CORS on the Deployed Backend

To send requests from the admin dashboard to the Medusa backend, you must set the `ADMIN_CORS` environment variable on your backend to the admin's URL:

```
ADMIN_CORS=<ADMIN_URL>
```

Visit your Railway dashboard click on the GitHub repo card and choose the **Variables** tab to your deployed Medusa backend web service. Update the `ADMIN_CORS` environment variable. 

Where `<ADMIN_URL>` is the URL of your admin dashboard that you just deployed on Cloudflare. 

![Update Admin CORS in Railway](/railway-update-admin-cors.png)

Then, redeploy your Medusa backend. Once the backend is running again, you can use your admin dashboard.

### Log into Medusa Admin Dashboard

Visit the URL to your Medusa Admin and log in using the user you created in the previous steps.

![Medusa Admin on Cloudflare Pages](/cloudflare-admin-login.png)

If all is working you should be able to log in to your dashboard and see all the orders you made previously when testing the development version of your store.

![Medusa Admin Dashboard Logged In](/medusa-admin-dashboard-loggedin-cloudflare.png)

## Deploy Storefront

We will deploy the Sveltekit storefront on Cloudflare Pages. 

### Create GitHub repo

In your local machine, navigate to the folder, `storefront` containing your Sveltekit storefront source code. Duplicate the directory and create a new GitHub repo based on this directory.

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
  }
};

export default config;
```

Push the changes you made in your repo to GitHub.

### Prepare Deployment via Cloudflare Dashboard

Log in to your [Cloudflare Dashboard](https://dash.cloudflare.com/) and select **Workers & Pages**.

![Cloudflare Dashboard](/cloudflare-dashboard.png)

Select **Create application** then **Pages** then **Connect to Git**.

You will be prompted to sign in with your preferred Git provider.

Next, select the GitHub project for your Sveltekit storefront repo.

Once you have selected a repository, select **Begin setup**. 

![Select GitHub repo](/cloudflare-select-storefront-repo.png)

### Configure Build Settings

You can then customize your deployment in **Set up builds and deployments**.

Your **project name** will be used to generate your project's hostname.

Select the new GitHub repository that you created and, in **Set up builds and deployments**, provide the following information:

|Configuration Option|Value|
|---|---|
|Production branch|`main`|
|Framework preset|`SvelteKit`|
|Build command|`npm run build`|
|Build directory|`.svelte-kit/cloudflare`|

Add the environment variables, `PUBLIC_STRIPE_KEY` for your Stripe Key and `MEDUSA_BACKEND_URL` for your Railway Medusa backend server URL.

![Cloudflare Storefront Build Config](/cloudflare-storefront-build-config-info.png)

Optionally, you can customize the **Project name** field. It defaults to the GitHub repository's name, but it does not need to match. The **Project name** value is assigned as your `*.pages.dev` subdomain.

### Save Configuration and Deploy Storefront

After completing configuration, click the **Save and Deploy** button.

You will see your first deploy pipeline in progress. Pages installs all dependencies and builds the project as specified.

Cloudflare Pages will automatically rebuild your project and deploy it on every new pushed commit.

Additionally, you will have access to [preview deployments](https://developers.cloudflare.com/pages/configuration/preview-deployments/), which repeat the build-and-deploy process for pull requests. With these, you can preview changes to your project with a real URL before deploying them to production.

When your project has finished deploying, you will receive a unique URL to view your deployed site.

![Cloudflare Page Storefront URL](/cloudflare-storefront-deploy-success.png)

### Configure CORS on the Deployed Backend

To send requests from the admin dashboard to the Medusa backend, you must set the `STORE_CORS` environment variable on your backend to the admin's URL:

```
STORE_CORS=<STORE_URL>
```

Visit your Railway dashboard click on the GitHub repo card and choose the **Variables** tab to your deployed Medusa backend web service. Update the `STORE_CORS` environment variable. 

Where `<STORE_URL>` is the URL of your Sveltekit storefront that you just deployed on Cloudflare. 

![Update Store CORS in Railway](/railway-update-store-cors.png)

Then, redeploy your Medusa backend. Once the backend is running again, you can use your storefront.

### Test Storefront

Visit the URL to your storefront in your browser. If all is working your storefront home page should appear with all the products from the Medusa backend.

![Sveltekit Storefront on Cloudflare Pages](/storefront-home-cloudflare.png)

## Conclusion

To conclude, this tutorial has guided you through the process of building a full-stack ecommerce application using Medusa as the backend and Sveltekit for the frontend. You've learned how to:

1. Set up and configure a Medusa server
2. Create a Sveltekit storefront and integrate it with the Medusa backend
3. Implement core ecommerce functionalities like product listing, cart management, and checkout
4. Add Stripe as a payment provider
5. Style your storefront for a better user experience
6. Deploy your Medusa backend on Railway
7. Deploy your Medusa Admin on Cloudflare Pages
8. Deploy your Sveltekit storefront on Cloudflare Pages

By following this tutorial, you've gained valuable experience in creating a modern, composable ecommerce solution. The combination of Medusa's powerful backend capabilities and Sveltekit's efficient frontend framework provides a solid foundation for building scalable and customizable online stores.

This project serves as an excellent starting point for further customization and expansion. You can now add more features, optimize performance, and tailor the user interface to meet specific business requirements. 

Remember that ecommerce development is an ongoing process, and you should continually update and improve your application based on user feedback and changing market needs. With the knowledge gained from this tutorial, you're well-equipped to tackle more complex ecommerce challenges and create innovative online shopping experiences.

