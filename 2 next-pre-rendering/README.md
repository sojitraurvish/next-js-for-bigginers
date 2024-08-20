next js pre-rendering

what is pre-rendering 
and why you want in production application

types of pre-rendering

1 static generation you may have to fetch external data
    - without data
    - with data
    
    - incremental static generation
    - dynamic parameters when fetching data

2 server-side rendering
    -data fetching

client-side data fetching 

combining pre-rendering with client-side data fetching


==============================================================

in react page will be served with 
<div id='root'><div>

entire page will be build on client side in side this 
root div you can see it by going to view source in browser

---------------------------------

in next js
<div id='__next'>content present<div>

by default next js pre render every page

but what does pre-render mean
    - Next js generates html for each page in advance
    instead of having it all done by client side javascript

--------------------------------------------

in react 

no pre rendering 

initial load                                Hydration-> react components are initialized and app become interactive (create different dom node and mount them on page that is hydration)
app is not rendered     ->> Js Loads >>>

--------------------------------------------

in next js 

in next js pages are pre-render 
in simple words  html is already generated with necessary data and then send to the browser
and then javascript loads and make page interactive



initial load                                            Hydration-> react components are initialized and app become interactive 
pre-rendered html is displayed    ->> Js Loads >>>    if your page have interactive components like <Link> so they will be active after Js loads

pre-render just mean render in advance of sending ti to  the browser

and pre-rendering is done by default  in a Next js app that way you can see all html element in page source code

-----------------------------------------------------------------

why pre-rendering ?

1 pre-rendering improve performance
    - in react app you need to wait for the javascript to be executed
    - perhaps fetch data form external API and then render UI
    - There is wait time for user when you have to show loader or shimmer
    - with a pre-rendered page, html is already generated and loads faster

2 pre-rendering helps with SEO
    - if you are building a blog or an e-commerce site, seo is a concern
    - with react app, if the search engine hits your page, it only sees a div tag with id equal to root
    - if search engine hits a pre-rendered page though, all content is present in the source code which will help index the page
    - if seo is concern then pre-rendering what you want

pre-render can result in better performance and seo

========================================================================

how pre-rendering

Next js supports two forms of pre-rendering

Static Generation
Server-side rendering

----------------------------------------------------------------

1) static generation 1) without external data fetch

a method of pre-rendering where the html pages are generated at build time

the html with all the data that makes up the content of web page are generated in advance  when you build your application 

this is the recommended method to pre-render pages  whenever possible

page can be build once, cashed  by CDN and served to client almost instantly and this will make huge performance boost for you app

eg Blog pages, e-commerce product pages, documentation and marketing pages

--------------------------------
static generation how? this allows the page to be cashed by cdn and index by a search engine

throughout this video, you have been mentioning that pages are generated at build time. but there is no build for application yet, is there?
Are not we running  the application in development mode?

so there is production and development server

prod server - an optimized build is created once and you deploy that build.
              you do not make changes on the go once it is deployed

dev server - we  should  be able to make changes on code and we want that code to immediately reflect in the browser

for production builds, a page will be pre-rendered once when we run build command

however, in development mode, the page is pre-render for every request you make

------------------------------------------------------
1) static generation 2) with external data fetch

    function UserList({users}){

    }

    async getStaticProps(){
        return {  
            props:{
                users:data //if return here then our component get this props at build time and it should only return object in this format
            }
        }
    }//here it 

    this function getStaticProps runs only server side
    that mean the code you write will not be included in Js bundle that is sent to the browser
    so that you don't need to worry about api key and all.

    it is only used for pre-rendering(server side) not for client side data fetching

Production    getStaticProps will run at build time
Development   during dev getStaticProps runs on every request

==================================================================================
.next folder


when you run npm run build it create build with output like bellow

page  size  (fist load Js)

page refers to page
size refers to size of assets download while navigating to corresponding file at client side

first load  js shared  by all is download for all route in browser url

now let see the folder structure inside .next folder

server and static folder

server folder we have pages folder inside that it will generate all static page as html
pages but for page that fetch external data it will also create user.json file for that for data
and that data is return from getStaticProps
and this folder also contain .js file but they are not send to browser but they are for transformation for page and component in our app 
and this file contain jsx code that will serve to the browser then what about hydration


but what about hydration
so here static folder comes to in picture
in static/chunks we have another pages folder which contains js file which can be send to the
browser busically it contain code to hydrate the page and make it interactive
this static folder also contain file like for css and all other


when you start your app with npm run start 

and when you you run localhost:3000 index.js page will be served from the server/pages/index.html it named as (localhost -or page name) or document
and the second file is from static/chunks/index fdsi3432.js to hydrate and make page interactive
and all css files and web pack file chunks

if assume you also have user.js file and you are in home page so it will not download 
chunks for user page if you donot added link to navigate there on index page

and if you add link to user page on home or index page it pre downloads user.json and user chunk file
so the advantage of this when we navigate to user page it will not fetch anything form server it will build with chunk file (ment for hydration) and with data of json file
and you can check that by clearing the network tab and then route to user page there will not be any request

Note: any <Link/> component in the viewport (initially or through scroll) will be prefetched by default (including the curresponding data) for pages using static generation 
this is how the page load time is faster

but if you directly navigate to user file form url then it will serve user.html file 
from server/pages/user.html (and this page contain all data along with all necessary html) otherwise it build that file with json and chunk file where link is used for user page
---------------------------------------------------------------------------------------
how to statically generate pages with dynamic data

we have

1 postList page

this page has all the posts 
if we have 100 posts on this page and we added <Link> component then
only file will be downloaded for pages that visible on screen and when
you scroll more .json file for that is downloaded


2 post page
    getStaticProps(context){
        const {params}= context
        const {PostId}=params;
    }

    we also have to write this function getStaticPaths() to generate all the pages 
    and this also run at build time

    getStaticPaths(){ with this function we tell next js that for these dynamic path you need to generate pages
        return {
            paths:[
                {
                    params:{postId:1},
                }
            ],
            fallback:false,
        }
    }


Note : if <Link> component does not have child as ancher tag then do this <Link passHref>

---------------------------------------------------------------------


fallback accept three possible values

fallback: false
fallback: true
fallback: 'blocking'

---------------------
1) fallback : false

if fallback is set to false, then any paths not returned by getStaticPaths will return in a 404 page     
like above we just return value 1 for path so if we enter 2 in browser it will return 404 page

2) fallback : true

1) the paths returned from getStaticPaths will be rendered to Html at build time by getStaticProps
2) the paths that have not been generated at build time will not result in 404 page. Instead, Next.js
will serve a 'fallback' version of the page on the first request to such a path
3) In the background, Next.js will statically generate the requested path Html and Json. This includes running getStaticProps.
4) When that's done, the browser receives the JSON for the generated path. This will be used to automatically render the page
with the required props. From the user's perspective, the page will be swapped form the fallback page to the full page.
5) At the same time, Next.js keeps track of the new list of pre-rendered pages. Subsequent requests to the same path will serve
the generated page, just like other pages pre-rendered at build time.


inside the component you can write

if(router.isFallback){
    return <H1>Loading</H1>
} // this is the fallback version of page


when you go to url post/2 that does not exists then it will 
first return fallback version of page in background it generate that
page as well as run that function getStaticProps you can put console log

if post/101 is not found you can return 404 page form getStaticProps

if(!data.id){
    return{
        notFound:true, // this will return 404 page
    }
}

---------------------------------------------------------

fallback:'blocking'

1) the paths returned from getStaticPaths will be rendered to Html at build time by getStaticProps
2)  the paths that have not been generated at build time  will not result in 404 page. Instead, on 
the first request, Next.js will render the page on the server and return the generated Html.
3) when that's done, the browser receives the Html for the generated path. Form the user's 
perspective, it will transition from 'the browser is requesting the page 'the full page is loaded'.
There is no flash of loading/fallback state.
4) At the same time, Next.js keeps track of the new list of pre-rendered pages. subsequent request to the same
page will serve the generated page, just like other pages pre-rendered at build time.

here you can check page loading time in browser console

but when we can you fallback to blacking

on a Ux level, sometimes, people prefer the page to be loaded without
a loading indicator if the wait time is a few milli seconds. This helps avoid the layout shifts.

some crawlers did not support javascript. the loading page would be rendered and then the full page
would be loaded which was causing  a problem.

------------------------------------------------------------
but static generation have some issues

the build time is proportional to the number of pages in the application
a page, once generated, can contain stale data till time you rebuild the application

----------------------------------------------------------------

incremental static regeneration

we have to file like above products/index.js and [productId]

getStaticPaths(){
    return{
        paths:[{parms:{productId:1}}],
        fallback:true
    }
}

so for id 2 and 3 also exists but generated when request is made

here there is two problem if the data is updated in data base
still our app will serve product/1 page with old data

but 2 and 3 page will have new data as it will served on request
but when you request them again and if database data will change 
then you will face same problem
----------------------------------------------------------------
there was a need to update only those pages which needed a change
without having to rebuild the entire app

incremental static regeneration (ISR)

with ISR, Next.js allows you to update static pages after you have build your app

so you can generate individual pages without rebuilding entire site

How ?

in the getStaticProps function, apart from the props key, we can specify a revalidate key

the value for revalidate is the number of seconds after which a page re-generation can occur


getStaticProps(){ you can put console.log and check
    if you request after 10 then it start rebuilding new page but before that it serve the old page
    and when rebuild occur(after 10 second) then and only this function run  
    return:{
        props:{
            products:data
        }
    },
    revalidate:10,// this mean rebuild this page every 10 second
}

so after 10 second when you request that page i start regeneration 
of that page but at that request i will serve you the old page
but after that you request then you will get updated page

------------------------------------------------------------
if content is fequently update then above any approach will not work
and for why i have added one photo see that mine 1 and mine 2

Server side rendering

with SSR Next.js allows you to pre-render a page not at build time but at the request time
so html is generated for every incoming request 
and fetch personalized data like SEO

and how it get us access of incoming request form that
we can get user specific data to fetch his all data 

getServersideProps(context){
    const {params,req,res,query}=context;//query is for query string but it also give route params
    const {category}=params;
}

this function will run for each request and generate new page
 so ues it whenever it is necessary
----------------------------------------
client side data fetching where seo is not needed

you can do this my own with useEffect but next js
recommend to use SWR library for cashing and all

-------------------------------------------
pre-rendering with clien side data fetching
video38

get server side props ma data fetch kari
component ma mokla pachhi useState ma store kari ne page ma use kara
and filter button muku je api call kari new data thi use state ne update karse

but here is some problem after filtering data i want to send that filtered data url my friend it is not possible here
so for that we have shallow routing. with shallow routing you can update url in browser without running gerServersideProps

in shallow routing 

for server side you just need to check that is that filter is exists then file data accordingly or else normal data

for client side when you apply filter or add filter there do this 

router.push('/events?category=sports',undefine,{shallow:true})

use query string when you need key or else add them in params

image optimiztion lazy load mean what ever you can see on dom load only that
add placeholdr iamge to get rid for layout shifts
fir static path add palceholder=blur
for dynamic path use  blurDataUrl=


path alias 59 video









