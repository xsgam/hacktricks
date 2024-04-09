# use dbox api to upload/download/delete files



## 1. Use the refresh token to get a new access token. 

- login your dropbox account in browser, create an app to get <App key><App secret>

  ```
  App key     xxxxxxxxxxxxxxx
  App secret  yyyyyyyyyyyyyyy
  ```

  

- Auth URL for code flow with offline token access type in browser

  ```
  https://www.dropbox.com/oauth2/authorize?client_id=<APP_KEY>&token_access_type=offline&response_type=code
  ```

  after authorize,  AUTHORIZATION_CODE will Generated

  ```
  <AUTHORIZATION_CODE>
  ```

- refresh token request

  ```shell
  curl https://api.dropbox.com/oauth2/token \
      -d code=<AUTHORIZATION_CODE> \
      -d grant_type=authorization_code \
      -d redirect_uri=<REDIRECT_URI> \
      -d client_id=<APP_KEY> \
      -d client_secret=<APP_SECRET>
  ```

  then get   Example: short-lived "offline" access token

  ```
  {
    "access_token": "219dZi6gxYPVnYPrpvISRSf9lxKWJzYLjtMPH-d9fo_0gXex7X37VIvpty4-G8f4-WX45AcEPfRnJJDwzv-",
    "expires_in": 14400,
    "token_type": "bearer",
    "scope": "account_info.read files.content.read files.content.write files.metadata.read",
    "refresh_token": "nBiM85CZNpNutK4ngsXHsqW4iGz9tisb3JyjGqikMJIYbd",
    "account_id": "dbid:AAH4f99T0taONIb-GRopQngc",
    "uid": "12345"
  }
  ```

  

- Use the refresh token to get a new access token. 

  ```shell
  curl https://api.dropbox.com/oauth2/token \
      -d grant_type=refresh_token \
      -d refresh_token=<REFRESH_TOKEN> \
      -d client_id=<APP_KEY> \
      -d client_secret=<APP_SECRET>
  ```

  the get:

  ```
  {
    "access_token": "sxYPVnYPrpvISRSf9lxKWJzYLjtMPH-d9fo_0gXex7X37VIvpty4-G8f4-WX45AcEPfRnJJDwzv-",
    "expires_in": 14400,
    "token_type": "bearer"
  }
  ```

  
## use access token to list/upload/download file

- list file
    ```shell
    @echo off
    cd %~dp0
    
    set token=<access_token>
    
    rem list folder
    rem ====================
    
    curl -X POST https://api.dropboxapi.com/2/files/list_folder --header "Authorization: Bearer %to%"    --header "Content-Type: application/json"     --data "{\"include_deleted\":false,\"include_has_explicit_shared_members\":false,\"include_media_info\":false,\"include_mounted_folders\":true,\"include_non_downloadable_files\":true,\"path\":\"/Github/\",\"recursive\":false}"
    
    pause 
    ```

- upload file

  ```shell
  @echo off
  cd %~dp0
  
  set ufile=1.txt
  set token=<access_token>
  
  rem upload file /Github
  ======================================
  curl -X POST https://content.dropboxapi.com/2/files/upload --header "Authorization: Bearer %token%" --header "Dropbox-API-Arg: {\"path\": \"/Github/%ufile%\",\"mode\": \"add\",\"autorename\": true,\"mute\": false,\"strict_conflict\": false}" --header "Content-Type: application/octet-stream" --data-binary @%ufile%
  
  pause
  ```

  

- download file

  ```shell
  @echo off
  cd %~dp0
  
  rem download file
  rem ====================
  
  set token=<access_token>
  set gitname=/github/1.txt
  set outname=out_1.txt
  
  curl -X POST https://content.dropboxapi.com/2/files/download --header "Authorization: Bearer %token%"   --header "Dropbox-API-Arg: {\"path\":\"%gitname%\"}" --output %outname%
  
  pause
  ```

  

- delete file

  ```shell
  @echo off
  cd %~dp0
  
  rem delete
  rem ====================
  set token=<access_token>
  set ufile=/Github/1.txt
  
  curl -X POST https://api.dropboxapi.com/2/files/delete_v2 --header "Authorization: Bearer %to%"    --header "Content-Type: application/json"   --data "{\"path\":\"%ufile%\"}"
  
  pause
  ```

  


