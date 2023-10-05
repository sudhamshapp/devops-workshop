## Publish jar file onto Jfrog Artifactory
1. Create Artifactory account  
2. Generate an access token  with username  (username must be your email id)
3. Add username and password under jenkins credentials   
4. Install Artifactory plugin  
5. Update Jenkinsfile with jar publish stage   
    ```sh 
         def registry = 'https://valaxy01.jfrog.io'
             stage("Jar Publish") {
            steps {
                script {
                        echo '<--------------- Jar Publish Started --------------->'
                         def server = Artifactory.newServer url:registry+"/artifactory" ,  credentialsId:"artifactory_token"
                         def properties = "buildid=${env.BUILD_ID},commitid=${GIT_COMMIT}";
                         def uploadSpec = """{
                              "files": [
                                {
                                  "pattern": "jarstaging/(*)",
                                  "target": "libs-release-local/{1}",
                                  "flat": "false",
                                  "props" : "${properties}",
                                  "exclusions": [ "*.sha1", "*.md5"]
                                }
                             ]
                         }"""
                         def buildInfo = server.upload(uploadSpec)
                         buildInfo.env.collect()
                         server.publishBuildInfo(buildInfo)
                         echo '<--------------- Jar Publish Ended --------------->'  
                
                }
            }   
        }   
    ```

Check-point: 
Ensure below are update
1. your jfrog account details in place of `https://valaxy01.jfrog.io` in the defination of registry `def registry = 'https://valaxy01.jfrog.io'`
2. Credentials id in the place of `jfrogforjenkins` in the  `def server = Artifactory.newServer url:registry+"/artifactory" ,  credentialsId:"artifactory_token"`
3. Maven repository name in the place of `libs-release-local` in the `"target": "ttrend-libs-release-local/{1}",`

eyJ2ZXIiOiIyIiwidHlwIjoiSldUIiwiYWxnIjoiUlMyNTYiLCJraWQiOiI4cGpRSU5MaWN6ZHIxbTJoX09BNnNkUm5VblZTVWk3eVdjN3liTW9YWk5RIn0.eyJzdWIiOiJqZmFjQDAxaGJwMTkwcnI0Y3NhMXkyNnY0a3ExN2R4L3VzZXJzL2t1YmVybmV0ZXMyOTdAZ21haWwuY29tIiwic2NwIjoiYXBwbGllZC1wZXJtaXNzaW9ucy9hZG1pbiIsImF1ZCI6IipAKiIsImlzcyI6ImpmZmVAMDFoYnAxOTBycjRjc2ExeTI2djRrcTE3ZHgiLCJpYXQiOjE2OTY0OTUxMTksImp0aSI6IjU4NDRjYWNlLTRjZWYtNGE4Mi04Y2E1LWM0N2U1NDI1ZWVjOSJ9.g2sT7r-c83PKU1OIa1lVzvhccgq__aE81i8p-i6X8viMr6rMpe-9vbbzxCkLQHhQ1_LBeSWmCxwxYOOHvQ5TVmPv4rJnafRHxaxvyPh4Hk_UH4f-fFPvK1w4pcLEbKwYorY2JwasA11WHw0WvHKiSm7LHjyVv7-pZIsox-aASIDaia3mHNVlcugFocyKrmaCM9lgo0BU4LIwixE7tKs_GynNDsPKKzXM3YFEnenQrbD-IxDQyK7of-cUjEazBKa5cj6L0DFKSPx0GMcQZV_W_nAk9wdhV5oSlK0TcJm0xaCQ1PXzKP2EkS4GjQMl_GPUqtftNVzCqqrwjtGiIXzPnw