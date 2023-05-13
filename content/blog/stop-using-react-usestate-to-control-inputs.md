---
external: false
title: Stop using React useState to control inputs
description: React useState hook causes its container component to re-render on every change, and that's what happens when an input is controlled by a useState and onChange
date: 2023-02-20
author: Rami Mustaklem
duration: 2 min read
---

![React useState hook](/images/blog/ferenc-almasi-ayjnmG4oUX4-unsplash.jpg)

## Why

Each update in a react component causes the whole component to re-render. Imagine you have a form with a few inputs, like name, age and date of birth. When you use the onChange input attribute to track the input value into a component state, the onChange updates the state and thus the whole component re-renders to update the input value, and so on.

Unless strictly necessary you want to watch or track an input value, try to extract the input and its state into its own component in order to only re-render this component itself without the surrounding elements.

## Solution/s

If no watching of fields is needed, try using a useRef hook or nothing at all when using inputs. Instead, wrap inputs in its own form element, along with the submit input/button, which also gives the benefit for using the “enter” key to submit and when submitting the form you can extract all input values without re-rendering the component.

When using a form element, there’s no need to custom implement the “enter” key submit, and also adding a required attribute to inputs prevents the form from submitting.

The only thing needed is to prevent the default behavior of the form submission event, and if needed reset the form all at once.

If you have more than a few inputs or forms in your application, I would recommend using a form library like `react-hook-form` in order to implement well optimized forms that do not trigger unnecessary re-renders, with the option to watch input values and many other functionalities.

### Conclusion

If a component is a form and it has a few inputs with onChange function, it will re-render the whole component once a change happens.

### Examples

#### Using Form onSubmit function without any state or ref

```tsx
export default function FormFields() {
  const onFormSubmitHandler = useCallback(
    (e: SyntheticEvent<HTMLFormElement, SubmitEvent>) => {
      e.preventDefault();
      const target = e.target as FormElements<MyFields>;
      console.log("target.field1.value", target.field1.value);
      console.log("target.field2.value", target.field2.value);
      console.log("target.field3.value", target.field3.value);
      target.reset();
    },
    []
  );

  return (
    <form onSubmit={onFormSubmitHandler}>
      <label htmlFor="field-1" key="field-1">
        <p>Field 1</p>
        <input type="text" id="field-1" name="field1" required />
      </label>

      <label htmlFor="field-2" key="field-2">
        <p>Field 2</p>
        <input type="text" id="field-2" name="field2" required />
      </label>

      <label htmlFor="field-3" key="field-3">
        <p>Field 3</p>
        <input type="text" id="field-3" name="field3" required />
      </label>

      <div>
        {/* Default role for the button inside a form is submit */}
        {/* Either this or use <input type="submit" /> */}
        <button>Submit</button>
      </div>
    </form>
  );
}
```

#### Using React Hook Form library

```tsx
export default function HookForm() {
  const { register, handleSubmit, watch } = useForm<MyFields>({});

  const onSubmit = (data: MyFields) => console.log(data);

  // Watching a field returns realtime field updates
  console.log(watch("firstName"));

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <label htmlFor="firstName" key="firstName">
        <p>Field 1</p>
        <input type="text" {...register("firstName", { required: true })} />
      </label>

      <label htmlFor="lastName" key="lastName">
        <p>Field 2</p>
        <input type="text" {...register("lastName", { required: true })} />
      </label>

      <label htmlFor="username" key="username">
        <p>Field 3</p>
        <input type="text" {...register("username", { required: true })} />
      </label>

      <div>
        <button>Submit</button>
      </div>
    </form>
  );
}
```

----------------------------------

*Do you still use npm? Read my take [here](/blog/why-i-use-pnpm-over-yarn-and-npm) on why I use pnpm over npm and yarn.*
