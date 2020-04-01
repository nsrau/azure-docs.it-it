---
title: Integrare un sito Web statico con la rete CDN di Azure - Archiviazione di AzureIntegrate a static website with Azure CDN - Azure Storage
description: Informazioni su come memorizzare nella cache il contenuto statico del sito Web da un account di archiviazione di Azure usando la rete per la distribuzione di contenuti (CDN) di Azure.Learn how to cache static website content from an Azure Storage account by using Azure Content Delivery Network (CDN).
author: normesta
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.author: normesta
ms.date: 01/22/2020
ms.openlocfilehash: 8eeff5187d27cb75b9e55eba8311dede8970bc4a
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2020
ms.locfileid: "80435226"
---
# <a name="integrate-a-static-website-with-azure-cdn"></a>Integrare un sito Web statico con la rete CDN di AzureIntegrate a static website with Azure CDN

È possibile abilitare la rete per [la distribuzione di contenuti (CDN)](../../cdn/cdn-overview.md) di Azure per memorizzare nella cache il contenuto di un sito Web statico ospitato in un account di archiviazione di Azure.You can enable Azure Content Delivery Network (CDN) to cache content from a [static website](storage-blob-static-website.md) that is hosted in an Azure storage account. È possibile usare la rete CDN di Azure per configurare l'endpoint di dominio personalizzato per il sito Web statico, eseguire il provisioning di certificati TLS/SSL personalizzati e configurare regole di riscrittura personalizzate. La configurazione della rete CDN di Azure comporta costi aggiuntivi, ma offre latenze coerentemente ridotte per il sito Web ovunque nel mondo. La rete CDN di Azure fornisce anche la crittografia TLS con il proprio certificato. 

Per informazioni sui prezzi della rete CDN, vedere [Prezzi della rete CDN Azure](https://azure.microsoft.com/pricing/details/cdn/).

## <a name="enable-azure-cdn-for-your-static-website"></a>Abilitare la rete CDN di Azure per il sito Web staticoEnable Azure CDN for your static website

È possibile abilitare la rete CDN di Azure per il sito Web statico direttamente dall'account di archiviazione. Se si vogliono specificare impostazioni di configurazione avanzate per l'endpoint della rete CDN, come ad esempio l'[ottimizzazione del download di un file di grandi dimensioni](../../cdn/cdn-optimization-overview.md#large-file-download), è invece possibile usare l'[estensione Rete CDN di Azure](../../cdn/cdn-create-new-endpoint.md) per creare un profilo e un endpoint di rete CDN.

1. Individuare l'account di archiviazione nel portale di Azure e visualizzare la panoramica dell'account.

2. Scegliere **Rete CDN di Azure** dal menu **Servizio BLOB** per configurare la rete CDN di Azure.

    Verrà visualizzata la pagina **Rete CDN di Azure**.

    ![Creare un endpoint della rete CDN](../../cdn/media/cdn-create-a-storage-account-with-cdn/cdn-storage-new-endpoint-creation.png)

3. Nella sezione **Profilo CDN** specificare un profilo CDN nuovo o esistente. 

4. Specificare un piano tariffario per l'endpoint CDN. Per ulteriori informazioni sui prezzi, vedere Prezzi della rete per [la distribuzione di contenuti](https://azure.microsoft.com/pricing/details/cdn/). Per altre informazioni sulle funzionalità disponibili con ogni livello, vedere [Confrontare](../../cdn/cdn-features.md)le funzionalità del prodotto della rete CDN di Azure.For more information about the features available with each tier, see Compare Azure CDN product features .

5. Nel campo **Nome endpoint rete CDN** specificare un nome per l'endpoint CDN. L'endpoint CDN deve essere univoco in Azure.

6. Specificare l'endpoint del sito Web statico nel campo **Nome host dell'origine**. 

   Per trovare l'endpoint del sito Web statico, passare alle impostazioni **Sito Web statico** per l'account di archiviazione.  Copiare l'endpoint primario e incollarlo nella configurazione della rete CDN.

   > [!IMPORTANT]
   > Assicurarsi di rimuovere l'identificatore di protocollo *(ad esempio,* HTTPS) e la barra finale nell'URL. Ad esempio, se l'endpoint del sito Web statico è `https://mystorageaccount.z5.web.core.windows.net/`, è necessario specificare `mystorageaccount.z5.web.core.windows.net` nel campo Nome host **origine.**

   L'immagine seguente mostra una configurazione di esempio dell'endpoint:

   ![Screenshot che mostra una configurazione di esempio dell'endpoint CDN](media/storage-blob-static-website-custom-domain/add-cdn-endpoint.png)

7. Selezionare **Crea**, quindi attendere che venga propagato. Dopo la creazione, l'endpoint verrà visualizzato nell'elenco di endpoint.

8. Per verificare che l'endpoint CDN sia configurato correttamente, fare clic sull'endpoint per passare alle relative impostazioni. Nella panoramica della rete CDN per l'account di archiviazione individuare il nome host dell'endpoint e passare all'endpoint, come illustrato nell'immagine seguente. Il formato dell'endpoint CDN sarà simile a `https://staticwebsitesamples.azureedge.net`.

    ![Screenshot che mostra la panoramica dell'endpoint CDN](media/storage-blob-static-website-custom-domain/verify-cdn-endpoint.png)

9. Al termine della propagazione dell'endpoint CDN, passando all'endpoint CDN verrà visualizzato il contenuto del file index.html caricato in precedenza nel sito Web statico.

10. Per esaminare le impostazioni dell'origine per l'endpoint CDN, passare a **Origine** nella sezione **Impostazioni** per l'endpoint CDN. Il campo **Tipo di origine** risulterà impostato su *Origine personalizzata* e nel campo **Nome host dell'origine** verrà visualizzato l'endpoint del sito Web statico.

    ![Screenshot che mostra le impostazioni dell'origine per l'endpoint CDN](media/storage-blob-static-website-custom-domain/verify-cdn-origin.png)

## <a name="remove-content-from-azure-cdn"></a>Rimuovere contenuti dalla rete CDN di Azure

Se non si vuole più memorizzare un oggetto nella cache della rete CDN di Azure, è possibile eseguire una delle operazioni seguenti:

* Rendere privato il contenitore, invece di pubblico. Per altre informazioni, vedere [Gestire l'accesso in lettura anonimo a contenitori e BLOB](storage-manage-access-to-resources.md).
* Disabilitare o eliminare l'endpoint della rete CDN usando il portale di Azure.
* Modificare il servizio ospitato, in modo che non risponda più a richieste per l'oggetto.

Un oggetto già memorizzato nella cache nella rete CDN di Azure rimane memorizzato nella cache fino alla scadenza del periodo di durata dell'oggetto o fino [all'eliminazione](../../cdn/cdn-purge-endpoint.md)dell'endpoint. Al termine della durata prevista, la rete CDN di Azure determina se l'endpoint della rete CDN è ancora valido e se l'oggetto è ancora accessibile in modo anonimo. In caso contrario, l'oggetto non sarà più memorizzato nella cache.

## <a name="next-steps"></a>Passaggi successivi

(Facoltativo) Aggiungere un dominio personalizzato all'endpoint della rete CDN di Azure.Add a custom domain to your Azure CDN endpoint. Vedere [Esercitazione: Aggiungere un dominio personalizzato all'endpoint della rete CDN di Azure.See Tutorial: Add a custom domain to your Azure CDN endpoint.](../../cdn/cdn-map-content-to-custom-domain.md)
