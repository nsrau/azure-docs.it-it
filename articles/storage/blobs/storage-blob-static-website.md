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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79370492"
---
# <a name="static-website-hosting-in-azure-storage"></a>Hosting di siti Web statici in Archiviazione di Azure

È possibile gestire il contenuto statico (file HTML, CSS, JavaScript e di immagine) direttamente da un contenitore di archiviazione denominato *$Web*. L'hosting del contenuto in archiviazione di Azure consente di usare architetture senza server che includono [funzioni di Azure](/azure/azure-functions/functions-overview) e altri servizi di piattaforma distribuita come servizio (PaaS).

[!INCLUDE [updated-for-az](../../../includes/storage-data-lake-gen2-support.md)]

> [!NOTE]
> Se il sito dipende dal codice sul lato server, usare invece [app Azure servizio](/azure/app-service/overview) .

## <a name="setting-up-a-static-website"></a>Configurazione di un sito Web statico

L'hosting di siti web statici è una funzionalità che è necessario abilitare nell'account di archiviazione.

Per abilitare l'hosting di siti web statici, selezionare il nome del file predefinito e, facoltativamente, specificare un percorso per una pagina personalizzata di 404. Se un contenitore di archiviazione BLOB denominato **$Web** non esiste già nell'account, ne viene creato uno. Aggiungere i file del sito a questo contenitore.

Per istruzioni dettagliate, vedere [ospitare un sito Web statico in archiviazione di Azure](storage-blob-static-website-how-to.md).

![Metrica Dati in uscita delle metriche dei siti Web statici di Archiviazione di Azure](./media/storage-blob-static-website/storage-blob-static-website-blob-container.png)

I file nel contenitore di **$Web** fanno distinzione tra maiuscole e minuscole, vengono serviti tramite richieste di accesso anonime e sono disponibili solo tramite le operazioni di lettura.

## <a name="uploading-content"></a>Caricamento del contenuto

È possibile usare uno di questi strumenti per caricare il contenuto nel contenitore **$Web** :

> [!div class="checklist"]
> * [Interfaccia della riga di comando di Azure](storage-blob-static-website-how-to.md#cli)
> * [Modulo di Azure PowerShell](storage-blob-static-website-how-to.md#powershell)
> * [AzCopy](../common/storage-use-azcopy-v10.md)
> * [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)
> * [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/)
> * [Estensione Visual Studio Code](/azure/javascript/tutorial-vscode-static-website-node-01)

## <a name="viewing-content"></a>Visualizzazione del contenuto

Gli utenti possono visualizzare il contenuto del sito da un browser usando l'URL pubblico del sito Web. È possibile trovare l'URL usando il portale di Azure, l'interfaccia della riga di comando di Azure o PowerShell. Usare questa tabella come riferimento.

|Strumento| Indicazioni |
|----|----|
|**Azure portal** | [Trovare l'URL del sito Web usando il portale di Azure](storage-blob-static-website-how-to.md#portal-find-url) |
|**Interfaccia della riga di comando di Azure** | [Trovare l'URL del sito Web usando l'interfaccia della riga di comando di Azure](storage-blob-static-website-how-to.md#cli-find-url) |
|**Modulo di Azure PowerShell** | [Trovare l'URL del sito Web usando PowerShell](storage-blob-static-website-how-to.md#powershell-find-url) |

L'URL del sito contiene un codice regionale. Ad esempio, l' `https://contosoblobaccount.z22.web.core.windows.net/` URL contiene codice `z22`regionale.

Sebbene il codice debba rimanere nell'URL, è solo per uso interno e non sarà necessario usare tale codice in altro modo.

Il documento di indice specificato quando si Abilita l'hosting di siti web statici viene visualizzato quando gli utenti aprono il sito e non specificano un file `https://contosoblobaccount.z22.web.core.windows.net`specifico (ad esempio:).  

Se il server restituisce un errore 404 e non è stato specificato un documento di errore quando è stato abilitato il sito Web, all'utente viene restituita una pagina predefinita di 404.

> [!NOTE]
> [CORS](https://docs.microsoft.com/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) non è supportato con il sito Web statico.

## <a name="impact-of-the-setting-the-public-access-level-of-the-web-container"></a>Effetti dell'impostazione del livello di accesso pubblico del contenitore Web

È possibile modificare il livello di accesso pubblico del contenitore **$Web** , ma ciò non ha alcun effetto sull'endpoint del sito Web statico primario perché questi file vengono serviti tramite richieste di accesso anonimo. Questo significa che l'accesso pubblico (di sola lettura) a tutti i file.

Lo screenshot seguente mostra l'impostazione del livello di accesso pubblico nel portale di Azure:

![Screenshot che illustra come impostare il livello di accesso pubblico nel portale](./media/storage-manage-access-to-resources/storage-manage-access-to-resources-0.png)

Sebbene l'endpoint del sito Web statico primario non sia interessato, una modifica al livello di accesso pubblico influisce sull'endpoint del servizio BLOB primario.

Ad esempio, se si modifica il livello di accesso pubblico del contenitore **$Web** da **privato (nessun accesso anonimo)** a **BLOB (accesso in lettura anonimo solo per BLOB)**, il livello di accesso pubblico all'endpoint `https://contosoblobaccount.z22.web.core.windows.net/index.html` del sito Web statico primario non cambia.

Tuttavia, l'accesso pubblico all'endpoint `https://contosoblobaccount.blob.core.windows.net/$web/index.html` di servizio BLOB primario passa da privato a pubblico. Ora gli utenti possono aprire il file usando uno di questi due endpoint.

## <a name="mapping-a-custom-domain-to-a-static-website-url"></a>Mapping di un dominio personalizzato a un URL del sito Web statico

È possibile rendere disponibile il sito Web statico tramite un dominio personalizzato. 

L'abilitazione dell'accesso HTTP per il dominio personalizzato è più semplice, perché archiviazione di Azure la supporta in modo nativo. Per abilitare HTTPS, è necessario usare la rete CDN di Azure perché archiviazione di Azure non supporta ancora in modo nativo HTTPS con domini personalizzati. per istruzioni dettagliate, vedere [eseguire il mapping di un dominio personalizzato a un endpoint di archiviazione BLOB di Azure](storage-custom-domain-name.md) .

Se l'account di archiviazione è configurato per [richiedere il trasferimento sicuro](../common/storage-require-secure-transfer.md) tramite HTTPS, gli utenti devono usare l'endpoint HTTPS. 

> [!TIP]
> Si consiglia di ospitare il dominio in Azure. Per altre informazioni, vedere [ospitare il dominio in DNS di Azure](../../dns/dns-delegate-domain-azure-dns.md).

## <a name="adding-http-headers"></a>Aggiunta di intestazioni HTTP

Non è possibile configurare le intestazioni come parte della funzionalità del sito Web statico. Tuttavia, è possibile usare la rete CDN di Azure per aggiungere intestazioni e aggiungere (o sovrascrivere) valori di intestazione. Vedere [le informazioni di riferimento sul motore regole standard per la rete CDN di Azure](https://docs.microsoft.com/azure/cdn/cdn-standard-rules-engine-reference).

Se si vogliono usare le intestazioni per controllare la memorizzazione nella cache, vedere [controllare il comportamento di memorizzazione nella cache della rete CDN di Azure con le regole di Caching](https://docs.microsoft.com/azure/cdn/cdn-caching-rules).

## <a name="pricing"></a>Prezzi

È possibile abilitare l'hosting di siti web statici gratuitamente. Viene addebitato solo l'archiviazione BLOB utilizzata dal sito e i costi operativi. Per altri dettagli sui prezzi di Archiviazione BLOB di Azure, consultare la [pagina dei prezzi di Archiviazione BLOB di Azure](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="metrics"></a>Metriche

È possibile abilitare le metriche nelle pagine statiche del sito Web. Dopo aver abilitato le metriche, le statistiche sul traffico sui file nel contenitore **$Web** vengono segnalate nel dashboard delle metriche.

Per abilitare le metriche nelle pagine del sito Web statico, vedere [abilitare le metriche nelle pagine del sito Web statico](storage-blob-static-website-how-to.md#metrics).

## <a name="next-steps"></a>Passaggi successivi

* [Ospitare un sito Web statico in archiviazione di Azure](storage-blob-static-website-how-to.md)
* [Eseguire il mapping di un dominio personalizzato a un endpoint di Archiviazione BLOB di Azure](storage-custom-domain-name.md)
* [Funzioni di Azure](/azure/azure-functions/functions-overview)
* [Servizio app di Azure](/azure/app-service/overview)
* [Compilare la prima app Web serverless](https://docs.microsoft.com/azure/functions/tutorial-static-website-serverless-api-with-database)
* [Esercitazione: Ospitare un dominio in DNS di Azure](../../dns/dns-delegate-domain-azure-dns.md)
