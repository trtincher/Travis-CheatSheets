# React Start Up

```js
<!-- spin up app -->
npx create-react-app name-app-client
cd name-client

<!-- Install Dependencies -->
<!-- React Router -->
yarn add react-router react-router-dom
or
npm i react-router react-router-dom
//inindex.js
import { BrowserRouter as Router } from "react-router-dom";

//...
Note that we are aliasing BrowserRouter as Router here for brevity.

ReactDOM.render(
  <Router>
    <App />
  </Router>,
  document.getElementById("root")
);
//in your component (typically app)
// src/components/App/App.js
d

  return(
    <div>
      <nav>
        <Link to="/">
          <img src="https://en.bitcoin.it/w/images/en/2/29/BC_Logo_.png" alt=""/>
          <h1>Bitcoin prices</h1>
        </Link>
        <Link to="/currencies">Currency List</Link>
      </nav>
      <main>
        <Switch>
          <Route path="/"
            exact
            component={Home}
          />
          <Route path="/currencies"
            component={Currencies}
          />
          <Route
            path="/price/:currency"
            render={(routerProps) => <Price handleClick={handleClick} {...routerProps} price={price} /> }
          />
        </Switch>
      </main>
    </div>
  )


<!-- useState  -->
import React, { useState } from 'react';

funciton App(){
    const [data, setData] = useState({})

    return()
}

<!-- useEffect -->
import React, { useEffect } from 'react';

<!-- useContext -->
import React, { useContext } from 'react';

```