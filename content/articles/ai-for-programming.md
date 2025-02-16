+++
date = '2025-02-15T15:55:12+01:00'
draft = true
title = "AI and why it's bad for Programming"
summary = "_Back in 2018 when i tried programming for the first time..._"
+++

Back in 2018 when i tried programming for the first time i came to the conclusion that this field is really difficult. The first language with what i interacted was C++. Even basic "Hello, world" program was a nightmare to me. It all came at once 'cause as it was basicaly beggining. I needed to install all those IDE, look for explanation without knowing anything etc. And i needed to do it all by myself. Go through reddit posts, 4chan discussion and many youtube tutorials. 

All these things gave me basic knowledge. I discovered many programming languages for myself. But, eventually i stopped. It all was to much for me to handle. 

Then, in 2019 i gave a chance to the Python course in one of the universities in my city. It was pretty nice. But, i started focusing more on self-taughing. I bought some books, (f.e. [Python for kids](https://www.goodreads.com/book/show/13221155-python-for-kids)) read many tutorials and explanations. 

I started to look over for some exercises. like, idk, text adventure: 

```python
print("You're in the dungeon.\nWhat would you like to do?\nGo to the room and kill a dragon or try to open a chest?")
choice = input("> ").lower()

if choice == "room":
    print("You've entered the room and died.")
elif choice == "chest":
    print("You chose a chest.\nIt has a lock of level 16. Would you like to throw a dice? (yes/no)")
    dice_choice = input("> ").lower()

    if dice_choice == "yes":
        import random
        roll = random.randint(1, 20)
        print(f"You rolled a {roll}.")

        if roll >= 16:
            print("You successfully unlocked the chest!")
        else:
            print("Failed to unlock the chest. Try again? (yes/no)")
            retry = input("> ").lower()
            if retry == "yes":
                roll = random.randint(1, 20)
                print(f"You rolled a {roll}.")
                if roll >= 16:
                    print("You successfully unlocked the chest!")
                else:
                    print("Failed again. Better luck next time!")
            else:
                print("Well, there's nothing else to do. You must open it! xD")
    else:
        print("Well, there's nothing else to do. You must open it! xD")"
```


I still remember thise nights trying to figure out where is the bag. And i coded it all in pip. Not vs code, code blocks or other but pip. Its now im in love with Vim and Nano, but then a newbie who never learned programming should have tried to solve it all.  

It helped me.

I spent 3 next years mastering python.

Idk why but in 2022 i stopped. Perhaps just lost interest. Now thinking i'd say i was an idiot.

Then in november of 2022 OpenAI published ChatGPT 3. I was one of the first to register there. Apparently in the day one. 

Well, AI changed my life in the worst way. That time i was finishing high-school. I'd say thankfully to ChatGPT, Notion and few other i stopped thinking and analysing and just uploaded everything there to explain as to idiot. 

In the end of 2024 i re-started my journey again. Currenly im learning few languages. Such as C, C++, C#, JS and Java. Also a bit of HTML. But my main ones are C and Java. 

Even after finishing CS50 and many other courses i still think im a begginer in many of them. 

That is where AI coming. Many students without understanding programming, or with lack of some concepts just upload their shit-code to LLM and just copy-paste corrected version without even reading changes. It stresses me out. 

I was just like it. Thank god i stopped.

In the field of programming in would be better to read Stack Overflow, GitHub discussions and other resourses but now just Ctrl+C -> Ctrl+V. 

If you do something like this, please **stop** and just learn.
