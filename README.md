# Threat Flow
A condensed Threat Modeling technique based on STRIDE and LINDDUN.

## ⚠️ corrections TODO
* "U" only for entities, like users
* Explicitly distinguish trust zones and criticality

# Summary

Yet another threat modeling technique tailored to do a simple yet effective (enough) threat modeling. It's based on classic STRIDE methodology and 
* Uses "criticality" instead of (or better yet, in combination to) usual Trust Zones, in order to better capture distributed architectures (microservices, anyone?)
* Condenses the STRIDE rules in a more compact form to maximize speed
* mixes in some of privacy threat form the LINDDUN framework 

# Input

An architecture diagram is needed as input, preferably a DFD

# The methodology

## Finding Criticalities
We'll label every system or actor in the diagram as having a “criticality” value. This will range from 0 to a maximum number. This doesn't need to be determined up front, rather will be the result of updating it several times during the labeling process itself.

Remark: in old-fashioned “layered” systems, trust zones kinda implicitly capture the concept of criticality too. 
In a microservice setting, services in different trust zones might have the same level of criticality. 
In this case we need to denote criticality explicitly, otherwise we end up with a model where we can't infer nothing from statements like _“Here we have a system labeled 2 accessing directly a system labeled 13“_. In fact — when labeling per trust zone — we'd reach high number pretty quickly, but those numbers wouldn't have have a criticality meaning therefore rendering impossible to draw conclusions about “bad relationships” among systems.

1. Start by giving untrusted external entities 0 e.g. users
2. Every element directly contacted by a 0 will have 1
3. navigate the diagram and give a criticality score, answering the question: _“how harmful is if this component gets compromised?”_
4. treat trusted “external” entities (e.g. systems in other teams in the company), like internal i.e. don't give 0


## Applying STRIDE rules, with a flow based algorithm

Here's a pseudocode of applying the 🅢🅣🅡🅘🅓🅔 (letters in circles) with **some** additional privacy-specific threats from the 🅻🅸🅽🅳🅳🆄🅽 framework (letters in squares).

```
for each flow (orig, dest):
    delta = criticality(dest) - criticality(orig)
    if delta < 0:
        mark dest with 🅘
    if delta > 0:
        mark dest with 🅔
        mark flow with 🅣
        if criticality(orig) = 0:
            mark dest with 🅢 🅓 🅡
            mark dest with 🆄 
    if dest is dataStore with:
        PII -> 🅸
        other -> 🅳
        PII & other -> 🅻🅸🅳 
```

## Evaluating “jumps”

Now it is possible to look at "jumps" on the data flows connecting different processes (or components). For instance if you have a flow connecting a process with 2 with a process with 10, there might be some issues or at least is something to keep on the radar. The bigger the jump, the bigger the concern. 


## Final thought

If we'd used usual trust zones, assigning every service a different number — to have different services in different zones — we would have ended up with tons of very high numbers, so evaluating "jumps" would have been nearly useless.

