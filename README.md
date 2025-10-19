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
