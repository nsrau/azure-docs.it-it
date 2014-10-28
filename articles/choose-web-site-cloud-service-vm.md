<properties linkid="manage-scenarios-choose-web-app-service" urlDisplayName="Web Options for Azure" pageTitle="Azure Websites, Cloud Services and Virtual Machines comparison" metaKeywords="Cloud Services, Virtual Machines, Web Sites" description="Learn when to use Azure Websites, Cloud Services, and Virtual Machines for hosting web applications. Review a feature comparison." metaCanonical="" services="web-sites,virtual-machines,cloud-services" documentationCenter="" title=" Cloud Services" authors="jroth" solutions="" manager="paulettm" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jroth"></tags>

# Confronto tra Siti Web, Servizi cloud e Macchine virtuali di Azure

Azure offre vari modi per ospitare le applicazioni Web: [Siti Web di Azure][], [Servizi cloud][] e [Macchine virtuali][]. Dopo aver esaminato le varie opzioni, si potrebbe essere incerti su quale possa essere quella più adatta alle proprie esigenze oppure alcuni concetti quali IaaS e PaaS potrebbero non risultare del tutto chiari. In questo articolo vengono fornite informazioni utili per comprendere le opzioni disponibili e decidere lo scenario Web migliore da adottare. Sebbene tutte e tre le opzioni consentano di eseguire applicazioni Web altamente scalabili in Azure, vi sono differenze che possono essere utili come guida nel prendere la decisione.

In molte situazioni Siti Web di Azure è l'opzione migliore. Fornisce opzioni semplici e flessibili per sviluppo e gestione e consente di ospitare siti Web con volumi elevati. È possibile creare un nuovo sito Web in modo rapido mediante strumenti software diffusi, come WordPress, o inclusi nella Raccolta di applicazioni Web, oppure è possibile spostare un sito Web esistente in Siti Web di Azure. È possibile aggiungere l'elaborazione di processi in background utilizzando [Azure WebJobs SDK][] (attualmente in anteprima).

È inoltre possibile ospitare applicazioni Web in Servizi cloud di Azure o in Macchine virtuali di Azure. Queste opzioni sono scelte adeguate quando il livello Web richiede l'ulteriore livello di controllo e di personalizzazione da queste fornito. Tuttavia, tale aumento di controllo ha un costo in termini di maggiore complessità nella creazione, nella gestione e nella distribuzione delle applicazioni. Nel diagramma riportato di seguito vengono illustrati i livelli di compromesso per tutte e tre le opzioni.

![ChoicesDiagram][]

Siti Web è più facile da configurare, gestire e monitorare, ma sono disponibili meno opzioni di configurazione. L'uso di Siti Web di Azure è in genere consigliato a meno che non vi sia un motivo impellente per collocare il proprio front-end Web in Servizi cloud o Macchine virtuali. Nel resto di questo documento vengono fornite le informazioni necessarie per poter prendere una decisione ragionata. Sono inclusi:

-   [Scenari][]
-   [Riepiloghi dei servizi][]
-   [Confronto delle funzionalità][]

## <a name="scenarios"></a>Scenari

### Sono un piccolo imprenditore e ho bisogno di ospitare il mio sito in modo economico ma tenendo sempre presente la possibilità di crescita futura.

Siti Web di Azure è un'ottima soluzione per questo scenario perché è possibile iniziare a usarlo gratuitamente e quindi aggiungere altre funzionalità in base alle proprie esigenze. Ogni sito Web gratuito è dotato, ad esempio, di un dominio fornito da Azure come *nome\_azienda*.azurewebsites.net. Quando si è pronti per iniziare a usare il proprio dominio, è possibile aggiungerlo al vantaggioso prezzo di $ 9,80 al mese (a partire dal mese di gennaio del 2014). Sono disponibili molti altri servizi e opzioni di ridimensionamento che consentono l'evoluzione del sito in base all'aumento della domanda da parte degli utenti. Con **Siti Web di Azure** è possibile:

-   Iniziare con il livello gratuito e quindi aggiungere risorse in base alle esigenze.
-   Utilizzare la raccolta di applicazioni per configurare rapidamente applicazioni Web diffuse, come WordPress.
-   Aggiungere funzionalità e servizi di Azure aggiuntivi all'applicazione, in base alle necessità.
-   Proteggere il sito Web con HTTPS usando il certificato fornito con il nome di dominio *nome\_azienda*.azurewebsites.net.

### Sono un Web designer o un progettista grafico e voglio progettare e realizzare siti Web per i miei clienti

Per gli sviluppatori Web, Siti Web di Azure fornisce tutto il necessario per creare applicazioni Web avanzate. Siti Web offre una stretta integrazione con strumenti quali Visual Studio e i database SQL. Con **Siti Web** gli sviluppatori possono:

-   Utilizzare strumenti da riga di comando per [attività automatiche][].
-   Lavorare con i linguaggi più utilizzati, ad esempio [.Net][], [PHP][], [Node.js][] e [Python][].
-   Selezionare tre diversi livelli di scalabilità per aumentare la capacità fino a livelli molto elevati.
-   Integrare con altri servizi di Azure, ad esempio [database SQL][], [bus di servizio][] e [archiviazione][] oppure offerte di partner da [Azure Store][], ad esempio MySQL e MongoDB.
-   Integrare con strumenti quali Visual Studio, Git, WebMatrix, WebDeploy, TFS e FTP.

### Sto migrando la mia applicazione multilivello con un front-end Web al cloud

Se si esegue un'applicazione multilivello, ad esempio un server Web che comunica con un server di database per archiviare e recuperare i dati del sito Web, sono disponibili diverse opzioni per la migrazione ad Azure. Tali opzioni architetturali includono Siti Web, Servizi cloud e Macchine virtuali. **Siti Web** è un'opzione valida per il livello Web della soluzione e può essere usato con il database SQL di Azure per creare un'architettura su due livelli. Siti Web consente inoltre di eseguire processi in background o a esecuzione prolungata usando l'anteprima di Azure WebJobs SDK. Se è necessaria un'architettura più complessa od opzioni di ridimensionamento più flessibili, Servizi cloud o Macchine virtuali sono una scelta migliore.

**Servizi cloud** consente di:

-   Ospitare servizi Web, di livello intermedio e back-end su ruoli Web e di lavoro scalabili.
-   Ospitare solo i servizi back-end e di livello intermedio su ruoli di lavoro, mantenendo il front-end su Siti Web di Azure.
-   Scalare servizi front-end e back-end in modo indipendente.

**Macchine virtuali** consente di:

-   Migrare più facilmente ambienti altamente personalizzati come un'immagine di macchina virtuale.
-   Eseguire il software o i servizi che non possono essere configurati su Siti Web o Servizi cloud.

### La mia applicazione dipende da ambienti Windows o Linux altamente personalizzati

Se l'applicazione richiede la complessa installazione o configurazione di software e sistema operativo, Macchine virtuali è probabilmente la soluzione migliore. Con **Macchine virtuali** è possibile:

-   Utilizzare la raccolta di macchine virtuali per iniziare con un sistema operativo, come Windows o Linux, e quindi personalizzarlo in base alle esigenze dell'applicazione.
-   Creare e caricare un'immagine personalizzata di un server locale esistente per eseguirla su una macchina virtuale in Azure.

### Il mio sito utilizza software open source, e voglio ospitarlo in Azure

Tutte e tre le opzioni permettono l'hosting di linguaggi e framework open source. **Servizi cloud** richiede l'utilizzo di attività di avvio per installare e configurare qualsiasi software open source necessario ed eseguibile in Windows. Con **Macchine virtuali**, il software viene installato e configurato sull'immagine della macchina, che può essere basata su Windows o su Linux. Se il framework open source è supportato in Siti Web, questo rappresenta un modo più semplice per ospitare questi tipi di applicazioni, poiché Siti Web può essere configurato automaticamente con i linguaggi e i framework richiesti dall'applicazione. **Siti Web** consente di:

-   Utilizzare molti linguaggi open source tra i più diffusi, ad esempio [.Net][], [PHP][], [Node.js][] e [Python][].
-   Configurare WordPress, Drupal, Umbraco, DNN e molte altre applicazioni Web di terze parti.
-   Eseguire la migrazione di un'applicazione esistente o crearne una nuova dalla raccolta di applicazioni.

### Ho un'applicazione line-of-business che deve connettersi alla rete aziendale

Se si desidera creare un'applicazione line-of-business, il sito Web potrebbe richiedere l'accesso diretto a servizi o dati nella rete aziendale. Ciò è possibile con **Siti Web**, **Servizi cloud** e **Macchine virtuali**. Il tipo di approccio da adottare deve tenere conto delle seguenti differenze:

-   Siti Web può connettersi in modo sicuro alle risorse locali attraverso l'inoltro del bus di servizio. Questo consente ai servizi della rete aziendale di eseguire attività per conto dell'applicazione Web senza spostare tutto il contenuto nel cloud o impostare una rete virtuale.
-   Servizi cloud e Macchine virtuali possono trarre vantaggio dalla rete virtuale. La rete virtuale consente infatti alle macchine in funzione in Azure di connettersi a una rete locale. Azure diventa quindi un'estensione del data center aziendale del cliente.

### Vorrei ospitare un'API REST o un servizio Web per client mobili

I servizi Web basati su HTTP consentono di supportare una vasta gamma di client, inclusi client mobili. Framework come API Web ASP.NET si integrano con Visual Studio per semplificare la creazione e l'utilizzo di servizi REST. Tali servizi sono esposti da un endpoint Web, pertanto, per il supporto di questo scenario, è possibile utilizzare qualsiasi tecnica di hosting Web in Azure. **Siti Web** è tuttavia un'ottima scelta per l'hosting di API REST. Con Siti Web è possibile:

-   Creare rapidamente un sito Web per ospitare il servizio Web HTTP in uno dei data center Azure distribuiti a livello globale.
-   Migrare i servizi esistenti o crearne di nuovi, potenzialmente sfruttando l'API Web ASP.NET in Visual Studio.
-   Soddisfare i requisiti del contratto di servizio per la disponibilità con una sola istanza oppure scalare orizzontalmente su più macchine dedicate.
-   Utilizzare il sito pubblicato per fornire API REST per qualsiasi client HTTP, compresi quelli mobili.

## <a name="services"></a>Riepiloghi dei servizi

[Siti Web di Azure][] consente di creare in modo rapido siti Web altamente scalabili in Azure. È possibile usare il portale di Azure o gli strumenti della riga di comando per configurare un sito Web con i linguaggi più comuni quali .NET, PHP, Node.js e Python. I framework supportati sono già distribuiti e non richiedono ulteriori passaggi di installazione. La raccolta di Siti Web di Azure contiene molte applicazioni di terze parti, quali Drupal e WordPress, oltre a framework di sviluppo come Django e CakePHP. Dopo aver creato un sito, è possibile migrare un sito Web esistente o creare un sito Web completamente nuovo. Siti Web elimina la necessità di gestire l'hardware fisico e fornisce diverse opzioni di ridimensionamento. È possibile passare da un modello multi-tenant condiviso a una modalità standard in cui determinate macchine dedicate gestiscono il traffico in ingresso. Siti Web consente inoltre l'integrazione con altri servizi di Azure, ad esempio database SQL, bus di servizio e archiviazione. È possibile aggiungere l'elaborazione in background utilizzando la versione di anteprima di [Azure WebJobs SDK][]. In sintesi, Siti Web di Azure consente di concentrarsi con maggiore facilità sullo sviluppo delle applicazioni grazie al supporto di un'ampia gamma di linguaggi, applicazioni open source e metodologie di distribuzione (FTP, Git, Distribuzione Web o TFS). Se non vi sono requisiti particolari per Servizi cloud o Macchine virtuali, un sito Web di Azure è con ogni probabilità la scelta migliore.

[Servizi cloud][] consente di creare applicazioni Web scalabili a disponibilità elevata in un ambiente avanzato di piattaforma distribuita come servizio (PaaS, Platform as a Service). A differenza di Siti Web, un servizio cloud viene creato in un ambiente di sviluppo come Visual Studio prima di essere distribuito in Azure. I framework, come ad esempio PHP, richiedono passaggi di distribuzione personalizzata o attività che consentano di installare il framework all'avvio del ruolo. Il vantaggio principale di Servizi cloud è la capacità di supportare architetture multilivello più complesse. Un singolo servizio cloud può consistere in un ruolo Web front-end e uno o più ruoli di lavoro. Ogni livello può essere ridimensionato in modo indipendente. Vi è inoltre un maggiore livello di controllo sull'infrastruttura dell'applicazione Web. È ad esempio possibile eseguire sessioni di Desktop remoto sulle macchine che eseguono le istanze del ruolo. È inoltre possibile creare script IIS più avanzati e modifiche di configurazione macchina che vengono eseguite all'avvio del ruolo, comprese attività che richiedono il controllo dell'amministratore.

[Macchine virtuali][] consente di eseguire le applicazioni Web su macchine virtuali in Azure. Questa funzionalità è anche nota come infrastruttura distribuita come servizio (IaaS). È possibile creare nuove macchine Windows Server o Linux attraverso il portale o caricare un'immagine di macchina virtuale esistente. Macchine virtuali offre un maggiore controllo sul sistema operativo, sulla configurazione, sul software e sui servizi installati. È una buona opzione per migrare rapidamente complesse applicazioni Web locali al cloud, perché le macchine possono essere spostate in blocco. Con una rete virtuale, è inoltre possibile connettere le macchine virtuali a reti aziendali locali. Come per Servizi cloud, è disponibile l'accesso remoto alle macchine e la possibilità di effettuare le modifiche di configurazione a livello amministrativo. Tuttavia, a differenza di Siti Web e Servizi cloud, è necessario gestire le immagini delle macchine virtuali e l'architettura delle applicazioni interamente a livello di infrastruttura. Un semplice esempio è la necessità di applicare le patch al sistema operativo in proprio.

## <a name="features"></a>Confronto delle funzionalità

Nella tabella seguente vengono confrontate le funzionalità di Siti Web, Servizi cloud e Macchine virtuali per aiutare i clienti a operare la scelta più adatta. Le voci accompagnate da un numero di nota vengono spiegate in maggior dettaglio nelle note presenti sotto alla tabella stessa.

<table>
<colgroup>
<col width="25%" />
<col width="25%" />
<col width="25%" />
<col width="25%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Funzionalità</th>
<th align="left">Siti Web</th>
<th align="left">Servizi cloud (ruoli Web)</th>
<th align="left">Macchine virtuali</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><p>Accesso a servizi quali bus di servizio, archiviazione, database SQL</p></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left">X</td>
</tr>
<tr class="even">
<td align="left"><p>Hosting del livello Web o dei servizi Web di un'architettura multilivello</p></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left">X</td>
</tr>
<tr class="odd">
<td align="left"><p>Hosting del livello intermedio di un'architettura multilivello</p></td>
<td align="left"></td>
<td align="left">X</td>
<td align="left">X</td>
</tr>
<tr class="even">
<td align="left"><p>Supporto integrato di MySQL distribuito come servizio</p></td>
<td align="left">X</td>
<td align="left">X <sup>1</sup></td>
<td align="left">X</td>
</tr>
<tr class="odd">
<td align="left"><p>Supporto per ASP.NET, ASP classico, Node.js, PHP, Python</p></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left">X</td>
</tr>
<tr class="even">
<td align="left"><p>Scalabilità orizzontale a più istanze senza ridistribuzione</p></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left">X <sup>2</sup></td>
</tr>
<tr class="odd">
<td align="left"><p>Supporto per SSL</p></td>
<td align="left">X <sup>3</sup></td>
<td align="left">X</td>
<td align="left">X</td>
</tr>
<tr class="even">
<td align="left"><p>Integrazione di Visual Studio</p></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left">X</td>
</tr>
<tr class="odd">
<td align="left"><p>Debug remoto</p></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left">X</td>
</tr>
<tr class="even">
<td align="left"><p>Distribuzione codice con TFS</p></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left">X</td>
</tr>
<tr class="odd">
<td align="left"><p>Distribuzione codice con GIT, FTP</p></td>
<td align="left">X</td>
<td align="left"></td>
<td align="left">X</td>
</tr>
<tr class="even">
<td align="left"><p>Distribuzione codice con distribuzione Web</p></td>
<td align="left">X</td>
<td align="left"><sup>4</sup></td>
<td align="left">X</td>
</tr>
<tr class="odd">
<td align="left"><p>Supporto WebMatrix</p></td>
<td align="left">X</td>
<td align="left"></td>
<td align="left">X</td>
</tr>
<tr class="even">
<td align="left"><p>Distribuzione quasi istantanea</p></td>
<td align="left">X</td>
<td align="left"></td>
<td align="left"></td>
</tr>
<tr class="odd">
<td align="left"><p>Le istanze condividono contenuti e configurazione</p></td>
<td align="left">X</td>
<td align="left"></td>
<td align="left"></td>
</tr>
<tr class="even">
<td align="left"><p>Aumento della dimensione delle macchine senza ridistribuzione</p></td>
<td align="left">X</td>
<td align="left"></td>
<td align="left"></td>
</tr>
<tr class="odd">
<td align="left"><p>Più ambienti di distribuzione (produzione e gestione temporanea)</p></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left"></td>
</tr>
<tr class="even">
<td align="left"><p>Isolamento della rete con una rete virtuale di Azure</p></td>
<td align="left"></td>
<td align="left">X</td>
<td align="left">X</td>
</tr>
<tr class="odd">
<td align="left"><p>Supporto per Gestione traffico di Azure</p></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left">X</td>
</tr>
<tr class="even">
<td align="left"><p>Accesso al server tramite Desktop remoto</p></td>
<td align="left"></td>
<td align="left">X</td>
<td align="left">X</td>
</tr>
<tr class="odd">
<td align="left"><p>Possibilità di definire/eseguire le attività di avvio</p></td>
<td align="left"></td>
<td align="left">X</td>
<td align="left">X</td>
</tr>
<tr class="even">
<td align="left"><p>Gestione automatica dell'aggiornamento del sistema operativo</p></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left"></td>
</tr>
<tr class="odd">
<td align="left"><p>Monitoraggio integrato degli endpoint</p></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left">X</td>
</tr>
<tr class="even">
<td align="left"><p>Commutazione di piattaforma trasparente (32 bit/64 bit)</p></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left"></td>
</tr>
</tbody>
</table>

<sup>1</sup> I ruoli Web o di lavoro possono integrare MySQL distribuito come servizio tramite le offerte di ClearDB, ma non come parte del flusso di lavoro del portale di gestione.

<sup>2</sup> Sebbene Macchine virtuali consenta la scalabilità orizzontale su più istanze, i servizi in esecuzione nelle macchine devono essere scritti per gestire tale scalabilità. Per indirizzare le richieste su tutte le macchine virtuali, è necessario configurare un servizio di bilanciamento del carico aggiuntivo. È infine necessario creare un gruppo di affinità per tutte le macchine che partecipano nello stesso ruolo, al fine di proteggerle da eventuali avvii simultanei causati da interventi di manutenzione o errori hardware.

<sup>3</sup> Per Siti Web, SSL per i nomi di dominio personalizzati è supportato solo nella modalità standard. Per altre informazioni sull'uso di SSL con Siti Web, vedere [Configurazione di un certificato SSL per un sito Web di Azure][].

<sup>4</sup> Distribuzione Web è supportato per i servizi cloud quando si effettua la distribuzione su ruoli a istanza singola. Per soddisfare i requisiti del contratto di servizio di Azure, tuttavia, i ruoli di produzione richiedono istanze multiple. Distribuzione Web non è pertanto un meccanismo di distribuzione adatto per servizi cloud in produzione.

  [Siti Web di Azure]: http://go.microsoft.com/fwlink/?LinkId=306051
  [Servizi cloud]: http://go.microsoft.com/fwlink/?LinkId=306052
  [Macchine virtuali]: http://go.microsoft.com/fwlink/?LinkID=306053
  [Azure WebJobs SDK]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs
  [ChoicesDiagram]: ./media/choose-web-site-cloud-service-vm/Websites_CloudServices_VMs_2.png
  [Scenari]: #scenarios
  [Riepiloghi dei servizi]: #services
  [Confronto delle funzionalità]: #features
  [attività automatiche]: http://www.windowsazure.com/it-it/documentation/scripts/?services=web-sites
  [.Net]: http://www.windowsazure.com/it-it/develop/net/
  [PHP]: http://www.windowsazure.com/it-it/develop/php/
  [Node.js]: http://www.windowsazure.com/it-it/develop/nodejs/
  [Python]: http://www.windowsazure.com/it-it/develop/python/
  [database SQL]: http://www.windowsazure.com/it-it/documentation/services/sql-database/
  [bus di servizio]: http://www.windowsazure.com/it-it/documentation/services/service-bus/
  [archiviazione]: http://www.windowsazure.com/it-it/documentation/services/storage/
  [Azure Store]: http://www.windowsazure.com/it-it/gallery/store/
  [Configurazione di un certificato SSL per un sito Web di Azure]: http://www.windowsazure.com/it-it/develop/net/common-tasks/enable-ssl-web-site/
