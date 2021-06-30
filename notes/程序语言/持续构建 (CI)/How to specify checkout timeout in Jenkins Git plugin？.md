# How to specify checkout timeout in Jenkins Git plugin?

`extensions: [[$class: 'CloneOption', timeout: 120]]`

Full checkout code:

```groovy
checkout([$class: 'GitSCM', branches: [[name: '*/master']],
            extensions: [[$class: 'CloneOption', timeout: 120]], gitTool: 'Default', 
            userRemoteConfigs: [[credentialsId: key, url: repo]]
        ])
```

---
via: https://stackoverflow.com/questions/35900451/how-to-specify-checkout-timeout-in-jenkins-git-plugin
