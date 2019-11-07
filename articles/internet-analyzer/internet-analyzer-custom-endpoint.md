---
title: Creare un endpoint personalizzato | Microsoft Docs
description: Questo articolo descrive come configurare un endpoint personalizzato per la misurazione con la risorsa Analizzatore Internet.
services: internet-analyzer
author: mattcalder
ms.service: internet-analyzer
ms.topic: quickstart
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: 44d570746a54154a2f8d3c7a200c761a59b7422b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73488107"
---
# <a name="measure-custom-endpoints-to-evaluate-in-your-internet-analyzer-tests"></a>Misurare gli endpoint personalizzati da valutare nei test dell'analizzatore Internet 

Questo articolo illustra come configurare un endpoint personalizzato da misurare come parte dei test dell'analizzatore Internet. Gli endpoint personalizzati consentono di valutare i carichi di lavoro locali, i carichi di lavoro in esecuzione in altri provider di servizi cloud e le configurazioni personalizzate di Azure.  È possibile confrontare due endpoint personalizzati in un test se un endpoint è una risorsa di Azure. Per altre informazioni sull'analizzatore Internet, vedere la [panoramica](internet-analyzer-overview.md). 

> [!IMPORTANT]
> L'anteprima pubblica viene messa a disposizione senza contratto di servizio e non deve essere usata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate, potrebbero avere funzioni limitate o potrebbero non essere disponibili in tutte le località di Azure. Vedere [Condizioni supplementari per l'uso delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="before-you-begin"></a>Prima di iniziare

Assicurarsi di configurare una risorsa analizzatore Internet e selezionare l'opzione "Endpoint personalizzato". L'analizzatore Internet presuppone che l'endpoint personalizzato sia accessibile da Internet. Per altre informazioni, vedere [Creare una risorsa analizzatore Internet](internet-analyzer-create-test-portal.md).


## <a name="create-custom-endpoint"></a>Creare un endpoint personalizzato

1. Scaricare un'immagine di test trasparente da un pixel [qui](https://fpc.msedge.net/apc/trans.gif). Questa immagine da un pixel è l'asset che verrà recuperato dal client JavaScript per misurare le prestazioni.
2. Nell'applicazione Web personalizzata distribuire l'immagine di test in un percorso accessibile pubblicamente. Il percorso deve funzionare tramite HTTPS. 
3. Copiare l'URL completo dell'endpoint personalizzato, ad esempio https://contoso.com/test/trans.gif), nel campo Endpoint personalizzato durante la creazione del test.

## <a name="next-steps"></a>Passaggi successivi

Leggere le [Domande frequenti sull'analizzatore Internet](internet-analyzer-faq.md)

