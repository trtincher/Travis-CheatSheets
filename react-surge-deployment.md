## Deploying React

Deploying a React App is a fairly straightforward process and there are several sites we can use to deploy our React app. In previous units you may have used Netlify, Surge or Heroku.  For this deployment we will use Surge. 


### Adding an apiConfig File

In order to create a single point of reference to our api url we could store the url in one place and have it accessed from there. That way if we need to change the url then we can do it in one place instead of having to go into several Components to make that change. 

Let's create an apiConfig file in the **/src** directory? 

src/
```sh
touch apiConfig.jsx
```
There may be times where you are still working on your app locally, prior to deployment and want to configure the React app to use a local backend end server.  In order to do this React can query **window.location.hostname** and if `localhost` is returned then it will use the `development` url but if not then the `production` url 

src/apiConfig.jsx
```js
let apiUrl
const apiUrls = {
  production: 'https://sei-items-api.herokuapp.com/api',
  development: 'http://localhost:3000/api'
}

if (window.location.hostname === 'localhost') {
  apiUrl = apiUrls.development
} else {
  apiUrl = apiUrls.production
}

export default apiUrl
```

Now replace all instances of http://localhost:3000/api in you Items, Item, ItemCreate, and ItemEdit components with `${apiUrl}` and don't forget to import the apiConfig component: `import apiUrl from '../../apiConfig'`


#### Install Surge

```sh
npm install --global surge
```

### Deploying The React App

Now let's package our React App for deployment by running `npm run build`.  This command takes all the React files and transpiles them into static assets

```sh
cd client
npm run build
```

Once this completes you should see the following folder/files:

<img src="https://i.imgur.com/UBWhXmO.png" alt="drawing" width="200"/>

Inside the **static** folder you will find the transpiled assets:

<img src="https://i.imgur.com/KFexMnz.png" alt="drawing" width="200"/>

If your React app is using React Router then you will need to change into the build directory and rename the index.html => 200.html.  

```sh
cd build
mv index.html 200.html
```

Run the surge command and follow the prompts.

```sh
surge
```

The first prompt is to acknowledge the current folder to be deployed and the second will be to accept or provide a unique url for the app:

<img src="https://i.imgur.com/DgePsrG.png" alt="drawing" width="600"/>
