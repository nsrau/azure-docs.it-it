---
title: Hosting di siti Web statici in Archiviazione di Azure
description: Hosting di siti Web statici in Archiviazione di Azure che fornisce una soluzione economica e scalabile per l'hosting di applicazioni Web moderne.
author: normesta
ms.service: storage
ms.topic: how-to
ms.author: normesta
ms.reviewer: dineshm
ms.date: 09/04/2020
ms.subservice: blobs
ms.custom: devx-track-js
ms.openlocfilehash: 27ea88ad4d349d6a7aedd4e1e3bc8dc804683292
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91326083"
---
# <a name="static-website-hosting-in-azure-storage"></a>Hosting di siti Web statici in Archiviazione di Azure

È possibile gestire contenuto statico (file di immagine, HTML, CSS e JavaScript) direttamente da un contenitore di archiviazione denominato *$web*. L'hosting del contenuto in Archiviazione di Azure consente di usare architetture serverless che includono [Funzioni di Azure](/azure/azure-functions/functions-overview) e altri servizi di piattaforma distribuita come servizio (PaaS). L'hosting di siti web statici di archiviazione di Azure è un'ottima opzione nei casi in cui non è necessario un server Web per il rendering del contenuto.

App [Web statiche del servizio app](https://azure.microsoft.com/services/app-service/static/) è un'ottima alternativa all'hosting di siti web statici di archiviazione di Azure ed è anche appropriata nei casi in cui non è necessario un server Web per il rendering del contenuto. App Web statiche del servizio app offre un flusso di lavoro di integrazione continua e recapito continuo completamente gestito dall'origine GitHub alla distribuzione globale.

Se è necessario un server Web per il rendering del contenuto, è possibile usare [app Azure servizio](https://azure.microsoft.com/services/app-service/).

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

> [!NOTE]
> Assicurarsi di creare un account di archiviazione standard per utilizzo generico V2. I siti Web statici non sono disponibili in nessun altro tipo di account di archiviazione.

## <a name="setting-up-a-static-website"></a>Configurazione di un sito Web statico

L'hosting di siti Web statici è una funzionalità che deve essere abilitata nell'account di archiviazione.

Quando si abilita l'hosting di siti Web statici, è possibile selezionare il nome del file predefinito e, facoltativamente, fornire un percorso a una pagina 404 personalizzata. Se un contenitore di archiviazione BLOB denominato **$web** non esiste già nell'account, ne viene creato uno automaticamente. Aggiungere i file del sito a questo contenitore.

Per istruzioni dettagliate, vedere [Ospitare un sito Web statico in Archiviazione di Azure](storage-blob-static-website-how-to.md).

![Metrica Dati in uscita delle metriche dei siti Web statici di Archiviazione di Azure](./media/storage-blob-static-website/storage-blob-static-website-blob-container.png)

I file nel contenitore **$web** contenitore fanno distinzione tra maiuscole e minuscole, vengono gestiti tramite richieste di accesso anonimo e sono disponibili solo tramite operazioni di lettura.

## <a name="uploading-content"></a>Caricamento di contenuto

È possibile usare uno di questi strumenti per caricare contenuto nel contenitore **$web**:

> [!div class="checklist"]
> * [Interfaccia della riga di comando di Azure](storage-blob-static-website-how-to.md?tabs=azure-cli)
> * [Modulo di Azure PowerShell](storage-blob-static-website-how-to.md?tabs=azure-powershell)
> * [AzCopy](../common/storage-use-azcopy-v10.md)
> * [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)
> * [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/)
> * [Estensione di Visual Studio Code](/azure/developer/javascript/tutorial-vscode-static-website-node-01)

## <a name="viewing-content"></a>Visualizzazione di contenuto

Gli utenti possono visualizzare il contenuto del sito da un browser usando l'URL pubblico del sito Web. L'URL è reperibile mediante il portale di Azure, l'interfaccia della riga di comando di Azure o PowerShell. Vedere [Specificare l'URL del sito Web](storage-blob-static-website-how-to.md#portal-find-url).

Se il server restituisce un errore 404 e non è stato specificato un documento di errore quando è stato abilitato il sito Web, all'utente viene restituita una pagina 404 predefinita.

> [!NOTE]
> [CORS](https://docs.microsoft.com/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) non è supportato con un sito Web statico.

### <a name="regional-codes"></a>Codici internazionali

L'URL del sito contiene un codice internazionale. Ad esempio, l'URL `https://contosoblobaccount.z22.web.core.windows.net/` contiene il codice internazionale `z22`.

Sebbene il codice debba rimanere nell'URL, è solo per uso interno e non è necessario usare tale codice in altro modo.

Il documento di indice specificato quando si abilita l'hosting di siti Web statici viene visualizzato quando gli utenti aprono il sito e non specificano un file specifico (ad esempio: `https://contosoblobaccount.z22.web.core.windows.net`).

### <a name="secondary-endpoints"></a>Endpoint secondari

Se si configura la [ridondanza in un'area secondaria](../common/storage-redundancy.md#redundancy-in-a-secondary-region), è anche possibile accedere al contenuto del sito Web usando un endpoint secondario. Poiché i dati vengono replicati in aree secondarie in modo asincrono, i file disponibili nell'endpoint secondario non sono sempre sincronizzati con i file disponibili nell'endpoint primario.

## <a name="impact-of-the-setting-the-public-access-level-of-the-web-container"></a>Impatto dell'impostazione del livello di accesso pubblico del contenitore Web

È possibile modificare il livello di accesso pubblico del contenitore **$web**, ma l'azione non ha alcun effetto sull'endpoint del sito Web statico primario, perché questi file vengono gestiti tramite richieste di accesso anonimo, ovvero accesso pubblico (di sola lettura) a tutti i file.

La screenshot seguente mostra l'impostazione del livello di accesso pubblico nel portale di Azure:

![Screenshot che illustra come impostare il livello di accesso pubblico nel portale](./media/anonymous-read-access-configure/configure-public-access-container.png)

Sebbene l'endpoint del sito Web statico primario non sia interessato, una modifica al livello di accesso pubblico influisce sull'endpoint del servizio BLOB primario.

Ad esempio, se si modifica il livello di accesso pubblico del contenitore **$web** contenitore da **Privato (nessun accesso anonimo)** a **BLOB (accesso in lettura anonimo solo per i BLOB)** , il livello di accesso pubblico all'endpoint del sito Web statico primario `https://contosoblobaccount.z22.web.core.windows.net/index.html` non cambia.

Tuttavia, l'accesso pubblico all'endpoint del servizio BLOB primario `https://contosoblobaccount.blob.core.windows.net/$web/index.html` passa da privato a pubblico. Ora gli utenti possono aprire il file usando uno di questi due endpoint.

La disabilitazione dell'accesso pubblico in un account di archiviazione non influisce sui siti web statici ospitati in tale account di archiviazione. Per altre informazioni, vedere [configurare l'accesso in lettura pubblico anonimo per contenitori e BLOB](anonymous-read-access-configure.md).

## <a name="mapping-a-custom-domain-to-a-static-website-url"></a>Mapping di un dominio personalizzato a un URL di un sito Web statico

È possibile rendere disponibile il sito Web statico mediante un dominio personalizzato.

L'abilitazione dell'accesso HTTP per il dominio personalizzato è più semplice, perché Archiviazione di Azure la supporta in modo nativo. Per abilitare HTTPS, è necessario usare Rete CDN di Azure perché Archiviazione di Azure non supporta ancora in modo nativo HTTPS con domini personalizzati. Vedere [Eseguire il mapping di un dominio personalizzato a un endpoint di Archiviazione BLOB di Azure](storage-custom-domain-name.md) per indicazioni dettagliate.

Se l'account di archiviazione è configurato per [richiedere il trasferimento sicuro](../common/storage-require-secure-transfer.md) su HTTPS, gli utenti devono usare l'endpoint HTTPS.

> [!TIP]
> Valutare la possibilità di ospitare il dominio in Azure. Per altre informazioni, vedere [Ospitare un dominio in DNS di Azure](../../dns/dns-delegate-domain-azure-dns.md).

## <a name="adding-http-headers"></a>Aggiunta di intestazioni HTTP

Non è possibile configurare le intestazioni come parte della funzionalità dei siti Web statici. Tuttavia, è possibile usare Rete CDN di Azure per aggiungere intestazioni e accodare (o sovrascrivere) valori di intestazione. Vedere [Informazioni di riferimento sul motore regole standard per la rete CDN di Azure](https://docs.microsoft.com/azure/cdn/cdn-standard-rules-engine-reference).

Per usare intestazioni al fine di controllare la memorizzazione nella cache, vedere [Controllare il comportamento di memorizzazione nella cache della rete CDN di Azure con regole](https://docs.microsoft.com/azure/cdn/cdn-caching-rules).

## <a name="multi-region-website-hosting"></a>Hosting di siti Web in più aree

Se si prevede di ospitare un sito Web in più aree geografiche, è consigliabile usare una rete per la [distribuzione di contenuti](https://docs.microsoft.com/azure/cdn/) per la memorizzazione nella cache a livello di area. Usare lo [sportello anteriore di Azure](https://docs.microsoft.com/azure/frontdoor/) se si vuole gestire contenuti diversi in ogni area. Fornisce anche funzionalità di failover. [Gestione traffico di Azure](https://docs.microsoft.com/azure/traffic-manager/) non è consigliato se si prevede di usare un dominio personalizzato. Possono verificarsi problemi a causa del modo in cui archiviazione di Azure verifica i nomi di dominio personalizzati.


## <a name="pricing"></a>Prezzi

È possibile abilitare l'hosting di siti Web statici gratuitamente. Vengono addebitati solo l'archiviazione BLOB usata dal sito e i costi operativi. Per altri dettagli sui prezzi di Archiviazione BLOB di Azure, consultare la [pagina dei prezzi di Archiviazione BLOB di Azure](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="metrics"></a>Metriche

È possibile abilitare le metriche nelle pagine dei siti Web statici. Dopo che le metriche sono state abilitate, le statistiche sul traffico per i file nel contenitore **$web** vengono riportate nel dashboard delle metriche.

Per abilitare le metriche nelle pagine dei siti Web statici, vedere [Abilitare le metriche nelle pagine dei siti Web statici](storage-blob-static-website-how-to.md#metrics).

## <a name="next-steps"></a>Passaggi successivi

* [Hosting di un sito Web statico in Archiviazione di Azure](storage-blob-static-website-how-to.md)
* [Eseguire il mapping di un dominio personalizzato a un endpoint di Archiviazione BLOB di Azure](storage-custom-domain-name.md)
* [Funzioni di Azure](/azure/azure-functions/functions-overview)
* [Servizio app di Azure](/azure/app-service/overview)
* [Compilare la prima app Web serverless](https://docs.microsoft.com/azure/functions/tutorial-static-website-serverless-api-with-database)
* [Esercitazione: Ospitare un dominio in DNS di Azure](../../dns/dns-delegate-domain-azure-dns.md)
