# Priority Exchange

Coming soon...

The idea behind our async in our ASYNC priority high transports is that we can send
some messages to asynch priority high in some messages to Async, uh, are the goal is
for those to end up in two different buckets so that we can read all of the aceing
priority high messages first and then the async messages effectively making the async
priority high a higher priority. We do that via our consumer normally by passing,
chasing priority high and then async. Now this worked before because when we were
using doctrine, we passed this queue_name high option, which def that defaults a, the
default value for that is just default. I'm gonna go over to an open terminal here
and run my SQL. That's you route a messenger on a tor tutorial just to get into my
database. And then let's look, let's say it's run, describe messenger_messages.

I just want to show you what that table looked like. This was the table that we were
storing our messages into and you can see it had a queue_name column in there.
[inaudible]. So by setting this Q under screen name to high for doctrine, that meant
that, uh, when we consumed, we get to consume those two some rows that have the queue
name high. Some rows would have a cue name, a default, and then we could consume
those messages independently of each other and have that order. So even though they
were in one table, they were really separated into two different buckets in that
table. The problem now is that this queue name option is specific to doctrine. It has
no effect whatsoever inside of MQP, but in a high level, all goal is pretty simple.
Right now we only have one Q called messages.

We need two different cues. We need a messages and a messages, a high priority, and
we need all of our async to go to the messages queue and we need the async priority.
Hi to go to that, um, the other queue. But with AMQ B, you don't send directly to Qs,
you send to exchanges. And then the exchanges are responsible via internal rules to
figure out which messages go to which cues. So there are two ways to get our messages
to two different queues. The first one is we could have a single exchange and then we
could add rules inside of it called bindings that say that this message goes to a QA
and this other message goes to QB. We're going to show you how to do that later. That
won't work right now because the messages type is, is fan out. What that basically
means is that if a message goes into the messages, if a message is sent to the
messages exchange, it's going to automatically get sent to every single cue that it's
bound to.

So if we added like another binding here called messages high priority, that would
simply mean that when we sent a message to this exchange, that message would go to
both queues. It would be duplicated. So we're going to do instead is we're actually
going to create two different queues and each queue is going to s it's going to
forward all of its messages to exactly one, uh, two different exchanges. And each
exchange, it's gonna message all of its, uh, for and all of its messages to exactly
one cue. So we're going to do is we can describe this inside of our Messenger.yaml.
So options we're going to say exchange and then we're going to say that this should
have an exchange called how about messages? High priority.

Okay.

And then below this I'm going to say cues and then let's say messages_hi and then
just till they, this can think has two effects. First of all it's going to cause,
because we have the auto setup feature on, it's going to cause a, it's going to tell
messenger to create an exchange with this name and to put a single cue in that
exchange that will receive all the messages. Then when we send messages, it's going
to send them to the messages, high priority exchange. And when we consume messages it
from the aceing priority, high transport, it's going to consume messages from the
messages. High Cue. That didn't make complete sense. That's okay. Let's see this in
action first. Make sure that your worker is not running. Ours is not running. That's
perfect.

Now let's go over here and let's start by deleting three photos. So one, two, three
and then I'm going to upload four photos. Cool. Now, however we rabbit MQ management
and first go to exchanges. So instantly you can see it took care of creating a new
messages. High priority. The original messages exchange still sends all of its
messages to a messages queue. But this new exchange messages, high priority sends all
of its messages to a queue called messages_hi. That's thanks to our cue name right
here. And if we look in the queues, here we go. This is the end result. We wanted the
three delete messages we have or any messages Q and the four a newly uploaded photos
are in the messages under score high. So now we have two buckets, which means we can
consume them independently. If we were in this command here, it would actually
consume async priority high first and then async or we can do them independently. So
let's actually try that first. Let's consume just the ASYNC messages. And you can see
it starts going through the image posts, deleted ones. Cause that's the only thing in
there.

And it will read until it completely empties out that queue. Yup. You can see it
actually went back down to zero and then it's not reading these messages. Hi.
Wondering the messages. Hi, we need to rerun this with async priority high and that
is going to read from the other queue. So two exchanges. Each exchange has one queue
and it passes it through to the queue. And then we're able to read the messages from
the individual cues. This isn't still not, we're still, we're starting to learn about
the power of aim QP. We're still not really leveraging it. It's still fairly simple
setup. Um, but you now have all the fundamental concepts to make things a little bit
more complex.