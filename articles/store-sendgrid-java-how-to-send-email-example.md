<properties linkid="store-requestform-preview" urlDisplayName="Request Azure Store Integration" pageTitle="How to Send Email Using SendGrid from Java in an Azure Deployment" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="How to Send Email Using SendGrid from Java in an Azure Deployment" authors="waltpo" solutions="" manager="" editor="mollybos" videoId="" scriptId="" />

Come inviare messaggi di posta elettronica utilizzando SendGrid da Java in una distribuzione Azure
==================================================================================================

Nell'esempio seguente viene illustrato come è possibile utilizzare SendGrid per inviare messaggi di posta elettronica da una pagina Web ospitata in Azure. L'applicazione risultante chiederà all'utente di inserire i valori relativi al messaggio di posta elettronica, come illustrato nella schermata seguente.

![Modulo per la posta elettronica](./media/store-sendgrid-java-how-to-send-email-example/SendGridJavaEmailform.jpg)

Il messaggio di posta elettronica finale dovrebbe essere simile a quello riportato nella schermata seguente.

![Messaggio di posta elettronica](./media/store-sendgrid-java-how-to-send-email-example/SendGridJavaEmailSent.jpg)

Per utilizzare il codice in questo argomento è necessario eseguire le operazioni seguenti:

1.  Ottenere i file JAR di javax.mail, ad esempio da &lt;http://www.oracle.com/technetwork/java/javamail/index.html\>.
2.  Aggiungere i file JAR al percorso di compilazione Java.
3.  Se si utilizza Eclipse per creare l'applicazione Java, è possibile includere le librerie SendGrid nel file di distribuzione dell'applicazione (WAR) utilizzando l'assembly di distribuzione di Eclipse. Se non si utilizza Eclipse per creare l'applicazione Java, verificare che le librerie siano incluse nello stesso ruolo di Azure dell'applicazione Java e che sia stato aggiunto al percorso della classe dell'applicazione.

Per poter inviare i messaggi di posta elettronica è inoltre necessario disporre di un nome utente e una password di SendGrid. Per iniziare a utilizzare SendGrid, vedere [Come inviare messaggi di posta elettronica utilizzando SendGrid da Java](../store-sendgrid-java-how-to-send-email).

Se non si utilizza Eclipse, è inoltre consigliabile consultare l'argomento [Creazione di un'applicazione Hello World per Azure in Eclipse](http://msdn.microsoft.com/en-us/library/windowsazure/hh690944) o acquisire familiarità con altre tecniche per l'hosting di applicazioni Java in Azure.

Creare un modulo Web per l'invio di posta elettronica
-----------------------------------------------------

Nel codice seguente viene illustrato come creare un modulo Web per recuperare i dati utente per l'invio di posta elettronica. Per gli scopi di questa esercitazione il file JSP è denominato **emailform.jsp**.

    <%@ page language="java" contentType="text/html; charset=ISO-8859-1"
        pageEncoding="ISO-8859-1" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
    <title>Email form</title>
    </head>
    <body>
     <p>Fill in all fields and click <b>Send this email</b>.</p>
     <br/>
      <form action="sendemail.jsp" method="post">
       <table>
         <tr>
           <td>To:</td>
           <td><input type="text" size=50 name="emailTo">
           </td>
         </tr>
         <tr>
           <td>From:</td>
           <td><input type="text" size=50 name="emailFrom">
           </td>
         </tr>
         <tr>
           <td>Subject:</td>
           <td><input type="text" size=100 name="emailSubject" value="My email subject">
           </td>
         </tr>
         <tr>
           <td>Text:</td>
           <td><input type="text" size=400 name="emailText" value="Hello,<p>This is my message.</p>Thank you." />
               </td>
         </tr>
         <tr>
           <td>SendGrid user name:</td>
           <td><input type="text" name="sendGridUser">
           </td>
         </tr>
         <tr>
           <td>SendGrid password:</td>
           <td><input type="password" name="sendGridPassword">
           </td>
         </tr>
         <tr>
           <td colspan=2><input type="submit" value="Send this email">
           </td>
         </tr>
       </table>
     </form>
     <br/>
    </body>
    </html>

Creare il codice per l'invio della posta elettronica
----------------------------------------------------

Il codice seguente, chiamato quando si completa il modulo in emailform.jsp, crea il messaggio di posta elettronica e lo invia. Per gli scopi di questa esercitazione il file JSP è denominato **sendemail.jsp**.

	<%@ page language="java" contentType="text/html; charset=ISO-8859-1"
	    pageEncoding="ISO-8859-1" import="javax.activation.*, javax.mail.*, javax.mail.internet.*, java.util.Date, java.util.Properties" %>
	<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
	<html>
	<head>
	<meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
	<title>Email processing happens here</title>
	</head>
	<body>
	    <b>This is my send mail page.</b><p/>
	 <%
	 
	 final String sendGridUser = request.getParameter("sendGridUser");
	 final String sendGridPassword = request.getParameter("sendGridPassword");
	 
	 class SMTPAuthenticator extends Authenticator
	 {
	   public PasswordAuthentication getPasswordAuthentication()
	   {
	        String username = sendGridUser;
	        String password = sendGridPassword;
	      
	        return new PasswordAuthentication(username, password);   
	   }
	 }
	 try
	 {
	     
	     // The SendGrid SMTP server.
	     String SMTP_HOST_NAME = "smtp.sendgrid.net";
	
	     Properties properties;
	    
	     properties = new Properties();
	     
	     // Specify SMTP values.
	     properties.put("mail.transport.protocol", "smtp");
	     properties.put("mail.smtp.host", SMTP_HOST_NAME);
	     properties.put("mail.smtp.port", 587);
	     properties.put("mail.smtp.auth", "true");
	     
	     // Display the email fields entered by the user. 
	     out.println("Value entered for email Subject: " + request.getParameter("emailSubject") + "<br/>");        
	     out.println("Value entered for email      To: " + request.getParameter("emailTo") + "<br/>");
	     out.println("Value entered for email    From: " + request.getParameter("emailFrom") + "<br/>");
	     out.println("Value entered for email    Text: " + "<br/>" + request.getParameter("emailText") + "<br/>");
	
	     // Create the authenticator object.
	     Authenticator authenticator = new SMTPAuthenticator();
	     
	     // Create the mail session object.
	     Session mailSession;
	     mailSession = Session.getDefaultInstance(properties, authenticator);
	     
	     // Display debug information to stdout, useful when using the
	     // compute emulator during development.
	     mailSession.setDebug(true);
	
	     // Create the message and message part objects.
	     MimeMessage message;
	     Multipart multipart;
	     MimeBodyPart messagePart; 
	     
	     message = new MimeMessage(mailSession);
	     
	     multipart = new MimeMultipart("alternative");
	     messagePart = new MimeBodyPart();
	     messagePart.setContent(request.getParameter("emailText"), "text/html");
	     multipart.addBodyPart(messagePart);            
	
	     // Specify the email To, From, Subject and Content. 
	     message.setFrom(new InternetAddress(request.getParameter("emailFrom")));
	     message.addRecipient(Message.RecipientType.TO, new InternetAddress(request.getParameter("emailTo")));
	     message.setSubject(request.getParameter("emailSubject")); 
	     message.setContent(multipart);
	     
	     // Uncomment the following if you want to add a footer.
	     // message.addHeader("X-SMTPAPI", "{\"filters\": {\"footer\": {\"settings\": {\"enable\":1,\"text/html\": \"<html>This is my <b>email footer</b>.</html>\"}}}}");
	
	     // Uncomment the following if you want to enable click tracking.
	     // message.addHeader("X-SMTPAPI", "{\"filters\": {\"clicktrack\": {\"settings\": {\"enable\":1}}}}");
	     
	     Transport transport;
	     transport = mailSession.getTransport();
	     // Connect the transport object.
	     transport.connect();
	     // Send the message.
	     transport.sendMessage(message,  message.getRecipients(Message.RecipientType.TO));
	     // Close the connection.
	     transport.close();
	 
	    out.println("<p>Email processing completed.</p>");
	     
	 }
	 catch (Exception e)
	 {
	     out.println("<p>Exception encountered: " + 
	                        e.getMessage()     +
	                        "</p>");   
	 }
	%>
	
	</body>
	</html>

Oltre a inviare la posta elettronica emailform.jsp fornisce un risultato all'utente. Nella schermata di seguito viene visualizzato un esempio:

![Risultato invio posta elettronica](./media/store-sendgrid-java-how-to-send-email-example/SendGridJavaResult.jpg)

Passaggi successivi
-------------------

Distribuire l'applicazione sull'emulatore di calcolo e, dall'interno di un browser, eseguire emailform.jsp, immettere i valori nel modulo, fare clic su **Send this email** e quindi visualizzare i risultati in sendemail.jsp.

Questo codice ha lo scopo di illustrare come utilizzare SendGrid con Java in Azure. Prima di eseguire la distribuzione in Azure in produzione, può essere necessario aggiungere ulteriori funzionalità per la gestione degli errori o per altri scopi. Ad esempio:

-   Anziché utilizzare un modulo Web, è possibile utilizzare l'archiviazione BLOB o un database SQL di Azure per l'archiviazione di indirizzi e-mail e messaggi di posta elettronica. Per informazioni sull'utilizzo dei BLOB di archiviazione di Azure in Java, vedere [Come utilizzare il servizio di archiviazione BLOB da Java](http://www.windowsazure.com/en-us/develop/java/how-to-guides/blob-storage/). Per informazioni sull'utilizzo dei database SQL in Java, vedere [Utilizzo di database SQL in Java](http://www.windowsazure.com/en-us/develop/java/how-to-guides/using-sql-azure-in-java/).
-   È possibile utilizzare `RoleEnvironment.getConfigurationSettings` per recuperare il nome utente e la password di SendGrid dalle impostazioni di configurazione della distribuzione, anziché utilizzare il modulo Web per recuperare questi valori. Per informazioni sulla classe `RoleEnvironment` vedere [Utilizzo della libreria di runtime del servizio Azure in JSP](http://msdn.microsoft.com/en-us/library/windowsazure/hh690948) e la documentazione sul pacchetto della libreria di runtime del servizio Azure all'indirizzo &lt;http://dl.windowsazure.com/javadoc\>.
-   Per ulteriori informazioni sull'utilizzo di SendGrid in Java, vedere [Come inviare messaggi di posta elettronica utilizzando SendGrid da Java](../store-sendgrid-java-how-to-send-email).

