## How To Create A Timeline To Track Your Achievements

# Introduction
Quite recently I came across a tweet by [Florin Pop](https://twitter.com/florinpop1705) which talked about tracking your minor and major achievements using a timeline. It was an interesting idea.. how come I never thought about this. Thus, inspired,  I set on to start creating my own version. In this article, you will see my journey from inspiration to creation.

%[https://twitter.com/florinpop1705/status/1338752452158435329?s=20]



# Tech Stack
What I wanted at the end was very clear, only the tech I needed to use was to be figured out. I chose Django as a backend .. because why not.. it provided easy to manage model-view-control architecture and out of the box admin panel and front-end was going to be simple Html and CSS,  I am not a fancy man.. simple is fine with me.

# Starting out
I started out by creating a simple Django project and then creating a timeline app. I planned to add more stuff to it moving forward so creating a separate app was good idea. further, I added *card* model with simply keeping **heading**, **body**, **date** as fields if anything else is ever needed like **tags**.. (which I plan to add later) can be added easily. 
```
class Card(models.Model):
    """
    In timeline: Each achievement is shown in card
    """
    heading = models.CharField(max_length=55, default="I forgot to add heading..", blank=False, null=False)
    body = models.TextField(max_length=999, default="", null=True, blank=True)
    date = models.DateField(default=timezone.now, null=False, blank=False)

    def __str__(self) -> str:
        return f"{self.heading}.. on {self.date}"
``` 

once the model was done.. it was time to create some view to handle some behind the scene stuff. I wanted a timeline in which all the cards (i.e. achievements) are separated by years.. thus I needed to handle this.

For view, I went with class based generic view `django.views.generic.View` and defined my own request handling methods.. since I figured I might be needing custom things in future. 
```
class CardView(View):
    template_name = 'timeline/card_list_view.html'
    def get(self, request):
        cards = Card.objects.all().order_by('-date')

        return render(request, template_name=self.template_name, context={'timeline_data':cards})

```
For my first approach, I simply tried rendering all cards to front.. which I soon figured is not gonna work. Since there is no way in Django(at least I couldn't find) that I can query the database in such a way that can give me cards separated by years. Hence, I added my own logic to handle this.
```
class CardView(View):
    template_name = 'timeline/card_list_view.html'
    def get(self, request):
        cards = Card.objects.all().order_by('-date').values()
     
        qs = defaultdict(list)
        for query in cards:
            card_year = query['date'].year
            qs[card_year] += [query]
        qs = dict(qs)
     
       return render(request, template_name=self.template_name, context={'timeline_data':qs})
```

I used `defaultdict`, very helpful tool.. saved me some `if-else`  and `try/except`. If you are wondering what I am doing here - first I am getting all the cards in newest to oldest order fashion then `.values()` is converting it to list of dictionaries. I then created a defaultdict object.. in which each item has `list` as default value and added all the cards of the same year in a key-value pair. That's it.. that's literally the depth of backend code.

Moving forward, I am little too lazy to create my own frontend thus I looked around here and there and found a great timeline template just as the way I wanted - simple yet powerful ( check [here](https://codepen.io/htmlcodex/details/KKVpdNK) ). 

%[https://codepen.io/htmlcodex/pen/KKVpdNK]

There was a problem.. it was using different css class for alternate cards *left* for odd ones and *right*  for even. So, for me to use this I needed a way to use 
1. counters in Django templates, and
2. find a way to if they are even or odd..since we cannot use simple %2 there.

Thus I did some looking around and found not one but two unexpected things .. together solving my problem. First is `forloop.counter`, as the name suggests.. it is a counter for `for` loops in django.. and second one  `divisibleby` filter (literally Django has everything). Finally, my front end  was looking something like this.. (I have trimmed irrelevant code)

```html
<div class="timeline-continue">
    {% for year, card_list in timeline_data.items %}
        {% for card  in card_list %}
            {% if forloop.counter|divisibleby:2 %}
                {% include 'timeline/snippets/right_card.html' with heading=card.heading date=card.date body=card.body %}
            {% else %} 
                {% include 'timeline/snippets/left_card.html'  with heading=card.heading date=card.date body=card.body %}
            {% endif %}
        {% endfor %}
        {% include 'timeline/snippets/year_circle.html' with year=year %}                    
    {% endfor %}
</div>
```

and yes, I used snippets to follow DRY principle.. with left and right card snippets looking like this.
left card
```
<div class="row timeline-right">
    <div class="col-md-6">
        <p class="timeline-date">
            {{date|date:"F  "}}
        </p>
    </div>
    <div class="col-md-6">
        <div class="timeline-box">
            <div class="timeline-icon">
                <i class="fa fa-gift"></i>
            </div>
            <div class="timeline-text">
                <center>
                <h3>{{heading|title }}</h3>
                <p>{{body}}</p>
                </center>
            </div>
        </div>
    </div>
</div>
```

right card 
```
<div class="row timeline-left">
    <div class="col-md-6 d-md-none d-block">
      <p class="timeline-date">
        {{date|date:"F"}}
      </p>
    </div>
    <div class="col-md-6">
      <div class="timeline-box">
        <div class="timeline-icon d-md-none d-block">
          <i class="fa fa-business-time"></i>
        </div>
        <div class="timeline-text">
          <center>
          <h3>{{heading|title }}</h3>
          <p>{{body}}</p>
          </center>
        </div>
        <div class="timeline-icon d-md-block d-none">
          <i class="fa fa-business-time"></i>
        </div>
      </div>
    </div>
    <div class="col-md-6 d-md-block d-none">
      <p class="timeline-date">
        {{date|date:"F"}}
      </p>
    </div>
  </div>
```

Honestly, it was it. It was just a matter of creating some cards in the database to check the final product. Which I believe came out well. You can see this final product  [here](https://amangoyal-timeline.herokuapp.com/) 


# Source Code
You can look at the complete code base [here](https://github.com/goyal-aman/portfolio/).

%[https://github.com/goyal-aman/portfolio/]


<hr>

credits : 
Article Cover <span>Photo by <a href="https://unsplash.com/@florianklauer?utm_source=unsplash&amp;utm_medium=referral&amp;utm_content=creditCopyText">Florian Klauer</a> on <a href="https://unsplash.com/s/photos/cover-photo?utm_source=unsplash&amp;utm_medium=referral&amp;utm_content=creditCopyText">Unsplash</a></span>
