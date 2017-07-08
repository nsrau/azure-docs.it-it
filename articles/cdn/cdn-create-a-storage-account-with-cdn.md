---
title: Integrare un account di archiviazione di Azure con la rete CDN di Azure | Documentazione Microsoft
description: Informazioni su come usare la rete per la distribuzione di contenuti (rete CDN) di Azure per distribuire contenuto con esigenze di larghezza di banda elevata, tramite la memorizzazione nella cache di BLOB da Archiviazione di Azure.
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: cbc2ff98-916d-4339-8959-622823c5b772
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.translationtype: Human Translation
ms.sourcegitcommit: db18dd24a1d10a836d07c3ab1925a8e59371051f
ms.openlocfilehash: 35896cc9891e2439df004cc4eab339219d47b805
ms.contentlocale: it-it
ms.lasthandoff: 06/15/2017


---
# <a name="integrate-an-azure-storage-account-with-azure-cdn"></a>Integrare un account di archiviazione di Azure con la rete CDN di Azure
È possibile abilitare la rete CDN per memorizzare nella cache i contenuti delle risorse di archiviazione di Azure. Questa rete offre agli sviluppatori una soluzione globale per il recapito di contenuto con esigenze di larghezza di banda elevata, tramite la memorizzazione nella cache di oggetti BLOB e contenuto statico di istanze di calcolo in nodi fisici negli Stati Uniti, in Europa, Asia, Australia e Sud America.

## <a name="step-1-create-a-storage-account"></a>Passaggio 1: Creare un account di archiviazione
Usare la procedura seguente per creare un nuovo account di archiviazione per una sottoscrizione ad Azure. Un account di archiviazione consente di accedere ai servizi di archiviazione di Azure. L'account di archiviazione rappresenta il livello più elevato dello spazio dei nomi per l'accesso a ogni componente dei servizi di archiviazione di Azure, ovvero servizi BLOB, servizi di accodamento e servizi tabelle. Per altre informazioni, fare riferimento a [Introduzione ad Archiviazione di Microsoft Azure](../storage/storage-introduction.md).

Per creare un account di archiviazione, è necessario essere amministratori del servizio o coamministratori della sottoscrizione.

> [!NOTE]
> Esistono diversi metodi che è possibile usare per creare un account di archiviazione, compresi il portale di Azure e Powershell.  Per questa esercitazione, verrà utilizzato il portale di Azure.  
> 
> 

**Per creare un account di archiviazione per una sottoscrizione di Azure**

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Nell'angolo in alto a sinistra della schermata fare clic su **Nuovo**. Nella finestra di dialogo **Nuovo** selezionare **Dati e archiviazione**, quindi fare clic su **Account di archiviazione**.
    
    Viene visualizzato il pannello **Crea account di archiviazione** .   

    ![Crea account di archiviazione][create-new-storage-account]  

3. Nel campo **Nome** digitare il nome di un sottodominio. Il nome può contenere tra 3 e 24 lettere minuscole e numeri.
   
    Questo valore diventa il nome host all'interno dell'URI usato per fare riferimento a risorse BLOB, di accodamento o tabelle per la sottoscrizione. Per fare riferimento a una risorsa contenitore nel servizio BLOB, usare un URI con il formato seguente, dove *&lt;EtichettaAccountArchiviazione&gt;* corrisponde al valore immesso in **Immettere un URL**:
   
    http://*&lt;EtichettaAccountArchiviazione&gt;*.blob.core.windows.net/*&lt;contenitorepersonale&gt;*
   
    **Importante:** l'etichetta dell'URL costituisce il sottodominio dell'URI dell'account di archiviazione e deve essere univoca in tutti i servizi ospitati in Azure.
   
    Questo valore viene usato anche come nome dell'account di archiviazione nel portale o quando si accede a questo account a livello di codice.
4. Lasciare le impostazioni predefinite per **Modello di distribuzione**, **Tipologia account**, **Prestazioni** e **Replica**. 
5. Selezionare la **Sottoscrizione** con cui verrà usato l'account di archiviazione.
6. Selezionare o creare un **gruppo di risorse**.  Per altre informazioni sui gruppi di risorse, vedere [Panoramica di Azure Resource Manager](../azure-resource-manager/resource-group-overview.md#resource-groups).
7. Selezionare la località per l'account di archiviazione.
8. Fare clic su **Crea**. Il completamento del processo di creazione dell'account di archiviazione potrebbe richiedere diversi minuti.

## <a name="step-2-enable-cdn-for-the-storage-account"></a>Passaggio 2: Abilitare la rete CDN per l'account di archiviazione

Grazie all'integrazione più recente, è ora possibile abilitare la rete CDN per l'account di archiviazione senza lasciare l'estensione del portale di archiviazione. 

1. Selezionare l'account di archiviazione, cercare "CDN" o scorrere verso il basso dal menu di navigazione a sinistra, quindi fare clic su "Rete CDN di Azure".
    
    Viene visualizzato il pannello **Rete CDN di Azure**.

    ![spostamento per l'abilitazione della rete CDN][cdn-enable-navigation]
    
2. Creare un nuovo endpoint immettendo le informazioni necessarie
    - **Profilo CDN**: è possibile creare un nuovo profilo o usarne uno esistente.
    - **Piano tariffario**: è possibile selezionare un piano tariffario solo se si crea un nuovo profilo CDN.
    - **Nome endpoint rete CDN** : immettere un nome dell'endpoint a scelta.

    > [!TIP]
    > L'endpoint della rete CDN creato utilizza il nome host dell'account di archiviazione come origine per impostazione predefinita.

    ![cdn new endpoint creation][cdn-new-endpoint-creation]

3. Dopo la creazione, il nuovo endpoint verrà visualizzato nell'elenco degli endpoint sopra riportato.

    ![nuovo endpoint di archiviazione rete CDN][cdn-storage-new-endpoint]

> [!NOTE]
> È anche possibile andare all'estensione Rete CDN di Azure per abilitare CDN.[Esercitazione](#Tutorial-cdn-create-profile).
> 
> 

[!INCLUDE [cdn-create-profile](../../includes/cdn-create-profile.md)]  

## <a name="step-3-enable-additional-cdn-features"></a>Passaggio 3: Abilitare funzionalità aggiuntive della rete CDN

Dal pannello "Rete CDN di Azure" dell'account di archiviazione fare clic sull'endpoint della rete CDN dall'elenco per aprire il pannello di configurazione della rete CDN. È possibile abilitare funzionalità aggiuntive della rete CDN per il recapito, ad esempio la compressione, la stringa di query, il filtro geografico. È inoltre possibile aggiungere mapping dominio personalizzato all'endpoint della rete CDN e abilitare HTTPS per il dominio personalizzato.
    
![configurazione CDN di archiviazione della rete CDN][cdn-storage-cdn-configuration]

## <a name="step-4-access-cdn-content"></a>Passaggio 4: Accedere al contenuto della rete CDN
Per accedere al contenuto memorizzato nella cache nella rete CDN, usare l'URL della rete CDN specificato nel portale. L'indirizzo per un oggetto BLOB memorizzato nella cache sarà analogo al seguente:

http://<*EndpointName*\>.azureedge.net/<*myPublicContainer*\>/<*BlobName*\>

> [!NOTE]
> Dopo l'abilitazione dell'accesso della rete CDN a un account di archiviazione, tutti gli oggetti disponibili pubblicamente saranno idonei per la memorizzazione nella cache perimetrale della rete CDN. Se si modifica un oggetto attualmente memorizzato nella cache nella rete CDN, il nuovo contenuto sarà disponibile tramite la rete CDN solo dopo l'aggiornamento dei contenuti della rete CDN alla scadenza della durata specificata per i contenuti memorizzati nella cache.
> 
> 

## <a name="step-5-remove-content-from-the-cdn"></a>Passaggio 5: rimuovere contenuto dalla rete CDN
Se non si desidera più memorizzare un oggetto nella cache della rete per la distribuzione di contenuti (rete CDN) di Azure, è possibile eseguire una delle operazioni seguenti:

* È possibile rendere privato il contenitore, invece di pubblico. Per altre informazioni, vedere [Gestire l'accesso in lettura anonimo a contenitori e BLOB](../storage/storage-manage-access-to-resources.md) .
* È possibile disabilitare o eliminare l'endpoint della rete CDN usando il portale di gestione.
* È possibile modificare il servizio ospitato, in modo che non risponda più a richieste per l'oggetto.

Un oggetto già memorizzato nella cache della rete CDN rimarrà nella cache fino alla scadenza della durata prevista per l'oggetto o fino a quando l’endpoint non verrà eliminato. Al termine della durata prevista, la rete CDN verificherà se l'endpoint della rete CDN è ancora valido e se l'oggetto è ancora accessibile in modo anonimo. In caso contrario, l'oggetto non sarà più memorizzato nella cache.

## <a name="additional-resources"></a>Risorse aggiuntive
* [Come eseguire il mapping del contenuto della rete CDN a un dominio personalizzato](cdn-map-content-to-custom-domain.md)
* [Abilitare HTTPS per il dominio personalizzato](cdn-custom-ssl.md)

[create-new-storage-account]: ./media/cdn-create-a-storage-account-with-cdn/CDN_CreateNewStorageAcct.png
[cdn-enable-navigation]: ./media/cdn-create-a-storage-account-with-cdn/cdn-storage-new-endpoint-creation.png
[cdn-storage-new-endpoint]: ./media/cdn-create-a-storage-account-with-cdn/cdn-storage-new-endpoint-list.png
[cdn-storage-cdn-configuration]: ./media/cdn-create-a-storage-account-with-cdn/cdn-storage-endpoint-configuration.png 

