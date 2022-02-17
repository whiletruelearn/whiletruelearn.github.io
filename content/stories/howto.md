---
title: "How to Forget someone?"
date: 2016-01-17T00:00:00+05:30
draft: false
---


Forgetting someone is a difficult thing. Even when you think that you had sort of got over someone, there are these push buttons waiting to be activated in our brain which sends in waves of memories related to that person (whom we shall call as ‘Y’ going forward). The trigger occurs when we visit some particular place or do an activity that is usually associated with ‘Y’. Sometimes it gets triggered for no particular reason also . Anyhow , it is not a good feeling when the trigger hits and you are back again to square one thinking about ‘Y’ when you clearly know you should have totally forgot that person by now.
I thought about this for quite a bit of time today, maybe I owe the alcohol in my blood stream some credit for coming up with this thought all together. In this vague thought of mine, I imagine my silly little brain as something similar to an Operating System (OS) which i have fine grain control on. We have just identified a means to over ride the OS which mostly does shit that makes no sense and is full of erratic behaviors. Thanks to the most advanced brain programming technology, we now can send patches that will over ride the default behaviors and settings.

# Creating the first Patch

Note : All Patches must be written in a new programming language which will be aptly called as BrainFuck2.
The first patch would be to solve this problem of forgetting someone. When we are really sure that we need to completely forget someone, we need to have the mechanism to do so and let’s get this done once and for all.

# The Requirement

1. Design a subroutine FORGET_PERSON(String Name) which will accept a person’s name as argument from the user.
2. The subroutine when called should remove all the memories associated with the person whose name is passed as argument.
3. Wait a second, we don’t want to delete all the memories right away. We need something like a pop up box to show up as a mental image.
4. The pop up box in this case could be a video (similar to the year end video which facebook creates) , the background music can be sad.
5. The user should be given 42 seconds to decide if he wants go ahead with permanent deletion of all the memories, if the user backs away then the subroutine should exit and pass the control back to the main program.
6. If the user has decided with heavy heart to go with it or if the user hasn’t made a decision by 42 seconds , initiate the process for permanent deletion of all memories associated with Y.
7. Memory deletion process should happen at night time during sleep and when the user wakes up next morning , person Y would have to be a complete stranger to the user.
8. Don’t Fuck this up.

# The Rough Plan

Step (i) :- The first thing the subroutine would have to do will be to get details of the first time the user met ‘Y’ , the process would have to get the timestamp of this event . Let’s call this value as the Alpha_Moment.

Step (ii) :- Similarly acquire Omega_Moment which will be the timestamp for the last memory the user posses related to ‘Y’.

Step (iii) :- Now all we have to do is find all the memories associated with ‘Y’ within these two time ranges (Alpha_Moment and Omega_Moment ) and delete them one by one.

Premature optimization is the root of all evil

You must know by now that Step (iii) is not going to be that easy. It is going to be a very slow process that can even take ages to complete. The part of difficulty is because we don’t have a pre-defined structure by which memory is organized. We need to think of a better way in which information related to each person can be stored in our memory.
A better data structure ?

```
{ Person A :
[Met first time while travelling on a train ,
………………………………………………………………………………….
Became Friends ,
…………………………………………………………………………………….
Called on New Years eve and talked for 20 mins]
Person Y :
[Met for the first time,
Talked for the first time,
…………………………………………………………………………………….
Decided to forget once and for all]
}
```

This new data structure will be aptly called as PEOPLE_IN_MY_LIFE .
My initial analysis tells me that it will be a HashMap/ Dictionary of sorts with the persons image & name combination as the key.
The value will be a linked list where the first memory associated with the person will be the head of the linked list and last memory would be the tail. There would have to be a cron job or something akin which runs daily during sleep time which updates this linked list.

# The Execution

(i) Subroutine FORGET_PERSON() gets called with Y as argument.

(ii) We do a O(1) lookup in the data structure PEOPLE_IN_MY_LIFE .

(iii) If the key is found delete the entire content of linked list. All the memories that is stored gets deleted instantly.

(iv) If by any chance , the user tries to access the empty value present in the data structure for the person ‘Y’ with a call such as
PEOPLE_IN_MY_LIFE.get(‘Y’)
or
PEOPLE_IN_MY_LIFE[‘Y’]
the user must be redirected to 404 mental image page with the below thought inserted
```
“Do you really know this person ? I don’t think you have met this person before. There is no memory related to this person available for playback.
We are almost 100% sure that you don’t know this person from your past.
Yours Sincerely,
Brain
```

# A Time for Second Thoughts
Now that the patch is ready in our new programming language , I am having second thoughts about whether this is a right thing to do. What does it really accomplish anyway? Even when we say we wish to forget someone real badly, there is a part of us that doesn’t want it.
Removing all memories associated with someone is the easiest path that we can all think of taking. Let’s not take the easy way out. Let’s keep our memories irrespective of whether they are good or bad. Our memories are what makes us who we are. Let’s keep them.
BTW Screw BrainFuck2.