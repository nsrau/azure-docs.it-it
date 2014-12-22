<properties urlDisplayName="Create a Global Web Presence on Azure Websites" pageTitle="Creare una presenza Web globale su Siti Web di Azure" metaKeywords="" description="This guide provides a technical overview of how to host your organization's (.COM) site on Azure Websites. This includes deployment, custom domains, SSL, and monitoring." metaCanonical="http://www.windowsazure.com/it-it/documentation/articles/web-sites-global-web-presence-solution-overview/" services="" documentationCenter="" title="Create a Global Web Presence on Azure Websites" authors="jroth" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jroth" />





# Creare una presenza Web globale su Siti Web di Azure

In questa guida vengono fornite informazioni tecniche generali su come ospitare il sito dell'organizzazione (.COM) su Azure. Questo scenario viene anche definito come presenza Web globale. La guida è incentrata sull'uso di [Siti Web di Azure][websitesoverview], in quanto questa piattaforma rappresenta il modo più veloce e semplice di creare, migrare, scalare e gestire un'applicazione Web su Azure. Tuttavia, alcuni requisiti dell'applicazione si prestano meglio ai [Servizi cloud di Azure][csoverview] o alle [Macchine virtuali di Azure][vmoverview] che eseguono IIS. Anche questi sono scelte eccellenti per ospitare applicazioni Web. Se si è nelle fasi iniziali della pianificazione, consultare il documento [Confronto tra Siti Web, Servizi cloud e Macchine virtuali di Azure][chooseservice]. In assenza di uno specifico requisito per l'uso dei Servizi cloud o delle Macchine virtuali, si consiglia di usare Siti Web di Azure per l'hosting della propria presenza Web globale. Nella parte restante del presente documento vengono fornite indicazioni sull'uso di Siti Web in questo scenario. 

In questa guida vengono illustrati gli argomenti seguenti:

- [Creare un sito Web di Azure](#createwebsite)
- [Distribuire il sito Web](#deploywebsite)
- [Aggiungere un dominio personalizzato](#customdomain)
- [Proteggere il sito Web con SSL](#ssl)
- [Monitorare il sito](#monitor)

<div class="dev-callout">
<strong>Nota</strong>
<p>In questa guida vengono illustrate alcune delle aree e delle attività più comuni relative allo sviluppo di un sito .COM pubblico. In Siti Web di Azure sono tuttavia disponibili altre funzionalità che è possibile usare nelle implementazioni specifiche. Per informazioni dettagliate su queste funzionalità, vedere le guide relative alle <a href="http://www.windowsazure.com/it-it/manage/services/web-sites/digital-marketing-campaign-solution-overview">campagne di marketing digitali</a> e alle <a href="http://www.windowsazure.com/it-it/manage/services/web-sites/business-application-solution-overview">applicazioni aziendali</a>.</p>
</div>

##<a name="createwebsite"></a>Creare un sito Web di Azure
È possibile creare un nuovo sito Web di Azure in molti modi differenti tramite il portale di gestione di Azure. Quando si fa clic sul pulsante **Nuovo** in basso nel portale, viene visualizzata la finestra di dialogo seguente:

![GlobalWebCreate][GlobalWebCreate]

Per la creazione di un nuovo sito Web sono disponibili tre opzioni: **Creazione rapida**, **Creazione personalizzata** e **Da raccolta**. Con ciascuna di tali opzioni occorre selezionare un'area Azure che si allinei alla maggioranza della base utenti.

Se si sta eseguendo la migrazione di un sito esistente, selezionare l'opzione **Creazione personalizzata** per creare o associare un database SQL o un database MySQL. Questa opzione offre inoltre la possibilità di specificare svariate opzioni di controllo del codice sorgente, come GitHub o Team Foundation Server (TFS). Se si gestisce già il proprio sito Web servendosi di un meccanismo di controllo del codice sorgente, questa opzione consente di configurare rapidamente il sito Web di Azure per la distribuzione.

L'opzione **Da raccolta** consente di configurare un nuovo sito con uno di più framework, ad esempio Drupal o WordPress. Ciò può rivelarsi utile per configurare rapidamente un nuovo sito che sia quindi possibile personalizzare con il framework prescelto.

Analogamente alla maggior parte dei servizi in Azure, è necessario selezionare un'area di Azure per il nuovo sito Web. Azure può contare su più aree dislocate sul territorio internazionale. Una volta distribuito in una qualsiasi area, il sito Web è accessibile a livello globale su Internet. La scelta di un numero superiore di aree offre tuttavia una maggiore flessibilità. Uno dei vantaggi più evidenti è la distribuzione dei siti nelle aree più vicine agli utenti. 

Per informazioni dettagliate sui passaggi necessari per creare un nuovo sito Web, vedere [Introduzione a Siti Web di Azure e ASP.NET][howtocreatewebsites].

##<a name="deploywebsite"></a>Distribuire il sito Web
Esistono molti modi per distribuire il proprio sito Web in Azure. Se è stato selezionato un framework dalla galleria è già stato distribuito un sito Web di partenza. Per fare progressi, tuttavia, è comunque necessario configurare un certo tipo di procedura di modifica e distribuzione. Tra le opzioni di distribuzione disponibili sono incluse le seguenti:

- Usare un client FTP.
- Eseguire la distribuzione dal controllo del codice sorgente.
- Pubblicare da Visual Studio.
- Pubblicare da [WebMatrix][webmatrix].

Ciascuna di queste opzioni è caratterizzata da vari punti di forza. La possibilità di pubblicare da un client FTP è una soluzione semplice e diretta per effettuare il push di nuovi file sul proprio sito. Essa garantisce inoltre che eventuali strumenti o processi di pubblicazione basati su FTP possano continuare a funzionare con Siti Web di Azure. Il controllo del codice sorgente offre il miglior tipo di controllo sulle versioni dei contenuti dei siti, perché consente di tracciare e pubblicare le modifiche nonché di eseguirne il rollback a versioni precedenti, qualora necessario. L'opzione di pubblicazione diretta da Visual Studio o da Web Matrix è una comodità per gli sviluppatori che usano uno dei due strumenti. Uno degli scenari più utili in relazione a questa funzionalità riguarda le fasi iniziali di un progetto o la creazione di un prototipo. In entrambi i casi, le frequenti operazioni di pubblicazione e test sono potenzialmente più convenienti se eseguite all'interno dell'ambiente di sviluppo. 

Molte delle attività di distribuzione qui descritte richiedono l'uso di informazioni contenute nel portale di gestione di Azure. Nel sito Web selezionare la scheda **Dashboard** e quindi cercare la sezione **Riepilogo rapido**. Nella schermata riportata di seguito sono illustrate svariate opzioni.

![GlobalWebQuickGlance][GlobalWebQuickGlance]

Alcuni strumenti di controllo codice sorgete e client FTP usano l'accesso con nome utente/password. Le credenziali di un nuovo sito Web non vengono create automaticamente. È tuttavia possibile eseguire facilmente questa operazione facendo clic su **Reimposta le credenziali di distribuzione**. Al termine, sarà possibile usare qualsiasi client FTP per la distribuzione del proprio sito Web tramite queste credenziali con l'opzione **Nome host FTP** sulla stessa pagina **Dashboard**.

![GlobalWebFTPSettings][GlobalWebFTPSettings]

Si noti che il nome nella sezione Utente FTP/distribuzione è una combinazione del nome del sito Web e del nome utente fornito dall'utente. Dunque, se l'indirizzo del proprio sito fosse "http://contoso.azurewebsite.net" e se il proprio nome utente fosse "mioutente", il nome utente per la distribuzione e l'FTP diverrebbe "contoso\mioutente".

È inoltre possibile scegliere di eseguire la distribuzione tramite un servizio di gestione del controllo codice sorgente, come GitHub o TFS Online. Fare clic sull'opzione **Imposta distribuzione dal controllo del codice sorgente**, quindi seguire le istruzioni relative al sistema o al servizio di controllo del codice sorgente preferito. Per istruzioni dettagliate sulla pubblicazione da un repository Git locale vedere [Pubblicazione da controllo del codice sorgente in Siti Web di Azure][publishingwithgit].

Se si pianifica di usare Visual Studio per creare e gestire il proprio sito, è possibile scegliere di pubblicare direttamente da Visual Studio. Uno dei metodi consiste nel fare clic sull'opzione **Scaricare il profilo di pubblicazione**. In tal modo sarà possibile salvare un file publishsettings che potrà quindi essere importato in Visual Studio per la pubblicazione sul Web. 

<div class="dev-callout">
<strong>Nota</strong>
<p>È importante mantenere il file <i>publishsettings</i> al sicuro e fuori dal controllo del codice sorgente, in quanto contiene nomi utente e password per la distribuzione nonché per eventuali stringhe di connessione ai database collegati.</p>
</div>

È inoltre possibile importare le informazioni di sottoscrizione direttamente in Visual Studio. Ad esempio, considerare un progetto ASP.NET locale in Visual Studio. Fare clic con il pulsante destro del mouse sul progetto Web e scegliere **Pubblica**. Il pulsante **Importa** nella finestra di dialogo **Pubblica sito Web** consente di importare un file contenente le impostazioni di sottoscrizione di Azure oppure il file publishsettings scaricato dal dashboard di Siti Web. Nella schermata riportata di seguito sono illustrate queste opzioni.

![GlobalWebVSPublish][GlobalWebVSPublish]

Per altre informazioni sulla distribuzione in Azure da Visual Studio vedere Distribuzione di un'applicazione Web ASP.NET in un sito Web di Azure. 

Un'ulteriore opzione per lo sviluppo e la distribuzione è WebMatrix nel portale di gestione di Azure.

![GlobalWebWebMatrix][GlobalWebWebMatrix]

Per altre informazioni su questa opzione, vedere Sviluppo e distribuzione di un sito Web con Microsoft WebMatrix][aspnetgetstarted].

Benché in questi passaggi siano indicate tutte le operazioni necessarie per la distribuzione del sito .COM, è consigliabile creare inoltre un piano di gestione del ciclo continuo di pubblicazione dei contenuti. Queste opzioni potrebbero variare dalla compilazione di una soluzione personalizzata alle periodiche ridistribuzioni di un sito che cambia in maniera non frequente fino a un sistema completo per la gestione dei contenuti (CMS). Se si sta creando un nuovo sito Web, si noterà che la galleria offre alcune opzioni per l'uso dei framework CMS esistenti, ad esempio [Drupal][drupal] o [Umbraco][umbraco].

##<a name="customdomain"></a>Aggiungere un dominio personalizzato
Se questa è la propria presenza Web globale, sarà opportuno associare il nome di dominio registrato al sito Web. Vi sono molti provider di terze parti che offrono servizi di registrazione del dominio. Ciascuno di essi supporta la creazione di diversi tipi di record DNS per la gestione del dominio. Un record DNS contribuisce a mappare un URL descrittivo, ad esempio "www.contoso.com", all'URL o all'indirizzo IP sul quale è ospitato il sito. 

<div class="dev-callout">
<strong>Nota</strong>
<p>Nella discussione seguente vi sono due tipi di record DNS di interesse. In primo luogo, un record CNAME può reindirizzare da un unico URL, ad esempio "www.contoso.com", a un URL differente, come "contoso.azurewebsites.net". In secondo luogo, un record A può mappare un URL, come "www.contoso.com", a un indirizzo IP, come 172.16.48.1.</p>
</div>

Per Siti Web di Azure è necessario prima creare un record CNAME sul sito Web di Azure. Questa impostazione viene configurata attraverso il sito del registrar di terze parti. Di seguito è riportato un esempio di record CNAME:

<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="top">Tipo</th>
   <th align="left" valign="top">Host</th>
   <th align="left" valign="top">Risposta</th>
   <th align="left" valign="top">TTL</th>
</tr>
<tr>
   <td valign="top"><strong>CNAME</strong></td>
   <td valign="top">www.contoso.com</td>
   <td valign="top">contoso.azurewebsites.net</td>
   <td valign="top">8000</td>
</tr>
</table>

Se il dominio è di nuova registrazione potrebbero volerci più giorni per la risoluzione su tutti i server DNS, che operano senza basarsi sulle voci DNS memorizzate nella cache. Se tuttavia il dominio è già esistente, la modifica CNAME dovrebbe verificarsi entro un minuto. Si noti che il record CNAME fornisce la mappatura tra il dominio (che deve essere qualificato da un alias di sottodominio, ad esempio "www") e l'URL del sito Web di Azure. Il record CNAME non include mai il prefisso "http://".

Nel portale di gestione di Azure verificare che l'esecuzione sia in modalità **Condiviso** o ****Standard nella scheda **Scala** (i domini personalizzati non sono supportati per i siti Web in modalità **Gratuito**). Passare quindi alla scheda **Configura** e fare clic sul pulsante **Gestisci domini**. In tal modo sarà possibile associare il sito Web al nome di dominio personalizzato. 

![GlobalWebWebMatrix][GlobalWebWebMatrix]

Prima di inserirlo in elenco, occorre creare un record CNAME presso il provider DNS per il proprio dominio personalizzato (www.contoso.com) che punta all'URL del sito Web di Azure (contoso.azurewebsites.net). Dopo la relativa propagazione sarà possibile immettere il dominio personalizzato nella finestra di dialogo illustrata nella schermata precedente. La presenza del record CNAME per www.contoso.com che punta a questo sito Web garantisce all'utente l'autorizzazione a usare tale nome di dominio con questo sito Web. A questo punto è possibile creare un record A con l'indirizzo IP nella parte inferiore della finestra di dialogo.

<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="top">Tipo</th>
   <th align="left" valign="top">Host</th>
   <th align="left" valign="top">Risposta</th>
   <th align="left" valign="top">TTL</th>
</tr>
<tr>
   <td valign="top"><strong>A</strong></td>
   <td valign="top">contoso.com</td>
   <td valign="top">172.16.48.1</td>
   <td valign="top">8000</td>
</tr>
</table>

Per altre informazioni, vedere [Configurazione di un nome di dominio personalizzato per un sito Web di Azure][customdns].

##<a name="ssl"></a>Proteggere il sito Web con SSL
Se il proprio sito contiene informazioni di sola lettura non sarà necessario fornire l'accesso protetto. Se tuttavia vengono raccolte informazioni sugli utenti, si pratica l'e-commerce o si gestiscono altri dati di natura sensibile, è consigliabile proteggere il sito. La sicurezza è un argomento importante e in questo documento non è possibile trattare tutte le procedure e le tecniche consigliate. È tuttavia importante sottolineare il processo di abilitazione di Secure Sockets Layer (SSL) per il sito Web. SSL consente agli utenti di connettersi al sito in maniera crittografata con indirizzi HTTPS invece di HTTP. Per usare SSL con Siti Web di Azure sono previste procedure specifiche. 

Siti Web di Azure fornisce automaticamente una connessione sicura all'URL effettivo del sito. Ad esempio, se l'indirizzo del proprio sito fosse http://contoso.azurewebsites.net, sarebbe possibile eseguire una connessione SSL modificando semplicemente "http" in "https", ad esempio **https**://contoso.azurewebsites.net.

Se invece si usa un nome di dominio personalizzato, sarà necessario caricare un certificato e abilitare il protocollo SSL tramite il portale di gestione di Azure per il proprio sito Web. Nella procedura seguente viene fornito un riepilogo di questo procedimento, ma le istruzioni dettagliate sono disponibili nell'argomento [Configurazione di un certificato SSL per un sito Web di Azure][ssl].

Occorre innanzitutto ottenere un certificato SSL da un'autorità di certificazione. Se si intende proteggere il proprio dominio con più sottodomini (ad esempio www.contoso.com e staging.contoso.com), sarà necessario ottenere un certificato jolly (*.contoso.com). Questo tipo di certificato può avere un costo maggiore, pertanto è necessario decidere se la flessibilità da esso offerta giustifica il costo.

Dopo aver ottenuto il certificato dall'apposita autorità non sarà possibile caricarlo semplicemente in Azure nello stesso formato, ma sarà necessario generare un file PFX mediante il comando openssl, che fa parte del progetto OpenSSL. Le sorgenti sono distribuite sul [sito Web OpenSSL][openssl], ma di solito è possibile reperire una versione precompilata dello strumento anche su Internet. Nell'esempio seguente vengono usati il certificato myserver.crt e il file di chiave privato myserver.key per creare un file PFX.

	openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt

Per caricare il certificato in Azure, aprire la scheda **Scala** e verificare che l'esecuzione sia in modalità **Standard**. Il protocollo SSL per i domini personalizzati non è supportato per le modalità **Gratuito** o **Condiviso**. Nella scheda **Configura** fare clic sul pulsante **Carica un certificato**.

![GlobalWebUplodateCert][GlobalWebUplodateCert]

Quindi, nella sezione **Associazioni SSL**, mappare il certificato al nome di dominio che protegge. Sono disponibili due opzioni per il mapping: SNI SSL e IP Based SSL.

![GlobalWebSSLBindings][GlobalWebSSLBindings]

L'opzione **SSL basato su IP** rappresenta il modo tradizionale di mappare l'indirizzo IP dedicato pubblico al nome di dominio e funziona con tutti i browser. L'opzione **SNI SSL** consente a più domini di condividere lo stesso indirizzo IP pur avendo diversi certificati SSL associati a ciascun dominio. SNI SSL non funziona con alcune versioni precedenti dei browser (per altre informazioni sulla compatibilità, vedere la [voce di Wikipedia su SNI SSL][sni]). Ad ogni certificato SSL è associata una tariffa mensile (ripartita per ogni ora) e i prezzi variano a seconda della scelta del protocollo SSL SNI o basato su IP. Per informazioni sui prezzi, consultare la pagina [Dettagli prezzi - Siti Web][sslpricing]. Per altre informazioni su questo procedimento, vedere [Configurazione di un certificato SSL per un sito Web di Azure][ssl].

##<a name="monitor"></a>Monitorare il sito
Quando il sito avrà cominciato a gestire attivamente le richieste degli utenti, è importante usare il monitoraggio. Ad esempio può essere opportuno sapere se il carico utenti sta causando un tempo di CPU elevato, che potrebbe indicare la necessità di scalare il sito. Altrimenti, l'inefficienza dell'applicazione potrebbe aumentare il tempo di risposta o produrre degli errori. In questa sezione vengono esaminate alcune delle funzionalità di monitoraggio incorporate nel portale di gestione di Azure.

La scheda **Monitora** contiene alcune metriche chiave per il proprio sito Web in formato di grafico. 

![GlobalWebMonitor1][GlobalWebMonitor1]

È possibile personalizzare le metriche in questo grafico tramite il pulsante Add Metrics.

![GlobalWebMonitor2][GlobalWebMonitor2]

È inoltre possibile abilitare il monitoraggio e gli avvisi relativi agli endpoint nei siti Web eseguiti in modalità **Standard**. Nella scheda **Configura** passare alla sezione **monitoraggio** e configurare un endpoint. Questo endpoint viene eseguito da una o più posizioni specificate dall'utente e periodicamente tenterà di accedere al sito Web. Vengono raccolti dati sui tempi e sugli errori. 

Nella scheda **Monitor** questo endpoint sembra visualizzare il tempo di risposta. Se si seleziona la metrica dell'endpoint sarà quindi possibile aggiungere una regola di avviso facendo clic sull'icona **Aggiungi regola**.

![GlobalWebMonitor3][GlobalWebMonitor3]

La regola consente di inviare e-mail agli amministratori o ad altri individui quando il tempo di risposta supera la soglia specificata.

![GlobalWebMonitor4][GlobalWebMonitor4]

Se si scopre che il sito necessita di ridimensionamento è possibile operare manualmente oppure tramite l'anteprima di Scalabilità automatica nella scheda **Scala**. In tale scheda sono disponibili opzioni sia di aumento (macchine dedicate più grandi) di riduzione (ulteriori istanze condivise o istanze dedicate delle stesse dimensioni). L'anteprima Autoscale supporta unicamente la scalabilità orizzontale. Per altri dettagli sul monitoraggio del sito Web vedere la sezione Scalabilità in base alla domanda da parte degli utenti dello scenario di [campagna marketing digitale][scenariodigitalmarketing]. Vedere anche [Come monitorare i siti Web][howtomonitor].

##<a name="summary"></a>Riepilogo
Per creare il sito della propria organizzazione (.COM) le attività standard includono la scelta di un framework di sviluppo, la creazione del sito, la distribuzione, l'assegnazione del dominio personalizzato e il monitoraggio. Nei siti in cui è necessario proteggere i dati degli utenti è fortemente consigliato il protocollo SSL. In questo articolo sono state fornite informazioni generali sull'esecuzione di queste attività in Siti Web di Azure. Per altre informazioni, vedere gli articoli tecnici seguenti citati nel documento.

<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="top">Area</th>
   <th align="left" valign="top">Risorse</th>
</tr>
<tr>
   <td valign="middle"><strong>Pianificazione</strong></td>
   <td valign="top">- <a href="http://www.windowsazure.com/it-it/manage/services/web-sites/choose-web-app-service">Confronto tra Siti Web, Servizi Cloud e Macchine virtuali di Azure</a></td>
</tr>
<tr>
   <td valign="middle"><strong>Creazione</strong></td>
   <td valign="top">- <a href="http://azure.microsoft.com/it-it/documentation/articles/web-sites-dotnet-get-started/">Introduzione a Siti Web di Azure e ASP.NET</a></td>
</tr>
<tr>
   <td valign="middle"><strong>Distribuire</strong></td>
   <td valign="top">- <a href="http://www.windowsazure.com/it-it/develop/net/common-tasks/publishing-with-git/">Pubblicazione da controllo del codice sorgente in Siti Web di Azure</a><br/>- <a href="http://www.windowsazure.com/it-it/develop/net/tutorials/get-started/">Distribuzione di un'applicazione Web ASP.NET in un sito Web di Azure</a><br/>- <a href="http://www.windowsazure.com/it-it/develop/net/tutorials/website-with-webmatrix/">Sviluppare e distribuire un sito Web con Microsoft WebMatrix</a></td>
</tr>
<tr>
   <td valign="middle"><strong>Domini personalizzati</strong></td>
   <td valign="top">- <a href="http://www.windowsazure.com/it-it/develop/net/common-tasks/custom-dns-web-site/">Configurazione di un nome di dominio personalizzato per un sito Web di Azure</a></td>
</tr>
<tr>
   <td valign="middle"><strong>SSL</strong></td>
   <td valign="top">- <a href="http://www.windowsazure.com/it-it/develop/net/common-tasks/enable-ssl-web-site/">Configurazione di un certificato SSL per un sito Web di Azure</a></td>
</tr>
<tr>
   <td valign="middle"><strong>Monitoraggio</strong></td>
   <td valign="top">- <a href="http://www.windowsazure.com/it-it/manage/services/web-sites/how-to-monitor-websites/">Come monitorare i siti Web</a></td>
</tr>
</table>

  [websitesoverview]:/it-it/documentation/services/web-sites/
  [csoverview]:/it-it/documentation/services/cloud-services/
  [vmoverview]:/it-it/documentation/services/virtual-machines/
  [chooseservice]:/it-it/manage/services/web-sites/choose-web-app-service
  
  
  [scenariodigitalmarketing]:/it-it/manage/services/web-sites/digital-marketing-campaign-solution-overview
  [howtocreatewebsites]:/it-it/documentation/articles/web-sites-dotnet-get-started
  [webmatrix]:http://www.microsoft.com/web/webmatrix/
  [publishingwithgit]:/it-it/develop/net/common-tasks/publishing-with-git/
  [aspnetgetstarted]:/it-it/develop/net/tutorials/get-started/
  [drupal]:https://drupal.org/
  [umbraco]:http://umbraco.com/
  [customdns]:/it-it/develop/net/common-tasks/custom-dns-web-site/
  [ssl]:/it-it/develop/net/common-tasks/enable-ssl-web-site/
  [openssl]:http://www.openssl.org/
  [sni]:http://en.wikipedia.org/wiki/Server_Name_Indication
  [sslpricing]:/it-it/pricing/details/web-sites/#service-ssl
  [howtomonitor]:/it-it/manage/services/web-sites/how-to-monitor-websites/
  
 [GlobalWebCreate]: ./media/web-sites-global-web-presence-solution-overview/GlobalWeb_Create.png
  [GlobalWebQuickGlance]: ./media/web-sites-global-web-presence-solution-overview/GlobalWeb_QuickGlance.png
  [GlobalWebMonitor1]: ./media/web-sites-global-web-presence-solution-overview/GlobalWeb_Monitor1.png
  [GlobalWebMonitor2]: ./media/web-sites-global-web-presence-solution-overview/GlobalWeb_Monitor2.png
  [GlobalWebMonitor3]: ./media/web-sites-global-web-presence-solution-overview/GlobalWeb_Monitor3.png
  [GlobalWebMonitor4]: ./media/web-sites-global-web-presence-solution-overview/GlobalWeb_Monitor4.png
  [GlobalWebVSPublish]: ./media/web-sites-global-web-presence-solution-overview/GlobalWeb_VS_Publish.png
  [GlobalWebSSLBindings]: ./media/web-sites-global-web-presence-solution-overview/GlobalWeb_SSL_Bindings.png
  [GlobalWebUplodateCert]: ./media/web-sites-global-web-presence-solution-overview/GlobalWeb_Uplodate_Cert.png
  [GlobalWebCustomDomain]: ./media/web-sites-global-web-presence-solution-overview/GlobalWeb_CustomDomain.png
  [GlobalWebWebMatrix]: ./media/web-sites-global-web-presence-solution-overview/GlobalWeb_WebMatrix.png
  [GlobalWebFTPSettings]: ./media/web-sites-global-web-presence-solution-overview/GlobalWeb_FTPSettings.png
  
  
  
  
  
  
  
  
  
  
  
