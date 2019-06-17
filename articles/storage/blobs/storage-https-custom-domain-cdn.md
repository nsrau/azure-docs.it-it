---
title: Usare Rete CDN di Azure per accedere ai BLOB con domini personalizzati tramite HTTPS
description: Informazioni su come integrare Rete CDN di Azure con l'archiviazione BLOB per accedere ai BLOB con domini personalizzati tramite HTTPS
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.date: 06/26/2018
ms.author: normesta
ms.reviewer: seguler
ms.subservice: blobs
ms.openlocfilehash: da3a6dcb0d125ac4666bc375e843c57cf12fb2fc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65148400"
---
# <a name="use-azure-cdn-to-access-blobs-with-custom-domains-over-https"></a>Usare Rete CDN di Azure per accedere ai BLOB con domini personalizzati tramite HTTPS

La rete per la distribuzione di contenuti di Azure (Rete CDN di Azure) supporta ora HTTPS per i nomi di dominio personalizzati. Con Rete CDN di Azure è possibile accedere ai BLOB usando il nome di dominio personalizzato tramite HTTPS. A questo scopo abilitare Rete CDN di Azure nell'endpoint BLOB o Web ed eseguire quindi il mapping di Rete CDN di Azure a un nome di dominio personalizzato. Al termine, l'abilitazione di HTTPS per il dominio personalizzato viene semplificato tramite l'accesso con un clic e la gestione completa dei certificati. Non viene applicato alcun aumento dei prezzi standard di Rete CDN di Azure.

Rete CDN di Azure aiuta a proteggere la riservatezza e l'integrità dei dati in transito delle applicazioni Web. L'uso del protocollo SSL per gestire il traffico tramite HTTPS garantisce la crittografia dei dati inviati su Internet. L'uso di HTTPS con Rete CDN di Azure consente di proteggere le applicazioni Web dagli attacchi.

> [!NOTE]  
> Oltre a fornire il supporto SSL per i nomi di dominio personalizzati, Rete CDN di Azure semplifica la scalabilità dell'applicazione per distribuire contenuto con esigenze di larghezza di banda elevata in tutto il mondo. Per altre informazioni, vedere [Panoramica di Rete CDN di Azure](../../cdn/cdn-overview.md).

## <a name="quickstart"></a>Guida introduttiva

Per abilitare HTTPS per l'endpoint di archiviazione BLOB personalizzato, eseguire le operazioni seguenti:

1.  [Integrare un account di archiviazione di Azure con la rete CDN di Azure](../../cdn/cdn-create-a-storage-account-with-cdn.md).  
    Questo articolo contiene informazioni dettagliate sulla creazione di un account di archiviazione nel portale di Azure, se questa operazione non è stata ancora eseguita.

    > [!NOTE]  
    > Per aggiungere l'endpoint Web di archiviazione durante l'anteprima del supporto per siti Web statici in Archiviazione di Azure, selezionare **Origine personalizzata** nell'elenco a discesa **Tipo di origine**. Nel portale di Azure è necessario eseguire questa operazione dal profilo di Rete CDN di Azure anziché direttamente nell'account di archiviazione.

2.  [Eseguire il mapping del contenuto della rete CDN a un dominio personalizzato](../../cdn/cdn-map-content-to-custom-domain.md).

3.  [Abilitare HTTPS in un dominio personalizzato della rete CDN di Azure](../../cdn/cdn-custom-ssl.md).

## <a name="shared-access-signatures"></a>Firme di accesso condiviso

Per impostazione predefinita, gli endpoint di archiviazione BLOB non consentono l'accesso anonimo in lettura. Se l'endpoint di archiviazione BLOB è configurato per non consentire l'accesso anonimo in lettura, fornire un token di [firma di accesso condiviso](../common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) in ogni richiesta al dominio personalizzato. Per altre informazioni, vedere [Gestire l'accesso in lettura anonimo a contenitori e BLOB](storage-manage-access-to-resources.md).

Rete CDN di Azure non rispetta eventuali restrizioni aggiunte al token di firma di accesso condiviso. Ad esempio, tutti i token di firma di accesso condiviso scadono. È comunque possibile accedere al contenuto con una firma di accesso condiviso scaduta fino a quando il contenuto non viene eliminato dai nodi perimetrali della rete CDN di Azure. È possibile controllare l'intervallo di tempo per cui i dati vengono memorizzati nella cache nella rete CDN di Azure impostando l'intestazione della risposta della cache. Per altre informazioni vedere [Gestire la scadenza dei BLOB di Archiviazione di Azure nella rete CDN di Azure](../../cdn/cdn-manage-expiration-of-blob-content.md).

Se si creano due o più URL di firma di accesso condiviso per lo stesso endpoint BLOB, è consigliabile attivare la memorizzazione nella cache delle stringhe di query per la rete CDN di Azure. In questo modo ogni URL viene considerato entità univoca in Azure. Per altre informazioni, vedere [Controllare il comportamento di memorizzazione nella cache della rete CDN di Azure con stringhe di query](../../cdn/cdn-query-string.md).

## <a name="http-to-https-redirection"></a>Reindirizzamento del traffico HTTP a HTTPS

È possibile reindirizzare il traffico da HTTP a HTTPS. A questo scopo è necessario usare l'offerta Premium di Rete CDN di Azure di Verizon. [Eseguire l'override del comportamento HTTP con il motore regole di Rete CDN di Azure](../../cdn/cdn-rules-engine.md) applicando la regola seguente:

![Regola di reindirizzamento del traffico HTTP a HTTPS](./media/storage-https-custom-domain-cdn/redirect-to-https.png)

*Cdn-endpoint-name*, che è possibile selezionare nell'elenco a discesa, fa riferimento al nome configurato per l'endpoint della rete CDN di Azure. *Origin-path* fa riferimento al percorso all'interno dell'account di archiviazione di origine in cui è archiviato il contenuto statico. Se si ospita tutto il contenuto statico in un singolo contenitore, sostituire *origin-path* con il nome del contenitore.

Per un approfondimento delle regole, vedere [Funzionalità del motore regole della rete CDN di Azure](../../cdn/cdn-rules-engine-reference-features.md).

## <a name="pricing-and-billing"></a>Prezzi e fatturazione

Quando si accede ai BLOB tramite Rete CDN di Azure, vengono applicati i [prezzi dell'archiviazione BLOB](https://azure.microsoft.com/pricing/details/storage/blobs/) per il traffico tra i nodi perimetrali e l'origine (archiviazione BLOB). Vengono applicati i [prezzi di Rete CDN di Azure](https://azure.microsoft.com/pricing/details/cdn/) per i dati a cui si accede dai nodi perimetrali.

Si supponga, ad esempio, di avere un account di archiviazione nell'area Stati Uniti occidentali a cui si accede con Rete CDN di Azure. Se un utente nel Regno Unito tenta di accedere a uno dei BLOB in questo account di archiviazione tramite Rete CDN di Azure, viene controllato prima il nodo perimetrale più vicino al Regno Unito. Se Azure trova il BLOB, accede a una copia e usa i prezzi di Rete CDN di Azure perché Rete CDN di Azure accede al BLOB. Se Azure non trova il BLOB, copia il BLOB nel nodo perimetrale. Questa azione comporta l'applicazione dei costi di uscita e di transazione in base ai prezzi di archiviazione BLOB. Azure accede quindi al file nel nodo perimetrale, con conseguente fatturazione di Rete CDN di Azure.

Nella [pagina dei prezzi di Rete CDN di Azure](https://azure.microsoft.com/pricing/details/cdn/), il supporto HTTPS per i nomi di domini personalizzati è disponibile per Rete CDN di Azure solo tramite i prodotti Standard e Premium Verizon.

## <a name="next-steps"></a>Passaggi successivi

* [Configurare un nome di dominio completo per l'endpoint di archiviazione BLOB](storage-custom-domain-name.md)
* [Hosting di siti Web statici in Archiviazione di Azure](storage-blob-static-website.md)
