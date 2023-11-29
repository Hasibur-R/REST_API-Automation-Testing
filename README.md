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
```java
   //Send GET request to get post with id=150 (/posts/150)
    @Test(priority = 3)
    public void testGetPostSecond() {
        int Id = 150;
        Response response = PostsEndPoints.getPosts(Id);

        response.then().log().all();
        Assert.assertEquals(response.getStatusCode(), 404);
        Assert.assertFalse(response.getBody().asString().isEmpty(), "Response body is not empty");
    }
```
<div align="center">
  <img src="https://cdn.discordapp.com/attachments/1170655156229128232/1179503544529866823/3.png?ex=657a0547&is=65679047&hm=aa358a4b79c8ebe06f90a0eb27c4e1bd0091c80f9ab2be259e27999dc9ccf328&" height="350">
</div>   

  
4. **Send POST request to create post with userId=1 and random body and random title (/posts).**
   - Expected Result: 
     - Status code is 201.
     - Post information is correct: title, body, userId match data from request, id is present in the response.
```java
//Send POST request to create post with userId=1 and random body and random title (/posts).
    @BeforeClass
    public void setupData() {
        int userId = 1;
        faker = new Faker();
        postPayload = new Posts();

        postPayload.setUserId(userId);
        postPayload.setId(faker.idNumber().hashCode());
        postPayload.setTitle(faker.lorem().sentence());
        String bodyWithBackslashes = String.join("\\" + " ", faker.lorem().words(5));
        postPayload.setBody(bodyWithBackslashes);
    }
    //Send POST request to create post with userId=1 and random body and random title (/posts).
    @Test(priority = 4)
    public void testPostingPosts() {
        int userId = 1;
        Response response = PostsEndPoints.createPosts(postPayload);

        response.then().log().all();
        Assert.assertEquals(response.getStatusCode(), 201);

        int postId = response.jsonPath().getInt("id");
        Assert.assertEquals(response.jsonPath().getInt("userId"), userId, "UserId is not present in the response");
        Assert.assertEquals(response.jsonPath().getString("title"), postPayload.getTitle(), "Title is not present in the response");
        Assert.assertEquals(response.jsonPath().getString("body"), postPayload.getBody(), "Body is not present in the response");
        Assert.assertTrue(postId > 0, "Id is not present in the response");
    }
```
<div align="center">
  <img src="https://cdn.discordapp.com/attachments/1170655156229128232/1179503584698703882/4.png?ex=657a0550&is=65679050&hm=a82b4ac320049be4d55bb30b7b6de805fdcf92b0f4de1a0eeaa7d6db5acb5fed&" height="350">
</div>   

  
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
```java
 @Test(priority = 1)
    public void testGetAllUsers() {
        Response response = UsersEndPoints.getAllUser();

        response.then().log().all();
        Assert.assertEquals(response.getStatusCode(), 200);

        String contentType = response.getContentType();
        Assert.assertTrue(contentType.startsWith("application/json"), "Content type is not JSON");

        // for user id = 5
        List<User> users = response.jsonPath().getList("$", User.class);
        Optional<User> optionalUser = users.stream()
                .filter(user -> user.getId() == 5)
                .findFirst();

        Assert.assertTrue(optionalUser.isPresent(), "User with id=5 not found");
        User expectedUserFromJSON = UserDataConfig.getUserData();
        Assert.assertNotNull(expectedUserFromJSON, "Expected user data is null");
        User userFromReadData = optionalUser.get();
        // Assertions comparing JSON values with expectedUser
        UserAssertions.assertUserData(userFromReadData, expectedUserFromJSON);
    }
```
<div align="center">
  <img src="https://cdn.discordapp.com/attachments/1170655156229128232/1179503651690135622/5.png?ex=657a0560&is=65679060&hm=c603ab3a2d92efc0ced076803e5766464267f260d34d4a06fc04ce127e31c3cb&" height="350">
</div>   


6. **Send GET request to get user with id=5 (/users/5)**
   - Expected Result: 
     - Status code is 200.
     - User data matches with user data in the previous step.
```java
@BeforeClass
    public void setupData() {
        faker = new Faker();
        userPayload = new User();

        userPayload.setId(faker.idNumber().hashCode());
        userPayload.setName(faker.name().name());
        userPayload.setUsername(faker.name().username());
        userPayload.setEmail(faker.internet().emailAddress());
        userPayload.setPhone(faker.phoneNumber().cellPhone());
        userPayload.setWebsite(faker.internet().url());

        User.Address address = new User.Address();
        address.setStreet(faker.address().streetName());
        address.setSuite(faker.address().secondaryAddress());
        address.setCity(faker.address().city());
        address.setZipcode(faker.address().zipCode());

        User.Geo geo = new User.Geo();
        geo.setLat(faker.address().latitude());
        geo.setLng(faker.address().longitude());

        address.setGeo(geo);
        userPayload.setAddress(address);

        User.Company company = new User.Company();
        company.setName(faker.company().name());
        company.setCatchPhrase(faker.lorem().sentence());
        String bs = String.join(" ", faker.lorem().words(5));
        company.setBs(bs);

        userPayload.setCompany(company);
    }
 @Test(priority = 2)
    public void testPostUser() {
        int userId = 5;
        Response response = UsersEndPoints.getUser(userId);

        response.then().log().all();
        Assert.assertEquals(response.getStatusCode(), 200);

        String contentType = response.getContentType();
        Assert.assertTrue(contentType.startsWith("application/json"), "Content type is not JSON");
        User expectedUserFromJSON = UserDataConfig.getUserData();
        Assert.assertNotNull(expectedUserFromJSON, "Expected user data is not null");
        // Assertions comparing JSON values with expectedUser
        User userFromReadData = response.as(User.class);
        UserAssertions.assertUserData(userFromReadData, expectedUserFromJSON);
    }
```
<div align="center">
  <img src="https://cdn.discordapp.com/attachments/1170655156229128232/1179503668458967141/6.png?ex=657a0564&is=65679064&hm=a30bdac57511f479bbe8e63487d6bb9160b92446ce36d0fa193d48dea1593159&" height="350">
</div>   

-Assertion Class for both test 5 and 6:
```java
public class UserAssertions {
    public static void assertUserData(User actualUser, User expectedUser) {
        Assert.assertEquals(actualUser.getName(), expectedUser.getName(), "Name data is not equal");
        Assert.assertEquals(actualUser.getUsername(), expectedUser.getUsername(), "Username data is not equal");
        Assert.assertEquals(actualUser.getEmail(), expectedUser.getEmail(), "Email data is not equal");

        User.Address address = actualUser.getAddress();
        User.Address expectedAddress = expectedUser.getAddress();
        Assert.assertEquals(address.getStreet(), expectedAddress.getStreet(), "Street data is not equal");
        Assert.assertEquals(address.getSuite(), expectedAddress.getSuite(), "Suite data is not equal");
        Assert.assertEquals(address.getCity(), expectedAddress.getCity(), "City data is not equal");
        Assert.assertEquals(address.getZipcode(), expectedAddress.getZipcode(), "Zipcode data is not equal");

        User.Geo geo = address.getGeo();
        User.Geo expectedGeo = expectedAddress.getGeo();
        Assert.assertEquals(geo.getLat(), expectedGeo.getLat(), "Lat data is not equal");
        Assert.assertEquals(geo.getLng(), expectedGeo.getLng(), "Lng data is not equal");

        User.Company company = actualUser.getCompany();
        User.Company expectedCompany = expectedUser.getCompany();
        Assert.assertEquals(company.getName(), expectedCompany.getName(), "Company name data is not equal");
        Assert.assertEquals(company.getCatchPhrase(), expectedCompany.getCatchPhrase(), "CatchPhrase data is not equal");
        Assert.assertEquals(company.getBs(), expectedCompany.getBs(), "Bs data is not equal");
    }
}
```
