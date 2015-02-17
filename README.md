# `workaround-upstart-snafu`

When lied to about the behavior of a job’s main process wrt. forking with the
“`expect`” stanza, Upstart can get into a confused state where it’s tracking a
nonexistent pid.

This hack creates new short-lived processes until one gets the pid in question,
then has its parent die so that it’s going to get reaped by pid 1 (Upstart),
which will get Upstart out of the confused state.

When “`status <jobname>`” says “`<jobname> stop/killed, process 12345`” and
there’s no such process, run “`workaround-upstart-snafu 12345`” and wait until
it exits.

# Taking too long?

To speed things up (i.e. to limit the PID space this script covers), follow these steps:

* get the current setting for ```pid_max```, and jot it down (typically 32768).

```
sysctl kernel.pid_max
```

* get the current highest PID:

```
ps axo pid | tail -n 1
```

* add a few hundred to this value, and call it N
* set the ```pid_max``` to this new value:

```
sudo sysctl -w kernel.pid_max=$N
```

* the above change is permanent, so remember to set it back to that initial value you jotted down.

