---
title: Inviare dati a un livello di archiviazione ad accesso frequente, a freddo e a freddo tramite Azure Data Box/Azure Data Box Heavy
description: Viene descritto come utilizzare Azure Data Box o Azure Data Box Heavy per inviare dati a un livello di archiviazione BLOB in blocchi appropriato, ad esempio accesso frequente, accesso sporadico o archivio
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: how-to
ms.date: 05/24/2019
ms.author: alkohli
ms.openlocfilehash: 80c4d8a70454c007ac45f588e59c03ef45f10933
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92125065"
---
# <a name="use-azure-data-box-or-azure-data-box-heavy-to-send-data-to-appropriate-azure-storage-blob-tier"></a>Usare Azure Data Box o Azure Data Box Heavy per inviare dati al livello BLOB di archiviazione di Azure appropriato

Azure Data Box sposta grandi quantità di dati in Azure spedendo un dispositivo di archiviazione proprietario. Si riempie il dispositivo con i dati e lo si restituisce. I dati vengono caricati da Data Box a un livello predefinito associato all'account di archiviazione. È quindi possibile spostare i dati in un altro livello di archiviazione.

Questo articolo descrive come i dati che vengono caricati da Data Box possono essere spostati in un livello BLOB ad accesso frequente, ad accesso sporadico o archivio. Questo articolo si applica sia a Azure Data Box sia a Azure Data Box Heavy.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="choose-the-correct-storage-tier-for-your-data"></a>Scegliere il livello di archiviazione corretto per i dati

Archiviazione di Azure consente tre livelli diversi per archiviare i dati nel modo economicamente più conveniente: ad accesso frequente, sporadico o archivio. Il livello di archiviazione ad accesso frequente è ottimizzato per l'archiviazione di dati a cui si accede di frequente. L'archiviazione ad accesso frequente ha costi di archiviazione più alti rispetto a quella ad accesso sporadico e al livello archivio, ma costi di accesso più bassi.

Il livello di archiviazione ad accesso sporadico è per i dati a cui si accede raramente e che devono essere archiviati per almeno 30 giorni. Il costo di archiviazione per il livello ad accesso sporadico è più basso di quello del livello di archiviazione ad accesso frequente, ma gli addebiti per l'accesso ai dati sono elevati rispetto al livello ad accesso frequente.

Il livello archivio di Azure è offline e offre i costi di archiviazione più bassi, ma anche i costi di accesso più alti. Questo livello è destinato a dati che rimangono in archivio per almeno 180 giorni. Per informazioni dettagliate su ogni livello e sui modelli di prezzi, vedere [Confronto tra i livelli di archiviazione](../storage/blobs/storage-blob-storage-tiers.md).

I dati del Data Box o Data Box Heavy vengono caricati in un livello di archiviazione associato all'account di archiviazione. Quando si crea un account di archiviazione, è possibile specificare il livello di accesso come accesso frequente o accesso sporadico. In base al modello di accesso del carico di lavoro e dei costi, è possibile spostare questi dati dal livello predefinito a un altro livello di archiviazione.

È possibile suddividere in livelli i dati dell'archivio oggetti solo negli account di archiviazione BLOB o per utilizzo generico v2. Gli account per utilizzo generico v1 non supportano la suddivisione in livelli. Per scegliere il livello di archiviazione corretto per i dati, vedere le considerazioni dettagliate in [archiviazione BLOB di Azure: livelli di archiviazione Premium, ad](../storage/blobs/storage-blob-storage-tiers.md)accesso frequente, ad accesso sporadico e archivio.

## <a name="set-a-default-blob-tier"></a>Impostare un livello BLOB predefinito

Il livello BLOB predefinito viene specificato quando viene creato l'account di archiviazione nel portale di Azure. Dopo aver selezionato il tipo di archiviazione per utilizzo generico v2 o BLOB, è possibile specificare l'attributo Livello di accesso. Per impostazione predefinita, è selezionato il livello Frequente.

Non è possibile specificare i livelli se si sta provando a creare un nuovo account durante l'ordinamento di un Data Box o Data Box Heavy. Dopo aver creato l'account, è possibile modificarlo nel portale per impostare il livello di accesso predefinito.

In alternativa, è possibile creare un account di archiviazione prima con l'attributo del livello di accesso specificato. Quando si crea l'ordine di Data Box o Data Box Heavy, selezionare l'account di archiviazione esistente. Per altre informazioni su come impostare il livello BLOB predefinito durante la creazione dell'account di archiviazione, vedere [Creare un account di archiviazione nel portale di Azure](../storage/common/storage-account-create.md?tabs=portal).

## <a name="move-data-to-a-non-default-tier"></a>Spostare i dati in un livello non predefinito

Una volta caricati i dati dal dispositivo Data Box al livello predefinito, potrebbe essere necessario spostare i dati in un livello non predefinito. Esistono due modi per spostare i dati in un livello non predefinito.

- **Gestione del ciclo di vita di archiviazione BLOB di Azure**: è possibile usare un approccio basato sui criteri per suddividere automaticamente i dati tra livelli o definirne la scadenza alla fine del ciclo di vita. Per altre informazioni, vedere [Gestione del ciclo di vita di archiviazione BLOB di Azure](../storage/blobs/storage-lifecycle-management-concepts.md).
- **Scripting**: è possibile usare un approccio con script tramite Azure PowerShell per abilitare l'organizzazione a livello di BLOB. È possibile chiamare l'operazione `SetBlobTier` per impostare il livello nel BLOB.

## <a name="use-azure-powershell-to-set-the-blob-tier"></a>Usare Azure PowerShell per impostare il livello BLOB

I passaggi seguenti descrivono come è possibile impostare il livello BLOB su Archivio usando uno script di Azure PowerShell.

1. Aprire una sessione di Windows PowerShell con privilegi elevati. Assicurarsi di eseguire PowerShell 5.0 o versione successiva. Digitare:

   `$PSVersionTable.PSVersion`     

2. Accedere ad Azure PowerShell. 

   `Login-AzAccount`  

3. Definire le variabili per account di archiviazione, chiave di accesso, contenitore e contesto di archiviazione.

    ```powershell
    $StorageAccountName = "<enter account name>"
    $StorageAccountKey = "<enter account key>"
    $ContainerName = "<enter container name>"
    $ctx = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey
    ```

4. Ottenere tutti i BLOB nel contenitore.

    `$blobs = Get-AzStorageBlob -Container "<enter container name>" -Context $ctx`
 
5. Impostare il livello di tutti i BLOB nel contenitore su Archivio.

    ```powershell
    Foreach ($blob in $blobs) {
    $blob.ICloudBlob.SetStandardBlobTier("Archive")
    }
    ```

    Di seguito è riportato un output di esempio:

    ```
    Windows PowerShell
    Copyright (C) Microsoft Corporation. All rights reserved.
    PS C:\WINDOWS\system32> $PSVersionTable.PSVersion

    Major  Minor  Build  Revision
    -----  -----  -----  --------
    5      1      17763  134
    PS C:\WINDOWS\system32> Login-AzAccount

    Account          : gus@contoso.com
    SubscriptionName : MySubscription
    SubscriptionId   : subscription-id
    TenantId         : tenant-id
    Environment      : AzureCloud

    PS C:\WINDOWS\system32> $StorageAccountName = "mygpv2storacct"
    PS C:\WINDOWS\system32> $StorageAccountKey = "mystorageacctkey"
    PS C:\WINDOWS\system32> $ContainerName = "test"
    PS C:\WINDOWS\system32> $ctx = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey
    PS C:\WINDOWS\system32> $blobs = Get-AzStorageBlob -Container "test" -Context $ctx
    PS C:\WINDOWS\system32> Foreach ($blob in $blobs) {
    >> $blob.ICloudBlob.SetStandardBlobTier("Archive")
    >> }
    PS C:\WINDOWS\system32>
    ```
   > [!TIP]
   > Per fare in modo che i dati vengano archiviati al momento dell'inserimento, impostare il livello di account predefinito su Hot. Se il livello predefinito è Cool, è prevista una penalità per l'eliminazione anticipata di 30 giorni se i dati passano immediatamente ad Archive.

## <a name="next-steps"></a>Passaggi successivi

-  Informazioni su come gestire gli [scenari comuni di suddivisione in livelli dei dati con le regole dei criteri del ciclo di vita](../storage/blobs/storage-lifecycle-management-concepts.md#examples)