# GUIDE: Building a Serverless Web Form with Vercel and Next.js

Web forms are the primary source of communication between a user and a web application. These are used to send data handled by a web server for processing and storage. A web form has a **client-server** relationship. The form itself is the client, and the server is any storage mechanism that can be used to store, retrieve and send data when needed.

This guide will help you learn and create a serverless web form with Next.js (client) and Vercel (server). But let's start with some basics:

## PART 1: HTML Form

Traditionally, HTML forms are quite popular and built using the `<form>` tag. This further takes up a set of attributes and fields to structure the form for features like text fields, checkboxes, dropdown menus, buttons, radio buttons, etc.

Here's the syntax of a simple HTML form:

```html
<!-- Basic HTML Form -->
<form action="/send-data-here" method="post">
 	<label for="first">First name:</label>
 	<input type="text" id="first" name="first">
 	<label for="last">Last name:</label>
 	<input type="text" id="last" name="last">
 	<button type="submit">Submit</button>
</form>
```

The front-end looks like this:

![html forms](/images/html-forms.jpg)

The HTML `<form>` tag acts as a container for different `<input>` elements like `text` field and submit `button`. Let's study each of these elements:

- `action`: An attribute that specifies where the form data is sent after hitting the Submit button. It's generally a URL (an absolute URL or a relative URL).
- `method`: Specifies the HTTP method, i.e., `GET` or `POST` used to send data while submitting the form.
- `<label>`: An element that defines the label for other form elements. Label aids accessibility, especially for screen readers.
- `<input>`: The form element that is widely used to structure the form fields. It depends significantly on the value of the `type` attribute. Input types can be text, checkbox, email, radio, etc.
- `<button>`: It represents a clickable button that's used to submit the form data.

### Form Validation

A process that checks if the information provided by a user is correct or not. Form validation also ensures that the provided info is in the correct format (e.g., there's an @ in the email field). These are of two types, i.e.,

- **Client-side**: Validation is done in the browser
- **Server-side**: Validation is done on the server

Though both these types are equally important but, for this guide, we'll focus on client-side validation only.

Client-side validation is further categorized as:

- **Built-in**: Uses HTML-based attributes like `required`, `type`, `minLength`, `maxLength`, etc.
- **JavaScript-based**: Validation that's coded with JavaScript.

### Built-in Form Validation Using `required`, `type`, `minLength`, `maxLength`

- `required`: Specifies which fields must be filled before submitting the form.
- `type`: Tells whether the data should be a number, email id, text string, etc.
- `minLength`: Decides the minimum length for the text data string.
- `maxLength`: Decides the maximum length for the text data string.

Let's modify the form w.r.t. to these attributes:

```html
<!-- HTML Form with Built-in Validation -->
<form action="/send-data-here" method="post">
 	<label for="first">First name:</label>
 	<input type="text" id="first" name="first" required minLength="2" maxLength="20">
 	<label for="last">Last name:</label>
 	<input type="text" id="last" name="last" required minLength="2" maxLength="20>
 	<button type="submit">Submit</button>
</form>
```

With these validation checks in place, when a user tries to submit an empty field for Last Name, it gives an error that pops right in the form field.

![form-validation](/images/form-validation.jpg)

### HTML Form Best Practices (Optional)

Apparently, form creation is an easy process. You'll add a few <inputs> with a Submit button, and you're done. This can be true only if you follow these best form practices:

*I might remove this section. I feel this is just going to be the repition of previous concepts.*

So far, this was a quick recap of setting up web forms in HTML.

> To learn more about HTML forms, check out the [official MDN Web Docs](https://developer.mozilla.org/en-US/docs/Learn/Forms).

## PART 2: Serverless Forms with Vercel and Next.js

Let's jump on to the next part, where you'll be recreating these HTML forms in React using Next.js and Vercel.

### Initial Setup

- Create a simple Next.js app. You can use the [create-next-app](https://nextjs.org/docs/getting-started#setup) for a quick start.
- Run `npm run dev` or `yarn dev` to start the development server.
- Visit `http://localhost:3000` to ensure that your app is running successfully.

## Setting up the Serverless Environment

At the beginning of this guide, you learned that web forms have a client-server relationship. So, let's set up the server environment first using Vercel.

Vercel supports out-the-box deployment of [Serverless Functions](https://vercel.com/docs/concepts/functions/serverless-functions), which you can code in your favorite backend languages (Node.js, Go, Python and Ruby). These functions take an HTTP request and provide a response.

Serverless functions perform a significant role in handling tasks like form submission because:

- Serverless functions are event-based, and every time you submit a form, it triggers a new event.
- Offer faster deployments with greater flexibility.
- Reduce architecture and management costs.

### Next.js API Route

Since your Next.js app will be used both as the client and the server, let's create an API Route to send form data. API routes provide a solution to build your API that handles the form submission.

In case you don't know, then Next.js offers a file-based system for routing that's built on the [concept of pages](https://nextjs.org/docs/basic-features/pages). Any file inside the folder `pages/api` is mapped to `/api/*` and will be treated as an API endpoint instead of a page. This API endpoint is going to be server-side only and won't increase your client-side bundle size.

Go to `pages/api`, create a file called `form.js` and paste this code written in Node.js:

```js
export default function handler(req, res) {
	const body = req.body;
	console.log('body: ', body);

	// Both of these are required.
	if (!body.first || !body.last) {
		return res.json({ data: 'First or last name not found' });
	}

	// Found the name.
	res.json({ data: `${body.first} ${body.last}` });
}
```

By doing so, you are creating a form `handler` function that will receive the `req` from the client(the submitted form data). And in return, it'll send a `res` as a JSON file that will have both the first and the last name.

> Moreover, you can also attach this `form.js` endpoint to a database like MongoDB and Google Sheets. This way, your submitted form data will be securely stored for later use.

#### API Deployment

Once this API endpoint is created, let's quickly deploy this forms app to Vercel. The reason behind deploying at this stage is to get a link to a URL that'll be used inside the `action` attribute.

```html
<form action="/send-data-here" method="post">
```

## PART 3: React Forms

So far, Vercel has helped to set up the server-side of forms through serverless functions. Now it's time to configure the client (the form itself) using Next.js. The first step will be extending your knowledge of HTML forms and converting it into React (using JSX).

```js
	<form action="/api/form" method="post">
		<label htmlFor="first">First Name</label>
		<input type="text" id="first" name="first" required />

		<label htmlFor="last">Last Name</label>
		<input type="text" id="last" name="last" required />

		<button type="submit">Submit</button>
	</form>
```

Here's what's changed:

- The form element can now be used inside a React component and rendered using JSX.
- The `for` attribute is changed to `htmlFor`. (Since `for` is a keyword associated with the "for" loop in JavaScript, React elements use `htmlFor` instead.)
- The `action` attribute now has a relative URL, the API endpoint deployed at Vercel.

This completes the basic structure of your React forms. Let's integrate it with your Next.js app.

> Use [this GitHub repo](add link) as a working example. Feel free to clone it and start right away. This demo is built with create-next-app, and you can preview the basic form styling inside `/styles/Home.modules.css` file.

The above GitHub repo will help you set up a working demo to understand the functioning of "Building Forms with Next.js".

Visit the `http://localhost:3000` link to preview the front-end. It should look something like this:

![forms-with-nextjs](/images/forms-with-nextjs.jpg)

This interface will help understand the remaining two parts of this guide, which features handling form submission with and without JavaScript.

## PART 4: Form Submission with JavaScript Disabled

JavaScript brings interactivity to our web applications, and if by chance JavaScript is disabled in our browsers, websites, and web apps, these will stop working immediately.

There are several reasons why users disable JavaScript, e.g., addressing bandwidth constraints, increasing device (phone or laptop) battery life, and even for privacy so they won’t be tracked with analytical scripts. Regardless of the reason, disabling JavaScript will impact site functionality partially, if not completely.

Next.js offers a robust solution for handling form submission in scenarios when JavaScript is disabled.

Go to the `next-forms` working directory. Inside the `/pages` directory, you'll find a file `no-js-form.js`.

![forms-with-js](/images/forms-with-js.jpg)

> **Quick Tip**: In Next.js, a page is a React Component exported from a `.js`, `.jsx`, `.ts`, or `.tsx` file in the pages directory. Each page is associated with a route based on its file name.
>
> Example: If you create `pages/no-js-form`, it will be accessible at `/no-js-form`.

Look at its code:

```js
import Link from 'next/link';
import styles from '../styles/Home.module.css';

export default function Form() {
	return (
		<div className="container">
			<h1 className={styles.title}>
				Form{' '}
				<Link href="/">
					<a>without</a>
				</Link>{' '}
				JavaScript.
			</h1>
			<p className={styles.description}>
				Get started by looking at <code className={styles.code}>pages/no-js-from.js</code>
			</p>
			{/*
			action: The action attribute defines where the data gets sent. Its value must be a valid relative or absolute URL. If this attribute isn't provided, the data will be sent to the URL of the page containing the form — the current page.

			method: The HTTP method to submit the form with. (case insensitive) s*/}
			<form action="/api/form" method="post">
				<label htmlFor="first">First Name</label>
				<input type="text" id="first" name="first" required />

				<label htmlFor="last">Last Name</label>
				<input type="text" id="last" name="last" required />

				<button type="submit">Submit</button>
			</form>
		</div>
	);
}

```

A React component for `Form` has a `<form>` element written in JSX. Considering JavaScript is disabled when you hit the Submit button, an event is triggered that invokes the `action` attribute and looks at the type of `method`.

The `action` attribute defines the URL where the form data is sent. In this case, it's being redirected to the API endpoint, which was created via the serverless functions. This completes the client's request.

On the server-side, this `req` is received by the form handler function that processes the form data and returns a JSON string as its `res`.

*Add video demonstrating this process*.

[form-with-no-js](https://on.awais.dev/jkuE9RJm)

## PART 5: Form Submission with JavaScript Enabled

This is the most important part of the guide, where you'll learn how Next.js handles form submission with JavaScript enabled.

Go to the same working GitHub repo for next-forms. Inside `/pages`, you'll see another file called `js-form.js`. This file powers the following part of your interface.

![forms-with-js](/images/forms-with-js.jpg)

Let's try to understand it's code:

```js
import Link from 'next/link';
import styles from '../styles/Home.module.css';

export default function PageWithJSbasedForm() {
	// Handle the submit event on form submit.
	const handleSubmit = async (event) => {
		// Stop the form from submitting and refreshing the page.
		event.preventDefault();

		// Get data from the form.
		const data = {
			first: event.target.first.value,
			last: event.target.last.value,
		};

		// Send the data to the server in JSON format.
		const JSONdata = JSON.stringify(data);

		// Send the form data to our forms API on Vercel and get a response.
		const response = await fetch('/api/form', {
			// Body of the request is the JSON data we created above.
			body: JSONdata,

			// Tell the server we're sending JSON.
			headers: {
				'Content-Type': 'application/json',
			},
			// The method is POST because we are sending data.
			method: 'POST',
		});

		// Get the response data from server as JSON.
		// If server returns the name submitted, that means the form works.
		const result = await response.json();
		alert(`Is this your full name: ${result.data}`);
	};
	return (
		<div className="container">
			<h1 className={styles.title}>
				Form{' '}
				<Link href="/">
					<a>with</a>
				</Link>{' '}
				JavaScript.
			</h1>

			<p className={styles.description}>
				Get started by looking at <code className={styles.code}>pages/js-from.js</code>
			</p>

			<form onSubmit={handleSubmit}>
				<label htmlFor="first">First Name</label>
				<input type="text" id="first" name="first" required />
				<label htmlFor="last">Last Name</label>
				<input type="text" id="last" name="last" required />

				<button type="submit">Submit</button>
			</form>
		</div>
	);
}
```

It again begins with a React component called `PageWithJSbasedForm` with a `<form>` element written in JSX. What's different here is that when a form is submitted, the `onSubmit` event is triggered hence invoking the `{handleSubmit}` function.

The `handleSubmit` function processes your form data through a series of steps:

- The `event.preventDefault()` stops the `<form>` element from refreshing the entire page.
- The received form data gets temporarily stored in a JavaScript object called `data`.
- The `JSON.stringify(data)` function then sends the form data to the server in JSON format.
- The `header` will also carry the info about the JSON file format and method type `post` to the server.

This information will reside inside the `req` parameter of the form handler function in your API endpoint, which is the `/api/form.js` file.

Once the server is ready with the desired `res`, the `alert` message is popped up on the front-end as follows:

*Add video demonstrating this process*.

[form-with-js](https://on.awais.dev/z8u1ybPR)

## Conclusion

Next.js and Vercel provides you an all-in solution for creating web forms. Use Next.js as the client for your JavaScript-based interaction and use Vercel's powerful serverless functions to handle the server.

For more details go through [Next.js Learn Course](https://nextjs.org/learn/basics/create-nextjs-app).
