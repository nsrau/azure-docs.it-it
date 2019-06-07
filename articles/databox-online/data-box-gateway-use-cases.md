---
title: Casi d'uso di Gateway finestra di dati di Microsoft Azure | Microsoft Docs
description: Descrive i casi d'uso per il Gateway di finestra di dati Azure, una soluzione di archiviazione di appliance virtuale che consente di trasferire i dati in Azure
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 03/02/2019
ms.author: alkohli
ms.openlocfilehash: e9092fb91ad98e6147647717e11d1a64bcff580e
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/07/2019
ms.locfileid: "66754188"
---
# <a name="use-cases-for-azure-data-box-gateway"></a>Casi d'uso di Azure Data Box Gateway

Azure Data Box Gateway è un dispositivo gateway di archiviazione nel cloud che dall'ambiente locale invia immagini, elementi multimediali e altri dati ad Azure. Questo gateway di archiviazione nel cloud è una macchina virtuale con provisioning nell'hypervisor. L'utente scrive i dati in questo dispositivo virtuale usando i protocolli NFS e SMB e tali dati vengono quindi inviati dal dispositivo ad Azure. Questo articolo offre una descrizione dettagliata degli scenari che supportano la distribuzione di questo dispositivo.

Usare Data Box Gateway per gli scenari seguenti:

- Per inserire in modo continuativo grandi quantità di dati.
- Per archiviare dati nel cloud in modo sicuro ed efficiente.
- Per trasferire dati in rete in modo incrementale al termine del trasferimento in blocco iniziale tramite Data Box.

Ognuno di questi scenari viene descritto nei dettagli nelle sezioni seguenti.


## <a name="continuous-data-ingestion"></a>Inserimento dati continuo

Uno dei principali vantaggi di Data Box Gateway è la capacità di inserire continuamente nel dispositivo dati da copiare nel cloud, indipendentemente dalle dimensioni dei dati stessi.

Man mano che i dati vengono scritti nel dispositivo gateway, questo li carica in Archiviazione di Azure. Il dispositivo gestisce automaticamente lo spazio di archiviazione rimuovendo i file in locale e conservando i metadati quando raggiunge una determinata soglia. Mantenere una copia locale dei metadati consente al dispositivo gateway di caricare solo le modifiche quando il file viene aggiornato. I dati caricati nel dispositivo gateway devono seguire le linee guida indicate in [Avvertenze sul caricamento dei dati](data-box-gateway-limits.md#data-upload-caveats).

Man mano che il dispositivo si riempie di dati, inizia a limitare la velocità in ingresso quanto necessario per adeguarsi alla velocità con cui i dati vengono caricati nel cloud. Per monitorare l'inserimento dati continuo nel dispositivo si usano gli avvisi. Questi avvisi vengono generati all'inizio della limitazione e vengono cancellati al termine.

## <a name="cloud-archival-of-data"></a>Archiviazione dei dati nel cloud

Usare Data Box Gateway se si ha l'esigenza di conservare i dati nel cloud per un lungo periodo. Per la conservazione a lungo termine è possibile usare il livello di archiviazione **Archivio**,

che è ottimizzato per archiviare i dati a cui si accede di rado per almeno 180 giorni. Il livello **Archivio** offre i costi di archiviazione più bassi, ma anche i costi di accesso più alti. Per altre informazioni, vedere [Livello di accesso archivio](/azure/storage/blobs/storage-blob-storage-tiers#archive-access-tier).

### <a name="move-data-to-archive-tier"></a>Spostare i dati nel livello di archiviazione archivio

Prima di iniziare, verificare di avere un dispositivo Data Box Gateway funzionante. Seguire le procedure illustrate in [Esercitazione: Preparare la distribuzione di Azure Data Box Gateway](data-box-gateway-deploy-prep.md) e continuare con l'esercitazione successiva finché il dispositivo non è operativo.

- Usare il dispositivo Data Box Gateway per caricare i dati in Azure tramite la consueta procedura di trasferimento, come descritto in [Esercitazione: Trasferire i dati con Azure Data Box Gateway](data-box-gateway-deploy-add-shares.md).
- Dopo aver caricato i dati, sarà necessario spostarli nel livello Archivio. È possibile impostare il livello BLOB in due modi: con uno script di Azure PowerShell o con i criteri di gestione del ciclo di vita di Archiviazione di Azure.  
    - Se si usa Azure PowerShell, seguire questi [passaggi](/azure/databox/data-box-how-to-set-data-tier#use-azure-powershell-to-set-the-blob-tier) per spostare i dati nel livello Archivio.
    - Se si usano i criteri di gestione del ciclo di vita di Azure, seguire questi passaggi per spostare i dati nel livello Archivio.
        - [Eseguire la registrazione](/azure/storage/common/storage-lifecycle-management-concepts) per l'anteprima del servizio di gestione del ciclo di vita a livello di BLOB per poter usare il livello Archivio.
        - Usare i criteri seguenti per [archiviare i dati al momento dell'inserimento](/azure/storage/blobs/storage-lifecycle-management-concepts#archive-data-at-ingest).
- Una volta contrassegnati come Archivio, i BLOB non possono più essere modificati dal gateway a meno che non vengano spostati nel livello di archiviazione ad accesso frequente o ad accesso sporadico. Se il file si trova nell'archivio locale, le eventuali modifiche apportate alla copia locale (incluse le eliminazioni) non vengono caricate nel livello di archiviazione archivio.
- Per poter essere letti nel livello di archiviazione archivio, i dati devono essere riattivati impostando il livello BLOB sul livello ad accesso frequente o sporadico. L'[aggiornamento della condivisione](data-box-gateway-manage-shares.md#refresh-shares) sul gateway non riattiva il BLOB.

Per altre informazioni, vedere [Gestione del ciclo di vita di Archiviazione BLOB di Azure](/azure/storage/common/storage-lifecycle-management-concepts).

## <a name="initial-bulk-transfer-followed-by-incremental-transfer"></a>Trasferimento in blocco iniziale seguito da un trasferimento incrementale

Usare Data Box e Data Box Gateway insieme quando si ha l'esigenza di eseguire un caricamento in blocco di una grande quantità di dati, seguito da trasferimenti incrementali. Usare Data Box per il trasferimento in blocco in modalità offline (seeding iniziale) e Data Box Gateway per i trasferimenti incrementali (feed continuativo) in rete.

### <a name="seed-the-data-with-data-box"></a>Effettuare il seeding dei dati con Data Box

Seguire questa procedura per copiare i dati in Data Box e caricarli in Archiviazione di Azure.

1. [Ordinare il Data Box](/azure/databox/data-box-deploy-ordered).
2. [Configurare il Data Box](/azure/databox/data-box-deploy-set-up).
3. [Copiare i dati nel Data Box tramite SMB](/azure/databox/data-box-deploy-copy-data).
4. [Restituire il Data Box, verificare il caricamento dei dati in Azure](/azure/databox/data-box-deploy-picked-up).
5. Una volta completato il caricamento dei dati in Azure, tutti i dati dovrebbero trovarsi in contenitori di archiviazione di Azure. Nell'account di archiviazione di Data Box passare al contenitore di BLOB (e di file) per verificare che tutti i dati siano stati copiati. Prendere nota del nome del contenitore, in quanto sarà necessario usarlo in seguito. Nello screenshot seguente, ad esempio, il contenitore `databox` verrà usato per il trasferimento incrementale.

    ![Contenitore con dati in Data Box](media/data-box-gateway-use-cases/data-container1.png)

Questo trasferimento in blocco completa la fase di seeding iniziale.

### <a name="ongoing-feed-with-data-box-gateway"></a>Feed continuativo con Data Box Gateway

Seguire questa procedura per l'inserimento dati continuo tramite Data Box Gateway.

1. Creare una condivisione cloud in Data Box Gateway. Questa condivisione carica automaticamente tutti i dati nell'account di Archiviazione di Azure. Nella risorsa Data Box Gateway passare a **Condivisioni** e fare clic su **+ Aggiungi condivisione**.

    ![Fare clic su +Aggiungi condivisione](media/data-box-gateway-use-cases/add-share1.png)

2. Verificare che la condivisione sia mappata al contenitore in cui si trovano i dati di cui è stato eseguito il seeding. Per **Seleziona contenitore BLOB** scegliere **Usa esistente** e passare al contenitore in cui sono stati trasferiti i dati da Data Box.

    ![Impostazioni della condivisione](media/data-box-gateway-use-cases/share-settings-select-existing-container1.png)

3. Dopo aver creato la condivisione, aggiornarla. Questa operazione aggiorna la condivisione locale con il contenuto di Azure.

    ![Aggiornare la condivisione](media/data-box-gateway-use-cases/refresh-share1.png)

    Una volta sincronizzata la condivisione, Data Box Gateway caricherà le modifiche incrementali nel caso in cui i file siano stati modificati sul client.

## <a name="next-steps"></a>Passaggi successivi

- Vedere i [requisiti di sistema di Data Box Gateway](data-box-gateway-system-requirements.md).
- Comprendere i [limiti di Data Box Gateway](data-box-gateway-limits.md).
- Distribuire [Azure Data Box Gateway](data-box-gateway-deploy-prep.md) nel portale di Azure.