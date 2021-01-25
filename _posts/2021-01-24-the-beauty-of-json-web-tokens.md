---
layout: post
title:  "The Beauty of JSON Web Tokens"
date:   2021-01-24
---

Recently, I've been working on a Web project with a couple of friends. The
project needs some form of user authentication and authorization, and after
trying out a hosted solution we ended up just rolling our own. Typically I shy
away from anything like that because it's easy to get it wrong, and doing so
carries serious consequences. The password authentication was actually quite
simple---we used good old `bcrypt`---but we didn't have a good idea for how best
to accomplish subsequent authentication. That is, once the user logs in, how
would we keep track of who they are during later requests?

I had zero experience actually building something like this---after all, the
wisdom I'd internalized was to never attempt it except for fun---but I'd read a
little bit about JSON Web Tokens (JWTs), so for lack of a better idea I gave
them a look. Having implemented an authN system around them now, I think JWTs
(pronounced "jaw-ts", apparently) are the best general-purpose system for this
problem. In just a few minutes of investigation, the design convinced me.

Here's how it works. First, you authenticate the user in the normal way
(password, MFA, fingerprint, facial recognition, etc.). Upon success, the server
builds up a JSON object with whatever fields it wants. At a minimum, it'll
probably put in a `sub` field (indicating the `sub`ject of the JWT, usually a
user ID) and an `iat` field ("issued at" time, usually the UNIX time). The
server can add as many fields as it cares to, like more information about the
user, or bits of application state. We call these key-value pairs "claims"
because anybody can make up any JSON object they want. So far, this isn't a
secure system.

The security comes from cryptography. Before sending the JWT to the user, the
server computes a signature and appends it to the payload. The server also
inserts a header object that indicates what algorithm to use to verify the JWT's
signature. After a quick `base64encode`, the JWT heads out on the wire, and the
user stores it wherever they want. Note that because the JWT has a signature,
nobody can convincingly modify the JWT's payload without knowing the server's
secret key. This is what makes JWT claims valid. **If the signature checks, then
the claims must be alright, since the server must have made them in the first
place.** This is my favorite feature of JWTs.

Later, when the user makes a request for which they need authentication, they
send the whole JWT along with it. The server checks the signature, and if it's
valid then it parses the payload. Then, it's up to whatever authorization rules
the server has in place to determine what to do next. The point of the JWT is to
solve the authentication problem (*who are you?*) so the server can focus on
authorization (*what should I let you do?*).

Notably, the server can also encrypt JWTs before sending them to the user, which
is important if any of the claims are sensitive or private. This protects the
claim data not just from the eyes of the user who holds it, but from anyone else
who may get a hold of it as well. In our application, we just leave them in
plaintext because there's nothing useful in them, but other applications could
surely use this feature.

A friend on our project pointed something out that I think is important to
mention about JWTs. For all their convenience, there is a single point of
failure: the server secret. If that secret becomes public, then anyone could
forge their own JWTs and get arbitrary access to the application, provided they
also had some domain knowledge about it. In the case of encrypted JWTs, they
could also decrypt them. This same friend pointed to session tokens as an
alternative. In the session token scheme, the server keeps a lookup table of
tokens and user data on the server and only gives the user the token itself,
which is a random string of bits. They said that they felt session tokens were
more secure because the sensitive data is kept server-side, while the user just
gets nearly meaningless noise to hold on to.

And I think that's a valid perspective. But I've realized since then that if our
threat model includes access to secrets on the server, then it doesn't make any
difference what authentication scheme we use; the attacker will always be able
to break it. If we're allowing for that threat, then we might as well use
whatever we like best.

I'm definitely biased because I think JWTs are neat. They use constant memory on
the server, and they only use a bit of extra time per authenticated request.
Plus, there's a little cryptography in there to make it go, and who doesn't like
that? And besides, if the server secret becomes public, it's not hard to set up
a new one and implicitly invalidate all existing JWTs written against the old
secret.

And if that wasn't enough, there's a cool website for JWTs at [jwt.io][0].
There's even a debugger there where you can decode and verify JWTs or go the
other way and build them out of JSON and a secret. Go play with it, and maybe
you'll agree that they're neat.

By the way, that conversation with my friend about the weaknesses of JWTs led us
to an idea: what if the server used a separate key for each JWT? The server
would need a lookup table, but this time it would map some sort of JWT ID to
secret keys. Signature verification would involve first looking up the proper
key. Encrypted JWTs wouldn't work unless you had some ID outside the encrypted
payload. And it still wouldn't protect against a bad actor on the server. But it
*would* protect the server from global key compromise if only a subset of the
key table got out. It's probably not worth it, though. In fact, I'm not sure
this combination system is any better than its component parts (session tokens
and JWTs), but it's still fun to consider.

[0]: https://jwt.io/
