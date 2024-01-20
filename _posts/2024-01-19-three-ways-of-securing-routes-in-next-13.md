# Next.js 13 Secure Routing: Exploring three secure approaches
### [Client-Side Route Security](https://github.com/vinhnguyendev/vinhblog.github.io/blob/main/_posts/2024-01-19-three-ways-of-securing-routes-in-next-13.md#1-client-side-route-security)
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
In this code example, we've demonstrated a simple yet effective method for safeguarding routes within a client component.

By integrating the useLayoutEffect hook with an authentication check and the redirect function, we've established a basic route protection mechanism. Unauthenticated users are redirected from the protected route to the 'Home' route, bolstering the security of your application.

Within the Profile component, we employ the useLayoutEffect hook to assess the user's authentication status upon component mounting. The isAuthenticated function is called, and its result is stored in the isAuth variable.

If the user is authenticated, isAuth will be true; otherwise, it will be false. We then verify if the user is not authenticated (i.e., if isAuth is false). In such cases, we utilize the redirect function to reroute them to the homepage or another designated landing page. This effectively bars unauthenticated users from accessing the protected route.

Alternatively, we can refine the code above to safeguard the Profile route by employing a Higher Order Component (HOC), offering a cleaner approach to securing routes on the client side.

To implement a HOC, create a file named 'isAuth' inside the 'components' folder and include the following code:
```
// isAuth.tsx

"use client";
import { isAuthenticated } from "@/Utils/Auth";
import { useEffect } from "react";
import { redirect } from "next/navigation";


export default function isAuth(Component: any) {
  return function IsAuth(props: any) {
    const auth = isAuthenticated;


    useEffect(() => {
      if (!auth) {
        return redirect("/");
      }
    }, []);


    if (!auth) {
      return null;
    }

    return <Component {...props} />;
  };
}
```
The provided code defines a Higher Order Component (isAuth) responsible for verifying the user's authentication status. In cases where the user is not authenticated, it intervenes to prevent the rendering of the protected component and reroutes them to the homepage.

We will employ this Higher Order Component to secure the 'Dashboard' route within our application. The protection is achieved by encapsulating the protected component with the isAuth HOC, as illustrated below:
```
// dashboard/page.tsx

import isAuth from "@/Components/isAuth";

const Dashboard = () => {
  return (
    <main className=" h-screen flex justify-center items-center">
      <h1>Dashboard</h1>
    </main>
  );
};


export default isAuth(Dashboard);
```
The code above seamlessly incorporates the isAuth HOC with the Dashboard component, guaranteeing the protection of the dashboard accessible exclusively to authenticated users. In cases where a user lacks authentication, the isAuth HOC directs them to an alternative route as specified.

Congratulations! We've effectively secured our routes on the client side, employing both useLayoutEffect and Higher Order Components.

## **2. Server-Side Route Security **
Next.js components default to server-side protection, offering an ideal solution for safeguarding server-rendered content. This method proves valuable when ensuring certain routes stay off-limits to unauthenticated users, preventing any potential exposure of sensitive information.

The process of securing routes on the server side is straightforward. With all components in Next.js inherently being server components, protection is seamlessly applied. The practical implementation is illustrated below:
```
// admin/page.tsx

import {isAuthenticated} from '@/Utils/Auth';
import { redirect } from 'next/navigation';


const AdminPage = () => {
    const isAuth = isAuthenticated;


    if(!isAuth) {
        redirect("/");
    }
  return (
    <main className="text-center h-screen flex justify-center items-center">
      <div>
        <h1>Admin Page</h1>
      </div>
    </main>
  );
};


export default Admin;
```
The provided code exemplifies route protection on server components, guaranteeing exclusive access to the admin page for authenticated users. Any attempt by an unauthenticated user to access this route results in a redirection to the homepage.

## **3. Middleware-Based Route Security **
Adding security to your Next.js app's routes is like setting up strong barriers to keep things safe. Think of it as using special guards called middleware. These guards watch who's trying to access different parts of your app and make sure only the right people get in.

It's like having a bouncer at a party – they check invitations and only let in the invited guests. This is super helpful when your app has secret information or different levels of access for users.

Middleware works for both the server and client parts of your app. Today, we'll use it to protect the 'Settings' route. We'll create a file named 'middleware.ts' in the main or 'src' folder and use some code magic to keep the 'Settings' safe. Here's how:

```
//middleware.ts

import { isAuthenticated } from "@/Utils/Auth";
import { NextResponse } from "next/server";
import type { NextRequest } from "next/server";


const protectedRoutes = ["/settings"];


export default function middleware(req: NextRequest) {
  if (!isAuthenticated && protectedRoutes.includes(req.nextUrl.pathname)) {
    const absoluteURL = new URL("/", req.nextUrl.origin);
    return NextResponse.redirect(absoluteURL.toString());
  }
}
```
The code above is like having a guard at the entrance of the 'Settings' page in your Next.js app. It does a couple of things to make sure only the right folks get in.

First, it checks if a user is logged in or not using a special function called 'isAuthenticated.' This function is like a VIP pass – if you have it, you're good to go; otherwise, you're redirected.

Then, it looks at the requested path – where the user wants to go. If it's a protected route (like '/settings'), and the user isn't authenticated, it redirects them to the main entrance.

Think of it as a friendly bouncer who says, 'Sorry, this area is just for invited guests.'

All this magic is done using Next.js tools like NextResponse and NextRequest, which handle requests and responses.

So, with this code, your 'Settings' page stays safe, and only the right people with the right pass can access it.

## ** Summary **
Protecting routes is a vital part of building web apps, especially when it comes to keeping sensitive information safe and controlling access to specific features.

Throughout this tutorial, we've covered three thorough methods to secure routes in Next.js 13, ensuring that only authorized users can access certain parts. By applying these techniques, you can strengthen your app's security and provide a better user experience.

Armed with the insights from this tutorial, you now have the tools to safeguard your Next.js app's routes, making it more secure and user-friendly. Whether you go for client-side, server-side, or middleware-based protection, the end goal is consistent: protect your app's integrity and your users' data.
