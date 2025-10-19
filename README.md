# React Hook Form

## Forms:

crucial aspect of any business application.

- from user perspective:
  - Registering
  - Login
  - Making Purchases
  - Scheduling Appointment
- from developers perspective:
  - Handling Form Data
  - Enforcing Validation
  - Providing Visual Feedback

## React Hook Form:

- what?:
  A small library that helps deal with forms in react.
- why?:
  - Manage form data
  - Submit form data
  - Enforce validations
  - Provide visual feedback
  - React Hook Form provides a simple, scalable and performant way to manage even the most complex of forms.

### 1) Managing Form State:

Every form has a few moving parts that keep changing from the time user loads the form to the time they submit it.

**Form State contains:**

- Current value of every field in the form.
- Whether a field has been interacted with.
- Whether a field’s value has changed.
- Whether the form is invalid.
- Whether a field contains errors.
- ….
  ```jsx
  import { useForm } from "react-hook-form";
  import { DevTool } from "@hookform/devtools";

  type FormValues = {
    username: string;
    email: string;
    channel: string;
  };
  export const YouTubeForm = () => {
    const form = useForm<FormValues>();
    const { register, control, handleSubmit, formState } = form;
    // const { name, ref, onChange, onBlur } = register("username");

    return (
      <div>
        <form>
            <label htmlFor="username">Username</label>
            <input
              type="text"
              id="username"
              {...register("username")}
            />
            {/* <input
            type="text"
            id="username"
            name={name}
            onChange={onChange}
            onBlur={onBlur}
          /> */}

            <label htmlFor="email">Email</label>
            <input
              type="email"
              id="email"
              {...register("email")}
            />

            <label htmlFor="channel">Channel</label>
            <input
              type="text"
              id="channel"
              {...register("channel")}
            />

          <button>Submit</button>
        </form>
        <DevTool control={control} />
      </div>
    );
  };
  ```
  either you can control each field by destructure the variables and methods like this
  ```jsx
  const { name, ref, onChange, onBlur } = register("username");
  <input
    type="text"
    id="username"
    name={name}
    onChange={onChange}
    onBlur={onBlur}
  />;
  ```
  or you destructure them inside the input itself like this
  ```jsx
  <input type="text" id="username" {...register("username")} />
  ```

### 2) Form Submission:

We can submit a form using React Hook Form in 3 steps:

1. define the function that should be called when the submit button is pressed. it is a callback function that is passed to handleSubmit function.

   ```jsx
   type FormValues = {
     username: string;
     email: string;
     channel: string;
   };
   export const YouTubeForm = () => {
     const form = useForm<FormValues>();
     const { register, control, handleSubmit, formState } = form;

     const onSubmit = (data: FormValues) => {
       console.log("Form submitted", data);
     };
     return (
     <form onSubmit={handleSubmit(onSubmit)} noValidate></form>)};
   ```

2. From the form object destructure a function called `handleSubmit`.
3. Listen to form `onSubmit` event and assign `handleSubmit` as the handler, to handle submit pass the function as an argument.

### 3) Form Validation:

React Hook Form supports various HTML validation rules:

- required
- minLength & maxLength
- min & max
- Pattern

Note that: `noValidate` this attribute will prevent browser validation , allowing RHF to handle validation of the fields.

Custom Validation:

you can add validation function to prevent the user from entering a specific value

```jsx
validate: (fieldValue) => {
  return (
    fieldValue !== "admin@example.com" || "Entern a different email address"
  );
};

validate: {
  notAdmin: (fieldValue) => {
    return (
      fieldValue !== "admin@example.com" || "Entern a different email address"
    );
  };
}
```

if the user enter same as in the example above, `Entern a different email address` this message will appear.

Error visualize:

you can destructure `errors` from `formstate` object which exist in `form` like this

```jsx
export const YouTubeForm = () => {
  const form = useForm<FormValues>();
  const { register, control, handleSubmit, formState } = form;
  const { errors } = formState;

  return()
 }
```

then you can UI element to make the error appear to the user.

```jsx
<p className="error">{errors.username?.message}</p>
```

based on the fields names that you declare in the form you can access the error message and display it to the user.

## Enhancing Reach Hook Form:

### Default values:

`useForm` hook accepts an object as an argument, inside this object we can specify a key called `defaultValues` which is also an object.

we can add key value pair to this object where is the key is the field name and value is the default we want to set.

```jsx
const form =
  useForm <
  FormValues >
  {
    defaultValues: { username: "", email: "", channel: "" },
  };
```

we don’t need to specify the form values type when invoking `useForm` hook cause typescript will infer the types based on the default values that passed in however for completnes we can leave the type.

### Load Saved Data:

you can populate previously saved data be converting defaultValues to async function

```jsx
const form =
  useForm <
  FormValues >
  {
    defaultValues: async () => {
      const response = await fetch(
        "https://jsonplaceholder.typicode.com/users/1"
      );
      const data = await response.json();
      return {
        username: data.username,
        email: data.email,
        channel: "",
      };
    },
  };
```

### Nested Object:

For more complex forms you may want to group certain data together into a separate object, for this we can use nested objects in RHF.

This can be done in 3 steps:

1. Add your nested object to the types of the `form values` .

   ```jsx
   type FormValues = {
     username: string,
     email: string,
     channel: string,
     social: {
       //Nested Object
       twitter: string,
       facebook: string,
     },
   };
   ```

2. On the default values object add a new key value pair and the value is an object.

   ```jsx
   const form =
     useForm <
     FormValues >
     {
       defaultValues: {
         username: "kklk",
         email: "",
         channel: "",
         social: { twitter: "", facebook: "" }, //Nested Object
       },
     };
   ```

3. Add the jsx of the newly added nested object & make sure to specify the register argument with dot notation on the nested object.

   ```jsx
   <div className="form-control">
     <label htmlFor="twitter">Twitter</label>
     <input type="text" id="twitter" {...register("social.twitter")} />
   </div>

   <div className="form-control">
     <label htmlFor="facebook">Facebook</label>
     <input type="text" id="facebook" {...register("social.facebook")} />
   </div>
   ```

### **Arrays:**

1. Add the array to the types of the `form values`.
2. Add the property to the default values object of `useForm` Hook.
3. Specify the jsx of the newly added property by indexing the array at different positions.

```jsx
type FormValues = {
  username: string;
  email: string;
  channel: string;
  social: {
    twitter: string;
    facebook: string;
  };
  phoneNumbers: string[];
};

export const YouTubeForm = () => {
  const form = useForm<FormValues>({
    defaultValues: {
      username: "kklk",
      email: "",
      channel: "",
      social: { twitter: "", facebook: "" },
      phoneNumbers: ["", ""],
    },
  });

  return (

      <div className="form-control">
        <label htmlFor="primary-phone">Primary phone number</label>
        <input
          type="text"
          id="primary-phone"
          {...register("phoneNumbers.0", {
            required: "This field is required",
          })}
        />
        <p className="error">{errors.phoneNumbers?.[0]?.message}</p>
      </div>

      <div className="form-control">
        <label htmlFor="secondary-phone">Secondary phone number</label>
        <input
          type="text"
          id="secondary-phone"
          {...register("phoneNumbers.1", {
            required: "This field is required",
          })}
        />
        <p className="error">{errors.phoneNumbers?.[1]?.message}</p>
      </div>)}
```

### **Dynamic Fields:**

If you want to collect multiple fields from the user you can use `useFieldArray` hook.
Dynamic Fields give the user the option to add or remove fields based on their needs.

**Note** that `useFieldArray` works only with object values.

Here is how it works:

1. Import `useFieldArray` hook from RHF.
2. Add a new property to form values type and it will be an array of object ,using array of objects cause `useFieldArray` works only with objects values.
3. Add same property to the default values object.
4. Specify the newly added field as array of fields , for that we will invoke `useFieldArray` hook , pass to the hook an object as argument with `name` set to the name of the newly added property and set the `control` option to control returned from the `useform` hook.
5. Add the jsx as in the mentioned example.
   1. Add the div tag and the label to list phone numbers.
   2. Add div tag that will wrap all phone numbers fields , within the div tag map over the fields array which destructured from the `useFieldArray` , then add a div tag will form-control class and set the key to [`field.id`](http://field.id) cause RHF recommends this rather than index , add input text field and register the field like this `{...register(phNumbers.${index}.number, {required: "This field is required",})}`
   3. Add buttons to add and remove phone numbers fields using the append and remove functions destructured from `useFieldArray` , to add within the `onClick` handler `{()=>append({number:""})}` , remove button will be associated with each phone number field and will add remove function to it’s `onClick` handler with specifying the index as an argument.

### Numeric and Date Values:

for the numeric values:

- add the property to the `formValues` type and to the `defaultValues` array.
- add jsx field that accept number as an input and will add `{...register("age", {valueAsNumber: true,required: { value: true, message: "Age is required" },})}` to ensure we are dealing with numeric values RHF provides `valueAsNumber` option on the register function.

for the date values:

- add the property to the `formValues` type and to the `defaultValues` array.
- add jsx field that accept date as an input and will add `{...register("age", {valueAsNumber: true,required: { value: true, message: "Age is required" },})}` to ensure we are dealing with numeric values RHF provides `valueAsNumber` option on the register function.

note that conversions happens before the validation kicks in so you don’t have to be confused as to what data type you have to validate.

### Watch Field Values:

RHF provides a `watch` method with which we can observe one or more form field values , destructure it from form object.

we can pass in fields name as arguments and store the result in a constant , it also accepts an array of fields to watch in form of array of strings.

If you don’t specify any argument the entire form is being watched for changes in value.

When starting typing into the form fields `watch` continuously observes the field value and updates the UI because of this the component re-renders every time the watched value changes , this is fine if the requirement for example is to display a preview of the form field values.

If you want to perform a side effect after watching a value you can use the callback version of the watch method

React Hook Form internally memoizes this function so its reference **doesn’t change** between renders.

```jsx
import { useForm, useFieldArray } from "react-hook-form";
import { DevTool } from "@hookform/devtools";
import { useEffect } from "react";

type FormValues = {
  username: string;
  email: string;
  channel: string;
  social: {
    twitter: string;
    facebook: string;
  };
  phoneNumbers: string[];
  phNumbers: {
    number: string;
  }[];
  age: number;
  dob: Date;
};

let renderCount = 0;

export const YouTubeForm = () => {
  const form = useForm<FormValues>({
    defaultValues: {
      username: "kklk",
      email: "",
      channel: "",
      social: { twitter: "", facebook: "" },
      phoneNumbers: ["", ""],
      phNumbers: [{ number: "" }],
      age: 0,
      dob: new Date(),
    },
  });
  const { register, control, handleSubmit, formState, watch } = form;

  useEffect(() => {
    const subscription = watch((value, { name, type }) => {
      console.log("watch", value, name, type);
    });
    return () => subscription.unsubscribe();
  }, [watch]);

  // const watchedFields = watch(["username", "email"]);

  renderCount++;

  return (
    <div>
      <h1>Youtube Form ({renderCount / 2})</h1>
      {/* <h2>Watched value :{watchedFields}</h2> */}
      )
  }
```

### Get Field Values:

Unlike `watch` function , `getValues` will not trigger re-renders or subscribe to input changes making it better option for getting form values when a user clicks on a button or performs a specific action.

Changing a field value will not trigger the `getValues` method.

You can get a specific field value by passing in the filed name or an array of filed names as argument to the `getValues` method.

```jsx
const handleGetValues = () => {
  console.log("Get Values", getValues(["username", "social"]));
};
```

### Set Field Value:

If you want to update a field value upon an action like clicking a button then we can use `setValue` function destructured from the form object.

It is important to note that calling a `setValue` doesn’t affect the state of the field such as `dirty` `touched` or validation.

If you want to change the field state as if a user is interacting we need to pass in a third argument to the `setValue` method.

```jsx
const handleSetValue = () => {
  setValue("username", "", {
    shouldDirty: true,
    shouldTouch: true,
    shouldValidate: true,
  });
};
```

### **Touched and Dirty States:**

Field Level States:

- `Touched` is a Boolean value that indicates whether the user interacted with the field or not. when you focused on a field and click outside the `Touched` state will be set to true.
- `Dirty` is a Boolean value that indicates whether the user has modified the input or not. when type into the input field you can see the `Dirty` state being set to true. what you should also know is that the comparison is made with the default value so if you were to revert the field back to it’s default value the `Dirty` state is set to false.

Form Level States:

- you can access field level states on the form level by destrucuring them from `formState` object called `touchedFields` and `dirtyFields` .
- They are initially empty when the page loads.
- Although the `formState` object provides `touchedFields` and `dirtyFields` RHF provides `isDirty` which is a derived form state that is easier to work with , you can destructure it from `formState` object.
- `isDirty` represents the state of the form itself and not an individual field , the property comes in handy when you have to enable the form submit button only after the user has filled in data.

Note:

- Focusing a field marks it as "touched", updates `formState`, and triggers a re-render.
- Any time you use `formState` properties in your component, changes to them will cause a re-render.

### Disabling Fields:

Traditionally to disable a form field you would set the `disabled` attribute on an input element

```jsx
<input
  type="text"
  id="username"
  disabled
  {...register("username", {
    required: { value: true, message: "Username is required" },
  })}
/>
```

With RHF disabling a form field is done using disabled option on the register function.

when you disable a field with RHF , the value of the field becomes undefined and the validation is also disabled.

You can also conditionally disable fields using `watch` method.

```jsx
<input
  type="text"
  id="twitter"
  {...register("social.twitter", {
    required: "This field is required",
    disabled: watch("channel") === "",
  })}
/>
```

### **Handle Submission Error:**

```jsx
const onError = (errors: FieldErrors<FormValues>) => {
    console.log("Form errors", errors);
  };

  <form onSubmit={handleSubmit(onSubmit,onError)} noValidate>
```

Why do we need to use `handleSubmit` instead of directly passing `onSubmit` , the reason is that `handleSubmit` accepts a second argument which is the `onError` handler.

```jsx
channel: {type: 'required', message: 'Channel is required', ref: input#channel}
dob: {type: 'required', message: 'Date of birth is required', ref: input#dob}
email: {type: 'pattern', message: 'Invalid email address', ref: input#email}
```

By submitting the form , you can see that the `onError` handler is invoked with the form errors. `errors` will only stores the fields that didn’t pass the validation.

If there is no errors `onError` function will not get invoked.

`onError` handler is the perfect place to provide custom error messages based on the errors object or even send reports to your logging service.

Usage is very simple but it does provide separation of logic for a successful submission and field validation.

### **Disable Form Submission:**

Let’s disable the form submit button if the user has never interacted with the form or if the form contains errors.

```jsx
<button disabled={!isDirty || !isValid}>Submit</button>
```

- disable the submit button when the user hasn’t entered any input , to do so when can set `disabled` attribute of the submit button to `!isDirty` cause this Boolean indicates whether the user has interacted with the form , if the form values are not different from the default values `isDirty` will be false and the submit button will be disabled.
- disable the submit button when the form state is in valid , to track the validity of our RHF we get access to `isValid` form state destructured from the `formState` object. You can use it along with the `isDirty` flag like in the example above.

### Form Submission State:

It is useful for tracking the progress and outcome of form submissions.

Covering 4 properties:

- `isSubmitting` allows you to track whether a form is in the process of being submitted , It is a Boolean value with a `false` default value and is set to `true` when the form is being submitted then back to `false` when the form submission is completed.
- `isSubmitted` allows you track whether a form has been submitted , It is a Boolean with a `false` default value and is set to `true` after the form is submitted and remains `true` until the form is reset.
- `isSubmitSuccessful` this property indicates whether the form was successfully submitted without any runtime errors , It will be `false` if there is a validation error.
- `submitCount` which keeps track of the number of times the form has been submitted , It is incremented by one each time the form us successfully submitted , Initially is zero.

Note: you can use `isSubmitting` flag to disable the submit button while the form is being submitted preventing multiple submission of the same form.

### Reset Form:

To reset form values we can make use of the `reset` method that is returned by the `useForm` hook which we can destrucute from the form object.

This `reset` method allows us to reset the form values to its default values that we initially defined in our form NOT cleared.

Another scenario where we might want to reset form field values is after a successful form submission,

It is recommended to NOT call the `reset` method inside the `onSubmit` function instead we can make use of the `isSubmitSuccessful` flag that is provided by RHF to check if the form submission was successful and then call the `reset` method inside a `useEffect` hook.

Note: `reset` method accepts some optional parameters such as values and options allow us to customize the behavior of the `reset` method.

### **Async Validation:**

Important feature to ensure data integrity and improve user experience in form submissions.

```jsx
<input
    type="email"
    id="email"
    {...register("email", {
      pattern: {
        value:
          /^[a-zA-Z0-9.!#$%&'*+/=?^_`{|}~-]+@[a-zA-Z0-9-]+(?:\.[a-zA-Z0-9-]+)*$/,
        message: "Invalid email address",
      },
      validate: {
        notAdmin: (fieldValue) => {
          return (
            fieldValue !== "admin@example.com" ||
            "Enter a different email address"
          );
        },
        notBlackListed: (fieldValue) => {
          return (
            !fieldValue.endsWith("baddomain.com") ||
            "This domain is not supported"
          );
        },
        emailAvailable: async (fieldValue) => {
          const response = await fetch(
            `https://jsonplaceholder.typicode.com/users?email=${fieldValue}`
          );
          const data = await response.json();
          return data.length == 0 || "Email already exists";
        },
      },
    })}
```

The `emailAvailable` validation will run **when the email field value changes and the form is being validated**. This happens:

- When the user types in the email field (on change or blur).
- When the form is submitted.

Because `emailAvailable` is an **async validation**, React Hook Form will:

- Call it after other synchronous validations (like pattern, `notAdmin`, `notBlackListed`).
- Wait for the result before updating the error state for the email field.

### **Validation Modes:**

By default when you submit the form RHF will check for any validation errors and display them accordingly, this is the default behavior and works well in most cases.

RHF allows you to change the validation mode by passing an option called `mode` to the `useForm` hook.

By default the value of modes is set to `onSubmit`

Others modes:

- `onBlur` RHF will trigger validation when focus and blur out of a form field. once error gets fixed and blur out of the form field again , the error message will disappear.
- `onTouched` RHF will trigger validation on the first blur event and and after that on every change event. this means that if you focus and blur out of a form field you will still see the error message however once you start typing the error message will disappear allowing you to correct your input without seeing the error message constantly.
- `onChange` RHF will trigger validation on every change event which means that as you start typing into a form field RHF will continuously validate the input. be careful when using this mode as it can lead to multiple re-renders and impact the performance .
- `all` RHF will trigger validation on both blur and change events, this means that you will see the error messages when you focus and blur out of a form field as well as when you make changes to the input. this mode provides a combination of both `onBlur` and `onChange` validation modes.

when using RHF it is essential to choose the right validation mode based on your specific requirements.

- if you want validation to occur only when the form is submitted you can stick with the default on submit mode.
- if you want validation to occur when a field is blurred or touched you can use `onBlur` or `onTouched` mode
- if you want validation to occur on every change event you can use `onChange` mode but be mindful of the potential performance impact.
- if you want a combination of both `onBlur` and `onChange` modes you can use the `all` mode

RHF provides different validation modes that give you flexibility in how and when validation occurs in your form by understanding and choosing the right validation mode you can enhance the user experience and ensure that the form data is accurate and valid.

### **Manually Trigger Validations:**

RHF provides us with a method called `trigger` that we can destructure from the `form` object.

This `tigger` method allows us to manually trigger validations for form fields. we can add a button to our form and then call the method when the button is clicked, when click the button we can see that all validations for the form fields are triggered and the validation errors are also displayed.

If you want to trigger validation for a single field, RHF allows us this as well. we can pass in the field names as an argument to the `trigger` method and the validation for only that specific field will be triggered.
