<properties  linkid="manage-scenarios-choose-web-app-service" urlDisplayName="Web Options for Azure" pageTitle="When to Choose Azure Web Sites, Cloud Services, or Virtual Machines" metaKeywords="Cloud Services, Virtual Machines, Web Sites" description="Learn when to use Azure Web Sites, Cloud Services, and Virtual Machines for hosting web applications" metaCanonical="" services="web-sites,virtual-machines,cloud-services" documentationCenter="" title=" Cloud Services" authors="jroth" solutions="" manager="paulettm" editor="mollybos" />

# Confronto tra Siti Web, Servizi cloud e Macchine virtuali di Azure

Azure offre vari modi per ospitare le applicazioni Web: [Siti Web di Azure][1], [Servizi cloud][2] e [Macchine virtuali][3]. Dopo aver esaminato le varie opzioni, si potrebbe essere incerti su quale possa essere quella più adatta alle proprie esigenze oppure alcuni concetti quali IaaS e PaaS potrebbero non risultare del tutto chiari. In questo articolo vengono fornite informazioni utili per comprendere le opzioni disponibili e decidere lo scenario Web migliore da adottare. Sebbene tutte e tre le opzioni consentano di eseguire applicazioni Web altamente scalabili in Azure, vi sono differenze che possono essere utili come guida nel prendere la decisione.

In molte situazioni Siti Web di Azure è l'opzione migliore. Fornisce opzioni semplici e flessibili per sviluppo e gestione e consente di ospitare siti Web con volumi elevati. È possibile creare un nuovo sito Web in modo rapido e utilizzando software diffusi come WordPress o software della raccolta di applicazioni Web, oppure è possibile spostare un sito Web esistente a Siti Web di Azure. È possibile aggiungere l'elaborazione di processi in background utilizzando [Azure WebJobs SDK][4] (attualmente in anteprima).

È inoltre possibile ospitare applicazioni Web in Servizi cloud di Azure o in Macchine virtuali di Azure. Queste opzioni sono scelte adeguate quando il livello Web richiede l'ulteriore livello di controllo e di personalizzazione da queste fornito. Tuttavia, tale aumento di controllo ha un costo in termini di maggiore complessità nella creazione, nella gestione e nella distribuzione delle applicazioni. Nel diagramma riportato di seguito vengono illustrati i livelli di compromesso per tutte e tre le opzioni.

![ChoicesDiagram](./media/choose-web-site-cloud-service-vm/Websites_CloudServices_VMs_2.png)

Siti Web è più facile da configurare, gestire e monitorare, ma sono disponibili meno opzioni di configurazione. La considerazione principale è che, se non vi è un motivo impellente per collocare il proprio front-end Web in Servizi cloud o Macchine virtuali, è opportuno utilizzare Siti Web di Azure. Nel resto di questo documento vengono fornite le informazioni necessarie per poter prendere una decisione ragionata. Sono inclusi:

* [Scenari](#scenarios)
* [Riepiloghi dei servizi](#services)
* [Confronto delle funzionalità](#features)

## <a name="scenarios"></a>Scenari

### Sono un piccolo imprenditore e ho bisogno di ospitare il mio sito in modo economico ma tenendo sempre presente la possibilità di crescita futura.

Siti Web di Azure è un'ottima soluzione per questo scenario, perché è possibile iniziare a utilizzarlo gratuitamente e quindi aggiungere funzionalità man mano che ciò si rende necessario. Ogni sito Web gratuito è dotato, ad esempio, di un dominio fornito da Azure come *your\_company*.azurewebsites.net. Quando si è pronti per iniziare a utilizzare il dominio, è possibile aggiungerlo al vantaggioso prezzo di $ 9,80 al mese (a partire da gennaio del 2014). Sono disponibili molti altri servizi e opzioni di ridimensionamento che consentono l'evoluzione del sito in base all'aumento della domanda da parte degli utenti. Con **Siti Web di Azure** è possibile:

* Iniziare con il livello gratuito e quindi aggiungere risorse in base alle esigenze.
* Utilizzare la raccolta di applicazioni per configurare rapidamente applicazioni Web diffuse, come WordPress.
* Aggiungere funzionalità e servizi di Azure aggiuntivi all'applicazione, in base alle necessità.
* Proteggere il sito Web con HTTPS utilizzando il certificato fornito con il nome di dominio *your\_company*.azurewebsites.net.

### Sono un Web designer o un progettista grafico e voglio progettare e realizzare siti Web per i miei clienti

Per gli sviluppatori Web, Siti Web di Azure fornisce tutto il necessario per la creazione di sofisticate applicazioni Web. Siti Web offre una stretta integrazione con strumenti quali Visual Studio e i database SQL. Con **Siti Web** gli sviluppatori sono in grado di:

* Utilizzare strumenti da riga di comando per [attività automatiche][5].
* Lavorare con i linguaggi più utilizzati, ad esempio [.Net][6], [PHP][7], [Node.js][8] e [Python][9].
* Selezionare tre diversi livelli di scalabilità per aumentare la capacità fino a livelli molto elevati.
* Integrare con altri servizi di Azure, ad esempio [database SQL][10], [bus di servizio][11] e [archiviazione][12] oppure offerte di partner da [Azure Store][13], ad esempio MySQL e MongoDB.
* Integrare con strumenti quali Visual Studio, Git, WebMatrix,
  WebDeploy, TFS e FTP.

### Sto migrando la mia applicazione multilivello con un front-end Web al cloud

Se si esegue un'applicazione multilivello, ad esempio un server Web che comunica con un server di database per archiviare e recuperare i dati del sito Web, sono disponibili diverse opzioni per la migrazione ad Azure. Tali opzioni architetturali includono Siti Web, Servizi cloud e Macchine virtuali. **Siti Web** è un'opzione valida per il livello Web della soluzione e può essere utilizzato con il database SQL di Azure per creare un'architettura su due livelli. Siti Web consente inoltre di eseguire processi in background o a esecuzione prolungata utilizzando l'anteprima di Azure WebJobs SDK. Se è necessaria un'architettura più complessa od opzioni di ridimensionamento più flessibili, Servizi cloud o Macchine virtuali sono una scelta migliore.

**Servizi cloud** consente di:

* Ospitare servizi Web, di livello intermedio e back-end su ruoli Web e
  di lavoro scalabili.
* Ospitare solo i servizi back-end e di livello intermedio su ruoli di
  lavoro, mantenendo il front-end su Siti Web di Azure.
* Scalare servizi front-end e back-end in modo indipendente.

**Macchine virtuali** consente di:

* Migrare più facilmente ambienti altamente personalizzati come
  un'immagine di macchina virtuale.
* Eseguire il software o i servizi che non possono essere configurati su
  Siti Web o Servizi cloud.

### La mia applicazione dipende da ambienti Windows o Linux altamente personalizzati

Se l'applicazione richiede la complessa installazione o configurazione di software e sistema operativo, Macchine virtuali è probabilmente la soluzione migliore. Con **Macchine virtuali** è possibile:

* Utilizzare la raccolta di macchine virtuali per iniziare con un
  sistema operativo, come Windows o Linux, e quindi personalizzarlo in
  base alle esigenze dell'applicazione.
* Creare e caricare un'immagine personalizzata di un server locale
  esistente per eseguirla su una macchina virtuale in Azure.

### Il mio sito utilizza software open source, e voglio ospitarlo in Azure

Tutte e tre le opzioni permettono l'hosting di linguaggi e framework open source. **Servizi cloud** richiede l'utilizzo di attività di avvio per installare e configurare qualsiasi software open source necessario ed eseguibile in Windows. Con **Macchine virtuali**, il software viene installato e configurato sull'immagine della macchina, che può essere basata su Windows o su Linux. Se il framework open source è supportato in Siti Web, questo rappresenta un modo più semplice per ospitare questi tipi di applicazioni, poiché Siti Web può essere configurato automaticamente con i linguaggi e i framework richiesti dall'applicazione. **Siti Web** consente di:

* Utilizzare molti linguaggi open source tra i più diffusi, ad esempio
  [.Net][6], [PHP][7], [Node.js][8] e [Python][9].
* Configurare WordPress, Drupal, Umbraco, DNN e molte altre applicazioni
  Web di terze parti.
* Eseguire la migrazione di un'applicazione esistente o crearne una
  nuova dalla raccolta di applicazioni.

### Ho un'applicazione line-of-business che deve connettersi alla rete aziendale

Se si desidera creare un'applicazione line-of-business, il sito Web potrebbe richiedere l'accesso diretto a servizi o dati nella rete aziendale. Ciò è possibile con **Siti Web**, **Servizi cloud** e **Macchine virtuali**. Il tipo di approccio da adottare deve tenere conto delle seguenti differenze:

* Siti Web può connettersi in modo sicuro alle risorse locali attraverso
  l'inoltro del bus di servizio. Questo consente ai servizi della rete
  aziendale di eseguire attività per conto dell'applicazione Web senza
  spostare tutto il contenuto nel cloud o impostare una rete virtuale.
* Servizi cloud e Macchine virtuali possono trarre vantaggio dalla rete
  virtuale. La rete virtuale consente infatti alle macchine in funzione
  in Azure di connettersi a una rete locale. Azure diventa quindi
  un'estensione del data center aziendale del cliente.

### Vorrei ospitare un'API REST o un servizio Web per client mobili

I servizi Web basati su HTTP consentono di supportare una vasta gamma di client, inclusi client mobili. Framework come API Web ASP.NET si integrano con Visual Studio per semplificare la creazione e l'utilizzo di servizi REST. Tali servizi sono esposti da un endpoint Web, pertanto, per il supporto di questo scenario, è possibile utilizzare qualsiasi tecnica di hosting Web in Azure. **Siti Web** è tuttavia un'ottima scelta per l'hosting di API REST. Con Siti Web è possibile:

* Creare rapidamente un sito Web per ospitare il servizio Web HTTP in
  uno dei data center Azure distribuiti a livello globale.
* Migrare i servizi esistenti o crearne di nuovi, potenzialmente
  sfruttando l'API Web ASP.NET in Visual Studio.
* Soddisfare i requisiti del contratto di servizio per la disponibilità
  con una sola istanza oppure scalare orizzontalmente su più macchine
  dedicate.
* Utilizzare il sito pubblicato per fornire API REST per qualsiasi
  client HTTP, compresi quelli mobili.

## <a name="services"></a>Riepiloghi dei servizi

[Siti Web di Azure][1] consente di creare in modo rapido siti Web altamente scalabili in Azure. È possibile utilizzare il portale di Azure o gli strumenti della riga di comando per configurare un sito Web con linguaggi popolari quali .NET, PHP, Node.js e Python. I framework supportati sono già distribuiti e non richiedono ulteriori passaggi di installazione. La raccolta di siti Web di Azure contiene molte applicazioni di terze parti, quali Drupal e WordPress, oltre a Framework di sviluppo come Django e CakePHP. Dopo aver creato un sito, è possibile migrare un sito Web esistente o creare un sito Web completamente nuovo. Siti Web elimina la necessità di gestire l'hardware fisico e fornisce diverse opzioni di ridimensionamento. È possibile passare da un modello multi-tenant condiviso a una modalità standard in cui determinate macchine dedicate gestiscono il traffico in ingresso. Siti Web consente l'integrazione con altri servizi di Azure, ad esempio database SQL, bus di servizio e archiviazione. È possibile aggiungere l'elaborazione in background utilizzando la versione di anteprima di [Azure WebJobs SDK][4]. In sintesi, Siti Web di Azure consente di concentrarsi con maggiore facilità sullo sviluppo delle applicazioni grazie al supporto di un'ampia gamma di linguaggi, applicazioni open source e metodologie di distribuzione (FTP, Git, distribuzione Web o TFS). Se non vi sono requisiti particolari che richiedono Servizi cloud o Macchine virtuali, un sito Web di Azure è con ogni probabilità la scelta migliore.

[Servizi cloud][2] consente di creare applicazioni Web scalabili a disponibilità elevata in un ambiente avanzato di piattaforma distribuita come servizio (PaaS, Platform as a Service). A differenza di Siti Web, un servizio cloud viene creato in un ambiente di sviluppo come Visual Studio prima di essere distribuito in Azure. I framework, come ad esempio PHP, richiedono passaggi di distribuzione personalizzata o attività che consentano di installare il framework all'avvio del ruolo. Il vantaggio principale di Servizi cloud è la capacità di supportare architetture multilivello più complesse. Un singolo servizio cloud può consistere in un ruolo Web front-end e uno o più ruoli di lavoro. Ogni livello può essere ridimensionato in modo indipendente. Vi è inoltre un maggiore livello di controllo sull'infrastruttura dell'applicazione Web. È ad esempio possibile eseguire sessioni di Desktop remoto sulle macchine che eseguono le istanze del ruolo. È inoltre possibile creare script IIS più avanzati e modifiche di configurazione macchina che vengono eseguite all'avvio del ruolo, comprese attività che richiedono il controllo dell'amministratore.

[Macchine virtuali][3] consente di eseguire le applicazioni Web su macchine virtuali in Azure. Questa funzionalità è anche nota come infrastruttura distribuita come servizio (IaaS). È possibile creare nuove macchine Windows Server o Linux attraverso il portale o caricare un'immagine di macchina virtuale esistente. Macchine virtuali offre un maggiore controllo sul sistema operativo, sulla configurazione, sul software e sui servizi installati. È una buona opzione per migrare rapidamente complesse applicazioni Web locali al cloud, perché le macchine possono essere spostate in blocco. Con una rete virtuale, è inoltre possibile connettere le macchine virtuali a reti aziendali locali. Come per Servizi cloud, è disponibile l'accesso remoto alle macchine e la possibilità di effettuare le modifiche di configurazione a livello amministrativo. Tuttavia, a differenza di Siti Web e Servizi cloud, è necessario gestire le immagini delle macchine virtuali e l'architettura delle applicazioni interamente a livello di infrastruttura. Un semplice esempio è la necessità di applicare le patch al sistema operativo in proprio.

## <a name="features"></a>Confronto delle funzionalità

Nella tabella seguente vengono confrontate le funzionalità di Siti Web, Servizi cloud e Macchine virtuali per aiutare i clienti a operare la scelta più adatta. Le voci accompagnate da un numero di nota vengono spiegate in maggior dettaglio nelle note presenti sotto alla tabella stessa.

<table  cellspacing="0" border="1">
<tr>
   <th  align="left" valign="middle">Funzionalità</th>

   <th  align="left" valign="middle">Siti Web</th>

   <th  align="left" valign="middle">Servizi cloud (ruoli Web)</th>

   <th  align="left" valign="middle">Macchine virtuali</th>

</tr>

<tr>
   <td  valign="middle"><p>Accesso a servizi quali bus di servizio, archiviazione, database SQL</p>
</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

</tr>

<tr>
   <td  valign="middle"><p>Hosting del livello Web o dei servizi Web di un'architettura multilivello</p>
</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

</tr>

<tr>
   <td  valign="middle"><p>Hosting del livello intermedio di un'architettura multilivello</p>
</td>

   <td  valign="middle" />

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

</tr>

<tr>
   <td  valign="middle"><p>Supporto integrato di MySQL distribuito come servizio</p>
</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X <sup>1</sup>
</td>

   <td  valign="middle">X</td>

</tr>

<tr>
   <td  valign="middle"><p>Supporto per ASP.NET, ASP classico, Node.js, PHP, Python</p>
</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

</tr>

<tr>
   <td  valign="middle"><p>Scalabilità orizzontale a più istanze senza ridistribuzione</p>
</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X <sup>2</sup>
</td>

</tr>

<tr>
   <td  valign="middle"><p>Supporto per SSL</p>
</td>

   <td  valign="middle">X <sup>3</sup>
</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

</tr>

<tr>
   <td  valign="middle"><p>Integrazione di Visual Studio</p>
</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

</tr>

<tr>
   <td  valign="middle"><p>Debug remoto</p>
</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

</tr>

<tr>
   <td  valign="middle"><p>Distribuzione codice con TFS</p>
</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

</tr>

<tr>
   <td  valign="middle"><p>Distribuzione codice con GIT, FTP</p>
</td>

   <td  valign="middle">X</td>

   <td  valign="middle" />

   <td  valign="middle">X</td>

</tr>

<tr>
   <td  valign="middle"><p>Distribuzione codice con distribuzione Web</p>
</td>

   <td  valign="middle">X</td>

   <td  valign="middle"><sup>4</sup>
</td>

   <td  valign="middle">X</td>

</tr>

<tr>
   <td  valign="middle"><p>Supporto WebMatrix</p>
</td>

   <td  valign="middle">X</td>

   <td  valign="middle" />

   <td  valign="middle">X</td>

</tr>

<tr>
   <td  valign="middle"><p>Distribuzione quasi istantanea</p>
</td>

   <td  valign="middle">X</td>

   <td  valign="middle" />

   <td  valign="middle" />

</tr>

<tr>
   <td  valign="middle"><p>Le istanze condividono contenuti e configurazione</p>
</td>

   <td  valign="middle">X</td>

   <td  valign="middle" />

   <td  valign="middle" />

</tr>

<tr>
   <td  valign="middle"><p>Aumento della dimensione delle macchine senza ridistribuzione</p>
</td>

   <td  valign="middle">X</td>

   <td  valign="middle" />

   <td  valign="middle" />

</tr>

<tr>
   <td  valign="middle"><p>Più ambienti di distribuzione (produzione e gestione temporanea)</p>
</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

   <td  valign="middle" />

</tr>

<tr>
   <td  valign="middle"><p>Isolamento della rete con una rete virtuale di Azure</p>
</td>

   <td  valign="middle" />

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

</tr>

<tr>
   <td  valign="middle"><p>Supporto per Gestione traffico di Azure</p>
</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

</tr>

<tr>
   <td  valign="middle"><p>Accesso al server tramite Desktop remoto</p>
</td>

   <td  valign="middle" />

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

</tr>

<tr>
   <td  valign="middle"><p>Possibilità di definire/eseguire le attività di avvio</p>
</td>

   <td  valign="middle" />

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

</tr>

<tr>
   <td  valign="middle"><p>Gestione automatica dell'aggiornamento del sistema operativo</p>
</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

   <td  valign="middle" />

</tr>

<tr>
   <td  valign="middle"><p>Monitoraggio integrato degli endpoint</p>
</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

</tr>

<tr>
   <td  valign="middle"><p>Commutazione di piattaforma trasparente (32 bit/64 bit)</p>
</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

   <td  valign="middle" />

</tr>

</table>

<sup>1</sup> I ruoli Web o di lavoro possono integrare MySQL distribuito come servizio tramite le offerte di ClearDB, ma non come parte del flusso di lavoro del portale di gestione.

<sup>2</sup> Sebbene Macchine virtuali consenta la scalabilità orizzontale su più istanze, i servizi in esecuzione nelle macchine devono essere scritti per gestire tale scalabilità. Per indirizzare le richieste su tutte le macchine virtuali, è necessario configurare un servizio di bilanciamento del carico aggiuntivo. È infine necessario creare un gruppo di affinità per tutte le macchine che partecipano nello stesso ruolo, al fine di proteggerle da eventuali avvii simultanei causati da interventi di manutenzione o errori hardware.

<sup>3</sup> Per Siti Web, SSL per i nomi di dominio personalizzati è supportato solo nella modalità standard. Per ulteriori informazioni sull'utilizzo di SSL con Siti Web, vedere [Configurazione di un certificato SSL per un sito Web di Azure][14].

<sup>4</sup>Distribuzione Web è supportato per i servizi cloud quando si effettua la distribuzione su ruoli a istanza singola. Per soddisfare i requisiti del contratto di servizio di Azure, tuttavia, i ruoli di produzione richiedono istanze multiple. Distribuzione Web non è pertanto un meccanismo di distribuzione adatto per servizi cloud in produzione.



[1]: http://go.microsoft.com/fwlink/?LinkId=306051
[2]: http://go.microsoft.com/fwlink/?LinkId=306052
[3]: http://go.microsoft.com/fwlink/?LinkID=306053
[4]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs
[5]: http://www.windowsazure.com/en-us/documentation/scripts/?services=web-sites
[6]: http://www.windowsazure.com/en-us/develop/net/
[7]: http://www.windowsazure.com/en-us/develop/php/
[8]: http://www.windowsazure.com/en-us/develop/nodejs/
[9]: http://www.windowsazure.com/en-us/develop/python/
[10]: http://www.windowsazure.com/en-us/documentation/services/sql-database/
[11]: http://www.windowsazure.com/en-us/documentation/services/service-bus/
[12]: http://www.windowsazure.com/en-us/documentation/services/storage/
[13]: http://www.windowsazure.com/en-us/gallery/store/
[14]: http://www.windowsazure.com/en-us/develop/net/common-tasks/enable-ssl-web-site/