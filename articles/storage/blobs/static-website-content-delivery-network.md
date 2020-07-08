---
title: Integrare un sito Web statico con la rete CDN di Azure-archiviazione di Azure
description: Informazioni su come memorizzare nella cache il contenuto statico del sito Web da un account di archiviazione di Azure usando la rete per la distribuzione di contenuti (CDN) di Azure
author: normesta
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.author: normesta
ms.date: 04/07/2020
ms.openlocfilehash: 02b7e02c33161db33420e2efe1ef4b70a138d127
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84465219"
---
# <a name="integrate-a-static-website-with-azure-cdn"></a>Integrare un sito Web statico con la rete CDN di Azure

È possibile abilitare la rete per la [distribuzione di contenuti (CDN) di Azure](../../cdn/cdn-overview.md) per memorizzare nella cache il contenuto da un [sito Web statico](storage-blob-static-website.md) ospitato in un account di archiviazione di Azure. È possibile usare la rete CDN di Azure per configurare l'endpoint del dominio personalizzato per il sito Web statico, effettuare il provisioning di certificati TLS/SSL personalizzati e configurare regole di riscrittura personalizzate. La configurazione della rete CDN di Azure comporta costi aggiuntivi, ma offre latenze coerentemente ridotte per il sito Web ovunque nel mondo. La rete CDN di Azure offre anche la crittografia TLS con il proprio certificato. 

Per informazioni sui prezzi della rete CDN, vedere [Prezzi della rete CDN Azure](https://azure.microsoft.com/pricing/details/cdn/).

## <a name="enable-azure-cdn-for-your-static-website"></a>Abilitare la rete CDN di Azure per il sito Web statico

È possibile abilitare la rete CDN di Azure per il sito Web statico direttamente dall'account di archiviazione. Se si vogliono specificare impostazioni di configurazione avanzate per l'endpoint della rete CDN, come ad esempio l'[ottimizzazione del download di un file di grandi dimensioni](../../cdn/cdn-optimization-overview.md#large-file-download), è invece possibile usare l'[estensione Rete CDN di Azure](../../cdn/cdn-create-new-endpoint.md) per creare un profilo e un endpoint di rete CDN.

1. Individuare l'account di archiviazione nel portale di Azure e visualizzare la panoramica dell'account.

1. Nel menu **servizio BLOB** selezionare rete **CDN di Azure** per aprire la pagina della rete **CDN di Azure** :

    ![Creare un endpoint della rete CDN](media/storage-blob-static-website-custom-domain/cdn-storage-new.png)

1. Nella sezione del profilo di rete **CDN** specificare se creare un nuovo profilo di rete CDN o utilizzarne uno esistente. Un profilo di rete CDN è una raccolta di endpoint della rete CDN che condividono un piano tariffario e un provider. Immettere quindi un nome per la rete CDN univoca all'interno della sottoscrizione.

1. Specificare un piano tariffario per l'endpoint CDN. Per altre informazioni sui prezzi, vedere [prezzi della rete](https://azure.microsoft.com/pricing/details/cdn/)per la distribuzione di contenuti. Per altre informazioni sulle funzionalità disponibili in ogni livello, vedere [confrontare le funzionalità del prodotto](../../cdn/cdn-features.md)della rete CDN di Azure.

1. Nel campo **Nome endpoint rete CDN** specificare un nome per l'endpoint CDN. L'endpoint della rete CDN deve essere univoco in Azure e fornisce la prima parte dell'URL dell'endpoint. Il modulo convalida che il nome dell'endpoint sia univoco.

1. Specificare l'endpoint del sito Web statico nel campo **nome host di origine** . 

   Per trovare l'endpoint del sito Web statico, passare alle impostazioni **Sito Web statico** per l'account di archiviazione.  Copiare l'endpoint primario e incollarlo nella configurazione della rete CDN.

   > [!IMPORTANT]
   > Assicurarsi di rimuovere l'identificatore del protocollo (*ad esempio*, HTTPS) e la barra finale nell'URL. Ad esempio, se l'endpoint del sito Web statico è `https://mystorageaccount.z5.web.core.windows.net/` , specificare `mystorageaccount.z5.web.core.windows.net` nel campo **nome host di origine** .

   L'immagine seguente mostra una configurazione di esempio dell'endpoint:

   ![Screenshot che mostra una configurazione di esempio dell'endpoint CDN](media/storage-blob-static-website-custom-domain/add-cdn-endpoint.png)

1. Selezionare **Crea**e quindi attendere che la rete CDN effettui il provisioning. Dopo la creazione, l'endpoint verrà visualizzato nell'elenco di endpoint. Se nel form sono presenti errori, accanto a tale campo verrà visualizzato un punto esclamativo.

1. Per verificare che l'endpoint CDN sia configurato correttamente, fare clic sull'endpoint per passare alle relative impostazioni. Nella panoramica della rete CDN per l'account di archiviazione individuare il nome host dell'endpoint e passare all'endpoint, come illustrato nell'immagine seguente. Il formato dell'endpoint CDN sarà simile a `https://staticwebsitesamples.azureedge.net`.

    ![Screenshot che mostra la panoramica dell'endpoint CDN](media/storage-blob-static-website-custom-domain/verify-cdn-endpoint.png)

1. Una volta eseguito il provisioning dell'endpoint della rete CDN, passando all'endpoint della rete CDN viene visualizzato il contenuto del file index.html caricato in precedenza nel sito Web statico.

1. Per esaminare le impostazioni dell'origine per l'endpoint CDN, passare a **Origine** nella sezione **Impostazioni** per l'endpoint CDN. Il campo **Tipo di origine** risulterà impostato su *Origine personalizzata* e nel campo **Nome host dell'origine** verrà visualizzato l'endpoint del sito Web statico.

    ![Screenshot che mostra le impostazioni dell'origine per l'endpoint CDN](media/storage-blob-static-website-custom-domain/verify-cdn-origin.png)

## <a name="remove-content-from-azure-cdn"></a>Rimuovere contenuti dalla rete CDN di Azure

Se non si vuole più memorizzare un oggetto nella cache della rete CDN di Azure, è possibile eseguire una delle operazioni seguenti:

* Rendere privato il contenitore, invece di pubblico. Per altre informazioni, vedere [Gestire l'accesso in lettura anonimo a contenitori e BLOB](storage-manage-access-to-resources.md).
* Disabilitare o eliminare l'endpoint della rete CDN usando il portale di Azure.
* Modificare il servizio ospitato, in modo che non risponda più a richieste per l'oggetto.

Un oggetto già memorizzato nella cache della rete CDN di Azure rimane nella cache fino alla scadenza della durata prevista per l'oggetto o fino a quando l'endpoint non viene [ripulito](../../cdn/cdn-purge-endpoint.md). Al termine della durata prevista, la rete CDN di Azure determina se l'endpoint della rete CDN è ancora valido e se l'oggetto è ancora accessibile in modo anonimo. In caso contrario, l'oggetto non sarà più memorizzato nella cache.

## <a name="next-steps"></a>Passaggi successivi

Opzionale Aggiungere un dominio personalizzato all'endpoint della rete CDN di Azure. Vedere [esercitazione: aggiungere un dominio personalizzato all'endpoint della rete CDN di Azure](../../cdn/cdn-map-content-to-custom-domain.md).
