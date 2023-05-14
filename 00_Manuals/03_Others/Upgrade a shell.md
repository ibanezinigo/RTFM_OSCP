Once we are got acces:
```
script /dev/null -c bash
```

Then Crtl + Z

```
stty raw -echo; fg
```

write the next text (you will no see it), then press enter:
```
reset xterm
```


If you want to set the console stty size type the next command in a new local terminal to know the rows and columns.
```
stty size
```

Then set this values in the reverse shell
```
stty rows NN columns NN
```
