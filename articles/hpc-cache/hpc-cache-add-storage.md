---
title: Aggiungere spazio di archiviazione a una cache HPC di Azure
description: Come definire le destinazioni di archiviazione in modo che la cache HPC di Azure possa usare il sistema NFS locale o i contenitori BLOB di Azure per l'archiviazione di file a lungo termine
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 09/30/2020
ms.author: v-erkel
ms.openlocfilehash: ab9b7fa330964f7db8393334dd8f209efd75573d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91611290"
---
# <a name="add-storage-targets"></a>Aggiungere destinazioni di archiviazione

Le *destinazioni di archiviazione* sono archiviazione back-end per i file a cui si accede tramite una cache HPC di Azure. È possibile aggiungere l'archiviazione NFS (ad esempio un sistema hardware locale) o archiviare i dati nel BLOB di Azure.

È possibile definire fino a dieci destinazioni di archiviazione diverse per una cache. La cache presenta tutte le destinazioni di archiviazione in uno spazio dei nomi aggregato.

I percorsi dello spazio dei nomi vengono configurati separatamente dopo aver aggiunto le destinazioni di archiviazione. In generale, una destinazione di archiviazione NFS può avere fino a dieci percorsi dello spazio dei nomi o più per alcune configurazioni di grandi dimensioni. Per informazioni dettagliate, vedere [percorsi dello spazio dei nomi NFS](add-namespace-paths.md#nfs-namespace-paths) .

Tenere presente che le esportazioni di archiviazione devono essere accessibili dalla rete virtuale della cache. Per l'archiviazione hardware locale, potrebbe essere necessario configurare un server DNS in grado di risolvere i nomi host per l'accesso con archiviazione NFS. Per altre informazioni, vedere [accesso DNS](hpc-cache-prerequisites.md#dns-access).

Aggiungere le destinazioni di archiviazione dopo la creazione della cache. Seguire questa procedura:

1. [Creare la cache](hpc-cache-create.md)
1. Definire una destinazione di archiviazione (informazioni in questo articolo)
1. [Creazione dei percorsi rivolte al client](add-namespace-paths.md) (per lo [spazio dei nomi aggregato](hpc-cache-namespace.md))

La procedura per aggiungere una destinazione di archiviazione è leggermente diversa a seconda che si stia aggiungendo un archivio BLOB di Azure o un'esportazione NFS. Di seguito sono riportati i dettagli per ognuno di essi.

Fare clic sull'immagine seguente per visualizzare una [dimostrazione video](https://azure.microsoft.com/resources/videos/set-up-hpc-cache/) della creazione di una cache e l'aggiunta di una destinazione di archiviazione dal portale di Azure.

[![anteprima video: cache HPC di Azure: programma di installazione (fare clic per visitare la pagina del video)](media/video-4-setup.png)](https://azure.microsoft.com/resources/videos/set-up-hpc-cache/)

## <a name="add-a-new-azure-blob-storage-target"></a>Aggiungere una nuova destinazione di archiviazione BLOB di Azure

Una nuova destinazione di archiviazione BLOB necessita di un contenitore BLOB vuoto o di un contenitore popolato con i dati nel formato file system del cloud di cache HPC di Azure. Altre informazioni sul precaricamento di un contenitore BLOB in [spostare i dati nell'archivio BLOB di Azure](hpc-cache-ingest.md).

La pagina portale di Azure **Aggiungi destinazione di archiviazione** include l'opzione per creare un nuovo contenitore BLOB appena prima di aggiungerlo.

### <a name="portal"></a>[Portale](#tab/azure-portal)

Dalla portale di Azure aprire l'istanza di cache e fare clic su **destinazioni di archiviazione** nella barra laterale sinistra.

![screenshot delle impostazioni > pagina destinazione di archiviazione, con due destinazioni di archiviazione esistenti in una tabella e un'evidenziazione intorno al pulsante + Aggiungi destinazione di archiviazione sopra la tabella](media/add-storage-target-button.png)

La pagina **destinazioni di archiviazione** elenca tutte le destinazioni esistenti e fornisce un collegamento per aggiungerne una nuova.

Fare clic sul pulsante **Aggiungi destinazione di archiviazione** .

![screenshot della pagina Aggiungi destinazione di archiviazione, popolato con le informazioni per una nuova destinazione di archiviazione BLOB di Azure](media/hpc-cache-add-blob.png)

Per definire un contenitore BLOB di Azure, immettere queste informazioni.

* **Nome destinazione di archiviazione** : impostare un nome che identifichi la destinazione di archiviazione nella cache HPC di Azure.
* **Tipo di destinazione** : scegliere **BLOB**.
* **Account di archiviazione** : selezionare l'account che si vuole usare.

  Per accedere all'account di archiviazione, è necessario autorizzare l'istanza della cache, come descritto in [aggiungere i ruoli di accesso](#add-the-access-control-roles-to-your-account).

  Per informazioni sul tipo di account di archiviazione che è possibile usare, vedere [requisiti di archiviazione BLOB](hpc-cache-prerequisites.md#blob-storage-requirements).

* **Contenitore di archiviazione** : selezionare il contenitore BLOB per la destinazione oppure fare clic su **Crea nuovo**.

  ![screenshot della finestra di dialogo per specificare il nome e il livello di accesso (privato) per il nuovo contenitore](media/add-blob-new-container.png)

Al termine, fare clic su **OK** per aggiungere la destinazione di archiviazione.

> [!NOTE]
> Se il firewall dell'account di archiviazione è impostato in modo da limitare l'accesso solo alle "reti selezionate", usare la soluzione alternativa temporanea documentata in [aggirare le impostazioni del firewall dell'account di archiviazione BLOB](hpc-cache-blob-firewall-fix.md).

### <a name="add-the-access-control-roles-to-your-account"></a>Aggiungere i ruoli di controllo di accesso all'account

Cache HPC di Azure usa il [controllo degli accessi in base al ruolo di Azure (RBAC di Azure)](https://docs.microsoft.com/azure/role-based-access-control/index) per autorizzare il servizio cache ad accedere all'account di archiviazione per le destinazioni di archiviazione BLOB di Azure.

Il proprietario dell'account di archiviazione deve aggiungere in modo esplicito i ruoli collaboratore [account di archiviazione](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-account-contributor) e collaboratore [dati BLOB di archiviazione](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor) per l'utente "HPC cache Resource Provider".

È possibile eseguire questa operazione in anticipo oppure facendo clic su un collegamento nella pagina in cui si aggiunge una destinazione di archiviazione BLOB. Tenere presente che possono essere necessari fino a cinque minuti per la propagazione delle impostazioni dei ruoli nell'ambiente Azure, quindi è necessario attendere alcuni minuti dopo aver aggiunto i ruoli prima di creare una destinazione di archiviazione.

Passaggi per aggiungere i ruoli di Azure:

1. Aprire la pagina **controllo di accesso (IAM)** per l'account di archiviazione. Il collegamento nella pagina **Aggiungi destinazione di archiviazione** apre automaticamente questa pagina per l'account selezionato.

1. Fare clic su nella **+** parte superiore della pagina e scegliere **Aggiungi un'assegnazione di ruolo**.

1. Selezionare il ruolo "collaboratore account di archiviazione" nell'elenco.

1. Nel campo **assegna accesso a** lasciare il valore predefinito selezionato ("Azure ad utente, gruppo o entità servizio").  

1. Nel campo **Seleziona** cercare "HPC".  Questa stringa deve corrispondere a un'entità servizio denominata "HPC cache Resource Provider". Fare clic su tale entità per selezionarla.

   > [!NOTE]
   > Se la ricerca di "HPC" non funziona, provare a usare la stringa "storagecache". Per gli utenti che hanno partecipato alle anteprime (prima di GA) potrebbe essere necessario usare il nome precedente per l'entità servizio.

1. Fare clic sul pulsante **Salva** nella parte inferiore.

1. Ripetere questo processo per assegnare il ruolo "collaboratore dati BLOB di archiviazione".  

![screenshot della GUI Aggiungi assegnazione ruolo](media/hpc-cache-add-role.png)

### <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

[!INCLUDE [cli-reminder.md](includes/cli-reminder.md)]

### <a name="prerequisite-storage-account-access"></a>Prerequisito: accesso all'account di archiviazione

Prima di aggiungere una destinazione di archiviazione BLOB, verificare che la cache disponga dei ruoli corretti per accedere all'account di archiviazione e che le impostazioni del firewall consentano la creazione della destinazione di archiviazione.

Cache HPC di Azure usa il [controllo degli accessi in base al ruolo di Azure (RBAC di Azure)](../role-based-access-control/index.yml) per autorizzare il servizio cache ad accedere all'account di archiviazione per le destinazioni di archiviazione BLOB di Azure.

Il proprietario dell'account di archiviazione deve aggiungere in modo esplicito i ruoli collaboratore [account di archiviazione](../role-based-access-control/built-in-roles.md#storage-account-contributor) e collaboratore [dati BLOB di archiviazione](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) per l'utente "HPC cache Resource Provider".

La creazione della destinazione di archiviazione avrà esito negativo se la cache non ha questi ruoli.

Possono essere necessari fino a cinque minuti per la propagazione delle impostazioni dei ruoli nell'ambiente Azure, quindi è necessario attendere alcuni minuti dopo aver aggiunto i ruoli prima di creare una destinazione di archiviazione.

Per istruzioni dettagliate, vedere [aggiungere o rimuovere assegnazioni di ruolo di Azure usando l'interfaccia](../role-based-access-control/role-assignments-cli.md) della riga di comando di Azure

Controllare anche le impostazioni del firewall dell'account di archiviazione. Se il firewall è impostato per limitare l'accesso solo alle "reti selezionate", la creazione della destinazione di archiviazione potrebbe non riuscire. Usare la soluzione alternativa documentata in [aggirare le impostazioni del firewall dell'account di archiviazione BLOB](hpc-cache-blob-firewall-fix.md).

### <a name="add-a-blob-storage-target-with-azure-cli"></a>Aggiungere una destinazione di archiviazione BLOB all'interfaccia della riga di comando di Azure

Usare l'interfaccia [AZ HPC-cache BLOB-Storage-target Add](/cli/azure/ext/hpc-cache/hpc-cache/blob-storage-target#ext-hpc-cache-az-hpc-cache-blob-storage-target-add) per definire una destinazione di archiviazione BLOB di Azure.

> [!NOTE]
> Ai comandi dell'interfaccia della riga di comando di Azure è attualmente necessario creare un percorso dello spazio dei nomi quando si aggiunge una destinazione di archiviazione. Questa operazione è diversa dal processo utilizzato con l'interfaccia portale di Azure.

Oltre ai parametri standard del gruppo di risorse e del nome della cache, è necessario specificare le opzioni seguenti per la destinazione di archiviazione:

* ``--name`` -Impostare un nome che identifichi la destinazione di archiviazione nella cache HPC di Azure.

* ``--storage-account``-Identificatore account, nel formato seguente:/subscriptions/*<subscription_id>*/resourceGroups/*<storage_resource_group *>/Providers/Microsoft.storage/storageAccounts/*<* account_name>

  Per informazioni sul tipo di account di archiviazione che è possibile usare, vedere [requisiti di archiviazione BLOB](hpc-cache-prerequisites.md#blob-storage-requirements).

* ``--container-name`` -Specificare il nome del contenitore da usare per questa destinazione di archiviazione.

* ``--virtual-namespace-path`` -Impostare il percorso del file per il client per la destinazione di archiviazione. Racchiudere i percorsi tra virgolette. Per ulteriori informazioni sulla funzionalità spazio dei nomi virtuale, vedere [pianificare lo spazio dei nomi aggregato](hpc-cache-namespace.md) .

Comando di esempio:

```azurecli
az hpc-cache blob-storage-target add --resource-group "hpc-cache-group" \
    --cache-name "cache1" --name "blob-target1" \
    --storage-account "/subscriptions/<subscriptionID>/resourceGroups/myrgname/providers/Microsoft.Storage/storageAccounts/myaccountname" \
    --container-name "container1" --virtual-namespace-path "/blob1"
```

---

## <a name="add-a-new-nfs-storage-target"></a>Aggiungere una nuova destinazione di archiviazione NFS

Una destinazione di archiviazione NFS ha impostazioni diverse da una destinazione di archiviazione BLOB. L'impostazione del modello di utilizzo consente alla cache di memorizzare in modo efficiente i dati da questo sistema di archiviazione.

![Screenshot della pagina Aggiungi destinazione di archiviazione con destinazione NFS definita](media/add-nfs-target.png)

> [!NOTE]
> Prima di creare una destinazione di archiviazione NFS, assicurarsi che il sistema di archiviazione sia accessibile dalla cache HPC di Azure e soddisfi i requisiti di autorizzazione. La creazione della destinazione di archiviazione avrà esito negativo se la cache non riesce ad accedere al sistema di archiviazione. Per informazioni dettagliate, vedere [requisiti di archiviazione NFS](hpc-cache-prerequisites.md#nfs-storage-requirements) e [risolvere i problemi di configurazione NAS e di destinazione archiviazione NFS](troubleshoot-nas.md) .

### <a name="choose-a-usage-model"></a>Scegliere un modello di utilizzo
<!-- referenced from GUI - update aka.ms link if you change this heading -->

Quando si crea una destinazione di archiviazione che punta a un sistema di archiviazione NFS, è necessario scegliere il modello di utilizzo per tale destinazione. Questo modello determina il modo in cui i dati vengono memorizzati nella cache.

Sono disponibili tre opzioni:

* **Read Heavy, scritture rare** : usare questa opzione se si vuole velocizzare l'accesso in lettura ai file statici o modificati raramente.

  Questa opzione consente di memorizzare nella cache i file letti dai client, ma di passare immediatamente le scritture all'archiviazione back-end. I file archiviati nella cache non vengono mai confrontati con i file nel volume di archiviazione NFS.

  Non usare questa opzione se esiste il rischio che un file venga modificato direttamente nel sistema di archiviazione senza prima scriverlo nella cache. In tal caso, la versione memorizzata nella cache del file non verrà mai aggiornata con le modifiche apportate dal back-end e il set di dati può diventare incoerente.

* **Scritture superiori al 15%** : questa opzione consente di velocizzare le prestazioni di lettura e scrittura. Quando si usa questa opzione, tutti i client devono accedere ai file tramite la cache HPC di Azure anziché montare direttamente l'archiviazione back-end. I file memorizzati nella cache avranno modifiche recenti che non sono archiviate nel back-end.

  In questo modello di utilizzo i file nella cache non vengono controllati in base ai file nell'archiviazione back-end. Si presuppone che la versione memorizzata nella cache del file sia più aggiornata. Un file modificato nella cache viene scritto nel sistema di archiviazione back-end dopo che è stato inserito nella cache per un'ora senza ulteriori modifiche.

* I **client scrivono nella destinazione NFS, ignorando la cache** . scegliere questa opzione se i client nel flusso di lavoro scrivono i dati direttamente nel sistema di archiviazione senza prima scrivere nella cache. I file richiesti dai client vengono memorizzati nella cache, ma tutte le modifiche apportate ai file dal client vengono passate immediatamente al sistema di archiviazione back-end.

  Con questo modello di utilizzo, i file nella cache vengono spesso controllati rispetto alle versioni back-end per gli aggiornamenti. Questa verifica consente di modificare i file all'esterno della cache mantenendo la coerenza dei dati.

In questa tabella vengono riepilogate le differenze del modello di utilizzo:

| Modello di utilizzo                   | Modalità di memorizzazione nella cache | Verifica del back-end | Ritardo massimo write-back |
|-------------------------------|--------------|-----------------------|--------------------------|
| Lettura di scritture complesse e non frequenti | Lettura         | Mai                 | Nessuno                     |
| Scritture superiori al 15%       | Lettura/Scrittura   | Mai                 | 1 ora                   |
| Client che ignorano la cache      | Lettura         | 30 secondi            | Nessuno                     |

### <a name="create-an-nfs-storage-target"></a>Creare una destinazione di archiviazione NFS

### <a name="portal"></a>[Portale](#tab/azure-portal)

Dalla portale di Azure aprire l'istanza di cache e fare clic su **destinazioni di archiviazione** nella barra laterale sinistra.

![screenshot delle impostazioni > pagina destinazione di archiviazione, con due destinazioni di archiviazione esistenti in una tabella e un'evidenziazione intorno al pulsante + Aggiungi destinazione di archiviazione sopra la tabella](media/add-storage-target-button.png)

La pagina **destinazioni di archiviazione** elenca tutte le destinazioni esistenti e fornisce un collegamento per aggiungerne una nuova.

Fare clic sul pulsante **Aggiungi destinazione di archiviazione** .

![Screenshot della pagina Aggiungi destinazione di archiviazione con destinazione NFS definita](media/add-nfs-target.png)

Fornire queste informazioni per una destinazione di archiviazione supportata da NFS:

* **Nome destinazione di archiviazione** : impostare un nome che identifichi la destinazione di archiviazione nella cache HPC di Azure.

* **Tipo di destinazione** : scegliere **NFS**.

* Nome **host** : immettere l'indirizzo IP o il nome di dominio completo per il sistema di archiviazione NFS. Usare un nome di dominio solo se la cache ha accesso a un server DNS in grado di risolvere il nome.

* **Modello di utilizzo** : scegliere uno dei profili di caching dei dati in base al flusso di lavoro, descritto in [scegliere un modello di utilizzo](#choose-a-usage-model) precedente.

Al termine, fare clic su **OK** per aggiungere la destinazione di archiviazione.

### <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

[!INCLUDE [cli-reminder.md](includes/cli-reminder.md)]

Usare il comando dell'interfaccia della riga di comando di Azure [AZ HPC-cache NFS-storage-target Add](/cli/azure/ext/hpc-cache/hpc-cache/nfs-storage-target#ext-hpc-cache-az-hpc-cache-nfs-storage-target-add) per creare la destinazione di archiviazione.

> [!NOTE]
> Ai comandi dell'interfaccia della riga di comando di Azure è attualmente necessario creare un percorso dello spazio dei nomi quando si aggiunge una destinazione di archiviazione. Questa operazione è diversa dal processo utilizzato con l'interfaccia portale di Azure.

Specificare questi valori oltre al nome della cache e al gruppo di risorse della cache:

* ``--name`` -Impostare un nome che identifichi la destinazione di archiviazione nella cache HPC di Azure.
* ``--nfs3-target`` -Indirizzo IP del sistema di archiviazione NFS. È possibile utilizzare un nome di dominio completo se la cache ha accesso a un server DNS in grado di risolvere il nome.
* ``--nfs3-usage-model`` -Uno dei profili di caching dei dati, descritto in [scegliere un modello di utilizzo](#choose-a-usage-model), sopra.

  Verificare i nomi dei modelli di utilizzo con il comando [AZ HPC-cache Usage-model list](/cli/azure/ext/hpc-cache/hpc-cache/usage-model#ext-hpc-cache-az-hpc-cache-usage-model-list).

* ``--junction`` -Il parametro di giunzione collega il percorso del file virtuale per il client con un percorso di esportazione sul sistema di archiviazione.

  Una destinazione di archiviazione NFS può avere più percorsi virtuali, purché ogni percorso rappresenti un'esportazione o una sottodirectory diversa nello stesso sistema di archiviazione. Creare tutti i percorsi per un sistema di archiviazione in una destinazione di archiviazione.

  È possibile [aggiungere e modificare i percorsi dello spazio dei nomi](add-namespace-paths.md) in una destinazione di archiviazione in qualsiasi momento.

  Il ``--junction`` parametro usa i valori seguenti:

  * ``namespace-path`` -Percorso del file virtuale rivolte al client
  * ``nfs-export`` -Esportazione del sistema di archiviazione da associare al percorso del client
  * ``target-path`` (facoltativo): sottodirectory dell'esportazione, se necessario

  Esempio: ``--junction namespace-path="/nas-1" nfs-export="/datadisk1" target-path="/test"``

  Per ulteriori informazioni sulla funzionalità spazio dei nomi virtuale, vedere [configurare lo spazio dei nomi aggregato](hpc-cache-namespace.md) .

Comando di esempio:

```azurecli

az hpc-cache nfs-storage-target add --resource-group "hpc-cache-group" --cache-name "doc-cache0629" \
    --name nfsd1 --nfs3-target 10.0.0.4 --nfs3-usage-model WRITE_WORKLOAD_15 \
    --junction namespace-path="/nfs1/data1" nfs-export="/datadisk1" target-path=""
```

Output:
```azurecli

{- Finished ..
  "clfs": null,
  "id": "/subscriptions/<subscriptionID>/resourceGroups/hpc-cache-group/providers/Microsoft.StorageCache/caches/doc-cache0629/storageTargets/nfsd1",
  "junctions": [
    {
      "namespacePath": "/nfs1/data1",
      "nfsExport": "/datadisk1",
      "targetPath": ""
    }
  ],
  "location": "eastus",
  "name": "nfsd1",
  "nfs3": {
    "target": "10.0.0.4",
    "usageModel": "WRITE_WORKLOAD_15"
  },
  "provisioningState": "Succeeded",
  "resourceGroup": "hpc-cache-group",
  "targetType": "nfs3",
  "type": "Microsoft.StorageCache/caches/storageTargets",
  "unknown": null
}

```

---

## <a name="view-storage-targets"></a>Visualizzare le destinazioni di archiviazione

È possibile usare il portale di Azure o l'interfaccia della riga di comando di Azure per visualizzare le destinazioni di archiviazione già definite per la cache.

### <a name="portal"></a>[Portale](#tab/azure-portal)

Dalla portale di Azure aprire l'istanza di cache e fare clic su **destinazioni di archiviazione**, che si trova sotto l'intestazione Impostazioni sulla barra laterale sinistra. Nella pagina Destinazioni di archiviazione sono elencati tutti i controlli e le destinazioni esistenti per aggiungerli o eliminarli.

Fare clic sul nome di una destinazione di archiviazione per aprirne la pagina dei dettagli.

Per altre informazioni, vedere [modificare le destinazioni di archiviazione](hpc-cache-edit-storage.md) .

### <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

[!INCLUDE [cli-reminder.md](includes/cli-reminder.md)]

Usare l'opzione [AZ HPC-cache storage-target list](/cli/azure/ext/hpc-cache/hpc-cache/storage-target#ext-hpc-cache-az-hpc-cache-storage-target-list) per visualizzare le destinazioni di archiviazione esistenti per una cache. Specificare il nome della cache e il gruppo di risorse (a meno che non sia stato impostato globalmente).

```azurecli
az hpc-cache storage-target list --resource-group "scgroup" --cache-name "sc1"
```

Usare [AZ HPC-cache storage-target Show](/cli/azure/ext/hpc-cache/hpc-cache/storage-target#ext-hpc-cache-az-hpc-cache-storage-target-list) per visualizzare i dettagli relativi a una particolare destinazione di archiviazione. (Specificare la destinazione di archiviazione in base al nome).

Esempio:

```azurecli
$ az hpc-cache storage-target show --cache-name doc-cache0629 --name nfsd1

{
  "clfs": null,
  "id": "/subscriptions/<subscription_ID>/resourceGroups/scgroup/providers/Microsoft.StorageCache/caches/doc-cache0629/storageTargets/nfsd1",
  "junctions": [
    {
      "namespacePath": "/nfs1/data1",
      "nfsExport": "/datadisk1",
      "targetPath": ""
    }
  ],
  "location": "eastus",
  "name": "nfsd1",
  "nfs3": {
    "target": "10.0.0.4",
    "usageModel": "WRITE_WORKLOAD_15"
  },
  "provisioningState": "Succeeded",
  "resourceGroup": "scgroup",
  "targetType": "nfs3",
  "type": "Microsoft.StorageCache/caches/storageTargets",
  "unknown": null
}
$
```

---

## <a name="next-steps"></a>Passaggi successivi

Dopo aver creato le destinazioni di archiviazione, continuare con queste attività per preparare la cache per l'uso:

* [Configurare lo spazio dei nomi aggregato](add-namespace-paths.md)
* [Montare la cache HPC di Azure](hpc-cache-mount.md)
* [Spostare i dati nell'archivio BLOB di Azure](hpc-cache-ingest.md)

Se è necessario aggiornare le impostazioni, è possibile [modificare una destinazione di archiviazione](hpc-cache-edit-storage.md).
