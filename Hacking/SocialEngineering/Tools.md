
Local server + tools : [gophish](https://github.com/gophish/gophish)
Templates generation : [PhishMailer](https://github.com/BiZken/PhishMailer)
[Fake SMS](https://rockyjaat111.medium.com/social-engineering-attacks-creating-a-fake-sms-message-8509388be25a)
[ZPhisher](https://github.com/htr-tech/zphisher)

[Social Engineering Toolkit docker](https://hub.docker.com/r/warch/social-engineering-toolkit)



#### Send a spoofed email

```bash
sendemail -xu real@email.com \
-xp smtppassword -s smtp-relay.sendinblue.com:587 \
-f "from@email.com" -t "target@email.com" \
-u "Subject Of Mail" -m ./mailcontent \
-o message-header="From: Prénom Nom <from@email.com>"
```

