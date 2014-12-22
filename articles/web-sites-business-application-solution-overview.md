<properties urlDisplayName="Create a Line-of-Business Application on Azure Websites" pageTitle="Creare un'applicazione line-of-business in Siti Web di Azure" metaKeywords="Web Sites" description="This guide provides a technical overview of how to use Azure Websites to create intranet, line-of-business applications. This includes authentication strategies, service bus relay, and monitoring." umbracoNaviHide="0" disqusComments="1" editor="mollybos" manager="wpickett" title="Create a Line-of-Business Application on Azure Websites" authors="jroth" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jroth" />



# Creare un'applicazione line-of-business in Siti Web di Azure

In questa guida è disponibile una panoramica tecnica dell'uso di Siti Web di Azure per la creazione di applicazioni line-of-business. Ai fini di questo documento, si suppone di creare applicazioni Intranet da proteggere per l'uso aziendale interno. Le applicazioni aziendali presentano due caratteristiche specifiche: richiedono l'autenticazione, in genere in una directory aziendale, e di solito richiedono l'accesso o l'integrazione con dati e servizi locali. In questa guida viene descritto come creare applicazioni aziendali in [Siti Web di Azure][websitesoverview]. In alcune situazioni è tuttavia preferibile usare [Servizi cloud di Azure][csoverview] o [Macchine virtuali di Azure][vmoverview]. È importante esaminare le differenze tra queste opzioni nell'argomento [Confronto tra Siti Web, Servizi cloud e Macchine virtuali di Azure][chooseservice]. 

In questa guida vengono illustrati gli argomenti seguenti:

- [Valutazione dei vantaggi](#benefits)
- [Scelta di una strategia di autenticazione](#authentication)
- [Creazione di un sito Web di Azure che supporta l'autenticazione](#createintranetsite)
- [Uso del bus di servizio per l'integrazione con le risorse locali](#servicebusrelay)
- [Monitoraggio dell'applicazione](#monitor)

<div class="dev-callout">
<strong>Nota</strong>
<p>In questa guida vengono illustrate alcune delle aree e delle attività più comuni relative allo sviluppo di un sito .COM pubblico. In Siti Web di Azure sono tuttavia disponibili altre funzionalità che è possibile usare nelle implementazioni specifiche. Per informazioni dettagliate su queste funzionalità, vedere le guide relative alla <a href="http://www.windowsazure.com/it-it/manage/services/web-sites/global-web-presence-solution-overview/">presenza Web globale</a> e alle <a href="http://www.windowsazure.com/it-it/manage/services/web-sites/digital-marketing-campaign-solution-overview">campagne di marketing digitali</a>.</p>
</div>

##<a name="benefits"></a>Valutazione dei vantaggi
Poiché le applicazioni line-of-business sono in genere destinate agli utenti aziendali, è necessario considerare i motivi per cui usare il cloud piuttosto che l'infrastruttura e le risorse aziendali locali. Il passaggio al cloud comporta innanzitutto alcuni vantaggi, come la possibilità di adattare le applicazioni a carichi di lavoro dinamici. Si consideri ad esempio un'applicazione che gestisce le valutazioni delle prestazioni annuali. Per la maggior parte dell'anno, questo tipo di applicazione gestisce un traffico molto ridotto. Tuttavia, durante il periodo di verifica, il traffico raggiunge livelli elevati nelle aziende di grandi dimensioni. Azure offre opzioni che garantiscono all'azienda la scalabilità orizzontale per gestire il carico durante il periodo di verifica con traffico elevato e di risparmiare denaro tornando alla scala iniziale per la parte restante dell'anno. Un altro vantaggio del cloud è la possibilità di concentrarsi maggiormente sullo sviluppo delle applicazioni e meno sull'acquisizione e la gestione dell'infrastruttura.

Oltre a questi vantaggi standard, la collocazione di un'applicazione aziendale nel cloud garantisce maggiore supporto per i dipendenti e i partner e consente l'uso dell'applicazione ovunque. Non è necessario che gli utenti siano connessi alla rete aziendale per poter usare l'applicazione e il gruppo IT evita di dover implementare soluzioni complesse con proxy inverso. Sono disponibili molte opzioni di autenticazione per proteggere l'accesso alle applicazioni aziendali. Queste opzioni sono descritte nelle sezioni che seguono della presente guida.

##<a name="authentication"></a>Scelta di una strategia di autenticazione
Per lo scenario dell'applicazione aziendale, la strategia di autenticazione è una delle decisioni più importanti. Esistono varie opzioni:

- Usare il [servizio Azure Active Directory][adoverview]. È possibile usare questo servizio come directory autonoma oppure sincronizzarlo con un'installazione di Active Directory locale. Le applicazioni interagiscono quindi con Azure Active Directory per autenticare gli utenti. Per una panoramica di questo approccio, vedere [Uso di Azure Active Directory][adusing].
- Usare Macchine virtuali e Rete virtuale di Azure per installare Active Directory. In questo modo è possibile estendere un'installazione di Active Directory locale sul cloud. È inoltre possibile scegliere di usare Active Directory Federation Services (ADFS) per eseguire la federazione delle richieste di identità sul servizio Active Directory locale. L'autenticazione dell'applicazione di Azure arriva al servizio Active Directory locale attraverso ADFS. Per altre informazioni su questo approccio, vedere [Esecuzione di Windows Server Active Directory nelle macchine virtuali][adwithvm] e [Linee guida per la distribuzione di Active Directory di Windows Server in Macchine virtuali di Azure][addeployguidelines]. 
- Usare un servizio intermediario, come il [Servizio di controllo di accesso di Azure][acs2] (ACS), per autenticare gli utenti tramite più servizi di identità. Questa soluzione fornisce un'astrazione per eseguire l'autenticazione tramite Active Directory o un provider di identità diverso. Per altre informazioni, vedere [Uso di Controllo di accesso di Azure Active Directory][acsusing].

Per questo scenario di applicazione aziendale, la prima soluzione, in cui viene usato Azure Active Directory, offre il percorso più rapido per implementare una strategia di autenticazione per l'applicazione. La parte restante di questa guida riguarda Azure Active Directory. A seconda dei requisiti aziendali, è tuttavia possibile che sia più adatta una delle altre due soluzioni. Se ad esempio non è possibile sincronizzare le informazioni di identità nel cloud, la soluzione con ADFS potrebbe essere migliore. Se invece occorre supportare altri provider di identità, come Facebook, è consigliabile optare per il servizio di controllo di accesso.

Prima di iniziare la configurazione di un nuovo servizio Azure Active Directory, è necessario verificare se i servizi esistenti, come Office 365 o Windows Intune, usano già Azure Active Directory. In tal caso, è necessario associare la sottoscrizione esistente alla sottoscrizione di Azure. Per informazioni dettagliate, vedere [Che cos'è un tenant di Azure AD?][adtenant].

Se non si dispone di un servizio che usa già Azure Active Directory, è possibile creare una nuova directory nel portale di gestione. Usare la sezione **Active Directory** del portale di gestione per creare una nuova directory.

![BusinessApplicationsAzureAD][BusinessApplicationsAzureAD]

A questo punto, è possibile creare e gestire gli utenti, le applicazioni integrate e i domini.

![BusinessApplicationsADUsers][BusinessApplicationsADUsers]

Per una procedura dettagliata completa, vedere [Aggiunta del processo di accesso nell'applicazione Web tramite Microsoft Azure AD][adsso]. Se si usa questa nuova directory come risorsa autonoma, occorre procedere sviluppando le applicazioni da integrare con la directory. Se invece si dispone di identità di Active Directory locali, è in genere preferibile sincronizzarle con il nuovo servizio Azure Active Directory. Per altre informazioni su come eseguire questa operazione, vedere [Integrazione di directory][dirintegration].

Dopo aver creato e popolato la directory, è necessario creare le applicazioni Web che richiedono l'autenticazione e quindi integrarle con la directory. Questi passaggi sono descritti nelle due sezioni che seguono.

##<a name="createintranetsite"></a>Creare un sito Web di Azure che supporta l'autenticazione
Nello scenario relativo alla presenza Web globale vengono illustrate diverse opzioni per la creazione e la distribuzione di un nuovo sito Web. Se non si è esperti di Siti Web di Azure, è consigliabile [esaminare le informazioni disponibili][scenarioglobalweb]. Un'applicazione ASP.NET in Visual Studio è una scelta comune per un'applicazione Web Intranet che usa l'autenticazione di Windows. Uno dei motivi è l'integrazione e il supporto completo che ASP.NET e Visual Studio forniscono per questo scenario.

Quando si crea ad esempio un progetto MVC 4 ASP.NET in Visual Studio, è possibile scegliere di creare un'**applicazione Intranet** nella finestra di dialogo di creazione del progetto.

![BusinessApplicationsVSIntranetApp][BusinessApplicationsVSIntranetApp]

In questo modo, le impostazioni del progetto vengono modificate per supportare l'autenticazione di Windows. In particolare, nel file web.config l'attributo **mode** dell'elemento **authentication** è impostato su **Windows**. Questa modifica deve essere eseguita manualmente se si crea un progetto ASP.NET diverso, ad esempio un progetto Web Form, o se si usa un progetto esistente.

Per un progetto MVC, è inoltre necessario modificare due valori nella finestra delle proprietà del progetto. Impostare **Autenticazione Windows** su **Abilitata** e **Autenticazione anonima** su **Disabilitata**.

![BusinessApplicationsVSProperties][BusinessApplicationsVSProperties]

Per poter eseguire l'autenticazione con Azure Active Directory, è necessario registrare l'applicazione con la directory e quindi modificare la configurazione dell'applicazione per consentire la connessione. Questa operazione può essere eseguita in Visual Studio con uno dei prodotti seguenti:

- [Identity and Access Tool per Visual Studio](#identityandaccessforvs)
- [Strumenti di Microsoft ASP.NET per Azure Active Directory](#aspnettoolsforwaad)

###<a name="identityandaccessforvs"></a>Identity and Access Tool per Visual Studio
Un metodo consiste nell'usare lo strumento [Identity and Access Tool][identityandaccess] (disponibile per il download e l'installazione). Questo strumento si integra con Visual Studio nel menu di scelta rapida del progetto. Le istruzioni e le catture di schermata seguenti si riferiscono a Visual Studio 2012. Fare clic con il pulsante destro del mouse sul progetto e scegliere **Identity and Access**. Le impostazioni da configurare sono tre. Nella scheda **Provider** è necessario specificare il **percorso del documento dei metadati STS** e il valore **URI ID APP** (per ottenere questi valori, vedere la sezione [Registrazione di applicazioni in Azure Active Directory](#registerwaadapp)).

![BusinessApplicationsVSIdentityAndAccess][BusinessApplicationsVSIdentityAndAccess]

La modifica finale da apportare alla configurazione va fatta nella scheda **Configuration** della finestra di dialogo **Identity and Access**. Selezionare la casella di controllo **Enable web farm cookies**. Per una procedura dettagliata, vedere [Aggiunta del processo di accesso nell'applicazione Web tramite Microsoft Azure AD][adsso].

####<a name="registerwaadapp"></a>Registrazione di applicazioni in Azure Active Directory:
Per compilare la scheda **Providers** è necessario registrare l'applicazione con Azure Active Directory. Nella sezione **Active Directory** del portale di gestione di Azure selezionare la propria directory e passare alla scheda **Applicazioni**. È disponibile un'opzione per aggiungere il sito Web di Azure in base all'URL. Si tenga presente che, durante questa procedura, inizialmente si imposta l'URL sull'indirizzo localhost fornito per il debug in Visual Studio. In un secondo momento questo valore viene sostituito con l'URL effettivo del sito Web durante la distribuzione.

![BusinessApplicationsADIntegratedApps][BusinessApplicationsADIntegratedApps]

Una volta aggiunto, il portale fornisce sia il percorso del documento dei metadati STS (in **URL documento metadati federazione**) che il valore **URI ID APP**. Questi valori vengono usati nella scheda **Providers** della finestra di dialogo **Identity and Access** in Visual Studio. 

![BusinessApplicationsADAppAdded][BusinessApplicationsADAppAdded]

###<a name="aspnettoolsforwaad"></a>Strumenti di Microsoft ASP.NET per Azure Active Directory
Un metodo alternativo per eseguire le operazioni sopra descritte consiste nell'usare gli [strumenti di Microsoft ASP.NET per Azure Active Directory][aspnettools]. A questo scopo, fare clic sulla voce **Enable Azure Authentication** nel menu **Progetto** in Visual Studio. Verrà visualizzata una semplice finestra di dialogo in cui è necessario specificare l'indirizzo del dominio di Azure Active Directory (non l'URL dell'applicazione).

![BusinessApplicationsVSEnableAuth][BusinessApplicationsVSEnableAuth]

Se si è l'amministratore del dominio di Active Directory, selezionare la casella di controllo **Provision this application in the Azure AD**. L'applicazione verrà registrata con Active Directory. Se non si è l'amministratore, deselezionare la casella di controllo e immettere le informazioni da visualizzare all'amministratore. L'amministratore potrà usare il portale di gestione per creare un'applicazione integrata tramite la procedura descritta in precedenza nello strumento Identity and Access. Per la procedura dettagliata su come usare gli strumenti di ASP.NET per Azure Active Directory, vedere [Autenticazione di Azure][azureauthtutorial].

Quando si gestisce l'applicazione line-of-business, è possibile usare uno dei sistemi di controllo del codice sorgente supportati per la distribuzione. Tuttavia, a causa del livello elevato di integrazione di Visual Studio in questo scenario, è più probabile che il sistema di controllo del codice sorgente scelto sia Team Foundation Service (TFS). In tal caso, è necessario tenere presente che Siti Web di Azure fornisce l'integrazione con TFS. Nel portale di gestione passare alla scheda **Dashboard** del sito Web. Selezionare **Imposta distribuzione dal controllo del codice sorgente**. Seguire le istruzioni per usare TFS. 

![BusinessApplicationsDeploy][BusinessApplicationsDeploy]

##<a name="servicebusrelay"></a>Uso del bus di servizio per l'integrazione con le risorse locali
Molte applicazioni line-of-business devono integrarsi con dati e servizi locali. Esistono vari motivi per cui determinati tipi di dati non possono essere spostati nel cloud. Possono essere motivi pratici o normativi. Se si sta decidendo quali dati ospitare in Azure e quali lasciare nel sistema locale, è importante vedere le risorse disponibili nel [Centro protezione di Azure][trustcenter]. Le applicazioni Web ibride vengono eseguite in Azure e accedono a risorse che devono essere locali.

Quando si usano le macchine virtuali o i servizi cloud, è possibile usare una rete virtuale per connettere le applicazioni in Azure con una rete aziendale. Siti Web non supporta tuttavia le reti virtuali, pertanto la soluzione migliore per ottenere questo tipo di integrazione con Siti Web consiste nell'usare il [servizio di inoltro del bus di servizio di Azure][sbrelay]. Questo servizio consente alle applicazioni nel cloud di connettersi in modo sicuro ai servizi WCF in esecuzione in una rete aziendale. Il bus di servizio garantisce questa comunicazione senza aprire le porte del firewall. 

Nel diagramma che segue, sia le applicazioni nel cloud che il servizio WCF locale comunicano con il bus di servizio tramite uno spazio dei nomi creato in precedenza. Il servizio WCF locale può accedere a dati e servizi interni che non possono essere spostati nel cloud. Il servizio WCF registra un endpoint nello spazio dei nomi. Il sito Web in esecuzione in Azure si connette a questo endpoint anche nel bus di servizio. Per completare il passaggio, occorre solo che sia possibile eseguire richieste HTTP pubbliche in uscita.

![BusinessApplicationsServiceBusRelay][BusinessApplicationsServiceBusRelay]

Il bus di servizio connette quindi l'applicazione cloud al servizio WCF locale. Questo meccanismo fornisce l'architettura di base per la creazione di applicazioni ibride che usano servizi e risorse in Azure e locali. Per altre informazioni, vedere [Come usare il servizio Inoltro del bus di servizio][sbrelayhowto] e l'[Esercitazione sulla messaggistica inoltrata del bus di servizio][sbrelaytutorial]. Per una dimostrazione di questa tecnica, vedere l'esempio di [connessione di siti Web a un servizio locale tramite bus di servizio][enterprisepizza].

##<a name="monitor"></a>Monitoraggio dell'applicazione
Le applicazioni aziendali usufruiscono delle funzionalità standard dei siti Web, ad esempio la scalabilità e il monitoraggio. Per un'applicazione aziendale con un carico variabile durante giornate o orari specifici, la funzionalità Scalabilità automatica (anteprima) può aiutare a scalare orizzontalmente il sito e quindi ridurlo per un utilizzo efficiente delle risorse. Le opzioni di monitoraggio includono il monitoraggio degli endpoint e delle quote. Tutti questi scenari sono descritti più dettagliatamente negli scenari di [presenza Web globale][scenarioglobalweb] e [campagna di marketing digitale][scenariodigitalmarketing].

I requisiti di monitoraggio variano tra applicazioni line-of-business diverse con livelli diversi di importanza per l'azienda. Per le applicazioni cruciali, si consideri l'ipotesi di investire in una soluzione di monitoraggio di terze parti, come [New Relic][newrelic].

Le applicazioni line-of-business sono in genere gestite dal reparto IT. Se si verificano errori o comportamenti imprevisti, il personale IT può abilitare una registrazione più dettagliata e quindi analizzare i dati risultanti per identificare il problema. Nel portale di gestione passare alla scheda **Configura** e controllare le opzioni nelle sezioni **Diagnostica applicazioni** e **Diagnostica del sito**. 

![BusinessApplicationsDiagnostics][BusinessApplicationsDiagnostics]

Usare i vari log delle applicazioni e del sito per risolvere i problemi relativi al sito Web. Si noti che alcune delle opzioni indicano **File system**, che significa che i file di log vengono memorizzati nel file system del sito. È possibile accedere a questi log tramite FTP, Azure PowerShell o gli strumenti da riga di comando di Azure. Altre opzioni indicano **Archiviazione**. In questo caso, le informazioni vengono inviate all'account di archiviazione di Azure specificato. Per **Registrazione del server Web** è possibile anche specificare una quota del disco per il file system o criteri di conservazione per l'opzione di archiviazione. In questo modo, risulta impossibile archiviare una quantità di dati di registrazione che aumentano in modo indefinito.

![BusinessApplicationsDiagRetention][BusinessApplicationsDiagRetention]

Per altre informazioni su queste impostazioni di registrazione, vedere [Procedura: Configurare i log di diagnostica e download per un sito Web][configurediagnostics].

Oltre a visualizzare i log non elaborati tramite FTP o con le utilità di archiviazione, come Esplora archivi di Azure, è anche possibile visualizzare le informazioni dei log in Visual Studio. Per informazioni dettagliate sull'uso di questi log per la risoluzione dei problemi, vedere [Risoluzione dei problemi di Siti Web di Azure in Visual Studio][troubleshootwebsites].

##<a name="summary"></a>Riepilogo
Azure consente di ospitare applicazioni per Intranet sicure nel cloud. Azure Active Directory garantisce la possibilità di autenticare gli utenti, in modo che solo i membri dell'organizzazione possano accedere alle applicazioni. Il servizio Inoltro del bus di servizio offre un meccanismo per le applicazioni Web per comunicare con servizi e dati locali. Questo approccio di applicazione ibrida facilita la pubblicazione di un'applicazione aziendale nel cloud senza eseguire anche la migrazione di tutti i dati e i servizi dipendenti. Dopo la distribuzione, le applicazioni aziendali dispongono delle funzionalità standard di scalabilità e monitoraggio fornite da Siti Web di Azure. Per altre informazioni, vedere gli articoli tecnici seguenti.

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
   <td valign="middle"><strong>Creazione e distribuzione</strong></td>
   <td valign="top">- <a href ="http://www.windowsazure.com/it-it/develop/net/tutorials/get-started/">Distribuzione di un'applicazione Web ASP.NET in un sito Web di Azure</a><br/>- <a href="http://www.windowsazure.com/it-it/develop/net/tutorials/web-site-with-sql-database/">Distribuire un'applicazione ASP.NET MVC sicura in Azure</a></td>
</tr>
<tr>
   <td valign="middle"><strong>Autenticazione</strong></td>
   <td valign="top">- <a href ="http://www.windowsazure.com/it-it/manage/windows/fundamentals/identity/">Informazioni sulle opzioni di identità di Azure</a><br/>- <a href="http://www.windowsazure.com/it-it/documentation/services/active-directory/">Servizio Azure Active Directory</a><br/>- <a href="http://technet.microsoft.com/it-it/library/jj573650.aspx">Che cos'è un tenant di Azure AD?</a><br/>- <a href="http://msdn.microsoft.com/library/windowsazure/dn151790.aspx">Aggiunta del processo di accesso nell'applicazione Web tramite Microsoft Azure AD</a><br/>- <a href="http://www.asp.net/aspnet/overview/aspnet-and-visual-studio-2012/windows-azure-authentication">Esercitazione sull'autenticazione di Azure</a></td>
</tr>
<tr>
   <td valign="middle"><strong>Inoltro del bus di servizio</strong></td>
   <td valign="top">- <a href="http://www.windowsazure.com/it-it/develop/net/how-to-guides/service-bus-relay/">Come usare il servizio Inoltro del bus di servizio</a><br/>- <a href="http://msdn.microsoft.com/it-it/library/windowsazure/ee706736.aspx">Esercitazione sulla messaggistica inoltrata del bus di servizio</a></td>
</tr>
<tr>
   <td valign="middle"><strong>Monitoraggio</strong></td>
   <td valign="top">- <a href ="http://www.windowsazure.com/it-it/manage/services/web-sites/how-to-monitor-websites/">Come monitorare i siti Web</a><br/>- <a href="http://msdn.microsoft.com/library/windowsazure/dn306638.aspx">Procedura: Ricevere notifiche di avviso e gestire le relative regole in Azure</a><br/>- <a href="http://www.windowsazure.com/it-it/manage/services/web-sites/how-to-monitor-websites/#howtoconfigdiagnostics">Procedura: Configurare i log di diagnostica e download per un sito Web</a><br/>- <a href="http://www.windowsazure.com/it-it/develop/net/tutorials/troubleshoot-web-sites-in-visual-studio/">Risoluzione dei problemi di Siti Web di Azure in Visual Studio</a></td>
</tr>
</table>

  [websitesoverview]:/it-it/documentation/services/web-sites/
  [csoverview]:/it-it/documentation/services/cloud-services/
  [vmoverview]:/it-it/documentation/services/virtual-machines/
  [chooseservice]:/it-it/manage/services/web-sites/choose-web-app-service
  [scenarioglobalweb]:/it-it/manage/services/web-sites/global-web-presence-solution-overview/
  [scenariodigitalmarketing]:/it-it/manage/services/web-sites/digital-marketing-campaign-solution-overview
  [adoverview]:/it-it/documentation/services/active-directory/
  [adusing]:/it-it/manage/windows/fundamentals/identity/#ad
  [adwithvm]:/it-it/manage/windows/fundamentals/identity/#adinvm
  [addeployguidelines]:http://msdn.microsoft.com/it-it/library/windowsazure/jj156090.aspx
  [acs2]:http://msdn.microsoft.com/library/windowsazure/hh147631.aspx
  [acsusing]:/it-it/manage/windows/fundamentals/identity/#ac
  [adtenant]:http://technet.microsoft.com/it-it/library/jj573650.aspx
  [adsso]:http://msdn.microsoft.com/library/windowsazure/dn151790.aspx
  [dirintegration]:http://technet.microsoft.com/it-it/library/jj573653.aspx
  [identityandaccess]:http://visualstudiogallery.msdn.microsoft.com/e21bf653-dfe1-4d81-b3d3-795cb104066e
  [aspnettools]:http://go.microsoft.com/fwlink/?LinkID=282306
  [azureauthtutorial]:http://www.asp.net/aspnet/overview/aspnet-and-visual-studio-2012/windows-azure-authentication
  [trustcenter]:/it-it/support/trust-center/
  [sbrelay]:http://msdn.microsoft.com/it-it/library/windowsazure/jj860549.aspx
  [sbrelayhowto]:/it-it/develop/net/how-to-guides/service-bus-relay/
  [sbrelaytutorial]:http://msdn.microsoft.com/it-it/library/windowsazure/ee706736.aspx
  [enterprisepizza]:http://code.msdn.microsoft.com/windowsazure/Enterprise-Pizza-e2d8f2fa
  [newrelic]:http://newrelic.com/azure
  [configurediagnostics]:/it-it/manage/services/web-sites/how-to-monitor-websites/#howtoconfigdiagnostics
  [troubleshootwebsites]:/it-it/develop/net/tutorials/troubleshoot-web-sites-in-visual-studio/
  [BusinessApplicationsAzureAD]: ./media/web-sites-business-application-solution-overview/BusinessApplications_AzureAD.png
  [BusinessApplicationsADUsers]: ./media/web-sites-business-application-solution-overview/BusinessApplications_AD_Users.png
  [BusinessApplicationsVSIntranetApp]: ./media/web-sites-business-application-solution-overview/BusinessApplications_VS_IntranetApp.png
  [BusinessApplicationsVSProperties]: ./media/web-sites-business-application-solution-overview/BusinessApplications_VS_Properties.png
  [BusinessApplicationsVSIdentityAndAccess]: ./media/web-sites-business-application-solution-overview/BusinessApplications_VS_IdentityAndAccess.png
  [BusinessApplicationsADIntegratedApps]: ./media/web-sites-business-application-solution-overview/BusinessApplications_AD_IntegratedApps.png
  [BusinessApplicationsADAppAdded]: ./media/web-sites-business-application-solution-overview/BusinessApplications_AD_AppAdded.png
  [BusinessApplicationsVSEnableAuth]: ./media/web-sites-business-application-solution-overview/BusinessApplications_VS_EnableAuth.png
  [BusinessApplicationsDeploy]: ./media/web-sites-business-application-solution-overview/BusinessApplications_Deploy.png
  [BusinessApplicationsServiceBusRelay]: ./media/web-sites-business-application-solution-overview/BusinessApplications_ServiceBusRelay.png
  [BusinessApplicationsDiagnostics]: ./media/web-sites-business-application-solution-overview/BusinessApplications_Diagnostics.png
  [BusinessApplicationsDiagRetention]: ./media/web-sites-business-application-solution-overview/BusinessApplications_Diag_Retention.png
  
  
  
  
  
  
  
  
  
  
  
  
  
  




  



