# Python 3.5 issues in OpenMediaVault

```python
Exception ignored in: <function WeakValueDictionary.__init__.<locals>.remove at 0x7fc5bc2b8598>
Traceback (most recent call last):
File "/usr/lib/python3.5/weakref.py", line 117, in remove
TypeError: 'NoneType' object is not callable
Exception ignored in: <function WeakValueDictionary.__init__.<locals>.remove at 0x7fc5bc2b8598>
Traceback (most recent call last):
File "/usr/lib/python3.5/weakref.py", line 117, in remove
TypeError: 'NoneType' object is not callable
```

Seems to be a bug in Python, see [bugs.python.org/issue29519](https://bugs.python.org/issue29519) and [github.com/python/cpython/commit/9cd7e17640a49635d1c1f8c2989578a8fc2c1de6](https://github.com/python/cpython/commit/9cd7e17640a49635d1c1f8c2989578a8fc2c1de6).

replace new lines fixed it:

line 109:

```python
def remove(wr, selfref=ref(self)):
```

**replace with:**

```python
def remove(wr, selfref=ref(self), _atomic_removal=_remove_dead_weakref):
```

line 117:

```python
_remove_dead_weakref(d, wr.key)
```

**replace with:**

```python
_atomic_removal(d, wr.key)
```

---
via: https://forum.openmediavault.org/index.php/Thread/19658-Upgrade-Debian-9-and-4-x/
