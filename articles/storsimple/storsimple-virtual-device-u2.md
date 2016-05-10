<properties 
   pageTitle="Dispositivo virtuale StorSimple Update 2 | Microsoft Azure"
   description="Informazioni su come creare, distribuire e gestire un dispositivo virtuale StorSimple in una rete virtuale di Microsoft Azure. (Si applica a StorSimple Update 2)."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/29/2016"
   ms.author="alkohli" />

# Distribuire e gestire un dispositivo virtuale StorSimple in Azure


##Panoramica
Il dispositivo virtuale StorSimple serie 8000 rappresenta una funzionalità aggiuntiva fornita con la soluzione Microsoft Azure StorSimple. Il dispositivo virtuale StorSimple viene eseguito in una macchina virtuale della rete virtuale di Microsoft Azure e può essere utilizzato per eseguire il backup e duplicare i dati dagli host. Questa esercitazione descrive come distribuire e gestire un dispositivo virtuale in Azure ed è applicabile a tutti i dispositivi virtuali che eseguono la versione del software Update 2 e precedenti.


#### Confronto tra modelli di dispositivi virtuali

Il dispositivo virtuale StorSimple è disponibile in due modelli, Standard 8010 (in precedenza noto come 1100) e Premium 8020 (introdotto nell'aggiornamento 2). La tabella seguente illustra un confronto tra i due modelli.


| Modello del dispositivo | 8010<sup>1</sup> | 8020 |
|-----------------------|---------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------|
| **Capacità massima** | 30 TB | 64 TB |
| **Macchina virtuale di Azure** | Standard\_A3 (4 core, 7 GB di memoria) | Standard\_DS3 (4 core, 14 GB di memoria) |
| **Compatibilità tra le versioni** | Versioni con aggiornamenti precedenti a Update 2 o successivi | Versioni con aggiornamenti Update 2 o successivi |
| **Aree di disponibilità** | Tutte le aree di Azure | Aree di Azure che supportano l'archiviazione Premium<br></br>Per un elenco delle aree, vedere [Aree supportate per il modello 8020](#supported-regions-for-8020) |
| **Tipo di archiviazione** | Usa l'Archiviazione Standard di Azure<br></br> Altre informazioni su come [creare un account di archiviazione Standard]() | Usa l'Archiviazione Standard di Azure<sup>2</sup> <br></br>Altre informazioni su come [creare un account di archiviazione Premium](storage-premium-storage.md#create-and-use-a-premium-storage-account-for-a-virtual-machine-data-disk) |
| **Indicazioni relative al carico di lavoro** | Recupero a livello di elemento per i file dai backup | Scenari di sviluppo e test cloud, bassa latenza, carichi di lavoro a prestazioni superiori <br></br>Dispositivo secondario per il ripristino di emergenza |
 
<sup>1</sup> *precedentemente noto come 1100*.

<sup>2</sup> *Entrambi i modelli 8010 e 8020 usano l'archiviazione di Azure Standard per il livello cloud. La differenza è solo nel livello locale nel dispositivo*.

#### Aree supportate per il modello 8020

Le aree di archiviazione Premium attualmente supportate per il modello 8020 sono elencate nella tabella seguente. L'elenco verrà aggiornato in base all'aumento delle aree in cui è disponibile l'archiviazione Premium.

| N. | Attualmente supportato nelle aree |
|---------------------------------------------------------|--------------------------------|
| 1 | Stati Uniti centrali |
| 2 | Stati Uniti Orientali |
| 3 | Stati Uniti orientali 2 |
| 4 | Stati Uniti occidentali |
| 5 | Europa settentrionale |
| 6 | Europa occidentale |
| 7 | Asia sudorientale |
| 8 | Giappone orientale |
| 9 | Giappone occidentale |
| 10 | Australia orientale |
| 11 | Australia sud-orientale* |
| 12 | Asia orientale* |
| 13 | Stati Uniti centro-meridionali* |

**L'archiviazione Premium è stata lanciata di recente in queste aree geografiche.

Questo articolo illustra il processo dettagliato per la distribuzione di un dispositivo virtuale StorSimple in Azure. Dopo avere letto l'articolo, si sarà in grado di:

- Comprendere le differenze tra il dispositivo virtuale e il dispositivo fisico.

- Creare e configurare il dispositivo virtuale.

- Connettersi al dispositivo virtuale.

- Usare il dispositivo virtuale.

Questa esercitazione si applica a tutti i dispositivi virtuali StorSimple con versione Update 2 e successiva.

## Differenze tra il dispositivo virtuale e il dispositivo fisico

Il dispositivo virtuale StorSimple consiste in una versione esclusivamente software di StorSimple che viene eseguita in un singolo nodo di una macchina virtuale di Microsoft Azure. Il dispositivo virtuale supporta il ripristino di emergenza nei casi in cui il dispositivo fisico non sia disponibile e può essere usato in scenari di recupero a livello di elemento dai backup, ripristino di emergenza locale e negli scenari di sviluppo e test basati su cloud.

#### Differenze rispetto al dispositivo fisico

La tabella seguente illustra alcune differenze fondamentali tra il dispositivo StorSimple virtuale e quello fisico.

| | Dispositivo fisico | Dispositivo virtuale |
|-----------------------------|----------------------------------------------------------|-------------------------------------------------------------------------------------------|
| **Posizione** | Si trova nel data center. | Viene eseguito in Azure. |
| **Interfacce di rete** | Ha sei interfacce di rete: da DATA 0 a DATA 5. | Ha una sola interfaccia di rete: DATA 0. |
| **Registrazione** | Registrato durante il passaggio di configurazione. | La registrazione è un'attività separata. |
| **Chiave DEK del servizio** | Rigenerare la chiave nel dispositivo fisico, quindi aggiornare il dispositivo virtuale con la nuova chiave. | Non è possibile rigenerarla dal dispositivo virtuale. |


## Prerequisiti per il dispositivo virtuale

Le sezioni seguenti illustrano i prerequisiti di configurazione per il dispositivo StorSimple virtuale. Prima di distribuire un dispositivo virtuale, esaminare le [considerazioni relative alla sicurezza per l'uso di un dispositivo virtuale](storsimple-security.md#storsimple-virtual-device-security).

#### Requisiti di Azure

Prima di eseguire il provisioning del dispositivo virtuale, è necessario effettuare le seguenti operazioni preliminari in Azure:

- Nel caso del dispositivo virtuale, [configurare una rete virtuale in Azure](../virtual-network/virtual-networks-create-vnet-classic-portal.md). Se si usa l'archiviazione Premium, sarà necessario creare una rete virtuale in un'area di Azure che supporta l'archiviazione Premium. Altre informazioni sulle [aree attualmente supportate per il modello 8020](#supported-regions-for-8020).
- È consigliabile utilizzare il server DNS predefinito fornito da Azure invece di specificare il nome del proprio server DNS. Se il nome del server DNS non è valido o se il server DNS non riesce a risolvere correttamente l'indirizzo IP, non sarà possibile creare il dispositivo virtuale.
- Le opzioni point-to-site e da sito a sito non sono obbligatorie, ma facoltative. Se si desidera, è possibile configurarle per scenari più avanzati. 
- È possibile creare[Macchine virtuali di Azure](../virtual-machines/virtual-machines-linux-about.md)(server host) nella rete virtuale che possono usare i volumi esposti dal dispositivo virtuale. Tali server devono soddisfare i seguenti requisiti: 							
	- Svolgere il ruolo di macchine virtuali Windows o Linux nelle quali è installato il software iSCSI Initiator
	- Essere in esecuzione nella stessa rete virtuale del dispositivo virtuale
	- Essere in grado di connettersi alla destinazione iSCSI del dispositivo virtuale utilizzando l'indirizzo IP interno del dispositivo virtuale

- Assicurarsi di aver configurato il supporto per il traffico iSCSI e cloud nella stessa rete virtuale.


#### Requisiti di StorSimple

Effettuare i seguenti aggiornamenti del servizio StorSimple di Azure, prima di creare un dispositivo virtuale:


- Aggiungere i [record controllo di accesso](storsimple-manage-acrs.md) per le macchine virtuali che svolgeranno il ruolo di server host per il dispositivo virtuale.

- Usare un [account di archiviazione](storsimple-manage-storage-accounts.md#add-a-storage-account) nella stessa area del dispositivo virtuale. Gli account di archiviazione posti in aree diverse possono causare una riduzione delle prestazioni. È possibile usare un account di archiviazione Standard o Premium con il dispositivo virtuale. Altre informazioni sulla creazione di un [account di archiviazione Standard]() o un [account di archiviazione Premium](storage-premium-storage.md#create-and-use-a-premium-storage-account-for-a-virtual-machine-data-disk)

- Usare un account di archiviazione differente rispetto a quello usato per creare i dati. Se si utilizza lo stesso account di archiviazione, si potrebbe verificare una riduzione delle prestazioni.

Prima di iniziare, verificare quanto segue:

- L'account del portale di Azure classico con le credenziali di accesso.

- Copia della chiave DEK del servizio ottenuta dal dispositivo fisico.


## Creare e configurare il dispositivo virtuale

Prima di eseguire queste procedure, assicurarsi che siano soddisfatti i [prerequisiti per il dispositivo virtuale](#prerequisites-for-the-virtual-device).

Dopo aver creato una rete virtuale, configurato un servizio StorSimple Manager e registrato il dispositivo StorSimple fisico nel servizio, è possibile seguire questa procedura per creare e configurare un dispositivo virtuale StorSimple.

### Passaggio 1: Creare un dispositivo virtuale

Per creare il dispositivo virtuale StorSimple, eseguire la procedura riportata di seguito:

[AZURE.INCLUDE [Creare un dispositivo virtuale](../../includes/storsimple-create-virtual-device-u2.md)]


### Passaggio 2: Configurare e registrare il dispositivo virtuale

Prima di iniziare questa procedura, assicurarsi di avere una copia della chiave DEK del servizio. La chiave DEK del servizio è stata creata durante la procedura di configurazione del primo dispositivo StorSimple ed è stata salvata in un luogo sicuro. Se non si dispone di una copia della chiave DEK del servizio, è necessario contattare il supporto tecnico Microsoft e richiedere assistenza.

Per configurare e registrare il dispositivo virtuale StorSimple, eseguire la procedura riportata di seguito.[AZURE.INCLUDE [Configurare e registrare il dispositivo virtuale](../../includes/storsimple-configure-register-virtual-device.md)]

### Passaggio 3: (Facoltativo) Modificare le impostazioni di configurazione del dispositivo

La sezione seguente illustra le impostazioni di configurazione del dispositivo necessarie per il dispositivo StorSimple virtuale se si vuole usare CHAP, StorSimple Snapshot Manager o si vuole modificare la password dell'amministratore del dispositivo.

#### Configurare l'iniziatore dell'autenticazione CHAP

Questo parametro include le credenziali che il dispositivo virtuale (destinazione) prevede di ricevere dagli iniziatori (server) che tentano di accedere ai volumi. Gli iniziatori forniranno un nome utente e una password CHAP per identificarsi nel dispositivo, durante questa procedura di autenticazione. Per informazioni dettagliate sulla procedura, vedere [Configurare CHAP per il dispositivo StorSimple](storsimple-configure-chap.md#unidirectional-or-one-way-authentication).

#### Configurare la destinazione dell'autenticazione CHAP

Questo parametro contiene le credenziali utilizzate dal dispositivo virtuale quando un iniziatore abilitato all'autenticazione CHAP richiede l'autenticazione reciproca o bidirezionale. Il dispositivo virtuale utilizzerà un nome utente e una password CHAP inversi per identificarsi nell'iniziatore durante la procedura di autenticazione. Tenere presente che le impostazioni di destinazione CHAP sono di tipo globale. Quando vengono applicate, tutti i volumi connessi al dispositivo virtuale di archiviazione utilizzano l'autenticazione CHAP. Per informazioni dettagliate sulla procedura, vedere [Configurare CHAP per il dispositivo StorSimple](storsimple-configure-chap.md#bidirectional-or-mutual-authentication).

#### Configurare la password di Gestione snapshot StorSimple

Il software di Gestione snapshot StorSimple si trova nell'host di Windows e consente agli amministratori di gestire i backup del dispositivo StorSimple sotto forma di snapshot locali e cloud.

>[AZURE.NOTE] Per il dispositivo virtuale, l'host di Windows è costituito da una macchina virtuale di Azure.

Quando si configura un dispositivo in StorSimple Snapshot Manager, verrà richiesto di specificare l'indirizzo IP del dispositivo StorSimple e la password per autenticare il dispositivo di archiviazione. Per informazioni dettagliate sulla procedura, vedere [Configurare la password di StorSimple Snapshot Manager](storsimple-change-passwords.md#change-the-storsimple-snapshot-manager-password).

#### Configurare la password dell’amministratore del dispositivo 

Quando si utilizza l'interfaccia di Windows PowerShell per accedere al dispositivo virtuale, è necessario immettere una password amministratore. Per garantire la sicurezza dei dati, è necessario modificare la password prima di utilizzare il dispositivo virtuale. Per informazioni dettagliate sulla procedura, vedere [Configurare la password dell'amministratore del dispositivo](storsimple-change-passwords.md#change-the-device-administrator-password).

## Connettersi in modalità remota al dispositivo virtuale.
Per impostazione predefinita, non è abilitato l'accesso remoto al dispositivo virtuale tramite l'interfaccia di Windows PowerShell. È necessario abilitare innanzitutto la gestione remota nel dispositivo virtuale e abilitarla nel client che verrà utilizzato per accedere al dispositivo virtuale.

Il processo in due passaggi per la connessione remota è illustrato di seguito.

### Passaggio 1: Configurare la gestione remota

Per configurare la gestione remota del dispositivo virtuale StorSimple, eseguire la procedura riportata di seguito.

[AZURE.INCLUDE [Configurare la gestione remota tramite HTTP per il dispositivo virtuale](../../includes/storsimple-configure-remote-management-http-device.md)]

### Passaggio 2: Accedere al dispositivo virtuale in modalità remota

Dopo avere abilitato la gestione remota nella pagina di configurazione del dispositivo StorSimple, è possibile usare la comunicazione remota di PowerShell per connettersi al dispositivo virtuale da un'altra macchina virtuale presente all'interno della stessa rete virtuale. Ad esempio, è possibile connettersi dalla macchina virtuale host configurata e usata per connettere iSCSI. Nella maggior parte delle distribuzioni, l'utente avrà già aperto un endpoint pubblico per accedere alla macchina virtuale che è possibile utilizzare per accedere al dispositivo virtuale.

>[AZURE.WARNING] **Per una maggiore sicurezza, si consiglia di utilizzare HTTPS quando ci si connette agli endpoint e di eliminare gli endpoint dopo aver completato la sessione remota in PowerShell.**

Eseguire le procedure descritte in [Connettersi in remoto al dispositivo StorSimple](storsimple-remote-connect.md) per impostare la connessione in remoto al dispositivo virtuale.

## Connettersi direttamente al dispositivo virtuale

È anche possibile connettersi direttamente al dispositivo virtuale. Se ci si vuole connettere direttamente al dispositivo virtuale da un altro computer non compreso nella rete virtuale oppure nell'ambiente Microsoft Azure, è necessario creare endpoint aggiuntivi, come descritto nella procedura seguente.

Per creare un endpoint pubblico nel dispositivo virtuale StorSimple, eseguire la procedura riportata di seguito:

[AZURE.INCLUDE [Creare endpoint pubblici in un dispositivo virtuale](../../includes/storsimple-create-public-endpoints-virtual-device.md)]

Si consiglia di connettersi da un'altra macchina virtuale presente all'interno della stessa rete virtuale; in questo modo si riduce il numero di endpoint pubblici nella rete virtuale. Quando si utilizza questo metodo, ci si connette alla macchina virtuale tramite una sessione Desktop remoto e si configura tale macchina virtuale affinché venga utilizzata come qualsiasi altro client di Windows in una rete locale. Non è necessario aggiungere il numero di porta pubblica poiché è già noto.

## Utilizzare il dispositivo virtuale StorSimple

Dopo aver creato e configurato il dispositivo virtuale StorSimple, è possibile iniziare a utilizzarlo. È possibile utilizzare contenitori di volumi, criteri per volumi e backup su un dispositivo virtuale esattamente come si farebbe con un dispositivo StorSimple fisico; l'unica differenza consiste nella necessità di selezionare il dispositivo virtuale dall'elenco dei dispositivi. Per procedure dettagliate per le diverse attività di gestione per il dispositivo virtuale, vedere la pagina relativa all'[uso del servizio StorSimple Manager per la gestione di un dispositivo virtuale](storsimple-manager-service-administration.md).

Le sezioni seguenti illustrano alcune delle differenze relative all'utilizzo del dispositivo virtuale.

### Gestire un dispositivo virtuale StorSimple

Dal momento che si tratta di un dispositivo solo software, la gestione del dispositivo virtuale è minore rispetto a quella di un dispositivo fisico. L'utente ha a disposizione le seguenti opzioni:

- **Aggiornamenti software**: è possibile visualizzare la data dell'ultimo aggiornamento del software insieme a eventuali messaggi sullo stato dell'aggiornamento. È possibile usare il pulsante **Verifica la disponibilità di aggiornamenti** nella parte inferiore della pagina per eseguire una ricerca manuale di nuovi aggiornamenti. Se sono disponibili aggiornamenti, fare clic su **Installa aggiornamenti** per eseguire l'installazione. Dal momento che non esiste un'unica interfaccia sul dispositivo virtuale, durante l'applicazione degli aggiornamenti si verificherà una breve interruzione del servizio. Il dispositivo virtuale verrà automaticamente arrestato e riavviato (se necessario) per applicare gli aggiornamenti disponibili. Per una procedura dettagliata, vedere la pagina relativa all'[aggiornamento del dispositivo](storsimple-update-device.md#install-regular-updates-via-the-azure-classic-portal).
- **Pacchetto di supporto**: è possibile creare e caricare un pacchetto di supporto per consentire al supporto tecnico Microsoft di risolvere i problemi relativi al dispositivo virtuale. Per una procedura dettagliata, vedere [Creare e gestire un pacchetto di supporto StorSimple](storsimple-create-manage-support-package.md).

### Account di archiviazione di un dispositivo virtuale

Gli account di archiviazione vengono creati per essere utilizzati dal servizio StorSimple Manager, dal dispositivo virtuale e da quello fisico. Quando si crea un account di archiviazione, è opportuno utilizzare un identificatore di area con nome descrittivo, in modo che l'area sia coerente in tutti i componenti di sistema. È importante che tutti i componenti di un dispositivo virtuale si trovino nella stessa area, al fine di evitare problemi di prestazioni.

Per una procedura dettagliata, vedere [Aggiungere un account di archiviazione](storsimple-manage-storage-accounts.md#add-a-storage-account).

### Disattivare un dispositivo virtuale StorSimple

Se si disattiva un dispositivo virtuale, vengono eliminate la macchina virtuale e le risorse create durante l'esecuzione del provisioning. Dopo aver disattivato il dispositivo, non è possibile ripristinarne lo stato precedente. Prima di disattivare il dispositivo virtuale, assicurarsi di arrestare o eliminare i client e gli host che dipendono da esso.

La disattivazione di un dispositivo virtuale determina le azioni seguenti:

- Il dispositivo virtuale viene rimosso.

- Il disco del sistema operativo e i dischi dati creati per il dispositivo virtuale vengono rimossi.

- Il servizio ospitato e la rete virtuale creata durante il provisioning vengono mantenuti. Se non si utilizzano, è necessario eliminarli manualmente.

- Le snapshot cloud create per il dispositivo virtuale vengono conservate.

Per una procedura dettagliata, vedere [Disattivare ed eliminare un dispositivo StorSimple](storsimple-deactivate-and-delete-device.md).

Non appena il dispositivo virtuale viene indicato come disattivato nella pagina del servizio StorSimple Manager, è possibile eliminare il dispositivo virtuale dall'elenco dispositivi nella pagina **Dispositivi**.


### Avviare, arrestare e riavviare un dispositivo virtuale
A differenza del dispositivo StorSimple fisico, in quello virtuale StorSimple non sono disponibili pulsanti di accensione o spegnimento. Tuttavia, in alcuni casi potrebbe essere necessario arrestare e riavviare il dispositivo virtuale. Ad esempio, per completare l'applicazione di alcuni aggiornamenti potrebbe essere richiesto di riavviare la macchina virtuale. Il modo più semplice per avviare, arrestare e riavviare un dispositivo virtuale consiste nell'utilizzare la console di gestione delle macchine virtuali.

Quando si apre la console di gestione, il dispositivo virtuale viene visualizzato in **esecuzione** poiché viene avviato per impostazione predefinita al termine della procedura di creazione. È possibile avviare, arrestare e riavviare una macchina virtuale in qualsiasi momento.

[AZURE.INCLUDE [Arrestare e riavviare un dispositivo virtuale](../../includes/storsimple-stop-restart-virtual-device.md)]

### Ripristino delle impostazioni di fabbrica

Se si decide di ripristinare il dispositivo virtuale, sarà sufficiente disattivarlo, eliminarlo e successivamente crearne uno nuovo. Analogamente a quanto accade per il ripristino del dispositivo fisico, nel nuovo dispositivo virtuale non saranno presenti aggiornamenti. Pertanto, assicurarsi di cercare gli aggiornamenti prima di utilizzarlo.


## Eseguire il failover nel dispositivo virtuale

Il ripristino di emergenza (DR, Disaster Recovery) rappresenta una delle situazioni principali per le quali il dispositivo è stato realizzato. In questo scenario, è possibile che il dispositivo fisico StorSimple o il data center completo non sia disponibile. Fortunatamente, è possibile utilizzare un dispositivo virtuale per ripristinare le operazioni in un percorso alternativo. Durante il ripristino di emergenza, i contenitori del volume del dispositivo di origine cambiano la proprietà e vengono trasferiti nel dispositivo virtuale. I prerequisiti per il ripristino di emergenza prevedono che sia stato creato e configurato il dispositivo virtuale, che tutti i volumi nel contenitore siano offline e che al contenitore del volume sia associata una snapshot cloud.

>[AZURE.NOTE] 
>
> - Quando si usa un dispositivo virtuale come dispositivo secondario per il ripristino di emergenza, occorre ricordare che 8010 ha 30 TB di archiviazione Standard e 8020 ha 64 TB di archiviazione Premium. La capacità superiore del dispositivo virtuale 8020 potrebbe essere più adatta per uno scenario di ripristino di emergenza.
> - Non è possibile eseguire il failover o la clonazione da un dispositivo con versione del software con Update 2 a un dispositivo con una versione precedente a Update 1. È tuttavia possibile eseguire il failover di un dispositivo che esegue la versione con Update 2 in un dispositivo con Update 1 (1.1 o 1.2)

Per una procedura dettagliata, vedere [Failover su un dispositivo virtuale StorSimple](storsimple-device-failover-disaster-recovery.md#fail-over-to-a-storsimple-virtual-device).
 

## Arrestare o eliminare il dispositivo virtuale

Se in precedenza è stato configurato e utilizzato un dispositivo virtuale StorSimple, ma si desidera arrestare l'addebito dei costi di calcolo relativi all'utilizzo, è possibile arrestare il dispositivo virtuale. Se si arresta il dispositivo virtuale, non viene eliminato il sistema operativo o i dischi dati nell'archiviazione. Inoltre, viene interrotto l'addebito di costi di sottoscrizione, ma non quello dei costi per il sistema operativo e per i dischi dati.

Se si elimina o si arresta il dispositivo virtuale, quest'ultimo verrà visualizzato come **Offline** nella pagina Dispositivi del servizio StorSimple Manager. È possibile disattivare o eliminare il dispositivo se si vogliono eliminare anche i backup creati dal dispositivo virtuale. Per altre informazioni, vedere [Disattivare ed eliminare un dispositivo StorSimple](storsimple-deactivate-and-delete-device.md).

[AZURE.INCLUDE [Arrestare un dispositivo virtuale](../../includes/storsimple-shutdown-virtual-device.md)]

[AZURE.INCLUDE [Eliminare un dispositivo virtuale](../../includes/storsimple-delete-virtual-device.md)]

   

## Passaggi successivi

- Informazioni su come [Usare il servizio StorSimple Manager per amministrare il dispositivo StorSimple](storsimple-manager-service-administration.md).
 
- Informazioni su come [Ripristinare un volume StorSimple da un set di backup](storsimple-restore-from-backup-set.md).

<!---HONumber=AcomDC_0504_2016-->