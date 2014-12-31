<properties pageTitle="Confronto tra Siti Web, Servizi cloud e Macchine virtuali di Azure" metaKeywords="Cloud Services, Virtual Machines, Web Sites" description="Learn when to use Azure Websites, Cloud Services, and Virtual Machines for hosting web applications." metaCanonical="" services="web-sites,virtual-machines,cloud-services" documentationCenter="" title="Azure Websites, Cloud Services, and Virtual Machines comparison" authors="tdykstra" solutions="" manager="wpickett" editor="jimbe" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/24/2014" ms.author="tdykstra" />

# Confronto tra Siti Web, Servizi cloud e Macchine virtuali di Azure

## Panoramica

Azure offre diverse modalità di hosting dei siti Web: [Siti Web][], [Servizi cloud][] e [Macchine virtuali][] di Azure. In questo articolo vengono fornite informazioni utili per comprendere le opzioni disponibili ed effettuare la scelta appropriata per l'applicazione Web.

Siti Web di Azure è la scelta migliore per la maggior parte delle app Web. La distribuzione e la gestione sono integrate nella piattaforma ed è possibile scalare rapidamente i siti per gestire carichi di traffico elevato; inoltre, il bilanciamento del carico e la gestione del traffico predefiniti offrono disponibilità elevata. È possibile spostare facilmente siti esistenti in Siti Web di Azure con uno [strumento di migrazione online](https://www.migratetoazure.net/), usare un'app open source dalla raccolta di applicazioni Web o creare un nuovo sito usando il framework e gli strumenti desiderati. La funzionalità [Processi Web][] semplifica l'aggiunta di elaborazione di processi in background all'app. 

Per esercitare un controllo maggiore sull'ambiente server Web, ad esempio poter accedere in remoto al server o configurare attività di avvio del server, Servizi cloud di Azure è in genere l'opzione migliore.

Se è presente un'applicazione esistente la cui esecuzione in Siti Web di Azure o Servizi cloud di Azure richiederebbe modifiche sostanziali, è possibile scegliere Macchine virtuali di Azure allo scopo di semplificare la migrazione al cloud. Tuttavia, per le corrette configurazione, protezione e manutenzione macchine virtuali sono necessari più tempo e competenze IT rispetto a Siti Web e Servizi cloud di Azure. Nel valutare la possibilità di usare Macchine virtuali di Azure, è necessario tenere conto delle continue attività di manutenzione necessarie per correggere, aggiornare e gestire l'ambiente delle macchine virtuali.  

Nel diagramma seguente viene illustrato il grado relativo di controllo rispetto alla facilità d'uso per ciascuna delle opzioni di hosting Web di Azure. 

![ChoicesDiagram][ChoicesDiagram]

## Sommario

- [Scenari e consigli](#scenarios)
- [Confronto tra le funzionalità](#features)
- [Passaggi successivi](#nextsteps)

##<a name="scenarios"></a>Scenari e indicazioni

Di seguito sono riportati alcuni scenari applicativi comuni con indicazioni riguardo all'opzione di hosting Web di Azure più appropriata per ciascuno di essi.

- [È necessario un front-end Web con elaborazione in background e un back-end di database per l'esecuzione di applicazioni integrate con risorse locali.](#onprem)
- [È necessario un metodo affidabile per ospitare il sito Web aziendale in modo da garantire scalabilità adeguata e portata globale.](#corp)
- [Viene eseguita un'applicazione IIS6 su Windows Server 2003.](#iis6)
- [Si è titolari di una piccola impresa il cui sito deve essere ospitato usando un metodo economico, ma che tenga presente della possibilità di crescita in futuro.](#smallbusiness)
- [In qualità di Web designer o progettista grafico, si creano siti Web per i propri clienti.](#designer)
- [Si sta eseguendo la migrazione dell'applicazione multilivello con un front-end Web nel cloud.](#multitier)
- [L'applicazione dipende da ambienti Windows o Linux altamente personalizzati e si vuole spostarla nel cloud.](#custom)
- [Il sito usa software open source e si vuole ospitarlo in Azure.](#oss)
- [Si possiede un'applicazione line-of-business che deve connettersi alla rete aziendale.](#lob)
- [Si vuole ospitare un'API REST o un servizio Web per client mobili.](#mobile)


### <a id="onprem"></a> Mi serve un front-end Web con funzionalità di elaborazione in background e un back-end di database per eseguire applicazioni aziendali integrate con risorse locali.

Siti Web di Azure è un'ottima soluzione per applicazioni aziendali complesse. Consente di sviluppare app scalabili automaticamente su una piattaforma con carico bilanciato, è protetto con Active Directory e si connette alle risorse locali. Semplifica la gestione di tali app tramite un portale di gestione e API di qualità superiore e consente di ottenere informazioni sul modo in cui i clienti le usano con strumenti di analisi approfondita delle app. La nuova funzionalità [Processi Web][] permette di eseguire processi e attività in background come parte del livello Web, mentre la connettività ibrida e le [funzionalità di rete virtuale](../fundamentals-introduction-to-azure/#networking/) semplificano la connessione alle risorse locali. Siti Web di Azure offre contratti di servizio con garanzia di disponibilità del 99,999% e permette di:

* Eseguire le applicazioni in modo affidabile su una piattaforma cloud con funzionalità automatiche di riparazione e di applicazione di patch. 
* Scalare automaticamente in una rete globale di data center.
* Eseguire il backup e il ripristino per il ripristino di emergenza. 
* Soddisfare la conformità agli standard ISO, SOC2 e PCI.
* Effettuare l'integrazione con Active Directory.

### <a id="corp"></a> Ho bisogno di un modo affidabile per ospitare il mio sito Web aziendale che sia scalabile e offra una portata globale. 

Siti Web di Azure è un'ottima soluzione per l'hosting di siti Web aziendali. Consente la scalabilità rapida e semplice dei siti per soddisfare la domanda in una rete globale di data center. Offre portata locale, tolleranza di errore e gestione intelligente del traffico, il tutto su una piattaforma che fornisce strumenti di gestione di livello superiore, consentendo di ottenere informazioni sullo stato di integrità e sul traffico del sito in modo semplice e rapido. Siti Web di Azure offre contratti di servizio con garanzia di disponibilità del 99,999% e permette di:

* Eseguire i siti Web in modo affidabile su una piattaforma cloud con funzionalità automatiche di riparazione e di applicazione di patch. 
* Scalare automaticamente in una rete globale di data center.
* Eseguire il backup e il ripristino per il ripristino di emergenza. 
* Gestire i log e il traffico con strumenti integrati. 
* Soddisfare la conformità agli standard ISO, SOC2 e PCI.
* Effettuare l'integrazione con Active Directory.

### <a id="iis6"></a> Ho un'applicazione IIS6 in esecuzione su Windows Server 2003.

Siti Web di Azure consente di evitare facilmente i costi associati alla migrazione delle precedenti applicazioni IIS6. Microsoft ha creato [strumenti di migrazione e informazioni aggiuntive facili da usare](https://www.movemetowebsites.net/) che permettono di verificare la compatibilità e di identificare le eventuali modifiche da apportare. L'integrazione con Visual Studio, TFS e strumenti CMS comuni semplifica la distribuzione di applicazioni IIS6 direttamente nel cloud. Dopo la distribuzione il portale di gestione di Azure fornisce solidi strumenti di gestione che consentono di ridurre il numero di istanze per gestire i costi e soddisfare la domanda in base alle esigenze. Con lo strumento di migrazione è possibile:

* Eseguire la migrazione dell'applicazione Web Windows Server 2003 al cloud in modo rapido e semplice.
* Scegliere di lasciare in locale il database SQL collegato per creare un'applicazione ibrida. 
* Spostare automaticamente il database SQL insieme all'applicazione legacy. 

### <a id="smallbusiness"></a>Si è titolari di una piccola impresa il cui sito deve essere ospitato usando un metodo economico, ma che tenga presente della possibilità di crescita in futuro.

Siti Web di Azure è un'ottima soluzione per questo scenario, perché è possibile iniziare a usarlo gratuitamente e quindi aggiungere funzionalità man mano che si rendano necessarie. Ogni sito Web gratuito include un dominio fornito da Azure (*nome_società*.azurewebsites.net) e la piattaforma include strumenti di distribuzione e gestione integrati, nonché una raccolta di applicazioni che semplifica le operazioni iniziali. Sono disponibili molti altri servizi e opzioni di ridimensionamento che consentono l'evoluzione del sito in base all'aumento della domanda da parte degli utenti. Con Siti Web di Azure è possibile:

- Iniziare con il livello gratuito e quindi aggiungere risorse in base alle esigenze.
- Usare la raccolta di applicazioni per configurare rapidamente applicazioni Web diffuse, come WordPress.
- Aggiungere funzionalità e servizi di Azure aggiuntivi all'applicazione, in base alle necessità.
- Proteggere il sito Web con HTTPS.

### <a id="designer"></a> In qualità di Web designer o progettista grafico, si progettano e creano siti Web per i propri clienti

Per gli sviluppatori e i progettisti Web Siti Web di Azure si integra facilmente con un'ampia gamma di framework e strumenti, include il supporto della distribuzione per Git e FTP e offre una stretta integrazione con strumenti e servizi come Visual Studio e il database SQL. Con Siti Web è possibile:

- Usare strumenti da riga di comando per [attività automatiche][scripting].
- Lavorare con i linguaggi più usati, come [.Net][dotnet], [PHP][], [Node.js][nodejs] e [Python][].
- Selezionare tre diversi livelli di scalabilità per aumentare la capacità fino a livelli molto elevati.
- Integrare questo con altri servizi di Azure, come il [database SQL][sqldatabase], il [bus di servizio][servicebus] e [Archiviazione][], oppure con offerte di partner disponibili in [Azure Store][azurestore], come MySQL e MongoDB.
- Integrare il servizio con strumenti come Visual Studio, Git, WebMatrix, WebDeploy, TFS e FTP.

### <a id="multitier"></a>Si sta eseguendo la migrazione dell'applicazione multilivello con un front-end Web nel cloud.

Se si esegue un'applicazione multilivello, ad esempio un server Web connesso a un database, Siti Web di Azure è una buona opzione, in grado di offrire una stretta integrazione con il database SQL di Azure. È anche possibile usare la funzionalità Processi Web per l'esecuzione di processi back-end.

Scegliere Servizi cloud per uno o più livelli se è necessario esercitare un controllo maggiore sull'ambiente server, ad esempio poter accedere in remoto al server o configurare attività di avvio del server.

Scegliere Macchine virtuali per uno o più dei livelli se si vuole usare la propria immagine di macchina o eseguire software o servizi server che non è possibile configurare in Servizi cloud. 

### <a id="custom"></a>La mia applicazione dipende da ambienti Windows o Linux altamente personalizzati e voglio effettuarne il passaggio al cloud.

Se l'applicazione richiede la complessa installazione o configurazione di software e sistema operativo, Macchine virtuali è probabilmente la soluzione migliore. Con Macchine virtuali è possibile:

- Usare la raccolta di macchine virtuali per iniziare con un sistema operativo, come Windows o Linux, e quindi personalizzarlo in base alle esigenze dell'applicazione. 
- Creare e caricare un'immagine personalizzata di un server locale esistente per eseguirla su una macchina virtuale in Azure. 

### <a id="oss"></a>Il sito usa software open source e si vuole ospitarlo in Azure

Se il framework open source è supportato su Siti Web, le lingue e i framework richiesti dall'applicazione sono configurati automaticamente. Siti Web consente di:

- Usare molti linguaggi open source tra i più diffusi, come [.NET][dotnet], [PHP][], [Node.js][nodejs] e [Python][]. 
- Configurare applicazioni WordPress, Drupal, Umbraco, DNN e molte altre applicazioni Web di terze parti. 
- Eseguire la migrazione di un'applicazione esistente o crearne una nuova dalla raccolta di applicazioni. 

Se il framework open source non è supportato in Siti Web, è possibile eseguirlo sulle altre due opzioni di hosting Web di Azure. Con Servizi cloud si usano le attività di avvio per installare e configurare qualsiasi software open source necessario che viene eseguito in Windows. Con Macchine virtuali il software viene installato e configurato sull'immagine della macchina, che può essere basata su Windows o su Linux. 

### <a id="lob"></a>Ho un'applicazione line-of-business che deve connettersi alla rete aziendale

Per creare un'applicazione line-of-business, il sito Web potrebbe richiedere l'accesso diretto a servizi o dati nella rete aziendale. Questo è possibile su Siti Web, Servizi cloud e Macchine virtuali usando il [servizio Rete virtuale di Azure](/it-it/services/virtual-network/). In Siti Web è possibile usare la nuova [funzionalità di integrazione della rete virtuale](http://azure.microsoft.com/blog/2014/09/15/azure-websites-virtual-network-integration/), che permette l'esecuzione delle applicazioni Azure come se si trovassero nella rete aziendale.

### <a id="mobile"></a>Si vuole ospitare un'API REST o un servizio Web per client mobili

I servizi Web basati su HTTP consentono di supportare una vasta gamma di client, inclusi client mobili. Framework come l'API Web ASP .NET si integrano con Visual Studio per semplificare la creazione e l'uso di servizi REST.  Tali servizi sono esposti da un endpoint Web, pertanto, per il supporto di questo scenario, è possibile usare qualsiasi tecnica di hosting Web in Azure. Siti Web è tuttavia un'ottima scelta per l'hosting di API REST. Con Siti Web è possibile:

- Creare rapidamente un sito Web per ospitare il servizio Web HTTP in uno dei data center Azure distribuiti a livello globale.
- Eseguire la migrazione di servizi esistenti o crearne di nuovi.
- Soddisfare i requisiti del contratto di servizio per la disponibilità con una singola istanza oppure implementare scalabilità orizzontale in più macchine dedicate. 
- Usare il sito pubblicato per fornire API REST per tutti i client HTTP, inclusi i client mobili.

##<a name="features"></a>Confronto delle funzionalità

Nella tabella seguente vengono confrontate le funzionalità di Siti Web, Servizi cloud e Macchine virtuali per aiutare i clienti a operare la scelta più adatta. Per informazioni aggiornate sul contratto di servizio per ogni opzione, vedere i [contratti di servizio di Azure](/it-it/support/legal/sla/).

<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">Funzionalità</th>
   <th align="left" valign="middle">Siti Web</th>
   <th align="left" valign="middle">Servizi cloud (ruoli Web)</th>
   <th align="left" valign="middle">Macchine virtuali</th>
   <th align="left" valign="middle">Note</th>
</tr>
<tr>
   <td valign="middle"><p>Distribuzione quasi istantanea</p></td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
   <td valign="middle"></td>
   <td valign="middle">La distribuzione di un'applicazione o di un aggiornamento di un'applicazione in un servizio cloud o la creazione di una macchina virtuale richiede almeno diversi minuti, mentre per la distribuzione di un'applicazione in un sito Web sono necessari solo pochi secondi.</td>
</tr>
<tr>
   <td valign="middle"><p>Aumento della dimensione delle macchine senza ridistribuzione</p></td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
   <td valign="middle"></td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>Poiché le istanze del server Web condividono contenuti e configurazione, non è necessario ridistribuire o riconfigurare in base al ridimensionamento.</p></td>
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
   <td valign="middle"><p>Distribuzione di codice con GIT, FTP</p></td>
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
   <td valign="middle">Servizi cloud supporta l'uso della distribuzione Web per distribuire gli aggiornamenti a singole istanze del ruolo. Tuttavia, non è possibile usarlo per la distribuzione iniziale di un ruolo e se si usa la distribuzione Web per un aggiornamento, è necessario effettuare la distribuzione separata in ogni istanza di un ruolo. Sono necessarie più istanze ai fini della qualifica per il contratto di servizio di Servizi cloud per ambienti di produzione.</td>
</tr>
<tr>
   <td valign="middle"><p>Supporto WebMatrix</p></td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>Accesso a servizi come il bus di servizio, Archiviazione o il database SQL</p></td>
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
   <td valign="middle">Siti Web può ospitare facilmente un livello intermedio dell'API REST e la funzionalità <a href="http://go.microsoft.com/fwlink/?linkid=390226">Processi Web</a> di Siti Web può ospitare processi di elaborazione in background. È possibile eseguire WebJobs in un sito Web dedicato per ottenere la scalabilità indipendente per il livello.</td>
</tr>
<tr>
   <td valign="middle"><p>Supporto integrato di MySQL distribuito come servizio</p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">Servizi Web può integrare MySQL distribuito come servizio attraverso le soluzioni di ClearDB, ma non come parte del flusso di lavoro del portale di gestione.</td>
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
   <td valign="middle">Per Siti Web, SSL per i nomi di dominio personalizzati è supportato solo nelle modalità Basic e Standard. Per informazioni sull'uso di SSL con Siti Web, vedere <a href="../web-sites-configure-ssl-certificate/">Configurazione di un certificato SSL per un sito Web di Azure</a>.</td>
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
   <td valign="middle"><p>Distribuzione di codice con TFS</p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle"></td>
</tr>
<tr>
   <td valign="middle"><p>Isolamento rete con <a href="/it-it/services/virtual-network/">Rete virtuale di Azure</a></p></td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">X</td>
   <td valign="middle">Vedere anche <a href="/blog/2014/09/15/azure-websites-virtual-network-integration/">Integrazioni della rete virtuale di Siti Web di Azure</a></td>
</tr>
<tr>
   <td valign="middle"><p>Supporto per <a href="/it-it/services/traffic-manager/">Gestione traffico di Azure</a></p></td>
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


> [WACOM.NOTE]
> Per iniziare a usare Siti Web di Azure prima di iscriversi per ottenere un account, visitare la pagina all'indirizzo <a href="https://trywebsites.azurewebsites.net/">https://trywebsites.azurewebsites.net</a>in cui è possibile creare immediatamente e gratuitamente un sito di base ASP.NET temporaneo in Siti Web di Azure. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.


## <a id="nextsteps"></a> Passaggi successivi

Per altre informazioni sulle tre opzioni di hosting Web, vedere le risorse seguenti:

* [Introduzione ad Azure](../fundamentals-introduction-to-azure/)
* [Modelli di esecuzione di Azure](../fundamentals-application-models/)

Per iniziare a usare le opzioni scelte per l'applicazione, vedere le risorse seguenti:

* [Siti Web di Azure](/it-it/documentation/services/websites/)
* [Servizi cloud di Azure](/it-it/documentation/services/cloud-services/)
* [Macchine virtuali di Azure](/it-it/documentation/services/virtual-machines/)

  [Diagramma delle scelte]: ./media/choose-web-site-cloud-service-vm/Websites_CloudServices_VMs_3.png
  [Siti Web di Azure]: http://go.microsoft.com/fwlink/?LinkId=306051
  [Servizi cloud]: http://go.microsoft.com/fwlink/?LinkId=306052
  [Macchine virtuali]: http://go.microsoft.com/fwlink/?LinkID=306053
  [ClearDB]: http://www.cleardb.com/
  [Processi Web]: http://go.microsoft.com/fwlink/?linkid=390226&clcid=0x409
  [Configurazione di un certificato SSL per un sito Web di Azure]: http://www.windowsazure.com/it-it/develop/net/common-tasks/enable-ssl-web-site/
  [azurestore]: http://www.windowsazure.com/it-it/gallery/store/
  [scripting]: http://www.windowsazure.com/it-it/documentation/scripts/?services=web-sites
  [dotnet]: http://www.windowsazure.com/it-it/develop/net/
  [nodejs]: http://www.windowsazure.com/it-it/develop/nodejs/
  [PHP]: http://www.windowsazure.com/it-it/develop/php/
  [Python]: http://www.windowsazure.com/it-it/develop/python/
  [servicebus]: http://www.windowsazure.com/it-it/documentation/services/service-bus/
  [sqldatabase]: http://www.windowsazure.com/it-it/documentation/services/sql-database/
  [Archiviazione]: http://www.windowsazure.com/it-it/documentation/services/storage/

<!--HONumber=35_1-->
