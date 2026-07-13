<h1>
  <span class="headline">Openhouse</span>
  <span class="subhead">Formatting Currency</span>
</h1>

**Learning objective:** By the end of this lesson, students will be able to apply the `Intl.NumberFormat()` method in JavaScript for accurate currency formatting.

## Formatting currency in JavaScript

Formatting currency in JavaScript is not too difficult. However, there are some things that we need to consider when working with currency in JavaScript. Let's take a look at some of these considerations:

- Use a library.
- Location considerations.
- Data consistency.
- Client-side vs server-side formatting.

### Use a library

It is recommended to use a library that can format currency for you. This is because we don't want to recreate the wheel and there are some edge cases that we might not consider when formatting currency. We are in luck because there is already a built-in library that we can use, [`Int.NumberFormat()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Intl/NumberFormat).

Let's see how we can use this library to format currency in JavaScript:

```js
const formatter = new Intl.NumberFormat('en-US', {
  style: 'currency',
  currency: 'USD',
  minimumFractionDigits: 2,
});

formatter.format(2500); /* $2,500.00 */
```

### Location considerations

When formatting currency, we need to consider the location of the user. Different countries use different currency symbols and different formats for displaying currency. For example, in the United States, we use the dollar sign `$` to represent our currency. However, in Canada, they use the dollar sign `$` as well. To differentiate between the two, Canada uses the dollar sign `$` and the letters `CAD` to represent their currency.

We can use the `Intl.NumberFormat()` method to format our currency based on the location of the user. Let's take a look at how we can do this:

```js
const formatter = new Intl.NumberFormat('en-US', {
  style: 'currency',
  currency: 'CAD',
  minimumFractionDigits: 2,
});

formatter.format(500); /* CA$500.00 */
```

We can see a full list of values for the `currency` values by running this code:

```js
Intl.supportedValuesOf('currency').forEach((currency) => {
  console.log(currency);
});
```

### Data consistency

When working with currency, we want to make sure that we are consistent with how we store our data. For example, we want to make sure that we are storing our data as a number and not a string. This is because we want to be able to perform mathematical operations on our data. If we store our data as a string, we will not be able to perform mathematical operations on it.

#### JavaScript numbers

JavaScript numbers under the hood are represented as 64-bit floating-point numbers. This means that JavaScript numbers are not integers. This can cause some issues when working with currency. For example, if we try to add two numbers together, we might get a result that is not what we expect. Let's take a look at an example:

```js
const num1 = 0.1;
const num2 = 0.2;

num1 + num2; /* 0.30000000000000004 */
```

We can see that the result is not `0.3` as we would expect. This is because of how JavaScript stores numbers. To get around this, we can use the built-in `Number()` method to convert our numbers to integers. Let's take a look at how we can do this:

```js
const num1 = 0.1;
const num2 = 0.2;

Number((num1 + num2).toFixed(2)); /* 0.3 */
```

We can see that we get the result we expect. We can also see that we are using the built-in `toFixed()` method to round our number to two decimal places. This is because we want to make sure that we are working with two decimal places when working with currency.

### Client-side vs server-side formatting

When working with currency, we need to consider where we are formatting our currency. We can format our currency on the client-side or the server-side. Let's take a look at the pros and cons of each:

**Client-side formatting:**

- Pros:
  - Less load on the server.
  - Faster response times.
  - Less data transfer.
- Cons:
  - Inconsistent formatting.
  - Security concerns.

**Server-side formatting:**

- Pros:
  - Consistent formatting.
  - More secure.
- Cons:
  - More load on the server.
  - Slower response times.
  - More data transfer.

Client-side formatting is great because it is faster and uses less data transfer. However, it is not as secure as server-side formatting. This is because the client can manipulate the data before it is sent to the server. This can cause some security concerns.

Server-side formatting is great because it is more secure and consistent. However, it is slower and uses more data transfer.
