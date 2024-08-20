how to create next js application

install node js first

npx create-next-app <name that you want to take>

yarn dev -> setup development server
=========================================
package.json

dependencies
react and react-dom to build ui
and next that is that is the framework build on top of react

devDependencies
eslint help you lint your code it will highlight warnings and errors during development
eslint-config-next 

scripts
dev -> run our app in dev mode with hot reloading 
build -> which compiles our app and prepare it for production deployment
start -> this start compiled app in production mode
lint -> lint all files in application 

===================================================

package-lock.json 

consistent installation of packages in your app
==================================================

next.config.js

reactStrictMode 
dev feature for highlighting potential problem in dev
identify and save life cycle and legacy api uses

====================================================

.eslintrc is configuration file for eslint

====================================================
.next

that is generated when we run either dev or build script
and form this folder our next.js app is served from
and it is gitignore

===================================================
styles

contains some styles
which contains global styles as well file specific styles

====================================================
public

holds all public file 
and as like react it does not hold any index.js file because or next app will be served form 

====================================================

how next js app is getting executed 

when we run npm run dev -> the execution is transfeared to _app.js which contain MyApp component

then MyApp component automatically get ({Component,pageProps}) which then return as part of jsx

and when we run our app that component refers to the component present inside the page/index.js file

that how our application run s


=======================================================

next js routing 

it uses file system base routing 

1) route with pages
2) Nested routes
3) Dynamic routes
4) Catch-all routes
5) Navigate from the Ui
6) Programmatically navigate b/w pages
------------------------------------------------------
1) when you add any file in pages folder it will be available as route

to implement these two routes
localhost:3000/about
localhost:3000/profile

you can directly add both file with above name into page folder and you can access them

-----------------------------------------------------------
2) Nested routes

now i want to create routes like 
localhost:3000/blog
localhost:3000/blog/first
localhost:3000/blog/second

for first route you can directly create /blog.js file in pages folder

but for the second 
you need to create blog folder inside pages folder and 
inside that create two files 

/blog/first.js
/blog/second.js

for this localhost:3000/blog you can put separate file in pages folder
but i would be better if we group them you we can do that in blog
folder you can create blog/index.js file that will serve when you do
not add seconde parameter like first and second 
-> so when you just write localhost:3000/blog the index file will be served
------------------------------------------------------------------

3) Dynamic routes

now we want to create routes like

localhost:3000/product
localhost:3000/product/id
localhost:3000/product/1
localhost:3000/product/2

what of there is 100 product 
create 100 routes is not the right solution 

so inside pages folder create product folder

/pages/product/[productId].js this is how you will create file
so it will accept all dynamic arguments after product
like this -> localhost:3000/product/1

but now how to extract that id from parameter and access onto the page
so in productId file import hook form this package
import {useRouter} from 'next/router'

const router=useRouter()
const productId=router.query.productId;
this is how you can access productId

Note: productId can be any string not just number it can be /product/sweater (browser)
it will also render

but here we might also need the file with same name /product/sweater.js

now the question is that when you hit localhost:3000/product/sweater

then which page will render sweater.js or [productId].js

it will give first priority to render sweater.js if it is not present then 
that [productId].js page will render.

---------------------------------------------------------------

4 ) Nested dynamic routes

some time it may happen that we have more than 1 dynamic segments in routes like bellow

to create routes like 
localhost:3000/product/1
localhost:3000/product/1/review/1

create /product folder first then inside that
crete /product/productId folder inside that
    first create index.js file that will manage to localhost:3000/product/1 route and now you can delete [productId].js file from /pages/product folder because it is inside [productId] folder
    and even you can access productId in side bellow review folder file as well so you also can create 1 single file for review and you can access id of both product and review

    so for review 
    create file inside
    /pages/product/[productId]/review/[reviewId].js
    and to access ids
        const router=useRouter()
        const {productId,reviewId}=router.query;


    now you can reach to this router /pages/product/[productId]/review/[reviewId].js

--------------------------------------------------------------------------

5) Catch all routes

to create routes like this

localhost:3000/docs/feature1/concept1
localhost:3000/docs/feature1/concept2
localhost:3000/docs/feature2/concept1
localhost:3000/docs/feature2/concept2

to create route like this have to create separate file for each route

what if there is 20 different parameter then it will like 20*20 =200files
so it is unrealistic

you can crete nested folder but here we have another level of nesting but we do not want that  

to achieve this there is way in next js

/pages/docs/[...params].js 

now this file can match all routes which starts from 

if if type

localhost:3000/docs/feature1
localhost:3000/docs/feature1/concept1
localhost:3000/docs/feature1/concept1/example1 

for above all routes it will be cached to 1 single file 

that is the purpose of that

but here to access parameter we get params array

const router=useRouter();
const {params = []}= router.query

here params refers to file name

but initially params is undefine then in second console log it will come
due to pre rendering feature

if(params.length===2){
    params[0] params[1]
}
if(params.length===1){
    params[0]
}
else{
    return <h1>docs home page</h1>
}

here i can pass filter's values
like i want to pass min and max values for home price
home/100/10000

optional catch all routes
if i navigate to localhost:3000/docs it will give 404 page but we will see it leter on

----------------------------------------------------------------------

6) Link component

    to perform client side navigation next js provide Link component

e.g
    import Link from 'next/link'

    <Link href='/blog' replace>
    <a>Blog</a>
    </link>

 replace prop will replace current history and when you go back with 
 browser back button it will go to home page
 
 if you use plan anchor tag it will create new server request and 
 any client state you are maintaining will be erased

 navigating programmatically

 const {useRouter} form 'next/router'
 router.push('/product')
 router.replace('/product') // to replace previous history

===================================================================

custom 404 page

if you navigate to route that is not exists i will goes to 
default 404 page

and to customize this page inside page folder you can create your
own custom 404 page /pages/404.js

if you have you own 404 page then that will be served

===================================================================
