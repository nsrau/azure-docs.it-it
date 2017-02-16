---
title: " Creare un account Servizi multimediali di Azure con il portale di Azure | Documentazione Microsofts"
description: Questa esercitazione illustra la procedura di creazione di un account Servizi multimediali di Azure con il portale di Azure.
services: media-services
documentationcenter: 
author: Juliako
manager: erikre
editor: 
ms.assetid: c551e158-aad6-47b4-931e-b46260b3ee4c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/10/2017
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: f6d6b7b1051a22bbc865b237905f8df84e832231
ms.openlocfilehash: 01c1297a6b107f91d8e6e73f17d76499d67849de


---
# <a name="create-an-azure-media-services-account-using-the-azure-portal"></a>Creare un account Servizi multimediali di Azure con il portale di Azure
> [!div class="op_single_selector"]
> * [Portale](media-services-portal-create-account.md)
> * [PowerShell](media-services-manage-with-powershell.md)
> * [REST](https://docs.microsoft.com/rest/api/media/mediaservice)
> 
> [!NOTE]
> Per completare l'esercitazione, è necessario un account Azure. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/). 
> 
> 

Nel portale di Azure è possibile creare rapidamente un account di Servizi multimediali di Azure (AMS). È possibile utilizzare l'account per accedere ai servizi multimediali che consentono di archiviare, crittografare, codificare, gestire e distribuire in streaming contenuti multimediali in Azure. Durante la creazione di un account di Servizi multimediali, viene creato l'account di archiviazione associato (se non si desidera utilizzarne uno esistente) nella stessa area geografica dell'account di Servizi multimediali.

Questo articolo illustra alcuni concetti comuni e come creare un account Servizi multimediali con il portale di Azure.

## <a name="concepts"></a>Concetti
Per accedere a Servizi multimediali è necessario disporre di due account associati:

* Account di Servizi multimediali. L'account consente di accedere a un set di Servizi multimediali basati su cloud disponibili in Azure. In un account di Servizi multimediali non è possibile archiviare contenuti multimediali effettivi, ma è possibile archiviare i metadati relativi al contenuto multimediale e i processi di elaborazione dei file multimediali. Al momento della creazione dell'account, è necessario selezionare un'area di Servizi multimediali disponibile. L'area selezionata è un data center in cui sono archiviati i record dei metadati per l'account.
  
    Le aree di Servizi multimediali (AMS) disponibili includono: Europa settentrionale, Europa occidentale, Stati Uniti occidentali, Stati Uniti orientali, Asia sudorientale, Asia orientale, Giappone occidentale, Giappone orientale. In Servizi multimediali non vengono utilizzati gruppi di affinità.
  
    AMS è ora disponibile anche nei data center seguenti: Brasile meridionale, India occidentale, India meridionale e India centrale. È ora possibile usare il portale di Azure per creare account Servizi multimediali ed eseguire diverse attività descritte qui. In questi data center la codifica live non è tuttavia abilitata. Inoltre, non sono disponibili tutti i tipi di unità riservate di codifica.
  
  * Brasile meridionale: sono disponibili solo unità riservate di codifica Standard e Basic.
  * India occidentale, India meridionale: 
* Un account di archiviazione di Azure. Gli account di archiviazione devono trovarsi nella stessa area geografica dell'account Servizi multimediali. Quando si crea un account di Servizi multimediali, è possibile scegliere un account di archiviazione esistente nella stessa area geografica oppure è possibile crearne uno nuovo, ma sempre nella stessa area. Se si elimina un account di Servizi multimediali, gli oggetti BLOB presenti nell'account di archiviazione associato non vengono eliminati.

## <a name="create-an-ams-account"></a>Creare un account AMS
I passaggi descritti in questa sezione illustrano come creare un account Servizi multimediali di Azure.

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Fare clic su **+Nuovo** > **Web e dispositivi mobili** > **Servizi multimediali**.
   
    ![Creare Servizi multimediali](./media/media-services-create-account/media-services-new1.png)
3. In **CREARE UN ACCOUNT DEL SERVIZIO MULTIMEDIALE** immettere i valori richiesti.
   
    ![Creare Servizi multimediali](./media/media-services-create-account/media-services-new3.png)
   
   1. In **Nome account**immettere il nome del nuovo account di AMS. Un nome di account di Servizi multimediali deve essere composto da tutte lettere minuscole o da numeri senza spazi con una lunghezza compresa tra 3 e 24 caratteri.
   2. In Sottoscrizione selezionare una delle diverse sottoscrizioni di Azure per le quali è disponibile l'accesso.
   3. In **Gruppo di risorse**selezionare la risorsa nuova o esistente.  Un gruppo di risorse è una raccolta di risorse che condividono il ciclo di vita, le autorizzazioni e i criteri. Fare clic [qui](../azure-resource-manager/resource-group-overview.md#resource-groups) per altre informazioni.
   4. In **Località** selezionare l'area geografica che verrà usata per archiviare i contenuti multimediali e  i record di metadati per l'account Servizi multimediali. Questa area verrà usata per elaborare e riprodurre in streaming il contenuto multimediale. Nella casella dell'elenco a discesa vengono visualizzate solo le aree di Servizi multimediali disponibili. 
   5. In **Account di archiviazione**selezionare un account di archiviazione per l'archivio BLOB del contenuto multimediale dell'account Servizi multimediali. È possibile scegliere un account di archiviazione esistente nella stessa area geografica dell'account Servizi multimediali oppure è possibile crearne uno. Un nuovo account di archiviazione viene creato nella stessa area geografica. Per i nomi degli account di archiviazione vengono seguite le stesse regole dei nomi degli account di Servizi multimediali.
      
       Altre informazioni sull'archiviazione sono disponibili [qui](../storage/storage-introduction.md).
   6. Selezionare **Aggiungi al dashboard** per visualizzare lo stato della distribuzione di account.
4. Fare clic su **Crea** nella parte inferiore del form.
   
    Dopo che l'account è stato creato, viene caricata la pagina della panoramica. Nella tabella dell'endpoint di streaming l'account avrà un endpoint di streaming predefinito con stato **Arrestato**. 

    >[!NOTE]
    >Quando l'account AMS viene creato, un endpoint di streaming **predefinito** viene aggiunto all'account con stato **Arrestato**. Per avviare lo streaming del contenuto e sfruttare i vantaggi della creazione dinamica dei pacchetti e della crittografia dinamica, l'endpoint di streaming da cui si vuole trasmettere il contenuto deve essere nello stato **In esecuzione**. 
   
    ![Impostazioni di Servizi multimediali](./media/media-services-create-account/media-services-settings.png)
   
    Per gestire l'account AMS, ad esempio per caricare video, codificare asset, monitorare lo stato dei processi, usare la finestra **Impostazioni** .

## <a name="manage-keys"></a>Gestione delle chiavi
Per accedere a livello di codice all'account Servizi multimediali, sono necessarie le informazioni relative al nome dell'account e alla chiave primaria.

1. Nel portale di Azure selezionare l'account. 
   
    Su lato destro verrà visualizzata la finestra **Impostazioni** . 
2. Nella finestra **Impostazioni** selezionare **Chiavi**. 
   
    Nella finestra **Gestisci chiavi** sono visualizzati il nome dell'account e le chiavi primaria e secondaria. 
3. Per copiare i valori, scegliere il pulsante Copia.
   
    ![Chiavi di Servizi multimediali](./media/media-services-create-account/media-services-keys.png)

## <a name="next-steps"></a>Passaggi successivi
Ora è possibile caricare i file nell'account AMS. Per altre informazioni, vedere [Upload files](media-services-portal-upload-files.md)(Caricare file).

## <a name="media-services-learning-paths"></a>Percorsi di apprendimento di Servizi multimediali
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]




<!--HONumber=Jan17_HO2-->


