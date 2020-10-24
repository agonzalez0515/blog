---
title: TIL Render if array is not empty
date: '2020-04-01'
tags:
  - react
  - javascript
  - today i learned
---

TLDR Today I learned that to render a react component if an array is not empty you can do:

```jsx
{
  !!someArray.length && <Component prop={someArray} />
}
```

---

I have a component called `Errors` that I only want to render if `errors` state array is not empty.

```jsx
function Errors({ errors }) {
  return errors.map(error => (
    <p className="error" key={error}>
      {error}
    </p>
  ))
}
```

At first I tried

```jsx
function myComponent() {
  const [errors, setErrors] = useState([])
 // ... code ... 
  return (
    <div>
      {errors.length && <Errors errors={errors} />}
      <RegisterForm
        handleChange={handleChange}
        handleSubmit={handleSubmit}
        formValues={input}
      />
    </div>
  );
};
```

which sort of worked. I'm TDDing this so my test checking that no errors were visible passed! But then just as a sanity checked I looked at what was rendered. And I saw a random `0` right above my form!



To fix this I changed it to `{errors.length > 0 && <Errors errors={errors} />}`. My test still passes and the 0 is gone. But in the spirit of Red, Green, Refactor, I wanted to find something a little less verbose. **After some Googling, I found (was reminded) about the double bang `!!` that coarces into boolean.** I liked this much better and tests still pass.
