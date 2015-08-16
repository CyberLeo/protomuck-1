The permissions model for MUF has recently changed.  While HARDUID,
SETUID and QUELL do the same things they did before, the way they work
together is now different.

HARDUID is no longer required to invoke SETUID or QUELL.  The permission
level of a program starts out at the default (the owner of the program)
and then works its way down to the lowest level of permission that the
H, S and Q bit grant.   The EXTPERMS (HIDDEN/#) flag enhance these
permissions further.

Note: MUF in Neon, Glow, and Proto run at the level of the program's owner.

HARDUID (H) = If a program has the HARDUID flag set on it, then it runs
> with the uid and permissions of the owner of the -trigger-
> of the program. If the program is a timequeue event with
> trigger #-1, then it will run with the permissions and uid
> of the owner of the program.  Normally this only applies to
> programs triggering the program, but if EXTPERMS/# is set,
> then permissions will also propagate from the the owner of
> the trigger ACTION or EXIT.  This means a program set H and #
> will run at the permissions of the person linking their
> action to it.

SETUID (S) = If a program is set SETUID, it inherits the uid and mucker level
> of the program or trigger that calls it, settings on the
> program. If the program was not called by another program it
> runs at the normal permissions level of the program owner,
> UNLESS the program is also set EXTPERMS, in which it runs at
> the level of the ACTION that called it.  This means programs
> set S and # can ONLY be called by actions that have at least
> an M bit.

QUELL (Q) = If a program is set QUELL, it will run at the permissions level
> of the bit that is set on the program object itself.  This is
> like the default behaviour on FB6   If Q and # are both set on
> the program, then if the owner of the program goes QUELL, the
> program will lose permission to run.

To reiterate an important point, if a combination of H S and Q are specified
the permissions will be LOWERED, not raised, to the lowest permission level
that the 3 bits specify.  So if a program has H S and Q set on it, and the
owner is a W4, the program is an M2, and is triggered by an action set W1,
it will run as an M2.  To raise its permissions, remove permission bits
accordingly.

At NO TIME however, is a program ever granted a permissions level greater
than the program owner.   If the owner of a program loses their wizbit or
MUCKER bit, their programs will cease to run.  This is a security feature.
To allow their programs to run again, transfer them to a player with the
appropriate permissions.