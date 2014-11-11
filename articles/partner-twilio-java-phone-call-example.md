<properties linkid="develop-java-how-to-twilio-phone-call" urlDisplayName="How to Make a Phone Call from Twilio in Java" pageTitle="How to Make a phone call from Twilio (Java) - Azure" metaKeywords="Azure Twilio call, Twilio call website, Azure Twilio Java" description="Learn how to make a phone call from a web page using Twilio in a Java application on Azure." metaCanonical="" services="" documentationCenter="Java" title="How to Make a Phone Call Using Twilio in a Java Application on Azure" authors="MicrosoftHelp@twilio.com; robmcm" videoId="" scriptId="" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="MicrosoftHelp@twilio.com; robmcm" />

# Come effettuare una chiamata tramite Twilio in un'applicazione Java in Azure

Nell'esempio seguente viene illustrato come è possibile usare Twilio per effettuare una chiamata da una pagina Web ospitata in Azure. L'applicazione risultante chiederà all'utente di inserire i valori relativi alla chiamata telefonica, come illustrato nella schermata seguente.

![Modulo di chiamata di Azure con Twilio e Java][Modulo di chiamata di Azure con Twilio e Java]

Per usare il codice in questo argomento è necessario eseguire le operazioni seguenti:

1.  Ottenere un account e un token di autenticazione Twilio. Per informazioni sui prezzi di Twilio, visitare la pagina [][]<http://www.twilio.com/pricing></a>. Per effettuare l'iscrizione, visitare la pagina [][1]<https://www.twilio.com/try-twilio></a>. Per informazioni sull'API fornita da Twilio, vedere [][2]<http://www.twilio.com/api></a>.
2.  Verificare il proprio numero di telefono come ID chiamante in uscita presso Twilio. Per informazioni su come verificare il numero di telefono, vedere [][3]<https://www.twilio.com/user/account/phone-numbers/verified#></a>. In alternativa, anziché usare un numero esistente, è possibile acquistare un numero di telefono Twilio.
    Ai fini di questo esempio, usare il numero di telefono verificato come valore **From** di callform.php illustrato più avanti.
3.  Ottenere il file JAR di Twilio. All'indirizzo [][4]<https://github.com/twilio/twilio-java></a> è possibile scaricare i file di origine disponibili in GitHub e creare un file JAR personalizzato o scaricarne uno precompilato, con o senza dipendenze.
    Il codice in questo argomento è stato scritto usando il JAR precompilato TwilioJava-3.3.8-with-dependencies.
4.  Aggiungere il file JAR al percorso di compilazione Java.
5.  Se si usa Eclipse per creare l'applicazione Java, includere il JAR di Twilio nel file WAR per la distribuzione dell'applicazione usando l'assembly di distribuzione di Eclipse. Se non si utilizza Eclipse per creare l'applicazione Java, verificare che il JAR di Twilio sia incluso nello stesso ruolo di Azure dell'applicazione Java e che sia stato aggiunto al percorso della classe dell'applicazione.
6.  Verificare che l'archivio chiavi cacerts del JDK contenga il certificato Equifax Secure Certificate Authority con ID digitale MD5 67:CB:9D:C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4 (il numero di serie è 35:DE:F4:CF e l'ID digitale SHA1 è D2:32:09:AD:23:D3:14:23:21:74:E4:0D:7F:9D:62:13:97:86:63:3A). Si tratta del certificato dell'Autorità di certificazione (CA) per il servizio [][5]<https://api.twilio.com></a>, che viene chiamato quando si utilizzano le API Twilio. Per informazioni sull'aggiunta del certificato CA all'archivio cacerts del JDK, vedere [Aggiunta di un certificato all'archivio certificati CA di Java][Aggiunta di un certificato all'archivio certificati CA di Java].

Se non si utilizza Eclipse, è inoltre consigliabile consultare l'argomento [Creazione di un'applicazione Hello World usando il plug-in Azure per Eclipse con Java (da Microsoft Open Technologies)][Creazione di un'applicazione Hello World usando il plug-in Azure per Eclipse con Java (da Microsoft Open Technologies)] o acquisire familiarità con altre tecniche per l'hosting di applicazioni Java in Azure.

## Creare un modulo Web per l'esecuzione di una chiamata

Nel codice seguente viene illustrato come creare un modulo Web per recuperare i dati utente per l'esecuzione di una chiamata. Ai fini di questo esempio, è stato creato un nuovo progetto Web dinamico denominato **TwilioCloud** ed è stato aggiunto il file JSP **callform.jsp**.

    <%@ page language="java" contentType="text/html; charset=ISO-8859-1"
        pageEncoding="ISO-8859-1" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
    <title>Automated call form</title>
    </head>
    <body>
     <p>Fill in all fields and click <b>Make this call</b>.</p>
     <br/>
      <form action="makecall.jsp" method="post">
       <table>
         <tr>
           <td>To:</td>
           <td><input type="text" size=50 name="callTo" value="" />
           </td>
         </tr>
         <tr>
           <td>From:</td>
           <td><input type="text" size=50 name="callFrom" value="" />
           </td>
         </tr>
         <tr>
           <td>Call message:</td>
           <td><input type="text" size=400 name="callText" value="Hello. This is the call text. Good bye." />
           </td>
         </tr>
         <tr>
           <td colspan=2><input type="submit" value="Make this call" />
           </td>
         </tr>
       </table>
     </form>
     <br/>
    </body>
    </html>

## Creare il codice per l'esecuzione della chiamata

Il codice seguente, chiamato quando l'utente completa il modulo visualizzato da callform.jsp, crea il messaggio di chiamata e genera la chiamata. Ai fini di questo esempio, il file JSP è denominato **makecall.jsp** ed è stato aggiunto al processo **TwilioCloud**. Nel codice seguente sostituire i valori segnaposto assegnati a **accountSID** e **authToken** con il proprio account e il token di autenticazione Twilio.

    <%@ page language="java" contentType="text/html; charset=ISO-8859-1"
    import="java.util.*"
    import="com.twilio.*"
    import="com.twilio.sdk.*"
    import="com.twilio.sdk.resource.factory.*"
    import="com.twilio.sdk.resource.instance.*"
    pageEncoding="ISO-8859-1" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
    <title>Call processing happens here</title>
    </head>
    <body>
        <b>This is my make call page.</b><p/>
     <%
    try 
    {
         // Use your account SID and authentication token instead
         // of the placeholders shown here.
         String accountSID = "your_twilio_account";
         String authToken = "your_twilio_authentication_token";
     
         // Instantiate an instance of the Twilio client.     
         TwilioRestClient client;
         client = new TwilioRestClient(accountSID, authToken);

         // Retrieve the account, used later to retrieve the CallFactory.
         Account account = client.getAccount();

         // Display the client endpoint. 
         out.println("<p>Using Twilio endpoint " + client.getEndpoint() + ".</p>");
     
         // Display the API version.
         String APIVERSION = TwilioRestClient.DEFAULT_VERSION;
         out.println("<p>Twilio client API version is " + APIVERSION + ".</p>");

         // Retrieve the values entered by the user.
         String callTo = request.getParameter("callTo");  
         // The Outgoing Caller ID, used for the From parameter,
         // must have previously been verified with Twilio.
         String callFrom = request.getParameter("callFrom");
         String userText = request.getParameter("callText");
     
         // Replace spaces in the user's text with '%20', 
         // to make the text suitable for a URL.
         userText = userText.replace(" ", "%20");
     
         // Create a URL using the Twilio message and the user-entered text.
         String Url="http://twimlets.com/message";
         Url = Url + "?Message%5B0%5D=" + userText;
     
         // Display the message URL.
         out.println("<p>");
         out.println("The URL is " + Url);
         out.println("</p>");

         // Place the call From, To and URL values into a hash map. 
         HashMap<String, String> params = new HashMap<String, String>();
         params.put("From", callFrom);
         params.put("To", callTo);
         params.put("Url", Url);
     
         CallFactory callFactory = account.getCallFactory();
         Call call = callFactory.create(params);
         out.println("<p>Call status: " + call.getStatus()  + "</p>"); 
    } 
    catch (TwilioRestException e) 
    {
        out.println("<p>TwilioRestException encountered: " + e.getMessage() + "</p>");
        out.println("<p>StackTrace: " + e.getStackTrace().toString() + "</p>");
    }
    catch (Exception e) 
    {
        out.println("<p>Exception encountered: " + e.getMessage() + "");
        out.println("<p>StackTrace: " + e.getStackTrace().toString() + "</p>");
    }
    %>
    </body>
    </html>

Oltre ad eseguire la chiamata, makecall.jsp visualizza l'endpoint Twilio, la versione dell'API e lo stato della chiamata. Nella schermata di seguito viene visualizzato un esempio:

![Risposta a chiamata di Azure tramite Twilio e Java][Risposta a chiamata di Azure tramite Twilio e Java]

## Esecuzione dell'applicazione

DI seguito è riportata la procedura generale per eseguire l'applicazione. Per istruzioni dettagliate, vedere [Creazione di un'applicazione Hello World usando il plug-in Azure per Eclipse con Java (da Microsoft Open Technologies)][Creazione di un'applicazione Hello World usando il plug-in Azure per Eclipse con Java (da Microsoft Open Technologies)].

1.  Esportare il file WAR TwilioCloud nella cartella **approot** di Azure.
2.  Modificare **startup.cmd** per decomprimere il file WAR TwilioCloud.
3.  Compilare l'applicazione per l'emulatore di calcolo.
4.  Avviare la distribuzione nell'emulatore di calcolo.
5.  Aprire un browser ed eseguire **http://localhost:8080/TwilioCloud/callform.jsp**.
6.  Immettere i valori nel modulo, fare clic su **Make this call** e quindi visualizzare i risultati in makecall.jsp.

Quando si è pronti a distribuire l'applicazione in Azure, ricompilare per la distribuzione nel cloud, distribuire in Azure ed eseguire http://*your_hosted_name*.cloudapp.net/TwilioCloud/callform.jsp nel browser, sostituendo *your_hosted_name* con il proprio valore.

## Passaggi successivi

Questo codice ha lo scopo di illustrare le funzionalità di base dell'utilizzo di Twilio con Java in Azure. Prima di eseguire la distribuzione in Azure in produzione, può essere necessario aggiungere ulteriori funzionalità per la gestione degli errori o per altri scopi. Ad esempio:

-   Anziché usare un modulo Web, è possibile usare l'archiviazione BLOB o un database SQL di Azure per l'archiviazione di numeri di telefono e testo delle chiamate. Per informazioni sull'utilizzo dei BLOB di archiviazione di Azure in Java, vedere [Come usare il servizio di archiviazione BLOB da Java][Come usare il servizio di archiviazione BLOB da Java]. Per informazioni sull'utilizzo dei database SQL in Java, vedere [Utilizzo di database SQL in Java][Utilizzo di database SQL in Java].
-   È possibile usare **RoleEnvironment.getConfigurationSettings** per recuperare l'ID e il token di autenticazione dell'account Twilio dalle impostazioni di configurazione della distribuzione, anziché impostare i valori come hardcoded in makecall.jsp. Per informazioni sulla classe **RoleEnvironment**, vedere [Utilizzo della libreria di runtime del servizio Azure in JSP][Utilizzo della libreria di runtime del servizio Azure in JSP] e la documentazione sul pacchetto della libreria di runtime del servizio Azure all'indirizzo [][6]<http://dl.windowsazure.com/javadoc></a>.
-   Il codice makecall.jsp assegna un URL fornito da Twilio, <http://twimlets.com/message></a>, alla variabile [][7][Url][7]</a>. Tale URL fornisce una risposta TwiML (Twilio Markup Language) che indica a Twilio in che modo eseguire la chiamata. Ad esempio, la risposta TwiML restituita può contenere un verbo **\<Say\>**, che offre una versione parlata del testo al destinatario della chiamata. Anziché usare l'URL fornito da Twilio, è possibile creare un servizio personalizzato per rispondere alla richiesta di Twilio. Per altre informazioni, vedere [Come usare Twilio per le funzionalità voce ed SMS in Java][Come usare Twilio per le funzionalità voce ed SMS in Java]. Per altre informazioni su TwiML, visitare la pagina [][8]<http://www.twilio.com/docs/api/twiml></a> e per altre informazioni su **\<Say\>** e altri verbi Twilio, visitare la pagina [][9]<http://www.twilio.com/docs/api/twiml/say></a>.
-   Leggere le linee guida sulla sicurezza di Twilio all'indirizzo [][10]<https://www.twilio.com/docs/security></a>.

Per altre informazioni su Twilio, vedere [][11]<https://www.twilio.com/docs></a>.

## Vedere anche

-   [Come usare Twilio per le funzionalità voce ed SMS in Java][Come usare Twilio per le funzionalità voce ed SMS in Java]
-   [Aggiunta di un certificato all'archivio certificati CA Java][Aggiunta di un certificato all'archivio certificati CA di Java]

  [Modulo di chiamata di Azure con Twilio e Java]: ./media/partner-twilio-java-phone-call-example/WA_TwilioJavaCallForm.jpg
  []: http://www.twilio.com/pricing
  [1]: http://www.twilio.com/try-twilio
  [2]: http://www.twilio.com/api
  [3]: https://www.twilio.com/user/account/phone-numbers/verified#
  [4]: http://github.com/twilio/twilio-java
  [5]: http://api.twilio.com
  [Aggiunta di un certificato all'archivio certificati CA di Java]: ../java-add-certificate-ca-store
  [Risposta a chiamata di Azure tramite Twilio e Java]: ./media/partner-twilio-java-phone-call-example/WA_TwilioJavaMakeCall.jpg
  [Come usare il servizio di archiviazione BLOB da Java]: http://www.windowsazure.com/it-it/develop/java/how-to-guides/blob-storage/
  [Utilizzo di database SQL in Java]: http://msdn.microsoft.com/it-it/library/windowsazure/hh749029.aspx
  [Utilizzo della libreria di runtime del servizio Azure in JSP]: http://msdn.microsoft.com/it-it/library/windowsazure/hh690948.aspx
  [6]: http://dl.windowsazure.com/javadoc
  [7]: http://twimlets.com/message
  [Come usare Twilio per le funzionalità voce ed SMS in Java]: ../partner-twilio-java-how-to-use-voice-sms
  [8]: http://www.twilio.com/docs/api/twiml
  [9]: http://www.twilio.com/docs/api/twiml/say
  [10]: http://www.twilio.com/docs/security
  [11]: http://www.twilio.com/docs
