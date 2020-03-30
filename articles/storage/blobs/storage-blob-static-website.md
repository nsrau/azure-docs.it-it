---
title: Hosting di siti Web statici in Archiviazione di Azure
description: Hosting di siti Web statici in Archiviazione di Azure che fornisce una soluzione economica e scalabile per l'hosting di applicazioni Web moderne.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.author: normesta
ms.reviewer: dineshm
ms.date: 05/29/2019
ms.subservice: blobs
ms.openlocfilehash: 848fd89444281d82d6d0d1bfc4df15d499c09ee0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79370492"
---
# <a name="static-website-hosting-in-azure-storage"></a>Hosting di siti Web statici in Archiviazione di Azure

Potete gestire contenuto statico (HTML, CSS, JavaScript e file di immagine) direttamente da un contenitore di archiviazione denominato *$web*. L'hosting del contenuto in Archiviazione di Azure consente di usare architetture senza server che includono [servizi Di Azure E](/azure/azure-functions/functions-overview) Platform as a Service (PaaS).

[!INCLUDE [updated-for-az](../../../includes/storage-data-lake-gen2-support.md)]

> [!NOTE]
> Se il sito dipende dal codice lato server, usare invece Il servizio app di [Azure.If](/azure/app-service/overview) your site depends on server-side code, use Azure App Service instead.

## <a name="setting-up-a-static-website"></a>Impostazione di un sito Web statico

L'hosting statico di siti Web è una funzionalità che è necessario abilitare nell'account di archiviazione.

Per abilitare l'hosting statico del sito Web, selezionare il nome del file predefinito e quindi specificare facoltativamente un percorso a una pagina 404 personalizzata. Se nell'account non esiste già un contenitore di archiviazione BLOB denominato **$web** non esiste già, ne viene creato uno automaticamente. Aggiungere i file del sito a questo contenitore.

Per istruzioni dettagliate, vedere [Ospitare un sito Web statico in Archiviazione di Azure.For](storage-blob-static-website-how-to.md)step-by-step guidance, see Host a static website in Azure Storage .

![Metrica Dati in uscita delle metriche dei siti Web statici di Archiviazione di Azure](./media/storage-blob-static-website/storage-blob-static-website-blob-container.png)

I file nel contenitore **$web** fanno distinzione tra maiuscole e minuscole, vengono serviti tramite richieste di accesso anonimo e sono disponibili solo tramite operazioni di lettura.

## <a name="uploading-content"></a>Caricamento di contenuti

È possibile utilizzare uno di questi strumenti per caricare il contenuto nel contenitore **$web:**

> [!div class="checklist"]
> * [Interfaccia della riga di comando di AzureAzure](storage-blob-static-website-how-to.md#cli)
> * [Modulo di Azure PowerShell](storage-blob-static-website-how-to.md#powershell)
> * [Copia Azzurra](../common/storage-use-azcopy-v10.md)
> * [Esplora archivi di AzureAzure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)
> * [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/)
> * [Estensione di codice di Visual StudioVisual Studio Code extension](/azure/javascript/tutorial-vscode-static-website-node-01)

## <a name="viewing-content"></a>Visualizzazione del contenuto

Gli utenti possono visualizzare il contenuto del sito da un browser utilizzando l'URL pubblico del sito Web. È possibile trovare l'URL usando il portale di Azure, l'interfaccia della riga di comando di Azure o PowerShell.You can find the URL by using the Azure portal, Azure CLI, or PowerShell. Usare questa tabella come riferimento.

|Strumento| Materiale sussidiario |
|----|----|
|**Portale di Azure** | [Trovare l'URL del sito Web tramite il portale di AzureFind the website url by using the Azure portal](storage-blob-static-website-how-to.md#portal-find-url) |
|**Interfaccia della riga di comando di AzureAzure** | [Trovare l'URL del sito Web tramite l'interfaccia della riga di comando di AzureFind the website URL by using the Azure CLI](storage-blob-static-website-how-to.md#cli-find-url) |
|**Modulo di Azure PowerShell** | [Trovare l'URL del sito Web tramite PowerShellFind the website url by using PowerShell](storage-blob-static-website-how-to.md#powershell-find-url) |

L'URL del sito contiene un codice regionale. Ad esempio, `https://contosoblobaccount.z22.web.core.windows.net/` l'URL contiene il codice `z22`regionale .

Anche se tale codice deve rimanere nell'URL, è solo per uso interno e non sarà necessario utilizzare tale codice in qualsiasi altro modo.

Il documento di indice specificato quando si abilita l'hosting di siti Web statici viene `https://contosoblobaccount.z22.web.core.windows.net`visualizzato quando gli utenti aprono il sito e non specificano un file specifico (ad esempio: ).  

Se il server restituisce un errore 404 e non è stato specificato un documento di errore quando è stato abilitato il sito Web, viene restituita una pagina 404 predefinita all'utente.

> [!NOTE]
> [CORS](https://docs.microsoft.com/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) non è supportato con il sito Web statico.

## <a name="impact-of-the-setting-the-public-access-level-of-the-web-container"></a>Impatto dell'impostazione del livello di accesso pubblico del contenitore Web

È possibile modificare il livello di accesso pubblico del contenitore **$web,** ma ciò non ha alcun impatto sull'endpoint del sito Web statico primario perché questi file vengono serviti tramite richieste di accesso anonimo. Ciò significa l'accesso pubblico (di sola lettura) a tutti i file.

La schermata seguente mostra l'impostazione del livello di accesso pubblico nel portale di Azure:The following screenshot shows the public access level setting in the Azure portal:

![Screenshot che mostra come impostare il livello di accesso pubblico nel portale](./media/storage-manage-access-to-resources/storage-manage-access-to-resources-0.png)

Mentre l'endpoint del sito Web statico primario non è interessato, una modifica al livello di accesso pubblico influisce sull'endpoint del servizio BLOB primario.

Ad esempio, se si modifica il livello di accesso pubblico del contenitore **$web** da **Private (nessun accesso anonimo)** a **Blob (accesso in lettura anonimo solo per i BLOB),** il livello di accesso pubblico all'endpoint `https://contosoblobaccount.z22.web.core.windows.net/index.html` del sito Web statico primario non cambia.

Tuttavia, l'accesso pubblico all'endpoint `https://contosoblobaccount.blob.core.windows.net/$web/index.html` del servizio BLOB primario cambia da privato a pubblico. Ora gli utenti possono aprire il file utilizzando uno di questi due endpoint.

## <a name="mapping-a-custom-domain-to-a-static-website-url"></a>Mapping di un dominio personalizzato all'URL di un sito Web statico

È possibile rendere disponibile il sito Web statico tramite un dominio personalizzato. 

È più semplice abilitare l'accesso HTTP per il dominio personalizzato, perché Archiviazione di Azure lo supporta in modo nativo. Per abilitare HTTPS, è necessario usare la rete CDN di Azure perché Archiviazione di Azure non supporta ancora in modo nativo HTTPS con domini personalizzati. per istruzioni dettagliate, vedere Eseguire il mapping di un dominio personalizzato a un endpoint di Archiviazione BLOB di Azure.See Map a custom domain [to an Azure Blob Storage endpoint](storage-custom-domain-name.md) for step-by-step guidance.

Se l'account di archiviazione è configurato per richiedere il [trasferimento sicuro](../common/storage-require-secure-transfer.md) su HTTPS, gli utenti devono usare l'endpoint HTTPS. 

> [!TIP]
> È consigliabile ospitare il dominio in Azure.Consider hosting your domain on Azure. Per altre informazioni, vedere Ospitare il dominio in DNS di Azure.For more [information,](../../dns/dns-delegate-domain-azure-dns.md)see Host your domain in Azure DNS .

## <a name="adding-http-headers"></a>Aggiunta di intestazioni HTTP

Non è possibile configurare le intestazioni come parte della funzionalità statica del sito Web. Tuttavia, è possibile usare la rete CDN di Azure per aggiungere intestazioni e aggiungere (o sovrascrivere) valori di intestazione. Vedere Informazioni di riferimento sul motore regole standard per la rete CDN di [Azure.See Standard rules engine reference for Azure CDN.](https://docs.microsoft.com/azure/cdn/cdn-standard-rules-engine-reference)

Se si vuole usare le intestazioni per controllare la memorizzazione nella cache, vedere Controllare il comportamento della memorizzazione nella cache della rete CDN di [Azure con le regole di memorizzazione nella cache.](https://docs.microsoft.com/azure/cdn/cdn-caching-rules)

## <a name="pricing"></a>Prezzi

È possibile abilitare l'hosting statico di siti Web gratuitamente. Viene addebitato solo l'archiviazione BLOB utilizzata dal sito e i costi operativi. Per altri dettagli sui prezzi di Archiviazione BLOB di Azure, consultare la [pagina dei prezzi di Archiviazione BLOB di Azure](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="metrics"></a>Metriche

È possibile abilitare le metriche nelle pagine statiche del sito Web. Dopo aver abilitato le metriche, le statistiche sul traffico relative ai file nel contenitore **$web** vengono segnalate nel dashboard delle metriche.

Per abilitare le metriche nelle pagine statiche del sito Web, vedere [Abilitare le metriche nelle pagine statiche del sito Web.](storage-blob-static-website-how-to.md#metrics)

## <a name="next-steps"></a>Passaggi successivi

* [Ospitare un sito Web statico in Archiviazione di AzureHost a static website in Azure Storage](storage-blob-static-website-how-to.md)
* [Eseguire il mapping di un dominio personalizzato a un endpoint di Archiviazione BLOB di Azure](storage-custom-domain-name.md)
* [Funzioni di AzureAzure Functions](/azure/azure-functions/functions-overview)
* [Servizio app di Azure](/azure/app-service/overview)
* [Compilare la prima app Web serverless](https://docs.microsoft.com/azure/functions/tutorial-static-website-serverless-api-with-database)
* [Esercitazione: Ospitare un dominio in DNS di Azure](../../dns/dns-delegate-domain-azure-dns.md)
