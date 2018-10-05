---
title: Esercitazione - Accedere ai BLOB di archiviazione con un dominio personalizzato della rete CDN di Azure tramite HTTPS | Microsoft Docs
description: ''
services: cdn
documentationcenter: ''
author: dksimpson
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/15/2018
ms.author: v-deasim
ms.custom: mvc
ms.openlocfilehash: a1a86f28c9650c86df7c333ca52d772a09597efd
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2018
ms.locfileid: "47091055"
---
# <a name="tutorial-access-storage-blobs-using-an-azure-cdn-custom-domain-over-https"></a>Esercitazione: Accedere ai BLOB di archiviazione con un dominio personalizzato della rete CDN di Azure tramite HTTPS

Dopo aver integrato l'account di archiviazione di Azure con Rete di distribuzione dei contenuti di Azure (rete CDN), è possibile aggiungere un dominio personalizzato e abilitare HTTPS nel dominio per l'endpoint archiviazione BLOB personalizzato. 

## <a name="prerequisites"></a>Prerequisiti

Prima di completare la procedura descritta in questa esercitazione, è necessario integrare l'account di archiviazione di Azure con la rete CDN di Azure. Per altre informazioni, vedere [Guida introduttiva: Integrare un account di archiviazione di Azure con la rete CDN di Azure](cdn-create-a-storage-account-with-cdn.md).

## <a name="add-a-custom-domain"></a>Aggiungere un dominio personalizzato
Quando si crea un endpoint della rete CDN nel profilo, il nome dell'endpoint, corrispondente a un sottodominio di azureedge.net, viene incluso nell'URL per la distribuzione del contenuto CDN per impostazione predefinita. È anche possibile scegliere di associare un dominio personalizzato a un endpoint della rete CDN. Grazie a questa opzione, si distribuisce il contenuto con un dominio personalizzato nell'URL invece che con un nome di endpoint. Per aggiungere un dominio personalizzato all'endpoint, seguire le istruzioni in questa esercitazione: [Aggiungere un dominio personalizzato all'endpoint della rete CDN di Azure](cdn-map-content-to-custom-domain.md).

## <a name="configure-https"></a>Configurare HTTPS
Usando il protocollo HTTPS nel dominio personalizzato, ci si assicura che i dati vengano recapitati in modo sicuro su Internet tramite crittografia TLS/SSL. Quando il browser è connesso a un sito Web tramite HTTPS, convalida il certificato di sicurezza del sito Web e verifica che sia emesso da un'autorità di certificazione legittima. Per configurare HTTPS nel dominio personalizzato, seguire le istruzioni riportate in questa esercitazione: [Configurare HTTPS in un dominio personalizzato della rete CDN di Azure](cdn-custom-ssl.md).

## <a name="shared-access-signatures"></a>Firme di accesso condiviso
Se l'endpoint di archiviazione BLOB è configurato per non consentire l'accesso anonimo in lettura, è necessario fornire un token di [firma di accesso condiviso](cdn-sas-storage-support.md) in ogni richiesta effettuata al dominio personalizzato. Per impostazione predefinita, gli endpoint di archiviazione BLOB non consentono l'accesso anonimo in lettura. Per altre informazioni sulla firma di accesso condiviso, vedere [Gestire l'accesso in lettura anonimo a contenitori e BLOB](../storage/blobs/storage-manage-access-to-resources.md).

La rete CDN di Azure ignora le eventuali restrizioni aggiunte al token di firma di accesso condiviso. Ad esempio, tutti i token di firma di accesso condiviso hanno una scadenza e questo significa che è comunque possibile accedere al contenuto con una firma di accesso condiviso scaduta fino a quando il contenuto non viene eliminato dai server POP (Point of Presence) della rete CDN. È possibile controllare l'intervallo di tempo per cui i dati vengono memorizzati nella cache nella rete CDN di Azure impostando l'intestazione della risposta della cache. Per altre informazioni, vedere [Gestire la scadenza dei BLOB di archiviazione di Azure nella rete CDN di Azure](cdn-manage-expiration-of-blob-content.md).

Se si creano più URL di firma di accesso condiviso per lo stesso endpoint BLOB, prendere in considerazione l'abilitazione della memorizzazione nella cache delle stringhe di query. In questo modo, ogni URL viene considerato un'entità univoca. Per altre informazioni, vedere [Controllare il comportamento di memorizzazione nella cache della rete CDN di Azure con stringhe di query](cdn-query-string.md).

## <a name="http-to-https-redirection"></a>Reindirizzamento da HTTP a HTTPS
È possibile scegliere di reindirizzare il traffico HTTP a HTTPS tramite la creazione di una [regola di reindirizzamento URL](cdn-rules-engine-reference-features.md#url-redirect) con il [motore regole della rete CDN di Azure](cdn-rules-engine.md). Questa opzione richiede un profilo di **rete CDN Premium di Azure con tecnologia Verizon**. 

![Regola di reindirizzamento URL](./media/cdn-storage-custom-domain-https/cdn-url-redirect-rule.png)

In questa regola *Cdn-endpoint-name* fa riferimento al nome configurato per l'endpoint della rete CDN, che è possibile selezionare nell'elenco a discesa. Il valore per *origin-path* fa riferimento al percorso all'interno dell'account di archiviazione di origine in cui si trova il contenuto statico. Se si ospita tutto il contenuto statico in un singolo contenitore, sostituire *origin-path* con il nome del contenitore.

## <a name="pricing-and-billing"></a>Prezzi e fatturazione
Quando si accede ai BLOB tramite la rete CDN di Azure, vengono applicati i [prezzi dell'archiviazione BLOB](https://azure.microsoft.com/pricing/details/storage/blobs/) per il traffico tra i server POP e l'origine (archiviazione BLOB) e i [prezzi della rete CDN](https://azure.microsoft.com/pricing/details/cdn/) per i dati a cui si accede dai server POP.

Ad esempio, se si dispone di un account di archiviazione negli Stati Uniti a cui si accede tramite la rete CDN di Azure e un utente in Europa tenta di accedere a uno dei BLOB in tale account di archiviazione tramite la rete CDN di Azure, la rete CDN di Azure controlla prima di tutto il POP più vicino all'area Europa per tale BLOB. Se viene trovato, la rete CDN di Azure accede a questa copia del BLOB e usa il piano tariffario della rete CDN, perché l'accesso avviene sulla rete CDN di Azure. In caso contrario, la rete CDN di Azure copia il BLOB nel server POP, applicando costi di uscita e transazione in base ai prezzi di archiviazione BLOB, e quindi accede al file nel server POP, generando la fatturazione della rete CDN di Azure.

## <a name="next-steps"></a>Passaggi successivi
[Esercitazione: Impostare le regole di memorizzazione nella cache della rete CDN di Azure](cdn-caching-rules-tutorial.md)




