---
title: Uso della rete CDN di Azure per accedere a BLOB con domini personalizzati tramite HTTPS
description: Informazioni su come integrare la rete CDN di Azure con l'archiviazione BLOB per accedere a BLOB con domini personalizzati tramite HTTPS
services: storage
documentationcenter: 
author: michaelhauss
manager: vamshik
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/04/2017
ms.author: mihauss
ms.openlocfilehash: 6bad04df324a374f6e8473890345cf516322abd6
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/29/2017
---
# <a name="using-the-azure-cdn-to-access-blobs-with-custom-domains-over-https"></a>Uso della rete CDN di Azure per accedere a BLOB con domini personalizzati tramite HTTPS

La rete per la distribuzione di contenuti (rete CDN) di Azure supporta ora HTTPS per i nomi di dominio personalizzati.
È possibile sfruttare questa funzionalità per accedere ai BLOB di archiviazione usando il dominio personalizzato tramite HTTPS. A questo scopo, è innanzitutto necessario abilitare la rete CDN di Azure nell'endpoint BLOB ed eseguire il mapping della rete CDN a un nome di dominio personalizzato. Dopo aver completato questi passaggi, l'uso di HTTPS per il dominio personalizzato risulta semplificato grazie all'abilitazione con un solo clic e alla gestione completa dei certificati, il tutto senza costi aggiuntivi rispetto ai normali prezzi della rete CDN.

Questa opportunità è importante perché permette di proteggere la privacy e l'integrità dei dati sensibili in transito dell'applicazione Web. L'uso del protocollo SSL per gestire il traffico tramite HTTPS garantisce la crittografia dei dati inviati su Internet. HTTPS garantisce attendibilità e autenticazione e protegge le applicazioni Web dagli attacchi.

> [!NOTE]
> Oltre a fornire il supporto SSL per i nomi di dominio personalizzati, la rete CDN di Azure semplifica il ridimensionamento dell'applicazione per distribuire contenuto con esigenze di larghezza di banda elevata in tutto il mondo.
> Per altre informazioni, vedere [Panoramica della rete per la distribuzione di contenuti (rete CDN) di Azure](../../cdn/cdn-overview.md).
>
>

## <a name="quick-start"></a>Avvio rapido

Questi sono i passaggi necessari per abilitare HTTPS per l'endpoint di archiviazione BLOB personalizzato:

1.  [Integrare un account di archiviazione di Azure con la rete CDN di Azure](../../cdn/cdn-create-a-storage-account-with-cdn.md).
    Questo articolo contiene informazioni dettagliate sulla creazione di un account di archiviazione nel portale di Azure.
2.  [Eseguire il mapping del contenuto della rete CDN a un dominio personalizzato](../../cdn/cdn-map-content-to-custom-domain.md).
3.  [Abilitare HTTPS in un dominio personalizzato della rete CDN di Azure](../../cdn/cdn-custom-ssl.md).

## <a name="shared-access-signatures"></a>Firme di accesso condiviso

Se l'endpoint di archiviazione BLOB è configurato per non consentire l'accesso anonimo in lettura, sarà necessario fornire un token di [firma di accesso condiviso](../common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) in ogni richiesta effettuata al dominio personalizzato. Per impostazione predefinita, gli endpoint di archiviazione BLOB non consentono l'accesso anonimo in lettura. Per altre informazioni sulle firme di accesso condiviso, vedere [Gestire l'accesso anonimo in lettura a contenitori e BLOB](storage-manage-access-to-resources.md).

La rete CDN di Azure non rispetta le eventuali restrizioni aggiunte al token di firma di accesso condiviso. Ad esempio, tutti i token di firma di accesso condiviso hanno una data di scadenza. Questo significa che è comunque possibile accedere al contenuto con una firma di accesso condiviso scaduta fino a quando il contenuto non viene eliminato dai nodi perimetrali della rete CDN. È possibile controllare l'intervallo di tempo per cui i dati vengono memorizzati nella cache nella rete CDN impostando l'intestazione della risposta della cache. Per istruzioni, vedere [Gestire la scadenza dei BLOB di archiviazione di Azure nella rete CDN di Azure](../../cdn/cdn-manage-expiration-of-blob-content.md).

Se si creano più URL di firma di accesso condiviso per lo stesso endpoint BLOB, è consigliabile attivare la memorizzazione nella cache delle stringhe di query per la rete CDN di Azure. In questo modo, ogni URL verrà considerato un'entità univoca. Per altre informazioni, vedere [Controllare il comportamento di memorizzazione nella cache della rete CDN di Azure con stringhe di query](../../cdn/cdn-query-string.md).

## <a name="http-to-https-redirection"></a>Reindirizzamento del traffico HTTP a HTTPS

È possibile scegliere di reindirizzare il traffico HTTP a HTTPS. A questo scopo, è necessario usare il livello Premium della rete CDN di Azure offerto da Verizon. È necessario [eseguire l'override del comportamento HTTP con il motore regole della rete CDN di Azure](../../cdn/cdn-rules-engine.md) con la regola seguente:

![](./media/storage-https-custom-domain-cdn/redirect-to-https.png)

"nome-endpoint-CDN" fa riferimento al nome configurato per l'endpoint della rete CDN. È possibile selezionare questo valore nell'elenco a discesa. "percorso-origine" fa riferimento al percorso all'interno dell'account di archiviazione di origine in cui si trova il contenuto statico.
Se si ospita tutto il contenuto statico in un singolo contenitore, sostituire "percorso-origine" con il nome del contenitore.

Per un approfondimento delle regole, vedere [Funzionalità del motore regole della rete CDN di Azure](../../cdn/cdn-rules-engine-reference-features.md).

## <a name="pricing-and-billing"></a>Prezzi e fatturazione

Quando si accede ai BLOB tramite una rete CDN di Azure, vengono applicati i [prezzi dell'archiviazione BLOB](https://azure.microsoft.com/pricing/details/storage/blobs/) per il traffico tra i nodi perimetrali e l'origine (archiviazione BLOB) e i [prezzi della rete CDN](https://azure.microsoft.com/pricing/details/cdn/) per l'accesso ai dati dai nodi perimetrali.

Si supponga, ad esempio, un account di archiviazione negli Stati Uniti occidentali cui si accede con una rete CDN di Azure. Se qualcuno in Regno Unito tenta di accedere a uno dei BLOB nell'account di archiviazione tramite la rete CDN, Azure controlla prima di tutto il nodo perimetrale più vicino al Regno Unito per il BLOB. Se lo trova, accede a questa copia del BLOB e applica i prezzi della rete CDN, perché l'accesso viene effettuato nella rete CDN. In caso contrario, Azure copia il BLOB nel nodo perimetrale, applicando costi di uscita e transazione in base ai prezzi di archiviazione BLOB, e quindi accede al file nel nodo perimetrale, generando la fatturazione della rete CDN.

Nel consultare la [pagina dei prezzi delle rete CDN](https://azure.microsoft.com/pricing/details/cdn/), tenere presente che il supporto HTTPS per i nomi di dominio personalizzati è disponibile solo per la rete CDN tramite prodotti Verizon (Standard e Premium).

## <a name="next-steps"></a>Passaggi successivi

[Configurare un nome di dominio completo per l'endpoint di archiviazione BLOB](storage-custom-domain-name.md)
