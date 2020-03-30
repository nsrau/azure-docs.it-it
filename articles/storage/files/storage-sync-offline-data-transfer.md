---
title: Eseguire la migrazione dei dati in Sincronizzazione file di Azure con Azure Data BoxMigrate data into Azure File Sync with Azure Data Box
description: Eseguire la migrazione di dati in blocco in modo compatibile con Sincronizzazione file di Azure.Migrate bulk data in a modo that's compatible with Azure File Sync.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 9398aceeb7465392e82aeaa5760f6c0504f8e33d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159524"
---
# <a name="migrate-bulk-data-to-azure-file-sync-with-azure-databox"></a>Eseguire la migrazione di dati in blocco in Sincronizzazione file di Azure con Azure DataBoxMigrate bulk data to Azure File Sync with Azure DataBox
È possibile eseguire la migrazione di massa di dati in Sincronizzazione file di Azure in due modi:You can migrate bulk data to Azure File Sync in two ways:

* **Caricare i file usando Sincronizzazione file di Azure.Upload your files by using Azure File Sync.** Questo è il metodo più semplice. Spostare i file in locale in Windows Server 2012 R2 o versione successiva e installare l'agente di Sincronizzazione file di Azure.Move your files locally to Windows Server 2012 R2 or later, and install the Azure File Sync agent. Dopo aver configurato la sincronizzazione, i file verranno caricati dal server. (I nostri clienti attualmente sperimentano una velocità di caricamento media di 1 TiB circa ogni due giorni.) Per assicurarsi che il server non utilizzi troppa larghezza di banda per il data center, è possibile impostare una pianificazione della [limitazione della larghezza](storage-sync-files-server-registration.md#ensuring-azure-file-sync-is-a-good-neighbor-in-your-datacenter)di banda.
* **Trasferisci i tuoi file offline.** Se la larghezza di banda non è sufficiente, potrebbe non essere possibile caricare file in Azure in un periodo di tempo ragionevole. La sfida è la sincronizzazione iniziale di tutto il set di file. Per superare questa sfida, usare strumenti di migrazione in blocco offline, ad esempio la [famiglia Azure Data Box](https://azure.microsoft.com/services/storage/databox). 

Questo articolo illustra come eseguire la migrazione dei file offline in modo compatibile con Sincronizzazione file di Azure.This article explains how to migrate files offline in a modo that's compatible with Azure File Sync. Seguire queste istruzioni per evitare conflitti di file e per mantenere gli elenchi di controllo di accesso (ACL) di file e cartelle dopo aver abilitato la sincronizzazione.

## <a name="migration-tools"></a>Strumenti di migrazione
Il processo descritto in questo articolo funziona non solo per Data Box, ma anche per altri strumenti di migrazione offline. Funziona anche per strumenti come AzCopy, Robocopy o strumenti e servizi partner che funzionano direttamente su Internet. Tuttavia, per superare la richiesta di caricamento iniziale, seguire i passaggi descritti in questo articolo per usare questi strumenti in modo compatibile con Sincronizzazione file di Azure.However to overcome the initial upload challenge, follow the steps in this article to use these tools in a modo that's compatible with Azure File Sync.

In some cases, you need to move from one Windows Server to another Windows Server before adopting Azure File Sync. [Il servizio di migrazione dell'archiviazione](https://aka.ms/storagemigrationservice) (SMS) può essere utile. Se è necessario eseguire la migrazione a una versione del sistema operativo Server supportata da Sincronizzazione file di Azure (Windows Server 2012R2 e versioni 2) o semplicemente eseguire la migrazione perché si acquista un nuovo sistema per Sincronizzazione file di Azure, SMS ha numerose funzionalità e vantaggi che consentono di ottenere il migrazione senza intoppi.

## <a name="benefits-of-using-a-tool-to-transfer-data-offline"></a>Vantaggi dell'utilizzo di uno strumento per trasferire i dati offline
Ecco i principali vantaggi dell'utilizzo di uno strumento di trasferimento come Data Box per la migrazione offline:

- Non è necessario caricare tutti i file in rete. Per gli spazi dei nomi di grandi dimensioni, questo strumento potrebbe risparmiare molto tempo e larghezza di banda di rete.
- Quando si usa Sincronizzazione file di Azure, indipendentemente dallo strumento di trasferimento usato (Casella dati, servizio Importazione/Esportazione di Azure e così via), il server attivo carica solo i file che cambiano dopo lo spostamento dei dati in Azure.When you use Azure File Sync, from st as which transfer tool you use (Data Box, Azure Import/Export service, and so on), your live server uploads only the files that change after you move the data to Azure.
- Sincronizzazione file di Azure consente di sincronizzare gli ACL di file e cartelle anche se lo strumento di migrazione in blocco offline non consente di trasportare gli ACL.
- Data Box e Azure File Sync non richiedono tempi di inattività. Quando si usa Data Box per trasferire dati in Azure, si usa la larghezza di banda di rete in modo efficiente e si mantiene la fedeltà dei file. You also keep your namespace up to date by uploading only the files that change after you move the data to Azure.

## <a name="prerequisites-for-the-offline-data-transfer"></a>Prerequisiti per il trasferimento di dati offlinePrerequisites for the offline data transfer
Non abilitare la sincronizzazione nel server di cui si esegue la migrazione prima di completare il trasferimento dei dati offline. Altri aspetti da considerare prima di iniziare sono i seguenti:

- Se si prevede di utilizzare Data Box per la migrazione in blocco: esaminare [i prerequisiti](../../databox/data-box-deploy-ordered.md#prerequisites)di distribuzione per Data Box .
- Pianificare la topologia finale di Sincronizzazione file di Azure: pianificare una distribuzione di [Sincronizzazione file](storage-sync-files-planning.md) di AzurePlan your final Azure File Sync topology: Plan for an Azure File Sync deployment
- Selezionare gli account di archiviazione di Azure che conterranno le condivisioni file con cui si vuole eseguire la sincronizzazione. Assicurarsi che la migrazione in blocco venga eseguita in condivisioni di staging temporanee negli stessi account di archiviazione. La migrazione in blocco può essere abilitata solo usando una condivisione finale e una condivisione di staging che si trovano nello stesso account di archiviazione.
- Una migrazione in blocco può essere usata solo quando si crea una nuova relazione di sincronizzazione con un percorso server. Non è possibile abilitare una migrazione in blocco con una relazione di sincronizzazione esistente.


## <a name="process-for-offline-data-transfer"></a>Processo per il trasferimento di dati offline
Ecco come configurare Sincronizzazione file di Azure in modo compatibile con gli strumenti di migrazione in blocco, ad esempio Azure Data Box:Here's how to set up Azure File Sync in a modo that's compatible with bulk migration tools such as Azure Data Box:

![Diagramma che illustra come configurare Sincronizzazione file di Azure](media/storage-sync-files-offline-data-transfer/data-box-integration-1-600.png)

| Passaggio | Dettagli |
|---|---------------------------------------------------------------------------------------|
| ![Passaggio 1](media/storage-sync-files-offline-data-transfer/bullet_1.png) | [Ordinare il Data Box](../../databox/data-box-deploy-ordered.md). La famiglia Data Box offre [diversi prodotti](https://azure.microsoft.com/services/storage/databox/data) per soddisfare le tue esigenze. Quando si riceve la data box, seguire la [relativa documentazione per copiare i dati in](../../databox/data-box-deploy-copy-data.md#copy-data-to-data-box) questo percorso UNC nella casella dati: * \\<DeviceIPAddres\>\<StorageAccountName_AzFile\>\<ShareName\>*. In questo caso, *ShareName* è il nome della condivisione di gestione temporanea. Inviare il Data Box ad Azure. |
| ![Passaggio 2](media/storage-sync-files-offline-data-transfer/bullet_2.png) | Attendere che i file vengano visualizzati nelle condivisioni file di Azure scelte come condivisioni di gestione temporanea temporanea. *Non abilitare la sincronizzazione con queste condivisioni.* |
| ![Passaggio 3](media/storage-sync-files-offline-data-transfer/bullet_3.png) | Creare una nuova condivisione vuota per ogni condivisione file creata automaticamente da Data Box.Create a new empty share for each file share that Data Box created for you. Questa nuova condivisione deve trovarsi nello stesso account di archiviazione della condivisione Data Box.This new share should be in the same storage account as the Data Box share. [Come creare una nuova condivisione file di Azure](storage-how-to-create-file-share.md). |
| ![Passaggio 4](media/storage-sync-files-offline-data-transfer/bullet_4.png) | [Creare un gruppo](storage-sync-files-deployment-guide.md#create-a-sync-group-and-a-cloud-endpoint) di sincronizzazione in un servizio di sincronizzazione archiviazione. Fare riferimento alla condivisione vuota come endpoint cloud. Ripetere questo passaggio per ogni condivisione file del Data Box. [Configurare Sincronizzazione file di Azure](storage-sync-files-deployment-guide.md). |
| ![Passaggio 5](media/storage-sync-files-offline-data-transfer/bullet_5.png) | [Aggiungere la directory del server live come endpoint server](storage-sync-files-deployment-guide.md#create-a-server-endpoint). Nel processo specificare che i file sono stati spostati in Azure e fare riferimento alle condivisioni di gestione temporanea. È possibile abilitare o disabilitare la suddivisione in livelli cloud in base alle esigenze. Durante la creazione di un endpoint server nel server attivo, fare riferimento alla condivisione di gestione temporanea. Nel pannello **Aggiungi endpoint server,** in **Trasferimento dati offline,** selezionare **Abilitato**e quindi selezionare la condivisione di gestione temporanea che deve trovarsi nello stesso account di archiviazione dell'endpoint cloud. In questo caso, l'elenco delle condivisioni disponibili viene filtrato in base all'account di archiviazione e alle condivisioni che non sono già sincronizzate. |

![Screenshot dell'interfaccia utente del portale di Azure che mostra come abilitare il trasferimento di dati offline durante la creazione di un nuovo endpoint server](media/storage-sync-files-offline-data-transfer/data-box-integration-2-600.png)

## <a name="syncing-the-share"></a>Sincronizzazione della condivisione
Dopo aver creato l'endpoint server, verrà avviata la sincronizzazione. Il processo di sincronizzazione determina se ogni file sul server esiste anche nella condivisione di gestione temporanea in cui Data Box ha depositato i file. Se il file esiste, il processo di sincronizzazione copia il file dalla condivisione di gestione temporanea anziché caricarlo dal server. Se il file non esiste nella condivisione di gestione temporanea o se nel server locale è disponibile una versione più recente, il processo di sincronizzazione carica il file dal server locale.

> [!IMPORTANT]
> È possibile abilitare la modalità di migrazione in blocco solo durante la creazione di un endpoint server. Dopo aver stabilito un endpoint server, non è possibile integrare i dati di migrazione in blocco da un server già sincronizzato nello spazio dei nomi.

## <a name="acls-and-timestamps-on-files-and-folders"></a>ACL e timestamp di file e cartelle
Sincronizzazione file di Azure garantisce che gli ACL di file e cartelle vengano sincronizzati dal server attivo anche se lo strumento di migrazione in blocco usato non è stato inizialmente trasportato gli ACL. Per questo motivo, la condivisione di gestione temporanea non deve contenere alcun ACL per file e cartelle. Quando si abilita la funzionalità di migrazione dei dati offline quando si crea un nuovo endpoint server, tutti gli ACL dei file vengono sincronizzati nel server. Vengono sincronizzati anche i timestamp appena creati e modificati.

## <a name="shape-of-the-namespace"></a>Forma dello spazio dei nomi
Quando si abilita la sincronizzazione, il contenuto del server determina la forma dello spazio dei nomi. Se i file vengono eliminati dal server locale al termine dello snapshot e della migrazione di Data Box, questi file non vengono spostati nello spazio dei nomi live di sincronizzazione. Rimangono nella condivisione temporanea, ma non vengono copiati. Ciò è necessario perché la sincronizzazione mantiene lo spazio dei nomi in base al server attivo. Lo *snapshot* della casella di dati è solo un punto di gestione temporanea per una copia efficiente dei file. Non è l'autorità per la forma dello spazio dei nomi attivo.

## <a name="cleaning-up-after-bulk-migration"></a>Pulizia dopo la migrazione di massaCleaning up after bulk migration 
Quando il server completa la sincronizzazione iniziale dello spazio dei nomi, i file di cui è stata eseguita la migrazione in blocco di Data Box utilizzano la condivisione file di gestione temporanea. Nella sezione **Trasferimento dati offline** del pannello Proprietà endpoint **server** del portale di Azure lo stato cambia da **In corso** a **Completato.** 

![Screenshot del pannello Proprietà endpoint server, in cui si trovano i controlli di stato e disabilitazione per il trasferimento di dati offline](media/storage-sync-files-offline-data-transfer/data-box-integration-3-444.png)

A questo punto è possibile pulire la condivisione di gestione temporanea per risparmiare sui costi:

1. Nel pannello **Proprietà endpoint server,** quando lo stato è **Completato,** selezionare **Disabilita trasferimento dati offline**.
2. Valutare la possibilità di eliminare la condivisione di gestione temporanea per risparmiare sui costi. La condivisione di gestione temporanea probabilmente non contiene ACL di file e cartelle, quindi è improbabile che sia utile. Per scopi temporizzato di backup, creare uno snapshot reale [della condivisione file di Azure per la sincronizzazione.](storage-snapshots-files.md) È possibile [configurare Backup]( ../../backup/backup-afs.md) di Azure per creare snapshot in base a una pianificazione.

Disattivare la modalità di trasferimento dati non in linea solo quando lo stato è **Completato** o quando si desidera annullare a causa di una configurazione errata. Se si disabilita la modalità durante una distribuzione, i file inizieranno a caricarsi dal server anche se la condivisione di gestione temporanea è ancora disponibile.

> [!IMPORTANT]
> Dopo aver disabilitato la modalità di trasferimento dati offline, non è possibile abilitarla nuovamente, anche se la condivisione di gestione temporanea dalla migrazione in blocco è ancora disponibile.

## <a name="next-steps"></a>Passaggi successivi
- [Pianificare una distribuzione di Sincronizzazione file di Azure](storage-sync-files-planning.md)
- [Come distribuire Sincronizzazione file di Azure](storage-sync-files-deployment-guide.md)
