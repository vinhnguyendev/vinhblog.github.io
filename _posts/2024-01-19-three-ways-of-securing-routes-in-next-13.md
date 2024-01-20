# Next.js 13 Secure Routing: Exploring three secure approaches
### **Introduction**
> Securing routes is a crucial aspect of web app development, especially when dealing with user authentication. It entails managing access to specific routes based on the user's authentication status. For example, you wouldn't want an unauthenticated user to access an admin dashboard or view sensitive data.

Before diving into the tutorial, ensure you have the following prerequisites:

1. Basic knowledge of Next.js, Node.js, and JavaScript
2. Familiarity with package managers like NPM or Yarn
3. A preferred code editor (e.g., Visual Studio Code)
4. Node.js and npm (or yarn) installed on your machine

Let's briefly introduce Next.js. Developed by Vercel, Next.js is a React framework designed for building full-stack web applications. It comes with features like file-based system routing, both client-side and server-side rendering, image optimizations, and robust TypeScript support.

To kickstart your Next.js web app project, use the following commands:
```
npx create-next-app@latest
```
After you've installed Next.js successfully, follow the prompts to set up your application. I suggest choosing the app router – it's the go-to option for routing in Next.js 13. In this tutorial, we'll be using Tailwind CSS for styling and adding TypeScript for extra functionality.

### **What is Routing**
Unlike React applications that often depend on third-party packages like react-router-dom for routing, Next.js 13 comes with its own integrated app router. This router offers support for shared layouts, nested routing, loading states, error handling, and more.

In Next.js, routes are organized through a file-based system. Routes are defined by folders containing page.js files, and these folders can also include one or more subfolders.

Implementing protected routes is made straightforward with Next.js. Before delving into the creation of protected routes, let's first grasp the basics of how routes are established.

### **Create Route in Next.js App**
To demonstrate the different ways we can protect routes in Next.js 13, we will create routes: Home, Dashboard, Admin, Settings, and Profile page routes.

Let's perform some code cleanup. Remove all the code found on the "page.tsx" file of the app folder and insert the following code:

```
// app/page.tsx

export default function HomePage(){
  return (
    <main className="text-center h-screen flex justify-center items-center">
      <h1>Home page</h1>
    </main>
  );
};
```
For this tutorial, the home page will be represented by the 'page.tsx' file located within the app folder. To enhance the structure, generate a 'Profile' folder inside the app directory and, within it, craft a 'page.tsx' file. Populate the file with the provided code:

```
export default function ProfilePage(){
  return (
    <main className="text-center h-screen flex justify-center items-center">
      <h1>Profile page</h1>
    </main>
  );
};
```
Repeat this procedure for the 'Dashboard,' 'Admin,' and 'Settings' folders, ensuring each includes a 'page.tsx' file. Once you've completed this, congratulations, you've successfully established your routes.

As mentioned earlier, we'll now delve into various methods to secure your routes.

### **1. Client-Side Route Security**

Client-side route protection proves beneficial in scenarios where you aim to restrict access for unauthenticated users to specific sections of your application on the client side.

This approach is suitable when you prefer a quick and uncomplicated method of securing routes, particularly in situations with minimal security requirements or public websites.

For the sake of simplicity, we won't delve into complex authentication processes in this tutorial. Instead, we'll create a function to store the authentication value.

To facilitate authentication, generate an 'Auth.ts' file within the 'Utils' folder located at the root of your Next.js app. Populate the file with the following code:

```
export const isAuthenticated = false
```

To enhance the security of your Profile route within a client component, we'll employ the useLayoutEffect. Let's explore its functionality by examining the code below:

```
// profile/page.tsx

"use client";

import {isAuthenticated} from '@/Utils/Auth';
import { redirect } from 'next/navigation';
import { useLayoutEffect } from 'react';

export default function ProfilePage(){

useLayoutEffect(() => {
      const isAuth = isAuthenticated;
      if(!isAuth){
        redirect("/")
      }
    }, [])

  return (
    <main className="text-center h-screen flex justify-center items-center">
      <h1>Profile page</h1>
    </main>
  );
};
```
