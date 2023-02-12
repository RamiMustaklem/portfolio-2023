---
external: false
draft: false
title: Why I use PNPM over YARN and NPM, and you should too
description: Stands for “Performant NPM”, which was made specifically to save disk space and boost installation speeds.
date: 2023-02-10
author: Rami Mustaklem
duration: 4 min read
---

## Introduction

These days if anyone wants to work or start a JavaScript project, they will probably start by running the `npm init` command or cloning a repository that will have a `package.json` file which consists of the configurations and dependencies for the project.
A JS project needs packages (dependencies), wether on the client side or the server side, in order to build up an application consisting of a lot of functionalities, like **authentication**, routing, connect it to DB, encrypting and others, or to use a framework like **ExpressJS** on the server or **Angular** on the client, and to avoid reinventing the wheel and building everything from scratch.
For that we have package managers like **NPM, Yarn, and PNPM**, that are built for the **Node.js JavaScript runtime environment**, and in this article I am comparing these 3 package managers and presenting the why, to use PNPM over others.

## NPM

If you’re a JS developer, you know NPM, it’s the default package manager that comes with the Node.js runtime environment. It reads and updates your `package.json` file, or adds/removes dependencies using the `npm install` command, installs inside a `node_modules` folder, and tracks each package version installed with the `package-lock.json` file, to keep track of the versions installed, so whenever you clone and install the project on other devices you will be able to replicate and install the same project dependencies even across operating systems.
NPM though, is not the best among package managers due to a few issues.

One of the problems with NPM is the inconsistency of the same package version installed for the same project on different devices. When package A is installed on device A, and the project is installed on device B, NPM does not guarantee that the package version will be installed exactly according to the `package-lock.json` file which was generated on device A, thus creating inconsistent installations and lock files between devices, and tracking the lock file with version control is important for that same goal, to ensure a team of developers share the same project installation across.
Another issue is the duplication of the same package across different projects as a “flat" `node_modules` folder structure which also leads to modules having access to packages they do not depend upon.

## YARN

Yarn is also a Node.js package manager that was developed by Facebook back in 2016. It was created as a collaboration of Facebook, Exponent, Google, and Tilde, mainly to solve the issues NPM was having, consistency, security and performance with large code bases.
Yarn uses a `yarn.lock` file to track package versions and checksum verifications.
One of the main issues that Yarn fixes, is the package inconsistency, and as per their website Yarn guarantees that an install that works now will continue to work the same way in the future.
Performance-wise, it is not in all cases faster than NPM, and for slow or unstable connections, it is very slow.
From a security perspective, Yarn ensures the integrity of packages installed before their code is executed.

## PNPM

> Fast, disk space efficient package manager.

Stands for “Performant NPM”, which was made specifically to save disk space and boost installation speeds.

> PNPM is up to 2x faster than the alternatives.

![PNPM performance stats vs yarn and npm](/images/pnpm-chart.svg)
*[PNPM performance stats vs yarn and npm](https://pnpm.io/benchmarks#lots-of-files)*

Explaining on their website as to why PNPM is this fast, to answer the question, it’s because PNPM does not have blocking stages of installation. Each dependency has its own stages and the next stage starts as soon as possible. To explain, each package has 3 stages, resolving, fetching and writing. Basically each package installation does not block other packages, as soon as a package is fetched over the wire, the installation starts, and doesn’t wait for previous packages to finish before starting.

When you use PNPM to install packages from your `package.json` file, what PNPM does is that it caches all those node_modules to a single content storage on your disk and shares these packages between projects. If 2 projects share the same package version, then these 2 projects use the same installed package on disk, and it is linked to the node_modules directory of your project without the need to duplicate and copy all packages to each project.

![PNPM reusing a lot of already installed packages](/images/pnpm-installations.png)
*A snippet of PNPM reusing a lot of already installed packages*

PNPM has a `non-flat` node_modules structure by default, which also means that if a project installs package A, package A installs package B in order to function, package B is linked inside package A and has access only to package A.

There’s a list of big companies using PNPM, including Microsoft, Prisma, Wix and others.
PNPM uses a yaml file structure to track package versions and checksum verifications, and like npx to npm, pnpm also has pnpx to run packages without downloading them.

## Conclusion

Ever since I started using PNPM, I felt the difference in performance, especially on a slow connection. If you want to use a more disk efficient, performant and reliable package manager, go for PNPM.
You can check out their [website](https://pnpm.io) for more data and info.
