<properties
	pageTitle="Linee guida sull'implementazione dei servizi di infrastruttura di Azure"
	description="Informazioni sulle linee guida di progettazione e implementazione fondamentali per la distribuzione di un carico di lavoro IT nei servizi di infrastruttura di Azure."
	documentationCenter=""
	services="virtual-machines"
	authors="squillace"
	manager="timlt"
	editor=""
	tags="azure-service-management,azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/21/2015"
	ms.author="rasquill"/>

# Linee guida sull'implementazione dei servizi di infrastruttura di Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Azure è una piattaforma eccellente per implementare le configurazioni di sviluppo/testing o di modello di prova, poiché richiede un investimento minimo per eseguire il test di un approccio specifico all'implementazione di soluzioni. Tuttavia, è necessario essere in grado di distinguere le procedure semplici per un ambiente di sviluppo/testing da quelle più complesse e dettagliate per un'implementazione di un carico di lavoro IT completamente funzionale e per ambienti di produzione.

Questa guida identifica molte aree per le quali la pianificazione è fondamentale per il successo di un carico di lavoro IT in Azure. La pianificazione fornisce inoltre un ordine per la creazione delle risorse necessarie. Sebbene sia possibile una certa flessibilità, è consigliabile applicare alla pianificazione e al processo decisionale l'ordine indicato in questo articolo.

Questo articolo è stato adattato sulla base del contenuto del post di blog relativo alle [linee guida sull'implementazione di Azure](http://blogs.msdn.com/b/thecolorofazure/archive/2014/05/13/azure-implementation-guidelines.aspx). Un ringraziamento a Santiago Cánepa (Application Development Manager per Microsoft) e Hugo Salcedo (Application Development Manager per Microsoft) per il materiale originale.

> [AZURE.NOTE]I gruppi di affinità sono stati deprecati e il loro uso non è illustrato in questo articolo. Per altre informazioni, vedere [Informazioni sulle reti virtuali di area e sui gruppi di affinità](../virtual-network/virtual-networks-migrate-to-regional-vnet.md).

## 1\. Convenzioni di denominazione

Prima di creare qualsiasi elemento in Azure, è necessaria una buona convenzione di denominazione Una convenzione di denominazione garantisce la possibilità che tutte le risorse dispongano di un nome stimabile, in modo da ridurre il carico amministrativo associato alla gestione di tali risorse.

È possibile scegliere di seguire un set specifico di convenzioni di denominazione definite per l'intera organizzazione oppure per un account o una sottoscrizione di Azure specifica. Sebbene sia facile per i singoli utenti all'interno delle organizzazioni stabilire le regole implicite quando si usano risorse di Azure, quando un team deve lavorare a un progetto in Azure, la scalabilità di tale modello non è ottimale.

Il set di convenzioni di denominazione deve essere concordato in anticipo. Alcune considerazioni relative alle convenzioni di denominazione trascendono i set di regole che compongono queste convenzioni.

### Affissi

Quando si creano determinate risorse, Azure userà alcune impostazioni predefinite per semplificare la gestione delle risorse associate a queste risorse. Ad esempio, quando si crea la prima macchina virtuale per un nuovo servizio cloud, il portale di Azure tenterà di usare il nome della macchina virtuale come nome di un nuovo servizio cloud per la macchina virtuale.

È quindi utile identificare i tipi di risorse che richiedono un affisso per l’identificazione del tipo. Inoltre, specificare chiaramente la posizione dell’affisso:

- Inizio del nome (prefisso)
- Fine del nome (suffisso)

Ad esempio, di seguito sono indicati due nomi possibili per un gruppo di risorse che ospita un motore di calcolo:

- Rg-CalculationEngine (prefisso)
- CalculationEngine-Rg (suffisso)

Gli affissi possono fare riferimento ai diversi aspetti che descrivono le specifiche risorse. La tabella seguente illustra alcuni esempi usati comunemente.

Aspetto | esempi | Note
--- | --- | ---
Environment | dev, stg, prod | A seconda dello scopo e del nome di ogni ambiente.
Location | usw (Stati Uniti occidentali), use (Stati Uniti orientali 2) | A seconda dell'area del data center o dell’area dell'organizzazione.
Componente di Azure, servizio o prodotto | Rg per gruppo di risorse, Svc per servizio cloud, VNet per rete virtuale | A seconda del prodotto per il quale la risorsa fornisce il supporto.
Ruolo | sql, ora, sp, iis | A seconda del ruolo della macchina virtuale.
Istanza | 01, 02 e 03 e così via. | Per le risorse con più di un'istanza. Ad esempio, i server Web con carico bilanciato in un servizio cloud.

Quando si stabiliscono le convenzioni di denominazione, assicurarsi che siano indicati chiaramente quali affissi usare per ogni tipo di risorsa, e in quale posizione (prefisso o suffisso).

### Date

Molte volte è importante determinare la data di creazione dal nome di una risorsa. È consigliabile usare il formato di data AAAAMMGG. Tale formato garantisce non solo che venga registrata la data completa, ma anche che due risorse i cui nomi si differenziano solo per la data verranno ordinate alfabeticamente e allo stesso tempo in ordine cronologico.

### Nomi delle risorse

È consigliabile definire ogni tipo di risorsa nella convenzione di denominazione, quindi è necessario avere regole che definiscono come assegnare nomi a ogni risorsa creata. Tali regole devono essere applicate a tutti i tipi di risorse, ad esempio:

- Sottoscrizioni
- Account
- Account di archiviazione
- Reti virtuali
- Subnet
- Set di disponibilità
- Gruppi di risorse
- Servizi cloud
- Macchine virtuali
- Endpoint
- Gruppi di sicurezza di rete
- Ruoli

Per assicurarsi che il nome fornisca informazioni sufficienti per determinare a quali risorse fa riferimento, è necessario inserire nomi descrittivi.

### Nomi dei computer

Quando gli amministratori creano una macchina virtuale, Microsoft Azure richiederà di specificare un nome macchina virtuale con lunghezza massima di 15 caratteri. Azure usa il nome della macchina virtuale come nome della risorsa macchina virtuale di Azure. Azure usa lo stesso nome come nome computer per il sistema operativo installato nella macchina virtuale. È possibile tuttavia che questi nomi non siano sempre gli stessi.

Nei casi in cui viene creata una macchina virtuale da un file immagine con estensione vhd che già contiene un sistema operativo, il nome della macchina virtuale in Azure può essere diverso dal nome computer del sistema operativo della macchina virtuale. Questa situazione può aggiungere un livello di difficoltà alla gestione delle macchine virtuali, quindi è sconsigliata. Assicurarsi sempre che il nome della risorsa macchina virtuale di Azure sia lo stesso nome del computer assegnato al sistema operativo di tale macchina virtuale.

È consigliabile che il nome della macchina virtuale di Azure sia identico al nome di computer del sistema operativo. Seguire quindi le regole di denominazione NetBIOS, come descritto in [Convenzioni di denominazione di computer NetBIOS Microsoft](https://support.microsoft.com/kb/188997/).

### Nomi account di archiviazione

Gli account di archiviazione hanno regole speciali che ne controllano i nomi. È possibile usare solo lettere minuscole e numeri. Per altre informazioni, vedere l'articolo relativo alla [Creazione di un account di archiviazione](../storage/storage-create-storage-account.md#create-a-storage-account). Inoltre, il nome dell’account di archiviazione, in combinazione con core.windows.net, deve essere un nome DNS a livello globale valido e univoco. Ad esempio, se il nome dell'account di archiviazione è mystorageaccount, i seguenti nomi DNS risultanti devono essere univoci:

- mystorageaccount.blob.core.windows.net
- mystorageaccount.table.core.windows.net
- mystorageaccount.queue.core.windows.net


### Nomi dei blocchi predefiniti di Azure

I blocchi predefiniti di Azure sono servizi a livello di applicazione offerti da Azure in genere alle applicazioni che sfruttano le funzionalità PaaS, sebbene possano essere usati dalle risorse IaaS, come il database SQL di Azure, Gestione traffico e altre risorse.

Questi servizi si basano su una matrice di elementi che vengono creati e registrati in Azure. Anche questi dovranno essere considerati nelle convenzioni di denominazione.

### Riepilogo delle linee guida di implementazione per le convenzioni di denominazione

Decisione:

- Quali sono le convenzioni di denominazione per le risorse di Azure?

Attività:

- Definire le convenzioni di denominazione in termini di affissi, gerarchia, valori di stringa e altri criteri per le risorse di Azure.

## 2\. Sottoscrizioni e account

Per lavorare con Azure, sono necessarie una o più sottoscrizioni ad Azure. Le risorse, ad esempio i servizi cloud o le macchine virtuali, esistono nel contesto di tali sottoscrizioni.

- I clienti aziendali dispongono in genere di una registrazione Enterprise, che è la risorsa più in alto nella gerarchia ed è associata a uno o più account.
- Per utenti e clienti senza una registrazione Enterprise, la risorsa di livello superiore è l'Account.
- Le sottoscrizioni sono associate agli account ed è possibile associare una o più sottoscrizioni a ogni account. In Azure le informazioni relative alla fatturazione vengono registrate a livello di sottoscrizione.

A causa del limite di due livelli della gerarchia nella relazione di account/sottoscrizione è importante allineare la convenzione di denominazione degli account e delle sottoscrizioni alle esigenze di fatturazione. Se ad esempio un'azienda globale usa Azure, può scegliere di disporre di un account per ogni area e fare in modo che le sottoscrizioni vengano gestite a livello di area.

![](./media/virtual-machines-infrastructure-services-implementation-guidelines/sub01.png)

Ad esempio, è possibile usare questa struttura.

![](./media/virtual-machines-infrastructure-services-implementation-guidelines/sub02.png)

Seguendo lo stesso esempio, se in un'area si decide di associare più sottoscrizioni a un determinato gruppo, la convenzione di denominazione deve incorporare un modo per codificare la parte aggiuntiva sia nel nome account che nel nome della sottoscrizione. Questa organizzazione consente di elaborare i dati di fatturazione per generare nuovi livelli di gerarchia durante la fatturazione dei report.

![](./media/virtual-machines-infrastructure-services-implementation-guidelines/sub03.png)

L'organizzazione potrebbe avere un aspetto simile al seguente.

![](./media/virtual-machines-infrastructure-services-implementation-guidelines/sub04.png)

Microsoft fornisce la fatturazione dettagliata tramite un file scaricabile, per un singolo account o per tutti gli account di un contratto Enterprise. È possibile elaborare questo file, ad esempio, tramite Microsoft Excel. Questo processo potrebbe inserire i dati, partizionare le risorse che codificano più livelli della gerarchia in colonne separate e usare una tabella pivot o PowerPivot per fornire funzionalità di creazione di report dinamici.

### Riepilogo delle linee guida dell’implementazione per sottoscrizioni e account

Decisione:

- Quali sono i set di sottoscrizioni e account necessari per l’hosting dell’infrastruttura o del carico di lavoro IT?

Attività:

- Creare il set di sottoscrizioni e account usando la convenzione di denominazione scelta.

## 3\. Archiviazione

Archiviazione di Azure è parte integrante di molte soluzioni di Azure. Archiviazione di Azure fornisce servizi per l'archiviazione dei dati dei file, i dati non strutturati e i messaggi ed è anche parte dell'infrastruttura di supporto alle macchine virtuali.

In Azure sono disponibili due tipi di archiviazione: Un account di archiviazione standard consente di accedere all'archiviazione BLOB (usata per archiviare dischi delle macchine virtuali di Azure), all'archiviazione tabelle, all'archiviazione coda e all'archiviazione file. L'archiviazione Premium è progettata per le applicazioni a prestazioni elevate, ad esempio SQL Server in un cluster AlwaysOn, e attualmente supporta solo i dischi di macchine virtuali di Azure.

Gli account di archiviazione sono associati a obiettivi di scalabilità. Vedere [Limiti, quote e vincoli delle sottoscrizioni e dei servizi di Microsoft Azure](../azure-subscription-service-limits.md#storage-limits) per acquisire familiarità con i limiti di archiviazione correnti di Azure. Vedere anche [Obiettivi di scalabilità e prestazioni per Archiviazione di Azure](../storage-scalability-targets.md).

Azure consente di creare macchine virtuali con un disco del sistema operativo, un disco temporaneo e zero o più dischi dati facoltativi. Il disco del sistema operativo e i dischi dati sono BLOB di Azure, mentre il disco temporaneo è archiviato localmente sul nodo in cui si trova il computer. Ciò rende il disco temporaneo non idoneo per i dati che devono essere salvati in modo permanente, poiché il computer potrebbe essere sottoposto a migrazione automatica da un nodo all'altro, perdendo tutti i dati in tale disco. Non archiviare alcun elemento nell’unità temporanea.

I dischi del sistema operativo e i dischi dati hanno una dimensione massima di 1023 gigabyte (GB), poiché la dimensione massima di un BLOB è 1024 GB e deve contenere i metadati (piè di pagina) del file del disco rigido virtuale (un GB è composto da 1024<sup>3</sup> byte). Per superare questo limite, è possibile implementare lo striping del disco in Windows.

### Dischi con striping
Oltre a fornire la capacità per creare dischi di dimensioni superiori a 1023 GB, in molti casi, usare lo striping per i dischi dati contribuisce a migliorare le prestazioni, consentendo a più BLOB di supportare l'archiviazione per un singolo volume. Con lo striping, le operazioni I/O necessarie per scrivere e leggere dati da un singolo disco logico procedono in parallelo.

In Azure sono presenti dei limiti sulla quantità di dischi dati e di larghezza di banda disponibile, a seconda delle dimensioni della macchina virtuale. Per informazioni dettagliate, vedere [Dimensioni delle macchine virtuali](virtual-machines-size-specs.md).

Se si usa lo striping del disco per i dischi dati di Azure, considerare le linee guida seguenti:

- I dischi dati devono essere sempre della massima dimensione (1023 GB)
- Connettere i dischi dati massimi consentiti per la dimensione della macchina virtuale
- Usare la configurazione degli spazi di archiviazione
- Usare la configurazione dello striping dell’archiviazione
- Evitare di usare le opzioni di memorizzazione nella cache del disco di dati di Azure (criterio di memorizzazione nella cache = Nessuno)

Per altre informazioni, vedere l’articolo sugli [spazi di archiviazione - progettazione della prestazione](http://social.technet.microsoft.com/wiki/contents/articles/15200.storage-spaces-designing-for-performance.aspx).

### Account di archiviazione multipli

L'utilizzo di più account di archiviazione per supportare i dischi associati a un gran numero di macchine virtuali garantisce che l’I/O aggregato di tali dischi sia ben al di sotto degli obiettivi di scalabilità per ognuno di tali account di archiviazione.

È consigliabile iniziare con la distribuzione di una sola macchina virtuale per account di archiviazione.

### Progettazione del layout di archiviazione

Per implementare queste strategie di implementazione del sottosistema del disco delle macchine virtuali con buone prestazioni, un'infrastruttura o un carico di lavoro IT in genere usufruirà di molti di account di archiviazione. Tali account ospiteranno un gran numero di BLOB VHD. In alcuni casi, a un singolo volume in una macchina virtuale sono associati più BLOB.

Questa situazione può rendere più complesse le attività di gestione. La progettazione di una strategia di archiviazione affidabile, che includa la denominazione appropriata per i dischi sottostanti e i BLOB VHD associati è fondamentale.

### Riepilogo delle linee guida dell’implementazione per l’archiviazione

Decisioni:

- È necessario lo striping del disco per creare dischi più grandi di 500 terabyte (TB)?
- È necessario lo striping del disco per ottenere prestazioni ottimali per il carico di lavoro?
- Quali sono i set di account di archiviazione necessari per l’hosting dell’infrastruttura o del carico di lavoro IT?

Attività:

- Creare il set di account di archiviazione usando la convenzione di denominazione scelta. È possibile usare il portale di anteprima di Azure, il portale di Azure o il cmdlet PowerShell **New-AzureStorageAccount**.

## 4\. Servizi cloud

I servizi cloud sono un blocco predefinito fondamentale in Gestione servizi di Azure, sia per i servizi PaaS sia per quelli IaaS. Per il modello PaaS, i servizi cloud rappresentano un'associazione di ruoli, le cui istanze possono comunicare tra loro. I servizi cloud sono associati a un indirizzo VIP e a un servizio di bilanciamento del carico, che accetta il traffico in ingresso da Internet e ne bilancia il carico ai ruoli configurati per la ricezione di tale traffico.

Nel caso di IaaS, i servizi cloud offrono una funzionalità simile, sebbene nella maggior parte dei casi, la funzionalità di bilanciamento del carico sia usata per inoltrare il traffico a porte TCP o UDP specifiche da Internet alle numerose macchine virtuali all'interno di tale servizio cloud.

> [AZURE.NOTE]I servizi cloud non sono disponibili in Gestione risorse di Azure. Per un'introduzione ai vantaggi di Gestione risorse, vedere [Provider di calcolo di Azure, rete e archiviazione in Gestione risorse di Azure](../articles/virtual-machines/virtual-machines-azurerm-versus-azuresm.md).

I nomi dei servizi cloud sono particolarmente importanti in IaaS, dal momento che Azure li usa come parte della convenzione di denominazione per i dischi. Il nome del servizio cloud può contenere solo lettere, numeri e trattini. Il primo e l'ultimo carattere nel campo devono essere una lettera o un numero.

Azure espone i nomi dei servizi cloud, poiché sono associati all'indirizzo VIP, nel dominio "cloudapp.net". Per migliorare l'esperienza utente relativamente all’applicazione, è necessario configurare un nome personalizzato per sostituire il nome del servizio cloud completo. Tale operazione in genere viene eseguita con un record CNAME nel DNS pubblico che associa il nome DNS pubblico della risorsa, ad esempio www.contoso.com, al nome DNS del servizio cloud che funziona da host per la risorsa (ad esempio, il servizio cloud che funziona da host per i server Web per www.contoso.com).

Inoltre, potrebbe essere necessario che la convenzione di denominazione usata per i servizi cloud tolleri le eccezioni, perché i nomi dei servizi cloud devono essere univoci tra tutti gli altri servizi cloud di Microsoft Azure, indipendentemente dal tenant di Microsoft Azure.

Una limitazione importante dei servizi cloud da prendere in considerazione è che può essere eseguita solo un'operazione di gestione della macchina virtuale alla volta per tutte le macchine virtuali nel servizio cloud. Quando viene eseguita un'operazione di gestione della macchina virtuale su una macchina virtuale nel servizio cloud, è necessario attendere fino al completamento prima di poter eseguire una nuova operazione di gestione su un'altra macchina virtuale. È quindi consigliabile che il numero di macchine virtuali in un servizio cloud sia ridotto.

Le sottoscrizioni di Azure possono supportare un massimo di 200 servizi cloud.

### Riepilogo delle linee guida dell’implementazione per i servizi cloud

Decisione:

- Quali sono i set di servizi cloud necessari per l’hosting dell’infrastruttura o del carico di lavoro IT?

Attività:

- Creare il set di servizi cloud usando la convenzione di denominazione scelta. È possibile usare il portale di Azure o il cmdlet di PowerShell **New-AzureService**.

## 5\. Reti virtuali

Il passaggio logico successivo consiste nel creare le reti virtuali necessarie per supportare le comunicazioni tra le macchine virtuali nella soluzione. Sebbene sia possibile eseguire l'hosting di più macchine virtuali di un carico di lavoro IT all'interno di un solo servizio cloud, è consigliabile usare le reti virtuali.

Le reti virtuali sono un contenitore per le macchine virtuali per le quali è anche possibile specificare subnet, indirizzamento personalizzato e opzioni di configurazione DNS. Le macchine virtuali all'interno della stessa rete virtuale possono comunicare direttamente con altri computer nella stessa rete virtuale, indipendentemente dal servizio cloud a cui appartengono. All'interno della rete virtuale, questa comunicazione rimane privata, senza la necessità di passare tramite endpoint pubblici. La comunicazione può avere luogo tramite indirizzo IP o mediante il nome, usando un server DNS installato nella rete virtuale o in locale, se la macchina virtuale è connessa alla rete aziendale.

### Connettività del sito
Se utenti e computer locali non richiedono connettività costante alle macchine virtuali in una rete virtuale Azure, creare una rete virtuale solo cloud.

![](./media/virtual-machines-infrastructure-services-implementation-guidelines/vnet01.png)

Si tratta di una rete indicata in genere per carichi di lavoro con connessione Internet, ad esempio un server Web basato su Internet. È possibile gestire queste macchine virtuali usando connessioni Desktop remoto, sessioni di PowerShell remote, connessioni Secure Shell (SSH) e connessioni VPN point-to-site.

Poiché non si connettono alla rete locale, le reti virtuali solo cloud possono usare qualsiasi parte dello spazio di indirizzi IP privato.

Se computer e utenti locali richiedono la connettività costante alle macchine virtuali in una rete virtuale di Azure, creare una rete virtuale cross-premise e connetterla alla rete locale con un ExpressRoute o una connessione VPN da sito a sito.

![](./media/virtual-machines-infrastructure-services-implementation-guidelines/vnet02.png)

In questa configurazione, la rete virtuale di Azure è essenzialmente un'estensione della rete locale basata su cloud.

Poiché si connettono alla rete locale, le reti virtuali cross-premise devono usare una parte dello spazio di indirizzi usato dall’organizzazione che sia univoco e l'infrastruttura di routing deve supportare il traffico di routing a tale porzione inoltrandolo al dispositivo VPN locale.

Per consentire ai pacchetti di spostarsi dalla rete virtuale cross-premise alla rete locale, è necessario configurare il set di prefissi di indirizzo locali pertinenti come parte della definizione della rete locale per la rete virtuale. A seconda lo spazio di indirizzi della rete virtuale e del set di percorsi locali pertinenti, è possibile che nella rete locale siano presenti molti prefissi di indirizzo.

È possibile convertire una rete virtuale solo cloud in una rete virtuale cross-premise. Tuttavia, probabilmente sarà necessario rinumerare lo spazio di indirizzi della rete virtuale, le subnet e le macchine virtuali che usano indirizzi IP statici assegnati da Azure, noti come IP dinamici (DIP). Valutare quindi attentamente il tipo di reti virtuali necessario (solo cloud o locali) prima di crearle.

### Subnet
Le subnet consentono di organizzare le risorse correlate, logicamente (ad esempio, una subnet per le macchine virtuali associate alla stessa applicazione) o fisicamente (ad esempio, una subnet per il servizio cloud) oppure di ricorrere a tecniche di isolamento subnet per una maggiore sicurezza.

Per le reti virtuali cross-premise, è necessario progettare delle subnet con le stesse convenzioni utilizzate per le risorse locali, tenendo presente che **Azure utilizza sempre i primi tre indirizzi IP dello spazio di indirizzi per ciascuna subnet**. Per determinare il numero di indirizzi necessari per la subnet, contare il numero di macchine virtuali necessarie, fare una stima della crescita futura e quindi usare la tabella seguente per determinare le dimensioni della subnet.

Numero di macchine virtuali necessarie | Numero di bit di host necessari | Dimensioni della subnet
--- | --- | ---
1-3 | 3 | /29
4–11 | 4 | /28
12–27 | 5 | /27
28–59 | 6 | /26
60–123 | 7 | /25

> [AZURE.NOTE]Per le normali subnet locali, il numero massimo di indirizzi host per una subnet con n bit di host è 2<sup>n</sup> – 2. Per una subnet Azure, il numero massimo di indirizzi host per una subnet con n bit di host è 2<sup>n</sup> – 5 (2 più 3 per gli indirizzi che Azure utilizza in ogni subnet).

Se si sceglie una dimensione subnet troppo piccola, è necessario rinumerare e ridistribuire le macchine virtuali nella subnet.

### Riepilogo delle linee guida dell’implementazione per le reti virtuali

Decisioni:

- Quale tipo di rete virtuale è necessaria per funzionare da host per l’infrastruttura o il carico di lavoro IT (solo cloud o cross-premise)?
- Per le reti virtuali cross-premise, qual è la quantità di spazio degli indirizzi necessaria per funzionare da host per le subnet e le macchine virtuali ora e quale invece per l'espansione ragionevole in futuro?

Attività:

- Definire lo spazio degli indirizzi per la rete virtuale.
- Definire il set di subnet e lo spazio degli indirizzi per ognuno.
- Per le reti virtuali cross-premise, definire il set di spazi degli indirizzi della rete locale per le sedi locali che devono essere raggiunte dalle macchine virtuali nella rete virtuale.
- Creare la rete virtuale usando la convenzione di denominazione scelta. È possibile usare il portale di anteprima di Azure o il portale di Azure.

## 6\. Set di disponibilità

In Azure PaaS, i servizi cloud contengono uno o più ruoli che eseguono il codice dell'applicazione. I ruoli possono avere una o più istanze di macchine virtuali per le quali l'infrastruttura esegue automaticamente il provisioning. In qualsiasi momento, Azure può aggiornare le istanze di questi ruoli, ma poiché fanno parte dello stesso ruolo, Azure sa di non dover eseguire l'aggiornamento di tutte le istanze contemporaneamente per evitare un'interruzione del servizio per il ruolo.

In Azure IaaS, il concetto di ruolo non è significativo, poiché ogni macchina virtuale IaaS rappresenta un ruolo con una sola istanza. Al fine di suggerire ad Azure di non arrestare contemporaneamente due o più computer associati (ad esempio, per gli aggiornamenti del sistema operativo del nodo in cui risiedono), è stato introdotto il concetto dei set di disponibilità. Un set di disponibilità indica ad Azure di non arrestare tutti i computer nello stesso set di disponibilità contemporaneamente, per evitare un'interruzione del servizio. I membri della macchina virtuale di un set di disponibilità hanno un contratto di servizio che garantisce un tempo di attività del 99,95%.

I set di disponibilità devono essere parte della pianificazione dell’elevata disponibilità della soluzione. Un set di disponibilità è definito come set di macchine virtuali all'interno di un singolo servizio cloud con lo stesso nome del set di disponibilità. È possibile creare i set di disponibilità dopo aver creato i servizi cloud.

### Riepilogo delle linee guida dell’implementazione per i set di disponibilità

Decisione:

- Quanti set di disponibilità sono necessari per i diversi ruoli e livelli nell’infrastruttura o nel carico di lavoro IT?

Attività:

- Definire il set di set di disponibilità usando la convenzione di denominazione scelta. È possibile associare una macchina virtuale a un set di disponibilità quando si creano le macchine virtuali oppure dopo che è stata creata la macchina virtuale.

## 7\. Macchine virtuali

In Azure PaaS, Azure consente di gestire le macchine virtuali e i relativi dischi associati. È necessario creare e denominare i servizi cloud e i ruoli e successivamente Azure creerà le istanze associate a tali ruoli. Nel caso di Azure IaaS, è responsabilità dell'utente fornire nomi per i servizi cloud, le macchine virtuali e i dischi associati.

Per ridurre il carico amministrativo, il portale di Azure usa il nome del computer come un suggerimento per il nome predefinito per il servizio cloud associato (nel caso in cui il cliente scelga di creare un nuovo servizio cloud come parte della creazione guidata della macchina virtuale).

Inoltre, in Azure i dischi e i relativi BLOB VHD di supporto vengono denominati usando una combinazione del nome del servizio cloud, nome del computer e data di creazione.

In generale, il numero di dischi è di gran lunga maggiore rispetto al numero di macchine virtuali. È necessario prestare attenzione durante la modifica delle macchine virtuali per evitare dischi orfani. Inoltre, è possibile eliminare i dischi senza eliminare il BLOB di supporto. In tal caso, il BLOB resterà nell’account di archiviazione finché non verrà eliminato manualmente.

### Riepilogo delle linee guida dell’implementazione per le macchine virtuali

Decisione:

- Quante macchine virtuali è necessario fornire per l’infrastruttura o il carico di lavoro IT?

Attività:

- Definire ciascun nome di macchina virtuale usando la convenzione di denominazione scelta.
- Creare le macchine virtuali con il portale di anteprima di Azure, il portale di Azure, il cmdlet di PowerShell **New-AzureVM**, l'interfaccia della riga di comando di Azure o i modelli di Gestione risorse.

## Esempio di un carico di lavoro IT: il motore di analisi finanziaria di Contoso

Contoso Corporation ha sviluppato un motore di analisi finanziaria di ultima generazione, con algoritmi proprietari all’avanguardia di supporto nelle intermediazioni finanziarie di mercato del futuro. L'azienda vuole rendere disponibile questo motore ai clienti come insieme di server in Azure, composto da:

- Due (o più) server Web basati su IIS che eseguono servizi Web personalizzati in un livello Web
- Due (o più) server applicazioni basati su IIS che eseguono i calcoli in un livello applicazione
- Un cluster SQL Server 2014 con gruppi di disponibilità AlwaysOn (due server SQL e un server di controllo del nodo di maggioranza) in cui sono archiviati i dati cronologici e i dati di calcolo in corso in un livello di database
- Due controller di dominio Active Directory per una foresta completa e un dominio al livello di autenticazione, richiesto dal clustering di SQL Server
- Tutti i server si trovano su due subnet: una subnet front-end per i server Web e una back-end per i server applicazioni, un cluster SQL server 2014 e dei controller di dominio.

![](./media/virtual-machines-infrastructure-services-implementation-guidelines/example-tiers.png)

Il traffico Web protetto in ingresso, proveniente dai client di Contoso in Internet deve essere sottoposto al bilanciamento del carico tra i server web. Il traffico di richiesta di calcolo sotto forma di richieste HTTP dai server Web deve essere bilanciato tra i server applicazioni. Inoltre, il motore deve essere progettato per l'elevata disponibilità.

La progettazione risultante deve includere:

- Una sottoscrizione e un account Contoso Azure
- Account di archiviazione
- Una rete virtuale con due subnet
- Set di disponibilità per i gruppi di server con ruoli simili
- Macchine virtuali
- Un unico gruppo di risorse

Tutti gli elementi dovranno seguire le convenzioni di denominazione di Contoso:

- Contoso usa come prefisso [carico di lavoro IT]-[percorso]-[risorsa di Azure]. Per questo esempio, "azfae" (motore di analisi finanziaria di Azure) è il nome del carico di lavoro IT e "use" (Stati Uniti orientali 2) è il percorso, poiché la maggior parte dei clienti iniziali di Contoso si trova sulla costa orientale degli Stati Uniti.
- Gli account di archiviazione usano contosoazfaeusesa[description]. Si noti che contoso è stato aggiunto come prefisso per garantire l'univocità e che i nomi di account di archiviazione non supportano l'uso di trattini.
- Le reti virtuali usano AZFAE-USE-VN[number].
- I set di disponibilità usano azfae-use-as-[role].
- I nomi delle macchine virtuali usano azfae-use-vm-[vmname].

### Sottoscrizioni e account di Azure

Contoso usa la sottoscrizione Enterprise, denominata Contoso Enterprise Subscription, per fornire la fatturazione per questo carico di lavoro IT.

### Account di archiviazione

Contoso ha stabilito che sono necessari due account di archiviazione:

- **contosoazfaeusesawebapp**, per l’archiviazione standard di server Web, i server applicazioni, i controller di dominio e i relativi dischi dati aggiuntivi
- **contosoazfaeusesasqlclust**, per l'archiviazione premium dei server del cluster SQL Server e dei relativi dischi di dati aggiuntivi

### Una rete virtuale con subnet

Poiché la rete virtuale non necessita di connettività costante alla rete locale Contoso, l’azienda ha optato per una rete virtuale solo cloud.

Contoso ha creato una rete virtuale solo cloud con le impostazioni seguenti tramite il portale di anteprima di Azure:

- Nome: AZFAE-USE-VN01
- Sede: Stati Uniti orientali 2
- Spazio degli indirizzi della rete virtuale: 10.0.0.0/8
- Prima subnet:
	- Nome: FrontEnd
	- Spazio degli indirizzi: 10.0.1.0/24
- Seconda subnet:
	- Nome: BackEnd
	- Spazio degli indirizzi: 10.0.2.0/24

### Set di disponibilità

Per mantenere l'elevata disponibilità di tutti i quattro i livelli del loro motore di analisi finanziaria, Contoso ha optato per quattro set di disponibilità:

- **azfae-use-as-dc**, per i controller di dominio
- **azfae-use-as-web** per i server Web
- **azfae-use-as-app**, per i server applicazioni
- **azfae-use-as-sql**, per i server del cluster SQL Server

Questi set di disponibilità verranno creati insieme alle macchine virtuali.

### Macchine virtuali

Contoso ha optato per i seguenti nomi per le macchine virtuali di Azure:

- **azfae-use-vm-dc01**, per il primo controller di dominio
- **azfae-use-vm-dc02**, per il secondo controller di dominio
- **azfae-use-vm-web01**, per il primo server web
- **azfae-use-vm-web02**, per il secondo server web
- **azfae-use-vm-app01**, per il primo server applicazioni
- **azfae-use-vm-app02**, per il secondo server applicazioni
- **azfae-use-vm-sql01**, per il primo server SQL nel cluster SQL Server
- **azfae-use-vm-sql02**, per il secondo server SQL nel cluster SQL Server
- **azfae-use-vm-sqlmn01**, per il server di controllo della maggioranza dei nodi nel cluster SQL Server

Di seguito è riportata la configurazione risultante.

![](./media/virtual-machines-infrastructure-services-implementation-guidelines/example-config.png)

Questa configurazione include:

- Una rete virtuale solo cloud con due subnet (front-end e back-end)
- Due account di archiviazione
- Quattro set di disponibilità, uno per ciascun livello del motore di analisi finanziaria
- Le macchine virtuali per i quattro livelli
- Un set esterno con bilanciamento del carico per il traffico Web basato su HTTPS da Internet ai server Web
- Un set interno con bilanciamento del carico per il traffico Web crittografato dai server Web ai server applicazioni
- Un unico gruppo di risorse

## Risorse aggiuntive

[Limiti, quote e vincoli delle sottoscrizioni e dei servizi di Microsoft Azure](../azure-subscription-service-limits.md#storage-limits)

[Dimensioni delle macchine virtuali](virtual-machines-size-specs.md)

[Obiettivi di scalabilità e prestazioni per Archiviazione di Azure](../storage-scalability-targets.md)

[Framework di integrazione piattaforma cloud (Modelli di architettura di Azure)](../azure-architectures-cpif-overview.md)

[Diagramma dell'architettura di riferimento per l'estensione di un data center](https://gallery.technet.microsoft.com/Datacenter-extension-687b1d84)

[Provider di calcolo, rete e archiviazione in Gestione risorse di Azure](../articles/virtual-machines/virtual-machines-azurerm-versus-azuresm.md)

<!---HONumber=Oct15_HO4-->