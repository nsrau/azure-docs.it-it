---
title: Migrazione della serie StorSimple 8000 a Sincronizzazione file di Azure
description: Informazioni su come eseguire la migrazione di un'appliance StorSimple 8100 o 8600 a Sincronizzazione file di Azure.
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 10/16/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: dcb690829aaed1d5a256f53766f05c922f8b2a9a
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92331115"
---
# <a name="storsimple-8100-and-8600-migration-to-azure-file-sync"></a>StorSimple 8100 e 8600 migrazione a Sincronizzazione file di Azure

La serie StorSimple 8000 viene rappresentata dal 8100 o dalle appliance fisiche, locali e dai componenti del servizio cloud 8600. È possibile eseguire la migrazione dei dati da uno di questi Appliance a un ambiente Sincronizzazione file di Azure. Sincronizzazione file di Azure è il servizio di Azure a lungo termine predefinito e strategico a cui è possibile eseguire la migrazione delle appliance StorSimple.

La serie StorSimple 8000 raggiungerà la [fine del ciclo di vita](https://support.microsoft.com/en-us/lifecycle/search?alpha=StorSimple%208000%20Series) nel 2022 dicembre. È importante iniziare a pianificare la migrazione appena possibile. Questo articolo fornisce i passaggi necessari per la Knowledge base e le migrazioni per una migrazione corretta a Sincronizzazione file di Azure. 

## <a name="phase-1-prepare-for-migration"></a>Fase 1: preparare la migrazione

Questa sezione contiene i passaggi da eseguire all'inizio della migrazione da volumi StorSimple a condivisioni file di Azure.

### <a name="inventory"></a>Argomento
Quando si inizia a pianificare la migrazione, identificare prima tutti i dispositivi e i volumi StorSimple di cui è necessario eseguire la migrazione. Una volta completata questa operazione, è possibile scegliere il percorso di migrazione migliore.

* StorSimple appliance fisiche (serie 8000) usare questa guida alla migrazione. 
* Le appliance virtuali, [StorSimple serie 1200, usano un'altra guida alla migrazione](storage-files-migration-storsimple-1200.md).

### <a name="migration-cost-summary"></a>Riepilogo dei costi della migrazione
Le migrazioni a condivisioni file di Azure da volumi StorSimple tramite processi di trasformazione dei dati in una risorsa StorSimple Data Manager sono gratuite. Tuttavia, esistono altri costi che possono essere sostenuti durante e dopo una migrazione:

* **Rete in uscita:** I file StorSimple risiedono in un account di archiviazione all'interno di una specifica area di Azure. Se si effettua il provisioning delle condivisioni file di Azure di cui si esegue la migrazione in un account di archiviazione situato nella stessa area di Azure, non si verificherà alcun costo in uscita. È possibile spostare i file in un account di archiviazione in un'area diversa come parte della migrazione. In tal caso, verranno applicati i costi in uscita.
* **Transazioni di condivisione file di Azure:** Quando i file vengono copiati in una condivisione file di Azure (come parte di una migrazione o all'esterno di uno), i costi delle transazioni vengono applicati come file e metadati scritti. Come procedura consigliata, avviare la condivisione file di Azure nel livello ottimizzato per le transazioni durante la migrazione. Passare al livello desiderato al termine della migrazione. Le fasi seguenti chiameranno questo argomento nel punto appropriato.
* **Modifica di un livello di condivisione file di Azure:** La modifica del livello di una condivisione file di Azure costa le transazioni. Nella maggior parte dei casi, sarà più conveniente seguire il Consiglio del punto precedente.
* **Costo di archiviazione:** Quando questa migrazione inizia a copiare i file in una condivisione file di Azure, File di Azure archiviazione viene utilizzata e fatturata. 
* **StorSimple:** Fino a quando non si ha la possibilità di effettuare il deprovisioning dei dispositivi StorSimple e degli account di archiviazione, il costo di StorSimple per archiviazione, backup e appliance continuerà a verificarsi.

### <a name="direct-share-access-vs-azure-file-sync"></a>Confronto tra accesso diretto e Sincronizzazione file di Azure
Le condivisioni file di Azure aprono un mondo completamente nuovo per la strutturazione della distribuzione dei servizi file. Una condivisione file di Azure è semplicemente una condivisione SMB nel cloud, che è possibile configurare per consentire agli utenti di accedere direttamente tramite il protocollo SMB con la nota autenticazione Kerberos e le autorizzazioni NTFS esistenti (ACL di file e cartelle) che funzionano in modo nativo. [Altre informazioni sull'accesso basato sulle identità alle condivisioni file di Azure](storage-files-active-directory-overview.md).

Un'alternativa all'accesso diretto è un [sincronizzazione file di Azure](https://aka.ms/AFS). Sincronizzazione file di Azure è un analogo diretto per la capacità di StorSimple di memorizzare nella cache i file usati di frequente in locale. 

Sincronizzazione file di Azure è un servizio cloud Microsoft basato su due componenti principali:

* Sincronizzazione di file e suddivisione in livelli nel cloud.
* Condivisioni file come archiviazione nativa in Azure a cui è possibile accedere tramite più protocolli, ad esempio SMB e REST di file. 

Le condivisioni file di Azure mantengono importanti aspetti di fedeltà dei file nei file archiviati, ad esempio attributi, autorizzazioni e timestamp. Con le condivisioni file di Azure non è più necessario che un'applicazione o un servizio interpreti i file e le cartelle archiviati nel cloud. È possibile accedervi in modo nativo su protocolli e client noti, ad esempio Esplora file di Windows. Le condivisioni file di Azure consentono di archiviare i dati file server di uso generico e i dati delle applicazioni nel cloud. Il backup di una condivisione file di Azure è una funzionalità incorporata che può essere ulteriormente migliorato da backup di Azure.

Questo articolo è incentrato sulla procedura di migrazione. Se prima della migrazione si desidera ottenere altre informazioni su Sincronizzazione file di Azure, è consigliabile usare gli articoli seguenti:

* [Sincronizzazione file di Azure-Panoramica](https://aka.ms/AFS "Panoramica")
* [Guida alla distribuzione di Sincronizzazione file di Azure](storage-sync-files-deployment-guide.md)

### <a name="storsimple-service-data-encryption-key"></a>Chiave di crittografia dei dati del servizio StorSimple
Quando si configura il dispositivo StorSimple per la prima volta, viene generata una*chiave di crittografia dei dati del servizio*e viene richiesto di archiviare in modo sicuro la chiave.
Questa chiave viene usata per crittografare tutti i dati nell'account di archiviazione di Azure associato, in cui il dispositivo StorSimple archivia i file.

La chiave di crittografia dei dati del servizio è necessaria per una migrazione corretta. Ora è il momento giusto per recuperare questa chiave dai record, per ogni appliance nell'inventario.

Se non è possibile trovare le chiavi nei record, è possibile recuperare la chiave dal dispositivo. Ogni appliance ha una chiave di crittografia univoca.

* Archiviare una richiesta di supporto con Microsoft Azure tramite il portale di Azure. Il contenuto della richiesta deve avere i numeri di serie del dispositivo StorSimple e la richiesta di recupero della chiave di crittografia dei dati del servizio. 
* Un tecnico del supporto StorSimple contatterà l'utente con una richiesta di riunione per la condivisione dello schermo.
* Assicurarsi che prima della riunione venga avviata la connessione all'appliance StorSimple [tramite una console seriale](../../storsimple/storsimple-8000-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console) o tramite una [sessione remota di PowerShell](../../storsimple/storsimple-8000-windows-powershell-administration.md#connect-remotely-to-storsimple-using-windows-powershell-for-storsimple). 

> [!CAUTION]
> Quando si decide come connettersi al dispositivo StorSimple, tenere presente quanto segue:
> * La connessione tramite una sessione HTTPS è l'opzione più sicura e consigliata.
> * La connessione diretta alla console seriale del dispositivo è protetta ma la connessione alla console seriale sugli switch di rete non lo è. 
> * Le connessioni di sessione HTTP sono un'opzione, ma **non sono crittografate** e pertanto non sono consigliate, a meno che non vengano usate all'interno di una rete chiusa e attendibile.

### <a name="storsimple-volume-backups"></a>Backup del volume StorSimple
StorSimple offre backup differenziali a livello di volume. Le condivisioni file di Azure hanno anche questa possibilità, denominate snapshot di condivisione.

Decidere se come parte della migrazione è anche necessario spostare i backup.

> [!CAUTION]
> Se è necessario eseguire la migrazione dei backup da volumi StorSimple, fermarsi qui.
>
> Attualmente è possibile eseguire solo la migrazione del backup del volume più recente. Il supporto per la migrazione del backup arriverà alla fine del 2020.
> Se si inizia ora, non è possibile "Bolt-on" nei backup in un secondo momento.
> Nella prossima versione, i backup devono essere "riprodotti" nelle condivisioni file di Azure dal meno recente al più recente, con gli snapshot di condivisione file di Azure presi tra loro.

Se si desidera eseguire solo la migrazione dei dati in tempo reale e non sono previsti requisiti per i backup, è possibile continuare seguendo questa guida.
Se si dispone solo di un requisito di conservazione dei backup a breve termine, ovvero un mese o due, è possibile decidere di continuare la migrazione e di effettuare il deprovisioning delle risorse StorSimple solo dopo tale periodo. Questo approccio consente di creare la stessa cronologia di backup nel lato condivisione file di Azure in modo analogo a quanto necessario. Per il periodo in cui vengono eseguiti entrambi i sistemi, viene applicato un costo aggiuntivo, che non è un approccio da considerare se è necessaria una conservazione dei backup a breve termine.

### <a name="map-your-existing-storsimple-volumes-to-azure-file-shares"></a>Eseguire il mapping dei volumi StorSimple esistenti alle condivisioni file di Azure
[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

### <a name="number-of-storage-accounts"></a>Numero di account di archiviazione
La migrazione probabilmente trarrà vantaggio da una distribuzione di più account di archiviazione, ognuno dei quali conserva un numero inferiore di condivisioni file di Azure.

Se le condivisioni file sono estremamente attive (usate da molti utenti e/o applicazioni), solo due condivisioni file di Azure potrebbero raggiungere il limite delle prestazioni dell'account di archiviazione. Per questo motivo, la procedura consigliata consiste nel migrare a più account di archiviazione, ognuno con le proprie singole condivisioni file e in genere non più di due o tre condivisioni per ogni account di archiviazione.

Una procedura consigliata consiste nel distribuire gli account di archiviazione con una condivisione file ciascuna. È possibile raggruppare più condivisioni file di Azure nello stesso account di archiviazione, nel caso in cui siano presenti condivisioni di archiviazione.

Queste considerazioni si applicano a un [accesso diretto al cloud](#direct-share-access-vs-azure-file-sync) (tramite una macchina virtuale o un servizio di Azure) rispetto a sincronizzazione file di Azure. Se si prevede di usare Sincronizzazione file di Azure solo in queste condivisioni, il raggruppamento di più in un unico account di archiviazione di Azure è corretto. Si prenda in considerazione anche la possibilità di spostare un'app nel cloud, in modo da accedere direttamente a una condivisione file. In alternativa, è possibile iniziare a usare un servizio in Azure che può anche trarre vantaggio da un numero maggiore di IOPS e velocità effettiva disponibili. 

Se è stato creato un elenco delle condivisioni, è necessario eseguire il mapping di ogni condivisione all'account di archiviazione in cui risiederà.

> [!IMPORTANT]
> Assicurarsi di scegliere un'area di Azure e quindi ogni account di archiviazione e Sincronizzazione file di Azure risorsa corrispondono alla stessa area selezionata.

### <a name="phase-1-summary"></a>Riepilogo fase 1
Alla fine della fase 1:
* Si dispone di una panoramica corretta dei dispositivi e dei volumi di StorSimple.
* Il servizio Data Transformation è pronto ad accedere ai volumi StorSimple nel cloud, perché è stata recuperata la chiave di crittografia dei dati del servizio per ogni dispositivo StorSimple.
* Si dispone di un piano non solo per i volumi di cui è necessario eseguire la migrazione, ma anche come eseguire il mapping dei volumi al numero appropriato di condivisioni file di Azure e account di archiviazione.

> [!CAUTION]
> Se è necessario eseguire la migrazione dei backup da volumi StorSimple, **arrestare qui**.
>
> Questo approccio alla migrazione si basa sulle nuove funzionalità del servizio di trasformazione dei dati che attualmente non possono eseguire la migrazione dei backup. Il supporto per la migrazione del backup arriverà alla fine del 2020.
> Attualmente è possibile eseguire solo la migrazione dei dati in tempo reale. Se si inizia ora, non è possibile "Bolt-on" nei backup in un secondo momento.
> I backup devono essere "riprodotti" nelle condivisioni file di Azure dai dati più vecchi a quelli più recenti, con gli snapshot di condivisione file di Azure.

Se si desidera eseguire solo la migrazione dei dati in tempo reale e non sono previsti requisiti per i backup, è possibile continuare seguendo questa guida.

## <a name="phase-2-deploying-azure-storage-and-migration-resources"></a>Fase 2: distribuzione delle risorse di archiviazione e migrazione di Azure

Questa sezione illustra le considerazioni relative alla distribuzione dei diversi tipi di risorse necessari in Azure. Alcuni manterranno i dati dopo la migrazione e alcuni sono necessari esclusivamente per la migrazione. Non iniziare a distribuire le risorse fino a quando non è stato finalizzato il piano di distribuzione. È difficile, a volte impossibile, modificare alcuni aspetti delle risorse di Azure dopo la distribuzione.

### <a name="deploy-storage-accounts"></a>Distribuire gli account di archiviazione
Probabilmente sarà necessario distribuire diversi account di archiviazione di Azure. Ognuno di essi conterrà un numero inferiore di condivisioni file di Azure, in base al piano di distribuzione, completato nella sezione precedente di questo articolo. È stato portale di Azure [distribuire gli account di archiviazione pianificati](../common/storage-account-create.md#create-a-storage-account). Si consiglia di attenersi alle seguenti impostazioni di base per qualsiasi nuovo account di archiviazione:

##### <a name="subscription"></a>Subscription
È possibile usare la stessa sottoscrizione usata per la distribuzione di StorSimple o un altro. L'unica limitazione è che la sottoscrizione deve trovarsi nello stesso tenant Azure AD della sottoscrizione StorSimple. Prima di avviare una migrazione, provare a trasferire la sottoscrizione StorSimple al tenant corretto. È possibile spostare solo l'intera sottoscrizione. le singole risorse StorSimple non possono essere spostate in un tenant o in una sottoscrizione diversa.

##### <a name="resource-group"></a>Resource group
I gruppi di risorse sono in aiuto con l'organizzazione delle risorse e le autorizzazioni di gestione amministratore. Scopri di più sui [gruppi di risorse in Azure](../../azure-resource-manager/management/manage-resource-groups-portal.md#what-is-a-resource-group).

##### <a name="storage-account-name"></a>Nome account di archiviazione
Il nome dell'account di archiviazione diventerà parte di un URL e avrà alcune limitazioni relative ai caratteri. Nella convenzione di denominazione, tenere presente che i nomi degli account di archiviazione devono essere univoci in tutto il mondo, consentire solo lettere minuscole e numeri, richiedere da 3 a 24 caratteri e non consentire caratteri speciali come trattini o caratteri di sottolineatura. Vedere anche: [regole di denominazione delle risorse di archiviazione di Azure](../../azure-resource-manager/management/resource-name-rules.md#microsoftstorage).

##### <a name="location"></a>Percorso
Il "percorso" o l'area di Azure di un account di archiviazione è molto importante. Se si usa Sincronizzazione file di Azure, tutti gli account di archiviazione devono trovarsi nella stessa area della risorsa del servizio di sincronizzazione archiviazione. L'area di Azure scelta deve essere vicina o centrale ai server/utenti locali. Una volta distribuita la risorsa, non è possibile modificarne l'area.

È possibile selezionare un'area diversa da quella in cui si trovano i dati StorSimple (account di archiviazione).

> [!IMPORTANT]
> Se si seleziona un'area diversa dalla posizione corrente dell'account di archiviazione StorSimple, [verranno applicati gli addebiti in uscita](https://azure.microsoft.com/pricing/details/bandwidth) durante la migrazione. I dati lasceranno l'area StorSimple e immetteranno la nuova area dell'account di archiviazione. Non vengono applicati addebiti per la larghezza di banda se si rimane nella stessa area di Azure.

##### <a name="performance"></a>Prestazioni
È possibile scegliere archiviazione Premium (SSD) per le condivisioni file di Azure o per l'archiviazione standard. Archiviazione standard include [diversi livelli per una condivisione file](storage-how-to-create-file-share.md#changing-the-tier-of-an-azure-file-share). Archiviazione standard è l'opzione ideale per la maggior parte dei clienti che eseguono la migrazione da StorSimple.

Non si è ancora sicuri?
* Se sono necessarie le [prestazioni di una condivisione file di Azure Premium](storage-files-planning.md#understanding-provisioning-for-premium-file-shares), scegliere archiviazione Premium.
* Scegliere archiviazione standard per i carichi di lavoro file server per utilizzo generico, inclusi i dati ad accesso frequente e i dati di archiviazione. Scegliere anche archiviazione standard se verrà Sincronizzazione file di Azure il solo carico di lavoro nella condivisione nel cloud.

##### <a name="account-kind"></a>Tipologia account
* Per l'archiviazione standard, scegliere: *archiviazione V2 (utilizzo generico v2)*
* Per le condivisioni file Premium, scegliere: *filestorage*

##### <a name="replication"></a>Replica
Sono disponibili diverse impostazioni di replica. Altre informazioni sui diversi tipi di replica.

Scegliere una delle due opzioni seguenti:
* *Archiviazione con ridondanza locale (LRS)*
* *Archiviazione con ridondanza della zona (ZRS)* , che non è disponibile in tutte le aree di Azure.

> [!NOTE]
> Solo i tipi di ridondanza con ridondanza locale e ZRS sono compatibili con le condivisioni file di Azure di grandi dimensioni, con capacità 100TiB.

L'archiviazione con ridondanza globale (tutte le varianti) non è attualmente supportata. È possibile cambiare il tipo di ridondanza in un secondo momento e in GRS quando il supporto per l'it arriverà in Azure.

##### <a name="enable-100tib-capacity-file-shares"></a>Abilita condivisioni file di capacità 100TiB

:::row:::
    :::column:::
        :::image type="content" source="media/storage-files-how-to-create-large-file-share/large-file-shares-advanced-enable.png" alt-text="Immagine che mostra la scheda Avanzate nella portale di Azure per la creazione di un account di archiviazione.":::
    :::column-end:::
    :::column:::
        Nella sezione *Avanzate* della creazione guidata nuovo account di archiviazione nel portale di Azure è possibile abilitare il supporto per *condivisioni di file di grandi dimensioni* in questo account di archiviazione. Se questa opzione non è disponibile, è molto probabile che sia stato selezionato il tipo di ridondanza errato. Assicurarsi di selezionare solo con ridondanza locale o ZRS per rendere disponibile questa opzione.
    :::column-end:::
:::row-end:::

Optare per le condivisioni file di grande capacità di 100TiB offre diversi vantaggi:
* Le prestazioni sono notevolmente aumentate rispetto alle condivisioni file di capacità 5TiB più piccole. (ad esempio: 10 volte il IOPS)
* Il completamento della migrazione sarà notevolmente più veloce.
* Assicurarsi che la condivisione file disponga di una capacità sufficiente a contenere tutti i dati di cui si eseguirà la migrazione.
* Viene analizzata la crescita futura.

### <a name="azure-file-shares"></a>Condivisioni file di Azure
Una volta creati gli account di archiviazione, è possibile passare alla sezione *"condivisione file"* dell'account di archiviazione e distribuire il numero appropriato di condivisioni file di Azure in base al piano di migrazione della fase 1. Si consiglia di attenersi alle seguenti impostazioni di base per le nuove condivisioni file in Azure:

:::row:::
    :::column:::
        :::image type="content" source="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-new-share.png" alt-text="Immagine che mostra la scheda Avanzate nella portale di Azure per la creazione di un account di archiviazione.":::
    :::column-end:::
    :::column:::
        </br>**Nome**</br>Sono supportate lettere minuscole, numeri e trattini.</br></br>**Quota**</br>La quota qui è paragonabile alla quota rigida SMB in un server Windows. La procedura consigliata consiste nel non impostare una quota perché la migrazione e altri servizi avranno esito negativo quando viene raggiunta la quota.</br></br>**Livello**</br>Selezionare *Transaction optimized* per la nuova condivisione file. Durante la migrazione, si verificheranno molte transazioni ed è più conveniente modificare il livello in un secondo momento fino al livello più adatto al carico di lavoro.
    :::column-end:::
:::row-end:::

### <a name="storsimple-data-manager"></a>StorSimple Data Manager
La risorsa di Azure che conterrà i processi di migrazione è detta *"StorSimple Data Manager"*. Fare clic su nuova risorsa e cercarla, quindi fare clic su *Crea*.

Si tratta di una risorsa temporanea utilizzata per l'orchestrazione, che verrà sottoposta a deprovisioning al termine della migrazione. Deve essere distribuito nella stessa sottoscrizione, gruppo di risorse e area geografica dell'account di archiviazione StorSimple.

### <a name="azure-file-sync"></a>Sincronizzazione file di Azure
Sincronizzazione file di Azure (AFS) consente di aggiungere la memorizzazione nella cache locale dei file a cui si accede più di frequente. Analogamente alle capacità di memorizzazione nella cache di StorSimple, la funzionalità di suddivisione in livelli nel cloud AFS offre latenza di accesso locale in combinazione con un controllo migliorato sulla capacità della cache disponibile in Windows Server e nella sincronizzazione multisito. Se l'obiettivo è la cache locale, nella rete locale preparare una macchina virtuale Windows Server (sono supportati anche i server fisici e i cluster di failover), con una capacità di "DAS (Direct Attached Storage)" sufficiente. 

> [!IMPORTANT]
> Non configurare Sincronizzazione file di Azure ancora, è consigliabile configurare Sincronizzazione file di Azure dopo il completamento della migrazione della condivisione. La distribuzione di AFS non dovrebbe essere avviata prima della fase 4 di una migrazione.

### <a name="phase-2-summary"></a>Riepilogo fase 2
Alla fine della fase 2, sono stati distribuiti gli account di archiviazione, tutte le condivisioni file di Azure tra di essi e anche una risorsa StorSimple Data Manager. Si userà quest'ultimo nella fase 3, quando si configurano effettivamente i processi di migrazione.

## <a name="phase-3-create-and-run-a-migration-job"></a>Fase 3: creare ed eseguire un processo di migrazione
Questa sezione descrive come configurare un processo di migrazione e mappare attentamente le directory in un volume StorSimple che deve essere copiato nella condivisione file di Azure di destinazione selezionata. Per iniziare, passare alla StorSimple Data Manager, individuare le **definizioni dei processi** nel menu e selezionare **+ definizione processo**. Il tipo di archiviazione di destinazione è l'impostazione predefinita: *condivisione file di Azure*.

![Tipi di processo di migrazione serie 8000 di StorSimple.](media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-new-job-type.png "Una schermata delle definizioni dei processi portale di Azure con una nuova finestra di dialogo Definizione processo aperta, che richiede il tipo di processo: copia in una condivisione file o in un contenitore BLOB.")

> [!IMPORTANT]
> Prima di eseguire qualsiasi processo di migrazione, arrestare tutti i backup pianificati automaticamente dei volumi di StorSimple.

:::row:::
    :::column:::
        ![Processo di migrazione serie StorSimple 8000.](media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-new-job.png "Screenshot del nuovo modulo per la creazione di processi per un processo di trasformazione dei dati.")
    :::column-end:::
    :::column:::
        **Nome definizione processo**</br>Questo nome deve essere indicativo del set di file da trasferire. Assegnare un nome simile alla condivisione file di Azure è una procedura consigliata. </br></br>**Località in cui viene eseguito il processo**</br>Quando si seleziona un'area, è necessario selezionare la stessa area dell'account di archiviazione di StorSimple o, se non è disponibile, un'area vicina. </br></br><h3>Source (Sorgente)</h3>**Sottoscrizione di origine**</br>Selezionare la sottoscrizione in cui archiviare la risorsa di Device Manager StorSimple. </br></br>**Risorsa StorSimple**</br>Selezionare la StorSimple Device Manager il dispositivo è registrato con. </br></br>**Chiave DEK del servizio**</br>Controllare questa [sezione precedente di questo articolo](#storsimple-service-data-encryption-key), nel caso in cui non sia possibile individuare la chiave nei record. </br></br>**Dispositivo**</br>Selezionare il dispositivo StorSimple che include il volume in cui si vuole eseguire la migrazione. </br></br>**Volume**</br>Selezionare il volume di origine. In un secondo momento si decide se si vuole eseguire la migrazione dell'intero volume o delle sottodirectory nella condivisione file di Azure di destinazione. </br></br><h3>Destinazione</h3>Selezionare la sottoscrizione, l'account di archiviazione e la condivisione file di Azure come destinazione del processo di migrazione.
    :::column-end:::
:::row-end:::

> [!IMPORTANT]
> Per eseguire la migrazione verrà usato il backup del volume più recente. Verificare che sia presente almeno un backup del volume oppure il processo non riuscirà. Assicurarsi inoltre che il backup più recente sia abbastanza recente, per mantenere il Delta alla condivisione Live il più piccolo possibile. Potrebbe valere la pena attivare e completare manualmente un altro backup del volume **prima** di eseguire il processo appena creato.

### <a name="directory-mapping"></a>Mapping della directory
Questa operazione è facoltativa per il processo di migrazione. Se si lascia vuota, **tutti** i file e le cartelle nella radice del volume StorSimple verranno spostati nella radice della condivisione file di Azure di destinazione. Nella maggior parte dei casi, l'archiviazione del contenuto di un intero volume in una condivisione file di Azure non è l'approccio migliore. Spesso è preferibile suddividere il contenuto di un volume tra più condivisioni file in Azure. Se non è già stato fatto, vedere prima questa sezione: [eseguire il mapping del volume StorSimple alle condivisioni file di Azure](#map-your-existing-storsimple-volumes-to-azure-file-shares)

Come parte del piano di migrazione, è possibile che sia stata decisa la suddivisione delle cartelle in un volume StorSimple tra più condivisioni file di Azure. In tal caso, è possibile eseguire la suddivisione in base a quanto segue:
1. Definendo più processi per eseguire la migrazione delle cartelle in un volume, ciascuna avrà la stessa origine del volume StorSimple, ma una condivisione file di Azure diversa come destinazione.
1. Specificando esattamente le cartelle del volume StorSimple di cui è necessario eseguire la migrazione nella condivisione file specificata, usando la sezione *mapping directory* del modulo di creazione del processo e seguendo la [semantica di mapping](#semantic-elements)specifica.

> [!IMPORTANT]
> Non è possibile convalidare i percorsi e le espressioni di mapping in questo form quando viene inviato il modulo. Se i mapping non sono specificati correttamente, un processo può avere esito negativo completamente o produrre un risultato indesiderato. In tal caso, è in genere consigliabile eliminare la condivisione file di Azure, ricrearla e quindi correggere le istruzioni di mapping in un nuovo processo di migrazione per la condivisione. L'esecuzione di un nuovo processo con un'istruzione fixed mapping può correggere le cartelle omesse e portarle nella condivisione esistente. Tuttavia, in questo modo è possibile risolvere solo le cartelle omesse a causa di errori di ortografia del percorso.

#### <a name="semantic-elements"></a>Elementi semantici
Un mapping è espresso da sinistra a destra: [percorso \Source] \> [percorso \target].

|Carattere semantico          | Significato  |
|:---------------------------|:---------|
| **\\**                     | indicatore del livello radice        |
| **\>**                     | [origine] e [destinazione] operatore di mapping        |
|**\|** o RETURN (nuova riga) | separatore di due istruzioni per il mapping di cartelle. </br>In alternativa, è possibile omettere questo carattere e premere INVIO per ottenere l'espressione di mapping successiva sulla propria riga.        |

### <a name="examples"></a>Esempi
Sposta il contenuto della cartella "User Data" nella radice della condivisione file di destinazione:
``` console
\User data > \\
```
Sposta l'intero contenuto del volume in un nuovo percorso nella condivisione file di destinazione:
``` console
\ \> \Apps\HR tracker
```
Sposta il contenuto della cartella di origine in un nuovo percorso nella condivisione file di destinazione:
``` console
\HR resumes-Backup \> \Backups\HR\resumes
```
Ordina più percorsi di origine in una nuova struttura di directory:
``` console
\HR\Candidate Tracker\v1.0 > \Apps\Candidate tracker
\HR\Candidates\Resumes > \HR\Candidates\New
\Archive\HR\Old Resumes > \HR\Candidates\Archived
```

### <a name="semantic-rules"></a>Regole semantiche
* Specificare sempre i percorsi di cartella relativi al livello radice. 
* Inizia ogni percorso di cartella con un indicatore di livello radice " \" . 
* Non includere lettere di unità. 
* Quando si specificano più percorsi, i percorsi di origine o di destinazione non possono sovrapporsi:</br>
   Esempio di sovrapposizione del percorso di origine non valido:</br>
    *\\folder\1 > \\ cartella*</br>
    *\\cartella \\ 1 \\ 2 > \\ Cartella2*</br>
   Esempio di sovrapposizione percorso di destinazione non valido:</br>
   *\\> cartella \\*</br>
   *\\> Cartella2 \\*</br>
* Le cartelle di origine che non esistono verranno ignorate. 
* Verranno create le strutture di cartelle che non esistono nella destinazione. 
* Analogamente a Windows: i nomi di cartella non fanno distinzione tra maiuscole e minuscole.

> [!NOTE]
> il contenuto della cartella "*\System Volume Information*" e del "*$recycle. bin*" sul volume StorSimple non verrà copiato dal processo di trasformazione.

### <a name="phase-3-summary"></a>Riepilogo fase 3
Alla fine della fase 3, i processi di trasformazione dei dati vengono eseguiti da volumi StorSimple in condivisioni file di Azure. È ora possibile concentrarsi sulla configurazione di Sincronizzazione file di Azure per la condivisione (dopo il completamento dei processi di migrazione per una condivisione) o l'accesso diretto alle app per gli Information Worker e alla condivisione file di Azure.

## <a name="phase-4-accessing-your-azure-file-shares"></a>Fase 4: accesso alle condivisioni file di Azure

Esistono due strategie principali per accedere alle condivisioni file di Azure:

* **Sincronizzazione file di Azure:** [come distribuire Sincronizzazione file di Azure](#deploy-azure-file-sync) in un server Windows locale. AFS offre tutti i vantaggi di una cache locale, proprio come StorSimple.
* **Accesso diretto alla condivisione:** [come distribuire Direct-Share-Access](#deploy-direct-share-access). Usare questa strategia se lo scenario di accesso per una condivisione file di Azure specificata non trarrà vantaggio dalla memorizzazione nella cache locale o non è più possibile ospitare un server Windows locale. In questo caso, gli utenti e le app continueranno ad accedere alle condivisioni SMB tramite il protocollo SMB, ma queste condivisioni non si trovano più in un server locale, ma direttamente nel cloud.

È necessario avere già deciso quale opzione è più adatta alle proprie esigenze, nella [fase 1](#phase-1-prepare-for-migration) di questa guida.

Il resto di questa sezione è incentrato sulle istruzioni per la distribuzione.

### <a name="deploy-azure-file-sync"></a>Distribuire Sincronizzazione file di Azure

È il momento di distribuire una parte del Sincronizzazione file di Azure:
1. Creare la risorsa Sincronizzazione file di Azure cloud.
1. Distribuire l'agente di Sincronizzazione file di Azure nel server locale.
1. Registrare il server con la risorsa cloud

Non creare ancora gruppi di sincronizzazione. La configurazione della sincronizzazione con una condivisione file di Azure deve essere eseguita solo dopo il completamento dei processi di migrazione a una condivisione file di Azure. Se si desidera iniziare a utilizzare Sincronizzazione file prima del completamento della migrazione, la migrazione renderà inutilmente difficoltosa poiché non è possibile stabilire facilmente quando è il momento di avviare un taglio.

#### <a name="deploy-the-azure-file-sync-cloud-resource"></a>Distribuire la risorsa Sincronizzazione file di Azure cloud

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

> [!TIP]
> Se si desidera modificare l'area di Azure in cui si trovano i dati al termine della migrazione, distribuire il servizio di sincronizzazione archiviazione nella stessa area degli account di archiviazione di destinazione per la migrazione.

#### <a name="deploy-an-on-premises-windows-server"></a>Distribuire un server Windows locale

* Creare una macchina virtuale di Windows Server 2019, almeno 2012R2, come macchina virtuale o server fisico. È supportato anche un cluster di failover di Windows Server. Non riutilizzare il server che precede StorSimple 8100 o 8600.
* Effettuare il provisioning o aggiungere l'archiviazione collegata direttamente (DAS rispetto a NAS, che non è supportata).

È consigliabile assegnare al nuovo Windows Server una quantità di spazio di archiviazione uguale o superiore a quella del dispositivo StorSimple 8100 o 8600 disponibile localmente per la memorizzazione nella cache. Si userà Windows Server nello stesso modo in cui è stato usato il dispositivo StorSimple, se ha la stessa quantità di spazio di archiviazione dell'appliance, l'esperienza di memorizzazione nella cache dovrebbe essere simile, in caso contrario.
È possibile aggiungere o rimuovere lo spazio di archiviazione dal server Windows in base alle esigenze. In questo modo è possibile ridimensionare le dimensioni del volume locale e la quantità di spazio di archiviazione locale disponibile per la memorizzazione nella cache.

#### <a name="prepare-the-windows-server-for-file-sync"></a>Preparare Windows Server per la sincronizzazione dei file

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

#### <a name="configure-azure-file-sync-on-the-windows-server"></a>Configurare Sincronizzazione file di Azure in Windows Server

Per questo processo, è necessario che il server Windows locale registrato sia pronto e connesso a Internet.

> [!IMPORTANT]
> Prima di procedere, è necessario completare la migrazione StorSimple di file e cartelle nella condivisione file di Azure. Assicurarsi che non siano state apportate altre modifiche alla condivisione file.

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!IMPORTANT]
> Assicurarsi di attivare la suddivisione in livelli nel cloud. La suddivisione in livelli nel cloud è la funzionalità Sincronizzazione file di Azure che consente al server locale di avere una capacità di archiviazione inferiore a quella archiviata nel cloud, ma è disponibile lo spazio dei nomi completo. I dati localmente interessanti vengono anche memorizzati nella cache localmente per ottenere prestazioni di accesso locale rapide. Un altro motivo per attivare la suddivisione in livelli nel cloud in questo passaggio è che non si vuole sincronizzare il contenuto del file in questa fase, ma è necessario che lo spazio dei nomi venga spostato in questo momento.

### <a name="deploy-direct-share-access"></a>Distribuire Direct-Share-Access

:::row:::
    :::column:::
        [![Guida dettagliata e demo su come esporre in modo sicuro le condivisioni file di Azure direttamente agli Information Worker e alle app. fare clic per riprodurre.](./media/storage-files-migration-storsimple-8000/azure-files-direct-access-video-placeholder.png)](https://youtu.be/KG0OX0RgytI)
    :::column-end:::
    :::column:::
        Questo video è una guida e una demo su come esporre in modo sicuro le condivisioni file di Azure direttamente agli Information Worker e alle app in cinque semplici passaggi.</br>
        Il video fa riferimento alla documentazione dedicata per alcuni argomenti:
* [Panoramica dell'identità](storage-files-active-directory-overview.md)
* [Come aggiungere un account di archiviazione a un dominio](storage-files-identity-auth-active-directory-enable.md)
* [Panoramica della rete per le condivisioni file di Azure](storage-files-networking-overview.md)
* [Come configurare gli endpoint pubblici e privati](storage-files-networking-endpoints.md)
* [Come configurare una VPN S2S](storage-files-configure-s2s-vpn.md)
* [Come configurare una VPN P2S Windows](storage-files-configure-p2s-vpn-windows.md)
* [Come configurare una VPN P2S Linux](storage-files-configure-p2s-vpn-linux.md)
* [Come configurare l'invio DNS](storage-files-networking-dns.md)
* [Configurare DFS-N](https://aka.ms/AzureFiles/Namespaces)
   :::column-end:::
:::row-end:::

### <a name="phase-4-summary"></a>Riepilogo della fase 4
In questa fase sono stati creati ed eseguiti più processi DTS ( *Data Transformation Service* ) nel *StorSimple Data Manager*. Questi processi hanno eseguito la migrazione dei file e delle cartelle alle condivisioni file di Azure. È stata inoltre distribuita una Sincronizzazione file di Azure o sono stati preparati gli account di archiviazione e di rete per l'accesso diretto alla condivisione.

## <a name="phase-5-user-cut-over"></a>Fase 5: riduzione dell'utente
Questa fase riguarda tutto il wrapping della migrazione:
* Pianificare il tempo di inattività.
* Aggiorna le modifiche apportate dagli utenti e dalle app prodotte sul lato StorSimple mentre i processi di trasformazione dei dati sono stati eseguiti nella fase 3. 
* Non consentire agli utenti di accedere al nuovo Windows Server con Sincronizzazione file di Azure o le condivisioni file di Azure tramite Direct-Share-Access.

### <a name="plan-your-downtime"></a>Pianificare il tempo di inattività
Questo approccio alla migrazione richiede tempi di inattività per gli utenti e le app. L'obiettivo è quello di ridurre al minimo i tempi di inattività e le seguenti considerazioni possono essere utili:

* Consente di rendere disponibili i volumi StorSimple durante l'esecuzione dei processi di trasformazione dei dati.
* Al termine dell'esecuzione dei processi di migrazione dati per una condivisione, è possibile rimuovere l'accesso utente (almeno in scrittura) dai volumi o condivisioni di StorSimple. Un RoboCopy finale rileverà la condivisione file di Azure, quindi sarà possibile ridurre gli utenti. La posizione in cui viene eseguito RoboCopy dipende dal fatto che si sia scelto di usare Sincronizzazione file di Azure o Direct-Share-Access. La prossima sezione di RoboCopy illustra questa.
* Una volta completato il recupero di RoboCopy, si è pronti per esporre la nuova posizione agli utenti. La condivisione file di Azure direttamente o una condivisione SMB in un server Windows con AFS. Spesso una distribuzione di DFS-N consente di realizzare un taglio rapido ed efficiente. Manterrà gli indirizzi di condivisione esistenti coerenti e punterà a una nuova posizione, contenente i file e le cartelle migrati.

### <a name="determine-when-your-namespace-has-fully-synced-to-your-server"></a>Determinare quando lo spazio dei nomi è stato completamente sincronizzato con il server

Quando si usa Sincronizzazione file di Azure per una condivisione file di Azure, è importante determinare che l'intero spazio dei nomi abbia terminato il download nel server prima di avviare qualsiasi RoboCopy locale. Il tempo necessario per scaricare lo spazio dei nomi dipende dal numero di elementi nella condivisione file di Azure. Esistono due metodi per determinare se lo spazio dei nomi è stato completamente raggiunto nel server:

#### <a name="azure-portal"></a>Portale di Azure
È possibile usare il portale di Azure per vedere quando lo spazio dei nomi è stato completamente raggiunto.
* Accedere al portale di Azure, passare al gruppo di sincronizzazione e controllare lo stato di sincronizzazione del gruppo di sincronizzazione e dell'endpoint server. 
* La direzione interessante è download: se viene appena effettuato il provisioning dell'endpoint server, viene visualizzata la **sincronizzazione iniziale** che indica che lo spazio dei nomi è ancora in arrivo.
Una volta apportate modifiche a una *sincronizzazione iniziale*, lo spazio dei nomi verrà popolato completamente nel server e sarà possibile procedere con un Robocopy locale.

#### <a name="windows-server-event-viewer"></a>Windows Server Visualizzatore eventi
unità organizzativa può inoltre utilizzare il Visualizzatore eventi nel server Windows per indicare quando lo spazio dei nomi è stato completamente raggiunto.

1. Aprire il **Visualizzatore eventi** e passare ad **applicazioni e servizi**.
1. Esplorare e aprire **Microsoft\FileSync\Agent\Telemetry**.
1. Cercare l' **evento 9102**più recente, che corrisponde a una sessione di sincronizzazione completata.
1. Selezionare **Details (dettagli** ) e confermare che si sta cercando un evento in cui è stato **scaricato**il valore **SyncDirection** .
1. Per il momento in cui lo spazio dei nomi ha completato il download sul server, sarà presente un solo evento con **scenario**, valore **FullGhostedSync** e **HRESULT**  =  **0**.
1. Se si dimentica questo evento, è anche possibile cercare altri **eventi 9102** con il **SyncDirection**  =  **download** e lo **scenario**  =  **"RegularSync"** di SyncDirection. La ricerca di uno di questi eventi indica inoltre che lo spazio dei nomi ha terminato il download e la sincronizzazione in sessioni di sincronizzazione regolari, indipendentemente dalla sincronizzazione.

### <a name="a-final-robocopy"></a>RoboCopy finale
A questo punto, esistono differenze tra il server Windows locale e l'appliance StorSimple 8100 o 8600:

1. È necessario recuperare le modifiche apportate dagli utenti o dalle app sul lato StorSimple mentre era in corso la migrazione.
1. Per i casi in cui si usa Sincronizzazione file di Azure: il dispositivo StorSimple dispone di una cache popolata rispetto a Windows Server solo uno spazio dei nomi senza contenuto di file archiviato localmente in questo momento. Quindi, la versione finale di RoboCopy può aiutare ad avviare la cache AFS locale estraendo il contenuto del file memorizzato nella cache locale per quanto disponibile e che può adattarsi al server AFS.
1. È possibile che alcuni file rimangano rimasti dal processo di trasformazione dei dati a causa di caratteri non validi. In tal caso, copiarli nel server Windows abilitato per la Sincronizzazione file di Azure. In seguito è possibile modificarle in modo che vengano sincronizzate. Se non si usa Sincronizzazione file di Azure per una condivisione particolare, è preferibile rinominare i file con caratteri non validi nel volume StorSimple, quindi eseguire RoboCopy direttamente nella condivisione file di Azure. 

> [!WARNING]
> Non è necessario avviare RoboCopy prima che il server disponga dello spazio dei nomi per una condivisione file di Azure scaricata completamente.
> Checkout: "[determinare quando lo spazio dei nomi è stato completamente scaricato nel server](#determine-when-your-namespace-has-fully-synced-to-your-server)"

 Si desidera copiare solo i file che sono stati modificati dopo l'ultima esecuzione del processo di migrazione e i file che non sono stati spostati nei processi precedenti. Al termine della migrazione, è possibile risolvere i problemi che non si sono spostati successivamente sul server. Vedere [sincronizzazione file di Azure risoluzione dei problemi](storage-sync-files-troubleshoot.md#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing).

RoboCopy presenta diversi parametri. L'esempio seguente illustra un comando finito e un elenco di motivi per la scelta di questi parametri:

```console
Robocopy /MT:16 /UNILOG:<file name> /TEE /B /MIR /COPYALL /DCOPY:DAT <SourcePath> <Dest.Path>
```

Sfondo:

:::row:::
   :::column span="1":::
      /MT
   :::column-end:::
   :::column span="1":::
      Consente a RoboCopy di eseguire multithread. Il valore predefinito è 8, Max è 128.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /UNILOG:<file name>
   :::column-end:::
   :::column span="1":::
      Restituisce lo stato del file di LOG come UNICODE (sovrascrive il log esistente).
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /TEE
   :::column-end:::
   :::column span="1":::
      Output nella finestra della console. Utilizzato in combinazione con l'output in un file di log.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      / B
   :::column-end:::
   :::column span="1":::
      Esegue RoboCopy nella stessa modalità usata da un'applicazione di backup. Consente a RoboCopy di spostare i file che l'utente corrente non dispone delle autorizzazioni per.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /MIR
   :::column-end:::
   :::column span="1":::
      Consente a RoboCopy di prendere in considerazione solo i delta tra l'origine (StorSimple Appliance) e la destinazione (directory di Windows Server).
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /COPY: copyflag [s]
   :::column-end:::
   :::column span="1":::
      fedeltà della copia del file (il valore predefinito è/COPY: DAT), flag di copia: D = dati, A = attributi, T = timestamp, S = Security = ACL NTFS, O = informazioni sul proprietario, U = informazioni sul controllo
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /COPYALL
   :::column-end:::
   :::column span="1":::
      COPIA tutte le informazioni sul file (equivalente a/COPY: DATSOU)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /DCOPY: copyflag [s]
   :::column-end:::
   :::column span="1":::
      fedeltà per la copia delle directory (il valore predefinito è/DCOPY: DA), i flag di copia: D = dati, A = attributi, T = timestamp
   :::column-end:::
:::row-end:::

Quando si configurano i percorsi di origine e di destinazione del comando RoboCopy, assicurarsi di esaminare la struttura di origine e destinazione per verificare che corrispondano. Se è stata usata la funzionalità di mapping di directory del processo di migrazione, la struttura della directory radice potrebbe essere diversa dalla struttura del volume StorSimple. In tal caso, potrebbero essere necessari più processi RoboCopy, uno per ogni sottodirectory. Poiché questo comando RoboCopy USA/MIR, non sposterà i file che sono gli stessi (file a livelli per istanza), ma se si ottiene un percorso di origine e di destinazione errato,/MIR ripulisce anche le strutture di directory nella condivisione file di Windows Server/Azure, che non sono presenti nel percorso di origine StorSimple. Quindi, devono corrispondere esattamente per il processo RoboCopy per raggiungere l'obiettivo di aggiornare solo il contenuto migrato con le ultime modifiche apportate durante la migrazione in corso. 

Per verificare se i file sono rimasti, consultare il file di log RoboCopy. Se sono presenti problemi, correggerli e rieseguire il comando RoboCopy. Non eseguire il deprovisioning di risorse StorSimple prima di risolvere i problemi in attesa per i file o le cartelle a cui si è interessati.

Se non si usa Sincronizzazione file di Azure per memorizzare nella cache la particolare condivisione file di Azure in questione, ma si è optato per l'accesso diretto alla condivisione:
1. [Montare la condivisione file di Azure](storage-how-to-use-files-windows.md#mount-the-azure-file-share) come unità di rete in un computer Windows locale.
1. Eseguire RoboCopy tra il StorSimple e la condivisione file di Azure montata. Se i file non vengono copiati, correggerne i nomi sul lato StorSimple per rimuovere i caratteri non validi e riprovare RoboCopy. Il comando RoboCopy elencato in precedenza può essere eseguito più volte con la funzione di richiamata non necessaria a StorSimple.

### <a name="user-cut-over"></a>Riduzione utente

Se si usa Sincronizzazione file di Azure, è probabile che sia necessario creare le condivisioni SMB in tale server Windows abilitato per AFS che corrispondano alle condivisioni presenti nei volumi StorSimple. È possibile eseguire il caricamento in primo piano di questo passaggio e farlo in precedenza in modo da non perdere tempo, ma è necessario assicurarsi che prima di questo punto nessuno possa accedere alle modifiche apportate a Windows Server.

Se si dispone di una distribuzione DFS-N, è possibile puntare la DFN-Namespaces ai nuovi percorsi delle cartelle del server. Se non si ha una distribuzione di DFS-N e l'appliance 8100/8600 è stata eseguita localmente con un server Windows, è possibile disconnettere il server. Quindi aggiungere a un dominio il nuovo Sincronizzazione file di Azure abilitato, Windows Server. Durante questo processo, assegnare al server lo stesso nome del server e i nomi delle condivisioni del server precedente, in modo che il taglio rimanga trasparente per gli utenti, i criteri di gruppo e gli script.

[Ulteriori informazioni su DFS-N](https://aka.ms/AzureFiles/Namespaces)

## <a name="deprovision"></a>Effettuare il deprovisioning
Quando si esegue il deprovisioning di una risorsa, si perde l'accesso alla configurazione di tale risorsa e ai relativi dati. Il deprovisioning non può essere annullato, quindi non procedere fino a quando non è stato confermato che la migrazione è stata completata e non ci sono dipendenze nei file StorSimple, nelle cartelle o nei backup del volume di cui si sta per effettuare il deprovisioning.

Prima di iniziare, è consigliabile osservare la nuova distribuzione di Sincronizzazione file di Azure in produzione, per un po'. Che consente di risolvere eventuali problemi che possono verificarsi.
Dopo aver osservato la distribuzione di Sincronizzazione file di Azure per almeno alcuni giorni, è possibile iniziare a eseguire il deprovisioning delle risorse nell'ordine seguente:

1. Effettuare il deprovisioning della risorsa StorSimple Data Manager tramite l'portale di Azure.
   Verranno eliminati tutti i processi DTS. Non sarà possibile recuperare facilmente i log di copia. Se sono importanti per i record, recuperarli prima di eseguire il deprovisioning.
1. Verificare che sia stata eseguita la migrazione delle appliance fisiche StorSimple, quindi annullarne la registrazione.
   Se non si è certi che sia stata eseguita la migrazione, non continuare. Se si effettua il deprovisioning di queste risorse mentre sono ancora necessarie, non sarà possibile recuperare i dati o la relativa configurazione.
1. Se non sono più presenti dispositivi registrati rimasti in una Device Manager StorSimple, è possibile procedere con la rimozione della risorsa Device Manager stessa.
1. A questo punto è possibile eliminare l'account di archiviazione StorSimple in Azure. Anche in questo caso, arrestare e verificare che la migrazione sia stata completata e che nessuno di essi dipenda da tali dati, prima di procedere.
1. Scollegare il dispositivo fisico StorSimple dalla data center.
1. Se si è proprietari dell'appliance StorSimple, è possibile riciclare il computer. 
   Se il dispositivo è con lease, informare il minore e restituire il dispositivo in base alle esigenze.

La migrazione è stata completata.

> [!NOTE]
> Hai ancora domande o hai riscontrato problemi?</br>
> Siamo qui per aiutarti: AzureFilesMigration@microsoft . com


## <a name="next-steps"></a>Passaggi successivi

* Acquisire familiarità con [sincronizzazione file di Azure: aka.ms/AFS](https://aka.ms/AFS).
* Comprendere la flessibilità dei criteri di suddivisione in [livelli nel cloud](storage-sync-cloud-tiering.md) .
* [Abilitare backup di Azure](../../backup/backup-afs.md#configure-backup-from-the-file-share-pane) nelle condivisioni file di Azure per pianificare gli snapshot e definire pianificazioni di conservazione dei backup.
* Se nella portale di Azure che alcuni file non sono sincronizzati in modo permanente, consultare la [Guida alla risoluzione dei](storage-sync-files-troubleshoot.md) problemi per risolvere questi problemi.
