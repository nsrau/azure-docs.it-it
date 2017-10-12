---
title: Aggiornare Servizi multimediali dopo il rollover delle chiavi di accesso alle risorse di archiviazione | Microsoft Docs
description: "Questo articolo fornisce informazioni sulle modalità per aggiornare Servizi multimediali dopo aver eseguito il rollover delle chiavi di accesso alle risorse di archiviazione."
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: a892ebb0-0ea0-4fc8-b715-60347cc5c95b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/09/2017
ms.author: milanga;cenkdin;juliako
ms.openlocfilehash: 304e72e0d2d4a7e95df513e6d5481def9eae3f68
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="update-media-services-after-rolling-storage-access-keys"></a>Aggiornare Servizi multimediali dopo il rollover delle chiavi di accesso alle risorse di archiviazione

Quando si crea un nuovo account di Servizi multimediali di Azure (AMS), viene chiesto di selezionare anche un account di archiviazione di Azure da usare per l'archiviazione dei contenuti multimediali. È possibile aggiungere più di un account di archiviazione all'account di Servizi multimediali. In questo argomento viene illustrato come far ruotare le chiavi di archiviazione. Viene inoltre illustrato come aggiungere gli account di archiviazione a un account multimediale. 

Per eseguire le operazioni descritte in questo argomento, è necessario utilizzare le [API ARM](https://docs.microsoft.com/rest/api/media/mediaservice) e [Powershell](https://docs.microsoft.com/powershell/resourcemanager/azurerm.media/v0.3.2/azurerm.media).  Per ulteriori informazioni, vedere [Gestire le risorse di Azure con PowerShell e Resource Manager](../azure-resource-manager/powershell-azure-resource-manager.md).

## <a name="overview"></a>Panoramica

Quando viene creato un nuovo account di archiviazione, Azure genera due chiavi di accesso a 512 bit alle risorse di archiviazione, che consentono di autenticare l'accesso all'account di archiviazione. Per mantenere le connessioni di archiviazione più sicure, si consiglia di rigenerare e far ruotare periodicamente la chiave di accesso alle risorse di archiviazione. Per non perdere mai la connessione all'account di archiviazione, vengono fornite due chiavi di accesso (primaria e secondaria), in modo da poter usare la prima mentre si rigenera la seconda. Questa procedura viene anche denominata "rollover delle chiavi di accesso".

Servizi multimediali dipende da una chiave di archiviazione fornita. In particolare, i localizzatori che sono usati per trasmettere in streaming o scaricare gli asset dipendono dalla chiave di accesso alle risorse di archiviazione specificata. Quando viene creato un account AMS, esso assume una dipendenza dalla chiave di accesso alle risorse di archiviazione primaria per impostazione predefinita, ma l’utente può aggiornare la chiave di archiviazione di Servizi multimediali di Azure. È necessario comunicare a Servizi multimediali la chiave da usare, seguendo i passaggi descritti in questo argomento.  

>[!NOTE]
> Se si dispone di più account di archiviazione, è necessario eseguire questa procedura per ogni account di archiviazione. L'ordine in cui ruotare le chiavi di archiviazione non è prefissato. È possibile ruotare prima la chiave secondaria e quindi quella principale o viceversa.
>
> Prima di eseguire la procedura descritta in questo argomento su un account di produzione, effettuarne il test in un account di pre-produzione.
>

## <a name="steps-to-rotate-storage-keys"></a>Passaggi per ruotare le chiavi di archiviazione 
 
 1. Modificare la chiave primaria dell'account di archiviazione tramite il cmdlet PowerShell o il portale di [Azure](https://portal.azure.com/).
 2. Chiamare il cmdlet Sync-AzureRmMediaServiceStorageKeys con i parametri appropriati per forzare l'account multimediale a prendere le chiavi dell'account di archiviazione
 
    Nell'esempio seguente viene illustrato come sincronizzare le chiavi con gli account di archiviazione.
  
         Sync-AzureRmMediaServiceStorageKeys -ResourceGroupName $resourceGroupName -AccountName $mediaAccountName -StorageAccountId $storageAccountId
  
 3. Attendere circa un'ora. Verificare che gli scenari di streaming funzionino.
 4. Modificare la chiave secondaria dell'account di archiviazione tramite il cmdlet PowerShell o il portale di Azure.
 5. Chiamare il cmdlet PowerShell Sync-AzureRmMediaServiceStorageKeys con i parametri appropriati per forzare l'account multimediale a prendere le nuove chiavi dell'account di archiviazione. 
 6. Attendere circa un'ora. Verificare che gli scenari di streaming funzionino.
 
### <a name="a-powershell-cmdlet-example"></a>Esempio di cmdlet PowerShell 

Nell'esempio seguente viene illustrato come ottenere l'account di archiviazione e sincronizzarlo con l'account AMS.

    $regionName = "West US"
    $resourceGroupName = "SkyMedia-USWest-App"
    $mediaAccountName = "sky"
    $storageAccountName = "skystorage"
    $storageAccountId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Storage/storageAccounts/$storageAccountName"

    Sync-AzureRmMediaServiceStorageKeys -ResourceGroupName $resourceGroupName -AccountName $mediaAccountName -StorageAccountId $storageAccountId

 
## <a name="steps-to-add-storage-accounts-to-your-ams-account"></a>Passaggi per aggiungere gli account di archiviazione all'account AMS

L'argomento seguente illustra come aggiungere gli account di archiviazione all'account AMS: [Collegare più account di archiviazione a un account di Servizi multimediali](meda-services-managing-multiple-storage-accounts.md).

## <a name="media-services-learning-paths"></a>Percorsi di apprendimento di Servizi multimediali
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

### <a name="acknowledgments"></a>Ringraziamenti
Siamo lieti di conferire un riconoscimento alle seguenti persone che hanno contribuito alla realizzazione di questo documento: Cenk Dingiloglu, Gada Milano, Seva Titov.
