<properties pageTitle="Confronto tra Siti Web, Servizi cloud e Macchine virtuali di Azure" metaKeywords="Cloud Services, Virtual Machines, Web Sites" description="Quando usare Siti Web, Servizi cloud e Macchine virtuali di Azure per l'hosting di applicazioni Web." metaCanonical="" services="web-sites,virtual-machines,cloud-services" documentationCenter="" title=" Servizi cloud" authors="tdykstra" solutions="" manager="wpickett" editor="jimbe" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/24/2014" ms.author="tdykstra" />

# Confronto tra Siti Web, Servizi cloud e Macchine virtuali di Azure

## Panoramica

Azure offre diverse modalità di hosting dei siti Web: [Siti Web][Siti Web], [Servizi cloud][Servizi cloud] e [Macchine virtuali di Azure][Macchine virtuali di Azure]. In questo articolo vengono fornite informazioni utili per comprendere le opzioni disponibili ed effettuare la scelta appropriata per l'applicazione Web.

Siti Web di Azure è la scelta migliore per la maggior parte delle app Web. La distribuzione e la gestione sono integrate nella piattaforma ed è possibile scalare rapidamente i siti per gestire carichi di traffico elevato; inoltre, il bilanciamento del carico e la gestione del traffico predefiniti offrono disponibilità elevata. È possibile spostare facilmente siti esistenti in Siti Web di Azure con uno [strumento di migrazione online][strumento di migrazione online], usare un'app open source della raccolta di applicazioni Web oppure creare un nuovo sito tramite il framework e gli strumenti di propria scelta. La funzionalità [WebJobs][WebJobs] (attualmente in anteprima) semplifica l'aggiunta dell'elaborazione dei processi in background all'app.

Per esercitare un controllo maggiore sull'ambiente server Web, ad esempio poter accedere in remoto al server o configurare attività di avvio del server, Servizi cloud di Azure è in genere l'opzione migliore.

Se è presente un'applicazione esistente la cui esecuzione in Siti Web di Azure o Servizi cloud di Azure richiederebbe modifiche sostanziali, è possibile scegliere Macchine virtuali di Azure allo scopo di semplificare la migrazione al cloud. Tuttavia, la corretta configurazione, la protezione e la gestione delle VM richiede molto più tempo e competenza IT rispetto a Siti Web di Azure e a Servizi cloud. Se si sta valutando la possibilità di usare Macchine virtuali di Azure, assicurarsi di tenere conto delle operazioni di manutenzione continua necessarie per le attività di applicazione di patch, aggiornamento e gestione relative all'ambiente di VM.

Nel diagramma seguente viene illustrato il grado relativo di controllo rispetto alla facilità d'uso per ciascuna delle opzioni di hosting Web di Azure.

![ChoicesDiagram][ChoicesDiagram]

## Sommario

-   [Scenari e indicazioni][Scenari e indicazioni]
-   [Confronto delle funzionalità][Confronto delle funzionalità]
-   [Passaggi successivi][Passaggi successivi]

## <a name="scenarios"></a>Scenari e indicazioni

Di seguito sono riportati alcuni scenari applicativi comuni con indicazioni riguardo all'opzione di hosting Web di Azure più appropriata per ciascuno di essi.

-   [Mi serve un front-end Web con funzionalità di elaborazione in background e un back-end di database per eseguire applicazioni aziendali integrate con risorse locali.][Mi serve un front-end Web con funzionalità di elaborazione in background e un back-end di database per eseguire applicazioni aziendali integrate con risorse locali.]
-   [Ho bisogno di un modo affidabile per ospitare il mio sito Web aziendale che sia scalabile e offra una portata globale.][Ho bisogno di un modo affidabile per ospitare il mio sito Web aziendale che sia scalabile e offra una portata globale.]
-   [Ho un'applicazione IIS6 in esecuzione su Windows Server 2003.][Ho un'applicazione IIS6 in esecuzione su Windows Server 2003.]
-   [Sono un piccolo imprenditore e ho bisogno di ospitare il mio sito in modo economico ma tenendo sempre presente la possibilità di crescita futura.][Sono un piccolo imprenditore e ho bisogno di ospitare il mio sito in modo economico ma tenendo sempre presente la possibilità di crescita futura.]
-   [Sono un Web designer o un progettista grafico e voglio progettare e realizzare siti Web per i miei clienti.][Sono un Web designer o un progettista grafico e voglio progettare e realizzare siti Web per i miei clienti.]
-   [Sto migrando la mia applicazione multilivello con un front-end Web al cloud.][Sto migrando la mia applicazione multilivello con un front-end Web al cloud.]
-   [La mia applicazione dipende da ambienti Windows o Linux altamente personalizzati e voglio effettuarne il passaggio al cloud.][La mia applicazione dipende da ambienti Windows o Linux altamente personalizzati e voglio effettuarne il passaggio al cloud.]
-   [Il mio sito usa software open source e voglio ospitarlo in Azure.][Il mio sito usa software open source e voglio ospitarlo in Azure.]
-   [Ho un'applicazione line-of-business che deve connettersi alla rete aziendale.][Ho un'applicazione line-of-business che deve connettersi alla rete aziendale.]
-   [Voglio ospitare un'API REST o un servizio Web per client mobili.][Voglio ospitare un'API REST o un servizio Web per client mobili.]

### <span id="onprem"></span></a> Mi serve un front-end Web con funzionalità di elaborazione in background e un back-end di database per eseguire applicazioni aziendali integrate con risorse locali.

Siti Web di Azure è un'ottima soluzione per applicazioni aziendali complesse. Consente di sviluppare app scalabili automaticamente su una piattaforma con carico bilanciato, è protetto con Active Directory e si connette alle risorse locali. Semplifica la gestione di tali app tramite un portale di gestione e API di qualità superiore e consente di ottenere informazioni sul modo in cui i clienti le usano con strumenti di analisi approfondita delle app. La nuova funzionalità [WebJobs][WebJobs] consente di eseguire processi e attività in background nell'ambito del livello Web, mentre la connettività ibrida e le [funzionalità VNET][funzionalità VNET] semplificano la connessione alle risorse in sede. Siti Web di Azure fornisce contratti di servizio del 99,999% e consente di:

-   Eseguire le applicazioni in modo affidabile su una piattaforma cloud con funzionalità automatiche di riparazione e di applicazione di patch.
-   Scalare automaticamente in una rete globale di data center.
-   Eseguire il backup e il ripristino per il ripristino di emergenza.
-   Essere conformi a ISO, SOC2 e PCI.
-   Effettuare l'integrazione con Active Directory.

### <span id="corp"></span></a> Ho bisogno di un modo affidabile per ospitare il mio sito Web aziendale che sia scalabile e offra una portata globale.

Siti Web di Azure è un'ottima soluzione per l'hosting di siti Web aziendali. Consente la scalabilità rapida e semplice dei siti per soddisfare la domanda in una rete globale di data center. Offre portata locale, tolleranza di errore e gestione intelligente del traffico, il tutto su una piattaforma che fornisce strumenti di gestione di livello superiore, consentendo di ottenere informazioni sullo stato di integrità e sul traffico del sito in modo semplice e rapido. Siti Web di Azure fornisce contratti di servizio del 99,999% e consente di:

-   Eseguire i siti Web in modo affidabile su una piattaforma cloud con funzionalità automatiche di riparazione e di applicazione di patch.
-   Scalare automaticamente in una rete globale di data center.
-   Eseguire il backup e il ripristino per il ripristino di emergenza.
-   Gestire i log e il traffico con strumenti integrati.
-   Essere conformi a ISO, SOC2 e PCI.
-   Effettuare l'integrazione con Active Directory.

### <span id="iis6"></span></a> Ho un'applicazione IIS6 in esecuzione su Windows Server 2003.

Siti Web di Azure consente di evitare facilmente i costi associati alla migrazione delle precedenti applicazioni IIS6. Microsoft ha creato [strumenti di migrazione e indicazioni dettagliate facili da usare][strumenti di migrazione e indicazioni dettagliate facili da usare] che consentono di controllare la compatibilità e di identificare eventuali modifiche da apportare. L'integrazione con Visual Studio, TFS e con strumenti CMS comuni semplifica la distribuzione delle applicazioni IIS6 direttamente nel cloud. Dopo la distribuzione il portale di gestione di Azure fornisce solidi strumenti di gestione che consentono di ridurre il numero di istanze per gestire i costi e soddisfare la domanda in base alle esigenze. Con lo strumento di migrazione è possibile:

-   Eseguire la migrazione dell'applicazione Web Windows Server 2003 al cloud in modo rapido e semplice.
-   Scegliere di lasciare in locale il database SQL collegato per creare un'applicazione ibrida.
-   Spostare automaticamente il database SQL insieme all'applicazione legacy.

### <span id="smallbusiness"></span></a>Sono un piccolo imprenditore e ho bisogno di ospitare il mio sito in modo economico ma tenendo sempre presente la possibilità di crescita futura.

Siti Web di Azure è un'ottima soluzione per questo scenario, perché è possibile iniziare a usarlo gratuitamente e quindi aggiungere funzionalità man mano che si rendano necessarie. Ogni sito Web gratuito include un dominio fornito da Azure (*nome\_società*.azurewebsites.net) e la piattaforma include strumenti di distribuzione e gestione integrati nonché una raccolta di applicazioni che consentono di iniziare a usare facilmente la funzionalità. Sono disponibili molti altri servizi e opzioni di ridimensionamento che consentono l'evoluzione del sito in base all'aumento della domanda da parte degli utenti. Con Siti Web di Azure è possibile:

-   Iniziare con il livello gratuito e quindi aggiungere risorse in base alle esigenze.
-   Usare la raccolta di applicazioni per configurare rapidamente applicazioni Web diffuse, come WordPress.
-   Aggiungere funzionalità e servizi di Azure aggiuntivi all'applicazione, in base alle necessità.
-   Proteggere il sito Web con HTTPS.

### <span id="designer"></span></a> Sono un Web designer o un progettista grafico e voglio progettare e realizzare siti Web per i miei clienti

Per gli sviluppatori e i progettisti Web Siti Web di Azure si integra facilmente con una varietà di framework e strumenti, include il supporto della distribuzione per Git e FTP e offre una stretta integrazione con strumenti e servizi quali Visual Studio e il database SQL. Con Siti Web è possibile:

-   Usare strumenti da riga di comando per [attività automatiche][attività automatiche].
-   Lavorare con i linguaggi più usati, ad esempio [.Net][.Net], [PHP][PHP], [Node.js][Node.js] e [Python][Python].
-   Selezionare tre diversi livelli di scalabilità per aumentare la capacità fino a livelli molto elevati.
-   Integrare con altri servizi di Azure, ad esempio [database SQL][database SQL], [bus di servizio][bus di servizio] e [archiviazione][archiviazione] oppure offerte di partner da [Azure Store][Azure Store], ad esempio MySQL e MongoDB.
-   Effettuare l'integrazione con strumenti quali Visual Studio, Git, WebMatrix, WebDeploy, TFS e FTP.

### <span id="multitier"></span></a>Sto eseguendo la migrazione della mia applicazione multilivello con un front-end Web al cloud

Se si esegue un'applicazione multilivello, ad esempio un server Web che si connette a un database, Siti Web di Azure è una buona opzione che offre una stretta integrazione con il database SQL di Azure. Inoltre, è possibile usare la nuova funzionalità WebJobs (in anteprima) per l'esecuzione di processi di back-end.

Scegliere Servizi cloud per uno o più livelli se è necessario esercitare un controllo maggiore sull'ambiente server, ad esempio poter accedere in remoto al server o configurare attività di avvio del server.

Scegliere Macchine virtuali per uno o più livelli per usare la propria immagine di macchina o per eseguire software o servizi server che non è possibile configurare su Servizi cloud.

### <span id="custom"></span></a>La mia applicazione dipende da ambienti Windows o Linux altamente personalizzati e voglio effettuarne il passaggio al cloud.

Se l'applicazione richiede la complessa installazione o configurazione di software e sistema operativo, Macchine virtuali è probabilmente la soluzione migliore. Con Macchine virtuali è possibile:

-   Usare la raccolta di macchine virtuali per iniziare con un sistema operativo, come Windows o Linux, e quindi personalizzarlo in base alle esigenze dell'applicazione.
-   Creare e caricare un'immagine personalizzata di un server locale esistente per eseguirla su una macchina virtuale in Azure.

### <span id="oss"></span></a>Il mio sito usa software open source e voglio ospitarlo in Azure

Se il framework open source è supportato su Siti Web, le lingue e i framework richiesti dall'applicazione sono configurati automaticamente. Siti Web consente di:

-   Usare molti linguaggi open source tra i più diffusi, ad esempio [.Net][.Net], [PHP][PHP], [Node.js][Node.js] e [Python][Python].
-   Configurare WordPress, Drupal, Umbraco, DNN e molte altre applicazioni Web di terze parti.
-   Eseguire la migrazione di un'applicazione esistente o crearne una nuova dalla raccolta di applicazioni.

Se il framework open source non è supportato in Siti Web, è possibile eseguirlo sulle altre due opzioni di hosting Web di Azure. Con Servizi cloud si usano le attività di avvio per installare e configurare qualsiasi software open source necessario che viene eseguito in Windows. Con Macchine virtuali il software viene installato e configurato sull'immagine della macchina, che può essere basata su Windows o su Linux.

### <span id="lob"></span></a>Ho un'applicazione line-of-business che deve connettersi alla rete aziendale

Per creare un'applicazione line-of-business, il sito Web potrebbe richiedere l'accesso diretto a servizi o dati nella rete aziendale. Ciò è possibile su Siti Web, Servizi cloud e Macchine virtuali usando il [servizio Rete virtuale di Azure][servizio Rete virtuale di Azure]. In Siti Web è possibile usare la nuova [funzionalità di integrazione VNET][funzionalità di integrazione VNET], che consente l'esecuzione delle applicazioni Azure come se queste si trovassero sulla rete aziendale.

### <span id="mobile"></span></a>Voglio ospitare un'API REST o un servizio Web per client mobili

I servizi Web basati su HTTP consentono di supportare una vasta gamma di client, inclusi client mobili. Framework come API Web ASP.NET si integrano con Visual Studio per semplificare la creazione e l'uso di servizi REST. Tali servizi sono esposti da un endpoint Web, pertanto, per il supporto di questo scenario, è possibile usare qualsiasi tecnica di hosting Web in Azure. Siti Web è tuttavia un'ottima scelta per l'hosting di API REST. Con Siti Web è possibile:

-   Creare rapidamente un sito Web per ospitare il servizio Web HTTP in uno dei data center Azure distribuiti a livello globale.
-   Eseguire la migrazione di servizi esistenti o crearne di nuovi.
-   Soddisfare i requisiti del contratto di servizio per la disponibilità con una sola istanza oppure scalare orizzontalmente su più macchine dedicate.
-   Usare il sito pubblicato per fornire API REST per qualsiasi client HTTP, compresi quelli mobili.

## <a name="features"></a>Confronto delle funzionalità

Nella tabella seguente vengono confrontate le funzionalità di Siti Web, Servizi cloud e Macchine virtuali per aiutare i clienti a operare la scelta più adatta. Per informazioni aggiornate sul contratto di servizio per ciascuna opzione, vedere i [contratti di servizio di Azure][contratti di servizio di Azure].

<table>
<colgroup>
<col width="20%" />
<col width="20%" />
<col width="20%" />
<col width="20%" />
<col width="20%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Funzionalità</th>
<th align="left">Siti Web</th>
<th align="left">Servizi cloud (ruoli Web)</th>
<th align="left">Macchine virtuali</th>
<th align="left">Note</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><p>Distribuzione quasi istantanea</p></td>
<td align="left">X</td>
<td align="left"></td>
<td align="left"></td>
<td align="left">La distribuzione di un'applicazione o di un aggiornamento di un'applicazione in un Servizio cloud o la creazione di una VMVMVM persistente richiede come minimo diversi minuti; la distribuzione di un'applicazione in un sito Web richiede pochi secondi.</td>
</tr>
<tr class="even">
<td align="left"><p>Aumento della dimensione delle macchine senza ridistribuzione</p></td>
<td align="left">X</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
</tr>
<tr class="odd">
<td align="left"><p>Le istanze del server Web condividono contenuti e configurazione, quindi non occorre ridistribuire o riconfigurare man mano che si scala.</p></td>
<td align="left">X</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
</tr>
<tr class="even">
<td align="left"><p>Più ambienti di distribuzione (produzione e gestione temporanea)</p></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left"></td>
<td align="left"></td>
</tr>
<tr class="odd">
<td align="left"><p>Gestione automatica dell'aggiornamento del sistema operativo</p></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left"></td>
<td align="left"></td>
</tr>
<tr class="even">
<td align="left"><p>Commutazione di piattaforma trasparente (è possibile passare facilmente tra 32 bit e 64 bit)</p></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left"></td>
<td align="left"></td>
</tr>
<tr class="odd">
<td align="left"><p>Distribuzione codice con GIT, FTP</p></td>
<td align="left">X</td>
<td align="left"></td>
<td align="left">X</td>
<td align="left"></td>
</tr>
<tr class="even">
<td align="left"><p>Distribuzione codice con distribuzione Web</p></td>
<td align="left">X</td>
<td align="left"></td>
<td align="left">X</td>
<td align="left">Servizi cloud supporta l'uso della distribuzione Web per distribuire gli aggiornamenti a singole istanze del ruolo. Tuttavia, non è possibile usarlo per la distribuzione iniziale di un ruolo e se si usa la distribuzione Web per un aggiornamento è necessario effettuare la distribuzione separata a ciascuna istanza di un ruolo. Sono necessarie più istanze per qualificarsi per il contratto di servizio di Servizi cloud per gli ambienti di produzione.</td>
</tr>
<tr class="odd">
<td align="left"><p>Supporto WebMatrix</p></td>
<td align="left">X</td>
<td align="left"></td>
<td align="left">X</td>
<td align="left"></td>
</tr>
<tr class="even">
<td align="left"><p>Accesso a servizi quali bus di servizio, archiviazione, database SQL</p></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left"></td>
</tr>
<tr class="odd">
<td align="left"><p>Hosting del livello Web o dei servizi Web di un'architettura multilivello</p></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left"></td>
</tr>
<tr class="even">
<td align="left"><p>Hosting del livello intermedio di un'architettura multilivello</p></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left">Siti Web può ospitare facilmente un livello intermedio API REST e la funzionalità <a href="http://go.microsoft.com/fwlink/?linkid=390226">WebJobs</a> di Siti Web (attualmente in anteprima) può ospitare processi di elaborazione in background. È possibile eseguire WebJobs in un sito Web dedicato per ottenere la scalabilità indipendente per il livello.</td>
</tr>
<tr class="odd">
<td align="left"><p>Supporto integrato di MySQL distribuito come servizio</p></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left">Servizi cloud può integrare MySQL distribuito come servizio tramite le offerte di ClearDB, ma non come parte del flusso di lavoro del portale di gestione.</td>
</tr>
<tr class="even">
<td align="left"><p>Supporto per ASP.NET, ASP classico, Node.js, PHP, Python</p></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left"></td>
</tr>
<tr class="odd">
<td align="left"><p>Scalabilità orizzontale a più istanze senza ridistribuzione</p></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left">Macchine virtuali consente la scalabilità orizzontale su più istanze; tuttavia, i servizi in esecuzione nelle macchine devono essere scritti per gestire tale scalabilità. È necessario configurare un bilanciamento del carico per instradare le richieste tra le macchine e creare un gruppo di affinità per impedire i riavvii simultanei di tutte le istanze a causa degli errori di manutenzione o hardware.</td>
</tr>
<tr class="even">
<td align="left"><p>Supporto per SSL</p></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left">Per Siti Web, SSL per i nomi di dominio personalizzati è supportato solo nella modalità Basic e Standard. Per informazioni sull'uso di SSL con Siti Web, vedere <a href="../web-sites-configure-ssl-certificate/">Configurazione di un certificato SSL per un sito Web di Azure</a>.</td>
</tr>
<tr class="odd">
<td align="left"><p>Integrazione di Visual Studio</p></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left"></td>
</tr>
<tr class="even">
<td align="left"><p>Debug remoto</p></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left"></td>
</tr>
<tr class="odd">
<td align="left"><p>Distribuzione codice con TFS</p></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left"></td>
</tr>
<tr class="even">
<td align="left"><p>Isolamento rete con la <a href="/it-it/services/virtual-network/">rete virtuale di Azure</a></p></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left">Vedere anche <a href="/blog/2014/09/15/azure-websites-virtual-network-integration/">Integrazione della rete virtuale di Siti Web di Azure</a></td>
</tr>
<tr class="odd">
<td align="left"><p>Supporto per <a href="/it-it/services/traffic-manager/">Gestione traffico di Azure</a></p></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left"></td>
</tr>
<tr class="even">
<td align="left"><p>Monitoraggio integrato degli endpoint</p></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left"></td>
</tr>
<tr class="odd">
<td align="left"><p>Accesso al server tramite Desktop remoto</p></td>
<td align="left"></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left"></td>
</tr>
<tr class="even">
<td align="left"><p>Installazione di qualsiasi MSI personalizzato</p></td>
<td align="left"></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left"></td>
</tr>
<tr class="odd">
<td align="left"><p>Possibilità di definire/eseguire le attività di avvio</p></td>
<td align="left"></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left"></td>
</tr>
<tr class="even">
<td align="left"><p>Possibilità di usare eventi ETW personalizzati per l'analisi e il debug</p></td>
<td align="left"></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left"></td>
</tr>
</tbody>
</table>

## <span id="nextsteps"></span></a>Passaggi successivi

Per altre informazioni sulle tre opzioni di hosting Web, vedere le risorse seguenti:

-   [Introduzione ad Azure][Introduzione ad Azure]
-   [Modelli di esecuzione di Azure][Modelli di esecuzione di Azure]

Per iniziare a usare le opzioni scelte per l'applicazione, vedere le risorse seguenti:

-   [Siti Web di Azure][Siti Web di Azure]
-   [Servizi cloud di Azure][Servizi cloud di Azure]
-   [Macchine virtuali di Azure][2]

  [Siti Web]: http://go.microsoft.com/fwlink/?LinkId=306051
  [Servizi cloud]: http://go.microsoft.com/fwlink/?LinkId=306052
  [Macchine virtuali di Azure]: http://go.microsoft.com/fwlink/?LinkID=306053
  [strumento di migrazione online]: https://www.migratetoazure.net/
  [WebJobs]: http://go.microsoft.com/fwlink/?linkid=390226&clcid=0x409
  [ChoicesDiagram]: ./media/choose-web-site-cloud-service-vm/Websites_CloudServices_VMs_3.png
  [Scenari e indicazioni]: #scenarios
  [Confronto delle funzionalità]: #features
  [Passaggi successivi]: #nextsteps
  [Mi serve un front-end Web con funzionalità di elaborazione in background e un back-end di database per eseguire applicazioni aziendali integrate con risorse locali.]: #onprem
  [Ho bisogno di un modo affidabile per ospitare il mio sito Web aziendale che sia scalabile e offra una portata globale.]: #corp
  [Ho un'applicazione IIS6 in esecuzione su Windows Server 2003.]: #iis6
  [Sono un piccolo imprenditore e ho bisogno di ospitare il mio sito in modo economico ma tenendo sempre presente la possibilità di crescita futura.]: #smallbusiness
  [Sono un Web designer o un progettista grafico e voglio progettare e realizzare siti Web per i miei clienti.]: #designer
  [Sto migrando la mia applicazione multilivello con un front-end Web al cloud.]: #multitier
  [La mia applicazione dipende da ambienti Windows o Linux altamente personalizzati e voglio effettuarne il passaggio al cloud.]: #custom
  [Il mio sito usa software open source e voglio ospitarlo in Azure.]: #oss
  [Ho un'applicazione line-of-business che deve connettersi alla rete aziendale.]: #lob
  [Voglio ospitare un'API REST o un servizio Web per client mobili.]: #mobile
  [funzionalità VNET]: ../fundamentals-networking/
  [strumenti di migrazione e indicazioni dettagliate facili da usare]: https://www.movemetowebsites.net/
  [attività automatiche]: http://www.windowsazure.com/it-it/documentation/scripts/?services=web-sites
  [.Net]: http://www.windowsazure.com/it-it/develop/net/
  [PHP]: http://www.windowsazure.com/it-it/develop/php/
  [Node.js]: http://www.windowsazure.com/it-it/develop/nodejs/
  [Python]: http://www.windowsazure.com/it-it/develop/python/
  [database SQL]: http://www.windowsazure.com/it-it/documentation/services/sql-database/
  [bus di servizio]: http://www.windowsazure.com/it-it/documentation/services/service-bus/
  [archiviazione]: http://www.windowsazure.com/it-it/documentation/services/storage/
  [Azure Store]: http://www.windowsazure.com/it-it/gallery/store/
  [servizio Rete virtuale di Azure]: /it-it/services/virtual-network/
  [funzionalità di integrazione VNET]: http://azure.microsoft.com/blog/2014/09/15/azure-websites-virtual-network-integration/
  [contratti di servizio di Azure]: /it-it/support/legal/sla/
  [Introduzione ad Azure]: ../fundamentals-introduction-to-azure/
  [Modelli di esecuzione di Azure]: ../fundamentals-application-models/
  [Siti Web di Azure]: /it-it/documentation/services/websites/
  [Servizi cloud di Azure]: /it-it/documentation/services/cloud-services/
  [2]: /it-it/documentation/services/virtual-machines/
