# Redux

Problem: how two non parent-child components would communicate?
![](https://css-tricks.com/wp-content/uploads/2016/03/redux-article-3-01.svg)

Resolution: Redux
![](https://css-tricks.com/wp-content/uploads/2016/03/redux-article-3-02.svg)

![](https://css-tricks.com/wp-content/uploads/2016/03/redux-article-3-03.svg)


## [The Ugly Side Of Redux](https://codeburst.io/the-ugly-side-of-redux-6591fde68200)

1. The global store is a singleton, but a component may have many instances. so when you reuse the component, it will have a problem, just like the problem between the screen componnet and error component using a service to exchange data in Galaxy Web.

> When we reuse the screen component and the error component in a popup, the screen component always sends data to a error panel in the main window. The global store is like the service we used, so it will have the same problem.
> In angularJS, it have a `require` attribute in the directive, so the screen component and the error component get the same parent, and exchange data via the parent componnet.