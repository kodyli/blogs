# Showing the Right Data with Out-of-Order Responses in Javascript

<a name="section-1"></a>
## 1. Introduction:
In the dynamic realm of web development, handling asynchronous HTTP requests is a common practice. However, ensuring that web pages remain consistent when responses from these requests arrive out of order is a challenge. In this article, we explore a common issue: when response A, initially sent before response B, returns after B. We'll delve into strategies, including the use of fencing tokens, to avoid the risk of displaying outdated employee data on your web page.

<a name="section-2"></a>
## 2. The Scenario:
Imagine a web application that sends two HTTP requests, labeled A and B, to the same URL, both fetching employee data. Ordinarily, Request A should complete before Request B, but network latency or server load can lead to responses returning out of order. The challenge arises when the delayed response A arrives after Request B has processed, potentially leading to inconsistencies in displayed employee data.

<a name="section-3"></a>
## 3. Challenges and Consequences:
When the delayed response (A) arrives out of order, the web page risks displaying outdated employee data. This can result in inconsistencies in the user experience, such as showing previous employee details, whose name is KK LL, instead of the employee whose name is Kody Li, potentially leading to user confusion or dissatisfaction.
![Challenges and Consequences of Out-of-Order Responses](../img/outOfOrderResponse/issue.png)

<a name="section-4"></a>
## 4. Strategies to Ensure Consistent Employee Data:
Use a fencing tokens. The idea come from the distributed lock.
![Making the lock safe with fencing](https://martin.kleppmann.com/2016/02/fencing-tokens.png)
Let's assume that every time before the request is sent, it will get a `fencing token`, which is a number that increases every time a request is send. We can then require that every time a request need to compare the fencing token before processing the response.

![Solution of Out-of-Order Responses](../img/outOfOrderResponse/solution.png)

<a name="section-5"></a>
## 5. Code

~~~typescript
class FencingTokens{
    private DEFAULT_URL: string = "/";
    private START_TOKEN:number = 1;
    private tokens = new Map<string, number>();
                    
    constructor(defaultUrl: string = ""){
        this.DEFAULT_URL = defaultUrl||this.DEFAULT_URL;
        this.tokens.set(this.DEFAULT_URL, this.START_TOKEN);
    }
    //Default Url
    get():number{
        return this.get(this.DEFAULT_URL);
    }
    
    next():number{
        return this.next(this.DEFAULT_URL);
    }
    rejected(token:number):boolean{
        return this.canUpdate(this.DEFAULT_URL, token);
    }
    //Any Url              
    get(url:string):number{
        return this.tokens.get(url)||this.START_TOKEN;
    }               
    next(url:string):number{
        let oldToken = this.get(url),
            newToken = oldToken++;
        this.tokens.set(url, newToken);
        return newToken;
    }
    rejected(url:string, token:number):boolean{
        return this.get(url) !== token;
    }
}
~~~
~~~html
<button onclick="searchEmployees()">Search</button>
~~~
~~~js
const employeeUrl = "/employees";
const fencingTokens = new FencingTokens(employeeUrl);

function searchEmployees() {
    let fencingToken = fencingTokens.next();
    $.ajax({
        url: employeeUrl,
        data: {firstName:"", lastName:""},
        success: function(res){
            if(!fencingTokens.rejected(fencingToken)){
                //display data to web page.
            }
        }
    });
}
~~~
~~~js
const fencingTokens = new FencingTokens();

function searchEmployees() {
    const url = "/employees";
    let fencingToken = fencingTokens.next(url);
    $.ajax({
        url: url,
        data: {firstName:"", lastName:""},
        success: function(res){
            if(!fencingTokens.rejected(url, fencingToken)){
                //display data to web page.
            }
        }
    });
}

function searchPositions() {
    const url = "/positions";
    let fencingToken = fencingTokens.next(url);
    $.ajax({
        url: url,
        data: {firstName:"", lastName:""},
        success: function(res){
            if(!fencingTokens.rejected(url, fencingToken)){
                //display data to web page.
            }
        }
    });
}
~~~

It can also integrate with Ajax injector of frameworks and libraries, like angularJS, angular, and Jquery Ajax, so we can handle all requestes together instead of one by one.

<a name="section-6"></a>
Read More:

[Showing the Right Data with Out-of-Order Responses in Javascript](https://www.tejusparikh.com/2017/showing-right-data-out-of-order-responses-javascript.html)

[Making the lock safe with fencing](https://martin.kleppmann.com/2016/02/08/how-to-do-distributed-locking.html)