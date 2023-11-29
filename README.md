# REST_API-Automation-Testing
## REST API (GET/POST)
Site URL: https://jsonplaceholder.typicode.com

## Test Cases
1. **Send GET Request to get all posts (/posts).**
   - Expected Result: 
     - Status code is 200.
     - The list in response body is JSON.
     - Posts are sorted ascending (by id).
```java  
//Send GET Request to get all posts (/posts).
    @Test(priority = 1)
    public void testGetAllPosts() {
        Response response = PostsEndPoints.getAllPosts();

        response.then().log().all();
        Assert.assertEquals(response.getStatusCode(), 200);

        String contentType = response.getContentType();
        Assert.assertTrue(contentType.startsWith("application/json"), "Content type is not JSON");

        List<Integer> postIds = response.jsonPath().getList("id");
        boolean sorted = Ordering.natural().isOrdered(postIds);
        Assert.assertTrue(sorted, "Post are not sorted ascending by id");
    }
```
<div align="center">
  <img src="https://cdn.discordapp.com/attachments/1170655156229128232/1179500922582335498/1.png?ex=657a02d6&is=65678dd6&hm=a5634818f7986ffadcd54a22fc21c0d15282e16cad5057c47ed1b637bb05dd59&" height="350">
</div>

2. **Send GET request to get post with id=99 (/posts/99).**
   - Expected Result: 
     - Status code is 200.
     - Post information is correct: userId is 10, id is 99, title, and body aren't empty.
```java
//Send GET request to get post with id=99 (/posts/99).
    @Test(priority = 2)
    public void testGetPost() {
        int postId = 99;
        Response response = PostsEndPoints.getPosts(postId);

        response.then().log().all();
        Assert.assertEquals(response.getStatusCode(), 200);

        int userId = response.jsonPath().getInt("userId");
        int id = response.jsonPath().getInt("id");
        String title = response.jsonPath().getString("title");
        String body = response.jsonPath().getString("body");

        Assert.assertEquals(userId, 10);
        Assert.assertEquals(id, postId);
        Assert.assertFalse(title.isEmpty(), "Title is empty");
        Assert.assertFalse(body.isEmpty(), "Body is empty");
    }
```
<div align="center">
  <img src="https://cdn.discordapp.com/attachments/1170655156229128232/1179502207956168806/2.png?ex=657a0408&is=65678f08&hm=220382ce7a43e4577cf94f5922a934006744a4cb32e96bf53c837ed93501ba5d&" height="350">
</div>    
  
3. **Send GET request to get post with id=150 (/posts/150).**
   - Expected Result: 
     - Status code is 404.
     - Response body is empty.
  
4. **Send POST request to create post with userId=1 and random body and random title (/posts).**
   - Expected Result: 
     - Status code is 201.
     - Post information is correct: title, body, userId match data from request, id is present in the response.
  
5. **Send GET request to get users (/users).**
   - Expected Result:
     - Status code is 200.
     - The list in the response body is JSON.
     - User Data (id=5):
     ```json
     {
       "name": "Chelsey Dietrich",
       "username": "Kamren",
       "email": "Lucio_Hettinger@annie.ca",
       "address": {
         "street": "Skiles Walks",
         "suite": "Suite 351",
         "city": "Roscoeview",
         "zipcode": "33263",
         "geo": {
           "lat": "-31.8129",
           "lng": "62.5342"
         }
       },
       "phone": "(254)954-1289",
       "website": "demarco.info",
       "company": {
         "name": "Keebler LLC",
         "catchPhrase": "User-centric fault-tolerant solution",
         "bs": "revolutionize end-to-end systems"
       }
     }
     ```

6. **Send GET request to get user with id=5 (/users/5)**
   - Expected Result: 
     - Status code is 200.
     - User data matches with user data in the previous step.
