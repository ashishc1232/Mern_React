## 1. Basic Controlled Input (Text Input)

This is the most important concept: **controlled components**.

```jsx
import { useState } from "react";

function SimpleForm() {
  const [name, setName] = useState("");

  const handleSubmit = (e) => {
    e.preventDefault();
    console.log("Name:", name);
  };

  return (
    <form onSubmit={handleSubmit}>
      <input
        type="text"
        value={name}
        onChange={(e) => setName(e.target.value)}
        placeholder="Enter your name"
      />
      <button type="submit">Submit</button>
    </form>
  );
}

export default SimpleForm;
```

What you learn:

* How React controls input value
* onChange updates state
* Preventing page reload using `e.preventDefault()`

---

## 2. Multiple Inputs Using One State Object

Used in real projects like login, signup, checkout forms.

```jsx
import { useState } from "react";

function LoginForm() {
  const [formData, setFormData] = useState({
    email: "",
    password: "",
  });

  const handleChange = (e) => {
    setFormData({
      ...formData,
      [e.target.name]: e.target.value,
    });
  };

  const handleSubmit = (e) => {
    e.preventDefault();
    console.log(formData);
  };

  return (
    <form onSubmit={handleSubmit}>
      <input
        type="email"
        name="email"
        value={formData.email}
        onChange={handleChange}
        placeholder="Email"
      />

      <input
        type="password"
        name="password"
        value={formData.password}
        onChange={handleChange}
        placeholder="Password"
      />

      <button type="submit">Login</button>
    </form>
  );
}

export default LoginForm;
```

What you learn:

* Handling multiple inputs
* Dynamic key update using `[name]`
* Real-world form structure

---

## 3. Checkbox Handling

Checkbox values are boolean.

```jsx
import { useState } from "react";

function TermsForm() {
  const [accepted, setAccepted] = useState(false);

  const handleSubmit = (e) => {
    e.preventDefault();
    console.log("Accepted:", accepted);
  };

  return (
    <form onSubmit={handleSubmit}>
      <label>
        <input
          type="checkbox"
          checked={accepted}
          onChange={(e) => setAccepted(e.target.checked)}
        />
        Accept terms and conditions
      </label>

      <button type="submit">Submit</button>
    </form>
  );
}

export default TermsForm;
```

What you learn:

* `checked` instead of `value`
* Boolean state handling

---

## 4. Radio Buttons Example

Used when user must select **only one option**.

```jsx
import { useState } from "react";

function GenderForm() {
  const [gender, setGender] = useState("");

  const handleSubmit = (e) => {
    e.preventDefault();
    console.log("Gender:", gender);
  };

  return (
    <form onSubmit={handleSubmit}>
      <label>
        <input
          type="radio"
          value="male"
          checked={gender === "male"}
          onChange={(e) => setGender(e.target.value)}
        />
        Male
      </label>

      <label>
        <input
          type="radio"
          value="female"
          checked={gender === "female"}
          onChange={(e) => setGender(e.target.value)}
        />
        Female
      </label>

      <button type="submit">Submit</button>
    </form>
  );
}

export default GenderForm;
```

What you learn:

* Handling radio groups
* Conditional checked logic

---

## 5. Select Dropdown Example

```jsx
import { useState } from "react";

function CityForm() {
  const [city, setCity] = useState("");

  const handleSubmit = (e) => {
    e.preventDefault();
    console.log("City:", city);
  };

  return (
    <form onSubmit={handleSubmit}>
      <select value={city} onChange={(e) => setCity(e.target.value)}>
        <option value="">Select City</option>
        <option value="Mumbai">Mumbai</option>
        <option value="Pune">Pune</option>
        <option value="Delhi">Delhi</option>
      </select>

      <button type="submit">Submit</button>
    </form>
  );
}

export default CityForm;
```

What you learn:

* Controlled select input
* Default empty option

---

## 6. Basic Form Validation

```jsx
import { useState } from "react";

function ValidationForm() {
  const [email, setEmail] = useState("");
  const [error, setError] = useState("");

  const handleSubmit = (e) => {
    e.preventDefault();

    if (!email.includes("@")) {
      setError("Invalid email");
      return;
    }

    setError("");
    console.log("Email:", email);
  };

  return (
    <form onSubmit={handleSubmit}>
      <input
        type="text"
        value={email}
        onChange={(e) => setEmail(e.target.value)}
        placeholder="Enter email"
      />

      {error && <p>{error}</p>}

      <button type="submit">Submit</button>
    </form>
  );
}

export default ValidationForm;
```

What you learn:

* Manual validation logic
* Error state handling

---

## 7. Full Real-World Signup Form

```jsx
import { useState } from "react";

function SignupForm() {
  const [data, setData] = useState({
    name: "",
    email: "",
    password: "",
  });

  const handleChange = (e) => {
    setData({
      ...data,
      [e.target.name]: e.target.value,
    });
  };

  const handleSubmit = (e) => {
    e.preventDefault();
    console.log(data);
  };

  return (
    <form onSubmit={handleSubmit}>
      <input
        name="name"
        value={data.name}
        onChange={handleChange}
        placeholder="Name"
      />

      <input
        name="email"
        value={data.email}
        onChange={handleChange}
        placeholder="Email"
      />

      <input
        type="password"
        name="password"
        value={data.password}
        onChange={handleChange}
        placeholder="Password"
      />

      <button type="submit">Sign Up</button>
    </form>
  );
}

export default SignupForm;
```






Tell me what you want next.
