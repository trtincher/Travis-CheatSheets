# Express Set Up
1. mkdir
2. touch server.js
3. npm init -y
4. npm i express 
5. npm i dotenv
6. touch .env
7. touch .gitignore
    ```
    echo
    /node_modules
    .DS_Store
    .env >>.gitignore
    ```
8. in .env 
    ```
    PORT=3000
    ```
9. in server.js
    ```
    const port = process.env.PORT || 3000
    ```
10. in package.json, scripts
    ```
    "dev"="nodemon server.js"
    ```
11. in server.js
    ```
    require('dotenv).config
    const express = require('express')
    const app = express()
    ```