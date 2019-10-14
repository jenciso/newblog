---
title: How to limit the allow characters in the jenkins creation item
date: 2019-10-14
comments: true
tags:
  - jenkins
---

Normally, Jenkins doesn't limit the allowed characters when you try to create a item, like folder, project name, etc. 

Sometimes, one special character could break the dashboard and your text search box won't works. So, to restrict and evict this human mistake in the creation time, you need to restrict this special characters, like this: `Ç` or `$`. 

This process could be done via Jenkins GUI. 

```txt
manage jenkins -> configure system -> restrict project naming.
```

E.g.

On my company, we restricted the jenkins item name to use only alphanumeric characters with this simple regexp  `"^[a-zA-Z0-9_]*$"`

![kafka regexp](/images/kafka_regexp.png)

Testing:

![](https://files.slack.com/files-pri/T0G7ZH5PX-FPCULEBTJ/image.png)

-----

Sources:

https://groups.google.com/forum/#!msg/jenkinsci-users/n9YqCkWDNhM/kTP5x-_2DwAJ

https://stackoverflow.com/questions/336210/regular-expression-for-alphanumeric-and-underscores

