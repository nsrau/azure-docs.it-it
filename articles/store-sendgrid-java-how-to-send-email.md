<properties linkid="dev-java-how-to-access-control" urlDisplayName="SendGrid Email Service" pageTitle="How to use the SendGrid email service (Java) - Azure" metaKeywords="Azure SendGrid, Azure email service, Azure SendGrid Java, Azure email Java" description="Learn how send email with the SendGrid email service on Azure. Code samples written in Java." metaCanonical="" services="" documentationCenter="Java" title="How to Send Email Using SendGrid from Java" authors="robmcm" solutions="" manager="wpickett" editor="mollybos" scriptId="" videoId="" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="robmcm" />

# Come inviare messaggi di posta elettronica utilizzando SendGrid da Java

Questa guida illustra come eseguire attività di programmazione comuni con il
servizio di posta elettronica SendGrid in Azure. Gli esempi sono scritti in
Java. Gli scenari presentati includono **creazione di messaggi di posta elettronica**, **invio di
messaggio di posta elettronica**, **aggiunta di allegati**, **uso di filtri** e **aggiornamento
delle proprietà**. Per altre informazioni su SendGrid e sull'invio di messaggi di posta elettronica, vedere
la sezione [Passaggi successivi][Passaggi successivi].

## Sommario

-   [Informazioni sul servizio di posta elettronica SendGrid][Informazioni sul servizio di posta elettronica SendGrid]
-   [Creare un account SendGrid][Creare un account SendGrid]
-   [Procedura: Utilizzare le librerie javax.mail][Procedura: Utilizzare le librerie javax.mail]
-   [Procedura: Creare un messaggio di posta elettronica][Procedura: Creare un messaggio di posta elettronica]
-   [Procedura: Inviare un messaggio di posta elettronica][Procedura: Inviare un messaggio di posta elettronica]
-   [Procedura: Aggiungere un allegato][Procedura: Aggiungere un allegato]
-   [Procedura: Utilizzare filtri per abilitare piè di pagina, monitoraggio e analisi][Procedura: Utilizzare filtri per abilitare piè di pagina, monitoraggio e analisi]
-   [Procedura: Aggiornare le proprietà dei messaggi di posta elettronica][Procedura: Aggiornare le proprietà dei messaggi di posta elettronica]
-   [Procedura: Utilizzare servizi aggiuntivi forniti da SendGrid][Procedura: Utilizzare servizi aggiuntivi forniti da SendGrid]
-   [Passaggi successivi][Passaggi successivi]

## <a name="bkmk_WhatIsSendGrid"> </a>Informazioni sul servizio di posta elettronica SendGrid

SendGrid è un [servizio di posta elettronica basato sul cloud][servizio di posta elettronica basato sul cloud] che offre
[recapito affidabile di messaggi di posta elettronica transazionali][recapito affidabile di messaggi di posta elettronica transazionali], scalabilità e analisi in tempo reale, oltre ad API
 flessibili che agevolano l'integrazione personalizzata. Gli scenari di utilizzo comuni di SendGrid
includono:

-   Invio automatico di ricevute ai clienti
-   Amministrazione di liste di distribuzione per l'invio mensile
    ai clienti di volantini elettronici e offerte speciali
-   Raccolta di metriche in tempo reale per elementi quali indirizzi di posta elettronica bloccati e
    velocità di risposta al cliente
-   Generazione di report per agevolare l'identificazione delle tendenze
-   Inoltro di richieste dei clienti
-   Notifiche di posta elettronica dall'applicazione

Per altre informazioni, visitare il sito <http://sendgrid.com>.

## <a name="bkmk_CreateSendGridAcct"> </a>Creazione di un account SendGrid

[WACOM.INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="bkmk_HowToUseJavax"> </a>Procedura: Utilizzare le librerie javax.mail

Ottenere le librerie javax.mail, ad esempio da
<http://www.oracle.com/technetwork/java/javamail> e importarle
nel codice. Ad alto livello, la procedura per usare la libreria javax.mail
per l'invio di un messaggio di posta elettronica mediante SMTP è la seguente:

1.  Specificare i valori SMTP, compreso il server SMTP,
    che per SendGrid è smtp.sendgrid.net.

        public class MyEmailer {
           private static final String SMTP_HOST_NAME = "smtp.sendgrid.net";
           private static final String SMTP_AUTH_USER = "your_sendgrid_username";
           private static final String SMTP_AUTH_PWD = "your_sendgrid_password";

           public static void main(String[] args) throws Exception{
              new MyEmailer().SendMail();
           }

           public void SendMail() throws Exception
           {
              Properties properties = new Properties();
              properties.put("mail.transport.protocol", "smtp");
              properties.put("mail.smtp.host", SMTP_HOST_NAME);
              properties.put("mail.smtp.port", 587);
              properties.put("mail.smtp.auth", "true");
              // …

2.  Estendere la classe <span class="auto-style1">javax.mail.Authenticator</span>
    e nella propria implementazione del metodo
    <span class="auto-style1">getPasswordAuthentication</span>
    restituire il nome utente e la password di SendGrid.

        private class SMTPAuthenticator extends javax.mail.Authenticator {
        public PasswordAuthentication getPasswordAuthentication() {
           String username = SMTP_AUTH_USER;
           String password = SMTP_AUTH_PWD;
           return new PasswordAuthentication(username, password);
        }

3.  Creare una sessione di posta autenticata mediante un oggetto
    <span class="auto-style1">javax.mail.Session</span>.

        Authenticator auth = new SMTPAuthenticator();
        Session mailSession = Session.getDefaultInstance(properties, auth);

4.  Creare il messaggio e assegnare i valori relativi ai campi **A**, **Da**, **Oggetto**
    e contenuto. Questa operazione è illustrata nella sezione [Procedura: Creare un messaggio di posta elettronica][Procedura: Creare un messaggio di posta elettronica].
5.  Inviare il messaggio tramite un oggetto
    <span class="auto-style1">javax.mail.Transport</span>. Questa operazione
    è illustrata nella sezione [Procedura: Inviare un messaggio di posta elettronica][Procedura: Inviare un messaggio di posta elettronica]
    .

## <a name="bkmk_HowToCreateEmail"> </a>Procedura: Creare un messaggio di posta elettronica

Il codice seguente illustra come specificare i valori per un messaggio di posta elettronica.

    MimeMessage message = new MimeMessage(mailSession);
    Multipart multipart = new MimeMultipart("alternative");
    BodyPart part1 = new MimeBodyPart();
    part1.setText("Hello, Your Contoso order has shipped. Thank you, John");
    BodyPart part2 = new MimeBodyPart();
    part2.setContent(
        "<p>Hello,</p>
        <p>Your Contoso order has <b>shipped</b>.</p>
        <p>Thank you,<br>John</br></p>",
        "text/html");
    multipart.addBodyPart(part1);
    multipart.addBodyPart(part2);
    message.setFrom(new InternetAddress("john@contoso.com"));
    message.addRecipient(Message.RecipientType.TO,
       new InternetAddress("someone@example.com"));
    message.setSubject("Your recent order");
    message.setContent(multipart);

## <a name="bkmk_HowToSendEmail"> </a>Procedura: Inviare un messaggio di posta elettronica

Il codice seguente illustra come inviare un messaggio di posta elettronica.

    Transport transport = mailSession.getTransport();
    // Connect the transport object.
    transport.connect();
    // Send the message.
    transport.sendMessage(message, message.getRecipients(Message.RecipientType.TO));
    // Close the connection.
    transport.close();

## <a name="bkmk_HowToAddAttachment"> </a>Procedura: Aggiungere un allegato

Il codice seguente illustra come aggiungere un allegato.

    // Local file name and path.
    String attachmentName = "myfile.zip";
    String attachmentPath = "c:\\myfiles\\"; 
    MimeBodyPart attachmentPart = new MimeBodyPart();
    // Specify the local file to attach.
    DataSource source = new FileDataSource(attachmentPath + attachmentName);
    attachmentPart.setDataHandler(new DataHandler(source));
    // This example uses the local file name as the attachment name.
    // They could be different if you prefer.
    attachmentPart.setFileName(attachmentName);
    multipart.addBodyPart(attachmentPart);

## <a name="bkmk_HowToUseFilters"> </a>Procedura: Utilizzare filtri per abilitare piè di pagina, monitoraggio e analisi

SendGrid fornisce funzionalità di posta elettronica aggiuntive mediante
l'uso di *filtri*. Si tratta di impostazioni che si possono aggiungere a un messaggio di posta elettronica per
abilitare funzionalità specifiche, ad esempio il monitoraggio dei clic, Google
Analytics, il monitoraggio delle sottoscrizioni e così via. Per un elenco completo dei filtri,
vedere [Impostazioni dei filtri][Impostazioni dei filtri].

-   Il codice seguente illustra come inserire un filtro per piè di pagina
    che provoca la visualizzazione di testo in formato HTML in fondo al messaggio di posta elettronica inviato.

        message.addHeader("X-SMTPAPI", 
            "{\"filters\": 
            {\"footer\": 
            {\"settings\": 
            {\"enable\":1,\"text/html\": 
            \"<html><b>Thank you</b> for your business.</html>\"}}}}");

-   Un altro esempio di filtro è quello per il monitoraggio dei clic. Si supponga ad esempio che
    il testo del messaggio di posta elettronica contenga un collegamento ipertestuale come il seguente
    e che si desideri monitorare il tasso di clic:

        messagePart.setContent(
            "Hello,
            <p>This is the body of the message. Visit 
            <a href='http://www.contoso.com'>http://www.contoso.com</a>.</p>
            Thank you.", 
            "text/html");

-   Per abilitare il monitoraggio dei clic, utilizzare il codice seguente:

        message.addHeader("X-SMTPAPI", 
            "{\"filters\": 
            {\"clicktrack\": 
            {\"settings\": 
            {\"enable\":1}}}}");

## <a name="bkmk_HowToUpdateEmail"> </a>Procedura: Aggiornare le proprietà dei messaggi di posta elettronica

È possibile sovrascrivere alcune proprietà dei messaggi di posta elettronica mediante **set*Property***
oppure aggiungerle usando **add*Property***.

Ad esempio, per specificare indirizzi **Rispondi a**, utilizzare il codice seguente:

    InternetAddress addresses[] = 
        { new InternetAddress("john@contoso.com"),
          new InternetAddress("wendy@contoso.com") };

    message.setReplyTo(addresses);

Per aggiungere un destinatario **Cc**, utilizzare il codice seguente:

    message.addRecipient(Message.RecipientType.CC, new 
    InternetAddress("john@contoso.com"));

## <a name="bkmk_HowToUseAdditionalSvcs"> </a>Procedura: Utilizzare servizi aggiuntivi forniti da SendGrid

SendGrid offre API basate sul Web che è possibile usare per sfruttare
altre funzionalità di SendGrid dall'applicazione Azure. Per
informazioni dettagliate, vedere la [documentazione dell'API SendGrid][documentazione dell'API SendGrid].

## <a name="bkmk_NextSteps"> </a>Passaggi successivi

A questo punto, dopo aver appreso le nozioni di base del servizio di posta elettronica SendGrid,
usare i collegamenti seguenti per ottenere altre informazioni.

-   Esempio in cui viene illustrato l'utilizzo di SendGrid in una distribuzione Azure: [Come inviare messaggi di posta elettronica usando SendGrid da Java in una distribuzione Azure][Come inviare messaggi di posta elettronica usando SendGrid da Java in una distribuzione Azure]
-   Informazioni sull'utilizzo di SendGrid con Java: <http://sendgrid.com/docs/Code_Examples/java.html>
-   Documentazione sull'API SendGrid: <http://sendgrid.com/docs/API_Reference/index.html>
-   Offerta speciale SendGrid per i clienti di Azure: <http://sendgrid.com/azure.html>

  [Passaggi successivi]: #bkmk_NextSteps
  [Informazioni sul servizio di posta elettronica SendGrid]: #bkmk_WhatIsSendGrid
  [Creare un account SendGrid]: #bkmk_CreateSendGridAcct
  [Procedura: Utilizzare le librerie javax.mail]: #bkmk_HowToUseJavax
  [Procedura: Creare un messaggio di posta elettronica]: #bkmk_HowToCreateEmail
  [Procedura: Inviare un messaggio di posta elettronica]: #bkmk_HowToSendEmail
  [Procedura: Aggiungere un allegato]: #bkmk_HowToAddAttachment
  [Procedura: Utilizzare filtri per abilitare piè di pagina, monitoraggio e analisi]: #bkmk_HowToUseFilters
  [Procedura: Aggiornare le proprietà dei messaggi di posta elettronica]: #bkmk_HowToUpdateEmail
  [Procedura: Utilizzare servizi aggiuntivi forniti da SendGrid]: #bkmk_HowToUseAdditionalSvcs
  [servizio di posta elettronica basato sul cloud]: http://sendgrid.com/solutions
  [recapito affidabile di messaggi di posta elettronica transazionali]: http://sendgrid.com/transactional-email
  [Impostazioni dei filtri]: http://sendgrid.com/docs/API_Reference/Web_API/filter_settings.html
  [documentazione dell'API SendGrid]: http://sendgrid.com/docs/API_Reference/index.html
  [Come inviare messaggi di posta elettronica usando SendGrid da Java in una distribuzione Azure]: ../store-sendgrid-java-how-to-send-email-example/
