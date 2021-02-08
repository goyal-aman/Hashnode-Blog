## Understanding Ruby On Rails in 10 Steps

# Introduction
Ruby on Rails is a full-stack web app building framework made with the Ruby programming language.

Ruby has clean, simple syntax without tons of parenthesis, brackets, or semi-colons

GitHub, Shopify, Calendly, ThePracticalDev, eggheadio & ConvertKit were built using Rails.

By the end, you will have a brief understanding of Ruby On Rails.

1. Ruby on Rails is a full-stack web app building framework made with the Ruby programming language.
Ruby has clean, simple syntax without tons of parenthesis, brackets, or semi-colons **GitHub**, **Shopify**, **Calendly**, **Dev.to** were built using Rails.
![pic 1.jpg](https://cdn.hashnode.com/res/hashnode/image/upload/v1609915699257/i17cg9z2O.jpeg)
2. Rails use the MVC (Model. View. Controller) programming pattern to organize and separate your code. 
This makes it easier to know where things are and how they are connected which gives you more time to focus on building features users need
![pic 2.jpg](https://cdn.hashnode.com/res/hashnode/image/upload/v1609915719653/xOrO6t54O.jpeg)

3. Models represent the data in your apps. 
You define models to make sure any new models match before being saved.
You can also associate different models with each other. For example, users and their comments and/or articles.
![pic 3.jpg](https://cdn.hashnode.com/res/hashnode/image/upload/v1609915760275/Ur_bBQ8ph.jpeg)
4. Views is what gets displayed to users. It's written just like plain HTML.
You can you embedded Ruby(eRB) to put Ruby code inside of your views to display data from the database. You can display multiple user's username writing just one line of code!
![pic 4.jpg](https://cdn.hashnode.com/res/hashnode/image/upload/v1609915876255/z7ljzcKCF.jpeg)

5. Controllers are where you tell your app what to do with your data and what to show to your views.
You can grab all users from the database and tell Rails to display them after a user logs-in or click a link to the user index page.
![pic 5.jpg](https://cdn.hashnode.com/res/hashnode/image/upload/v1609915894655/L5FHsKOtV.jpeg)
6. Partials are Views you can share to keep you from re-writing the same code multiple times.
A user sign-up and user edit are both forms with emails, passwords, etc.
You can write the form once and use it in any view with the render keyword and eRB syntax.
![pic 6.jpg](https://cdn.hashnode.com/res/hashnode/image/upload/v1609915886135/uzkynlCpZ.jpeg)
7. Helpers are another way Rails keeps you from rewriting the same code.
Rails has built-in helpers for common things like formatting numbers. You can make your own helpers in the app/helpers folder and use them in your views with eRB.
![pic 7.jpg](https://cdn.hashnode.com/res/hashnode/image/upload/v1609915896659/2NG1Z7WbM.jpeg)
8. Testing is made easy in Rails. 
A test folder is made with each new Rails app.
 Each time you create a new model or controller it creates a test file as well.
Testing library minitest is built in. Use the test method with assertions to check for expected outcomes.
![pic 8.jpg](https://cdn.hashnode.com/res/hashnode/image/upload/v1609915901355/JobIx4-uS.jpeg)
9. Gems are like plugins you add to Rails to gain new functionality.
Some gems handle complex things like authentication and payments. Gems are located in Gemfile in the root of your Rails app.
![pic 9.jpg](https://cdn.hashnode.com/res/hashnode/image/upload/v1609915911740/3OepEh6sK.jpeg)
10. Routing tells your application where things go.  
Each path is linked to an action inside the controller. 
The users path shows all users which is linked to the index action inside the controller.  Which matches the users index view.
![pic 10.jpg](https://cdn.hashnode.com/res/hashnode/image/upload/v1609915916169/boEpU5985.jpeg)

# Conclusion
Ruby on Rails is, as I think of it, is very similar to Django and very fun. I came across those tweets by [willjohnsonio](https://twitter.com/willjohnsonio) on Twitter in [this thread]() I couldn't resist sharing them here.
<hr>
PS: You can find him [here on twiiter](https://twitter.com/willjohnsonio) and here [here on dev ](https://dev.to/willjohnsonio), me [here on twitter](https://twitter.com/_goyalaman). And please share your thoughts below, maybe a Hii, It makes my day.


