---
layout: post
title: Member gradient descent?
---

The general paradigm of online learning is the following:

$$ a = b $$ 


{% pseudocode %}
Function swap(old, new)
  remaining <- quorumSize
  success <- False
  For Each host
    result[host] <- send(host, propose(old, new))
    If result[host] = "ok"
      remaining--

  If remaining > 1+quorumSize/2
    success <- True

  For Each result
    If success
      send(host, confirm(old, new))
    Else
      send(host, cancel(old, new))
{% endpseudocode %}
