---
title: Aggiungere spazio di archiviazione a una cache HPC di Azure
description: Come definire le destinazioni di archiviazione in modo che la cache HPC di Azure possa usare il sistema NFS locale o i contenitori BLOB di Azure per l'archiviazione di file a lungo termine
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: v-erkell
ms.openlocfilehash: 4554214b74b4d09fa40e355270208bebda4076b7
ms.sourcegitcommit: a4b5d31b113f520fcd43624dd57be677d10fc1c0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70775260"
---
# <a name="add-storage"></a>Aggiungere le risorse di archiviazione

Le *destinazioni di archiviazione* sono archiviazione back-end per i file a cui si accede tramite un'istanza di cache HPC di Azure. È possibile aggiungere l'archiviazione NFS, ad esempio un sistema hardware locale, o archiviare i dati nel BLOB di Azure.

È possibile definire fino a dieci destinazioni di archiviazione diverse per una cache. La cache presenta tutte le destinazioni di archiviazione in uno spazio dei nomi aggregato.

Tenere presente che le esportazioni di archiviazione devono essere accessibili dalla rete virtuale della cache. Per l'archiviazione hardware locale, potrebbe essere necessario configurare un server DNS in grado di risolvere i nomi host per l'accesso con archiviazione NFS. Per altre informazioni, vedere [accesso DNS](hpc-cache-prereqs.md#dns-access).

È possibile aggiungere destinazioni di archiviazione durante la creazione della cache HPC di Azure o successivamente. La procedura è leggermente diversa a seconda che si stia aggiungendo un archivio BLOB di Azure o un'esportazione NFS. Di seguito sono riportati i dettagli per ognuno di essi.

## <a name="add-storage-targets-while-creating-the-cache"></a>Aggiungere destinazioni di archiviazione durante la creazione della cache

Utilizzare la scheda **destinazioni di archiviazione** della creazione guidata cache per definire l'archiviazione nello stesso momento in cui si crea l'istanza della cache.

![screenshot della pagina Destinazioni di archiviazione](media/create-targets.png)

Fare clic sul collegamento **Aggiungi destinazione di archiviazione** per aggiungere spazio di archiviazione.

## <a name="add-storage-targets-from-the-cache"></a>Aggiungere destinazioni di archiviazione dalla cache

Dalla portale di Azure aprire l'istanza di cache e fare clic su **destinazioni di archiviazione** nella barra laterale sinistra. La pagina destinazione di archiviazione elenca tutte le destinazioni esistenti e fornisce un collegamento per aggiungerne una nuova.

## <a name="add-a-new-azure-blob-storage-target"></a>Aggiungere una nuova destinazione di archiviazione BLOB di Azure

Una nuova destinazione di archiviazione BLOB necessita di un contenitore BLOB vuoto o di un contenitore popolato con i dati nel formato del filesystem cloud di Azure HPC cache. Altre informazioni sul precaricamento di un contenitore BLOB in [spostare i dati nell'archivio BLOB di Azure](hpc-cache-ingest.md).

Per definire un contenitore BLOB di Azure, immettere queste informazioni.

![screenshot della pagina Aggiungi destinazione di archiviazione, popolato con le informazioni per una nuova destinazione di archiviazione BLOB di Azure](media/hpc-cache-add-blob.png)

* **Nome destinazione di archiviazione** : impostare un nome che identifichi la destinazione di archiviazione nella cache HPC di Azure.
* **Tipo di destinazione** : scegliere **BLOB**.
* **Account di archiviazione** : selezionare l'account con il contenitore a cui fare riferimento.

  Per accedere all'account di archiviazione, è necessario autorizzare l'istanza della cache, come descritto in [aggiungere i ruoli di accesso](#add-the-access-control-roles-to-your-account).
* **Contenitore di archiviazione** : selezionare il contenitore BLOB per la destinazione.

* **Percorso dello spazio dei nomi virtuale** : impostare il FilePath per il client per questa destinazione di archiviazione. Per ulteriori informazioni sulla funzionalità spazio dei nomi virtuale, vedere [configurare lo spazio dei nomi aggregato](hpc-cache-namespace.md) .

<!--  The namespace path value must end with a slash (``/``) and should not start with one.  -->

Al termine, fare clic su **OK** per aggiungere la destinazione di archiviazione.

### <a name="add-the-access-control-roles-to-your-account"></a>Aggiungere i ruoli di controllo di accesso all'account

La cache HPC di Azure usa il [controllo degli accessi in base al ruolo (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/index) per autorizzare l'applicazione della cache ad accedere all'account di archiviazione per le destinazioni di archiviazione BLOB di Azure.

Il proprietario dell'account di archiviazione deve aggiungere in modo esplicito il collaboratore per l' [account di archiviazione](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-account-contributor) Rolls e i [dati BLOB di archiviazione](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor) per l'utente "StorageCache Resource Provider".

È possibile eseguire questa operazione in anticipo oppure facendo clic su un collegamento nella pagina in cui si aggiunge una destinazione di archiviazione BLOB.

Passaggi per aggiungere i ruoli RBAC:

1. Aprire la pagina **controllo di accesso (IAM)** per l'account di archiviazione. Il collegamento nella pagina **Aggiungi destinazione di archiviazione** apre automaticamente questa pagina per l'account selezionato.

1. Fare clic su nella parte superiore della pagina e scegliere **Aggiungi un'assegnazione di ruolo.** **+**

1. Selezionare il ruolo "collaboratore account di archiviazione" nell'elenco.

1. Nel campo **assegna accesso a** lasciare il valore predefinito selezionato ("Azure ad utente, gruppo o entità servizio").  

1. Nel campo **Seleziona** cercare "storagecache".  Questa stringa deve corrispondere a un'entità di sicurezza, denominata "HPC cache Resource Provider". Fare clic su tale entità per selezionarla.

1. Fare clic sul pulsante **Salva** per aggiungere l'assegnazione di ruolo all'account di archiviazione.

1. Ripetere questo processo per assegnare il ruolo "collaboratore dati BLOB di archiviazione".  

![screenshot della GUI Aggiungi assegnazione ruolo](media/hpc-cache-add-role.png)

## <a name="add-a-new-nfs-storage-target"></a>Aggiungere una nuova destinazione di archiviazione NFS

Una destinazione di archiviazione NFS include alcuni campi aggiuntivi per specificare come raggiungere l'esportazione di archiviazione e come memorizzare nella cache i dati in modo efficiente. Inoltre, è possibile creare più percorsi dello spazio dei nomi da un host NFS se sono disponibili più esportazioni.

![Screenshot della pagina Aggiungi destinazione di archiviazione con destinazione NFS definita](media/hpc-cache-add-nfs-target.png)

Fornire queste informazioni per una destinazione di archiviazione supportata da NFS:

* **Nome destinazione di archiviazione** : impostare un nome che identifichi la destinazione di archiviazione nella cache HPC di Azure.

* **Tipo di destinazione** : scegliere **NFS**.

* Nome **host** : immettere l'indirizzo IP o il nome di dominio completo per il sistema di archiviazione NFS. Usare un nome di dominio solo se la cache ha accesso a un server DNS in grado di risolvere il nome.

* **Modello di utilizzo** : scegliere uno dei profili di caching dei dati in base al flusso di lavoro, descritto di [seguito in scegliere un modello di utilizzo](#choose-a-usage-model).

È possibile creare più percorsi dello spazio dei nomi per rappresentare esportazioni diverse nello stesso sistema di archiviazione NFS, ma è necessario crearle tutte da un'unica destinazione di archiviazione.

Per ogni esportazione, immettere i valori seguenti:

* **Percorso dello spazio dei nomi virtuale** : impostare il FilePath per il client per questa destinazione di archiviazione. Per ulteriori informazioni sulla funzionalità spazio dei nomi virtuale, vedere [configurare lo spazio dei nomi aggregato](hpc-cache-namespace.md) .

<!--  The virtual path should start with a slash ``/``. -->

* **Percorso di esportazione NFS** : immettere il percorso dell'esportazione NFS.

* **Percorso sottodirectory** : se si vuole montare una sottodirectory specifica dell'esportazione, immetterla qui. In caso contrario, lasciare vuoto questo campo. 

Al termine, fare clic su **OK** per aggiungere la destinazione di archiviazione.

### <a name="choose-a-usage-model"></a>Scegliere un modello di utilizzo 
<!-- link in GUI to this heading -->

Quando si crea una destinazione di archiviazione che punta a un sistema di archiviazione NFS, è necessario scegliere il *modello di utilizzo* per tale destinazione. Questo modello determina il modo in cui i dati vengono memorizzati nella cache.

* Lettura elevata: se si usa per la maggior parte la cache per velocizzare l'accesso in lettura ai dati, scegliere questa opzione. 

* Lettura/scrittura: se i client usano la cache per la lettura e la scrittura, scegliere questa opzione.

* Client ignorano la cache: scegliere questa opzione se i client scrivono i dati direttamente nel sistema di archiviazione senza prima scrivere nella cache.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver creato le destinazioni di archiviazione, prendere in considerazione una delle seguenti attività:

* [Montare la cache HPC di Azure](hpc-cache-mount.md)
* [Spostare i dati nell'archivio BLOB di Azure](hpc-cache-ingest.md)