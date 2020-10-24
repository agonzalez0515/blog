---
title: Learning Functional Programming
date: '2019-11-08'
---

These last three weeks I have been deep in learning how to write functional programs, and I’ve been doing it while also learning Clojure. We’ll talk abotu Clojure later. 

Learning how to think functionally has been fun. The biggest shift for me was learning how to write functions without side effects. Pure functions are functions that are predictable, stable, they always give you the same output if you give it the same input. Pure functions don’t have side effects. A side effect would make the function not predictable. What if the output relied on that side effect? If the side effect fails, what will the output be? Having to ask this questions means you don’t have a pure function. 

One of the main points in Object Oriented design is that objects hold their data and their behavior. I remember reading this and having an a-ha moment. It made so much sense that an object would hold information about itself and know what to do with it. This means that an object is mutable and can tell you its state right now in time. But…it can’t tell you what it was 2 seconds ago. Maybe it changed when you didn’t notice and now you don’t know why it changed and what changed it and what this means for anything that depends on it. 

In functional programming, you never change anything. You feed some data structure into a function, it acts on it (a process), and then outputs a new data structure. When you connect these new outputs through an identity, you get an “object”. One of my favorite examples I read (I think it was in Clojure for the Brave and True) was the idea of a phone number as immutable. You have a thing called “Angie’s Phone Number”, it exists, other people have access to it and use it. When you say “new number who dis?”, you have a new phone number that is still identified as “Angie’s Phone Number”. The old one still exists somewhere, but this new one that was created now has the identity. You can have all these numbers throughout time, and they can all be referred to as “Angie’s Phone Number”.  Rich Hickey says in his talk ["Are we there yet?"](https://www.infoq.com/presentations/Are-We-There-Yet-Rich-Hickey/) that we "associate identities with a series of causally related values...[this] doesn't mean there is an enduring, changing entity". This is contrasted with an Object, which is thought of as a living thing that changes over time.

Having to switch from changing an object to creating a new one through a pure function was the first big thing I struggled through. I kept wanting to ask the "object" what the current state was. I kept trying to shove 2-3 things to do in a function before it outputted something. It started to click for me when I was working on the Tic Tac Toe board. When it came to figuring out the winning logic, in ruby I used probably around 10 lines of code all shoved inside 2 methods. In Clojure, I had about 10 functions! But each one did one thing, purely. I actually like the way this reads better, everything has a description because of the function name and I can even reuse some of the functions in other logic. Being forced to think smaller and purely gave me more code, but I think it's more readable, changeable, stable, and easier to tell where something could have gone wrong.

**They are building blocks!** 


My ruby methods to check if there is a winner on the board:
``` ruby
    def winner?(board)
        rows = board.each_slice(3).to_a
        columns = rows.transpose
        diagonals = [[rows[0][0],rows[1][1],rows[2][2]],[rows.reverse[0][0],rows.reverse[1][1],rows.reverse[2][2]]]
        
        [rows, columns, diagonals].map {|section| check_spots(section)}.include?(true)
    end

    def check_spots(section)
        section.map {|group| return true if group.uniq.length == 1 && (group[0] == "X" || group[0] == "O") }.include?(true)
    end
```

 My clojure functions to do the same thing:
``` clojure
(defn- get-board-size
  [board]
  (math/sqrt (count board)))

(defn- create-columns
  [board]
 (apply mapv vector (partition (get-board-size board) board)))

(defn create-rows
  [board]
  (apply mapv vector (create-columns board)))
  
(defn- get-cell-value
  [row-with-index]
  (first (map (fn [[index row]] (row index)) row-with-index)))

(defn- create-diagonal
  [cells]
    (let [row (map-indexed hash-map cells)]
      (into [] (map get-cell-value row))))

(defn- create-left-to-right-diagonal
  [board]
  (create-diagonal (create-rows board)))

(defn- create-right-to-left-diagonal
  [board]
  (create-diagonal (reverse (create-rows board))))

(defn- create-groups-of-cells
  [board]
  [(create-rows board) (create-columns board) [(create-left-to-right-diagonal board) (create-right-to-left-diagonal board)]])

(defn- check-group-of-cells
  [cells]
  (map (fn[collection] (= 1 (count (set collection)))) cells))

(defn- available-spaces?
  [board]
  (some number? board))

(defn win?
  [board]
  (boolean (some true? (flatten (map check-group-of-cells (create-groups-of-cells board))))))
```