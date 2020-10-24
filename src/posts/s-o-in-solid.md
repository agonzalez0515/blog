---
title: Single Responsibility and Open-Closed Principles
date: '2019-09-06'
---

One of the first things I'm learning at 8th Light is best practices around Object Oriented languages and learning the SOLID principles. 

### Single Responsibility

It's really easy to violate this principle even when trying to be super aware of it. One example I realized I do a lot that violates this principle is fetching data in a component in React and then using that same component to display it in a specific way. I could abstract getting data to a component that only handles that, and then passes it to any kind of presentational component as props. Then any other component that may also need some or all of that data, can also use it and present it any other way. Say you have a shopping list and you can toggle between layouts, one view can show it as bullet points and another view can show it as cards with an image. All of this data can be fetched once in a parent component, and then passed to these child components, instead of each component fetching the data themselves. 

### Open-Closed

Open-Closed principle means that a class or method should be open to extension, but closed to modification. You should not need to change something when you want to add new functionality. You should be able to work on what's already laid out. 

In one of my Gilded Rose refactors, I violated the Open-Closed principle because in order to add the new item to the catalog, I need to modify the current class, I'm not extending it😖 

``` ruby
def regular_item(item)
    item.update
end

def backstage_passes(item)
    item.update
end

def aged_brie(item)
    item.update
end
```

Above is what I currently have in my refactored Gilded Rose, and to add a new item I need to add a new method that does the exact same thing as the other 3.

``` ruby
def conjured(item)
   item.update
end
```

🙅🏻‍♀️🙅🏻‍♀️🙅🏻‍♀️

In the end, I created a hash map to hold all the items in the catalog, and would then reference that hash using the name of the item passed to generalized update method. This way I never need to change my update method, I just add a new item to the catalog.

My fully refactored Gilded Rose kata can be found on [GitHub](https://github.com/agonzalez0515/gilded-rose-refactor/blob/master/lib/gilded_rose.rb).
