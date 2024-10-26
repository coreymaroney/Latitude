# Take home assignment for Latitude
Chris and Eugene, thank you for taking the time to check out my take-home! I completed this project as a web deployment using Svelte, and I've provided two methods of viewing the project. 

1. This app is deployed live on https://coreymaroney.github.io/Latitude/

2. If you'd prefer to run this locally on your system, follow the standard procedure below.
## Running this project locally

Clone the project to your local system (this is using HTTPS)
### `git clone https://github.com/coreymaroney/Latitude.git`
## Navigate into  the project repository using
### `cd Latitude`
## Install the dependencies

### `npm install`

## Run the web deployment
### `npm run dev`

# Details & Takeaways from the project
To start, I found Svelte straight forward to jump into! While working on the app, I experimented with multiple language features, so if you notice in some cases there's a more straight forward JavaScript approach -- I've tried to take the "Svelte-y" approach in the spirit of this assignment. That said, after reviewing the instructions I've opted to capture mouse clicks in the video. In order to adhere to the directions, I completed this by passing the video element's onmousemove event to a function _handleCursor_, then offsetting the x,y of the cursor so that it's coordinates are scoped to the local element instead of the viewport. Each click is captured in the onclick event handling function _handleVideoClick_ where I increment each click and push that data into an array of objects. The Svelte features I used are binding, the $lib alias, and the #each, #key, and #if template syntax. Additionally, I've leaned into Latitude's branding and styled my button and page based on that. 

Overall I'm pleased with the development experience of Svelte and enjoyed using their documentation throughout the project. With more time, there are a few improvements I would certainly make, for one I wrote this without abstracting code to chase an optimal folder structure, in favor of keeping it monolithic and easily readable. Additonally I would more strictly type the app, optimize viewing for mobile and pursure further optimizations. Thank you again for your time during our interview process!
