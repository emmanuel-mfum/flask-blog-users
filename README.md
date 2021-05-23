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







