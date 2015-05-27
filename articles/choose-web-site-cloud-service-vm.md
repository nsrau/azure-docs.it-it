<properties 
	pageTitle="Confronto tra Azure App Service, Servizi cloud e Macchine virtuali di Azure" 
	description="Informazioni su quando usare Azure App Service, Servizi cloud e Macchine virtuali di Azure per l'hosting di applicazioni Web."
	services="•app-service\web, virtual-machines, cloud-services" 
	documentationCenter="" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/29/2015" 
	ms.author="tdykstra"/>

# Confronto tra Azure App Service, Servizi cloud e Macchine virtuali di Azure

## Panoramica

Azure offre vari modi per ospitare i siti Web: [Azure App Service][], [Servizi cloud][] e [Macchine virtuali][]. In questo articolo vengono fornite informazioni utili per comprendere le opzioni disponibili ed effettuare la scelta appropriata per l'applicazione Web.

Azure App Service è la scelta migliore per la maggior parte delle app Web. La distribuzione e la gestione sono integrate nella piattaforma ed è possibile scalare rapidamente i siti per gestire carichi di traffico elevato; inoltre, il bilanciamento del carico e la gestione del traffico predefiniti offrono disponibilità elevata. È possibile spostare facilmente siti esistenti in Azure App Service con uno [strumento di migrazione online](https://www.migratetoazure.net/), usare un'app open source della raccolta di applicazioni Web oppure creare un nuovo sito tramite il framework e gli strumenti di propria scelta. La funzionalità[WebJobs][] semplifica l'aggiunta di elaborazione di processi in background all'app Web di servizio app.

Per esercitare un controllo maggiore sull'ambiente server Web, ad esempio poter accedere in remoto al server o configurare attività di avvio del server, Servizi cloud di Azure è in genere l'opzione migliore.

Se è presente un'applicazione esistente la cui esecuzione in Azure App Service o Servizi cloud di Azure richiederebbe modifiche sostanziali, è possibile scegliere Macchine virtuali di Azure allo scopo di semplificare la migrazione al cloud. Tuttavia, la corretta configurazione, la protezione e la gestione delle VM richiede molto più tempo e competenza IT rispetto a Azure App Service e a Servizi cloud. Se si sta valutando la possibilità di usare Macchine virtuali di Azure, assicurarsi di tenere conto delle operazioni di manutenzione continua necessarie per le attività di applicazione di patch, aggiornamento e gestione relative all'ambiente di VM.

Nel diagramma seguente viene illustrato il grado relativo di controllo rispetto alla facilità d'uso per ciascuna delle opzioni di hosting Web di Azure.

![ChoicesDiagram][ChoicesDiagram]

##<a name="scenarios"></a>Scenari e indicazioni

Di seguito sono riportati alcuni scenari applicativi comuni con indicazioni riguardo all'opzione di hosting Web di Azure più appropriata per ciascuno di essi.

- [Mi serve un front-end Web con funzionalità di elaborazione in background e un back-end di database per eseguire applicazioni aziendali integrate con risorse locali.](#onprem)
- [Ho bisogno di un modo affidabile per ospitare il mio sito Web aziendale che sia scalabile e offra una portata globale.](#corp)
- [Ho un'applicazione IIS6 in esecuzione su Windows Server 2003.](#iis6)
- [Sono un piccolo imprenditore e ho bisogno di ospitare il mio sito in modo economico ma tenendo sempre presente la possibilità di crescita futura.](#smallbusiness)
- [Sono un Web designer o un progettista grafico e voglio progettare e realizzare siti Web per i miei clienti.](#designer)
- [Sto migrando la mia applicazione multilivello con un front-end Web al cloud.](#multitier)
- [La mia applicazione dipende da ambienti Windows o Linux altamente personalizzati e voglio effettuarne il passaggio al cloud.](#custom)
- [Il mio sito usa software open source, e voglio ospitarlo in Azure.](#oss)
- [Ho un'applicazione line-of-business che deve connettersi alla rete aziendale.](#lob)
- [Voglio ospitare un'API REST o un servizio Web per client mobili.](#mobile)


### <a id="onprem"></a>Mi serve un front-end Web con funzionalità di elaborazione in background e un back-end di database per eseguire applicazioni aziendali integrate con risorse locali.

Azure App Service è un'ottima soluzione per applicazioni aziendali complesse. Consente di sviluppare app scalabili automaticamente su una piattaforma con carico bilanciato, è protetto con Active Directory e si connette alle risorse locali. Semplifica la gestione di tali app tramite un portale di gestione e API di qualità superiore e consente di ottenere informazioni sul modo in cui i clienti le usano con strumenti di analisi approfondita delle app. La nuova funzionalità [WebJobs][] consente di eseguire processi e attività in background nell'ambito del livello Web, mentre la connettività ibrida e le [funzionalità VNET](fundamentals-introduction-to-azure.md#networking/) semplificano la connessione alle risorse in sede. Azure App Service offre contratti di servizio con garanzia di disponibilità del 99,999% per le app Web e consente di:

* Eseguire le applicazioni in modo affidabile su una piattaforma cloud con funzionalità automatiche di riparazione e di applicazione di patch. 
* Scalare automaticamente in una rete globale di data center.
* Eseguire il backup e il ripristino per il ripristino di emergenza. 
* Essere conformi a ISO, SOC2 e PCI.
* Effettuare l'integrazione con Active Directory.

### <a id="corp"></a> Ho bisogno di un modo affidabile per ospitare il mio sito Web aziendale che sia scalabile e offra una portata globale. 

Azure App Service è un'ottima soluzione per l'hosting di siti Web aziendali. Consente la scalabilità rapida e semplice delle app Web per soddisfare la domanda in una rete globale di data center. Offre portata locale, tolleranza di errore e gestione intelligente del traffico, il tutto su una piattaforma che fornisce strumenti di gestione di livello superiore, consentendo di ottenere informazioni sullo stato di integrità e sul traffico del sito in modo semplice e rapido. Azure App Service offre contratti di servizio con garanzia di disponibilità del 99,999% per le app Web e consente di:

* Eseguire i siti Web in modo affidabile su una piattaforma cloud con funzionalità automatiche di riparazione e di applicazione di patch. 
* Scalare automaticamente in una rete globale di data center.
* Eseguire il backup e il ripristino per il ripristino di emergenza. 
* Gestire i log e il traffico con strumenti integrati. 
* Essere conformi a ISO, SOC2 e PCI.
* Effettuare l'integrazione con Active Directory.

### <a id="iis6"></a> Ho un'applicazione IIS6 in esecuzione su Windows Server 2003.

Azure App Service consente di evitare facilmente i costi associati alla migrazione delle precedenti applicazioni IIS6. Microsoft ha creato [strumenti di migrazione e indicazioni dettagliate facili da usare](https://www.movemetowebsites.net/) che consentono di controllare la compatibilità e di identificare eventuali modifiche da apportare. L'integrazione con Visual Studio, TFS e con strumenti CMS comuni semplifica la distribuzione delle applicazioni IIS6 direttamente nel cloud. Dopo la distribuzione il portale di gestione di Azure fornisce solidi strumenti di gestione che consentono di ridurre il numero di istanze per gestire i costi e soddisfare la domanda in base alle esigenze. Con lo strumento di migrazione è possibile:

* Eseguire la migrazione dell'applicazione Web Windows Server 2003 al cloud in modo rapido e semplice.
* Scegliere di lasciare in locale il database SQL collegato per creare un'applicazione ibrida. 
* Spostare automaticamente il database SQL insieme all'applicazione legacy. 

### <a id="smallbusiness"></a>Sono un piccolo imprenditore e ho bisogno di ospitare il mio sito in modo economico ma tenendo sempre presente la possibilità di crescita futura.

Azure App Service è un'ottima soluzione per questo scenario, perché è possibile iniziare a usarlo gratuitamente e quindi aggiungere funzionalità man mano che si rendano necessarie. Ogni sito Web gratuito include un dominio fornito da Azure (*nome_società*.azurewebsites.net) e la piattaforma include strumenti di distribuzione e gestione integrati nonché una raccolta di applicazioni che consentono di iniziare a usare facilmente la funzionalità. Sono disponibili molti altri servizi e opzioni di ridimensionamento che consentono l'evoluzione del sito in base all'aumento della domanda da parte degli utenti. Con il Azure App Service, è possibile:

- Iniziare con il livello gratuito e quindi aggiungere risorse in base alle esigenze.
- Usare la raccolta di applicazioni per configurare rapidamente applicazioni Web diffuse, come WordPress.
- Aggiungere funzionalità e servizi di Azure aggiuntivi all'applicazione, in base alle necessità.
- Proteggere l'app Web con HTTPS.

### <a id="designer"></a> Sono un Web designer o un progettista grafico e voglio progettare e realizzare siti Web per i miei clienti

Per gli sviluppatori e i progettisti Azure App Service si integra facilmente con una varietà di framework e strumenti, include il supporto della distribuzione per Git e FTP e offre una stretta integrazione con strumenti e servizi quali Visual Studio e il database SQL. Con il servizio app, è possibile:

- Utilizzare strumenti da riga di comando per [attività automatiche][scripting].
- Lavorare con i linguaggi più utilizzati, ad esempio [.Net][dotnet], [PHP][], [Node.js][nodejs] e [Python][].
- Selezionare tre diversi livelli di scalabilità per aumentare la capacità fino a livelli molto elevati.
- Integrare con altri servizi di Azure, ad esempio [database SQL][sqldatabase], [bus di servizio][servicebus] e [archiviazione][] oppure offerte di partner da [Azure Store][azurestore], ad esempio MySQL e MongoDB.
- Effettuare l'integrazione con strumenti quali Visual Studio, Git, WebMatrix, WebDeploy, TFS e FTP.

### <a id="multitier"></a>Sto migrando la mia applicazione multilivello con un front-end Web al cloud

Se si esegue un'applicazione multilivello, ad esempio un server Web che si connette a un database, Azure App Service è una buona opzione che offre una stretta integrazione con il database SQL di Azure. È anche possibile usare la funzionalità Processi Web per l'esecuzione di processi back-end.

Scegliere Servizi cloud per uno o più livelli se è necessario esercitare un controllo maggiore sull'ambiente server, ad esempio poter accedere in remoto al server o configurare attività di avvio del server.

Scegliere Macchine virtuali per uno o più livelli per usare la propria immagine di macchina o per eseguire software o servizi server che non è possibile configurare su Servizi cloud.

### <a id="custom"></a>La mia applicazione dipende da ambienti Windows o Linux altamente personalizzati e voglio effettuarne il passaggio al cloud.

Se l'applicazione richiede la complessa installazione o configurazione di software e sistema operativo, Macchine virtuali è probabilmente la soluzione migliore. Con Macchine virtuali è possibile:

- Utilizzare la raccolta di macchine virtuali per iniziare con un sistema operativo, come Windows o Linux, e quindi personalizzarlo in base alle esigenze dell'applicazione. 
- Creare e caricare un'immagine personalizzata di un server locale esistente per eseguirla su una macchina virtuale in Azure. 

### <a id="oss"></a>Il mio sito usa software open source e voglio ospitarlo in Azure

Se il framework open source è supportato su Azure App Service, le lingue e i framework richiesti dall'applicazione sono configurati automaticamente. Il servizio app consente di:

- Utilizzare molti linguaggi open source tra i più diffusi, ad esempio [.Net][dotnet], [PHP][], [Node.js][nodejs] e [Python][]. 
- Configurare WordPress, Drupal, Umbraco, DNN e molte altre applicazioni Web di terze parti. 
- Eseguire la migrazione di un'applicazione esistente o crearne una nuova dalla raccolta di applicazioni. 

Se il framework open source non è supportato in App Service, è possibile eseguirlo sulle altre due opzioni di hosting Web di Azure. Con Servizi cloud si usano le attività di avvio per installare e configurare qualsiasi software open source necessario che viene eseguito in Windows. Con Macchine virtuali il software viene installato e configurato sull'immagine della macchina, che può essere basata su Windows o su Linux.

### <a id="lob"></a>Ho un'applicazione line-of-business che deve connettersi alla rete aziendale

Per creare un'applicazione line-of-business, il sito Web potrebbe richiedere l'accesso diretto a servizi o dati nella rete aziendale. Ciò è possibile su Siti Web, Servizi cloud e Macchine virtuali usando il [servizio Azure App Service](/services/virtual-network/). Nel servizio app è possibile usare la nuova [funzionalità di integrazione VNET](http://azure.microsoft.com/blog/2014/09/15/azure-websites-virtual-network-integration/), che consente l'esecuzione delle applicazioni Azure come se queste si trovassero sulla rete aziendale.

### <a id="mobile"></a>Voglio ospitare un'API REST o un servizio Web per client mobili

I servizi Web basati su HTTP consentono di supportare una vasta gamma di client, inclusi client mobili. Framework come API Web ASP.NET si integrano con Visual Studio per semplificare la creazione e l'uso di servizi REST. Tali servizi sono esposti da un endpoint Web, pertanto, per il supporto di questo scenario, è possibile usare qualsiasi tecnica di hosting Web in Azure. Servizio app è tuttavia un'ottima scelta per l'hosting di API REST. Con il servizio app, è possibile:

- Creare rapidamente un sito Web per ospitare il servizio Web HTTP in uno dei data center Azure distribuiti a livello globale.
- Eseguire la migrazione di servizi esistenti o crearne di nuovi.
- Soddisfare i requisiti del contratto di servizio per la disponibilità con una sola istanza oppure scalare orizzontalmente su più macchine dedicate. 
- Utilizzare il sito pubblicato per fornire API REST per qualsiasi client HTTP, compresi quelli mobili.

Inoltre, Azure App Service dispone di una nuova funzionalità di anteprima per le API REST: app per le API. Per altre informazioni sulle app per le API, vedere [Informazioni sulle app per le API](app-service-api-apps-why-best-platform.md).

##<a name="features"></a>Confronto delle funzionalità

Nella tabella seguente vengono confrontate le funzionalità del Servizio app, Servizi cloud e Macchine virtuali per aiutare i clienti a operare la scelta più adatta. Per informazioni aggiornate sul contratto di servizio per ciascuna opzione, vedere i [contratti di servizio di Azure](/support/legal/sla/).

<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">Funzionalità</th>
   <th align="left" valign="middle">Servizio app (app Web)</th>
   <th align="left" valign="middle">Servizi cloud (ruoli Web)</th>
   <th align="left" valign="middle">Macchine virtuali</th>
   <th align="left" valign="middle">Note</th>
</tr>
<tr>
   <td valign="middle"><p>Distribuzione quasi istantanea</p></td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
   <td valign="middle"></td>
   <td valign="middle">La distribuzione di un'applicazione o di un aggiornamento di un'applicazione in un Servizio cloud o la creazione di una VM persistente richiede come minimo diversi minuti; la distribuzione di un'applicazione in un app Web richiede pochi secondi.</td>
</tr>
<tr>
   <td valign="middle"><p>Aumento della dimensione delle macchine senza ridistribuzione</p></td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
   <td valign="middle"></td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>Le istanze del server Web condividono contenuti e configurazione, quindi non occorre ridistribuire o riconfigurare man mano che si scala.</p></td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
   <td valign="middle"></td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>Più ambienti di distribuzione (produzione e gestione temporanea)</p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>Gestione automatica dell'aggiornamento del sistema operativo</p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>Commutazione di piattaforma trasparente (è possibile passare facilmente tra 32 bit e 64 bit)</p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>Distribuzione codice con GIT, FTP</p></td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>Distribuzione codice con distribuzione Web</p></td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
   <td valign="middle">X</td>
   <td valign="middle">Servizi cloud supporta l'uso della distribuzione Web per distribuire gli aggiornamenti a singole istanze del ruolo. Tuttavia, non è possibile usarlo per la distribuzione iniziale di un ruolo e se si usa la distribuzione Web per un aggiornamento è necessario effettuare la distribuzione separata a ciascuna istanza di un ruolo. Sono necessarie più istanze per qualificarsi per il contratto di servizio di Servizi cloud per gli ambienti di produzione.</td>
</tr>
<tr>
   <td valign="middle"><p>Supporto WebMatrix</p></td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>Accesso a servizi quali bus di servizio, archiviazione, database SQL</p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>Hosting del livello Web o dei servizi Web di un'architettura multilivello</p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>Hosting del livello intermedio di un'architettura multilivello</p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">App Web del servizio app può ospitare facilmente un livello intermedio API REST e la funzionalità <a href="http://go.microsoft.com/fwlink/?linkid=390226">WebJobs</a> può ospitare processi di elaborazione in background. È possibile eseguire WebJobs in un sito Web dedicato per ottenere la scalabilità indipendente per il livello. L'anteprima [app per le API](app-service-api-apps-why-best-platform.md) AL'anteprima [app per le API](app-service-api-apps-why-best-platform.md) API fornisce anche altre funzionalità per l'hosting di servizi REST.PI fornisce anche altre funzionalità per l'hosting di servizi REST.</td>
</tr>
<tr>
   <td valign="middle"><p>Supporto integrato di MySQL distribuito come servizio</p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">Servizi cloud può integrare MySQL distribuito come servizio tramite le offerte di ClearDB, ma non come parte del flusso di lavoro del portale di gestione.</td>
</tr>
<tr>
   <td valign="middle"><p>Supporto per ASP.NET, ASP classico, Node.js, PHP, Python</p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>Scalabilità orizzontale a più istanze senza ridistribuzione</p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">Macchine virtuali consente la scalabilità orizzontale su più istanze; tuttavia, i servizi in esecuzione nelle macchine devono essere scritti per gestire tale scalabilità. È necessario configurare un bilanciamento del carico per instradare le richieste tra le macchine e creare un gruppo di affinità per impedire i riavvii simultanei di tutte le istanze a causa degli errori di manutenzione o hardware.</td>
</tr>
<tr>
   <td valign="middle"><p>Supporto per SSL</p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">Per App Web del servizio app, SSL per i nomi di dominio personalizzati è supportato solo nella modalità Basic e Standard. Per informazioni sull'uso di SSL con app Web, vedere <a href="../web-sites-configure-ssl-certificate/">Configurazione di un certificato SSL per un sito Web di Azure</a>.</td>
</tr>
<tr>
   <td valign="middle"><p>Integrazione di Visual Studio</p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>Debug remoto</p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>Distribuzione codice con TFS</p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>Isolamento rete con la <a href="/services/virtual-network/">rete virtuale di Azure</a></p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">Vedere anche <a href="/blog/2014/09/15/azure-websites-virtual-network-integration/">Integrazione della rete virtuale di Siti Web di Azure</a></td>
</tr>
<tr>
   <td valign="middle"><p>Supporto per <a href="/services/traffic-manager/">Gestione traffico di Azure</a></p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>Monitoraggio integrato degli endpoint</p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>Accesso al server tramite Desktop remoto</p></td>
   <td valign="middle"></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>Installazione di qualsiasi MSI personalizzato</p></td>
   <td valign="middle"></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>Possibilità di definire/eseguire le attività di avvio</p></td>
   <td valign="middle"></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>Possibilità di essere in ascolto di eventi ETW</p></td>
   <td valign="middle"></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
</tr>
</table>


> [AZURE.NOTE]Per iniziare a usare Azure App Service prima di registrare un account, andare a la pagina all'indirizzo <a href="https://trywebsites.azurewebsites.net/">https://trywebsites.azurewebsites.net</a>, in cui è possibile creare immediatamente e gratuitamente un'app iniziale temporanea in Azure App Service. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.


## <a id="nextsteps"></a> Passaggi successivi

Per altre informazioni sulle tre opzioni di hosting Web, vedere le risorse seguenti:

* [Introduzione ad Azure](fundamentals-introduction-to-azure.md)
* [Opzioni di hosting di calcolo fornite da Azure](fundamentals-application-models.md)

Per iniziare a usare le opzioni scelte per l'applicazione, vedere le risorse seguenti:

* [Siti Web di Azure](/documentation/services/app-service/)
* [Servizi cloud di Azure](/documentation/services/cloud-services/)
* [Macchine virtuali di Azure](/documentation/services/virtual-machines/)

[ChoicesDiagram]: ./media/choose-web-site-cloud-service-vm/Websites_CloudServices_VMs_3.png
[Azure App Service]: /services/app-service/
[Servizi cloud]: http://go.microsoft.com/fwlink/?LinkId=306052
[Macchine virtuali]: http://go.microsoft.com/fwlink/?LinkID=306053
[ClearDB]: http://www.cleardb.com/
[WebJobs]: http://go.microsoft.com/fwlink/?linkid=390226&clcid=0x409
[Configuring an SSL certificate for an Azure Website]: http://www.windowsazure.com/develop/net/common-tasks/enable-ssl-web-site/
[azurestore]: http://www.windowsazure.com/gallery/store/
[scripting]: http://www.windowsazure.com/documentation/scripts/?services=web-sites
[dotnet]: http://www.windowsazure.com/develop/net/
[nodejs]: http://www.windowsazure.com/develop/nodejs/
[PHP]: http://www.windowsazure.com/develop/php/
[Python]: http://www.windowsazure.com/develop/python/
[servicebus]: http://www.windowsazure.com/documentation/services/service-bus/
[sqldatabase]: http://www.windowsazure.com/documentation/services/sql-database/
[archiviazione]: http://www.windowsazure.com/documentation/services/storage/

<!--HONumber=54-->