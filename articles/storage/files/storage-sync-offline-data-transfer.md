---
title: Eseguire la migrazione dei dati in sincronizzazione File di Azure usando Azure Data Box e altri metodi
description: Eseguire la migrazione dei dati in blocco in modo che sia compatibile con sincronizzazione File di Azure.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 02/12/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: d1ec5168b898d0aa75c12e6eb435e20c09de1929
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64700280"
---
# <a name="migrate-bulk-data-to-azure-file-sync"></a>Eseguire la migrazione di dati per operazioni bulk per sincronizzazione File di Azure
È possibile migrare i dati per operazioni bulk per sincronizzazione File di Azure in due modi:

* **Caricare i file tramite sincronizzazione File di Azure.** Si tratta del metodo più semplice. Spostare i file in locale a Windows Server 2012 R2 o versione successiva e installare l'agente sincronizzazione File di Azure. Dopo aver configurato la sincronizzazione, verranno caricati i file dal server. (Attualmente i nostri clienti esperienza una velocità di caricamento medio da 1 TiB su ogni due giorni.) Per assicurarsi che il server non usa una quantità eccessiva di larghezza di banda per il tuo Data Center, è possibile configurare un [pianificazione di limitazione della larghezza di banda](storage-sync-files-server-registration.md#ensuring-azure-file-sync-is-a-good-neighbor-in-your-datacenter).
* **Trasferire i file offline.** Se non si dispone di larghezza di banda sufficiente, potrebbe non essere in grado di caricare file in Azure in un intervallo di tempo ragionevole. Il problema è la sincronizzazione iniziale dell'intero set di file. Per risolvere questo problema, usare gli strumenti di migrazione di massa non in linea, ad esempio la [famiglia di Azure Data Box](https://azure.microsoft.com/services/storage/databox). 

Questo articolo illustra come eseguire la migrazione di file offline in modo che sia compatibile con sincronizzazione File di Azure. Seguire queste istruzioni per evitare conflitti tra file e di mantenere i file ed elenchi di controllo di accesso (ACL) alle cartelle e i timestamp dopo aver abilitato la sincronizzazione.

## <a name="online-migration-tools"></a>Strumenti di migrazione online
Il processo che descritto in questo articolo funziona non solo per Data Box, ma anche per altri strumenti di migrazione offline. Funziona anche per gli strumenti online come AzCopy, Robocopy o strumenti di partner e servizi. Tuttavia è ovviare iniziale carica sfida, seguire i passaggi descritti in questo articolo per usare questi strumenti in modo che sia compatibile con sincronizzazione File di Azure.


## <a name="benefits-of-using-a-tool-to-transfer-data-offline"></a>Vantaggi dell'uso di uno strumento per il trasferimento dei dati offline
Ecco i principali vantaggi dell'uso di uno strumento di trasferimento, ad esempio Data Box per la migrazione offline:

- Non è necessario caricare tutti i file in rete. Spazi dei nomi di grandi dimensioni, questo strumento è stato possibile risparmiare tempo e larghezza di banda di rete significativo.
- Quando si Usa sincronizzazione File di Azure, indipendentemente da quale strumento di trasferimento è usare (Data Box, il servizio importazione/esportazione di Azure e così via), il server in tempo reale consente di caricare solo i file che cambiano dopo aver spostato i dati di Azure.
- Sincronizzazione File di Azure consente di sincronizzare gli ACL di file e cartella anche se lo strumento di migrazione di massa non in linea non trasporto ACL.
- Data Box e sincronizzazione File di Azure è necessario alcun tempo di inattività. Quando si usa Data Box per trasferire i dati in Azure, utilizzare la larghezza di banda di rete in modo efficiente e conservare la fedeltà di file. Anche mantenere lo spazio dei nomi aggiornati caricando solo i file che cambiano dopo aver spostato i dati di Azure.

## <a name="prerequisites-for-the-offline-data-transfer"></a>Prerequisiti per il trasferimento dei dati offline
Non è necessario abilitare la sincronizzazione sul server che si esegue la migrazione prima di completare il trasferimento dei dati offline. Di seguito sono riportati altri aspetti da considerare prima di iniziare:

- Se si prevede di usare Data Box per la migrazione in blocco: Esaminare i [prerequisiti della distribuzione per Data Box](../../databox/data-box-deploy-ordered.md#prerequisites).
- Pianificare la topologia di sincronizzazione File di Azure finale: [Pianificare una distribuzione di Sincronizzazione file di Azure](storage-sync-files-planning.md)
- Selezionare gli account di archiviazione di Azure che conterranno le condivisioni file con cui si vuole eseguire la sincronizzazione. Assicurarsi che la migrazione in blocco venga eseguita in condivisioni di staging temporanee negli stessi account di archiviazione. La migrazione in blocco può essere abilitata solo usando una condivisione finale e una condivisione di staging che si trovano nello stesso account di archiviazione.
- Una migrazione in blocco può essere usata solo quando si crea una nuova relazione di sincronizzazione con un percorso server. Non è possibile abilitare una migrazione in blocco con una relazione di sincronizzazione esistente.


## <a name="process-for-offline-data-transfer"></a>Processo di trasferimento dei dati offline
Di seguito viene illustrato come configurare sincronizzazione File di Azure in modo che sia compatibile con strumenti di migrazione per operazioni bulk, ad esempio Azure Data Box:

![Diagramma che illustra come impostare la sincronizzazione File di Azure](media/storage-sync-files-offline-data-transfer/data-box-integration-1-600.png)

| Passaggio | Dettagli |
|---|---------------------------------------------------------------------------------------|
| ![Passaggio 1](media/storage-sync-files-offline-data-transfer/bullet_1.png) | [Ordinare il Data Box](../../databox/data-box-deploy-ordered.md). Le offerte della famiglia di Data Box [diversi prodotti](https://azure.microsoft.com/services/storage/databox/data) in base alle esigenze. Quando si riceve il Data Box, seguire le [la documentazione per copiare i dati](../../databox/data-box-deploy-copy-data.md#copy-data-to-data-box) a questo percorso UNC in Data Box:  *\\< DeviceIPAddres\>\<StorageAccountName_AzFile\> \<ShareName\>* . In questo caso, *nomecondivisione* è il nome della condivisione di gestione temporanea. Inviare il Data Box ad Azure. |
| ![Passaggio 2](media/storage-sync-files-offline-data-transfer/bullet_2.png) | Attendere finché non sono disponibili i file nelle condivisioni di file di Azure che si è scelto come condivisioni di staging temporanee. *Non abilitare la sincronizzazione in tali condivisioni.* |
| ![Passaggio 3](media/storage-sync-files-offline-data-transfer/bullet_3.png) | Creare una nuova condivisione vuota per ogni condivisione di file che Data Box ha creato automaticamente. Questa nuova condivisione deve essere nello stesso account di archiviazione come condivisione di Data Box. [Come creare una nuova condivisione file di Azure](storage-how-to-create-file-share.md). |
| ![Passaggio 4](media/storage-sync-files-offline-data-transfer/bullet_4.png) | [Creare un gruppo di sincronizzazione](storage-sync-files-deployment-guide.md#create-a-sync-group-and-a-cloud-endpoint) in un servizio di sincronizzazione archiviazione. Fare riferimento alla condivisione vuota come endpoint cloud. Ripetere questo passaggio per ogni condivisione file del Data Box. [Configurare sincronizzazione File di Azure](storage-sync-files-deployment-guide.md). |
| ![Passaggio 5](media/storage-sync-files-offline-data-transfer/bullet_5.png) | [Aggiungere la directory del server live come endpoint server](storage-sync-files-deployment-guide.md#create-a-server-endpoint). Nel processo, specificare che si è spostati i file di Azure e fare riferimento a condivisioni di gestione temporanea. È possibile abilitare o disabilitare il cloud a livelli in base alle esigenze. Durante la creazione di un endpoint server nel server in tempo reale, fare riferimento alla condivisione di gestione temporanea. Nel **Aggiungi endpoint server** pannello, in **trasferimento Offline dei dati**, selezionare **abilitato**e quindi selezionare la condivisione di staging che deve essere nello stesso account di archiviazione cloud endpoint. In questo caso, l'elenco delle condivisioni disponibili viene filtrato per account di archiviazione e condivisioni che non sono già la sincronizzazione. |

![Screenshot dell'interfaccia utente del portale di Azure, che illustra come abilitare il trasferimento dei dati offline durante la creazione di un nuovo endpoint server](media/storage-sync-files-offline-data-transfer/data-box-integration-2-600.png)

## <a name="syncing-the-share"></a>Sincronizzazione della condivisione
Dopo aver creato l'endpoint server, verrà avviata la sincronizzazione. Il processo di sincronizzazione determina se ogni file nel server esiste anche nella condivisione di gestione temporanea in cui Data Box depositate i file. Se non è presente il file, il processo di sincronizzazione copia il file dalla condivisione di gestione temporanea anziché caricandolo dal server. Se il file non esiste nella condivisione di gestione temporanea o una versione più recente è disponibile nel server locale, il processo di sincronizzazione carica il file dal server locale.

> [!IMPORTANT]
> È possibile abilitare la modalità di migrazione in blocco solo quando si crea un endpoint server. Dopo aver stabilito un endpoint server, non è possibile integrare la migrazione bulk di dati da un server già la sincronizzazione nello spazio dei nomi.

## <a name="acls-and-timestamps-on-files-and-folders"></a>ACL e timestamp di file e cartelle
Sincronizzazione File di Azure garantisce che gli ACL di file e cartelle vengono sincronizzati dal server in tempo reale anche se lo strumento di migrazione delle operazioni bulk che è stato usato non è stata inizialmente trasporto ACL. Per questo motivo, la condivisione di gestione temporanea non deve contenere gli ACL per i file e cartelle. Quando si abilita la funzionalità di migrazione dei dati offline durante la creazione di un nuovo endpoint server, tutti gli ACL di file vengono sincronizzati nel server. Vengono anche sincronizzati con timestamp appena creato e modificato.

## <a name="shape-of-the-namespace"></a>Forma dello spazio dei nomi
Quando si abilita la sincronizzazione, il contenuto del server determina la forma dello spazio dei nomi. Se i file vengono eliminati dal server locale al termine lo snapshot di Data Box e la migrazione, non spostare questi file nello spazio dei nomi in tempo reale, la sincronizzazione. Rimangono nella condivisione di gestione temporanea, ma questi non vengono copiati. Ciò è necessario perché la sincronizzazione mantiene lo spazio dei nomi in base al server in tempo reale. Data Box *snapshot* è semplicemente un terreno staging per la copia dei file efficiente. Non è l'autorità per la forma dello spazio dei nomi in tempo reale.

## <a name="cleaning-up-after-bulk-migration"></a>Pulizia dopo la migrazione di massa 
Poiché il server ha completato la sincronizzazione iniziale dello spazio dei nomi, i file migrati bulk Data Box usano la condivisione file di gestione temporanea. Nel **proprietà dell'Endpoint Server** pannello nel portale di Azure nel **trasferimento Offline dei dati** sezione, lo stato passa da **In corso** a **completato** . 

![Screenshot del Pannello proprietà dell'Endpoint Server, in cui si trovano i controlli di stato e disabilitare il trasferimento dei dati offline](media/storage-sync-files-offline-data-transfer/data-box-integration-3-444.png)

A questo punto è possibile pulire la condivisione di gestione temporanea per ridurre i costi:

1. Nel **proprietà dell'Endpoint Server** blade, quando lo stato è **Completed**, selezionare **disabilitare il trasferimento dei dati offline**.
2. Provare a eliminare la condivisione di gestione temporanea per ridurre i costi. La condivisione di gestione temporanea probabilmente non contiene gli ACL di file e cartelle, pertanto non è molto utile. Punto nel tempo a scopo di backup, creare un vero [snapshot di condivisione file di Azure la sincronizzazione](storage-snapshots-files.md). È possibile [configurare il Backup di Azure per acquisire snapshot]( ../../backup/backup-azure-files.md) in una pianificazione.

Disabilitare la modalità di trasferimento dei dati offline solo quando lo stato è **Completed** o quando si desidera annullare a causa di un errore di configurazione. Se si disabilita la modalità durante una distribuzione, i file inizieranno a caricare dal server, anche se la condivisione di gestione temporanea è ancora disponibile.

> [!IMPORTANT]
> Dopo aver disabilitato la modalità di trasferimento dei dati offline, è non è possibile abilitarlo nuovamente, anche se la condivisione di gestione temporanea dalla migrazione bulk è ancora disponibile.

## <a name="next-steps"></a>Passaggi successivi
- [Pianificare una distribuzione di Sincronizzazione file di Azure](storage-sync-files-planning.md)
- [Come distribuire Sincronizzazione file di Azure](storage-sync-files-deployment-guide.md)
