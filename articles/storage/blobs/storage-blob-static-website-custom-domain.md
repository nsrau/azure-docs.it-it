---
title: 'Esercitazione: Usare la rete CDN di Azure per abilitare un dominio personalizzato con SSL per un sito Web statico - Archiviazione di Azure'
description: Informazioni su come configurare un dominio personalizzato per l'hosting di siti Web statici.
services: storage
author: tamram
ms.service: storage
ms.topic: tutorial
ms.date: 12/07/2018
ms.author: tamram
ms.custom: seodec18
ms.openlocfilehash: 6ccd33805fe4b62d3456121321edc4eec3bff2e5
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2018
ms.locfileid: "53110316"
---
# <a name="tutorial-use-azure-cdn-to-enable-a-custom-domain-with-ssl-for-a-static-website"></a>Esercitazione: Usare la rete CDN di Azure per abilitare un dominio personalizzato con SSL per un sito Web statico

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

1. Nel Web browser aprire il [portale di Azure](https://portal.azure.com/). 
1. Individuare l'account di archiviazione e visualizzare la sezione della panoramica dell'account.
1. Scegliere **Rete CDN di Azure** dal menu **Servizio BLOB** per configurare la rete CDN di Azure.
1. Nella sezione **Nuovo endpoint** compilare i campi per creare un nuovo endpoint della rete CDN.
1. Immettere un nome di endpoint, ad esempio *mystaticwebsiteCDN*.
1. Immettere il dominio del sito Web come nome host per l'endpoint di rete CDN.
1. Per il nome host di origine, immettere l'endpoint del sito Web statico. Per individuare l'endpoint del sito Web statico, passare alla sezione **Sito Web statico** per l'account di archiviazione e copiare l'endpoint. 
1. Testare l'endpoint di rete CDN passando a *mywebsitecdn.azureedge.net* nel browser.

## <a name="enable-custom-domain-and-ssl"></a>Abilitare il dominio personalizzato ed SSL

1. Creare un record CNAME con il provider di nomi di dominio per reindirizzare il dominio personalizzato all'endpoint di rete CDN. Il record CNAME per il sottodominio *www* sarà simile al seguente:

    ![Specificare il record CNAME per il sottodominio www](media/storage-blob-static-website-custom-domain/subdomain-cname-record.png)

1. Nel portale di Azure fare clic sull'endpoint appena creato per configurare il dominio personalizzato e il certificato SSL.
1. In **Aggiungi dominio personalizzato** immettere il nome di dominio e quindi fare clic su **Aggiungi**.
1. Selezionare il mapping del dominio personalizzato appena creato per effettuare il provisioning di un certificato SSL.
1. Impostare **HTTPS dominio personalizzato** su **SÌ**. Selezionare **Gestito dalla rete CDN** per specificare che il certificato SSL deve essere gestito dalla rete CDN di Azure. Fare clic su **Save**.
1. Testare il sito Web accedendo all'URL.

## <a name="next-steps"></a>Passaggi successivi

Nella seconda parte di questa esercitazione si è appreso a configurare un dominio personalizzato con SSL per il sito Web statico nella rete CDN di Azure.

Fare clic su questo collegamento per altre informazioni sull'hosting di siti Web statici in Archiviazione di Azure.

> [!div class="nextstepaction"]
> [Altre informazioni sui siti Web statici](storage-blob-static-website.md)
