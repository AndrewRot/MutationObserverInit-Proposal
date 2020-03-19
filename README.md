# MutationObserverInit-Proposal
Add additional filtering options to observer other DOM behavior / Elements

There are no filter options for elements types or elements types in subtree nodes.


## Current Difficulties:
Currently, it is not very straight forward to observe changes to specific elements types on the DOM. Instead of passing `MutationObserverInit` the specific element types you want to observe, you must do something like this:
```
let inputs = document.getElementsByTagName('input');
let numInputs = inputs.length;
let observer = new MutationObserver(() => {
  if (inputs.length !== numInputs) {
    foo();
  }
});

observer.observe(document.documentElement, {
  childList: true,
  subtree: true
});
```
This approach monitors the DOM for changes to the number of inputs on the page. This approach is inefficent as it requires you to observe the root element of the page and listenn for changes in _all_ of the subtree nodes. This observer could fire far too many times if the DOM is updating often, causing unnecessary work to be done, when all we care about is mutation to certain element types on the page.

## Similar capabilities
There are similar capabilities already available to monitor only elements with certain attributes, see [`attributeFilter`](https://developer.mozilla.org/en-US/docs/Web/API/MutationObserverInit/attributeFilter).

## Proposed Functionality

I suggest there should be a similar filter but for watching specific changes to element types on the page so that you can watch for changes in the number of inputs (amongst other elements) on the page.

New property: `elementTypeFilter` (optional)
- An array of specific element types to be monitored. If this property isn't included, `MutationObserverInit` will still trigger notifications in the same way as previous to prevent backwards compatibility with existing web functionaly (mainly for efficiency). No default value

Example:
```
let observer = new MutationObserver(mutations) => {
  mutations.forEach(mutation => {
    // immediate handle on all new 'input' elements on the page...
  });
  
});

observer.observe(document.documentElement, {
  childList: true,
  subtree: true,
  elementTypeFilter: ['input']
});
```

This would allow a user to instantly monitor changes to the DOM involving specific elements to the page while increasing efficiency of the observer.
