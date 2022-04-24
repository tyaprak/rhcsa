# SETFACL
Do not forget to set "d:" after giving the proper rights.
Ex:
```bash
setfacl -m g:profs:rx /examfiles
setfacl -m d:g:profs:rx /examfiles #For the new files!!!
```