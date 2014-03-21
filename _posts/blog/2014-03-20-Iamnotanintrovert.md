---
layout: post
title: I am not an introvert. I am just busy.
description: I am not an introvert. I am just busy.？
category: blog
---
For a few weeks now I’ve been fighting with a really odd bug. I have a server process that opens a persistent connection to a service provider, authenticates the end user, and then performs a series of streaming operations.
<br  />
Inexplicably, every now and then this process leaks a socket. It doesn’t happen very often, but often enough that, after a while, the machine on which the server is running gets starved of resources and starts thrashing around as it queues requests that cannot be fulfilled.
<br  />
It’s been driving me crazy; I can’t figure out what the problem is, and I spend my time stuck between restarting servers before they crap out completely and staring at this piece of code. Here, let me show it to y…
<br  />
Hang on.
<br  />
This is not my office. I am not sitting at my desk. My computer is nowhere to be found. Where the hell am I?
<br  />
There are people around. It’s pretty loud. Looks like maybe it’s a party of some kind.
<br  />
Hey, why is my hand wet? Oh, look. A drink; dark and sparkly, it looks like a coke. Yep, it’s a coke. The drink feels cold, but there’s no ice. The little white napkin is soaked through, its edges smashed up in my hand, so I guess I must have been here for a while.
<br  />
Right, now I remember. It’s that office party my friend Dan invited me to. Someone’s turning 40. Or maybe 50. I don’t remember. Dan is a good guy, but we couldn’t be more different, and he talks too much. I guess it comes with being an insurance executive.
<br  />
Oh well, at least I didn’t have to wear a suit to come here. Still, I managed to avoid dressing like that idiot over there by the elevators. Seriously, who wears a hoodie at an office party?
<br  />
Well, who cares. He’s called the elevator, so he’s probably on the way out. Oops, watch it there, buddy—stop looking at your Facebook account on that iPhone, or you’ll miss the elevator. The light’s off, the car is going to arrive any second now… and you missed it. Haha, you silly idiot, you…
<br  />
Holy cow.
<br  />
Holy cow.
<br  />
He wasn’t paying attention. He missed the elevator.
<br  />
I bet you that’s exactly what happens in my code. If the remote end hangs up while I’m waiting for my authentication tokens, my app won’t notice, error out, and leak the socket.
<br  />
That’s it. Two weeks of agony, and the solution had to come to me in the middle of an office full of insurance salespeople, with a flat coke and a smushed up paper napkin in my hand.
<br  />
I should probably leave now and go check if this solves the problem. But I don’t want to be a jerk… I’ll need to figure out a way to sneak out unseen. I can’t just wait around. This has been gnawing at me for too long.
<br  />
Ah, crud. Here comes Dan. He’s smiling, and there’s an older man with him.
<br  />
Sigh. I guess I can’t just leave. Are they actually talking to me? Unbelievable. I can’t talk, Dan; can’t you see? I’m hanging to this idea by a thread as it is. I can’t waste words, or this brilliantly obvious solution may be lost by the time I get back in front of a keyboard.
<br  />
Oh, this is your CEO. Yes. Pleasure to meet you, too. Smile. Bow a little. Nod. I must not forget about that damn elevator and the authentication tokens. Yes, Dan is a great guy. Oh, he’s told you about me? How nice. I’m sure he hasn’t told you about this damn problem you’re keeping me from actually solving so I can finally sleep for the first time in days.
<br  />
All right, they’re leaving. I probably blew it, as usual. They’ll think I’m crazy. Or asocial. I really don’t give a toss right now, because I’m finally fixing this stupid bug.
<br  />
Let me call the elevator before someone else has the brilliant idea of trying to waste more of my time. I won’t miss it. I have a bug to fix!
