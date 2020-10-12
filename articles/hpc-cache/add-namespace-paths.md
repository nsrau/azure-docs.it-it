---
title: Configurare lo spazio dei nomi aggregato della cache HPC di Azure
description: Come creare percorsi destinati ai client per l'archiviazione back-end con la cache HPC di Azure
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 09/30/2020
ms.author: v-erkel
ms.openlocfilehash: 090e3f93d025fe87ad5b89a98193574595f3d632
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91614631"
---
# <a name="set-up-the-aggregated-namespace"></a>Configurare lo spazio dei nomi aggregato

Dopo aver creato le destinazioni di archiviazione, è necessario creare anche percorsi dello spazio dei nomi. I computer client usano questi percorsi virtuali per accedere ai file attraverso la cache anziché connettersi direttamente all'archiviazione back-end. Questo sistema consente agli amministratori della cache di modificare i sistemi di archiviazione back-end senza dover riscrivere le istruzioni client.

Per ulteriori informazioni su questa funzionalità, vedere [pianificare lo spazio dei nomi aggregato](hpc-cache-namespace.md) .

La pagina **dello spazio dei nomi** nel portale di Azure Mostra i percorsi utilizzati dai client per accedere ai dati tramite la cache. Utilizzare questa pagina per creare, rimuovere o modificare i percorsi dello spazio dei nomi. È anche possibile configurare percorsi dello spazio dei nomi tramite l'interfaccia della riga di comando di Azure.

Tutti i percorsi del client esistenti sono elencati nella pagina **spazio dei nomi** . Se una destinazione di archiviazione non dispone di alcun percorso, non viene visualizzata nella tabella.

È possibile ordinare le colonne della tabella facendo clic sulle frecce per comprendere meglio lo spazio dei nomi aggregato della cache.

![screenshot della pagina dello spazio dei nomi del portale con due percorsi in una tabella. Intestazioni di colonna: percorso dello spazio dei nomi, destinazione di archiviazione, percorso di esportazione ed esportazione sottodirectory. Gli elementi nella prima colonna sono collegamenti selezionabili. Pulsanti principali: Aggiungi percorso spazio dei nomi, Aggiorna, Elimina](media/namespace-page.png)

## <a name="add-or-edit-client-facing-namespace-paths"></a>Aggiungere o modificare percorsi dello spazio dei nomi per il client

È necessario creare almeno un percorso dello spazio dei nomi prima che i client possano accedere alla destinazione di archiviazione. Per altre informazioni sull'accesso client, vedere [montare la cache HPC di Azure](hpc-cache-mount.md) .

### <a name="blob-namespace-paths"></a>Percorsi dello spazio dei nomi BLOB

Una destinazione di archiviazione BLOB di Azure può avere un solo percorso dello spazio dei nomi.

Seguire le istruzioni seguenti per impostare o modificare il percorso con l'portale di Azure o l'interfaccia della riga di comando di Azure.

### <a name="portal"></a>[Portale](#tab/azure-portal)

Dalla portale di Azure caricare la pagina impostazioni **spazio dei nomi** . Da questa pagina è possibile aggiungere, modificare o eliminare percorsi dello spazio dei nomi.

* **Aggiungere un nuovo percorso:** Fare clic sul pulsante **+ Aggiungi** nella parte superiore e immettere le informazioni nel pannello modifica.

  * Selezionare la destinazione di archiviazione nell'elenco a discesa. In questa schermata non è possibile selezionare la destinazione di archiviazione BLOB perché contiene già un percorso dello spazio dei nomi.

    ![Screenshot del nuovo spazio dei nomi modificare i campi con il selettore di destinazione di archiviazione esposto](media/namespace-select-storage-target.png)

  * Per una destinazione di archiviazione BLOB di Azure, i percorsi di esportazione e sottodirectory vengono impostati automaticamente su ``/`` .

* **Modificare un percorso esistente:** Fare clic sul percorso dello spazio dei nomi. Verrà visualizzato il pannello modifica in cui è possibile modificare il percorso.

  ![Screenshot della pagina dello spazio dei nomi dopo aver fatto clic sul percorso di uno spazio dei nomi BLOB. i campi di modifica vengono visualizzati in un riquadro a destra](media/edit-namespace-blob.png)

* **Eliminare un percorso dello spazio dei nomi:** Selezionare la casella di controllo a sinistra del percorso e fare clic sul pulsante **Elimina** .

### <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

[!INCLUDE [cli-reminder.md](includes/cli-reminder.md)]

Quando si usa l'interfaccia della riga di comando di Azure è necessario aggiungere un percorso dello spazio dei nomi quando si crea la destinazione di archiviazione. Per informazioni dettagliate, vedere [aggiungere una nuova destinazione di archiviazione BLOB di Azure](hpc-cache-add-storage.md?tabs=azure-cli#add-a-new-azure-blob-storage-target) .

Per aggiornare il percorso dello spazio dei nomi della destinazione, usare il comando [AZ HPC-cache BLOB-Storage-target Update](/cli/azure/ext/hpc-cache/hpc-cache/blob-storage-target#ext-hpc-cache-az-hpc-cache-blob-storage-target-update) . Gli argomenti per il comando Update sono simili a quelli del comando create, ad eccezione del fatto che non si passa il nome del contenitore o l'account di archiviazione.

Non è possibile eliminare un percorso dello spazio dei nomi da una destinazione di archiviazione BLOB con l'interfaccia della riga di comando di Azure, ma è possibile sovrascrivere il percorso con un valore diverso.

---

### <a name="nfs-namespace-paths"></a>Percorsi dello spazio dei nomi NFS

Una destinazione di archiviazione NFS può avere più percorsi virtuali, purché ogni percorso rappresenti un'esportazione o una sottodirectory diversa nello stesso sistema di archiviazione.

Quando si pianifica lo spazio dei nomi per una destinazione di archiviazione NFS, tenere presente che ogni percorso deve essere univoco e non può essere una sottodirectory di un altro percorso dello spazio dei nomi. Se, ad esempio, si dispone di un percorso dello spazio dei nomi chiamato ``/parent-a`` , non è possibile creare anche percorsi dello spazio dei nomi come ``/parent-a/user1`` e ``/parent-a/user2`` . Tali percorsi di directory sono già accessibili nello spazio dei nomi come sottodirectory di ``/parent-a`` .

Tutti i percorsi dello spazio dei nomi per un sistema di archiviazione NFS vengono creati in una destinazione di archiviazione. La maggior parte delle configurazioni della cache può supportare fino a dieci percorsi dello spazio dei nomi per destinazione di archiviazione, ma le configurazioni più grandi possono supportare fino a 20.

Questo elenco Mostra il numero massimo di percorsi dello spazio dei nomi per configurazione.

* Fino a 2 GB/s di velocità effettiva:

  * cache da 3 TB-10 percorsi dello spazio dei nomi
  * cache da 6 TB-10 percorsi dello spazio dei nomi
  * cache da 23 TB-20 percorsi dello spazio dei nomi

* Fino a 5 GB/s di velocità effettiva:

  * cache da 6 TB-10 percorsi dello spazio dei nomi
  * cache da 12 TB-10 percorsi dello spazio dei nomi
  * cache da 24 TB-20 percorsi dello spazio dei nomi

* Fino a 8 GB/s di velocità effettiva:

  * cache da 12 TB-10 percorsi dello spazio dei nomi
  * cache da 24 TB-10 percorsi dello spazio dei nomi
  * cache da 48 TB-20 percorsi dello spazio dei nomi

Per ogni percorso dello spazio dei nomi NFS, fornire il percorso per il client, l'esportazione del sistema di archiviazione e, facoltativamente, una sottodirectory di esportazione.

### <a name="portal"></a>[Portale](#tab/azure-portal)

Dalla portale di Azure caricare la pagina impostazioni **spazio dei nomi** . Da questa pagina è possibile aggiungere, modificare o eliminare percorsi dello spazio dei nomi.

* **Per aggiungere un nuovo percorso:** Fare clic sul pulsante **+ Aggiungi** nella parte superiore e immettere le informazioni nel pannello modifica.
* **Per modificare un percorso esistente:** Fare clic sul percorso dello spazio dei nomi. Verrà visualizzato il pannello modifica in cui è possibile modificare il percorso.
* **Per eliminare un percorso dello spazio dei nomi:** Selezionare la casella di controllo a sinistra del percorso e fare clic sul pulsante **Elimina** .

Immettere questi valori per ogni percorso dello spazio dei nomi:

* **Percorso dello spazio dei nomi** : percorso del file per il client.

* **Destinazione di archiviazione** : se si crea un nuovo percorso dello spazio dei nomi, selezionare una destinazione di archiviazione dal menu a discesa.

* **Percorso di esportazione** : immettere il percorso dell'esportazione NFS. Assicurarsi di digitare correttamente il nome esportazione. il portale convalida la sintassi per questo campo, ma non controlla l'esportazione finché non viene inviata la modifica.

* **Esporta sottodirectory** : se si vuole che questo percorso Monti una sottodirectory specifica dell'esportazione, immetterla qui. In caso contrario, lasciare vuoto questo campo.

![screenshot della pagina dello spazio dei nomi del portale con la pagina di aggiornamento aperta a destra](media/update-namespace-nfs.png)

### <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

[!INCLUDE [cli-reminder.md](includes/cli-reminder.md)]

Quando si usa l'interfaccia della riga di comando di Azure, è necessario aggiungere almeno un percorso dello spazio dei nomi quando si crea la destinazione di archiviazione. Per informazioni dettagliate, vedere [aggiungere una nuova destinazione di archiviazione NFS](hpc-cache-add-storage.md?tabs=azure-cli#add-a-new-nfs-storage-target) .

Per aggiornare il percorso dello spazio dei nomi della destinazione o aggiungere percorsi aggiuntivi, usare il comando [AZ HPC-cache NFS-storage-target Update](/cli/azure/ext/hpc-cache/hpc-cache/nfs-storage-target#ext-hpc-cache-az-hpc-cache-nfs-storage-target-update) . Utilizzare l' ``--junction`` opzione per specificare tutti i percorsi dello spazio dei nomi desiderati.

Le opzioni utilizzate per il comando di aggiornamento sono simili a quelle del comando "Crea", ad eccezione del fatto che non si passano le informazioni sul sistema di archiviazione (indirizzo IP o nome host) e il modello di utilizzo è facoltativo. Per ulteriori informazioni sulla sintassi dell'opzione, vedere [aggiungere una nuova destinazione di archiviazione NFS](hpc-cache-add-storage.md?tabs=azure-cli#add-a-new-nfs-storage-target) ``--junction`` .

---

## <a name="next-steps"></a>Passaggi successivi

Dopo aver creato lo spazio dei nomi aggregato per le destinazioni di archiviazione, è possibile montare i client nella cache. Per altre informazioni, leggere questi articoli.

* [Montare la cache HPC di Azure](hpc-cache-mount.md)
* [Spostare i dati nell'archivio BLOB di Azure](hpc-cache-ingest.md)
