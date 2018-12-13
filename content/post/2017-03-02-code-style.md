---
layout: post
title: code style
date: 2017-03-02
description:
category:
Tags: []
---


Ref: https://patchwork.kernel.org/patch/9368853/

There is one *correct* way to remove an entry from a singly linked
list, and it looks like this:

    struct entry **pp, *p;

    pp = &head;
    while ((p = *pp) != NULL) {
        if (right_entry(p)) {
            *pp = p->next;
            break;
        }
        pp = &p->next;
    }

and that's it. Nothing else. The above code exits the loop with "p"
containing the entry that was removed, or NULL if nothing was. It
can't get any simpler than that, but more importantly, anything more
complicated than that is WRONG.
