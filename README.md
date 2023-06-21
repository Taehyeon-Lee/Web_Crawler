CS5700 - SP2023
Project 2: Web Crawler
Tae-Hyeon Lee + Mitchell Neides


High-level approach:

We began by opening a TLS socket to the server and handled logging in the user. This involved 2 GET requests in order
to collect the sessionID and csrftoken cookies, as well as the csrfmiddleware token from the response body. 
Then, using these cookies and credentials, we sent a POST request along with the user login credentials and the 
csrfmiddleware token in the body in order to complete the login process.
Once we established the logged-in session, we then sent one more GET request to the site's homepage.
At this point we could begin our crawler functionality. This involved parsing the HTML response for 'a' tags and
adding all the urls that are internal to the website to a queue. We also maintained a set of previously visited
urls in order to ensure we do not visit urls more than once. After collecting all the unique urls on the current 
page, we also parsed the HTML for 'h2' tags to search for any secret flags that may be in the page body.
If a secret flag was found, we add it to our secret flag list and continue our search.
Once we found 5 secret flags (or once our queue of urls to visit becomes empty), our search is completed and we break 
our search loop. Finally, we close the socket connection and exit our program.


Challenges faced:

Initially, we were not aware that we needed a csrfmiddleware token in order to log in. Upon attempting to log in 
without it, we received an error response from the server telling us to check our cookies. The professor told us in
class that we would need this additional token, and after we were able to find it we thought that it was meant to be 
included with the other cookies (because we were still receiving the same error as before). After some investigation, 
we saw in the browser inspection tools that it was included in the body, and so we restructured our request to match 
this format. However, this error message still persisted. Finally, we realized that we needed to send the content-length 
of the body in our request header, as well as include '&next=' at the end of the body. This fixed our issue and we were 
then able to log in successfully and continue progressing in our search for secret flags.
We also had an issue where we were receiving NoneType responses from the server after about 5 minutes of crawling.
Upon investigation, we noticed that we were sending far more requests than the number of unique urls that we were 
finding. This led us to understand that there was an error in our logic for checking which urls we've already visited. 
Once we fixed this issue our crawler was able to work to completion.


Code testing methods:

We used try/except blocks in each method with specifying exit messages in order to indicate at what point in the code 
errors were occurring (and what the user should check/do differently). Additionally, at each level of development we 
added print statements to confirm that our responses were formatted correctly and also to ensure the responses returned 
back were as expected. Once we got the core functionality of our execution flow coded, we could then only test by 
running the entire code with print statements to ensure that the crawler was advancing through the links that it found 
and successfully finding the secret flags.


Work breakdown:

Separately, we went through the HTTP tutorial and learned about how to implement HTTP. We then worked together to 
investigate the network tab of the inspection tools in order to determine the flow of requests when logging in through 
the browser. We also wrote the GET and POST requests together as this was where we were stuck for the most time.
Once we successfully logged in programmatically, we pseudo-coded together the general execution flow that our program 
would follow. At this point we split the writing of functions between the two of us; Mitchell focused on parsing the 
data needed from the responses and Tae-Hyeon focused on the main execution flow of the loop. Although we each took lead 
for these respective tasks, it still involved a lot of working together and good communication in order to ensure that 
we were on the same page with how we were storing and accessing the data.
