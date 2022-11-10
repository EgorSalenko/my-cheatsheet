# Python Venv

### Create venv

```
$ python3 -m venv venv_name
```

### Activate venv

```
$ . venv_name/bin/activate
```
or
```
$ source venv_name/bin/activate
```
### Deactivate venv
```
$ deactivate
```
### Remove/Delete venv
```
$ rm -rf /path/to/venv_name
```
### Clear existing venv
```
$ python3 -m venv --clear path/to/venv_name
```
### Update Python version
```
$ python3 -m venv /path/to/my_venv --upgrade
```
### Give virtual environment access to system site-packages
```
$ python3 -m venv /path/to/my_venv --system-site-packages
```

