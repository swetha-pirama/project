# Frontend Contact Form — README

## Project Overview

This project is a simple, clean, responsive **Contact Form** built with **React**, plain **HTML**, and **CSS**. The goal is to demonstrate a production-ready frontend contact form that includes form validation, accessibility considerations, visual feedback, and an easy way to connect to a backend API (e.g., a Node/Express contact endpoint). This README explains the project step-by-step and describes the development process, file structure, and deployment tips.

---

## Table of Contents

1. Project goals
2. Prerequisites
3. Project structure
4. Step-by-step setup
5. Component breakdown
6. Styling & responsive approach
7. Form validation logic
8. Accessibility considerations
9. Connecting to a backend
10. Testing the form
11. Deployment
12. Troubleshooting & tips
13. License

---

## 1. Project goals

* Build a reusable React contact form component using plain HTML and CSS.
* Implement client-side validation and friendly error messages.
* Provide accessible form markup and keyboard support.
* Show how to submit data to a backend API and handle success/error states.
* Keep styling modular and responsive so the component fits any layout.

## 2. Prerequisites

* Node.js (v14+ recommended) and npm or yarn installed.
* Basic knowledge of React (functional components, hooks).
* Familiarity with HTML form elements and CSS Flexbox/Grid.

## 3. Project structure (example)

```
contact-form-frontend/
├─ public/
│  └─ index.html
├─ src/
│  ├─ components/
│  │  └─ ContactForm.jsx
│  ├─ styles/
│  │  └─ contact-form.css
│  ├─ utils/
│  │  └─ validators.js
│  ├─ App.jsx
│  ├─ index.jsx
│  └─ serviceWorker.js (optional)
├─ .gitignore
├─ package.json
└─ README.md
```

## 4. Step-by-step setup

1. Create a new React app (Vite, Create React App, or your preferred tooling). Example using Vite:

   ```bash
   npm create vite@latest contact-form-frontend --template react
   cd contact-form-frontend
   npm install
   ```
2. Create the component folder and files: `src/components/ContactForm.jsx` and `src/styles/contact-form.css`.
3. Add a `validators.js` in `src/utils/` to keep validation rules separate and testable.
4. Implement the form UI in `ContactForm.jsx` using semantic HTML: `<form>`, `<label>`, `<input>`, `<textarea>`, and `<button>`.
5. Use React hooks (`useState`, `useEffect`, `useRef`) to manage form state, touched fields, and submission state.
6. Wire the CSS file into the component or import it in `App.jsx` if using global styles.
7. Add client-side validation and inline error messages; prevent submission when invalid.
8. Implement an async function to `fetch()` or `axios.post()` form data to the backend endpoint. Handle loading, success, and error states.
9. Test the UI across responsive breakpoints and browsers.
10. Commit your work and push to GitHub.

## 5. Component breakdown (ContactForm.jsx)

* **State hooks**:

  * `formData` — an object for `name`, `email`, `subject`, `message`.
  * `errors` — object keyed by field with validation messages.
  * `touched` — which inputs the user interacted with (for showing errors only after touch).
  * `isSubmitting` — boolean while waiting for backend response.
  * `submitResult` — success message or error returned from the server.
* **Refs**:

  * `firstInputRef` to autofocus the name field for better UX.
* **Handlers**:

  * `handleChange(e)` — update `formData` and clear field-level errors when changed.
  * `handleBlur(e)` — mark field as touched and validate that single field.
  * `validateAll()` — run full validation before submission.
  * `handleSubmit(e)` — prevent default, validate, set `isSubmitting`, call API, handle response.
* **Effect hooks**:

  * Optionally focus first input on mount; reset form after success with `useEffect`.

## 6. Styling & responsive approach (contact-form.css)

* Use a lightweight, maintainable layout with CSS Grid or Flexbox for the form.
* Keep form width constrained (e.g., `max-width: 620px`) with centered layout and padding.
* Inputs should have clear focus states using `:focus` and `outline` for keyboard users.
* Error states should be visible (red border + small error text) and also announced to screen readers (see Accessibility below).
* For mobile, stack inputs vertically and increase touch target sizes (`min-height` for buttons, `padding` for inputs).
* Use CSS variables for colors and spacing so theming is easy.

## 7. Form validation logic

* Prefer small, testable functions inside `src/utils/validators.js`:

  * `isRequired(value)` — checks presence and non-empty string.
  * `isEmail(value)` — simple regex to validate email format (keep regex moderate; perfect validation is server-side responsibility).
  * `minLength(value, n)` — for message length requirements.
* Validation flow:

  1. On blur, validate single field and set `errors[field]` if invalid.
  2. On submit, run `validateAll()` and if any errors exist, prevent submission and focus the first invalid input.
  3. If valid, call the API and show `submitResult` (success or error).

## 8. Accessibility considerations

* Use semantic HTML: `<label for="id">` + `id` on inputs.
* Ensure labels are visible; if you use placeholders only, keep labels for screen readers.
* Add `aria-invalid="true"` to invalid inputs and `aria-describedby="error-id"` pointing to the error text element.
* For success/failure messages after submission, use an ARIA live region: `<div role="status" aria-live="polite">` to announce results to screen reader users.
* Ensure color contrast for text and error states meets WCAG 2.1 AA guidelines.
* Ensure tab order is natural and all interactive elements are keyboard accessible.

## 9. Connecting to a backend

* Keep the API URL in an environment variable, e.g., `VITE_API_URL` or `REACT_APP_API_URL` depending on toolchain.
* Example `handleSubmit` snippet:

  ```js
  async function handleSubmit(e) {
    e.preventDefault();
    const validation = validateAll();
    if (!validation.isValid) {
      focusFirstError(validation.firstField);
      return;
    }
    setIsSubmitting(true);
    try {
      const res = await fetch(`${import.meta.env.VITE_API_URL}/contact`, {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify(formData),
      });
      if (!res.ok) throw new Error('Network response was not ok');
      setSubmitResult({ success: true, message: 'Message sent successfully!' });
      setFormData(initialFormData);
    } catch (err) {
      setSubmitResult({ success: false, message: err.message || 'Submission failed' });
    } finally {
      setIsSubmitting(false);
    }
  }
  ```
* Always validate again on the server; client-side validation is only for UX.

## 10. Testing the form

* Manual testing checklist:

  * Required fields block submit.
  * Invalid email shows message.
  * Loading spinner or disabled submit button during request.
  * Success message shown and form reset.
  * Error message shown if network or server error.
  * Keyboard navigation works and focus moves to first invalid field.
  * Screen reader announces errors and success messages.
* Automated testing ideas:

  * Unit test validators with Jest.
  * Component tests with React Testing Library to assert validation behavior and API calls (mock fetch/axios).

## 11. Deployment

* Build the project: `npm run build`.
* Host on static hosts: Netlify, Vercel, GitHub Pages, or any static file server.
* If using environment variables, configure them in the host settings (Vercel/Netlify environment settings).
* For SSL and CORS: ensure the backend allows requests from your deployed frontend origin and uses HTTPS.

## 12. Troubleshooting & tips

* If CORS errors occur: confirm backend `Access-Control-Allow-Origin` header or proxy through dev server.
* If form fields don’t update: ensure inputs have `value={formData.field}` and `onChange` correctly wired.
* To avoid double submits: disable the submit button while `isSubmitting` is true and show a spinner.
* Keep error handling user-friendly: do not show raw server errors; map them to simpler messages.
* For production, consider adding rate limiting and CAPTCHA on the backend to reduce spam.

## 13. Example CSS variables (starter)

```css
:root {
  --max-width: 620px;
  --gap: 12px;
  --radius: 10px;
  --color-primary: #0b72ff;
  --color-error: #d9534f;
  --color-bg: #ffffff;
}
```

## 14. Final notes & checklist (before submitting the project)

* [ ] Semantic HTML present and labels connected.
* [ ] Client-side validation implemented and tested.
* [ ] Accessibility checks performed (aria attributes, live region, keyboard nav).
* [ ] Responsive styling verified on mobile/tablet/desktop.
* [ ] API integration works and environment variables configured.
* [ ] README and inline code comments explain key decisions.

---

## License

This project is released under the MIT License — adapt and reuse freely.
