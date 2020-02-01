---
title: Integrare un sito Web statico con la rete CDN di Azure-archiviazione di Azure
description: Informazioni su come memorizzare nella cache il contenuto statico del sito Web da un account di archiviazione di Azure usando la rete per la distribuzione di contenuti (CDN) di Azure
author: normesta
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.author: normesta
ms.date: 01/22/2020
ms.openlocfilehash: aaf61ccbb3577036c614aa6196d2af57124550fa
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/31/2020
ms.locfileid: "76908553"
---
# <a name="integrate-a-static-website-with-azure-cdn"></a>Integrare un sito Web statico con la rete CDN di Azure

È possibile abilitare la rete per la [distribuzione di contenuti (CDN) di Azure](../../cdn/cdn-overview.md) per memorizzare nella cache il contenuto da un [sito Web statico](storage-blob-static-website.md) ospitato in un account di archiviazione di Azure. È possibile usare la rete CDN di Azure per configurare l'endpoint del dominio personalizzato per il sito Web statico, effettuare il provisioning di certificati SSL personalizzati e configurare regole di riscrittura personalizzate. La configurazione della rete CDN di Azure comporta costi aggiuntivi, ma offre latenze coerentemente ridotte per il sito Web ovunque nel mondo. La rete CDN di Azure fornisce anche la crittografia SSL con il certificato dell'utente. 

Per informazioni sui prezzi della rete CDN, vedere [Prezzi della rete CDN Azure](https://azure.microsoft.com/pricing/details/cdn/).

## <a name="enable-azure-cdn-for-your-static-website"></a>Abilitare la rete CDN di Azure per il sito Web statico

È possibile abilitare la rete CDN di Azure per il sito Web statico direttamente dall'account di archiviazione. Se si vogliono specificare impostazioni di configurazione avanzate per l'endpoint della rete CDN, come ad esempio l'[ottimizzazione del download di un file di grandi dimensioni](../../cdn/cdn-optimization-overview.md#large-file-download), è invece possibile usare l'[estensione Rete CDN di Azure](../../cdn/cdn-create-new-endpoint.md) per creare un profilo e un endpoint di rete CDN.

1. Individuare l'account di archiviazione nel portale di Azure e visualizzare la panoramica dell'account.

2. Scegliere **Rete CDN di Azure** dal menu **Servizio BLOB** per configurare la rete CDN di Azure.

    Verrà visualizzata la pagina **Rete CDN di Azure**.

    ![Creare un endpoint della rete CDN](../../cdn/media/cdn-create-a-storage-account-with-cdn/cdn-storage-new-endpoint-creation.png)

3. Nella sezione **Profilo CDN** specificare un profilo CDN nuovo o esistente. 

4. Specificare un piano tariffario per l'endpoint CDN. Per altre informazioni sui prezzi, vedere [prezzi della rete](https://azure.microsoft.com/pricing/details/cdn/)per la distribuzione di contenuti. Per altre informazioni sulle funzionalità disponibili in ogni livello, vedere [confrontare le funzionalità del prodotto](../../cdn/cdn-features.md)della rete CDN di Azure.

5. Nel campo **Nome endpoint rete CDN** specificare un nome per l'endpoint CDN. L'endpoint CDN deve essere univoco in Azure.

6. Specificare l'endpoint del sito Web statico nel campo **Nome host dell'origine**. 

   Per trovare l'endpoint del sito Web statico, passare alle impostazioni **Sito Web statico** per l'account di archiviazione.  Copiare l'endpoint primario e incollarlo nella configurazione della rete CDN.

   > [!IMPORTANT]
   > Assicurarsi di rimuovere l'identificatore del protocollo (*ad esempio*, HTTPS) e la barra finale nell'URL. Se, ad esempio, l'endpoint del sito Web statico è `https://mystorageaccount.z5.web.core.windows.net/`, è necessario specificare `mystorageaccount.z5.web.core.windows.net` nel campo **nome host di origine** .

   L'immagine seguente mostra una configurazione di esempio dell'endpoint:

   ![Screenshot che mostra una configurazione di esempio dell'endpoint CDN](media/storage-blob-static-website-custom-domain/add-cdn-endpoint.png)

7. Selezionare **Crea**e quindi attendere la propagazione. Dopo la creazione, l'endpoint verrà visualizzato nell'elenco di endpoint.

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

Un oggetto già memorizzato nella cache della rete CDN di Azure rimane nella cache fino alla scadenza della durata prevista per l'oggetto o fino a quando l'endpoint non viene [ripulito](../../cdn/cdn-purge-endpoint.md). Al termine della durata prevista, la rete CDN di Azure determina se l'endpoint della rete CDN è ancora valido e se l'oggetto è ancora accessibile in modo anonimo. In caso contrario, l'oggetto non sarà più memorizzato nella cache.

## <a name="next-steps"></a>Passaggi successivi

Opzionale Aggiungere un dominio personalizzato all'endpoint della rete CDN di Azure. Vedere [esercitazione: aggiungere un dominio personalizzato all'endpoint della rete CDN di Azure](../../cdn/cdn-map-content-to-custom-domain.md).
