<properties
   pageTitle="Dispositivo virtuale StorSimple in Azure | Microsoft Azure"
   description="Informazioni su come creare, distribuire e gestire un dispositivo virtuale StorSimple in una rete virtuale di Microsoft Azure. (Si applica alla versione 3 di StorSimple e alle versioni precedenti.)"
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carolz"
   editor="" />
<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/16/2015"
   ms.author="alkohli" />

# Distribuire e gestire un dispositivo virtuale StorSimple in Azure

[AZURE.INCLUDE [storsimple-version-selector-sva](../../includes/storsimple-version-selector-sva.md)]

## Panoramica

Il dispositivo virtuale StorSimple rappresenta una funzionalità aggiuntiva fornita con la soluzione Microsoft Azure StorSimple. Il dispositivo virtuale StorSimple viene eseguito in una macchina virtuale della rete virtuale di Microsoft Azure e può essere utilizzato per eseguire il backup e duplicare i dati dagli host. Gli argomenti seguenti consentono di ottenere ulteriori informazioni, configurare e utilizzare il dispositivo virtuale StorSimple.

- Differenze tra il dispositivo virtuale e il dispositivo fisico.

- Considerazioni relative alla sicurezza di utilizzo di un dispositivo virtuale.

- Prerequisiti per il dispositivo virtuale.

- Creare e configurare il dispositivo virtuale.

- Utilizzare il dispositivo virtuale.

- Eseguire il failover nel dispositivo virtuale.

- Arrestare o eliminare il dispositivo virtuale.


## Differenze tra il dispositivo virtuale e il dispositivo fisico

Il dispositivo virtuale StorSimple consiste in una versione esclusivamente software di StorSimple che viene eseguita in un singolo nodo di una macchina virtuale di Microsoft Azure. Il dispositivo virtuale supporta il ripristino di emergenza nei casi in cui il dispositivo fisico non sia disponibile. Tale funzionalità deve essere usata in scenari di sviluppo e test basati su cloud.

Di seguito sono riportate alcune differenze fondamentali tra il dispositivo StorSimple virtuale e quello fisico:

- Il dispositivo virtuale ha una sola interfaccia di rete: DATA 0. Il dispositivo fisico dispone di sei interfacce di rete: da DATA 0 a DATA 5.
- Il dispositivo virtuale viene registrato durante la procedura di configurazione, senza dover eseguire un'apposita operazione.
- Da un dispositivo virtuale non è possibile rigenerare la chiave DEK (Data Encryption Key) del servizio. Durante il rollover della chiave, l'utente rigenera la chiave nel dispositivo fisico, quindi aggiorna il dispositivo virtuale con la nuova chiave.
- Qualora fosse necessario applicare gli aggiornamenti al dispositivo virtuale, quest'ultimo sarà inattivo per un breve periodo. Tale inattività non interessa il dispositivo fisico.

## Considerazioni relative alla sicurezza di utilizzo di un dispositivo virtuale

Quando si utilizza il dispositivo virtuale StorSimple, tenere presenti le seguenti considerazioni sulla sicurezza:

- La sottoscrizione a Microsoft Azure consente di proteggere il dispositivo virtuale. Ciò significa che se l'utente utilizza il dispositivo virtuale in presenza di una sottoscrizione ad Azure compromessa, anche i dati memorizzati nel dispositivo virtuale possono essere vulnerabili.

- La chiave pubblica del certificato utilizzato per crittografare i dati archiviati in Azure StorSimple viene resa disponibile per il portale Azure; la chiave privata viene conservata nel dispositivo StorSimple. Nel dispositivo virtuale StorSimple le chiavi pubbliche e private vengono archiviate entrambe in Azure.

- Il dispositivo virtuale è ospitato nel data center di Microsoft Azure.


## Prerequisiti per il dispositivo virtuale

Nelle sezioni seguenti vengono riportate informazioni su come iniziare a usare il dispositivo virtuale StorSimple.

### Requisiti di Azure

Prima di eseguire il provisioning del dispositivo virtuale, è necessario effettuare le seguenti operazioni preliminari in Azure:

- Nel caso del dispositivo virtuale, [configurare una rete virtuale in Azure](virtual-networks-create-vnet.md).
- È consigliabile utilizzare il server DNS predefinito fornito da Azure invece di specificare il nome del proprio server DNS. Se il nome del server DNS non è valido, la creazione del dispositivo virtuale avrà esito negativo.
- Le opzioni point-to-site e da sito a sito non sono obbligatorie, ma facoltative. Se si desidera, è possibile configurarle per scenari più avanzati.

>[AZURE.IMPORTANT]**Assicurarsi che la rete virtuale sia presente nella stessa area degli account di archiviazione cloud che verranno utilizzati con il dispositivo virtuale.**

- È possibile creare[Macchine virtuali di Azure](../virtual-machines/virtual-machines-about.md)(server host) nella rete virtuale che possono utilizzare i volumi esposti dal dispositivo virtuale. Tali server devono soddisfare i seguenti requisiti:
	- Svolgere il ruolo di macchine virtuali Windows o Linux nelle quali è installato il software iSCSI Initiator
	- Essere in esecuzione nella stessa rete virtuale del dispositivo virtuale
	- Essere in grado di connettersi alla destinazione iSCSI del dispositivo virtuale utilizzando l'indirizzo IP interno del dispositivo virtuale

- Assicurarsi di aver configurato il supporto per il traffico iSCSI e cloud nella stessa rete virtuale.

### Requisiti di StorSimple

Effettuare i seguenti aggiornamenti del servizio StorSimple di Azure, prima di creare un dispositivo virtuale:


- Aggiungere i [record controllo di accesso](storsimple-manage-acrs.md) per le macchine virtuali che svolgeranno il ruolo di server host per il dispositivo virtuale.

- Assicurarsi di disporre di un [account di archiviazione](storsimple-manage-storage-accounts.md#add-a-storage-account) nella stessa area del dispositivo virtuale. Gli account di archiviazione posti in aree diverse possono causare una riduzione delle prestazioni.

- Per creare un dispositivo virtuale, assicurarsi di utilizzare un account di archiviazione differente rispetto a quello utilizzato per creare i dati. Se si utilizza lo stesso account di archiviazione, si potrebbe verificare una riduzione delle prestazioni.

Prima di iniziare, verificare quanto segue:

- L'utente dispone delle credenziali di accesso relative all'account del portale di Azure.

- L'utente dispone delle credenziali di accesso relative all'account di archiviazione di Azure.

- L'utente dispone di una copia della chiave DEK del servizio ottenuta dal dispositivo fisico.

- L'utente dispone di una chiave di crittografia del servizio cloud per ogni contenitore del volume.


## Creare e configurare il dispositivo virtuale

Prima di eseguire queste procedure, assicurarsi che siano soddisfatti i [prerequisiti per il dispositivo virtuale](#prerequisites-for-the-virtual-device).

Dopo aver completato queste procedure, è possibile [utilizzare il dispositivo virtuale](#work-with-the-storsimple-virtual-device).

### Creare il dispositivo virtuale

Dopo aver creato una rete virtuale, configurato un servizio StorSimple Manager e registrato il dispositivo StorSimple fisico con il servizio, è possibile effettuare la procedura seguente e creare un dispositivo virtuale StorSimple.

Per creare il dispositivo virtuale StorSimple, eseguire la procedura riportata di seguito:

1.  Nel portale di Azure, accedere al servizio **StorSimple Manager**.

2. Visitare la pagina **Dispositivi**.

3. Nella finestra di dialogo **Crea dispositivo virtuale**, indicare quanto segue.

	![StorSimple crea dispositivo virtuale](./media/storsimple-virtual-device/StorSimple_CreateVirtualDevice1.png)

	1. **Nome**: un nome univoco per il dispositivo virtuale.

	2. **Rete virtuale**: il nome della rete virtuale che si desidera utilizzare con il dispositivo virtuale.

	3. **Subnet**: la subnet nella rete virtuale da utilizzare con il dispositivo virtuale.

	4. **Account di archiviazione per creare un dispositivo virtuale**: l'account di archiviazione che verrà utilizzato per conservare l'immagine del dispositivo virtuale durante il provisioning. Questo account di archiviazione deve trovarsi nella stessa area del dispositivo virtuale e della rete virtuale. Inoltre, non deve essere utilizzato dal dispositivo fisico o dal dispositivo virtuale per archiviare i dati. Per effettuare questa operazione, verrà creato un nuovo account di archiviazione (impostazione predefinita). Tuttavia, se già si dispone di un account di archiviazione adatto a eseguire l'operazione, selezionarlo dall'elenco.

4. Fare clic sul segno di spunta per indicare di aver compreso che i dati archiviati nel dispositivo virtuale verranno ospitati in un datacenter Microsoft. Verrà creata la macchina virtuale. Potrebbero occorrere fino a 45 minuti o 1 ora per creare un dispositivo virtuale.![Dispositivo virtuale StorSimple fase di creazione](./media/storsimple-virtual-device/StorSimple_VirtualDeviceCreating1M.png)

Quando si utilizza soltanto un dispositivo fisico, la chiave di crittografia viene conservata nel dispositivo; di conseguenza, Microsoft non può decrittografarli. Quando si utilizza un dispositivo virtuale, sia la chiave di crittografia che la chiave di decrittografia vengono archiviate in Microsoft Azure. Per ulteriori informazioni, vedere [Considerazioni relative alla sicurezza di utilizzo di un dispositivo virtuale](#security-considerations-for-using-a-virtual-device).

### Configurare e registrare il dispositivo virtuale

Prima di iniziare questa procedura, assicurarsi di avere una copia della chiave DEK del servizio. La chiave DEK del servizio è stata creata durante la procedura di configurazione del primo dispositivo fisico StorSimple ed sono state fornite istruzioni per salvarla in un luogo sicuro. Se non si dispone di una copia della chiave DEK del servizio, è necessario [contattare il supporto tecnico Microsoft](storsimple-contact-microsoft-support.md) e richiedere assistenza.

Per configurare e registrare il dispositivo virtuale StorSimple, eseguire la procedura riportata di seguito.


1. Selezionare il**dispositivo virtuale StorSimple**appena creato nella pagina **Dispositivi**.

2. Fare clic su **Completa la configurazione del dispositivo**. Verrà avviata la configurazione guidata del dispositivo.

	![Installazione completa del dispositivo StorSimple nella pagina dei dispositivi](./media/storsimple-virtual-device/StorSimple_CompleteDeviceSetupSVA1M.png)

1. Nella configurazione guidata del dispositivo:

	1. Immettere la **chiave DEK del servizio** nell'apposito spazio.
	2. Immettere la**password di gestione Snapshot**. La **password di gestione Snapshot** deve essere composta da 14 o 15 caratteri e contenere una combinazione di lettere maiuscole, lettere minuscole, numeri e caratteri speciali.
	3. Immettere la**password amministratore del dispositivo**. La **password amministratore del dispositivo** deve avere una lunghezza compresa tra gli 8 e i 15 caratteri e contenere una combinazione di lettere maiuscole, lettere minuscole, numeri e caratteri speciali.
	4. Fare clic sul segno di spunta per completare la configurazione e la registrazione iniziali del dispositivo virtuale.

		![Impostazioni del dispositivo virtuale StorSimple](./media/storsimple-virtual-device/StorSimple_VirtualDeviceSettings1.png)

Dopo aver completato la configurazione e la registrazione, il dispositivo viene portato online. Potrebbero occorrere alcuni minuti per questa operazione.

![Fase online dispositivo virtuale StorSimple](./media/storsimple-virtual-device/StorSimple_VirtualDeviceOnline1M.png)

### Modificare le impostazioni di configurazione del dispositivo

Nella sezione seguente vengono descritte le impostazioni di configurazione del dispositivo che devono essere configurate per il dispositivo virtuale StorSimple. Tali impostazioni includono quelle relative a CHAP, password gestione Snapshot StorSimple o password amministratore del dispositivo.

#### Configurare l'iniziatore dell'autenticazione CHAP (facoltativo)

Questo parametro include le credenziali che il dispositivo virtuale (destinazione) prevede di ricevere dagli iniziatori (server) che tentano di accedere ai volumi. Gli iniziatori forniranno un nome utente e una password CHAP per identificarsi nel dispositivo, durante questa procedura di autenticazione.

#### Configurare la destinazione CHAP (facoltativo)

Questo parametro contiene le credenziali utilizzate dal dispositivo virtuale quando un iniziatore abilitato all'autenticazione CHAP richiede l'autenticazione reciproca o bidirezionale. Il dispositivo virtuale utilizzerà un nome utente e una password CHAP inversi per identificarsi nell'iniziatore durante la procedura di autenticazione. Tenere presente che le impostazioni di destinazione CHAP sono di tipo globale. Quando vengono applicate, tutti i volumi connessi al dispositivo virtuale di archiviazione utilizzano l'autenticazione CHAP.

#### Configurare la password di StorSimple Snapshot Manager (facoltativo)

Il software di Gestione snapshot StorSimple si trova nell'host di Windows e consente agli amministratori di gestire i backup del dispositivo StorSimple sotto forma di snapshot locali e cloud.

>[AZURE.NOTE]Per il dispositivo virtuale, l'host di Windows è costituito da una macchina virtuale di Azure.

Quando si configura un dispositivo in StorSimple Snapshot Manager, verrà richiesto di specificare l'indirizzo IP del dispositivo StorSimple e la password per autenticare il dispositivo di archiviazione. Questa password viene configurata dapprima utilizzando l'interfaccia di Windows PowerShell.

Eseguire i passaggi seguenti per modificare StorSimple Snapshot Manager, quando viene utilizzato insieme al dispositivo virtuale StorSimple.

1. Nel dispositivo virtuale, andare a **Dispositivi > Configura**.

- Scorrere verso il basso fino alla sezione **Snapshot Manager**. Immettere una password composta da 14 o 15 caratteri. La password deve contenere una combinazione di lettere maiuscole, minuscole, numeri e caratteri speciali.

- Confermare la password.

- Fare clic su **Save** nella parte inferiore della pagina.

A questo punto, la password di Snapshot StorSimple Manager è aggiornata e può essere utilizzata quando si autenticano gli host di Windows.

#### Configurare la password amministratore del dispositivo

Quando si utilizza l'interfaccia di Windows PowerShell per accedere al dispositivo virtuale, è necessario immettere una password amministratore. Per garantire la sicurezza dei dati, è necessario modificare la password prima di utilizzare il dispositivo virtuale.

Per configurare la password amministratore del dispositivo virtuale StorSimple, eseguire la procedura riportata di seguito.

1. Nel dispositivo virtuale, andare a **Dispositivi > Configura**.

1. Scorrere verso il basso fino alla sezione **Password amministratore del dispositivo** Specificare una password amministratore contenente dagli 8 ai 15 caratteri. La password deve contenere una combinazione di lettere maiuscole, minuscole, numeri e caratteri speciali.

1. Confermare la password.

1. Fare clic su **Save** nella parte inferiore della pagina.

A questo punto, la password amministratore del dispositivo dovrebbe essere aggiornata. Tale password modificata deve essere utilizzata per accedere all'interfaccia di Windows PowerShell nel dispositivo virtuale.

#### Configurare la gestione remota (facoltativo)

Per impostazione predefinita, non è abilitato l'accesso remoto al dispositivo virtuale tramite l'interfaccia di Windows PowerShell. È necessario abilitare innanzitutto la gestione remota nel dispositivo virtuale e abilitarla nel client che verrà utilizzato per accedere al dispositivo virtuale.

È possibile scegliere di connettersi tramite HTTP o HTTPS. Per motivi di sicurezza, si consiglia di utilizzare HTTPS con un certificato autofirmato per connettersi al dispositivo virtuale.

Per configurare la gestione remota del dispositivo virtuale StorSimple, eseguire la procedura riportata di seguito.

1. Nel dispositivo virtuale, andare a **Dispositivi > Configura**.

2. Scorrere verso il basso fino alla sezione **Gestione remota**.

3. Impostare **Abilita la gestione remota** su **Sì**.

4. È ora possibile scegliere di connettersi tramite HTTP. La connessione tramite HTTPS rappresenta la scelta predefinita. La connessione tramite HTTP dovrebbe essere eseguita solo su reti attendibili.

5. Fare clic su **Scarica certificato di gestione remota** per scaricare un certificato di gestione remota. È necessario specificare il percorso in cui salvare il file. Tale certificato deve quindi essere installato nel computer client o host che si utilizzerà per eseguire la connessione al dispositivo virtuale.

6. Fare clic su **Save** nella parte inferiore della pagina.

![Video disponibile](./media/storsimple-virtual-device/Video_icon.png) **Video disponibile**

Per guardare un video che descrive come creare un dispositivo StorSimple virtuale nel cloud, fare clic [qui](http://azure.microsoft.com/documentation/videos/create-a-storsimple-virtual-device/).

## Utilizzare il dispositivo virtuale StorSimple

Dopo aver creato e configurato il dispositivo virtuale StorSimple, è possibile iniziare a utilizzarlo. È possibile utilizzare contenitori di volumi, criteri per volumi e backup su un dispositivo virtuale esattamente come si farebbe con un dispositivo StorSimple fisico; l'unica differenza consiste nella necessità di selezionare il dispositivo virtuale dall'elenco dei dispositivi. Fare riferimento alle sezioni seguenti per istruzioni sulle attività associate:


- [Contenitori di volume](storsimple-manage-volume-containers.md).

- [Volumi](storsimple-manage-volumes.md)

- [Criteri di backup](storsimple-manage-backup-policies.md)

Le sezioni seguenti illustrano alcune delle differenze relative all'utilizzo del dispositivo virtuale.

### Gestire un dispositivo virtuale StorSimple

Dal momento che si tratta di un dispositivo solo software, la gestione del dispositivo virtuale è minore rispetto a quella di un dispositivo fisico. L'utente ha a disposizione le seguenti opzioni:

- **Aggiornamenti software**: è possibile visualizzare la data dell'ultimo aggiornamento del software insieme a eventuali messaggi sullo stato dell'aggiornamento. È possibile utilizzare il pulsante di ricerca degli aggiornamenti nella parte inferiore della pagina per eseguire una ricerca di nuovi aggiornamenti.
- **Pacchetto di supporto**: è possibile creare e caricare un pacchetto di supporto per consentire al supporto tecnico Microsoft di risolvere i problemi relativi al dispositivo virtuale.

### Account di archiviazione di un dispositivo virtuale

Gli account di archiviazione vengono creati per essere utilizzati dal servizio StorSimple Manager, dal dispositivo virtuale e da quello fisico. Quando si crea un account di archiviazione, è opportuno utilizzare un identificatore di area con nome descrittivo, in modo che l'area sia coerente in tutti i componenti di sistema. È importante che tutti i componenti di un dispositivo virtuale si trovino nella stessa area, al fine di evitare problemi di prestazioni.

### Disattivare un dispositivo virtuale StorSimple

Se si disattiva un dispositivo virtuale, vengono eliminate la macchina virtuale e le risorse create durante l'esecuzione del provisioning. Dopo aver disattivato il dispositivo, non è possibile ripristinarne lo stato precedente. Prima di disattivare il dispositivo virtuale, assicurarsi di arrestare o eliminare i client e gli host che dipendono da esso.

La disattivazione di un dispositivo virtuale determina le azioni seguenti:

- Il dispositivo virtuale viene rimosso.

- L'OSDisk e i dischi dati creati per il dispositivo virtuale vengono rimossi.

- Il servizio ospitato e la rete virtuale creata durante il provisioning vengono mantenuti. Se non si utilizzano, è necessario eliminarli manualmente.

- Le snapshot cloud create per il dispositivo virtuale vengono conservate.

Non appena il dispositivo viene indicato come disattivato nella pagina del servizio StorSimple Manager, è possibile eliminare il dispositivo virtuale dall'elenco dispositivi del servizio StorSimple Manager.

### Accedere in remoto a un dispositivo virtuale StorSimple

Dopo averlo impostato nella pagina di configurazione dispositivo di StorSimple, è possibile utilizzare Windows PowerShell in remoto per connettersi al dispositivo virtuale da un'altra macchina virtuale presente all'interno della stessa rete virtuale; ad esempio, è possibile connettersi dalla macchina virtuale host configurata e utilizzata per connettere iSCSI. Nella maggior parte delle distribuzioni, l'utente avrà già aperto un endpoint pubblico per accedere alla macchina virtuale che è possibile utilizzare per accedere al dispositivo virtuale.

>[AZURE.WARNING]Per una maggiore sicurezza, si consiglia di utilizzare HTTPS quando ci si connette agli endpoint e di eliminare gli endpoint dopo aver completato la sessione remota in PowerShell.

E’ necessario eseguire le procedure descritte in [Connessione in remoto al dispositivo StorSimple](storsimple-remote-connect.md) per impostare la connessione in remoto al dispositivo virtuale.

Tuttavia, se si desidera connettersi direttamente al dispositivo virtuale da un altro computer non compreso nella rete virtuale oppure nell'ambiente di Microsoft Azure, è necessario creare endpoint aggiuntivi, come descritto nella procedura seguente.

Per creare un endpoint pubblico nel dispositivo virtuale StorSimple, eseguire la procedura riportata di seguito:

1. Accedere al portale di Azure.

- Fare clic su **Macchine virtuali**, quindi selezionare la macchina virtuale che viene utilizzata come dispositivo virtuale.

- Fare clic su **Endpoint**. Nella pagina Endpoint sono elencati tutti gli endpoint disponibili per la macchina virtuale.

- Fare clic su **Aggiungi**. Verrà visualizzata la finestra di dialogo Aggiungi endpoint. Fare clic sulla freccia per continuare.

- Nel campo **Nome** digitare il nome seguente per l'endpoint: **WinRMHttps**.

- Per il **protocollo**, specificare **TCP**.

- Per la **porta pubblica**, digitare i numeri di porta che si desidera utilizzare per la connessione.

- Specificare **5986** per la **porta privata**.

- Fare clic sul segno di spunta per creare l'endpoint.

Dopo aver creato l'endpoint, è possibile visualizzarne i dettagli e determinare l'indirizzo IP virtuale pubblico (VIP, Virtual Private Network). Registrare tale indirizzo.

Si consiglia di connettersi da un'altra macchina virtuale presente all'interno della stessa rete virtuale; in questo modo si riduce il numero di endpoint pubblici nella rete virtuale. Quando si utilizza questo metodo, ci si connette alla macchina virtuale tramite una sessione Desktop remoto e si configura tale macchina virtuale affinché venga utilizzata come qualsiasi altro client di Windows in una rete locale. Non è necessario aggiungere il numero di porta pubblica poiché è già noto.

### Arresto e riavvio

A differenza del dispositivo StorSimple fisico, in quello virtuale StorSimple non sono disponibili pulsanti di accensione o spegnimento. Tuttavia, in alcuni casi potrebbe essere necessario arrestare e riavviare il dispositivo virtuale. Ad esempio, per completare l'applicazione di alcuni aggiornamenti potrebbe essere richiesto di riavviare la macchina virtuale. Il modo più semplice per avviare, arrestare e riavviare un dispositivo virtuale consiste nell'utilizzare la console di gestione delle macchine virtuali.

Quando si apre la console di gestione, il dispositivo virtuale viene visualizzato in **esecuzione** poiché viene avviato per impostazione predefinita al termine della procedura di creazione. È possibile arrestare e riavviare una macchina virtuale in qualsiasi momento.

Per arrestare un dispositivo virtuale, fare clic sul suo nome, quindi selezionare **Arresta**. Durante l'arresto del dispositivo virtuale, lo stato viene indicato come **Arresto in corso**. Dopo aver arrestato il dispositivo virtuale, lo stato viene indicato come **Arrestato**.

Quando un dispositivo virtuale è in esecuzione e si desidera riavviarlo, fare clic sul suo nome, quindi scegliere **Riavvia**. Durante il riavvio, lo stato del dispositivo viene indicato come **Riavvio in corso**. Quando è possibile utilizzare il dispositivo, lo stato viene indicato come **In esecuzione**.

È anche possibile utilizzare i seguenti cmdlet di Windows PowerShell per avviare, arrestare e riavviare il dispositivo virtuale. Di seguito, è riportato un esempio per ogni cmdlet.

`Start-AzureVMC:\PS>Start-AzureVM -ServiceName "MyStorSimpleservice1" -Name "MyStorSimpleDevice"`


`Stop-AzureVMC:\PS>Stop-AzureVM -ServiceName "MyStorSimpleservice1" -Name "MyStorSimpleDevice"`

`Restart-AzureVMC:\PS>Restart-AzureVM -ServiceName "MyStorSimpleservice1" -Name "MyStorSimpleDevice"`

### Ripristino delle impostazioni di fabbrica

Se si decide di ripristinare il dispositivo virtuale, sarà sufficiente disattivarlo, eliminarlo e successivamente crearne uno nuovo. Analogamente a quanto accade per il ripristino del dispositivo fisico, nel nuovo dispositivo virtuale non saranno presenti aggiornamenti. Pertanto, assicurarsi di cercare gli aggiornamenti prima di utilizzarlo.


## Eseguire il failover nel dispositivo virtuale

Il ripristino di emergenza (DR, Disaster Recovery) rappresenta una delle situazioni principali per le quali il dispositivo è stato realizzato. In questo scenario, è possibile che il dispositivo fisico StorSimple o il data center completo non sia disponibile. Fortunatamente, è possibile utilizzare un dispositivo virtuale per ripristinare le operazioni in un percorso alternativo. Durante il ripristino di emergenza, i contenitori del volume del dispositivo di origine cambiano la proprietà e vengono trasferiti nel dispositivo virtuale. I prerequisiti per il ripristino di emergenza prevedono che sia stato creato e configurato il dispositivo virtuale, che tutti i volumi nel contenitore siano offline e che al contenitore del volume sia associata una snapshot cloud.

### Ripristinare il dispositivo fisico per il dispositivo virtuale StorSimple

1. Verificare che il contenitore del volume di cui si desidera eseguire il failover sia associato alle snapshot cloud.

- Nella pagina **Dispositivo**, fare clic sulla scheda **Contenitori di volumi**.

- Selezionare un contenitore del volume di cui si desidera eseguire il failover a un dispositivo virtuale. Fare clic su contenitore del volume per visualizzare l'elenco dei volumi presenti in questo contenitore. Selezionare un volume e fare clic su **Offline** per passare il volume offline. Ripetere questo processo per tutti i volumi nel contenitore di volumi.

- Ripetere il passaggio precedente per tutti i contenitori di volumi di cui si desidera eseguire il failover al dispositivo virtuale.

- Nella pagina **Dispositivo**, selezionare il dispositivo di cui eseguire il failover, quindi fare clic su **Failover** per aprire la procedura guidata Failover del dispositivo.

- In **Scegli contenitore del volume di cui eseguire il failover**, selezionare i contenitori di volumi per i quali si desidera eseguire il failover. Affinché sia visualizzato in questo elenco, il contenitore del volume deve contenere una snapshot cloud e deve essere offline. Se per errore non viene visualizzato un contenitore del volume, annullare la procedura guidata e verificare che sia offline.

- Nella pagina successiva, in **Scegli un dispositivo di destinazione per i volumi dei contenitori selezionati**, selezionare il dispositivo virtuale dall'elenco a discesa dei dispositivi disponibili. Solo i dispositivi dotati di capacità disponibile vengono visualizzati nell'elenco.

- Controllare tutte le impostazioni di failover nella pagina **Conferma failover**. Se sono corrette, fare clic sull'icona del segno di spunta.

A questo punto, viene avviato il processo di failover. Al termine del processo di failover, accedere alla pagina **Dispositivi** e selezionare il dispositivo virtuale che è stato utilizzato come destinazione del processo di failover. Andare alla pagina Contenitori di volumi. Dovrebbero essere elencati tutti i contenitori di volumi, insieme ai volumi del dispositivo precedente.

>[AZURE.NOTE]La quantità di risorse di archiviazione supportate nel dispositivo virtuale StorSimple è pari a 30 TB.

![Video disponibile](./media/storsimple-virtual-device/Video_icon.png) **Video disponibile**

Per guardare un video che descrive come è possibile ripristinare un dispositivo fisico in cui si è verificato un errore in un dispositivo virtuale nel cloud, fare clic [qui](http://azure.microsoft.com/documentation/videos/storsimple-and-disaster-recovery/).

## Arrestare o eliminare il dispositivo virtuale

Se in precedenza è stato configurato e utilizzato un dispositivo virtuale StorSimple, ma si desidera arrestare l'addebito dei costi di calcolo relativi all'utilizzo, è possibile arrestare il dispositivo virtuale. Se si arresta il dispositivo virtuale, non viene eliminato il sistema operativo o i dischi dati nell'archiviazione. Inoltre, viene interrotto l'addebito di costi di sottoscrizione, ma non quello dei costi per il sistema operativo e per i dischi dati.

Se si elimina o si arresta il dispositivo virtuale, quest'ultimo verrà visualizzato come **Offline** nella pagina Dispositivi del servizio StorSimple Manager. Se si desidera eliminare i backup creati dal dispositivo virtuale, è possibile disattivarlo o eliminarlo come dispositivo. Per ulteriori informazioni, vedere [Disattivazione di un dispositivo](storsimple-deactivate-and-delete-device.md#deactivate-a-device).

### Per arrestare il dispositivo virtuale StorSimple

1. Accedere al portale di Azure.

2. Fare clic su **Macchine virtuali**, quindi selezionare il dispositivo virtuale.

3. Fare clic su **Arresta**.

### Per eliminare il dispositivo virtuale StorSimple

1. Accedere al portale di Azure.

- Fare clic su **Macchine virtuali**, quindi selezionare il dispositivo virtuale.

- Fare clic su **Elimina** e scegliere di eliminare tutti i dischi di macchina virtuale.


## Passaggi successivi

Per amministrare il dispositivo virtuale, fare riferimento a un elenco dettagliato dei flussi di lavoro in [Amministrare il dispositivo StorSimple utilizzando il servizio di gestione di StorSimple](storsimple-manager-service-administration.md#administer-storsimple-device-using-storsimple-manager-service).

<!---HONumber=Oct15_HO3-->