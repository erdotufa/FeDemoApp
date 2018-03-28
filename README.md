# FeDemoApp

App creata per la presentazione al gruppo FrontEnders Ticino Marzo 2018
[Slides presentazione](https://docs.google.com/presentation/d/1c5SWmUCUPV-a-SFxHok222YWyLNrRawyfmFcIulSqIY/edit?usp=sharing)


## Dipendenze

- Jekyllrb
- Snipcart 
- Firebase


## Installazione

Installare [Jekyll](https://jekyllrb.com/) 
Creare un app [Firebase](https://firebase.google.com/) 
Sostituire in assets/js/app.js la parte di config con quella copiata da firebase

```js
    var config = {
        apiKey: "",
        authDomain: "",
        databaseURL: "",
        projectId: "",
        storageBucket: "",
        messagingSenderId: ""
      };
```

Creare un account di test su [Snipcart](https://snipcart.com)
Nel file _includes/head.html inserire la chiave di snipcart

```js
 data-api-key="YourKey"
```

Eseguire il comando:

    $ jekyll s 

provare il sito in locale.

Durante la presentazione avevo fatto inoltre vedere come usare [Zapier](https://zapier.com) collegato a Snipcart tramite Webhook che aggiungeva un record nel nodo payments del DB in Firebase ed una Cloud function di Firebase che mandava una mail sul trigger del aggiunta di un nuovo record sul DB payments.

```js
const functions = require('firebase-functions');
const admin = require('firebase-admin');
const nodemailer = require('nodemailer');

admin.initializeApp(functions.config().firebase);

const mailTransport = nodemailer.createTransport({
  service: 'gmail',
  auth: {
    user: yourEmail,
    pass: yourPassword,
  },
});

exports.PaymentMailSender = functions.database.ref('/payments/{pushId}/').onWrite((event) => {
    // Grab the current value of what was written to the Realtime Database.
    const payment = event.data.val();
    console.log('Payment', event.params.pushId, payment);   
    const to = "nexupavaz@carbtc.net";
    const mailOptions = { from: `<noreply@firebase.com>`, to: to };
    mailOptions.subject = `Welcome to FrontEndersDemoapp`;
    mailOptions.text = `Pagamento ricevuto: numero di oggetti ${payment.items}  per un totale di ${payment.total} `;
    return mailTransport.sendMail(mailOptions).then(() => {
        return console.log('New payment email sent to ', to);
    });
});
```
