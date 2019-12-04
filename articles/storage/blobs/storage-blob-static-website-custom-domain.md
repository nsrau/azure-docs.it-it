---
title: 'Esercitazione: Abilitare un dominio personalizzato e SSL per un sito Web statico in Azure'
description: Informazioni su come configurare un dominio personalizzato per l'hosting di siti Web statici.
author: normesta
ms.service: storage
ms.topic: tutorial
ms.date: 11/19/2019
ms.author: normesta
ms.reviewer: dineshm
ms.openlocfilehash: 893ac53dc9f0b6b162c5ec22e478cd15706e50fb
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74327505"
---
# <a name="tutorial-enable-custom-domain--ssl-for-a-static-website-in-azure"></a>Esercitazione: Abilitare un dominio personalizzato e SSL per un sito Web statico in Azure

Questa è la seconda di una serie di esercitazioni. In questa parte si apprenderà ad abilitare l'endpoint di un dominio personalizzato con SSL per il sito Web statico. 

Questa esercitazione mostra come usare la [rete CDN di Azure](../../cdn/cdn-overview.md) per configurare l'endpoint del dominio personalizzato per il sito Web statico. Con la rete CDN di Azure è possibile effettuare il provisioning di certificati SSL personalizzati, usare un dominio personalizzato e configurare regole di riscrittura personalizzate, il tutto contemporaneamente. La configurazione della rete CDN di Azure comporta costi aggiuntivi, ma offre latenze coerentemente ridotte per il sito Web ovunque nel mondo. La rete CDN di Azure fornisce anche la crittografia SSL con il certificato dell'utente. Per informazioni sui prezzi della rete CDN, vedere [Prezzi della rete CDN Azure](https://azure.microsoft.com/pricing/details/cdn/).

Nella seconda parte della serie si apprenderà come:

> [!div class="checklist"]
> * Creare un endpoint di rete CDN nell'endpoint del sito Web statico
> * Abilitare il dominio personalizzato ed SSL

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare questa esercitazione, completare la prima parte: [Esercitazione: Ospitare un sito Web statico nell'archiviazione BLOB](storage-blob-static-website-host.md). 

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Accedere al [portale di Azure](https://portal.azure.com/) per iniziare.

## <a name="create-a-cdn-endpoint-on-the-static-website-endpoint"></a>Creare un endpoint di rete CDN nell'endpoint del sito Web statico

1. Individuare l'account di archiviazione nel portale di Azure e visualizzare la panoramica dell'account.
1. Scegliere **Rete CDN di Azure** dal menu **Servizio BLOB** per configurare la rete CDN di Azure.
1. Nella sezione **Profilo CDN** specificare un profilo CDN nuovo o esistente. Per altre informazioni, vedere [Avvio rapido: Creare un profilo e un endpoint della rete CDN di Azure](../../cdn/cdn-create-new-endpoint.md).
1. Specificare un piano tariffario per l'endpoint CDN. Questa esercitazione usa il piano tariffario **Akamai standard** perché la propagazione viene completata rapidamente, in genere in pochi minuti. Altri piani tariffari potrebbero richiedere più tempo per la propagazione, ma possono offrire altri vantaggi. Per altre informazioni, vedere [Confronto tra funzionalità dei prodotti della rete CDN di Azure](../../cdn/cdn-features.md).
1. Nel campo **Nome endpoint rete CDN** specificare un nome per l'endpoint CDN. L'endpoint CDN deve essere univoco in Azure.
1. Specificare l'endpoint del sito Web statico nel campo **Nome host dell'origine**. Per trovare l'endpoint del sito Web statico, passare alle impostazioni **Sito Web statico** per l'account di archiviazione. Copiare l'endpoint primario e incollarlo nella configurazione della rete CDN, rimuovendo l'identificatore del protocollo (*ad esempio* HTTPS).

    L'immagine seguente mostra una configurazione di esempio dell'endpoint:

    ![Screenshot che mostra una configurazione di esempio dell'endpoint CDN](media/storage-blob-static-website-custom-domain/add-cdn-endpoint.png)

1. Creare l'endpoint CDN e attendere il completamento della propagazione.
1. Per verificare che l'endpoint CDN sia configurato correttamente, fare clic sull'endpoint per passare alle relative impostazioni. Nella panoramica della rete CDN per l'account di archiviazione individuare il nome host dell'endpoint e passare all'endpoint, come illustrato nell'immagine seguente. Il formato dell'endpoint CDN sarà simile a `https://staticwebsitesamples.azureedge.net`.

    ![Screenshot che mostra la panoramica dell'endpoint CDN](media/storage-blob-static-website-custom-domain/verify-cdn-endpoint.png)

    Al termine della propagazione dell'endpoint CDN, passando all'endpoint CDN verrà visualizzato il contenuto del file index.html caricato in precedenza nel sito Web statico.

1. Per esaminare le impostazioni dell'origine per l'endpoint CDN, passare a **Origine** nella sezione **Impostazioni** per l'endpoint CDN. Il campo **Tipo di origine** risulterà impostato su *Origine personalizzata* e nel campo **Nome host dell'origine** verrà visualizzato l'endpoint del sito Web statico.

    ![Screenshot che mostra le impostazioni dell'origine per l'endpoint CDN](media/storage-blob-static-website-custom-domain/verify-cdn-origin.png)

## <a name="enable-custom-domain-and-ssl"></a>Abilitare il dominio personalizzato ed SSL

1. Creare un record CNAME con il provider di nomi di dominio per reindirizzare il dominio personalizzato all'endpoint di rete CDN. Il record CNAME per il sottodominio *www* sarà simile al seguente:

    ![Specificare il record CNAME per il sottodominio www](media/storage-blob-static-website-custom-domain/subdomain-cname-record.png)

1. Nel portale di Azure visualizzare le impostazioni per l'endpoint CDN. Passare a **Domini personalizzati** in **Impostazioni** per configurare il dominio personalizzato e il certificato SSL.
1. In **Aggiungi dominio personalizzato** immettere il nome di dominio e quindi fare clic su **Aggiungi**.
1. Selezionare il nuovo mapping del dominio personalizzato per effettuare il provisioning di un certificato SSL.
1. Impostare **HTTPS dominio personalizzato** su **SÌ** e quindi fare clic su **Salva**. La configurazione del dominio personalizzato potrebbe richiedere diverse ore. Lo stato di avanzamento verrà visualizzato nel portale come illustrato nell'immagine seguente.

    ![Screenshot che mostra lo stato di avanzamento della configurazione del dominio personalizzato](media/storage-blob-static-website-custom-domain/configure-custom-domain-https.png)

1. Testare il mapping del sito Web statico al dominio personalizzato accedendo all'URL del dominio personalizzato.

Per altre informazioni sull'abilitazione di HTTPS per i domini personalizzati, vedere [Esercitazione: Configurare HTTPS in un dominio personalizzato della rete CDN di Azure](../../cdn/cdn-custom-ssl.md).

## <a name="next-steps"></a>Passaggi successivi

Nella seconda parte di questa esercitazione si è appreso a configurare un dominio personalizzato con SSL per il sito Web statico nella rete CDN di Azure.

Per altre informazioni sulla configurazione e sull'uso della rete CDN di Azure, vedere [Informazioni sulla rete CDN di Azure](../../cdn/cdn-overview.md).