<properties urlDisplayName="SharePoint on Azure" pageTitle="Distribuzione di SharePoint 2010 in Macchine virtuali di Azure" metaKeywords="" description="Informazioni sugli scenari supportati per l'uso di SharePoint 2010 in macchine virtuali di Azure." metaCanonical="" services="virtual-machines" documentationCenter="" title="Distribuzione di SharePoint in Macchine virtuali di Azure" authors="josephd" solutions="" manager="timlt" editor="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="josephd" />

# Distribuzione di SharePoint in Macchine virtuali di Azure

## Sunto

Microsoft SharePoint Server 2010 offre ampia flessibilità nella distribuzione, consentendo alle organizzazioni di identificare gli scenari appropriati e di allinearli alle esigenze e agli obiettivi aziendali. Il servizio Macchine virtuali di Azure, ospitato e gestito nel cloud, offre un'infrastruttura completa, affidabile e disponibile per supportare vari carichi di lavoro su richiesta di applicazioni e database, ad esempio le distribuzione di Microsoft SQL Server e SharePoint.

Anche se Macchine virtuali di Azure supporta più carichi di lavoro, questo documento è incentrato sulle distribuzioni di SharePoint. Grazie a Macchine virtuali di Azure, le organizzazioni possono creare e gestire rapidamente un'infrastruttura di SharePoint, con il provisioning e l'accesso a qualsiasi host a livello globale. Questo servizio offre il controllo completo e la gestione di processori, RAM, intervalli della CPU e altre risorse delle macchine virtuali (VM, Virtual Machine) di SharePoint.

Macchine virtuali di Azure riduce la necessità di acquisire hardware, consentendo alle organizzazioni di dedicarsi alla creazione e alla gestione di un'infrastruttura scalabile in base a specifiche esigenze, senza affrontare costi iniziali elevati e complessità. In questo modo possono innovare, sperimentare e ripetere le procedure in poche ore anziché in diversi giorni e settimane come nelle tradizionali distribuzioni.

### A chi è destinato questo documento?

Questo documento è stato concepito per i professionisti IT. Inoltre, decisori come architetti e amministratori di sistema possono usare queste informazioni e gli scenari proposti per pianificare e progettare un'infrastruttura SharePoint virtualizzata in Azure.

### Perché leggere questo documento?

In questo documento viene illustrato come configurare e distribuire SharePoint all'interno di Macchine virtuali di Azure. Viene inoltre spiegato perché questo tipo di distribuzione può offrire vantaggi a organizzazioni di diverse dimensioni.

## Passaggio al cloud computing

Secondo la definizione di Gartner, il cloud computing è una modalità secondo la quale funzionalità IT ampiamente scalabili vengono distribuite sotto forma di servizio a clienti esterni tramite tecnologie Internet. I termini più significativi in questa definizione sono "scalabili", "servizio" e "Internet". In breve, il cloud computing può essere definito come un insieme di servizi IT che vengono **distribuiti e trasferiti tramite Internet** e che sono **scalabili su richiesta**.

Indubbiamente il cloud computing rappresenta una svolta significativa negli attuali ambienti IT. Nel passato i temi in discussione riguardavano consolidamento e costi. Oggi invece l'interesse è rivolto verso la nuova categoria di vantaggi che il colud computing può offrire, trasformando il modo in cui le organizzazioni usufruiscono dell'IT, grazie alla possibilità di sfruttare una potenza di nuova generazione. Il cloud computing sta cambiando radicalmente il mondo dell'IT, con un impatto significativo su ogni ruolo, dai provider di servizi e gli architetti di sistemi agli sviluppatori e gli utenti finali.

La ricerca dimostra che i tre principali fattori trainanti per l'adozione del cloud sono flessibilità, centralità ed economicità:

-   **Flessibilità**: il cloud computing offre alle organizzazioni la possibilità di sfruttare più rapidamente le nuove opportunità e di rispondere a mutevoli esigenze aziendali.

-   **Centralità**: il cloud computing consente ai reparti IT di tagliare drasticamente i costi dell'infrastruttura. Grazie all'astrazione dell'infrastruttura e al raggruppamento delle risorse, infatti, è possibile gestire l'IT più come un'utilità che non come un insieme di complicati servizi e sistemi. Inoltre, è ora possibile assegnare all'IT ruoli più innovativi e strategici.

-   **Economicità**: il cloud computing consente di ridurre i costi associati all'erogazione di funzionalità IT e di incrementare l'utilizzo e l'efficienza del data center. I costi dell'erogazione sono inferiori perché con il cloud computing le applicazioni e le risorse diventano self-service e l'utilizzo di tali risorse diventa misurabile in nuovi modi più precisi. Inoltre, l'hardware viene maggiormente sfruttato, grazie al raggruppamento e all'astrazione delle risorse di archiviazione, calcolo e rete.

## Modelli di erogazione dei servizi cloud

In breve, il cloud computing rappresenta l'astrazione dei servizi IT. Questi servizi possono spaziare dall'infrastruttura di base ad applicazioni complete. Gli utenti finali richiedono e usano servizi astratti senza la necessità di gestire (o addirittura di conoscere) i relativi componenti. Oggi, nel settore sono stati identificati tre modelli di erogazione dei servizi cloud, ognuno dei quali offre un rapporto diverso tra controllo/flessibilità e costi complessivi:

-   **Infrastructure distribuita come servizio** (IaaS): l'infrastruttura virtuale che ospita le macchine virtuali e prevalentemente le applicazioni esistenti.

-   **Piattaforma distribuita come servizio** (PaaS): l'infrastruttura di applicazioni cloud che fornisce un ambiente host di applicazioni su richiesta.

-   **Software come un servizio** (SaaS): modello di servizi cloud in cui un'applicazione viene distribuita tramite Internet e ai clienti vengono addebitati i costi per uso, ad esempio Microsoft Office 365 o Microsoft CRM Online.

Nella figura 1 è illustrata la tassonomia dei servizi cloud e la relativa associazione dei componenti di un'infrastruttura IT. Con un modello locale, il cliente è responsabile della gestione dell'intero stack, dalla connettività di rete alle applicazioni. Con IaaS, i livelli inferiori dello stack vengono gestiti da un fornitore, mentre il cliente è responsabile della gestione del sistema operativo e delle applicazioni. Con PaaS, il fornitore della piattaforma fornisce e gestisce tutti i componenti, dalla connettività di rete fino al runtime. Il cliente è responsabile solo della gestione di applicazioni e dati. L'offerta di Azure rientra principalmente in questo modello. Infine, con SaaS, il fornitore provvede alle applicazioni e all'astrazione di tutti i servizi da tutti i componenti sottostanti.

Figura 1: tassonomia dei servizi cloud

![azure-sharepoint-wp-1][azure-sharepoint-wp-1]

## Macchine virtuali di Azure

Con Macchine virtuali di Azure è stata introdotta una funzionalità che offre il pieno controllo e la gestione delle VM, oltre a funzionalità estese di rete virtuale. Questa offerta assicura vantaggi notevoli alle organizzazioni, come illustrato di seguito.

-   **Gestione**: le VM possono essere gestite centralmente nel cloud con il pieno controllo per configurare e mantenere l'infrastruttura.

-   **Mobilità delle applicazioni**: i dischi rigidi virtuali (VHD) possono essere spostati tra ambienti locali e basati su cloud. Non è necessario ricompilare le applicazioni per l'esecuzione nel cloud.

-   **Accesso ad applicazioni server Microsoft**: è possibile eseguire nel colud le stesse applicazioni e infrastrutture usate in locale, tra cui Microsoft SQL Server, SharePoint Server, Windows Server e Active Directory.

Macchine virtuali di Azure è una piattaforma semplice, aperta, flessibile e potente che consente alle organizzazioni di distribuire ed eseguire VM Windows Server e Linux in pochi minuti.

-   **Semplice**: con Macchine virtuali Azure, le procedure di creazione, migrazione, distribuzione e gestione di VM nel cloud sono semplici e immediate. Le organizzazioni possono effettuare la migrazione dei carichi di lavoro in Azure senza la necessità di modificare il codice esistente oppure possono configurare nuove VM in Azure con pochi clic del mouse. L'offerta prevede inoltre assistenza per lo sviluppo di nuove applicazioni cloud grazie all'integrazione delle funzionalità IaaS e PaaS di Azure.

-   **Aperta e flessibile**: Azure è una piattaforma aperta che assicura flessibilità alle organizzazioni. È possibile iniziare da un'immagine predefinita della raccolta immagini oppure creare e usare dischi rigidi virtuali personalizzati e locali da caricare nella raccolta immagini. Sono inoltre disponibili versioni di Linux commerciali e per community.

-   **Potente**: Azure è una piattaforma cloud aziendale per l'esecuzione di applicazioni come SQL Server, SharePoint Server o Active Directory nel cloud. Le organizzazioni possono creare soluzioni ibride, locali e cloud, con connettività VPN tra il data center Azure e le loro reti.

## SharePoint in Macchine virtuali di Azure

La flessibilità di SharePoint 2010 supporta la maggior parte dei carichi di lavoro in una distribuzione di Macchine virtuali di Azure. Macchine virtuali di Azure rappresenta la soluzione ottimale per scenari FIS (SharePoint Server for Internet Sites) e di sviluppo. Analogamente, sono supportati anche i principali carichi di lavoro di SharePoint. Se un'organizzazione ha l'esigenza di gestire e controllare la propria implementazione di SharePoint 2010 sfruttando allo stesso tempo le opzioni per la virtualizzazione nel cloud, Macchine virtuali di Azure è la scelta ideale per la distribuzione.

L'offerta Macchine virtuali di Azure è ospitata e gestita nel cloud. Assicura flessibilità nella distribuzione e costi inferiori, grazie alla riduzione delle spese di capitale associate all'approvvigionamento dell'hardware. Con una maggiore flessibilità dell'infrastruttura, le organizzazioni possono distribuire SharePoint Server in poche ore anziché in diversi giorni o settimane. Grazie a Macchine virtuali di Azure, le organizzazioni possono inoltre distribuire carichi di lavoro di SharePoint nel cloud in base a un modello di pagamento a consumo. Con l'aumento dei carichi di lavoro di SharePoint, le organizzazioni possono ampliare rapidamente l'infrastruttura e in seguito, se le esigenze di elaborazione diminuiscono, possono restituire le risorse non più necessarie, assumendosi di fatto solo i costi di quelle che usano.

### Cambio di approccio per l'IT

Molte organizzazioni affidano in appalto i componenti comuni dell'infrastruttura IT e della gestione, ad esempio hardware, sistemi operativi, sicurezza, archiviazione dei dati e backup, mantenendo allo stesso tempo il controllo delle applicazioni mission-critical come SharePoint Server. Delegando tutti i livelli dei servizi non mission-critical delle loro piattaforme IT a un provider virtuale, le organizzazioni possono indirizzare i loro obiettivi IT verso i servizi di base e mission-critical di SharePoint e acquisire valore di business con i progetti di SharePoint, anziché dedicare più tempo alla configurazione dell'infrastruttura.

### Distribuzione più rapida

Le attività di supporto e distribuzione di un'infrastruttura SharePoint su vasta scala possono richiedere tempi più lunghi del previsto rispetto ai requisiti aziendali. Possono infatti essere necessarie diverse settimane, o addirittura mesi, per completare le procedure di creazione, test e preparazione di server e farm di SharePoint e la successiva distribuzione in un ambiente di produzione, a seconda dei processi e dei vincoli dell'organizzazione. Con Macchine virtuali di Azure, le organizzazioni possono invece distribuire rapidamente i loro carichi di lavoro di SharePoint, senza spese di capitale associate all'hardware. In questo modo possono sfruttare la flessibilità dell'infrastruttura per completare la distribuzione in poche ore anziché in diversi giorni o settimane.

### Scalabilità

Senza la necessità di occuparsi delle procedure di distribuzione, test e preparazione di server e farm fisici di SharePoint, le organizzazioni possono ampliare e acquistare capacità di calcolo su richiesta, con pochissimo preavviso. Con l'aumento dei requisiti dei carichi di lavoro di SharePoint, le organizzazioni possono rapidamente espanderne l'infrastruttura nel cloud. Analogamente, quando le esigenze di elaborazione diminuiscono, possono ridurre le risorse e assumersi solo i costi di quelle che usano. Macchine virtuali di Azure consente di ridurre le spese iniziali e gli impegni a lungo termine, assicurando alle organizzazioni la massima scalabilità nello sviluppo e nella gestione delle infrastrutture di SharePoint. Anche in questo caso, il risultato è la possibilità di innovare, sperimentare e ripetere le procedure in poche ore anziché in diversi giorni e settimane come nelle tradizionali distribuzioni.

### Misurazione dell'utilizzo

L'offerta Macchine virtuali di Azure assicura la potenza di elaborazione, la memoria e lo spazio di archiviazione per gli scenari di SharePoint, i cui prezzi sono in genere basati sull'utilizzo delle risorse. Le organizzazioni si assumono solo i costi delle risorse che usano e il servizio fornisce tutta la capacità necessaria per gestire l'infrastruttura SharePoint. Per altre informazioni su prezzi e fatturazione, vedere [Panoramica dei prezzi di Azure][Panoramica dei prezzi di Azure]. Si noti che per il trasferimento di spazio di archiviazione e dati all'esterno del cloud di Azure da una rete locale sono previsti costi nominali. Non sono invece previsti addebiti per il caricamento di dati.

### Flessibilità

Il servizio Macchine virtuali di Azure offre agli sviluppatori la flessibilità per scegliere il linguaggio o l'ambiente di runtime che preferiscono, con il supporto ufficiale per .NET, Node.js, Java e PHP, oltre alla possibilità di scegliere i loro strumenti, grazie al supporto per Microsoft Visual Studio, WebMatrix, Eclipse e editor di testo. Inoltre, Microsoft assicura un percorso a basso costo e a basso rischio verso il cloud e offre procedure semplici e convenienti di provisioning e distribuzione per le esigenze reporting nel cloud, grazie all'accesso a funzionalità di business intelligence (BI) da diversi dispositivi e postazioni. Infine, con l'offerta Azure, oltre a trasferire i dischi rigidi virtuali nel cloud, è anche possibile effettuarne una copia ed eseguirli in locale tramite un altro provider di servizi cloud, purché si disponga della licenza appropriata.

## Processo di provisioning

In questa sottosezione vengono descritti i punti di forza di base di Azure. La **raccolta immagini** in Azure fornisce l'elenco di VM preconfigurate disponibili. Gli utenti possono pubblicare contenuti di SharePoint Server, SQL Server, Windows Server e altri ISO/VHD nella raccolta immagini. Per semplificare la creazione di VM, nella raccolta vengono create e pubblicate immagini di base. Gli utenti autorizzati possono usare queste immagini per creare la macchina virtuale desiderata. Per altre informazioni, vedere [Creazione di una macchina virtuale che esegue Windows Server 2008 R2][Creazione di una macchina virtuale che esegue Windows Server 2008 R2] sul sito Web di Azure. Nella figura 2 è illustrata la procedura di base per creare una VM tramite il portale di gestione di Azure:

Figura 2: panoramica della procedura per la creazione di una VM

![azure-sharepoint-wp-2][azure-sharepoint-wp-2]

Gli utenti possono anche caricare un'immagine sottoposta a sysprep nel portale di gestione di Azure. Per altre informazioni, vedere [Creazione e caricamento di un disco rigido virtuale][Creazione e caricamento di un disco rigido virtuale]. Nella figura 3 è illustrata la procedura di base per caricare un'immagine e creare una VM:

Figura 3: panoramica della procedura per il caricamento di un'immagine

![azure-sharepoint-wp-3][azure-sharepoint-wp-3]

### Distribuzione di SharePoint 2010 in Azure

Per distribuire SharePoint 2010 in Azure, eseguire la procedura seguente:

<ol>
<li>
Accedere al [portale di gestione di Azure][portale di gestione di Azure] con l'account della sottoscrizione di Azure.

-   Se non si dispone di un account Azure, [iscriversi per una versione di valutazione gratuita][iscriversi per una versione di valutazione gratuita].

</li>
<li>
Per creare una macchina virtuale con il sistema operativo di base, nel portale di gestione di Azure fare clic su **NUOVO**, quindi su **CALCOLO**, su **MACCHINA VIRTUALE** e infine su **DA RACCOLTA**.

</li>
<li>
Verrà visualizzata la finestra di dialogo **Scegli un'immagine**. Fare clic sull'immagine della piattaforma **Windows Server 2008 R2 SP1** e quindi sulla freccia a destra.

</li>
<li>
Verrà visualizzata la finestra di dialogo ***Configurazione macchina virtuale***. Specificare le informazioni seguenti:

-   Immettere un nome per **NOME MACCHINA VIRTUALE**.
-   Selezionare un valore appropriato per **DIMENSIONE**.
    -   Per ambienti di produzione (server applicazioni e database di SharePoint), è consigliabile scegliere l'opzione A3 *(4 core, 7 GB di memoria)*.
-   In **NUOVO NOME UTENTE** digitare un nome di account amministratore locale.
-   Nella casella **NUOVA PASSWORD** immettere una password complessa.
-   Nella casella **CONFERMA** ridigitare la password, quindi fare clic sulla freccia a destra.

<li>
Verrà visualizzata la seconda finestra di dialogo **Configurazione macchina virtuale**. Specificare le informazioni seguenti:

<ul>
<li>
Nella casella **SERVIZIO CLOUD** scegliere una delle opzioni seguenti:

-   **Crea un nuovo servizio cloud**; in questo caso è necessario specificare anche un nome DNS del servizio cloud.
-   Selezionare un servizio cloud esistente.

<li>
Nella casella **REGIONE/GRUPPO DI AFFINITÀ/RETE VIRTUALE** selezionare l'area in cui verrà ospitata l'immagine virtuale.

</li>
<li>
Nella casella **ACCOUNT DI ARCHIVIAZIONE** scegliere una delle opzioni seguenti:

<ul>
<li>
**Usa un account di archiviazione generato automaticamente**.

</li>
<li>
Selezionare un nome di account di archiviazione esistente.

</li>
-   Verrà creato automaticamente un solo account di archiviazione per ogni area. Tutte le altre macchine virtuali create con questa impostazione verranno inserite in questo account di archiviazione.
-   È possibile creare un massimo di 20 account di archiviazione.
-   Per altre informazioni, vedere [Creare un account di archiviazione in Azure][Creare un account di archiviazione in Azure].

</li>
<li>
Nella casella **SET DI DISPONIBILITÀ** selezionare **(nessuno)** e quindi fare clic sulla freccia a destra.

</li>
</ul>
</li>
</ul>
</li>
<li>
Nella terza finestra di dialogo **Configurazione macchina virtuale** fare clic sul segno di spunta per creare la macchina virtuale.

</li>
<li>
Per connettersi alla macchina virtuale:

-   Aprire la macchina virtuale tramite Desktop remoto.
-   Nel portale di gestione di Azure selezionare la macchina virtuale e quindi la pagina **DASHBOARD**.
-   Fare clic su **Connetti**.

</li>
<li>
Creare la macchina virtuale di SQL Server con una delle opzioni seguenti:

-   Creare una macchina virtuale di SQL Server 2012 eseguendo i passaggi da 1 a 7 descritti in precedenza, ma nel **passaggio 3** usare l'immagine di SQL Server 2012 anziché quella di Windows Server 2008 R2 SP1. Per altre informazioni, vedere [Provisioning di una macchina virtuale di SQL Server in Azure][Provisioning di una macchina virtuale di SQL Server in Azure].
    -   Se si sceglie questa opzione, tramite il processo di provisioning viene mantenuta una copia dei file del programma di installazione di SQL Server 2012 nel percorso della directory *C:\\SQLServer\_11.0\_Full*, in modo che sia possibile personalizzare l'installazione. È ad esempio possibile convertire l'installazione di valutazione di SQL Server 2012 in una versione con licenza usando il codice di licenza.
-   Usare l'Utilità preparazione sistema (SysPrep) di SQL Server per installare SQL Server nella macchina virtuale con il sistema operativo di base, come illustrato in precedenza nei passaggi da 1 a 7. Per altre informazioni, vedere [Installare SQL Server 2012 tramite SysPrep][Installare SQL Server 2012 tramite SysPrep].
-   Usare il prompt dei comandi per installare SQL Server. Per altre informazioni, vedere [Installare SQL Server 2012 dal prompt dei comandi][Installare SQL Server 2012 dal prompt dei comandi].
-   Usare supporti compatibili con SQL Server e il proprio codice di licenza per installare SQL Server nella macchina virtuale con il sistema operativo di base, come illustrato in precedenza nei passaggi da 1 a 7.

</li>
<li>
Creare la farm di SharePoint eseguendo i passaggi secondari seguenti:

-   Passaggio secondario 1: Configurare la sottoscrizione di Azure usando i file di script.
-   Passaggio secondario 2: Eseguire il provisioning dei server SharePoint creando un'altra macchina virtuale con il sistema operativo di base, come illustrato in precedenza nei passaggi da 1 a 7. Per creare un server SharePoint nella macchina virtuale corrente, scegliere una delle opzioni seguenti:
    -   Provisioning tramite l'interfaccia utente grafica di SharePoint:
        -   Per creare una farm di SharePoint ed eseguirne il provisioning, vedere [Creare una farm di Microsoft SharePoint Server][Creare una farm di Microsoft SharePoint Server].
        -   Per aggiungere un server Web o un server applicazioni alla farm, vedere [Aggiungere server Web o server applicazioni a farm in SharePoint 2013][Aggiungere server Web o server applicazioni a farm in SharePoint 2013].
        -   Per aggiungere un server di database a una farm esistente, vedere [Aggiungere un server di database a una farm esistente in SharePoint 2013][Aggiungere un server di database a una farm esistente in SharePoint 2013].

            -   Per usare SQL Server 2012 per la farm di SharePoint, è necessario scaricare e installare il Service Pack 1 per SharePoint Server 2010 dopo aver installato l'applicazione e aver scelto di non configurare il server. Per altre informazioni, vedere [Service Pack 1 per Microsoft SharePoint Server 2010][Service Pack 1 per Microsoft SharePoint Server 2010].
            -   Per sfruttare le funzionalità di business intelligence di SQL Server, è consigliabile installare SharePoint Server come server farm anziché come server autonomo. Per altre informazioni, vedere [Installare le funzionalità di business intelligence di SQL Server 2012][Installare le funzionalità di business intelligence di SQL Server 2012].

    -   Provisioning tramite Microsoft Windows PowerShell: È possibile usare lo strumento da riga di comando Psconfig come interfaccia alternativa per eseguire diverse operazioni che controllano la modalità di provisioning dei prodotti SharePoint 2010. Per altre informazioni, vedere [Informazioni di riferimento sullo strumento da riga di comando Psconfig][Informazioni di riferimento sullo strumento da riga di comando Psconfig].
-   Passaggio secondario 3: Configurare SharePoint. Quando ogni macchina virtuale di SharePoint è pronta per essere usata, configurare SharePoint Server in ogni server scegliendo una delle opzioni seguenti:
    -   Configurare SharePoint dalla GUI.
    -   Configurare SharePoint tramite Windows PowerShell. Per altre informazioni, vedere [Installare SharePoint Server 2010 tramite Windows PowerShell][Installare SharePoint Server 2010 tramite Windows PowerShell].
        -   È anche possibile usare CodePlex Project AutoSPInstaller, che comprende script di Windows PowerShell, un file di input XML e un file batch di Microsoft Windows standard. AutoSPInstaller fornisce un framework per uno script di installazione di SharePoint 2010 basato su Windows PowerShell. Per altre informazioni, vedere [CodePlex: AutoSPInstaller][CodePlex: AutoSPInstaller]. **Nota**: assicurarsi di configurare la sicurezza nell'endpoint del portale di gestione e di impostare una porta in ingresso in Windows Firewall della macchina virtuale. Quindi, verificare che sia possibile avviare una sessione remota di Windows PowerShell in uno dei server applicazioni SharePoint aprendo una sessione di Windows PowerShell con credenziali di amministratore.

</li>
<li>
Al termine dello script, connettersi alla macchina virtuale usando l'apposito dashboard.

</li>
<li>
Verificare la configurazione di SharePoint: accedere al server SharePoint e quindi verificare la configurazione usando Amministrazione centrale.

</li>
</ol>
### Creazione e caricamento di un disco rigido virtuale

È anche possibile creare le proprie immagini e caricarle in Azure come file VHD. Per creare e caricare un file VHD in Azure, eseguire la procedura seguente:

1.  Creare l'immagine abilitata per Hyper-V: usare la console di gestione di Hyper-V per creare il disco rigido virtuale compatibile con Hyper-V. Per altre informazioni, vedere [Creare dischi rigidi virtuali][Creare dischi rigidi virtuali].
2.  Creare un account di archiviazione in Azure: l'account di archiviazione di Azure è necessario per caricare un file VHD da usare per creare una macchina virtuale. Questo account può essere creato tramite il portale di gestione di Azure. Per altre informazioni, vedere [Creare un account di archiviazione in Azure][Creazione e caricamento di un disco rigido virtuale].
3.  Preparare l'immagine da caricare: prima di caricare l'immagine in Azure, è necessario generalizzarla tramite il comando SysPrep. Per altre informazioni, vedere [Introduzione all'utilizzo di SysPrep][Introduzione all'utilizzo di SysPrep].
4.  Caricare l'immagine in Azure: per caricare un'immagine contenuta in un file VHD, è necessario creare e installare un certificato di gestione. Ottenere l'identificazione personale del certificato e l'ID sottoscrizione. Impostare la connessione e caricare il file VHD tramite lo strumento da riga di comando CSUpload. Per altre informazioni, vedere [Caricare l'immagine in Azure][Creazione e caricamento di un disco rigido virtuale].

## Scenari di utilizzo

In questa sezione vengono illustrati alcuni importanti scenari di clienti per le distribuzioni di SharePoint tramite Macchine virtuali di Azure. Ogni scenario è diviso in due parti, ovvero una breve descrizione seguita dai passaggi per iniziare.

### Scenario 1: Semplice ambiente di sviluppo e test di SharePoint

#### Descrizione

Le organizzazioni cercano soluzioni più flessibili per creare applicazioni di SharePoint e configurare gli ambienti per i test e lo sviluppo all'interno e in delocalizzazione. Fondamentalmente, desiderano abbreviare i tempi richiesti per configurare i progetti di sviluppo di applicazioni di SharePoint e ridurre i costi incrementando l'utilizzo degli ambienti di test. Potrebbero ad esempio scegliere di eseguire test di carico su richiesta in SharePoint Server e svolgere test di accettazione con più utenti simultanei in diverse località geografiche. Analogamente, l'integrazione di team interni e delocalizzati rappresenta un'esigenza aziendale sempre più importante per molte organizzazioni odierne.

In questo scenario viene illustrato come usare farm di SharePoint preconfigurate per i carichi di lavoro di sviluppo e test. Una topologia di distribuzione di SharePoint è esattamente uguale a una distribuzione virtualizzata in locale. Le attuali competenze IT sono perfettamente adeguate per la distribuzione di Macchine virtuali di Azure, con il principale vantaggio costituito da un passaggio quasi completo da spese di capitale a spese operative, in quanto non è necessario l'acquisto iniziale di server fisici. Le organizzazioni possono eliminare i costi di capitale per l'hardware dei server e acquisire flessibilità grazie a una riduzione sostanziale dei tempi di provisioning necessari per creare, configurare o estendere una farm di SharePoint per un ambiente di test e sviluppo. I responsabili IT possono aggiungere e rimuovere dinamicamente la capacità in base a mutevoli esigenze di test e sviluppo. Possono inoltre dedicare più risorse ad attività a valore aggiunto con i progetti di SharePoint e meno alla gestione dell'infrastruttura.

Per sfruttare appieno i computer dei test di carico, le organizzazioni possono configurare sistemi virtualizzati SharePoint di test e sviluppo in Azure con il supporto del sistema operativo Windows Sever 2008 R2. In questo modo i team di sviluppo possono creare e testare le applicazioni ed eseguire facilmente la migrazione ad ambienti di produzione locali o su cloud senza modifiche al codice. È possibile usare gli stessi framework e set di strumenti in locale e nel cloud, consentendo l'accesso di team distribuiti allo stesso ambiente. Gli utenti possono anche accedere a dati e applicazioni locali stabilendo una connessione VPN diretta.

#### Per iniziare

Nella figura 4 è illustrato un ambiente di test e sviluppo di SharePoint in una macchina virtuale di Azure. Per creare questa distribuzione, sfruttare inizialmente lo stesso ambiente di test e sviluppo di SharePoint usato per sviluppare applicazioni. Quindi, caricare e distribuire le applicazioni nella macchina virtuale di Azure per i test e lo sviluppo. In seguito, sarà possibile spostare di nuovo le applicazioni in locale senza la necessità di modificarle.

Figura 4: ambiente di test e sviluppo di SharePoint in macchine virtuali di Azure

![azure-sharepoint-wp-11][azure-sharepoint-wp-11]

#### Configurazione dell'ambiente per lo scenario

Per implementare un ambiente di test e sviluppo di SharePoint in Azure, eseguire la procedura seguente:

1.  *Eseguire il provisioning*: eseguire innanzitutto il provisioning di una connessione VPN tra l'ambiente locale e Azure tramite Rete virtuale di Azure. Poiché in questo caso non si usa Active Directory, è necessario un tunnel VPN. Per altre informazioni, vedere [Panoramica di Rete virtuale (considerazioni sulla progettazione e scenari di connessioni sicure)][Panoramica di Rete virtuale (considerazioni sulla progettazione e scenari di connessioni sicure)]. Usare quindi il portale di gestione per eseguire il provisioning di una nuova macchina virtuale da un'immagine della raccolta.
    -   È possibile caricare le macchine virtuali di test e sviluppo di SharePoint locali nell'account di archiviazione di Azure e farvi riferimento tramite la raccolta immagini per creare l'ambiente richiesto.
    -   È possibile usare l'immagine di SQL Server 2012 anziché quella di Windows Server 2008 R2 SP1. Per altre informazioni, vedere [Provisioning di una macchina virtuale di SQL Server in Azure][Provisioning di una macchina virtuale di SQL Server in Azure].

2.  *Installare*: installare SharePoint Server, Visual Studio e SQL Server nelle macchine virtuali usando una connessione Desktop remoto.
    -   Scegliere un'opzione per l'installazione di SharePoint Server:
        -   Usare gli strumenti SharePoint 2010 Easy Setup Script per creare un computer SharePoint per lo sviluppo. Per altre informazioni, vedere [SharePoint 2010 Easy Setup Script][SharePoint 2010 Easy Setup Script].
        -   Usare Windows PowerShell. Per altre informazioni, vedere [Installare SharePoint Server 2010 tramite Windows PowerShell][Installare SharePoint Server 2010 tramite Windows PowerShell].
        -   Usare CodePlex Project AutoSPInstaller. Per altre informazioni, vedere [CodePlex: AutoSPInstaller][CodePlex: AutoSPInstaller].
    -   Installare Visual Studio. Per altre informazioni, vedere [Installazione di Visual Studio][Installazione di Visual Studio].
    -   Installare SQL Server. Per altre informazioni, vedere [Installare SQL Server tramite SysPrep][Installare SQL Server 2012 tramite SysPrep].
        -   Fare riferimento al laboratorio interattivo per creare e configurare SQL Server 2012 per la distribuzione di una farm di SharePoint: [Configurazione di SQL Server 2012 per SharePoint in Azure][Configurazione di SQL Server 2012 per SharePoint in Azure].
        -   Fare riferimento al laboratorio interattivo per creare una farm di SharePoint configurando Active Directory e usando un singolo database di SQL Server: [Distribuzione di una farm di SharePoint con Macchine virtuali di Azure][Distribuzione di una farm di SharePoint con Macchine virtuali di Azure].

3.  *Sviluppare pacchetti e script di distribuzione per applicazioni e database*: se si prevede di usare una macchina virtuale disponibile nella raccolta immagini, è possibile distribuire le applicazioni e i database locali desiderati in Macchine virtuali di Azure:
    -   Creare i pacchetti di distribuzione per le applicazioni e i database locali esistenti usando SQL Server Data Tools e Visual Studio.
    -   Usare questi pacchetti per distribuire le applicazioni e i database in Macchine virtuali di Azure.

4.  *Distribuire applicazioni e database di SharePoint*:
    -   Configurare la sicurezza nell'endpoint del portale di gestione per impostare una porta di ingresso in Windows Firewall della macchina virtuale.
    -   Distribuire le applicazioni e i database di SharePoint in Macchine virtuali di Azure usando i pacchetti e gli script di distribuzione creati nel passaggio 3.
    -   Testare le applicazioni e i database distribuiti.

5.  *Gestire le macchine virtuali*:
    -   Monitorare le macchine virtuali tramite il portale di gestione.
    -   Monitorare le applicazioni tramite Visual Studio e SQL Server Management Studio.
    -   È inoltre possibile monitorare e gestire le macchine virtuali usando software di gestione locale, come Microsoft System Center - Operations Manager.

### Scenario 2: farm di SharePoint pubblica con personalizzazione

#### Descrizione

Le organizzazioni desiderano creare una presenza su Internet ospitata nel cloud che sia facilmente scalabile in base alle esigenze e alla domanda. Desiderano inoltre creare siti Web Extranet per la collaborazione con i partner e implementare procedure semplificate e distribuite di creazione e approvazione del relativo contenuto. Infine, per gestire l'aumento dei carichi di lavoro, desiderano fornire capacità su richiesta ai loro siti Web.

In questo scenario si usa SharePoint Server come base per l'hosting di un sito Web pubblico. In questo modo le organizzazioni possono rapidamente distribuire, personalizzare e ospitare i loro siti Web aziendali in un'infrastruttura cloud scalabile e sicura. Con i siti Web pubblici di SharePoint in Azure, le organizzazioni si assicurano la scalabilità per far fronte all'aumento del traffico e si assumono solo i costi delle risorse che usano. È possibile usare strumenti comuni, simili a quelli impiegati in locale, per la creazione di contenuti, il flusso di lavoro e l'approvazione con SharePoint in Azure.

Inoltre, usando Macchine virtuali di Azure, le organizzazioni possono facilmente configurare ambienti di gestione temporanea e di produzione in esecuzione su macchine virtuali. Le macchine virtuali pubbliche di SharePoint create in Azure possono essere sottoposte a backup in uno spazio di archiviazione virtuale. Inoltre, ai fini del ripristino di emergenza, la funzionalità di replica geografica continua consente alle organizzazione di eseguire automaticamente il backup delle macchine virtuali di un data center in un altro data center situato a chilometri di distanza. Per altre informazioni sulla replica geografica, vedere [Introduzione alla replica geografica per Archiviazione di Azure][Introduzione alla replica geografica per Archiviazione di Azure].

Le macchine virtuali dell'infrastruttura Azure sono convalidate e supportate per l'utilizzo con altri prodotti Microsoft, ad esempio SQL Server e SharePoint Server. È consigliabile usare Azure con SharePoint Server, in quanto fanno entrambi parte della famiglia di prodotti Microsoft e sono pienamente integrati, supportati e testati per offrire nell'insieme un'esperienza ottimale. Prevedono entrambi un singolo punto di supporto per l'applicazione SharePoint e l'infrastruttura Azure.

#### Per iniziare

In questo scenario è necessario aggiungere altri server Web front-end per SharePoint in modo da supportare l'aumento del traffico. Questi server richiedono sicurezza avanzata e controller di dominio di Servizi di dominio Active directory per supportare l'autenticazione e l'autorizzazione degli utenti. Nella figura 5 è illustrato il layout di questo scenario.

Figura 5: farm di SharePoint pubblica con personalizzazione

![azure-sharepoint-wp-12][azure-sharepoint-wp-12]

#### Configurazione dell'ambiente per lo scenario

Per implementare una farm di SharePoint pubblica in Azure, eseguire la procedura seguente:

1.  *Distribuire Active Directory*: i requisiti di base per la distribuzione di Active Directory in Macchine virtuali di Azure sono simili, ma non identici, a quelli della distribuzione in macchine virtuali (e, in una certa misura, in computer fisici) in locale. Per altre informazioni sulle differenze, oltre a linee guida e altre considerazioni, vedere [Linee guida per la distribuzione di Active Directory in macchine virtuali di Azure][Linee guida per la distribuzione di Active Directory in macchine virtuali di Azure]. Per distribuire Active Directory in Azure:
    -   Definire e creare una rete virtuale in cui è possibile assegnare le macchine virtuali a specifiche subnet. Per altre informazioni, vedere [Configurare le reti virtuali][Configurare le reti virtuali].
    -   Usare il portale di gestione per creare e distribuire il controller di dominio in una nuova macchina virtuale in Azure. Per altre informazioni, vedere [Distribuzione e creazione del controller di dominio][Configurare le reti virtuali].
        -   È anche possibile fare riferimento allo script di Windows PowerShell per distribuire un dominio autonomo nel cloud usando Macchine virtuali e Rete virtuale di Azure. Per altre informazioni, vedere [Distribuzione di Active Directory in Azure (Windows PowerShell)][Distribuzione di Active Directory in Azure (Windows PowerShell)].
        -   Per altre informazioni sulla creazione di una nuova foresta Active Directory in una macchina virtuale di Rete virtuale di Azure, vedere [Installazione di una nuova foresta Active Directory in Azure][Installazione di una nuova foresta Active Directory in Azure].

2.  *Eseguire il provisioning di una macchina virtuale*: Usare il portale di gestione per eseguire il provisioning di una nuova macchina virtuale da un'immagine della raccolta.
3.  *Distribuire una farm di SharePoint*:
    -   Usare la macchina virtuale di cui è stato eseguito il provisioning per installare SharePoint e generare un'immagine riutilizzabile. Per altre informazioni sull'installazione di SharePoint Server, vedere [Installare SharePoint Server 2010 tramite Windows PowerShell][Installare SharePoint Server 2010 tramite Windows PowerShell] o [CodePlex: AutoSPInstaller][CodePlex: AutoSPInstaller].
    -   Configurare la macchina virtuale di SharePoint e connetterla alla farm di SharePoint.
    -   Usare il portale di gestione per configurare il bilanciamento del carico.
        -   Configurare gli endpoint della macchina virtuale, selezionare l'opzione per il bilanciamento del carico del traffico in un endpoint esistente, quindi specificare il nome della macchina virtuale con carico bilanciato.
        -   Aggiungere un'altra macchina virtuale Web front-end alla farm di SharePoint esistente per il traffico aggiuntivo.

4.  *Gestire le macchine virtuali*:
    -   Monitorare le macchine virtuali tramite il portale di gestione.
    -   Monitorare la farm di SharePoint tramite Amministrazione centrale.

### Scenario 3: farm con scalabilità orizzontale per servizi aggiuntivi di business intelligence

#### Descrizione

Le funzionalità di business intelligence sono essenziali per acquisire informazioni significative e prendere soluzioni rapide e oculate. Con la transizione da un approccio locale, le organizzazioni desiderano distribuire le applicazioni di business intelligence nel cloud senza apportare modifiche all'ambiente esistente. Desiderano ospitare i report di SQL Server Analysis Services (SSAS) o SQL Server Reporting Services (SSRS) in un ambiente ampiamente affidabile e disponibili, mantenendo il massimo controllo dell'applicazione di business intelligence e senza dedicare troppo tempo o risorse di budget alla manutenzione.

In questo scenario viene illustrato come usare Macchine virtuali di Azure per ospitare applicazioni di business intelligence mission-critical. Le organizzazioni possono distribuire farm di SharePoint in Macchine virtuali di Azure e scalare orizzontalmente i componenti di BI della macchina virtuale del server applicazioni, come SSRS o Excel Services. Scalando i componenti a uso intensivo di risorse nel cloud, è possibile supportare in modo più semplice ed efficace i carichi di lavoro speciali. Si noti che SQL Server offre prestazioni elevate in Macchine Virtuali di Azure. Inoltre, le istanze di SQL Server sono facilmente scalabili da installazioni di piccole dimensioni a quelle molto grandi. In questo modo le organizzazioni ottengono la flessibilità per eseguire dinamicamente il provisioning (espansione) o il deprovisioning (riduzione) delle istanze di business intelligence in base ai requisiti immediati del carico di lavoro.

La migrazione delle attuali applicazioni di business intelligence in Azure assicura una maggiore scalabilità. Con le funzionalità avanzate di SSAS, SSRS e SharePoint Server, le organizzazioni possono creare potenti applicazioni e dashboard di BI e reporting ampiamente scalabili in entrambe le direzioni. Tali applicazioni e dashboard possono inoltre essere integrati in modo sicuro con le applicazioni e i dati locali. Azure assicura la conformità del data center con il supporto per lo standard ISO 27001. Per altre informazioni, vedere il [Centro protezione di Azure][Centro protezione di Azure].

#### Per iniziare

Per scalare orizzontalmente la distribuzione dei componenti di business intelligence, è necessario installare un nuovo server applicazioni con servizi come PowerPivot, Power View, Excel Services o PerformancePoint Services. In alternativa, è necessario aggiungere istanze di BI di SQL Server come SSAS o SSRS alla farm esistente per supportare l'elaborazione aggiuntiva di query. Il server può essere aggiunto come nuova macchina virtuale di Azure con SharePoint 2010 Server o SQL Server installato. Sarà quindi possibile installare, distribuire e configurare i componenti di business intelligence in tale server (figura 6).

Figura 6: farm di SharePoint con scalabilità orizzontale per servizi BI aggiuntivi

![azure-sharepoint-wp-13][azure-sharepoint-wp-13]

#### Configurazione dell'ambiente per lo scenario

Per scalare orizzontalmente un ambiente di business intelligence in Azure, eseguire la procedura seguente:

1.  *Eseguire il provisioning*:
    -   Eseguire il provisioning di una connessione VPN tra l'ambiente locale e Azure tramite Rete virtuale di Azure. Per altre informazioni, vedere [Panoramica di Rete virtuale (considerazioni sulla progettazione e scenari di connessioni sicure)][Panoramica di Rete virtuale (considerazioni sulla progettazione e scenari di connessioni sicure)].
    -   Usare il portale di gestione per eseguire il provisioning di una nuova macchina virtuale da un'immagine della raccolta.
        -   È possibile caricare le immagini dei carichi di lavoro BI di SharePoint Server o SQL Server nella raccolta immagini. In seguito, qualsiasi utente autorizzato potrò selezionare queste macchine virtuali di componenti di BI per creare l'ambiente con scalabilità orizzontale.

2.  *Installare*: se l'organizzazione non dispone di immagini predefinite di componenti di BI di SharePoint Server o SQL Server, installare SharePoint Server e SQL Server nelle macchine virtuali usando una connessione Desktop remoto.
    -   Per altre informazioni sull'installazione di SharePoint, vedere [Installare SharePoint Server 2010 tramite Windows PowerShell][Installare SharePoint Server 2010 tramite Windows PowerShell] o [CodePlex: AutoSPInstaller][CodePlex: AutoSPInstaller].
    -   Per altre informazioni sull'installazione di SQL Server, vedere [Installare SQL Server tramite SysPrep][Installare SQL Server 2012 tramite SysPrep].
    -   Fare riferimento al laboratorio interattivo per creare e configurare SQL Server 2012 per la distribuzione di una farm di SharePoint: [Configurazione di SQL Server 2012 per SharePoint in Azure][Configurazione di SQL Server 2012 per SharePoint in Azure].
    -   Fare riferimento al laboratorio interattivo per creare una farm di SharePoint configurando Active Directory e usando un singolo database di SQL Server: [Distribuzione di una farm di SharePoint con Macchine virtuali di Azure][Distribuzione di una farm di SharePoint con Macchine virtuali di Azure].

3.  *Aggiungere la macchina virtuale BI*:
    -   Configurare la sicurezza nell'endpoint del portale di gestione per impostare una porta di ingresso in Windows Firewall della macchina virtuale.
    -   Aggiungere la macchina virtuale BI creata alla farm di SharePoint o SQL Server esistente.

4.  *Gestire le macchine virtuali*:
    -   Monitorare le macchine virtuali tramite il portale di gestione.
    -   Monitorare la farm di SharePoint tramite Amministrazione centrale.
    -   Monitorare e gestire le macchine virtuali usando software di gestione locale come Microsoft System Center - Operations Manager.

### Scenario 4: sito Web basato su SharePoint completamente personalizzato

#### Descrizione

Le organizzazioni desiderano sempre più spesso creare siti Web di SharePoint completamente personalizzati nel cloud. Avvertono l'esigenza di un ambiente ampiamente affidabile e disponibile che offra il controllo completo per mantenere complesse applicazioni in esecuzione sul cloud, ma senza dedicare grandi quantità di tempo e budget.

In questo scenario le organizzazioni possono distribuire l'intera farm di SharePoint nel cloud e scalare dinamicamente tutti i componenti per ottenere capacità aggiuntiva. In alternativa, possono estendere la distribuzione locale nel cloud per incrementare la capacità e migliorare le prestazioni quando è necessario. Questo scenario è destinato alle organizzazioni che desiderano un'esperienza completa con SharePoint per lo sviluppo di applicazioni e la gestione di contenuti aziendali. I siti più complessi possono anche includere funzionalità avanzate di reporting, Power View, PerformancePoint, PowerPivot, grafici approfonditi e la maggior parte delle altre funzionalità dei siti di SharePoint per la piena funzionalità end-to-end.

Le organizzazioni possono usare Macchine virtuali di Azure per ospitare applicazioni personalizzate e i componenti associati in un'infrastruttura cloud ampiamente sicura e a costi contenuti. Possono inoltre usare la versione locale di Microsoft System Center come strumento di gestione comune per le applicazioni locali e su cloud.

#### Per iniziare

Per implementare un sito Web di SharePoint completamente personalizzato in Azure, è necessario distribuire un dominio Active Directory nel cloud ed eseguirvi il provisioning di nuove macchine virtuali. È quindi necessario creare e configurare una macchina virtuale che esegue SQL Server 2012 come parte di una farm di SharePoint. Infine, è necessario creare la farm di SharePoint con carico bilanciato e connetterla ad Active Directory e SQL Server (figura 7).

Figura 7: sito Web basato su SharePoint completamente personalizzato

![azure-sharepoint-wp-14][azure-sharepoint-wp-14]

#### Configurazione dell'ambiente per lo scenario

Nella procedura seguente viene illustrato come creare un ambiente farm di SharePoint personalizzato dalle immagini predefinite disponibili nella raccolta immagini. Si noti, tuttavia, che è anche possibile caricare le macchine virtuali della farm di SharePoint nella raccolta immagini. In seguito, gli utenti autorizzati potranno scegliere queste macchine virtuali per creare la necessaria farm di SharePoint in Azure.

1.  Distribuire Active Directory*: i requisiti di base per la distribuzione di Active Directory in Macchine virtuali di Azure sono simili, ma non identici, a quelli della distribuzione in macchine virtuali (e, in una certa misura, in computer fisici) in locale. Per altre informazioni sulle differenze, oltre a linee guida e altre considerazioni, vedere [Linee guida per la distribuzione di Active Directory in macchine virtuali di Azure][Linee guida per la distribuzione di Active Directory in macchine virtuali di Azure]. Per distribuire Active Directory in Azure:*
    -   Definire e creare una rete virtuale in cui è possibile assegnare le macchine virtuali a specifiche subnet. Per altre informazioni, vedere [Configurare le reti virtuali][Configurare le reti virtuali].
    -   Usare il portale di gestione per creare e distribuire il controller di dominio in una nuova macchina virtuale in Azure. Per altre informazioni, vedere [Distribuzione e creazione del controller di dominio][Configurare le reti virtuali].
        -   È anche possibile fare riferimento allo script di Windows PowerShell per distribuire un dominio autonomo nel cloud usando Macchine virtuali e Rete virtuale di Azure. Per altre informazioni, vedere [Distribuzione di Active Directory in Azure (Windows PowerShell)][Distribuzione di Active Directory in Azure (Windows PowerShell)].
        -   Per altre informazioni sulla creazione di una nuova foresta Active Directory in una macchina virtuale di Rete virtuale di Azure, vedere [Installazione di una nuova foresta Active Directory in Azure][Installazione di una nuova foresta Active Directory in Azure].

2.  *Distribuire SQL Server*:
    -   Usare il portale di gestione per eseguire il provisioning di una nuova macchina virtuale da un'immagine della raccolta.
    -   Configurare SQL Server nella macchina virtuale. Per altre informazioni, vedere [Installare SQL Server tramite SysPrep][Installare SQL Server 2012 tramite SysPrep].
    -   Aggiungere la macchina virtuale al dominio Active Directory appena creato.

3.  *Distribuire una farm di SharePoint multiserver*:
    -   Creare una rete virtuale. Per altre informazioni, vedere [Panoramica di Rete virtuale (considerazioni sulla progettazione e scenari di connessioni sicure)][Panoramica di Rete virtuale (considerazioni sulla progettazione e scenari di connessioni sicure)].
        -   Quando si distribuiscono macchine virtuali di SharePoint, è necessario che vengano fornite subnet per SharePoint Server in modo che durante il provisioning siano disponibili gli indirizzi DNS dell'implementazione di Active Directory locale.
    -   Usare il portale di gestione per creare una macchina virtuale.
    -   Installare SharePoint Server in questa macchina virtuale e generare un'immagine riutilizzabile. Per altre informazioni sull'installazione di SharePoint Server, vedere [Installare SharePoint Server 2010 tramite Windows PowerShell][Installare SharePoint Server 2010 tramite Windows PowerShell] o [CodePlex: AutoSPInstaller][CodePlex: AutoSPInstaller].
    -   Configurare la macchina virtuale di SharePoint in modo da creare e connettersi alla farm di SharePoint usando il comando [Join-SharePointFarm][Join-SharePointFarm].
    -   Usare il portale di gestione per configurare il bilanciamento del carico:
        -   Configurare gli endpoint della macchina virtuale, selezionare l'opzione per il bilanciamento del carico del traffico in un endpoint esistente, quindi specificare il nome della macchina virtuale con carico bilanciato.
            -   Per altre informazioni sulla distribuzione di farm di SharePoint in Macchine virtuali di Azure, guardare il video [TechEd North America 2012][TechEd North America 2012].

4.  *Gestire la farm di SharePoint tramite System Center*:
    -   Usare l'agente Operations Manager e il nuovo Azure Integration Pack per connettere la versione locale di System Center a Macchine virtuali di Azure.
    -   Usare le versioni locali di App Controller e Orchestrator per le funzioni di gestione.

## Conclusioni

Il cloud computing implica una profonda trasformazione dell'IT nelle organizzazioni. Consente infatti di sfruttare una nuova categoria di vantaggi, con un marcata riduzione dei costi a fronte di livelli superiori di centralità, agilità e flessibilità dell'IT. Azure si propone come soluzione all'avanguardia nel cloud computing, offrendo un'infrastruttura virtuale semplice, aperta, flessibile e potente. Macchine virtuali di Azure riduce la necessità di acquisire hardware, consentendo alle organizzazioni di ridurre i costi e la complessità tramite un'infrastruttura dimensionata in base a specifiche esigenze, con il massimo controllo e una gestione semplificata.

Macchine virtuali di Azure offre la piena continuità delle distribuzioni di SharePoint. Si tratta di una soluzione completamente supportata e testata per assicurare un'esperienza ottimale con altre applicazioni Microsoft. Di conseguenza, le organizzazioni possono facilmente configurare e distribuire SQL Server all'interno di Azure, per eseguire il provisioning dell'infrastruttura per una nuova distribuzione di SharePoint oppure per espanderne una esistente. Con l'aumento dei carichi di lavoro aziendali, le organizzazioni possono rapidamente espandere l'infrastruttura SharePoint. Analogamente, se le esigenze dei carichi di lavoro diminuiscono, possono acquisire risorse su richiesta, assumendosi solo i costi di quelle che usano. Macchine virtuali di Azure offre un'eccellente infrastruttura per un'ampia varietà di requisiti aziendali, come dimostrato nei quattro scenari basati su SharePoint presentati in questo documento.

Per una corretta distribuzione di SharePoint Server in Macchine virtuali di Azure, è necessaria un'attenta pianificazione, soprattutto considerando la gamma di opzioni disponibili per l'architettura e la distribuzione di farm strategiche. Le informazioni e le procedure dettagliate descritte in questo documento possono risultare utili per prendere decisioni oculate nell'implementazione di una distribuzione di SharePoint.

## Risorse aggiuntive

-   Kit di formazione di Azure: Laboratorio interattivo e presentazione

    <http://windowsazure-trainingkit.github.com/labs.htm>

-   Guida introduttiva ad Azure PowerShell

    <http://msdn.microsoft.com/it-it/library/windowsazure/jj156055>

-   Cmdlet di gestione di Azure

    <http://msdn.microsoft.com/it-it/library/windowsazure/jj152841>

-   Strumenti da riga di comando e cmdlet di PowerShell per diversi sistemi operativi

    [https://www.windowsazure.com/it-it/manage/downloads/][https://www.windowsazure.com/it-it/manage/downloads/]

-   Guida alle procedure e documentazione sulle procedure consigliate

    [https://www.windowsazure.com/it-it/manage/windows/][https://www.windowsazure.com/it-it/manage/windows/]

  [azure-sharepoint-wp-1]: ./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-1.png
  [Panoramica dei prezzi di Azure]: /it-it/pricing/details/
  [Creazione di una macchina virtuale che esegue Windows Server 2008 R2]: /it-it/manage/windows/tutorials/virtual-machine-from-gallery/
  [azure-sharepoint-wp-2]: ./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-2.png
  [Creazione e caricamento di un disco rigido virtuale]: /it-it/manage/windows/common-tasks/upload-a-vhd/
  [azure-sharepoint-wp-3]: ./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-3.png
  [portale di gestione di Azure]: http://manage.windowsazure.com/
  [iscriversi per una versione di valutazione gratuita]: http://www.windowsazure.com/it-it/pricing/free-trial/
  [Creare un account di archiviazione in Azure]: /it-it/manage/windows/common-tasks/upload-a-vhd/#createstorage
  [Provisioning di una macchina virtuale di SQL Server in Azure]: /it-it/manage/windows/common-tasks/install-sql-server/
  [Installare SQL Server 2012 tramite SysPrep]: http://msdn.microsoft.com/it-it/library/ee210664.aspx
  [Installare SQL Server 2012 dal prompt dei comandi]: http://msdn.microsoft.com/it-it/library/ms144259.aspx#SysPrep
  [Creare una farm di Microsoft SharePoint Server]: http://technet.microsoft.com/it-it/library/ee805948.aspx#CreateConfigure
  [Aggiungere server Web o server applicazioni a farm in SharePoint 2013]: http://technet.microsoft.com/it-it/library/cc261752.aspx
  [Aggiungere un server di database a una farm esistente in SharePoint 2013]: http://technet.microsoft.com/it-it/library/cc262781
  [Service Pack 1 per Microsoft SharePoint Server 2010]: http://www.microsoft.com/it-it/download/details.aspx?id=26623
  [Installare le funzionalità di business intelligence di SQL Server 2012]: http://technet.microsoft.com/it-it/library/hh231681(v=sql.110).aspx
  [Informazioni di riferimento sullo strumento da riga di comando Psconfig]: http://technet.microsoft.com/it-it/library/cc263093.aspx
  [Installare SharePoint Server 2010 tramite Windows PowerShell]: http://technet.microsoft.com/it-it/library/cc262839.aspx
  [CodePlex: AutoSPInstaller]: http://autospinstaller.codeplex.com/
  [Creare dischi rigidi virtuali]: http://technet.microsoft.com/it-it/library/cc742509
  [Introduzione all'utilizzo di SysPrep]: http://technet.microsoft.com/it-it/library/bb457073.aspx
  [azure-sharepoint-wp-11]: ./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-11.png
  [Panoramica di Rete virtuale (considerazioni sulla progettazione e scenari di connessioni sicure)]: http://msdn.microsoft.com/it-it/library/windowsazure/jj156007.aspx
  [SharePoint 2010 Easy Setup Script]: http://www.microsoft.com/it-it/download/details.aspx?id=23415
  [Installazione di Visual Studio]: http://msdn.microsoft.com/it-it/library/e2h7fzkw.aspx
  [Configurazione di SQL Server 2012 per SharePoint in Azure]: https://github.com/WindowsAzure-TrainingKit/HOL-DeployingSQLServerForSharePoint
  [Distribuzione di una farm di SharePoint con Macchine virtuali di Azure]: https://github.com/WindowsAzure-TrainingKit/HOL-DeploySharePointVMs
  [Introduzione alla replica geografica per Archiviazione di Azure]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/introducing-geo-replication-for-windows-azure-storage.aspx
  [azure-sharepoint-wp-12]: ./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-12.png
  [Linee guida per la distribuzione di Active Directory in macchine virtuali di Azure]: http://msdn.microsoft.com/it-it/library/windowsazure/jj156090
  [Configurare le reti virtuali]: https://github.com/WindowsAzure-TrainingKit/HOL-DeployingActiveDirectory/blob/master/HOL.md
  [Distribuzione di Active Directory in Azure (Windows PowerShell)]: https://github.com/WindowsAzure-TrainingKit/HOL-DeployingActiveDirectoryPS
  [Installazione di una nuova foresta Active Directory in Azure]: /it-it/manage/services/networking/active-directory-forest/
  [Centro protezione di Azure]: /it-it/support/trust-center/compliance/
  [azure-sharepoint-wp-13]: ./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-13.png
  [azure-sharepoint-wp-14]: ./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-14.png
  [Join-SharePointFarm]: http://technet.microsoft.com/it-it/library/ff607979.aspx
  [TechEd North America 2012]: http://channel9.msdn.com/Events/TechEd/NorthAmerica/2012/AZR327
  [https://www.windowsazure.com/it-it/manage/downloads/]: /it-it/manage/downloads/
  [https://www.windowsazure.com/it-it/manage/windows/]: /it-it/manage/windows/
