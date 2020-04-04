---
title: Aggiungere spazio di archiviazione a una cache HPC di AzureAdd storage to an Azure HPC Cache
description: Come definire le destinazioni di archiviazione in modo che la cache HPC di Azure possa usare il sistema NFS locale o i contenitori BLOB di Azure per l'archiviazione di file a lungo termineHow to define storage targets so that your Azure HPC Cache can use your on-premises NFS system or Azure Blob containers for long-term file storage
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: rohogue
ms.openlocfilehash: 3fbc4e683c2b0e72c3a084a59793dbf9eb4b658c
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80657420"
---
# <a name="add-storage-targets"></a>Aggiungere destinazioni di archiviazione

*Le destinazioni di* archiviazione sono l'archiviazione back-end per i file a cui si accede tramite una cache HPC di Azure.Storage targets are back-end storage for files that are accessed through an Azure HPC Cache. È possibile aggiungere l'archiviazione NFS (ad esempio un sistema hardware locale) o archiviare i dati nel BLOB di Azure.You can add NFS storage (like an on-premises hardware system), or store data in Azure Blob.

È possibile definire fino a dieci destinazioni di archiviazione diverse per una cache. La cache presenta tutte le destinazioni di archiviazione in uno spazio dei nomi aggregato.

Tenere presente che le esportazioni di archiviazione devono essere accessibili dalla rete virtuale della cache. Per l'archiviazione hardware locale, potrebbe essere necessario configurare un server DNS in grado di risolvere i nomi host per l'accesso all'archiviazione NFS. Per saperne di più in [Accesso DNS](hpc-cache-prereqs.md#dns-access).

Aggiungere le destinazioni di archiviazione dopo la creazione della cache. La procedura è leggermente diversa a seconda che si stia aggiungendo l'archiviazione BLOB di Azure o un'esportazione NFS. I dettagli per ciascuno sono riportati di seguito.

## <a name="open-the-storage-targets-page"></a>Aprire la pagina Obiettivi di archiviazione

Nel portale di Azure aprire l'istanza della cache e fare clic su **Destinazioni di archiviazione** nella barra laterale sinistra. La pagina delle destinazioni di archiviazione elenca tutte le destinazioni esistenti e fornisce un collegamento per aggiungerne una nuova.

![schermata del collegamento destinazioni di archiviazione nella barra laterale, sotto l'intestazione Configura, che si trova tra le intestazioni di categoria Impostazioni e monitoraggio](media/hpc-cache-storage-targets-sidebar.png)

## <a name="add-a-new-azure-blob-storage-target"></a>Aggiungere una nuova destinazione di archiviazione BLOB di AzureAdd a new Azure Blob storage target

Una nuova destinazione di archiviazione BLOB richiede un contenitore BLOB vuoto o un contenitore popolato con dati nel formato del file system cloud della cache HPC di Azure.A new Blob storage target needs an empty Blob container or a container that is populated with data in the Azure HPC Cache cloud file system format. Per altre informazioni sul precaricamento di un contenitore [BLOB, vedere Spostare i dati nell'archiviazione BLOB](hpc-cache-ingest.md)di Azure.Read more about pre-loading a Blob container in Move data to Azure Blob storage .

È possibile creare un nuovo contenitore da questa pagina appena prima di aggiungerlo.

![Screenshot della pagina di destinazione aggiungi archiviazione, popolata con informazioni per una nuova destinazione di archiviazione BLOB di Azure](media/hpc-cache-add-blob.png)

Per definire un contenitore BLOB di Azure, immettere queste informazioni.

* **Nome destinazione archiviazione:** impostare un nome che identifichi questa destinazione di archiviazione nella cache HPC di Azure.Storage target name - Set a name that identifies this storage target in the Azure HPC Cache.
* **Tipo di destinazione:** scegliere **BLOB**.
* **Account di archiviazione:** selezionare l'account che si vuole usare.

  È necessario autorizzare l'istanza della cache ad accedere all'account di archiviazione come descritto in [Aggiungere i ruoli](#add-the-access-control-roles-to-your-account)di accesso .

  Per informazioni sul tipo di account di archiviazione che è possibile usare, vedere Requisiti di [archiviazione BLOB](hpc-cache-prereqs.md#blob-storage-requirements).

* **Contenitore di archiviazione:** selezionare il contenitore BLOB per questa destinazione oppure fare clic su **Crea nuovo**.

  ![schermata della finestra di dialogo per specificare il nome e il livello di accesso (privato) per il nuovo contenitore](media/add-blob-new-container.png)

* **Percorso dello spazio dei nomi virtuale:** impostare il percorso del file rivolto al client per questa destinazione di archiviazione. Per altre informazioni sulla funzionalità dello spazio dei nomi virtuale, vedere [Configurare lo spazio dei nomi aggregato.](hpc-cache-namespace.md)

Al termine, fare clic **su OK** per aggiungere la destinazione di archiviazione.

> [!NOTE]
> Se il firewall dell'account di archiviazione è impostato per limitare l'accesso solo alle "reti selezionate", usare la soluzione temporanea documentata in [Impostazioni del firewall dell'account](hpc-cache-blob-firewall-fix.md)di archiviazione BLOB .

### <a name="add-the-access-control-roles-to-your-account"></a>Aggiungere i ruoli di controllo di accesso all'accountAdd the access control roles to your account

La cache HPC di Azure usa il controllo degli accessi in base al ruolo per autorizzare il servizio cache ad accedere all'account di archiviazione per le destinazioni di archiviazione BLOB di Azure.Azure HPC Cache uses [role-based access control (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/index) to authorize the cache service to access your storage account for Azure Blob storage targets.

Il proprietario dell'account di archiviazione deve aggiungere in modo esplicito i ruoli [Collaboratore account](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-account-contributor) di archiviazione e [Collaboratore dati BLOB di archiviazione](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor) per l'utente "Provider di risorse cache HPC".

È possibile eseguire questa operazione in anticipo oppure fare clic su un collegamento nella pagina in cui si aggiunge una destinazione di archiviazione BLOB. Tenere presente che possono essere necessarie fino a cinque minuti prima che le impostazioni dei ruoli vengano propagate nell'ambiente Azure, pertanto è consigliabile attendere alcuni minuti dopo l'aggiunta dei ruoli prima di creare una destinazione di archiviazione.

Passaggi per aggiungere i ruoli RBAC:

1. Aprire la pagina **Controllo di accesso (IAM)** per l'account di archiviazione. Il collegamento nella pagina **Aggiungi destinazione archiviazione** apre automaticamente questa pagina per l'account selezionato.

1. Fare clic nella parte superiore della pagina e scegliere **Aggiungi assegnazione ruolo**. **+**

1. Selezionare il ruolo "Storage Account Contributor" dall'elenco.

1. Nel campo **Assegna accesso a** lasciare selezionato il valore predefinito ("utente, gruppo o entità servizio di Azure AD").  

1. Nel campo **Seleziona,** cercare "hpc".  Questa stringa deve corrispondere a un'entità servizio, denominata "HPC Cache Resource Provider". Fare clic sull'entità per selezionarla.

   > [!NOTE]
   > Se la ricerca di "hpc" non funziona, provare a utilizzare la stringa "storagecache". Gli utenti che hanno partecipato alle anteprime (prima di GA) potrebbero dover usare il nome precedente per l'entità servizio.

1. Fare clic sul pulsante **Salva** nella parte inferiore.

1. Ripetere questo processo per assegnare il ruolo "Storage Blob Data Contributor".  

![schermata dell'aggiunta di un'assegnazione di ruolo GUI](media/hpc-cache-add-role.png)

## <a name="add-a-new-nfs-storage-target"></a>Aggiungere una nuova destinazione di archiviazione NFSAdd a new NFS storage target

Una destinazione di archiviazione NFS include più campi della destinazione di archiviazione BLOB. Questi campi specificano come raggiungere l'esportazione di archiviazione e come memorizzare in modo efficiente i dati. Inoltre, una destinazione di archiviazione NFS consente di creare più percorsi dello spazio dei nomi se l'host NFS ha più di un'esportazione disponibile.

![Schermata della pagina di destinazione di aggiunta di archiviazione con la destinazione NFS definita](media/add-nfs-target.png)

> [!NOTE]
> Prima di creare una destinazione di archiviazione NFS, assicurarsi che il sistema di archiviazione sia accessibile dalla cache HPC di Azure e soddisfi i requisiti di autorizzazione. La creazione della destinazione di archiviazione avrà esito negativo se la cache non riesce ad accedere al sistema di archiviazione. Leggere i requisiti di [archiviazione NFS](hpc-cache-prereqs.md#nfs-storage-requirements) e Risolvere i problemi relativi alla configurazione NAS e alla [destinazione dell'archiviazione NFS](troubleshoot-nas.md) per informazioni dettagliate.

Fornire queste informazioni per una destinazione di archiviazione supportata da NFS:Provide this information for an NFS-backed storage target:

* **Nome destinazione archiviazione:** impostare un nome che identifichi questa destinazione di archiviazione nella cache HPC di Azure.Storage target name - Set a name that identifies this storage target in the Azure HPC Cache.

* **Tipo di destinazione:** scegliere **NFS**.

* **Nome host:** immettere l'indirizzo IP o il nome di dominio completo per il sistema di archiviazione NFS. Utilizzare un nome di dominio solo se la cache ha accesso a un server DNS in grado di risolvere il nome.

* **Modello di utilizzo:** scegliere uno dei profili di memorizzazione dei dati nella cache in base al flusso di lavoro, descritto in [Scegliere un modello](#choose-a-usage-model)di utilizzo di seguito.

### <a name="nfs-namespace-paths"></a>Percorsi dello spazio dei nomi NFS

Una destinazione di archiviazione NFS può avere più percorsi virtuali, purché ogni percorso rappresenti un'esportazione o una sottodirectory diversa nello stesso sistema di archiviazione.

Creare tutti i percorsi da una destinazione di archiviazione.

È possibile [aggiungere e modificare i percorsi dello spazio dei nomi](hpc-cache-edit-storage.md) in una destinazione di archiviazione in qualsiasi momento.

Compilare questi valori per ogni percorso dello spazio dei nomi:Fill in these values for each namespace path:

* **Percorso dello spazio dei nomi virtuale:** impostare il percorso del file rivolto al client per questa destinazione di archiviazione. Per altre informazioni sulla funzionalità dello spazio dei nomi virtuale, vedere [Configurare lo spazio dei nomi aggregato.](hpc-cache-namespace.md)

<!--  The virtual path should start with a slash ``/``. -->

* **Percorso esportazione NFS:** immettere il percorso dell'esportazione NFS.

* **Percorso sottodirectory:** se si desidera montare una sottodirectory specifica dell'esportazione, immetterla qui. In caso contrario, lasciare vuoto questo campo.

Al termine, fare clic **su OK** per aggiungere la destinazione di archiviazione.

### <a name="choose-a-usage-model"></a>Scegliere un modello di utilizzo
<!-- referenced from GUI - update aka.ms link if you change this heading -->

Quando si crea una destinazione di archiviazione che punta a un sistema di archiviazione NFS, è necessario scegliere il modello di utilizzo per tale destinazione. Questo modello determina la modalità di memorizzazione dei dati nella cache.

Sono disponibili tre opzioni:

* Lettura di **scritture pesanti e poco frequenti:** utilizzare questa opzione se si desidera velocizzare l'accesso in lettura ai file statici o modificati raramente.

  Questa opzione memorizza nella cache i file che i client leggono, ma passa immediatamente le scritture all'archiviazione back-end. I file memorizzati nella cache non vengono mai confrontati con i file nel volume di archiviazione NFS.

  Non utilizzare questa opzione se esiste il rischio che un file venga modificato direttamente nel sistema di archiviazione senza prima scriverlo nella cache. In questo caso, la versione memorizzata nella cache del file non verrà mai aggiornata con le modifiche dal back-end e il set di dati può diventare incoerente.

* **Scritture superiori al 15%** - Questa opzione velocizza le prestazioni di lettura e scrittura. Quando si usa questa opzione, tutti i client devono accedere ai file tramite la cache HPC di Azure anziché montare direttamente l'archiviazione back-end. I file memorizzati nella cache avranno modifiche recenti che non vengono archiviate nel back-end.

  In questo modello di utilizzo, i file nella cache non vengono confrontati con i file nell'archiviazione back-end. Si presuppone che la versione memorizzata nella cache del file sia più aggiornata. Un file modificato nella cache viene scritto nel sistema di archiviazione back-end dopo che è stato nella cache per un'ora senza ulteriori modifiche.

* **I client scrivono nella destinazione NFS, ignorando la cache:** scegliere questa opzione se i client del flusso di lavoro scrivono i dati direttamente nel sistema di archiviazione senza prima scrivere nella cache. I file che i client richiedono vengono memorizzati nella cache, ma tutte le modifiche apportate a tali file dal client vengono passate immediatamente al sistema di archiviazione back-end.

  Con questo modello di utilizzo, i file nella cache vengono spesso confrontati con le versioni back-end per gli aggiornamenti. Questa verifica consente di modificare i file al di fuori della cache mantenendo la coerenza dei dati.

In questa tabella sono riepilogate le differenze tra i modelli di utilizzo:

| Modello di utilizzo | Modalità di memorizzazione nella cacheCaching mode | Verifica back-end | Ritardo massimo di writeback |
| ---- | ---- | ---- | ---- |
| Leggi scritti pesanti e poco frequenti | Lettura | Never | nessuno |
| Più del 15% scrive | Lettura/Scrittura | Never | 1 ora |
| I client ignorano la cache | Lettura | 30 secondi | nessuno |

## <a name="next-steps"></a>Passaggi successivi

Dopo aver creato le destinazioni di archiviazione, considerare una di queste attività:After creating storage targets, consider one of these tasks:

* [Montare la cache HPC di Azure](hpc-cache-mount.md)
* [Spostare i dati nell'archiviazione BLOB di AzureMove data to Azure Blob storage](hpc-cache-ingest.md)

Se è necessario aggiornare le impostazioni, è possibile [modificare una destinazione di archiviazione.](hpc-cache-edit-storage.md)
