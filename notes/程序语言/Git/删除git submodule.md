# 删除git submodule

git并没有提供删除submodule的方法，只有手动删除。

1. 删除.gitmodules下submodule的信息
2. 删除.git/config下submodule的信息
3. `git rm —cache`

---
via: http://chenzaichun.github.io/2012-12-26-git-submodule-remove.html
