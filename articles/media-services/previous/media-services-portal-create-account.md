---
title: Creare un account Servizi multimediali di Azure con il portale di Azure | Documentazione di Microsoft
description: Questa esercitazione illustra la procedura di creazione di un account Servizi multimediali di Azure con il portale di Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: c551e158-aad6-47b4-931e-b46260b3ee4c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: juliako
ms.openlocfilehash: ddc1c7f2dd207cba18a8c080c8b14cc53c149a39
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2019
ms.locfileid: "58804176"
---
# <a name="create-a-media-services-account-using-the-azure-portal"></a>Creare un account di Servizi multimediali usando il portale di Azure

> [!NOTE]
> Non saranno aggiunte nuove caratteristiche o funzionalità a Servizi multimediali v2. <br/>Esplorare l'ultima versione, [Servizi multimediali v3](https://docs.microsoft.com/azure/media-services/latest/). Vedere anche [materiale sussidiario di migrazione dalla v2 alla v3](../latest/migrate-from-v2-to-v3.md)

Nel portale di Azure è possibile creare rapidamente un account di Servizi multimediali di Azure (AMS). È possibile utilizzare l'account per accedere ai servizi multimediali che consentono di archiviare, crittografare, codificare, gestire e distribuire in streaming contenuti multimediali in Azure. Durante la creazione di un account di Servizi multimediali, viene creato anche l'account di archiviazione associato (se non si desidera usarne uno esistente). Se si elimina un account di Servizi multimediali, gli oggetti BLOB presenti nell'account di archiviazione associato non vengono eliminati.

L'account di archiviazione primario può essere per utilizzo generico versione 1 o versione 2. Attualmente, il portale di Azure consente di selezionare solo v1 ma è possibile aggiungere v2 durante la creazione dell'account usando l'API o Powershell. Per altre informazioni sui tipi di archiviazione, vedere [Informazioni sugli account di archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account).

L'account di Servizi multimediali e tutti gli account di archiviazione associati devono far parte della stessa sottoscrizione di Azure. È consigliabile usare account di archiviazione nella stessa posizione dell'account di Servizi multimediali per evitare latenza e costi di dati in uscita aggiuntivi.

Questo articolo illustra come creare un account di Servizi multimediali usando il portale di Azure.

> [!NOTE]
> Per informazioni sulla disponibilità delle funzionalità di Servizi multimediali di Azure nelle diverse aree, vedere la [disponibilità delle funzionalità di AMS nei data center](scenarios-and-availability.md#availability).

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione, è necessario un account Azure. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/). 

## <a name="create-an-ams-account"></a>Creare un account AMS

I passaggi descritti in questa sezione illustrano come creare un account Servizi multimediali di Azure.

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Fare clic su **+Nuovo** > **Web e dispositivi mobili** > **Servizi multimediali**.
   
    ![Creare Servizi multimediali](./media/media-services-create-account/media-services-new1.png)
3. In **CREARE UN ACCOUNT DEL SERVIZIO MULTIMEDIALE** immettere i valori richiesti.
   
    ![Creare Servizi multimediali](./media/media-services-create-account/media-services-new3.png)
   
   1. In **Nome account**immettere il nome del nuovo account di AMS. Un nome di account di Servizi multimediali deve essere composto solo da lettere minuscole o da numeri senza spazi con una lunghezza compresa tra 3 e 24 caratteri.
   2. In Sottoscrizione selezionare una delle diverse sottoscrizioni di Azure per le quali è disponibile l'accesso.
   3. In **Gruppo di risorse**selezionare la risorsa nuova o esistente.  Un gruppo di risorse è una raccolta di risorse che condividono il ciclo di vita, le autorizzazioni e i criteri. Fare clic [qui](../../azure-resource-manager/resource-group-overview.md#resource-groups) per altre informazioni.
   4. In **Località** selezionare l'area geografica che verrà usata per archiviare i contenuti multimediali e  i record di metadati per l'account Servizi multimediali. Questa area verrà usata per elaborare e riprodurre in streaming il contenuto multimediale. Nella casella dell'elenco a discesa vengono visualizzate solo le aree di Servizi multimediali disponibili. 
   5. In **Account di archiviazione** selezionare un account di archiviazione per l'archivio BLOB del contenuto multimediale dell'account Servizi multimediali. È possibile scegliere un account di archiviazione esistente nella stessa area geografica dell'account Servizi multimediali oppure è possibile crearne uno. Un nuovo account di archiviazione viene creato nella stessa area geografica. Per i nomi degli account di archiviazione vengono seguite le stesse regole dei nomi degli account di Servizi multimediali.
      
       Altre informazioni sull'archiviazione sono disponibili [qui](../../storage/common/storage-introduction.md).
   6. Selezionare **Aggiungi al dashboard** per visualizzare lo stato della distribuzione di account.
4. Fare clic su **Crea** nella parte inferiore del form.
   
    Dopo che l'account è stato creato, viene caricata la pagina della panoramica. Nella tabella dell'endpoint di streaming l'account avrà un endpoint di streaming predefinito con stato **Arrestato**. 

    >[!NOTE]
    >Quando l'account AMS viene creato, un endpoint di streaming **predefinito** viene aggiunto all'account con stato **Arrestato**. Per avviare lo streaming del contenuto e sfruttare i vantaggi della creazione dinamica dei pacchetti e della crittografia dinamica, l'endpoint di streaming da cui si vuole trasmettere il contenuto deve essere nello stato **In esecuzione**. 
   
## <a name="to-manage-your-ams-account"></a>Per gestire l'account AMS

Selezionare **Impostazioni** a sinistra del portale per gestire l'account AMS, ad esempio connettersi all'API AMS a livello di codice, caricare video, codificare asset, configurare la protezione del contenuto e monitorare lo stato del processo. Da **Impostazioni** passare a uno dei pannelli disponibili, ad esempio: **Accesso all'API**, **Asset**, **Processi** e **Protezione del contenuto**.


## <a name="next-steps"></a>Passaggi successivi

Ora è possibile caricare i file nell'account AMS. Per altre informazioni, vedere [Upload files](media-services-portal-upload-files.md)(Caricare file).

Se si prevede di accedere all'API AMS a livello di codice, vedere [Accedere all'API di Servizi multimediali di Azure con l'autenticazione di Azure AD](media-services-use-aad-auth-to-access-ams-api.md).

## <a name="media-services-learning-paths"></a>Percorsi di apprendimento di Servizi multimediali
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

