<properties
   pageTitle="Creazione di un'immagine di macchina virtuale per Azure Marketplace | Microsoft Azure"
   description="Istruzioni dettagliate su come creare un'immagine di macchina virtuale per Azure Marketplace acquistabile dagli utenti."
   services="Azure Marketplace"
   documentationCenter=""
   authors="HannibalSII"
   manager=""
   editor=""/>

<tags
   ms.service="marketplace"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="Azure"
   ms.workload="na"
   ms.date="03/07/2016"
   ms.author="hascipio; v-divte"/>

# Guida alla creazione di un'immagine di macchina virtuale per Azure Marketplace

Questo articolo, **Passaggio 2**, illustra la preparazione di dischi rigidi virtuali (VHD) da distribuire in Azure Marketplace. I dischi rigidi virtuali costituiscono la base dello SKU. Il processo varia a seconda che si stia offrendo uno SKU basato su Linux o su Windows. In questo articolo vengono descritti entrambi gli scenari. Questo processo può essere eseguito parallelamente alla [creazione e registrazione dell'account][link-acct-creation].

## 1\. Definire le offerte e gli SKU

Questa sezione illustra come definire le offerte e i relativi SKU associati.

Un'offerta è l'elemento "padre" di tutti i relativi SKU. È possibile disporre di più offerte. Il modo in cui si sceglie di strutturarle è assolutamente personale. Quando un'offerta passa all'ambiente di staging, passano a tale fase anche tutti i relativi SKU. Considerare attentamente gli identificatori degli SKU, perché saranno visibili nell'URL:

- Azure.com: http://azure.microsoft.com/marketplace/partners/{PartnerNamespace}/{OfferIdentifier}-{SKUidentifier}

- Portale di anteprima di Azure: https://portal.azure.com/#gallery/{PublisherNamespace}.{OfferIdentifier}{SKUIDdentifier}

SKU è il nome commerciale per un'immagine di macchina virtuale. Un'immagine di macchina virtuale contiene un disco del sistema operativo e zero o più dischi dati. Si tratta essenzialmente del profilo di archiviazione completo per una macchina virtuale. È necessario un disco rigido virtuale per ogni disco. È necessario crearne uno anche per i dischi dati vuoti.

Indipendentemente dal sistema operativo usato, aggiungere solo il numero minimo di dischi dati necessari per lo SKU. In fase di distribuzione i clienti non possono rimuovere i dischi che fanno parte di un'immagine, ma possono sempre aggiungerne altri durante o dopo la distribuzione se necessario.

### 1\.1 Aggiungere un'offerta

1. Accedere al [portale di pubblicazione][link-pubportal] usando l'account venditore.
2. Selezionare la scheda **Macchine virtuali** del portale di pubblicazione. Immettere il nome dell'offerta nel campo visualizzato. Il nome dell'offerta corrisponde in genere al nome del prodotto o servizio da vendere in Azure Marketplace.
3. Selezionare **Crea**.

### 1\.2 Definire uno SKU
Dopo aver aggiunto l'offerta, è necessario definire e identificare gli SKU. È possibile avere più offerte e ogni offerta può includere più SKU. Quando un'offerta passa all'ambiente di staging, passano a tale fase anche tutti i relativi SKU.

1. **Aggiungere uno SKU.** Per lo SKU è necessario un identificatore, che viene usato nell'URL. L'identificatore deve essere univoco nel profilo di pubblicazione, ma non vi è alcun rischio di conflitto tra identificatori con altri editori.

> [AZURE.NOTE] L'identificatore dell'offerta e quello dello SKU vengono visualizzati nell'URL dell'offerta nel Marketplace.

2. **Aggiungere una descrizione di riepilogo per lo SKU.** Le descrizioni di riepilogo sono visibili ai clienti, è quindi consigliabile che siano facili da leggere. Non è necessario bloccare queste informazioni fino al raggiungimento della fase "Passa a gestione temporanea". Fino a quel momento, è possibile modificarle liberamente.
3. Se si usano SKU basati su Windows, seguire i collegamenti consigliati per acquistare le versioni approvate di Windows Server.

## 2\. Creare un VHD compatibile con Azure (basato su Linux)
Questa sezione è incentrata sulle procedure consigliate per la creazione di un'immagine di macchina virtuale basata su Linux per Azure Marketplace. Per una procedura dettagliata, vedere la documentazione seguente: [Creazione e caricamento di un disco rigido virtuale che contiene il sistema operativo Linux][link-azure-vm-1].

> [AZURE.TIP] Molti dei passaggi seguenti (ad esempio, installazione dell'agente e parametri di avvio del kernel) sono già stati eseguiti per le immagini Linux disponibili nella Raccolta immagini di Microsoft Azure. Partendo quindi da una di queste immagini invece di configurare un'immagine Linux non compatibile con Azure, è possibile risparmiare tempo.

### 2\.1 Scegliere la dimensione corretta per il VHD
Gli SKU (immagini di macchina virtuale) pubblicati devono essere progettati per funzionare con tutte le dimensioni di macchina virtuale che supportano il numero di dischi per lo SKU. È possibile fornire indicazioni sulle dimensioni consigliate, che tuttavia verranno trattate come raccomandazioni, senza alcun obbligo di osservarle.

1. VHD del sistema operativo Linux: il disco rigido virtuale del sistema operativo Linux nell'immagine di macchina virtuale deve essere creato come VHD con formato fisso da 30-50 GB. Le dimensioni non possono essere inferiori a 30 GB. Se la dimensione fisica è inferiore alla dimensione del VHD, il VHD deve essere di tipo sparse. I VHD Linux di dimensioni superiori a 50 GB verranno considerati caso per caso. Se si possiede già un VHD in un formato diverso, è possibile usare il [cmdlet Convert-VHD di PowerShell per modificarne il formato][link-technet-1].
2. VHD dei dischi dati: i dischi dati possono avere dimensioni fino a 1 TB. I VHD dei dischi dati devono essere creati come VHD con formato fisso, ma devono anche essere di tipo sparse. Nello stabilire le dimensioni dei dischi, tenere presente che i clienti non possono ridimensionare i VHD all'interno di un'immagine.

### 2\.2 Assicurarsi che sia installato l'agente Linux di Azure più aggiornato
Nel preparare il VHD del sistema operativo, assicurarsi che sia installato l'[agente Linux di Azure][link-azure-vm-2] più aggiornato usando i pacchetti RPM o Deb. Il pacchetto è spesso denominato walinuxagent o WALinuxAgent, ma per esserne certi è consigliabile consultare il distributore. L'agente offre funzioni importanti per distribuzioni IaaS Linux in Azure, quali il provisioning di VM e le funzionalità di rete.

Anche se l'agente può essere configurato in diversi modi, è consigliabile usare una configurazione generica per ottimizzare la compatibilità. È possibile installare l'agente manualmente, ma si consiglia vivamente di usare pacchetti preconfigurati del proprio distributore, se disponibili.

Se si sceglie di installare l'agente manualmente dal [repository GitHub][link-github-waagent], copiare prima di tutto il file Waagent in /usr/sbin ed eseguire questo comando nella directory radice:

    # chmod 755 /usr/sbin/waagent
    # /usr/sbin/waagent -install

Il file di configurazione dell'agente viene inserito in /etc/waagent.conf.

### 2\.3 Verificare che siano incluse le librerie necessarie
Oltre all'agente Linux di Azure, devono essere incluse le librerie seguenti:

1. [Linux Integration Services][link-intsvc] 3.0 o versione successiva deve essere abilitato nel kernel. Vedere [Requisiti del kernel Linux](./virtual-machines-linux-create-upload-vhd-generic/#linux-kernel-requirements).
2. [Patch per il kernel](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/scsi/storvsc_drv.c?id=5c1b10ab7f93d24f29b5630286e323d1c5802d5c) per la stabilità delle operazioni di I/O di Azure (probabilmente non necessaria per kernel recenti, ma è consigliabile verificare).
3. [Python][link-python] 2.6 o versione successiva.
4. Pacchetto pyasn1 per Python, se non già installato.
5. [OpenSSL][link-openssl] (è consigliata la versione 1.0 o successiva).

### 2\.4 Configurare le partizioni dei dischi
Si consiglia di non usare la gestione dei volumi logici. Creare una singola partizione radice per il disco del sistema operativo. Non usare una partizione di swapping sul sistema operativo o sul disco dati. È consigliabile rimuovere una partizione di swapping, anche se non è montata in /etc/fstab. Se necessario, una partizione di swapping può essere creata nel disco risorse locale (/dev/sdb) dall'agente Linux.

### 2\.5 Aggiungere i parametri della riga di comando necessari per l'avvio del kernel
I parametri seguenti devono essere aggiunti anch'essi alla riga di comando per l'avvio del kernel.

        console=ttyS0 earlyprintk=ttyS0 rootdelay=300

In questo modo, il supporto tecnico di Azure può fornire ai clienti l'output della console seriale quando è necessario. Viene anche fornito un timeout adeguato per il montaggio del disco del sistema operativo dall'archiviazione cloud. Anche se lo SKU impedisce ai clienti di usare SSH direttamente nella macchina virtuale, l'output della console seriale deve essere abilitato.

### 2\.6 Includere il server SSH per impostazione predefinita
È consigliabile abilitare SSH per il cliente. Se il server SSH è abilitato, aggiungere la configurazione per mantenere attive le sessioni SSH con l'opzione seguente: **ClientAliveInterval 180**. Anche se è consigliabile usare il valore 180, l'intervallo accettabile è compreso tra 30 e 235. Non tutte le applicazioni offrono ai clienti un accesso SSH diretto alla macchina virtuale. In caso di blocco esplicito di SSH, non è necessario impostare l'opzione **ClientAliveInterval**.

### 2\.7 Soddisfare i requisiti di rete
Di seguito sono indicati i requisiti di rete per un'immagine di macchina virtuale Linux compatibile con Azure:

- In molti casi, è meglio disabilitare NetworkManager. Fanno eccezione i sistemi basati su CentOS 7.x (e sistemi derivati) per cui è consigliabile lasciare abilitato NetworkManager.
- Dovrebbe essere possibile controllare la configurazione di rete con gli script **ifup** e **ifdown**. L'agente Linux può usare questi comandi per riavviare la rete durante il provisioning.
- Non sono consentite configurazioni di rete personalizzate. Il file Resolv.conf deve essere eliminato come ultimo passaggio. Questa operazione in genere viene eseguita durante il deprovisioning (vedere la [Guida dell'utente dell'agente Linux di Azure](./virtual-machines-linux-agent-user-guide/)). È anche possibile eseguire questo passaggio manualmente con il comando seguente:

        rm /etc/resolv.conf

- Il dispositivo di rete deve essere attivato all'avvio e deve usare DHCP.
- Azure non supporta IPv6. Se questa proprietà è abilitata, non funzionerà.

### 2\.8 Verificare la corretta applicazione delle procedure consigliate per la sicurezza
Per gli SKU in Azure Marketplace, è essenziale osservare le procedure consigliate relative alla sicurezza, tra cui:

- Installare tutte le patch di protezione per la distribuzione.
- Seguire le linee guida per la sicurezza della distribuzione.
- Evitare di creare account predefiniti, che restano identici, tra diverse istanze di provisioning.
- Cancellare le voci della cronologia della Bash.
- Includere il software iptables (firewall), ma non abilitare alcuna regola. In questo modo, è possibile offrire ai clienti un'esperienza predefinita uniforme. I clienti che desiderano usare un firewall per macchine virtuali per eseguire configurazioni aggiuntive possono configurare le regole di iptables in base alle esigenze specifiche.

### 2\.9 Generalizzare l'immagine
Dal momento che tutte le immagini in Azure Marketplace devono poter essere riutilizzate in modo generico, è necessario eliminare alcuni aspetti più specifici della configurazione. A questo scopo, in Linux è necessario eseguire il deprovisioning del VHD del sistema operativo.

Il comando di Linux per il deprovisioning è il seguente:

        # waagent -deprovision

Il comando esegue automaticamente le operazioni seguenti:

- Rimuove la configurazione di NameServer in /etc/resolv.conf.
- Rimuove i lease del client DHCP memorizzati nella cache.
- Reimposta il nome host su localhost.localdomain.

È consigliabile impostare il file di configurazione (/etc/waagent.conf) per garantire il completamento delle operazioni seguenti:

- Impostare Provisioning.RegenerateSshHostKeyPair su "y" nel file di configurazione per rimuovere tutte le chiavi host SSH.
- Impostare Provisioning.DeleteRootPassword su "y" nel file di configurazione per rimuovere la password "root" da /etc/shadow. Per la documentazione dei contenuti del file di configurazione, vedere la sezione "CONFIGURATION" del file README nella pagina del repository GitHub relativa all'agente ([https://github.com/Azure/WALinuxAgent](https://github.com/Azure/WALinuxAgent) e scorrere verso il basso).  

La generalizzazione della VM Linux è stata completata. Disabilitare la macchina virtuale dal portale di Azure, dalla riga di comando o dall'interno della macchina virtuale. Dopo aver disattivato la macchina virtuale, andare al passaggio 3.4.

## 3\. Creare un VHD compatibile con Azure (basato su Windows)
Questa sezione è incentrata sui passaggi necessari per creare uno SKU basato su Windows Server per Azure Marketplace.

### 3\.1 Assicurarsi di usare i VHD di base corretti
Il VHD del sistema operativo per l'immagine di macchina virtuale deve essere basato su un'immagine di base approvata per Azure contenente Windows Server o SQL Server.

Per iniziare, creare una macchina virtuale da una delle immagini seguenti, disponibili nel [portale di Microsoft Azure][link-azure-portal]\:

- Windows Server ([2012 R2 Datacenter][link-datactr-2012-r2], [2012 Datacenter][link-datactr-2012], [2008 R2 SP1][link-datactr-2008-r2])
- SQL Server 2014 ([Enterprise][link-sql-2014-ent], [Standard][link-sql-2014-std], [Web][link-sql-2014-web])
- SQL Server 2012 SP2 ([Enterprise][link-sql-2012-ent], [Standard][link-sql-2012-std], [Web][link-sql-2012-web])

Questi collegamenti sono disponibili anche nella pagina degli SKU del portale di pubblicazione.

> [AZURE.TIP] Se si usa l'attuale portale di Azure o PowerShell, sono approvate le immagini di Windows Server pubblicate a partire dall'8 settembre 2014.


### 3\.2 Creare la macchina virtuale basata su Windows
Con pochi semplici passaggi, dal portale di Microsoft Azure è possibile creare la macchina virtuale basata sull'immagine di base approvata. Di seguito viene illustrata una panoramica del processo.

1. Nella pagina delle immagini di base selezionare **Crea macchina virtuale** per essere reindirizzati al nuovo [portale di Microsoft Azure][link-azure-portal].

    ![disegno][img-acom-1]

2. Accedere al portale con l'account Microsoft e la password per la sottoscrizione di Azure che si vuole usare.
3. Seguire le istruzioni per la creazione di una macchina virtuale usando l'immagine di base selezionata. È necessario specificare un nome host (nome del computer), un nome utente (registrato come amministratore) e una password per la macchina virtuale.

    ![disegno][img-portal-vm-create]

4. Selezionare la dimensione della macchina virtuale da distribuire:

    a. Se si prevede di sviluppare il VHD in locale, la dimensione non è rilevante. Valutare l'opportunità di usare una delle macchine virtuali di dimensioni minori.

    b. Se si prevede di sviluppare l'immagine in Azure, valutare l'opportunità di usare una delle dimensioni di macchina virtuale consigliate per l'immagine selezionata.

    c. Per informazioni sui prezzi, vedere il selettore del **piano tariffario consigliato** visualizzato nel portale. Verranno indicate le tre dimensioni consigliate, specificate dall'editore. In questo caso, l'editore è Microsoft.

    ![disegno][img-portal-vm-size]

5. Impostare le proprietà:

    a. Per una distribuzione rapida, è possibile mantenere i valori predefiniti per le proprietà in **Configurazione facoltativa** e **Gruppo di risorse**.

    b. Facoltativamente, in **Account di archiviazione** è possibile selezionare l'account di archiviazione in cui archiviare il VHD del sistema operativo.

    c. Facoltativamente, in **Gruppo di risorse** è possibile selezionare il gruppo logico in cui inserire la macchina virtuale.
6. Selezionare la **Località** in cui eseguire la distribuzione:

    a. Se si prevede di sviluppare il VHD in locale, la località non è rilevante, perché l'immagine verrà caricata in Azure successivamente.

    b. Se si prevede di sviluppare l'immagine in Azure, valutare l'opportunità di usare una delle aree di Microsoft Azure negli Stati Uniti sin dall'inizio. In questo modo, è possibile accelerare il processo di copia del VHD eseguito da Microsoft quando si invia l'immagine per la certificazione.

    ![disegno][img-portal-vm-location]

7. Fare clic su **Crea**. Viene avviata la distribuzione della macchina virtuale. La distribuzione verrà completata in pochi minuti e sarà possibile iniziare a creare l'immagine per lo SKU.

### 3\.3 Sviluppare il VHD nel cloud
È consigliabile sviluppare il VHD nel cloud usando Remote Desktop Protocol (RDP). Per connettersi a RDP, usare il nome utente e la password specificati durante il provisioning.

> [AZURE.IMPORTANT] Se si sviluppa il VHD in locale (scelta non consigliata), vedere l'articolo relativo alla [creazione di un'immagine di macchina virtuale in locale](marketplace-publishing-vm-image-creation-on-premise.md). Il download del VHD non è necessario se lo sviluppo viene eseguito nel cloud.


**Connettersi con RDP usando il [portale di Microsoft Azure][link-azure-portal]**

1. Selezionare **Sfoglia** > **Macchine virtuali**.
2. Verrà visualizzato il pannello Macchine virtuali. Assicurarsi che la macchina virtuale a cui connettersi sia in esecuzione e selezionarla nell'elenco delle macchine virtuali distribuite.
3. Viene visualizzato un pannello contenente la descrizione della macchina virtuale selezionata. Nella parte superiore fare clic su **Connetti**.
4. Verrà richiesto di immettere il nome utente e la password specificati durante il provisioning.

**Connettersi tramite RDP usando PowerShell**

Per scaricare un file desktop remoto in una macchina locale, usare il [cmdlet Get-AzureRemoteDesktopFile][link-technet-2]. Per usare questo cmdlet, è necessario conoscere il nome del servizio e quello della macchina virtuale. Se la macchina virtuale è stata creata nel [portale di Microsoft Azure][link-azure-portal], queste informazioni sono disponibili nella sezione delle proprietà della macchina virtuale.

1. Nel portale di Microsoft Azure selezionare **Sfoglia** > **Macchine virtuali**.
2. Verrà visualizzato il pannello Macchine virtuali. Selezionare la macchina virtuale distribuita.
3. Viene visualizzato un pannello contenente la descrizione della macchina virtuale selezionata.
4. Fare clic su **Proprietà**.
5. La prima parte del nome di dominio è costituita dal nome del servizio. Il nome host è il nome della macchina virtuale.

    ![disegno][img-portal-vm-rdp]

6. Il cmdlet per scaricare il file RDP per la macchina virtuale creata nel desktop locale dell'amministratore è il seguente:

        Get‐AzureRemoteDesktopFile ‐ServiceName “baseimagevm‐6820cq00” ‐Name “BaseImageVM” –LocalPath “C:\Users\Administrator\Desktop\BaseImageVM.rdp”

Altre informazioni su RDP sono disponibili nell'articolo di MSDN [Connettersi a una macchina virtuale di Azure con RDP o SSH](http://msdn.microsoft.com/library/azure/dn535788.aspx).

**Configurare una macchina virtuale e creare lo SKU**

Dopo aver scaricato il VHD del sistema operativo, usare Hyper-V e configurare una macchina virtuale per iniziare a creare lo SKU. I passaggi dettagliati sono disponibili nell'articolo di TechNet [Installare Hyper-V e creare una macchina virtuale](http://technet.microsoft.com/library/hh846766.aspx).

### 3\.4 Scegliere la dimensione corretta per il VHD
Il VHD del sistema operativo Windows nell'immagine di macchina virtuale deve essere creato come VHD con formato fisso da 128 GB.

Se la dimensione fisica è inferiore a 128 GB, il VHD deve essere di tipo sparse. Le immagini Windows e SQL Server di base fornite soddisfano già questi requisiti. Evitare di modificare il formato o la dimensione del VHD ottenuto.

I dischi dati possono avere dimensioni fino a 1 TB. Nello stabilire le dimensioni dei dischi, tenere presente che i clienti non possono ridimensionare i VHD all'interno di un'immagine in fase di distribuzione. I VHD dei dischi dati devono essere creati come VHD con formato fisso, ma devono anche essere di tipo sparse. I dischi dati possono essere vuoti o contenere dati.


### 3\.5 Installare le patch per Windows più recenti
Le immagini di base contengono le patch più recenti fino al momento della data di pubblicazione. Prima di pubblicare il VHD del sistema operativo creato, assicurarsi che sia stato eseguito Windows Update e che siano stati installati i più recenti aggiornamenti della sicurezza contrassegnati come critici e importanti.

### 3\.6 Eseguire eventuali configurazioni aggiuntive e pianificare le attività nel modo necessario
Se si rendono necessarie configurazioni aggiuntive, valutare l'opportunità di usare un'attività pianificata da eseguire all'avvio per apportare eventuali modifiche finali alla macchina virtuale dopo la distribuzione.

- È una procedura consigliata fare in modo che l'attività elimini se stessa al termine dell'esecuzione.
- Evitare di eseguire configurazioni su unità diverse da C o D, perché queste sono le uniche due unità la cui presenza è sempre garantita. L'unità C corrisponde al disco del sistema operativo e l'unità D al disco locale temporaneo.

### 3\.7 Generalizzare l'immagine
Tutte le immagini in Azure Marketplace devono poter essere riutilizzate in modo generico. In altri termini, il VHD del sistema operativo deve essere generalizzato.

- Per Windows, l'immagine deve essere preparata con sysprep e non è possibile eseguire configurazioni che non supportano il comando **sysprep**.
- È possibile eseguire il comando seguente dalla directory %windir%\\System32\\Sysprep.

        sysprep.exe /generalize /oobe /shutdown

  Le indicazioni su come preparare con sysprep il sistema operativo sono incluse in un passaggio dell'articolo di MSDN relativo a [creazione e caricamento di un disco rigido virtuale con Windows Server in Azure](./virtual-machines-create-upload-vhd-windows-server/).

## 4\. Distribuire una macchina virtuale dai VHD
Dopo aver caricato uno o più VHD (ovvero il VHD del sistema operativo generalizzato e zero o più VHD dei dischi dati) in un account di archiviazione di Azure, è possibile registrarli come immagine di macchina virtuale degli utenti ed eseguirne il test. Dal momento che il VHD del sistema operativo è generalizzato, non è possibile distribuire direttamente la macchina virtuale specificando l'URL del VHD.

Per altre informazioni sulle immagini di macchina virtuale, vedere i post di blog seguenti:

- [Immagine VM](https://azure.microsoft.com/blog/vm-image-blog-post/)
- [Procedure di PowerShell per immagini VM](https://azure.microsoft.com/blog/vm-image-powershell-how-to-blog-post/)
- [Informazioni sulle immagini di macchine virtuali in Azure](https://msdn.microsoft.com/library/azure/dn790290.aspx)

### 4\.1 Creare un'immagine di macchina virtuale degli utenti
Per creare un'immagine di macchina virtuale degli utenti dallo SKU per poter iniziare a distribuire più macchine virtuali, è necessario usare l'[API REST Create VM Image](http://msdn.microsoft.com/library/azure/dn775054.aspx) per registrare i VHD come immagine di macchina virtuale.

Per creare un'immagine di macchina virtuale da PowerShell, è possibile usare il cmdlet **Invoke-WebRequest**. Lo script di PowerShell seguente illustra come creare un'immagine di macchina virtuale con un disco del sistema operativo e un disco dati. Si noti che è necessario avere già configurato la sessione di PowerShell e avere impostato una sottoscrizione.

        # Image Parameters to Specify
        $ImageName=’ENTER-YOUR-OWN-IMAGE-NAME-HERE’
        $Label='ENTER-YOUR-LABEL-HERE'
        $Description='DESCRIBE YOUR IMAGE HERE’
        $osCaching='ReadWrite'
        $os = 'Windows'
        $state = 'Generalized'
        $osMediaLink = 'https://mystorageaccount.blob.core.windows.net/vhds/myosvhd.vhd'
        $dataCaching='None'
        $lun='1'
        $dataMediaLink='http://mystorageaccount.blob.core.windows.net/vhds/mydatavhd.vhd'
        # Subscription-Related Properties
        $SrvMngtEndPoint='https://management.core.windows.net'
        $subscription = Get-AzureSubscription -Current -ExtendedDetails
        $certificate = $subscription.Certificate
        $SubId = $subscription.SubscriptionId
        $body =  
        "<VMImage xmlns=`"http://schemas.microsoft.com/windowsazure`" xmlns:i=`"http://www.w3.org/2001/XMLSchema-instance`">" + Name>" + $ImageName + "</Name>" +
        "<Label>" + $Label + "</Label>" +
        "<Description>" + $Description + "</Description>" + "<OSDiskConfiguration>" +
        "<HostCaching>" + $osCaching + "</HostCaching>" +
        "<OSState>" + $state + "</OSState>" +
        "<OS>" + $os + "</OS>" +
        "<MediaLink>" + $osMediaLink + "</MediaLink>" +
        "</OSDiskConfiguration>" +
        "<DataDiskConfigurations>" +
        "<DataDiskConfiguration>" +
        "<HostCaching>" + $dataCaching + "</HostCaching>" +
        "<Lun>" + $lun + "</Lun>" +
        "<MediaLink>" + $dataMediaLink + "</MediaLink>" +
        "</DataDiskConfiguration>" +
        "</DataDiskConfigurations>" +
        "</VMImage>"
        $uri = $SrvMngtEndPoint + "/" + $SubId + "/" + "services/vmimages" $headers = @{"x-ms-version"="2014-06-01"}
        $response = Invoke-WebRequest -Uri $uri -ContentType "application/xml" -Body
        $body -Certificate $certificate -Headers $headers -Method POST
        if ($response.StatusCode -ge 200 -and $response.StatusCode -lt 300)
        {
        echo "Accepted"
        } else {
        echo "Not Accepted" }
        $opId = $response.Headers.'x-ms-request-id'
        $uri2 = $SrvMngtEndPoint + "/" + $SubId + "/" + "operations" + "/" + $opId $response2 = Invoke-WebRequest -Uri $uri2 -ContentType "application/xml" -
        Certificate $certificate -Headers $headers -Method GET
        $response2.RawContent


Eseguendo questo script, viene creata un'immagine di macchina virtuale con il nome specificato nel parametro ImageName, ovvero "myVMImage". L'immagine è costituita da un disco del sistema operativo e da un disco dati.

Questa API è un'operazione asincrona e restituisce il codice di accettazione 202. Per determinare se l'immagine di macchina virtuale è stata creata, è necessario eseguire una query per ottenere lo stato dell'operazione. x-ms-request-id nella risposta restituita corrisponde all'ID operazione. Questo ID deve essere impostato in $opId, come mostrato di seguito.

        $opId = #Fill In With Operation ID
        $uri2 = $SrvMngtEndPoint + "/" + $SubId + "/" + "operations" + "/" + "opId"
        $response2 = Invoke‐WebRequest ‐Uri $uri2 ‐ContentType "application/xml" ‐Certificate $certificate ‐Headers $headers ‐Method GET

Per creare un'immagine di macchina virtuale da un VHD del sistema operativo e un disco dati vuoto aggiuntivo (per questo disco non è stato creato il VHD) con l'API Create VM Image, usare lo script seguente:

        # Image Parameters to Specify
        $ImageName=’myVMImage’
        $Label='IMAGE_LABEL'
        $Description='My VM Image to Test’
        $osCaching='ReadWrite'
        $os = 'Windows'
        $state = 'Generalized'
        $osMediaLink = 'http://mystorageaccount.blob.core.windows.net/containername/myOSvhd.vhd'
        $dataCaching='None'
        $lun='1'
        $emptyDiskSize= 32
        # Subscription-Related Properties
        $SrvMngtEndPoint='https://management.core.windows.net'
        $subscription = Get‐AzureSubscription –Current ‐ExtendedDetails
        $certificate = $subscription.Certificate
        $SubId = $subscription.SubscriptionId
        $body =
        "<VMImage xmlns="http://schemas.microsoft.com/windowsazure" xmlns:i="http://www.w3.org/2001/XMLSchema‐instance">" +
        "<Name>" + $ImageName + "</Name>" +
        "<Label>" + $Label + "</Label>" +
        "<Description>" + $Description + "</Description>" +
        "<OSDiskConfiguration>" +
        "<HostCaching>" + $osCaching + "</HostCaching>" +
        "<OSState>" + $state + "</OSState>" +
        "<OS>" + $os + "</OS>" +
        "<MediaLink>" + $osMediaLink + "</MediaLink>" +
        "</OSDiskConfiguration>" +
        "<DataDiskConfigurations>" +
        "<DataDiskConfiguration>" +
        "<HostCaching>" + $dataCaching + "</HostCaching>" +
        "<Lun>" + $lun + "</Lun>" +
        "<MediaLink>" + $dataMediaLink + "</MediaLink>" +
        "<LogicalDiskSizeInGB>" + $emptyDiskSize + "</LogicalDiskSizeInGB>" +
        "</DataDiskConfiguration>" +
        "</DataDiskConfigurations>" +
        "</VMImage>"
        $uri = $SrvMngtEndPoint + "/" + $SubId + "/" + "services/vmimages"
        $headers = @{"x‐ms‐version"="2014‐06‐01"}
        $response = Invoke‐WebRequest ‐Uri $uri ‐ContentType "application/xml" ‐Body $body ‐Certificate $certificate ‐Headers $headers ‐Method POST
        if ($response.StatusCode ‐ge 200 ‐and $response.StatusCode ‐lt 300)
        {
        echo "Accepted"
        }
        else
        {
        echo "Not Accepted"
        }

Eseguendo questo script, viene creata un'immagine di macchina virtuale con il nome specificato nel parametro ImageName, ovvero "myVMImage". L'immagine è costituita da un disco del sistema operativo e da un disco dati.

Questa API è un'operazione asincrona e restituisce il codice di accettazione 202. Per determinare se l'immagine di macchina virtuale è stata creata, è necessario eseguire una query per ottenere lo stato dell'operazione. x-ms-request-id nella risposta restituita corrisponde all'ID operazione. Questo ID deve essere impostato in $opId, come mostrato di seguito.

        $opId = #Fill In With Operation ID
        $uri2 = $SrvMngtEndPoint + "/" + $SubId + "/" + "operations" + "/" + "$opId"
        $response2 = Invoke-WebRequest -Uri $uri2 -ContentType "application/xml" Certificate $certificate -Headers $headers -Method GET

Per creare un'immagine di macchina virtuale da un VHD del sistema operativo e un disco dati vuoto aggiuntivo (per questo disco non è stato creato il VHD) con l'API Create VM Image, usare lo script seguente:

        # Image Parameters to Specify
        $ImageName=’myVMImage’
        $Label='IMAGE_LABEL'
        $Description='My VM Image to Test’
        $osCaching='ReadWrite'
        $os = 'Windows'
        $state = 'Generalized'
        $osMediaLink =
        'http://mystorageaccount.blob.core.windows.net/containername/myOSvhd.vhd'
        $dataCaching='None'
        $lun='1'
        $emptyDiskSize= 32
        # Subscription-Related Properties
        $SrvMngtEndPoint='https://management.core.windows.net'
        $subscription = Get-AzureSubscription –Current -ExtendedDetails
        $certificate = $subscription.Certificate
        $SubId = $subscription.SubscriptionId
        $body =  
        "<VMImage xmlns=`"http://schemas.microsoft.com/windowsazure`" xmlns:i=`"http://www.w3.org/2001/XMLSchema-instance`">" +
        "<Name>" + $ImageName + "</Name>" +
        "<Label>" + $Label + "</Label>" +
        "<Description>" + $Description + "</Description>" + "<OSDiskConfiguration>" + "<HostCaching>" + $osCaching + "</HostCaching>" +
        "<OSState>" + $state + "</OSState>" +
        "<OS>" + $os + "</OS>" +
        "<MediaLink>" + $osMediaLink + "</MediaLink>" +
        "</OSDiskConfiguration>" +
        "<DataDiskConfigurations>" +
        "<DataDiskConfiguration>" +
        "<HostCaching>" + $dataCaching + "</HostCaching>" +
        "<Lun>" + $lun + "</Lun>" +
        "<MediaLink>" + $dataMediaLink + "</MediaLink>" +
        "<LogicalDiskSizeInGB>" + $emptyDiskSize + "</LogicalDiskSizeInGB>" + "</DataDiskConfiguration>" +
        "</DataDiskConfigurations>" +
        "</VMImage>"
        $uri = $SrvMngtEndPoint + "/" + $SubId + "/" + "services/vmimages"
        $headers = @{"x-ms-version"="2014-06-01"}
        $response = Invoke-WebRequest -Uri $uri -ContentType "application/xml" -Body $body Certificate $certificate -Headers $headers -Method POST
        if ($response.StatusCode -ge 200 -and $response.StatusCode -lt 300)
        { echo "Accepted"
        } else
        { echo "Not Accepted"
        }

Eseguendo questo script, viene creata un'immagine di macchina virtuale con il nome specificato nel parametro ImageName, ovvero "myVMImage". L'immagine è costituita da un disco del sistema operativo, basato sul VHD passato, e da un disco dati da 32 GB vuoto.

### 4\.2 Distribuire una macchina virtuale da un'immagine di macchina virtuale degli utenti
Per distribuire una macchina virtuale da un'immagine di macchina virtuale degli utenti, è possibile usare l'attuale [portale di Azure](https://manage.windowsazure.com) o PowerShell.

**Distribuire una macchina virtuale dall'attuale portale di Azure**

1. Selezionare **Nuovo** > **Calcolo** > **Macchina virtuale** > **Da raccolta**.

    ![disegno][img-manage-vm-new]

2. Passare a **Immagini personali** e selezionare l'immagine di macchina virtuale da cui distribuire una macchina virtuale.
  1. Selezionare con attenzione l'immagine, perché la visualizzazione **Immagini personali** contiene sia immagini del sistema operativo che immagini di macchina virtuale.
  2. Esaminando il numero di dischi è possibile determinare il tipo di immagine distribuita, perché la maggior parte delle immagini di macchina virtuale contiene più di un disco. Tuttavia, può comunque essere presente un'immagine di macchina virtuale con un solo disco del sistema operativo. In tal caso la voce **Numero di dischi** è impostata su 1.

    ![disegno][img-manage-vm-select]

3. Completare i passaggi della procedura guidata di creazione della macchina virtuale e specificare il nome, la dimensione e la posizione della macchina virtuale, nonché il nome utente e la password.

**Distribuire una macchina virtuale da PowerShell**

Per distribuire una macchina virtuale di grandi dimensioni dall'immagine di macchina virtuale generalizzata appena creata, è possibile usare i cmdlet seguenti.

    $img = Get‐AzureVMImage ‐ImageName "myVMImage"
    $user = "user123"
    $pass = "adminPassword123"
    $myVM = New‐AzureVMConfig ‐Name "VMImageVM" ‐InstanceSize "Large" ‐ImageName $img.ImageName | Add‐AzureProvisioningConfig ‐Windows ‐AdminUsername $user ‐Password $pass
    New‐AzureVM ‐ServiceName "VMImageCloudService" ‐VMs $myVM ‐Location "West US" ‐WaitForBoot

## 5\. Ottenere la certificazione per l'immagine di macchina virtuale
Il passaggio successivo per la preparazione dell'immagine di macchina virtuale per Azure Marketplace consiste nell'ottenere la relativa certificazione.

Il processo include l'esecuzione di uno speciale strumento di certificazione, il caricamento dei risultati della verifica nel contenitore di Azure in cui si trovano i VHD, l'aggiunta di un'offerta, la definizione dello SKU e l'invio dell'immagine di macchina virtuale per la certificazione.

### 5\.1 Scaricare ed eseguire lo strumento di test della certificazione per Azure Certified
Lo strumento di certificazione viene eseguito su una macchina virtuale a sua volta in esecuzione, di cui è stato effettuato il provisioning dall'immagine di macchina virtuale degli utenti, per garantire che l'immagine di macchina virtuale sia compatibile con Microsoft Azure. Lo strumento verifica che siano stati osservati i requisiti e le indicazioni sulla preparazione del VHD. L'output dello strumento è un report compatibilità, che deve essere caricato nel portale di pubblicazione durante la richiesta di certificazione.

Lo strumento di certificazione può essere usato con macchine virtuali Windows e Linux. Lo strumento si connette alle macchine virtuali basate su Windows usando PowerShell e alle macchine virtuali Linux usando SSH.Net.

1. Scaricare prima di tutto lo strumento di certificazione dal [sito di download Microsoft][link-msft-download].
2. Aprire lo strumento di certificazione e fare clic sul pulsante **Start New Test**.
3. Nella schermata **Test Information** immettere un nome per l'esecuzione dei test.
4. Specificare se la macchina virtuale è su Linux o Windows. A seconda dell'opzione scelta, selezionare le opzioni successive.

### **Connettere lo strumento di certificazione a un'immagine di macchina virtuale Linux**

1. Selezionare la modalità di autenticazione SSH, ovvero password o file della chiave.
2. Se si usa l'autenticazione basata su password, immettere il nome DNS (Domain Name System), il nome utente e la password.
3. Se si usa l'autenticazione basata su file della chiave, immettere il nome DNS, il nome utente e il percorso della chiave privata.

  ![Autenticazione della password dell'immagine VM Linux][img-cert-vm-pswd-lnx]

  ![Autenticazione del file della chiave dell'immagine VM Linux][img-cert-vm-key-lnx]

### **Connettere lo strumento di certificazione a un'immagine di macchina virtuale basata su Windows**

1. Immettere il nome DNS completo della macchina virtuale, ad esempio MyVMName.Cloudapp.net.
2. Immettere il nome utente e la password.

  ![Autenticazione della password dell'immagine VM Windows][img-cert-vm-pswd-win]

Dopo aver selezionato le opzioni corrette per l'immagine di macchina virtuale basata su Windows o Linux, fare clic su **Test Connection** per verificare che SSH.Net o PowerShell sia connesso correttamente a scopo di test. Dopo che è stata stabilita una connessione, fare clic su **Next** per avviare il test.

Al termine del test, si riceveranno i risultati (Pass/Fail/Warning) per ogni elemento del test.

![Test case per l'immagine VM Linux][img-cert-vm-test-lnx]

![Test case per l'immagine VM Windows][img-cert-vm-test-win]

Se uno dei test non viene superato, l'immagine non verrà certificata. In tal caso, riesaminare i requisiti e apportare le modifiche necessarie.

Dopo il test automatizzato, viene chiesto di fornire altre informazioni sull'immagine di macchina virtuale rispondendo a un questionario. Rispondere alle domande e fare clic su **Next**.

![Questionario dello strumento di certificazione][img-cert-vm-questionnaire]

![Questionario dello strumento di certificazione][img-cert-vm-questionnaire-2]

Dopo aver completato il questionario, è possibile immettere informazioni aggiuntive, ad esempio le informazioni di accesso SSH per l'immagine di macchina virtuale Linux e una spiegazione per le eventuali valutazioni non superate. È possibile scaricare i risultati dei test e i file di log per i test case eseguiti oltre alle risposte date al questionario. Salvare i risultati nello stesso contenitore dei VHD.

![Salvare i risultati del test di certificazione][img-cert-vm-results]

### 5\.2 Ottenere l'URI di firma di accesso condiviso per le immagini di macchina virtuale

Durante il processo di pubblicazione vengono specificati gli URI (Uniform Resource Identifier) di ciascun VHD creato per lo SKU. Microsoft deve accedere a tali VHD durante il processo di certificazione. È quindi necessario creare un URI di firma di accesso condiviso per ogni VHD. Si tratta dell'URI che deve essere immesso nella scheda **Immagini** nel portale di pubblicazione.

L'URI di firma di accesso condiviso deve soddisfare i requisiti seguenti:

- Per la generazione degli URI di firma di accesso condiviso per i VHD, sono sufficienti le autorizzazioni List e Read­. Evitare di fornire accesso con autorizzazioni di scrittura o eliminazione.
- La durata dell'accesso deve essere di un minimo di sette giorni lavorativi dalla creazione dell'URI di firma di accesso condiviso.
- Per evitare errori immediati dovuti a sfasamenti di orario, specificare un'ora di 15 minuti precedente rispetto all'ora corrente.

Per creare un URI di firma di accesso condiviso, è possibile seguire le istruzioni disponibili in [Firme di accesso condiviso, parte 1: Informazioni sul modello di firma di accesso condiviso][link-azure-1] e [Firme di accesso condiviso, parte 2: Creare e usare una firma di accesso condiviso con il servizio BLOB][link-azure-2].

Invece di generare una chiave di accesso condiviso con il codice, è possibile usare anche strumenti di archiviazione, ad esempio [Azure Storage Explorer][link-azure-codeplex].

**Usare Azure Storage Explorer per generare una chiave di accesso condiviso**

1. Scaricare [Azure Storage Explorer][link-azure-codeplex] 6 o versioni successive da CodePlex.
2. Al termine dell'installazione, aprire l'applicazione.
3. Fare clic su **Add Account**.

    ![disegno][img-azstg-add]

4. Specificare il nome dell'account di archiviazione, la chiave dell'account di archiviazione e il dominio degli endpoint di archiviazione. Non selezionare **Use HTTPS**.

    ![disegno][img-azstg-setup-1]

5. Azure Storage Explorer è ora connesso all'account di archiviazione specifico. e inizierà a mostrare tutti i contenitori nell'account di archiviazione. Selezionare il contenitore in cui è stato copiato il file VHD del disco del sistema operativo (nonché i dischi dati se sono applicabili allo scenario).

    ![disegno][img-azstg-setup-2]

6. Dopo aver selezionato il contenitore BLOB, i file all'interno del contenitore sono visibili in Azure Storage Explorer. Selezionare il file di immagine (vhd) da inviare.

    ![disegno][img-azstg-setup-3]

7. Dopo aver selezionato il file con estensione vhd nel contenitore, fare clic sulla scheda **Security**.

    ![disegno][img-azstg-setup-4]

8. Nella finestra di dialogo **Blob Container Security**, mantenere le impostazioni predefinite nella scheda **Access Level** e quindi fare clic sulla scheda **Shared Access Signatures**.

    ![disegno][img-azstg-setup-5]

9. Seguire questa procedura per generare un URI di firma di accesso condiviso per l'immagine con estensione vhd:

    ![disegno][img-azstg-setup-6]

    a. **Access permitted from**: per tenere conto dell'ora UTC, selezionare il giorno prima della data corrente. Ad esempio, se la data corrente è il 6 ottobre 2014 selezionare 5/10/2014.

    b. **Access permitted to**: selezionare una data che sia almeno 7 o 8 giorni successiva alla data **Access permitted from**.

    c. **Actions permitted**: selezionare le autorizzazioni **List** e **Read**.

    d. Se il file VHD è stato selezionato correttamente, in **Blob name to access** viene visualizzata l'estensione ".vhd".

    e. Fare clic su **Generate Signature**.

    f. In **Generated Shared Access Signature URI of this container** verificare quanto segue, come evidenziato sopra.

    - 	Assicurarsi che l'URL non inizi con "https".
    - 	Assicurarsi che il nome file di immagine e ".vhd" siano nell'URI.
    - 	Assicurarsi che "=rl" sia presente alla fine della firma. Questo indica che le autorizzazioni Read e List sono state fornite correttamente.

    g. Per verificare il corretto funzionamento dell'URI di firma di accesso condiviso generato, fare clic su **Test in Browser**. Verrà avviato il processo di download.
10. Copiare l'URI di firma di accesso condiviso. Questo URI deve essere incollato nel portale di pubblicazione.
11. Ripetere questi passaggi per ogni VHD nello SKU.

### 5\.3 Fornire le informazioni sull'immagine di macchina virtuale e richiedere la certificazione nel portale di pubblicazione
Dopo aver creato l'offerta e lo SKU, è necessario immettere i dettagli relativi all'immagine associati allo SKU.

1. Accedere al [portale di pubblicazione][link-pubportal] usando l'account venditore.
2. Selezionare la scheda **Immagini VM**.
3. L'identificatore visualizzato nella parte superiore della pagina è in realtà l'identificatore dell'offerta e non quello dello SKU.
4. Impostare le proprietà nella sezione **SKU**.
5. In **Famiglia di sistemi operativi** selezionare il tipo di sistema operativo associato al VHD del sistema operativo.
6. Nella casella **Sistema operativo** descrivere il sistema operativo. Usare preferibilmente il formato famiglia di sistemi operativi, tipo, versione e aggiornamenti. Esempio: "Windows Server Datacenter 2014 R2".
7. Selezionare fino a sei dimensioni della macchina virtuale. Questi consigli vengono visualizzati dal cliente nel pannello Piano tariffario del portale di Azure quando sceglie di acquistare e distribuire l'immagine. **Si tratta esclusivamente di consigli. Il cliente può selezionare qualsiasi dimensione di macchina virtuale appropriata ai dischi specificati nell'immagine.**
8. Immettere la versione. Il campo della versione incapsula una versione semantica per identificare il prodotto e gli aggiornamenti.
  -	Le versioni devono essere nel formato X.Y.Z, dove X, Y e Z sono numeri interi.
  -	Le immagini in SKU diversi possono avere versioni principali e secondarie diverse.
  -	Le versioni in uno SKU dovrebbero essere solo modifiche incrementali, che aumentano la versione della patch (da X.Y.Z a Z).
9. In **URL VHD sistema operativo** immettere l'URI di firma di accesso condiviso creato per il VHD del sistema operativo.
10. Se allo SKU sono associati dischi dati, selezionare il numero di unità logica (LUN) su cui deve essere montato il disco dati in fase di distribuzione.
11. In **URL VHD LUN X** immettere l'URI di firma di accesso condiviso creato per il primo VHD dei dati.

    ![disegno](media/marketplace-publishing-vm-image-creation/vm-image-pubportal-skus-3.png)

## Passaggio successivo
Dopo aver specificato i dettagli dello SKU, passare alla [guida dei contenuti marketing di Azure Marketplace][link-pushstaging]. In questo passaggio del processo di pubblicazione vengono forniti i contenuti marketing, i prezzi e le altre informazioni necessarie prima di continuare con il **Passaggio 3: Test dell'offerta di macchina virtuale nell'ambiente di staging**, dove vengono testati diversi scenari di casi d'uso prima di distribuire l'offerta in Azure Marketplace per la visibilità pubblica e l'acquisto.

## Vedere anche
- [Guida introduttiva: Come pubblicare un'offerta in Azure Marketplace](marketplace-publishing-getting-started.md)

[img-acom-1]: media/marketplace-publishing-vm-image-creation/vm-image-acom-datacenter.png
[img-portal-vm-size]: media/marketplace-publishing-vm-image-creation/vm-image-portal-size.png
[img-portal-vm-create]: media/marketplace-publishing-vm-image-creation/vm-image-portal-create-vm.png
[img-portal-vm-location]: media/marketplace-publishing-vm-image-creation/vm-image-portal-location.png
[img-portal-vm-rdp]: media/marketplace-publishing-vm-image-creation/vm-image-portal-rdp.png
[img-azstg-add]: media/marketplace-publishing-vm-image-creation/vm-image-storage-add.png
[img-azstg-setup-1]: media/marketplace-publishing-vm-image-creation/vm-image-storage-setup.png
[img-azstg-setup-2]: media/marketplace-publishing-vm-image-creation/vm-image-storage-setup-2.png
[img-azstg-setup-3]: media/marketplace-publishing-vm-image-creation/vm-image-storage-setup-3.png
[img-azstg-setup-4]: media/marketplace-publishing-vm-image-creation/vm-image-storage-setup-4.png
[img-azstg-setup-5]: media/marketplace-publishing-vm-image-creation/vm-image-storage-setup-5.png
[img-azstg-setup-6]: media/marketplace-publishing-vm-image-creation/vm-image-storage-setup-6.png
[img-manage-vm-new]: media/marketplace-publishing-vm-image-creation/vm-image-manage-new.png
[img-manage-vm-select]: media/marketplace-publishing-vm-image-creation/vm-image-manage-select.png
[img-cert-vm-key-lnx]: media/marketplace-publishing-vm-image-creation/vm-image-certification-keyfile-linux.png
[img-cert-vm-pswd-lnx]: media/marketplace-publishing-vm-image-creation/vm-image-certification-password-linux.png
[img-cert-vm-pswd-win]: media/marketplace-publishing-vm-image-creation/vm-image-certification-password-win.png
[img-cert-vm-test-lnx]: media/marketplace-publishing-vm-image-creation/vm-image-certification-test-sample-linux.png
[img-cert-vm-test-win]: media/marketplace-publishing-vm-image-creation/vm-image-certification-test-sample-win.png
[img-cert-vm-results]: media/marketplace-publishing-vm-image-creation/vm-image-certification-results.png
[img-cert-vm-questionnaire]: media/marketplace-publishing-vm-image-creation/vm-image-certification-questionnaire.png
[img-cert-vm-questionnaire-2]: media/marketplace-publishing-vm-image-creation/vm-image-certification-questionnaire-2.png
[img-pubportal-vm-skus]: media/marketplace-publishing-vm-image-creation/vm-image-pubportal-skus.png
[img-pubportal-vm-skus-2]: media/marketplace-publishing-vm-image-creation/vm-image-pubportal-skus-2.png

[link-pushstaging]: marketplace-publishing-push-to-staging.md
[link-github-waagent]: https://github.com/Azure/WALinuxAgent
[link-azure-codeplex]: https://azurestorageexplorer.codeplex.com/
[link-azure-2]: ../storage/storage-dotnet-shared-access-signature-part-2/
[link-azure-1]: ../storage/storage-dotnet-shared-access-signature-part-1/
[link-msft-download]: http://www.microsoft.com/download/details.aspx?id=44299
[link-technet-3]: https://technet.microsoft.com/library/hh846766.aspx
[link-technet-2]: https://msdn.microsoft.com/library/dn495261.aspx
[link-azure-portal]: https://portal.azure.com
[link-pubportal]: https://publish.windowsazure.com
[link-sql-2014-ent]: http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2014enterprisewindowsserver2012r2/
[link-sql-2014-std]: http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2014standardwindowsserver2012r2/
[link-sql-2014-web]: http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2014webwindowsserver2012r2/
[link-sql-2012-ent]: http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2012sp2enterprisewindowsserver2012/
[link-sql-2012-std]: http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2012sp2standardwindowsserver2012/
[link-sql-2012-web]: http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2012sp2webwindowsserver2012/
[link-datactr-2012-r2]: http://azure.microsoft.com/marketplace/partners/microsoft/windowsserver2012r2datacenter/
[link-datactr-2012]: http://azure.microsoft.com/marketplace/partners/microsoft/windowsserver2012datacenter/
[link-datactr-2008-r2]: http://azure.microsoft.com/marketplace/partners/microsoft/windowsserver2008r2sp1/
[link-acct-creation]: marketplace-publishing-accounts-creation-registration.md
[link-azure-vm-1]: ./virtual-machines-linux-create-upload-vhd/
[link-technet-1]: https://technet.microsoft.com/library/hh848454.aspx
[link-azure-vm-2]: ./virtual-machines-linux-agent-user-guide/
[link-openssl]: https://www.openssl.org/
[link-intsvc]: http://www.microsoft.com/download/details.aspx?id=41554
[link-python]: https://www.python.org/

<!---HONumber=AcomDC_0309_2016-->