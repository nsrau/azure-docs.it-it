<properties 
	pageTitle="Linee guida sull’implementazione dei servizi di infrastruttura di Azure" 
	description="Informazioni sulle linee guida di progettazione e implementazione fondamentali per la distribuzione di un carico di lavoro IT nei servizi di infrastruttura di Azure." 
	documentationCenter=""
	services="virtual-machines" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/05/2015" 
	ms.author="josephd"/>

# Linee guida sull’implementazione dei servizi di infrastruttura di Azure
 
Queste linee guida sono incentrate sulle decisioni essenziali nella progettazione e sulle attività per determinare la varietà di risorse coinvolte nella maggior parte delle implementazioni dei servizi di infrastruttura di Azure.

Azure è una piattaforma eccellente per implementare la configurazione di prova, poiché richiede un investimento minimo per eseguire il test di un approccio specifico all'implementazione di soluzioni. Tuttavia, è necessario essere in grado di distinguere le procedure semplici per un modello di prova da quelle più complesse, dettagliate per un'implementazione completamente funzionale, per l’ambiente di produzione, di un carico di lavoro IT.

In questa guida vengono identificate molte aree per le quali la pianificazione è fondamentale per il successo di un'infrastruttura IT o di un carico di lavoro in Azure. Inoltre, la guida è utile per l'implementazione di soluzioni sulla piattaforma Azure in quanto fornisce un ordine per la creazione delle risorse necessarie. Sebbene sia possibile una certa flessibilità, Microsoft consiglia di applicare questo ordine alla pianificazione e al processo decisionale.

1.	Convenzioni di denominazione
2.	Sottoscrizioni e account
3.	Archiviazione
4.	Reti virtuali
5.	Servizi cloud
6.	SET DI DISPONIBILITÀ
7.	Macchine virtuali

Definire una buona convenzione di denominazione, nonché seguire un ordine specifico, sistematico nella creazione delle risorse in Azure riduce enormemente il carico amministrativo e aumenta le probabilità di successo di qualsiasi progetto di implementazione.

> [AZURE.NOTE]I gruppi di affinità non sono descritti in quanto il loro utilizzo è stato deprecato. Per ulteriori informazioni, vedere [Informazioni sulle reti virtuali di area e sui gruppi di affinità](https://msdn.microsoft.com/library/azure/jj156085.aspx).

## 1. Convenzioni di denominazione

Prima di creare qualsiasi elemento in Azure, è necessaria una buona convenzione di denominazione Una convenzione di denominazione garantisce la possibilità che tutte le risorse dispongano di un nome stimabile, in modo da ridurre il carico amministrativo associato alla gestione di tali risorse.

È possibile scegliere di seguire un set specifico di convenzioni di denominazione definite per l'intera organizzazione o per un account o una sottoscrizione specifica di Azure. Sebbene sia facile per i singoli utenti all'interno delle organizzazioni stabilire le regole implicite quando si utilizzano risorse di Azure, quando un team deve lavorare a un progetto in Azure, la scalabilità di tale modello non è ottimale.

Il set di convenzioni di denominazione deve essere concordato in anticipo. Alcune considerazioni relative alle convenzioni di denominazione trascendono i set di regole che compongono queste convenzioni.

### Affissi

Quando si creano determinate risorse, Azure utilizzerà alcune impostazioni predefinite per semplificare la gestione delle risorse associate a queste risorse. Ad esempio, quando si crea la prima macchina virtuale per un nuovo servizio cloud, il portale di gestione di Azure tenterà di utilizzare il nome della macchina virtuale come nome di un nuovo servizio cloud per la macchina virtuale.

Pertanto, è utile identificare i tipi di risorse che richiedono un affisso per l’identificazione del tipo. Inoltre, specificare chiaramente la posizione dell’affisso:

- Inizio del nome (prefisso)
- Fine del nome (suffisso)

Ad esempio, di seguito sono indicati due nomi possibili per un servizio cloud che funziona da host per un motore di calcolo:

- Svc-CalculationEngine (prefisso)
- CalculationEngine-Svc (suffisso)

Gli affissi possono fare riferimento ai diversi aspetti che descrivono le specifiche risorse. Nella tabella seguente vengono illustrati alcuni esempi utilizzati comunemente.

Aspetto | esempi | Note
--- | --- | ---
Environment | dev, stg, prod | A seconda dello scopo e del nome di ogni ambiente.
Location | usw (Stati Uniti occidentali), use (Stati Uniti orientali 2) | A seconda dell'area del data center o dell’area dell'organizzazione.
Componente di Azure, servizio o prodotto | Svc per servizio cloud, VNet per rete virtuale | A seconda del prodotto per il quale la risorsa fornisce il supporto.
Ruolo | sql, ora, sp, iis | A seconda del ruolo della macchina virtuale.
Istanza | 01, 02 e 03 e così via. | Per le risorse che possono avere più di un'istanza. Ad esempio, i server Web con carico bilanciato in un servizio cloud.
		
Quando si stabiliscono le convenzioni di denominazione, assicurarsi che siano indicati chiaramente quali affissi utilizzare per ogni tipo di risorsa, e in quale posizione (prefisso o suffisso).

### Date

Molte volte è importante determinare la data di creazione dal nome di una risorsa. Microsoft consiglia il formato di data AAAAMMGG. Tale formato garantisce non solo che venga registrata la data completa, ma anche che due risorse i cui nomi si differenziano solo per la data verranno ordinate alfabeticamente e allo stesso tempo in ordine cronologico.

### Nomi delle risorse
È necessario definire ciascun tipo di risorsa nella convenzione di denominazione, pertanto è necessario avere regole che definiscono come assegnare nomi a ogni risorsa creata. Tali regole devono essere applicate a tutti i tipi di risorse, ad esempio:

- Sottoscrizioni
- Account
- Account di archiviazione
- Reti virtuali
- Subnet
- SET DI DISPONIBILITÀ
- Microsoft Azure
- Macchine virtuali
- Endpoint
- Gruppi di sicurezza di rete
- Ruoli

I nomi devono quanto più possibile descrittivi, per garantire che il nome sia in grado di fornire informazioni sufficienti per determinare a quali risorse fa riferimento.

### Nomi dei computer

Quando gli amministratori creano una macchina virtuale, Microsoft Azure richiede di specificare un nome macchina virtuale. Microsoft Azure utilizzerà il nome della macchina virtuale come nome della risorsa macchina virtuale di Azure. Azure utilizzerà lo stesso nome come nome computer per il sistema operativo installato nella macchina virtuale. Tuttavia, questi nomi potrebbero non essere sempre gli stessi.

Nei casi in cui viene creata una macchina virtuale da un File VHD che già contiene un sistema operativo, il nome della macchina virtuale in Microsoft Azure può essere diverso dal nome computer del sistema operativo della macchina virtuale. Questa situazione può aggiungere un livello di difficoltà alla gestione delle macchine virtuali, pertanto è sconsigliata. Assicurarsi sempre che il nome della risorsa macchina virtuale di Azure sia lo stesso nome del computer assegnato al sistema operativo di tale macchina virtuale.

È consigliabile che il nome della macchina virtuale di Azure sia identico al nome di computer del sistema operativo. Pertanto, seguire le regole di denominazione NetBIOS, come descritto in [Convenzioni di denominazione di computer NetBIOS Microsoft](https://support.microsoft.com/kb/188997/).

### Nomi account di archiviazione

Gli account di archiviazione hanno regole speciali che ne controllano i nomi. È possibile utilizzare solo lettere minuscole e numeri e il nome assegnato, concatenato al servizio (BLOB, tabella o coda). Il dominio predefinito (core.windows.net) deve eseguire il rendering di un nome DNS valido a livello globale e univoco. Ad esempio, se il nome dell'account di archiviazione è mystorageaccount, i seguenti URL risultanti devono essere nomi DNS univoci validi:

- mystorageaccount.blob.core.windows.net
- mystorageaccount.table.core.windows.net
- mystorageaccount.queue.core.windows.net

Inoltre, gli account di archiviazione possono avvalersi dei contenitori. Questi devono essere conformi alle convenzioni di denominazione come descritto in [Assegnazione di nome e riferimento a contenitori, BLOB e metadati](https://msdn.microsoft.com/library/azure/dd135715.aspx).

### Nomi dei blocchi predefiniti di Azure

I blocchi predefiniti di Azure sono servizi a livello di applicazione offerti da Azure in genere alle applicazioni che sfruttano le funzionalità PaaS, sebbene possano essere utilizzati dalle risorse IaaS, come SQL Azure, Gestione traffico e altre.

Questi servizi si basano su una matrice di elementi che vengono creati e registrati in Azure. Anche questi dovranno essere considerati nelle convenzioni di denominazione.

### Riepilogo delle linee guida di implementazione per le convenzioni di denominazione

Decisione:

- Quali sono le convenzioni di denominazione per le risorse di Azure? 

Attività:

- Definire le convenzioni di denominazione in termini di affissi, gerarchia, valori di stringa e altri criteri per le risorse di Azure.

## 2. Sottoscrizioni e account

Per lavorare con Azure, sono necessarie una o più sottoscrizioni ad Azure. Le risorse, ad esempio i servizi cloud o le macchine virtuali, esistono nel contesto di tali sottoscrizioni.

- I clienti aziendali dispongono in genere di una registrazione Enterprise, che è la risorsa più in alto nella gerarchia ed è associata a uno o più account. 
- Per utenti e clienti senza una registrazione Enterprise, la risorsa più in alto è l'Account.
- Le sottoscrizioni sono associate agli account ed è possibile associare una o più sottoscrizioni a ogni account. In Azure le informazioni relative alla fatturazione vengono registrate a livello di sottoscrizione.

A causa del limite di due livelli della gerarchia nella relazione di account/sottoscrizione è importante allineare la convenzione di denominazione degli account e delle sottoscrizioni alle esigenze di fatturazione. Ad esempio, se un'azienda globale utilizza Azure, può scegliere di disporre di un account per ogni area e fare in modo che le sottoscrizioni vengano gestite a livello di area.

![](./media/virtual-machines-infrastructure-services-implementation-guidelines/sub01.png)
  
Ad esempio, è possibile utilizzare questa struttura.

![](./media/virtual-machines-infrastructure-services-implementation-guidelines/sub02.png)
  
Seguendo lo stesso esempio, se in un'area si decide di associare più sottoscrizioni a un determinato gruppo, la convenzione di denominazione deve incorporare un modo per codificare la parte aggiuntiva sia nel nome account che nel nome della sottoscrizione. Questa organizzazione consente di elaborare i dati di fatturazione per generare nuovi livelli di gerarchia durante la fatturazione dei report.

![](./media/virtual-machines-infrastructure-services-implementation-guidelines/sub03.png)

L'organizzazione potrebbe avere un aspetto simile al seguente.

![](./media/virtual-machines-infrastructure-services-implementation-guidelines/sub04.png)
 
Microsoft fornisce la fatturazione dettagliata tramite un file scaricabile, per un singolo account o per tutti gli account di un contratto enterprise. È possibile elaborare questo file, ad esempio, tramite Excel. Questo processo potrebbe inserire i dati, partizionare le risorse che codificano più livelli della gerarchia in colonne separate e utilizzare una tabella pivot o PowerPivot per fornire funzionalità di creazione di report dinamici.

### Riepilogo delle linee guida dell’implementazione per sottoscrizioni e account

Decisione:

- Quali sono i set di sottoscrizioni e account necessari per l’hosting dell’infrastruttura o del carico di lavoro IT?

Attività:

- Creare il set di sottoscrizioni e account utilizzando la convenzione di denominazione scelta.

## 3. Archiviazione

L’archiviazione è parte integrante di qualsiasi soluzione Azure, poiché non solo fornisce servizi a livello di applicazione, ma è anche parte dell'infrastruttura di supporto delle macchine virtuali.
 
In Azure sono disponibili due tipi di archiviazione: L’archiviazione standard consente di accedere all'archiviazione BLOB, all'archiviazione tabelle, all'archiviazione di accodamento e all'archiviazione file. L’archiviazione Premium è progettata per le applicazioni a prestazioni elevate, ad esempio SQL Server in un cluster AlwaysOn e attualmente supporta solo i dischi di macchine virtuali di Azure.

Gli account di archiviazione sono associati a obiettivi di scalabilità. Vedere [Limiti, quote e vincoli delle sottoscrizioni e dei servizi di Microsoft Azure](azure-subscription-service-limits.md#storage-limits) per acquisire familiarità con i limiti di archiviazione correnti. Vedere anche [Obiettivi di scalabilità e prestazioni per Archiviazione di Azure](storage/storage-scalability-targets.md).

Azure consente di creare macchine virtuali con un disco del sistema operativo, un disco temporaneo e zero o più dischi dati facoltativi. Il disco del sistema operativo e i dischi dati sono BLOB di Azure, mentre il disco temporaneo viene supportato dall'archiviazione locale del nodo cui si trova il computer. Ciò rende il disco temporaneo non idoneo per i dati che devono essere salvati in modo permanente, poiché il computer potrebbe essere migrato automaticamente da un nodo all’altro, perdendo tutti i dati in tale disco. Non archiviare alcun elemento nell’unità temporanea.

I dischi del sistema operativo e i dischi dati hanno una dimensione massima di 1023 GB, poiché la dimensione massima di un BLOB che deve contenere i metadati (piè di pagina) del file VHD (un GB è composto da 10243 byte) è di 1024 GB. Per superare questo limite, è possibile implementare lo striping del disco in Windows.

### Dischi con striping
Oltre a fornire la capacità per creare dischi di dimensioni superiori a 1023 GB, in molti casi, utilizzare lo striping per i dischi dati contribuisce a migliorare le prestazioni, consentendo a più BLOB di supportare l'archiviazione per un singolo volume. In questo modo viene parallelizzato l’I/O richiesto per la scrittura e la lettura dei dati da un singolo disco.

In Azure sono presenti dei limiti sulla quantità di dischi dati e di larghezza di banda disponibile, a seconda delle dimensioni della macchina virtuale. Per informazioni dettagliate, vedere [Dimensioni delle macchine virtuali e dei servizi cloud per Azure](https://msdn.microsoft.com/library/azure/dn197896.aspx).

Se si utilizza lo striping del disco per i dischi dati di Azure, considerare le linee guida seguenti:

- I dischi dati devono essere sempre della massima dimensione (1023 GB)
- Connettere i dischi dati massimi consentiti per la dimensione della macchina virtuale
- Utilizzare la configurazione degli spazi di archiviazione
- Utilizzare la configurazione dello striping dell’archiviazione
- Evitare di utilizzare le opzioni di memorizzazione nella cache del disco di dati di Azure (criterio di memorizzazione nella cache = Nessuno)

Per ulteriori informazioni, vedere l’articolo sugli [spazi di archiviazione e la progettazione delle prestazioni](http://social.technet.microsoft.com/wiki/contents/articles/15200.storage-spaces-designing-for-performance.aspx).

### Account di archiviazione multipli

L’utilizzo di più account di archiviazione per supportare i dischi associati a un gran numero di macchine virtuali garantisce che l’I/O aggregato di tali dischi sia ben al di sotto degli obiettivi di scalabilità per ognuno di tali account di archiviazione.

Microsoft consiglia di iniziare con la distribuzione di una sola macchina virtuale per account di archiviazione.

### Progettazione del layout di archiviazione

Per implementare queste strategie di implementazione del sottosistema del disco delle macchine virtuali con buone prestazioni, un’infrastruttura o un carico di lavoro IT in genere usufruirà di molti di account di archiviazione. Tali account ospiteranno un gran numero di BLOB VHD. In alcuni casi, a un singolo volume in una macchina virtuale sono associati più BLOB.

Questa situazione può rendere più complesse le attività di gestione. La progettazione di una strategia di archiviazione affidabile, che includa la denominazione appropriata per i dischi sottostanti e i BLOB VHD associati è fondamentale.

### Riepilogo delle linee guida dell’implementazione per l’archiviazione

Decisioni:

- È necessario lo striping del disco per creare dischi più grandi di 500 TB?
- È necessario lo striping del disco per ottenere prestazioni ottimali per il carico di lavoro?
- Quali sono i set di account di archiviazione necessari per l’hosting dell’infrastruttura o del carico di lavoro IT?

Attività:

- Creare il set di account di archiviazione utilizzando la convenzione di denominazione scelta. È possibile utilizzare il portale di anteprima di Azure, il portale di gestione di Azure o il cmdlet PowerShell **New-AzureStorageAccount**.

## 4. Microsoft Azure

I servizi cloud sono un blocco predefinito fondamentale in Azure, sia per i servizi PaaS che per quelli IaaS. Per il modello PaaS, i servizi cloud rappresentano un'associazione di ruoli, le cui istanze possono comunicare tra loro. I servizi cloud sono associati a un indirizzo VIP e a un bilanciamento del carico, che accetta il traffico in ingresso da Internet e ne bilancia il carico ai ruoli configurati per la ricezione di tale traffico.

Nel caso di IaaS, i servizi cloud offrono una funzionalità simile, sebbene nella maggior parte dei casi, la funzionalità di bilanciamento del carico sia utilizzata per inoltrare il traffico a porte TCP o UDP specifiche da Internet alle numerose macchine virtuali all'interno di tale servizio cloud.

I nomi dei servizi cloud sono particolarmente importanti in IaaS, dal momento che Azure li utilizza come parte della convenzione di denominazione per i dischi. Il nome del servizio cloud può contenere solo lettere, numeri e trattini. Il primo e l’ultimo carattere nel campo deve essere una lettera o un numero.

Microsoft Azure espone i nomi dei servizi cloud, poiché sono associati all'indirizzo VIP, nel dominio "cloudapp.net". Per migliorare l'esperienza utente relativamente all’applicazione, è necessario configurare un nome personalizzato per sostituire il nome del servizio cloud completo. Tale operazione in genere viene eseguita con un record CNAME nel DNS pubblico che associa il nome DNS pubblico della risorsa, ad esempio www.contoso.com, al nome DNS del servizio cloud che funziona da host per la risorsa (ad esempio, il servizio cloud che funziona da host per i server Web per www.contoso.com).

Inoltre, potrebbe essere necessario che la convenzione di denominazione utilizzata per i servizi cloud tolleri le eccezioni, perché i nomi dei servizi cloud devono essere univoci tra tutti gli altri servizi di cloud Microsoft Azure, indipendentemente dal tenant Microsoft Azure.

Le sottoscrizioni di Azure possono supportare un massimo di 200 servizi cloud.

### Riepilogo delle linee guida dell’implementazione per i servizi cloud

Decisione:

- Quali sono i set di servizi cloud necessari per l’hosting dell’infrastruttura o del carico di lavoro IT? 

Attività:

- Creare il set di servizi cloud utilizzando la convenzione di denominazione scelta. È possibile utilizzare il portale di gestione di Azure o il cmdlet di PowerShell **New-AzureService**.

## 5. Reti virtuali

Il passaggio logico successivo consiste nel creare le reti virtuali necessarie per supportare le comunicazioni tra le macchine virtuali nella soluzione. Sebbene sia possibile eseguire l’hosting di più macchine virtuali di un carico di lavoro IT all'interno di un solo servizio cloud, è consigliabile utilizzare le reti virtuali.

Le reti virtuali sono un contenitore per le macchine virtuali per le quali è anche possibile specificare subnet, indirizzamento personalizzato e opzioni di configurazione DNS. Le macchine virtuali all'interno della stessa rete virtuale possono comunicare direttamente con altri computer nella stessa rete virtuale, indipendentemente dal servizio cloud a cui appartengono. All'interno della rete virtuale, questa comunicazione rimane privata, senza la necessità di passare tramite endpoint pubblici. La comunicazione può avere luogo tramite indirizzo IP o mediante il nome, utilizzando un server DNS installato nella rete virtuale o in locale, se la macchina virtuale è connessa alla rete aziendale.

### Connettività del sito
Se utenti e computer locali non richiedono connettività costante alle macchine virtuali in una rete virtuale Azure, creare una rete virtuale solo cloud.

![](./media/virtual-machines-infrastructure-services-implementation-guidelines/vnet01.png)
 
Si tratta di una rete indicata in genere per carichi di lavoro con connessione Internet, ad esempio un server Web basato su Internet. È possibile gestire queste macchine virtuali utilizzando connessioni Desktop remoto, sessioni di PowerShell remote, connessioni Secure Shell (SSH) e connessioni VPN point-to-site.

Poiché non si connettono alla rete locale, le reti virtuali solo cloud possono utilizzare qualsiasi parte dello spazio di indirizzi IP privato.

Se computer e utenti locali richiedono la connettività costante alle macchine virtuali in una rete virtuale di Azure, creare una rete virtuale cross-premise e connetterla alla rete locale con un ExpressRoute o una connessione VPN da sito a sito.

![](./media/virtual-machines-infrastructure-services-implementation-guidelines/vnet02.png)
 
In questa configurazione, la rete virtuale di Azure è essenzialmente un'estensione della rete locale basata su cloud.

Poiché si connettono alla rete locale, le reti virtuali cross-premise devono utilizzare una parte dello spazio di indirizzi usato dall’organizzazione che sia univoco, e l’infrastruttura di routing deve supportare il traffico di routing a tale porzione inoltrandolo al dispositivo VPN locale.

Per consentire ai pacchetti di spostarsi dalla rete virtuale cross-premise alla rete locale, è necessario configurare il set di prefissi di indirizzo locali pertinenti come parte della definizione della rete locale per la rete virtuale. A seconda lo spazio di indirizzi della rete virtuale e del set di percorsi locali pertinenti, è possibile che nella rete locale siano presenti molti prefissi di indirizzo.

È possibile convertire una rete virtuale solo cloud in una rete virtuale cross-premise. Tuttavia, probabilmente sarà necessario rinumerare lo spazio di indirizzi della rete virtuale, le subnet e le macchine virtuali che utilizzano indirizzi IP statici assegnati da Azure, noti come IP dinamici (DIP). Pertanto, valutare attentamente il tipo di reti virtuali necessario (solo cloud o locali) prima di crearle.

### Subnet
Le subnet consentono di organizzare le risorse correlate, logicamente (ad esempio, una subnet per le macchine virtuali associate alla stessa applicazione) o fisicamente (ad esempio, una subnet per il servizio cloud) oppure di ricorrere a tecniche di isolamento subnet per una maggiore sicurezza.

Per le reti virtuali cross-premise, è necessario progettare delle subnet con le stesse convenzioni utilizzate per le risorse locali, tenendo presente che **Azure utilizza sempre i primi tre indirizzi IP dello spazio di indirizzi per ciascuna subnet**. Per determinare il numero di indirizzi necessari per la subnet, contare il numero di macchine virtuali necessarie, fare una stima della crescita futura e quindi utilizzare la tabella seguente per determinare le dimensioni della subnet.
 
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
- Creare la rete virtuale utilizzando la convenzione di denominazione scelta. È possibile utilizzare il portale di anteprima di Azure o il portale di gestione di Azure.

## 6. SET DI DISPONIBILITÀ

In Azure PaaS, i servizi cloud contengono uno o più ruoli che eseguono il codice dell'applicazione. I ruoli possono avere una o più istanze di macchine virtuali per le quali l'infrastruttura esegue automaticamente il provisioning. In qualsiasi momento, Azure può aggiornare le istanze di questi ruoli, ma poiché fanno parte dello stesso ruolo, Azure sa di non eseguire l'aggiornamento di tutte le istanze contemporaneamente per evitare un'interruzione del servizio per il ruolo.

In Azure IaaS, il concetto di ruolo non è significativo, poiché ogni macchina virtuale IaaS rappresenta un ruolo con una sola istanza. Al fine di suggerire ad Azure di non arrestare contemporaneamente due o più computer associati (ad esempio, per gli aggiornamenti del sistema operativo del nodo in cui risiedono), è stato introdotto il concetto dei set di disponibilità. Un set di disponibilità indica ad Azure di non arrestare tutti i computer nello stesso set di disponibilità contemporaneamente, per evitare un'interruzione del servizio. I membri della macchina virtuale di un set di disponibilità hanno un contratto di servizio che garantisce un tempo di attività del 99,95%.

I set di disponibilità devono essere parte della pianificazione dell’elevata disponibilità della soluzione. Un set di disponibilità è definito come set di macchine virtuali all'interno di un singolo servizio cloud con lo stesso nome del set di disponibilità. È possibile creare i set di disponibilità dopo aver creato i servizi cloud.

### Riepilogo delle linee guida dell’implementazione per i set di disponibilità

Decisione:

- Quanti set di disponibilità sono necessari per i diversi ruoli e livelli nell’infrastruttura o nel carico di lavoro IT?

Attività:

- Definire il set di set di disponibilità utilizzando la convenzione di denominazione scelta. È possibile associare una macchina virtuale a un set di disponibilità quando si creano le macchine virtuali oppure dopo che è stata creata la macchina virtuale.

## 7. Macchine virtuali

In Azure PaaS, Azure consente di gestire le macchine virtuali e i relativi dischi associati. È necessario creare e denominare i servizi cloud e i ruoli e successivamente Azure creerà le istanze associate a tali ruoli. Nel caso di Azure IaaS, è responsabilità dell'utente fornire nomi per i servizi cloud, le macchine virtuali e i dischi associati.

Per ridurre il carico amministrativo, il portale di gestione di Azure utilizzerà il nome del computer come un suggerimento per il nome predefinito per il servizio cloud associato (nel caso in cui il cliente scelga di creare un nuovo servizio cloud come parte della creazione guidata della macchina virtuale).

Inoltre, in Azure i dischi e i relativi BLOB VHD di supporto vengono denominati utilizzando una combinazione del nome del servizio cloud, nome del computer e data di creazione.

In generale, il numero di dischi sarà di gran lunga maggiore rispetto alla quantità di macchine virtuali. È necessario prestare attenzione durante la modifica delle macchine virtuali per evitare dischi orfani. Inoltre, è possibile eliminare i dischi senza eliminare il BLOB di supporto. In tal caso, il BLOB resterà nell’account di archiviazione finché non verrà eliminato manualmente.

### Riepilogo delle linee guida dell’implementazione per le macchine virtuali

Decisione:

- Quante macchine virtuali è necessario fornire per l’infrastruttura o il carico di lavoro IT?

Attività:

- Definire ciascun nome di macchina virtuale utilizzando la convenzione di denominazione scelta.
- Creare le macchine virtuali con il portale di anteprima di Azure, il portale di gestione di Azure o il cmdlet PowerShell **New-AzureVM**.

## Esempio di un carico di lavoro IT: il motore di analisi finanziaria di Contoso

Contoso Corporation ha sviluppato un motore di analisi finanziaria di ultima generazione, con algoritmi proprietari all’avanguardia di supporto nelle intermediazioni finanziarie di mercato del futuro. L’azienda desidera rendere disponibile questo motore ai clienti come insieme di server in Azure, composto da:

- Due (o più) server Web basati su IIS che eseguono servizi Web personalizzati in un livello Web
- Due (o più) server applicazioni basati su IIS che eseguono i calcoli in un livello applicazione
- Un cluster SQL Server 2014 con gruppi di disponibilità AlwaysOn (due server SQL e un server di controllo della maggioranza dei nodi) in cui sono memorizzati i dati cronologici e i dati di calcolo in corso in un livello di database
- Due controller di dominio Active Directory per una foresta completa e un dominio al livello di autenticazione, richiesto dal clustering di SQL Server
- Tutti i server si trovano su due subnet; una subnet front-end per i server Web e una back-end per i server applicazioni, un cluster SQL server 2014 e dei controller di dominio

![](./media/virtual-machines-infrastructure-services-implementation-guidelines/example-tiers.png)
 
Il traffico Web protetto in ingresso, proveniente dai client di Contoso in Internet deve essere sottoposto al bilanciamento del carico tra i server web. Il traffico di richiesta di calcolo sotto forma di richieste HTTP dai server Web deve essere bilanciato tra i server applicazioni. Inoltre, il motore deve essere progettato per l’elevata disponibilità.

La progettazione risultante deve includere:

- Una sottoscrizione e un account Contoso Azure
- Account di archiviazione
- Una rete virtuale con due subnet
- Un set di servizi cloud
- Set di disponibilità per i gruppi di server con ruoli simili
- Macchine virtuali

Tutti gli elementi dovranno seguire le convenzioni di denominazione di Contoso:

- Contoso utilizza come prefisso [carico di lavoro IT]-[percorso]-[risorsa di Azure]. Per questo esempio, "azfae" (motore di analisi finanziaria di Azure) è il nome del carico di lavoro IT e "use" (Stati Uniti orientali 2) è il percorso, poiché la maggior parte dei clienti iniziali di Contoso si trova sulla costa orientale degli Stati Uniti.
- Gli account di archiviazione utilizzano contosoazfaeusesa[description] Si noti che contoso è stato aggiunto come prefisso per garantire l'univocità, e che i nomi account di archiviazione non supportano l'utilizzo di trattini.
- I servizi cloud utilizzano contoso-azfae-use-cs-[description] Si noti che contoso è stato aggiunto come prefisso per fornire univocità.
- Le reti virtuali utilizzano AZFAE-USE-VN[number].
- I set di disponibilità utilizzano azfae-use-as-[role].
- I nomi macchine virtuali utilizzano azfae-use-vm-[vmname].

### Sottoscrizioni e account di Azure

Contoso utilizza la sottoscrizione Enterprise, denominata Contoso Enterprise Subscription, per fornire la fatturazione per questo carico di lavoro IT.

### Account di archiviazione

Contoso ha stabilito che sono necessari due account di archiviazione:

- **contosoazfaeusesawebapp**, per l’archiviazione standard di server Web, server applicazioni, controller di dominio e i relativi dischi dati aggiuntivi
- **contosoazfaeusesasqlclust**, per l'archiviazione premium dei server del cluster SQL Server e dei relativi dischi di dati aggiuntivi

Contoso ha creato due account di archiviazione con i seguenti comandi PowerShell:

	New-AzureStorageAccount -StorageAccountName "contosoazfaeusesawebapp" -Location "East US 2"
	New-AzureStorageAccount -StorageAccountName "contosoazfaeusesasqlclust" -Location "East US 2" -Type Premium_LRS

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

### Servizi cloud

Contoso ha optato per due servizi cloud:

- **contoso-azfae-use-cs-frontend**, per i server Web front-end
- **contoso-azfae-use-cs-backend**, per i server applicazioni back-end, i server del cluster SQL server e i controller di dominio

Contoso ha creato due servizi cloud con i seguenti comandi PowerShell:

	New-AzureService -Service "contoso-azfae-use-cs-frontend" -Location "East US 2"
	New-AzureService -Service "contoso-azfae-use-cs-backend" -Location "East US 2"

### SET DI DISPONIBILITÀ

Per mantenere l'elevata disponibilità di tutti i quattro i livelli del loro motore di analisi finanziaria, Contoso ha optato per quattro set di disponibilità:

- **azfae-use-as-dc**, per i controller di dominio
- **azfae-use-as-web**, per i server Web
- **azfae-use-as-app**, per i server applicazioni
- **azfae-use-as-sql**, per i server del cluster SQL Server

Questi set di disponibilità verranno creati insieme alle macchine virtuali.

### Macchine virtuali

Contoso ha optato per i seguenti nomi per le macchine virtuali di Azure:

- **azfae-use-vm-dc01**, per il primo controller di dominio
- **azfae-use-vm-dc02**, per il secondo controller di dominio
- **azfae-use-vm-web01**, per il primo server Web
- **azfae-use-vm-web02**, per il secondo server Web
- **azfae-use-vm-app01**, per il primo server applicazioni
- **azfae-use-vm-app02**, per il secondo server applicazioni
- **azfae-use-vm-sql01**, per il primo server SQL del cluster SQL Server
- **azfae-use-vm-sql02**, per il secondo server SQL del cluster SQL Server
- **azfae-use-vm-sqlmn01**, per il server di controllo della maggioranza dei nodi nel cluster SQL Server

Di seguito è riportata la configurazione risultante.

![](./media/virtual-machines-infrastructure-services-implementation-guidelines/example-config.png)
 
Questa configurazione include:

- Una rete virtuale solo cloud con due subnet (front-end e back-end)
- Due servizi cloud
- Due account di archiviazione
- Quattro set di disponibilità, uno per ciascun livello del motore di analisi finanziaria
- Le macchine virtuali per i quattro livelli
- Un set esterno con bilanciamento del carico per il traffico Web basato su HTTPS da Internet ai server Web
- Un set interno con bilanciamento del carico per il traffico Web crittografato dai server Web ai server applicazioni

Questi comandi PowerShell di Azure creano le macchine virtuali in questa configurazione per gli account di archiviazione, i servizi cloud e la rete virtuale creati in precedenza.

	#Specify the storage account for the web and application servers
	Set-AzureSubscription –SubscriptionName "Contoso Enterprise Subscription" -CurrentStorageAccountName "contosoazfaeusesawebapp"
	
	#Specify the cloud service name for the web servers
	$ServiceName="contoso-azfae-use-cs-frontend"
	
	#Get the image string for the latest version of the Windows Server 2012 R2 Datacenter image in the gallery
	$image= Get-AzureVMImage | where { $_.ImageFamily -eq "Windows Server 2012 R2 Datacenter" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	
	#Create the first web server
	$cred1=Get-Credential –Message "Type the name and password of the local administrator account for the first web server."
	$vm1=New-AzureVMConfig -Name azfae-use-vm-web01 -InstanceSize large -ImageName $image -AvailabilitySetName azfae-use-as-web
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password 
	$vm1 | Set-AzureSubnet -SubnetNames FrontEnd
	$vm1 | Add-AzureEndpoint -Name Web1 -Protocol tcp -LocalPort 443 -PublicPort 443 -LBSetName "WebSet" -DefaultProbe
	New-AzureVM –ServiceName $ServiceName -VMs $vm1 -VNetName AZFAE-USE-VN01
	
	#Create the second web server 
	$cred1=Get-Credential –Message "Type the name and password of the local administrator account for the second web server."
	$vm1=New-AzureVMConfig -Name azfae-use-vm-web02 -InstanceSize Large -ImageName $image -AvailabilitySetName azfae-use-as-web
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password 
	$vm1 | Set-AzureSubnet -SubnetNames FrontEnd
	$vm1 | Add-AzureEndpoint -Name Web2 -Protocol tcp -LocalPort 443 -PublicPort 443 -LBSetName "WebSet" -DefaultProbe
	New-AzureVM –ServiceName $ServiceName -VMs $vm1 -VNetName AZFAE-USE-VN01
	
	#Specify the cloud service name for the application, SQL server, and authentication tiers
	$ServiceName="contoso-azfae-use-cs-backend"
	
	#Create the first domain controller server
	$cred1=Get-Credential –Message "Type the name and password of the local administrator account for the first domain controller server."
	$vm1=New-AzureVMConfig -Name azfae-use-vm-dc01 -InstanceSize Small -ImageName $image -AvailabilitySetName azfae-use-as-dc
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password 
	$vm1 | Set-AzureSubnet -SubnetNames BackEnd
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 100 -DiskLabel AppFiles –LUN 0 -HostCaching None
	New-AzureVM –ServiceName $ServiceName -VMs $vm1 -VNetName AZFAE-USE-VN01
	
	#Create the second domain controller server
	$cred1=Get-Credential –Message "Type the name and password of the local administrator account for the second domain controller server."
	$vm1=New-AzureVMConfig -Name azfae-use-vm-dc02 -InstanceSize Small -ImageName $image -AvailabilitySetName azfae-use-as-dc
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password 
	$vm1 | Set-AzureSubnet -SubnetNames BackEnd
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 100 -DiskLabel AppFiles –LUN 0 -HostCaching None
	New-	AzureVM –ServiceName $ServiceName -VMs $vm1 -VNetName AZFAE-USE-VN01
	
	#Create an internal load balancer instance for the application server tier 
	Add-AzureInternalLoadBalancer -ServiceName $ServiceName -InternalLoadBalancerName "AppTierILB" –SubnetName BackEnd –StaticVNetIPAddress 10.0.2.100
	
	#Create the first application server
	$cred1=Get-Credential –Message "Type the name and password of the local administrator account for the first application server."
	$vm1=New-AzureVMConfig -Name azfae-use-vm-app01 -InstanceSize Large -ImageName $image -AvailabilitySetName azfae-use-as-app
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password 
	$vm1 | Set-AzureSubnet -SubnetNames BackEnd
	$vm1 | Add-AzureEndpoint -Name App1 -Protocol tcp -LocalPort 80 -PublicPort 80 -LBSetName "AppSet" -InternalLoadBalancerName "AppTierILB" -DefaultProbe
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 500 -DiskLabel AppFiles –LUN 0 -HostCaching None
	New-	AzureVM –ServiceName $ServiceName -VMs $vm1 -VNetName AZFAE-USE-VN01
	
	#Create the second application server 
	$cred1=Get-Credential –Message "Type the name and password of the local administrator account for the second application server."
	$vm1=New-AzureVMConfig -Name azfae-use-vm-app02 -InstanceSize Large -ImageName $image -AvailabilitySetName azfae-use-as-app
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password 
	$vm1 | Add-AzureEndpoint -Name App2 -Protocol tcp -LocalPort 80 -PublicPort 80 -LBSetName "AppSet" -InternalLoadBalancerName "AppTierILB" -DefaultProbe
	$vm1 | Set-AzureSubnet -SubnetNames BackEnd
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 500 -DiskLabel AppFiles –LUN 0 -HostCaching None
	New-AzureVM –ServiceName $ServiceName -VMs $vm1 -VNetName AZFAE-USE-VN01
	
	#Specify the premium storage account for the SQL Server cluster
	Set-AzureSubscription –SubscriptionName "Contoso Enterprise Subscription" -CurrentStorageAccountName "contosoazfaeusesasqlclust"
	
	#Create the majority node witness server for the SQL Server cluster
	$cred1=Get-Credential –Message "Type the name and password of the local administrator account for the majority node witness server."
	$vm1=New-AzureVMConfig -Name azfae-use-vm-sqlmn01 -InstanceSize Medium -ImageName $image -AvailabilitySetName azfae-use-as-sql
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password 
	$vm1 | Set-AzureSubnet -SubnetNames BackEnd
	New-AzureVM –ServiceName $ServiceName -VMs $vm1 -VNetName AZFAE-USE-VN01
	
	#Change the image string for the latest version of the SQL Server 2014 image in the gallery
	$image= Get-AzureVMImage | where { $_.ImageFamily -eq "SQL Server 2014 RTM Standard on Windows Server 2012 R2" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	
	#Create the first SQL Server
	$cred1=Get-Credential –Message "Type the name and password of the local administrator account for the first SQL Server."
	$vm1=New-AzureVMConfig -Name azfae-use-vm-sql01 -InstanceSize A5 -ImageName $image  -AvailabilitySetName azfae-use-as-sql
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password
	$vm1 | Set-AzureSubnet -SubnetNames BackEnd
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1000 -DiskLabel SQLFiles –LUN 0 -HostCaching None
	New-AzureVM –ServiceName $ServiceName -VMs $vm1 -VNetName AZFAE-USE-VN01
	
	#Create the second SQL Server
	$cred1=Get-Credential –Message "Type the name and password of the local administrator account for the second SQL Server."
	$vm1=New-AzureVMConfig -Name azfae-use-vm-sql02 -InstanceSize A5 -ImageName $image  -AvailabilitySetName azfae-use-as-sql
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password
	$vm1 | Set-AzureSubnet -SubnetNames BackEnd
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1000 -DiskLabel SQLFiles –LUN 0 -HostCaching None
	New-AzureVM –ServiceName $ServiceName -VMs $vm1 -VNetName AZFAE-USE-VN01

## Risorse aggiuntive

[Limiti, quote e vincoli delle sottoscrizioni e dei servizi Microsoft Azure](azure-subscription-service-limits.md#storage-limits)

[Dimensioni delle macchine virtuali e dei servizi cloud per Azure](https://msdn.microsoft.com/library/azure/dn197896.aspx)

[Obiettivi di scalabilità e prestazioni per Archiviazione di Azure](storage/storage-scalability-targets.md)

<!--HONumber=54-->