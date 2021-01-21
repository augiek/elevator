# elevator

In my rush to push everything to Github, I somehow deleted all of my work and it won’t restore properly from my trash bin. I wrote this before that, when I realized I was going to run out of time.

I set out to build a "game" where a user could enter their floor of embarkation and their desired destination floor, and the elevator would then travel from its current location to the rider's floor, pick them up, and travel to their target floor. The ultimate (and only worthwhile) version of the game would permit several users at once to choose their embark and target floors, thereby requiring the elevator car to travel to them in succession (to be defined below)

I attempted to build the game with Python, in Django. I got as far as launching the app and creating Elevator and Rider models, as well as a couple of viewable pages, before realizing I was short on time and wouldn't produce anything useable at that rate. 

So, I decided to take a few minutes to write down my thoughts just in case I cannot finish the project. Hopefully they count for something. 

To accurately model the sequence of the elevator's stops, and consequently the rider's wait and ride times, you need to know a few things: the elevator's current floor, the elevator's current direction (up or down), and the elevator's current queue of stops (from the other riders). I included all of these fields in my Elevator model. I spent too much time trying to figure out how to include an ArrayField in my model, for the elevator's current queue. I hadn't used that field type before, and, in the end, I sadly couldn't get it to work (this is where a lot of my time went, leaving me with too little time to get something else cool to work).

Once you have the elevator's current floor (EC), its queue of stops (EQ), and its direction (ED), you can tell how many stops are left before any given floor (assuming you also know the building height, in floors, and assuming no one new joins the queue). Let's assume the building is 13 floors tall (just our luck!). If EC is 4, ED is "up", and EQ is [2, 3, 1, 5, 1, 7, 12, 4], then at 1 second per non-stopping floor and 5 seconds per stopping floor, the person on 12 will have to wait 5 (5th) + 1 (6th) + 5 (7th) + 1 (8th) + 1 (9th) + 1 (10th) + 1 (11th) = 15 seconds for the elevator to pick them up. 

This model does not consider (yet) that a real elevator only stops if the difference between the rider's current floor (RC) and target floor (RT) matches the ED. If you're on 4 and want to travel to 10, you'll only get picked up when the elevator is going up (RT - RC is positive). If the ED is "down", you won't get picked up when it travels from 5 to 1 (even though your wait time would be the same whether you rode down and back up, or just waited for it to hit 1 and turn around)

It doesn't take long to create a quite complicated model, especially when you start including multiple, independent riders. Another consideration would be elevator capacity. If there are multiple elevators, you could also speed things up by only allowing a certain number of stops to collect new riders during a single run either up or down (after a point, they can just get the next one!). 

In my game, I was going to have a page where a rider could enter their start and end floors, and another page that showed the status of the elevator (ED, EC, and EQ), as well as the rider's status (waiting, riding, arrived). As each rider refreshes their status page, they can see the elevator move between floors, stopping to collect and deposit riders. They could also potentially see who else is in the elevator with them, and if the ride was long enough, they could message the other riders. As each new rider submitted their embark (RE) and debark floors, those numbers would be appended to the EQ, and stops would be made according to the EQ and direction. As the elevator stopped at specific floors in the EQ, they would be removed from the list. Again, this is the "simple" method, which may require some riders to travel up to go down, or down to go up, as the car stopped on their floor. 

A quick rundown of how I'd think about the items requested in the challenge: 

    To track what floor it's on:
        A display on the Elevator Status page shows the floor, based in the time per floor and difference between the previous and next stopping floors. 

    Where it needs to go:
        It goes to the next higher (if going up) or lower (if going down) floor from its current location.

    How it receives new input: 
        Users can enter their start (RE) and end (RT) floors via a form. The floors are appended to the EQ list. I suppose the list could be reordered and duplicates could be dropped, although this isn't necessary.

    How it accepts multiple riders/floors at once: 
        Since the EQ is just a list and the elevator has to go in order (all the way up to the highest number, and down to the lowest), then even if many people are pushing the call button or requesting the same floors simultaneously, the elevator will still pick them up and drop them off in the same order. This does not account for whether to rider disires to go with or against the current ED--in the first release, they'll have to take the long way.

    User message: 
        When EC = RT, show the message (although I think I'd show one when EC = RE as well, to let them know they should get onboard to start their journey)

This was a difficult challenge but a fun one to think about, especially since no one has had the pleasure of riding a real elevator in almost a whole year! Thank your for your consideration and for your support of Code Platoon!



