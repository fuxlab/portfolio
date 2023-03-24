---
layout: post
title: Dynamic Rendering of React Components inside static HTML pages
permalink: react-components
tags: react javascript
author: Fuxlab
---
Imagine you have some small widgets, written in React, you want to use on a static - non-react-driven - page. The Widgets, accepting input params, could look like:

```javascript
import React from 'react';

export default function YourComponentA({
  param1='',
  param2=''
}){
  return(
    <div>
      <h2>{param1}</h2>
      <p><{param2}/p>
    </div>
  );
}
```

This pattern can help using (many) React Components (Widgets for example) on a static html page:

```javascript
import React from 'react';
import ReactDOM from 'react-dom'

import YourComponentA from './YourComponentA';
import YourComponentB from './YourComponentB';

const registeredComponents = {
  'ComponentA': YourComponentA,
  'ComponentB': YourComponentB,
};

window.renderReactComponent = (elementID: String, reactComponentName: String, reactComponentProps: any) => {
  $( document ).ready(function() {
    var rootElement = document.getElementById(elementID);
    if (rootElement !== null) {
      var comps = [{
        componentName: registeredComponents[reactComponentName],
        props: reactComponentProps
      }]
      ReactDOM.render(
        <>
          {
            comps.map((obj, i) => {
                var ComponentName = obj.componentName;
                return <ComponentName key={`${reactComponentName}-${i}`} {...obj.props} />
            })
          }
        </>,
        rootElement
      );
    } else {
      console.log(`${reactComponentName} could not be rendered into: #${elementID}.`);
    }
  });
}
```

Now you can use many components, to be rendered inside an existing DIV, and pass params individual to them..

```html
<div id="comp-a-1"></div>
<div id="comp-b-1"></div>
<div id="comp-a-2"></div>

<script>
  $( document ).ready(function() {
    window.renderReactComponent('comp-a-1', 'ComponentA', { param1: 'a11', param2: 'a21' });
    window.renderReactComponent('comp-a-2', 'ComponentA', { param1: 'a12', param2: 'a22' });
    window.renderReactComponent('comp-b-1', 'ComponentB', { param1: 'b11', param2: 'b21' });
  });
</script>
```
