+++
title = "Frontend: Next.js and TailwindCSS"
outputs = ["Reveal"]
+++

# Frontend Development
Next.js and TailwindCSS

---

{{% section %}}

slides are at [tp.denzeliskandar.com/frontend](https://tp.denzeliskandar.com/frontend)

{{% /section %}}

---

{{% section %}}

## disclaimer

* i am not an expert in nextjs or reactjs
  * there are also some things i dont know
  * i am still learning too
* i might still get things wrong (apologies in advance)
* im trying my best (real)

---

you can usually learn more by doing it yourself, so hopefully this yapping session will be quick and we can move on to exercises

{{% /section %}}

---

{{% section %}}

## what is next.js

Next.js basically wraps React.js

---

## wdym

--- 

## what is next.js

* TypeScript wraps JavaScript -> adds typesafety
* Next.js wraps React.js -> adds extra features

---

### what extra features?

* server components
* performance (cache)
* routing (directory structure)

---

#### directory structure

```
my-very-cool-project/
├── src/
│   ├── app/
│   │   ├── dashboard/
│   │   │   └── page.tsx
│   │   ├── login/
│   │   │   └── page.tsx
│   │   ├── register/
│   │   │   └── page.tsx
│   │   ├── layout.tsx
│   │   ├── page.tsx
│   │   └── globals.css
│   └── components/
├── public/
```

---

and many other stuff

[https://nextjs.org/docs](https://nextjs.org/docs)

{{% /section %}}

---

{{% section %}}

## next.js vs react.js

you can still use everything that React.js has

{{% fragment %}} umm but idk what React.js has {{% /fragment %}}

---

## hooks

{{% fragment %}} what are hooks? {{% /fragment %}}

{{% fragment %}} hooks allow function components to have access to state and other React features {{% /fragment %}}

---

### common hooks

* useState
* useEffect
* useMemo

{{% fragment %}} sound familiar yet? {{% /fragment %}}

---

### no? i gotchu

if yes, uhh just keep listening 🤭

---

### useState

State lets a component “remember” information like user input. For example, a form component can use state to store the input value

```tsx
function RegisterPage() {
    const [name, setName] = useState("");
    // ...
}
```

---

### useEffect

Effects let a component connect to and synchronize with external systems such as performing side effects in function components, such as data fetching, subscriptions, blablabla

```tsx
useEffect(() => {
    const interval = setInterval(() => {
        setSeconds(s => s + 1);
    }, 1000);

    return () => clearInterval(interval);
}, []);
```

---

### useMemo

useMemo lets you cache the result of an expensive calculation (or even inexpensive calculation if u want)

```tsx
const readyToSubmit = useMemo(
    () => validator.isEmail(email) && password,
    [email, password]
);
```

---

### custom hooks

... and many more hooks

{{% fragment %}} [read the docs :)](https://react.dev/learn/reusing-logic-with-custom-hooks#extracting-your-own-custom-hook-from-a-component) {{% /fragment %}}

{{% /section %}}

---

{{% section %}}

## what is tailwindcss

CSS, but better

{{% fragment %}} how is it better? 🤓 {{% /fragment %}}

---

### inline css

```tsx
<div style={{ width: "500px" }}>
```

not this kind of inline css (this is lame)

---

### inline css

```tsx
<div className="flex items-center justify-center min-h-screen">
```

this kind of inline css

---

### breaking it down

* `flex` is basically `display: flex`
* `items-center` is basically `align-items: center`
* `justify-center` is basically `justify-content: center`
* `min-h-screen` is basically `min-height: 100vh`

---

### why is that good 🤨

{{% fragment %}} we no longer have to define style in another file, it is all done in the same file {{% /fragment %}}

---

#### but what about repetition? 

rEpEaTiNg cOdE iS bAd

{{% fragment %}} we can define things like colour hex in `tailwind.config.ts` {{% /fragment %}}

---

### responsiveness

what is it?

{{% fragment %}} website remains usable even in different screen sizes {{% /fragment %}}

---

### okay how

define screen sizes in `tailwind.config.ts`

```ts
theme: {
    screens: {
      'sm': '640px',   // => @media (min-width: 640px) { ... }
      'md': '768px',   // => @media (min-width: 768px) { ... }
      'lg': '1024px',  // => @media (min-width: 1024px) { ... }
      'xl': '1280px',  // => @media (min-width: 1280px) { ... }
      '2xl': '1536px', // => @media (min-width: 1536px) { ... }
    },
}
```

---

### and how do we use it

```tsx
<div class="text-sm md:text-base lg:text-lg">
  This text will be small on mobile, medium on tablets, and large on desktops.
</div>
```

---

### as always

[TailwindCSS Documentation](https://tailwindcss.com/docs/installation)

seriously, even i keep coming back to this website

{{% /section %}}

---

{{% section %}}

## setting up

```
npx create-next-app@latest
```

{{% /section %}}

---

{{% section %}}

## data fetching

* process of retrieving data from a server or API to display in a web application's user interface
* involves making HTTP requests to specific endpoints, then updating the UI with the received data

---

### data fetching in next.js

```tsx
export default async function Page() {
  let data = await fetch('https://api.vercel.app/blog')
  let posts = await data.json()
  return (
    <ul>
      {posts.map((post) => (
        <li key={post.id}>{post.title}</li>
      ))}
    </ul>
  )
}
```

{{% fragment %}} there is no longer a need for **axios** {{% /fragment %}}

---

### async? await?

asynchronous React Server Component

we need to **wait** for the data to be sent back to us

---

### soooo...

now we have the results stored in a JSON which we can use (e.g. displaying the information in a dashboard)

{{% /section %}}

---

{{% section %}}

## API/Routes

what are they?

---

### API

- allow clients to access particular functions or data
- act as gateways for communication between the client and server, defining how requests should be made and what responses to expect.

---

## route examples

{{<figure src="/route-examples.png">}}

---

## API Workflow

Retrieving account details

{{<figure src="/rest-api-flowchart.png" height="450">}}

---

### why is this important

---

### integration of frontend and backend

we need to know what the backend is sending us

{{% fragment %}}... so we know what to display in frontend {{% /fragment %}}

---

### how do we know?

look at the backend code and read it 🤓

... or is there a better way to do it?

---

## rest client extension

{{<figure src="/rest-client.png">}}

---

### rest client extension

* allows us to send requests to the backend server
* it can be localhost or actual server

```
POST https://localhost:3000/login HTTP/1.1
content-type: application/json

{
  "email": "admin@gmail.com",
  "password": "supersecretpassword"
}
```

(totally not my password)

---

### output

```
HTTP/1.1 200 OK
X-Powered-By: Express
Vary: Origin
Access-Control-Allow-Credentials: true
Set-Cookie: some cookie settings... (auth workshop)
Content-Type: application/json; charset=utf-8
Content-Length: 109
ETag: W/"6d-Wc6h/8EfqjpTyRKZN3rJaS6fT2Y"
Date: Tue, 08 Oct 2024 07:35:45 GMT
Connection: keep-alive
keep-alive: timeout=5

{
  "researcherId": "d56195cc-6432-4f47-9e3b-0976ae7a85b6",
  "researcherName": "admin",
  "researcherUsername": "admin"
}
```

---

soo it shows you the various "options" for the request, such as Access-Control-Allow-Credentials, etc etc

but most importantly, you know what the output of the backend is

this will be **very useful** when integrating frontend and backend

{{% /section %}}

---

{{% section %}}

# questions?

{{% /section %}}

---

{{% section %}}

# demo

turning figma into reality

{{% /section %}}