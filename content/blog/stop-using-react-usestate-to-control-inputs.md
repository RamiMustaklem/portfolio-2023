---
external: false
draft: true
title: Stop using React useState to control inputs
description: React useState hook causes its container component to re-render on every change, and that's what happens when an input is controlled by a useState and onChange
date: 2023-02-20
author: Rami Mustaklem
duration: 2 min read
---

![React useState hook](/images/blog/ferenc-almasi-ayjnmG4oUX4-unsplash.jpg)

Each update in a react component causes the whole component to re-render. Imagine you have a form with a few inputs, like name, age and date of birth. When you use the onChange input attribute to track the input value into a component state, the onChange updates the state and thus the whole component re-renders to update the input value, and so on.

Unless strictly necessary you want to watch or track an input value, try to extract the input and its state into its own component in order to only re-render this component itself without the surrounding elements.

If no watching of fields is needed, try using a useRef hook or nothing at all when using inputs. Instead, wrap inputs in its own form element, along with the submit input/button, which also gives the benefit for using the “enter” key to submit and when submitting the form you can extract all input values without re-rendering the component.

When using a form element, there’s no need to custom implement the “enter” key submit, and also adding a required attribute to inputs prevents the form from submitting.

The only thing needed is to prevent the default behavior of the form submission event, and if needed reset the form all at once.

If you have more than a few inputs or forms in your application, I would recommend using a form library like `react-hook-form` in order to implement well optimized forms that do not trigger unnecessary re-renders, with the option to watch input values and many other functionalities.

If a component is a form and it has a few inputs with onChange function, it will re-render the whole component once a change happens.
