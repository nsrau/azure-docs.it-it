---
title: Eseguire la migrazione dei dati in Sincronizzazione file di Azure tramite Azure Data Box e altri metodi
description: Eseguire la migrazione dei dati in blocco in modo che sia compatibile con Sincronizzazione file di Azure.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 9264aa6d24256b991abefe35b41045caa2e76d67
ms.sourcegitcommit: 4b8a69b920ade815d095236c16175124a6a34996
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/23/2019
ms.locfileid: "69997774"
---
# <a name="migrate-bulk-data-to-azure-file-sync"></a>Eseguire la migrazione dei dati in blocco al Sincronizzazione file di Azure
È possibile eseguire la migrazione dei dati in blocco a Sincronizzazione file di Azure in due modi:

* **Caricare i file usando Sincronizzazione file di Azure.** Questo è il metodo più semplice. Spostare i file in locale in Windows Server 2012 R2 o versione successiva e installare l'agente di Sincronizzazione file di Azure. Dopo aver impostato la sincronizzazione, i file verranno caricati dal server. I clienti hanno attualmente una velocità di caricamento media di 1 TiB ogni due giorni. Per assicurarsi che il server non usi troppa larghezza di banda per il Data Center, potrebbe essere necessario configurare una pianificazione della [limitazione della larghezza di banda](storage-sync-files-server-registration.md#ensuring-azure-file-sync-is-a-good-neighbor-in-your-datacenter).
* **Trasferire i file offline.** Se la larghezza di banda non è sufficiente, potrebbe non essere possibile caricare i file in Azure in un periodo di tempo ragionevole. La richiesta è la sincronizzazione iniziale dell'intero set di file. Per ovviare a questo problema, utilizzare strumenti di migrazione in blocco offline, ad esempio la [famiglia di Azure Data Box](https://azure.microsoft.com/services/storage/databox). 

Questo articolo illustra come eseguire la migrazione di file offline in modo compatibile con Sincronizzazione file di Azure. Seguire queste istruzioni per evitare conflitti di file e per mantenere gli elenchi di controllo di accesso (ACL) e i timestamp dei file e delle cartelle dopo aver abilitato la sincronizzazione.

## <a name="migration-tools"></a>Strumenti di migrazione
Il processo descritto in questo articolo funziona non solo per Data Box ma anche per altri strumenti di migrazione offline. Funziona anche per strumenti quali AzCopy, Robocopy o strumenti e servizi partner che funzionano direttamente su Internet. Tuttavia, per superare la richiesta di caricamento iniziale, attenersi alla procedura descritta in questo articolo per usare questi strumenti in modo compatibile con Sincronizzazione file di Azure.

In alcuni casi è necessario spostarsi da un server Windows a un altro Windows Server prima di adottare Sincronizzazione file di Azure. [Servizio migrazione archiviazione](https://aka.ms/storagemigrationservice) (SMS) può risultare utile. Se è necessario eseguire la migrazione a una versione del sistema operativo del server supportata da Sincronizzazione file di Azure (Windows Server 2012R2) o semplicemente è necessario eseguire la migrazione perché si sta acquistando un nuovo sistema per Sincronizzazione file di Azure, SMS presenta numerose funzionalità e vantaggi che consentiranno di ottenere il MIGR zione eseguita senza problemi.

## <a name="benefits-of-using-a-tool-to-transfer-data-offline"></a>Vantaggi dell'utilizzo di uno strumento per trasferire i dati offline
Ecco i principali vantaggi dell'uso di uno strumento di trasferimento come Data Box per la migrazione offline:

- Non è necessario caricare tutti i file in rete. Per gli spazi dei nomi di grandi dimensioni, questo strumento può risparmiare tempo e larghezza di banda di rete significative.
- Quando si usa Sincronizzazione file di Azure, indipendentemente dallo strumento di trasferimento usato (Data Box, servizio importazione/esportazione di Azure e così via), il server Live carica solo i file che cambiano dopo lo spostamento dei dati in Azure.
- Sincronizzazione file di Azure Sincronizza gli ACL di file e cartelle anche se lo strumento di migrazione in blocco non in linea non trasporta gli ACL.
- Data Box e Sincronizzazione file di Azure non richiedono tempi di inattività. Quando si usa Data Box per trasferire i dati in Azure, si usa la larghezza di banda di rete in modo efficiente e si mantiene la fedeltà dei file. Lo spazio dei nomi è sempre aggiornato caricando solo i file che cambiano dopo lo spostamento dei dati in Azure.

## <a name="prerequisites-for-the-offline-data-transfer"></a>Prerequisiti per il trasferimento di dati offline
Non abilitare la sincronizzazione nel server di cui si sta eseguendo la migrazione prima di completare il trasferimento dei dati offline. Gli altri aspetti da considerare prima di iniziare sono i seguenti:

- Se si prevede di usare Data Box per la migrazione in blocco: Esaminare i [prerequisiti della distribuzione per Data Box](../../databox/data-box-deploy-ordered.md#prerequisites).
- Pianificare la topologia di Sincronizzazione file di Azure finale: [Pianificare una distribuzione di Sincronizzazione file di Azure](storage-sync-files-planning.md)
- Selezionare gli account di archiviazione di Azure che conterranno le condivisioni file con cui si vuole eseguire la sincronizzazione. Assicurarsi che la migrazione in blocco venga eseguita in condivisioni di staging temporanee negli stessi account di archiviazione. La migrazione in blocco può essere abilitata solo usando una condivisione finale e una condivisione di staging che si trovano nello stesso account di archiviazione.
- Una migrazione in blocco può essere usata solo quando si crea una nuova relazione di sincronizzazione con un percorso server. Non è possibile abilitare una migrazione in blocco con una relazione di sincronizzazione esistente.


## <a name="process-for-offline-data-transfer"></a>Processo per il trasferimento di dati offline
Di seguito viene illustrato come configurare Sincronizzazione file di Azure in modo che sia compatibile con gli strumenti di migrazione in blocco, ad esempio Azure Data Box:

![Diagramma che illustra come configurare Sincronizzazione file di Azure](media/storage-sync-files-offline-data-transfer/data-box-integration-1-600.png)

| Passaggio | Dettagli |
|---|---------------------------------------------------------------------------------------|
| ![Passaggio 1](media/storage-sync-files-offline-data-transfer/bullet_1.png) | [Ordinare il Data Box](../../databox/data-box-deploy-ordered.md). La famiglia di Data Box offre [diversi prodotti](https://azure.microsoft.com/services/storage/databox/data) che soddisfino le proprie esigenze. Quando si riceve la data box, seguire la [documentazione per copiare i dati](../../databox/data-box-deploy-copy-data.md#copy-data-to-data-box) in questo percorso UNC nel data box:  *\\< DeviceIPAddres StorageAccountName_AzFile\> \>\<\< \>ShareName*. Qui *ShareName* è il nome della condivisione di staging. Inviare il Data Box ad Azure. |
| ![Passaggio 2](media/storage-sync-files-offline-data-transfer/bullet_2.png) | Attendere che i file vengano visualizzati nelle condivisioni file di Azure selezionate come condivisioni di staging temporanee. *Non abilitare la sincronizzazione per queste condivisioni.* |
| ![Passaggio 3](media/storage-sync-files-offline-data-transfer/bullet_3.png) | Creare una nuova condivisione vuota per ogni condivisione file che Data Box creata automaticamente. Questa nuova condivisione deve trovarsi nello stesso account di archiviazione della condivisione Data Box. [Come creare una nuova condivisione file di Azure](storage-how-to-create-file-share.md). |
| ![Passaggio 4](media/storage-sync-files-offline-data-transfer/bullet_4.png) | [Creare un gruppo di sincronizzazione](storage-sync-files-deployment-guide.md#create-a-sync-group-and-a-cloud-endpoint) in un servizio di sincronizzazione archiviazione. Fare riferimento alla condivisione vuota come endpoint cloud. Ripetere questo passaggio per ogni condivisione file del Data Box. [Configurare sincronizzazione file di Azure](storage-sync-files-deployment-guide.md). |
| ![Passaggio 5](media/storage-sync-files-offline-data-transfer/bullet_5.png) | [Aggiungere la directory del server live come endpoint server](storage-sync-files-deployment-guide.md#create-a-server-endpoint). Nel processo specificare che i file sono stati spostati in Azure e fare riferimento alle condivisioni di staging. È possibile abilitare o disabilitare la suddivisione in livelli nel cloud in base alle esigenze. Quando si crea un endpoint server sul server Live, fare riferimento alla condivisione di staging. Nel pannello **Aggiungi endpoint server** , in **trasferimento dati offline**Selezionare **abilitato**e quindi selezionare la condivisione di gestione temporanea che deve trovarsi nello stesso account di archiviazione dell'endpoint cloud. In questo caso, l'elenco delle condivisioni disponibili viene filtrato in base all'account di archiviazione e alle condivisioni che non sono già sincronizzate. |

![Screenshot dell'interfaccia utente di portale di Azure, che Mostra come abilitare il trasferimento di dati offline durante la creazione di un nuovo endpoint server](media/storage-sync-files-offline-data-transfer/data-box-integration-2-600.png)

## <a name="syncing-the-share"></a>Sincronizzazione della condivisione
Dopo aver creato l'endpoint server, la sincronizzazione viene avviata. Il processo di sincronizzazione determina se ogni file nel server esiste anche nella condivisione di staging in cui Data Box depositato i file. Se il file esiste già, il processo di sincronizzazione copia il file dalla condivisione di staging anziché caricarlo dal server. Se il file non esiste nella condivisione di staging o se nel server locale è disponibile una versione più recente, il processo di sincronizzazione carica il file dal server locale.

> [!IMPORTANT]
> È possibile abilitare la modalità di migrazione in blocco solo quando si crea un endpoint server. Dopo aver stabilito un endpoint server, non è possibile integrare i dati migrati in blocco da un server già sincronizzato nello spazio dei nomi.

## <a name="acls-and-timestamps-on-files-and-folders"></a>ACL e timestamp di file e cartelle
Sincronizzazione file di Azure garantisce che gli ACL di file e cartelle vengano sincronizzati dal server Live anche se lo strumento di migrazione in blocco utilizzato non ha inizialmente utilizzato il trasporto degli ACL. Per questo motivo, non è necessario che nella condivisione di staging siano contenuti ACL per file e cartelle. Quando si Abilita la funzionalità di migrazione dei dati offline quando si crea un nuovo endpoint server, tutti gli ACL dei file vengono sincronizzati nel server. Vengono sincronizzati anche i timestamp appena creati e modificati.

## <a name="shape-of-the-namespace"></a>Forma dello spazio dei nomi
Quando si Abilita la sincronizzazione, il contenuto del server determina la forma dello spazio dei nomi. Se i file vengono eliminati dal server locale dopo il completamento dello snapshot Data Box e della migrazione, questi file non vengono spostati nello spazio dei nomi Live, syncing. Rimangono nella condivisione di staging, ma non vengono copiati. Questa operazione è necessaria perché la sincronizzazione mantiene lo spazio dei nomi in base al server Live. Lo *snapshot* del data box è semplicemente un terreno di staging per una copia di file efficiente. Non è l'autorità per la forma dello spazio dei nomi Live.

## <a name="cleaning-up-after-bulk-migration"></a>Pulizia dopo la migrazione in blocco 
Quando il server completa la sincronizzazione iniziale dello spazio dei nomi, i Data Box file di cui è stata eseguita la migrazione bulk utilizzano la condivisione file di gestione temporanea. Nel pannello delle **proprietà dell'endpoint server** in portale di Azure, nella sezione **trasferimento dati offline** lo stato passa da **in corso** a **completato**. 

![Screenshot del pannello delle proprietà dell'endpoint server, in cui si trovano i controlli stato e Disabilita per il trasferimento di dati offline](media/storage-sync-files-offline-data-transfer/data-box-integration-3-444.png)

A questo punto è possibile eliminare la condivisione di staging per ridurre i costi:

1. Quando lo stato è **completato**, nel pannello **proprietà endpoint server** selezionare **Disabilita trasferimento dati offline**.
2. Provare a eliminare la condivisione di staging per ridurre i costi. La condivisione di staging probabilmente non contiene ACL di file e cartelle, quindi non è molto utile. Per gli scopi temporizzati del backup, creare uno snapshot reale [della condivisione file di Azure di sincronizzazione](storage-snapshots-files.md). È possibile [configurare backup di Azure per eseguire snapshot]( ../../backup/backup-azure-files.md) in base a una pianificazione.

Disabilitare la modalità di trasferimento dati offline solo quando lo stato è **completato** o quando si desidera annullare a causa di una configurazione errata. Se si disabilita la modalità durante una distribuzione, i file inizieranno a essere caricati dal server anche se la condivisione di gestione temporanea è ancora disponibile.

> [!IMPORTANT]
> Dopo aver disattivato la modalità di trasferimento dati offline, non è possibile abilitarla di nuovo, anche se la condivisione di staging dalla migrazione in blocco è ancora disponibile.

## <a name="next-steps"></a>Passaggi successivi
- [Pianificare una distribuzione di Sincronizzazione file di Azure](storage-sync-files-planning.md)
- [Come distribuire Sincronizzazione file di Azure](storage-sync-files-deployment-guide.md)
