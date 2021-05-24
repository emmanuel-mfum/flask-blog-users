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












