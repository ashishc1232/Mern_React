

# Job Portal Example (React Router)

## Folder Structure

```
src
 ├── App.jsx
 ├── pages
 │    ├── Jobs.jsx
 │    └── JobDetails.jsx
```

---

# 1. App.jsx (Routing Setup)

```jsx
import { BrowserRouter, Routes, Route } from "react-router-dom";
import Jobs from "./pages/Jobs";
import JobDetails from "./pages/JobDetails";

export default function App() {
  return (
    <BrowserRouter>

      <Routes>

        <Route path="/jobs" element={<Jobs />} />

        <Route path="/jobs/:id" element={<JobDetails />} />

      </Routes>

    </BrowserRouter>
  );
}
```

Explanation

* `/jobs` → shows job listings
* `/jobs/:id` → shows details of a specific job

---

# 2. Jobs.jsx (Jobs List)

```jsx
import { useNavigate } from "react-router-dom";

export default function Jobs() {

  const navigate = useNavigate();

  const jobs = [
    { id: 1, role: "Frontend Developer", company: "Google" },
    { id: 2, role: "Backend Developer", company: "Amazon" },
    { id: 3, role: "Full Stack Developer", company: "Startup Pvt Ltd" }
  ];

  return (
    <div>

      <h1>Job Portal</h1>
      <p>Find your dream job... or at least something better than your current one.</p>

      {jobs.map((job) => (
        <div key={job.id}>

          <h3>{job.role}</h3>
          <p>Company: {job.company}</p>

          <button
            onClick={() => navigate(`/jobs/${job.id}`)}
          >
            View Job
          </button>

        </div>
      ))}

    </div>
  );
}
```

Funny part students enjoy:

```
Find your dream job... or at least something better than your current one.
```

---

# 3. JobDetails.jsx (useParams)

```jsx
import { useParams, useNavigate } from "react-router-dom";

export default function JobDetails() {

  const { id } = useParams();
  const navigate = useNavigate();

  return (
    <div>

      <h1>Job Details</h1>

      <p>Job ID: {id}</p>

      <p>
        Congratulations! You opened the job details.
        Now comes the real challenge: clearing the interview.
      </p>

      <button onClick={() => navigate("/jobs")}>
        Back to Jobs
      </button>

    </div>
  );
}
```

Another light funny line:

```
Now comes the real challenge: clearing the interview.
```

---

# How This Works

### Step 1

User opens

```
/jobs
```

They see job listings.

---

### Step 2

User clicks **View Job**

Example:

```
/jobs/2
```

---

### Step 3

`useParams()` extracts the ID

```js
const { id } = useParams();
```

If URL is:

```
/jobs/2
```

Then:

```
id = 2
```

---

# Concept Summary

| Concept       | Usage                      |
| ------------- | -------------------------- |
| BrowserRouter | Enables routing            |
| Routes        | Container for routes       |
| Route         | Defines path and component |
| useNavigate   | Navigate programmatically  |
| useParams     | Read dynamic route values  |
| :id           | Dynamic route parameter    |




Company Profile
Candidate Profile
Job Details
```

This one usually makes React Router **very clear to students**.
