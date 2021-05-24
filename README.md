# flask-blog-users
Prototype for a Flask blog implementing users sessions, authentication and RESTful routes.

This project builds upon the other Flask blog prototype, but this time we add users to our blog, so that anyone can sign up
and post comments on our blog. In order to add such feature we need to add authentication to our blog.

This blog is a Flask app, uses Flask-Bootstrap and Jinja templating on the front-end as well as an SQLite database on the back-end.
The app is run on a Flask server where the routes of the blog are implemented.

This project was tackled in 5 steps:

1. Register new users
2. Login registered users
3. Protect routes
4. Establish relationships between tables in our database
5. Allow any user to add comments to the blog's posts

## Register new users

We create a form in the "forms.py" file called RegisterForm (as a class inheriting from FlaskForm), import it inside the main.py file.
In the "/register" route, we create an instance of that form and pass it inside the render_template function.
On the register page, we use Flask-Bootstrap to render the form we passed onto the screen.

![image](https://user-images.githubusercontent.com/55893421/119278416-31177500-bbf3-11eb-932f-6783dfad0fb1.png)

![image](https://user-images.githubusercontent.com/55893421/119278440-4d1b1680-bbf3-11eb-9743-571de36cde87.png)

![image](https://user-images.githubusercontent.com/55893421/119278429-4391ae80-bbf3-11eb-8922-2e2cd7828d4d.png)

After that, we must implement the "/register" route by hashing the password provided by the new user and create a new entry for the User table
on our database using the email provided by the user, the hashed password and the name provided by the user.

We then add and commit this entry into the database.

![image](https://user-images.githubusercontent.com/55893421/119278573-23aeba80-bbf4-11eb-8981-ea0b80901251.png)

This will add our new user and his/her credentials into our SQLite database.


## Login registered users

Now that we are able to reigster new users, we must set up the login route in order to allow these users to have access to their accounts,
using their credentials that theu used upon registering. We use the Flask module Flask-Login (https://flask-login.readthedocs.io/en/latest/) to achieve that.
We already did this in previous projects, but the process is the same, first we define a user_loader() function

![image](https://user-images.githubusercontent.com/55893421/119285466-17385b00-bc10-11eb-8300-b0d2b2b56227.png)

Next, we need to implement a User class for login sessions. This is doen by pass the UserMixin class as a parameter (inheritance) in the User class we made 
for the database:

![image](https://user-images.githubusercontent.com/55893421/119285593-5ff01400-bc10-11eb-8253-32f13b55c700.png)

We can then start implementing our login route and create a WTForm (LoginForm) that will be rendered on the screen:

![image](https://user-images.githubusercontent.com/55893421/119285800-e3116a00-bc10-11eb-9558-652f828816a9.png)


![image](https://user-images.githubusercontent.com/55893421/119285707-a5144600-bc10-11eb-96cd-5d8171ad6367.png)

![image](https://user-images.githubusercontent.com/55893421/119285755-c117e780-bc10-11eb-9922-95ff104d0689.png)


We can also add some modifications to our register route.

If the user successfully register, they are taken back to the home page and are logged in with Flask-Login.

![image](https://user-images.githubusercontent.com/55893421/119285962-46030100-bc11-11eb-843d-c30a2bf02222.png)

If a user is trying to register with an email that already exists in the database then they should be redirected to the "/login" route 
and a flash message used to tell them to log in with that email instead.

![image](https://user-images.githubusercontent.com/55893421/119286000-616e0c00-bc11-11eb-9dbb-9e0840b9fe8b.png)
 
To flash messages on the login page:

![image](https://user-images.githubusercontent.com/55893421/119286198-cfb2ce80-bc11-11eb-8225-902285004a0d.png)

In the login route , if a user's email does not exist in the database or if their password does not match the one stored using check_password(),
then they should be redirected back to /login and a flash message should let them know what they issue was and ask them to try again.

![image](https://user-images.githubusercontent.com/55893421/119286369-37691980-bc12-11eb-8451-deaee12f4b1e.png)
![image](https://user-images.githubusercontent.com/55893421/119286459-62ec0400-bc12-11eb-9ae1-77f5341d484b.png)

Now our login route is complete.

We should also update the navbar so that when a user is not logged in it shows the "Login" and "Register" links, but after authentication, it shows
only the "Logout" links. This is possible by using Jinja syntax and using the Flask Login proxy "current_user" which is available in every template.

We write the following code inside the header.html file:

![image](https://user-images.githubusercontent.com/55893421/119286677-ec9bd180-bc12-11eb-9d35-66f37d6ed64a.png)

Lastly for this step is to code up the "/logout" route so that when the user clicks on the LOG OUT button, it logs them out and takes them back to the home page.

 ![image](https://user-images.githubusercontent.com/55893421/119286816-3389c700-bc13-11eb-8166-6085f21392e4.png)
 
 
 ## Protect routes
 
 The third step is to protect some of the routes we just made. In the case of this blog, the first registered user (the user with the id of 1) will be the admin.
 Only the admin will be able to create new blog posts, edit posts and delete posts.
 
 We can therefore make sure that only the admin user can see the "Create New Post" and "Edit Post" and Delete buttons.
 
 ![image](https://user-images.githubusercontent.com/55893421/119409285-10afef00-bcb5-11eb-81cf-4f7abc1fed83.png)

We do this by using conditionals in Jinja syntax in "index.html"

![image](https://user-images.githubusercontent.com/55893421/119409397-40f78d80-bcb5-11eb-8af6-fef0696386df.png)

The same in "post.html"

But just because we remove those buttons from the view of other users doesn't mean that they no longer have access to the routes tied to these buttons.
In fact, the user could very well try to manually access these routes (the /edit-post or /new-post or /delete routes). Usually we could simply use a Python decorator
above the route function of each route, but login_required will still allow non-admin logged in users to manually access those routes. We therefore have to implement our 
own function decorator called "@admin_only".

![image](https://user-images.githubusercontent.com/55893421/119409988-1bb74f00-bcb6-11eb-8641-88415d9fa05d.png)

This function decorator is in fact a function that will wrap around a route function defined below and and check if the current user is not authenticated or the current user's
id is not 1. If one of these two condition is true, trying to reach a route defined by a route function wrapped by such decorator will result in a 403 HTTP error.
Otherwise, the function wrapped by the decorator is returned.

We then use the decorator above route function for adding a new post, editing a post and deleting a post.

![image](https://user-images.githubusercontent.com/55893421/119410227-81a3d680-bcb6-11eb-981f-2c7a34b2bddb.png)

![image](https://user-images.githubusercontent.com/55893421/119410308-a4ce8600-bcb6-11eb-84ef-e8a96b531939.png)

![image](https://user-images.githubusercontent.com/55893421/119410353-b44dcf00-bcb6-11eb-8269-828d4f0f73d6.png)


## Establish relationships between tables in our database

The fourth step is to create relationships in our database. We know that the first user of the blog is also the admin. It would make sense to link blog posts to 
this account and perhaps also grant admin privileges to other users so that they can write blog posts and have them link to their own account.

Therefore, we need to create a relationship between the User table and the BlogPost table in order to link them together.So we can see which BlogPosts a User has written. 
Or see which User is the author of a particular BlogPost.

If we were just writing Python code, you could imagine creating a User object which has a property called posts that contains a List of BlogPost objects.

```python
class User:
    def __init__(self, name, email, password):
         self.name = name
         self.email = email
         self.password = password
         self.posts = []
 
class BlogPost:
    def __init__(self, title, subtitle, body):
         self.title = title
         self.subtitle = subtitle
         self.body = body
 
new_user = User(
    name="Emmanuel",
    email="emmanuel@mail.com",
    password=123456,
    posts=[
        BlogPost(
            title="Life of Cactus",
            subtitle="So Interesting",
            body="blah blah"
        )
    ]        
}
```

This would make it easy to find all the BlogPosts a particular user has written. But what about the other way around? 
How can you find the author of a particular BlogPost object? That is why we use relational databases instead of a simple Python structure like a list.

In relational databases such as SQLite, MySQL or Postgresql we can to define a relationship between tables using a ForeignKey and a relationship() method.

If we wanted to create a One to Many relationship between the User Table and the BlogPost table, where One User can create Many BlogPost objects, we can use the SQLAlchemy docs to achieve this : https://docs.sqlalchemy.org/en/13/orm/basic_relationships.html

So in our case, the User class (users table) is the parent:

![image](https://user-images.githubusercontent.com/55893421/119413243-40fa8c00-bcbb-11eb-9fae-b2086a1ce5e2.png)

The BlogPost class (blog_posts) is the child: 

![image](https://user-images.githubusercontent.com/55893421/119413507-bcf4d400-bcbb-11eb-83c2-be4cbe75b534.png)

We added a foreign key "author_id" in the BlogPost class, an integers that refers to the property "id" inside the table "users".

In the parent (User class), we added a "posts" property which will be like a list refering to all the BlogPost objects attached to that User
We use the relationship() for that case and specify the name of the class to which these objects will belong (BlogPost).
To make a bidirectional relationship in one-to-many, we need to use the "back_populates" parameter which connects to the two tables together.
That parameter acts like a pointer to a particular property in the parent/child table (vice-versa)

After all that, we must delete and re-initialize our database, since we just modified the configuration of our tables (we added a new column, author_id).

To display the author's name of a blog post, since the author property of BlogPost is now a User object, we can use that to our advantage and tap into that property
to access the "name" property of the User class.

In index.html:

![image](https://user-images.githubusercontent.com/55893421/119415971-a1d89300-bcc0-11eb-86a2-ee581763e59d.png)

In post.html:

![image](https://user-images.githubusercontent.com/55893421/119416021-b9b01700-bcc0-11eb-9e63-e6ab33c0234a.png)



















