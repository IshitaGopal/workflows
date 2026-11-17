# OAuth

- API Key to acccess Youtubes API 
- API Key : Get public info from Youtube that nanyone can find on Youtube. There may be an option to restric the key to a specific ip adress etc. 
- Buid an API service object 
- OAuth client is used to access private information from an account if the user is properly authenticated 
- 
- 
- If, say, you wanted to view your private or unlisted videos in Python, then you would need to give your script permission to view your YouTube account's data. --> create an OAuth ckkient.

    - Configure User type == External (any user with a gogle account)
    - Application types = Web application -> run a local web server that allows you to loginto your goole account and give your script access to my private youtube data 
    - Select a redirect URI http://localhost:8080/
    - Client ID + Client Secret 
    - There are google authorization libraries 
    - When woring with OAuth you also want to add the scope of the information you are working with "This script will like to view your Youtube account" tell what exactly that you are agreeing to
    - Access + Refresh
    - Once you get authorized with google account, you will get several tokens. 
    - Access token short lived 
    - Refresh token last longer and used to get new access tokens
    - 


```
api_key = 'AIzaSyskuh123duh7890oij'


```