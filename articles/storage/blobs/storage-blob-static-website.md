---
title: Hosting di siti Web statici in Archiviazione di Azure
description: Hosting di siti Web statici in Archiviazione di Azure che fornisce una soluzione economica e scalabile per l'hosting di applicazioni Web moderne.
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.author: normesta
ms.reviewer: seguler
ms.date: 05/29/2019
ms.subservice: blobs
ms.openlocfilehash: 36cc8cebdb567cb9650ad1ad3baf72a0b5478247
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/31/2019
ms.locfileid: "66427968"
---
# <a name="static-website-hosting-in-azure-storage"></a>Hosting di siti Web statici in Archiviazione di Azure

È possibile rendere disponibile contenuto statico (HTML, CSS, JavaScript e i file di immagine) direttamente da un contenitore di archiviazione denominato *$web*. Ospita il contenuto in archiviazione di Azure consente di utilizzare le architetture senza server che includono [funzioni di Azure](/azure/azure-functions/functions-overview) e altri piattaforma distribuita come un servizio (PaaS) di servizi.

> [!NOTE]
> Se il sito dipende dal codice lato server, utilizzare [servizio App di Azure](/azure/app-service/overview) invece.

## <a name="setting-up-a-static-website"></a>Configurazione di un sito Web statico

Hosting di siti Web statici è una funzionalità che è necessario attivare l'account di archiviazione.

Per abilitare l'hosting di siti Web statici, selezionare il nome del file predefinito e quindi, facoltativamente, specificare un percorso a una pagina 404 personalizzata. Se un contenitore di archiviazione blob denominato **$web** non esiste già nell'account di, ne viene creata una. Aggiungere i file del sito in tale contenitore.

Per istruzioni dettagliate, vedere [ospitare un sito Web statico in archiviazione di Azure](storage-blob-static-website-how-to.md).

![Metrica Dati in uscita delle metriche dei siti Web statici di Archiviazione di Azure](./media/storage-blob-static-website/storage-blob-static-website-blob-container.png)

File con il **$web** contenitore sono tra maiuscole e minuscole, servite tramite le richieste di accesso anonimo e sono disponibili solo tramite operazioni di lettura.

## <a name="uploading-content"></a>Caricamento di contenuti

È possibile usare uno di questi strumenti per caricare il contenuto per il **$web** contenitore:

> [!div class="checklist"]
> * [Interfaccia della riga di comando di Azure](storage-blob-static-website-how-to.md#cli)
> * [Modulo di Azure PowerShell](storage-blob-static-website-how-to.md#powershell)
> * [AzCopy](../common/storage-use-azcopy-v10.md)
> * [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)
> * [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/)
> * [Estensione di Visual Studio Code](https://code.visualstudio.com/tutorials/static-website/getting-started)

## <a name="viewing-content"></a>Visualizzazione del contenuto

Gli utenti possono visualizzare il contenuto del sito da un browser usando l'URL del sito Web pubblico. È possibile trovare l'URL usando il portale di Azure, della riga di comando di Azure o PowerShell. Usare questa tabella come riferimento.

|Strumento| Materiale sussidiario |
|----|----|
|**Portale di Azure** | [Trovare l'URL del sito Web usando il portale di Azure](storage-blob-static-website-how-to.md#portal-find-url) |
|**Interfaccia della riga di comando di Azure** | [Trovare l'URL del sito Web tramite la CLI di Azure](storage-blob-static-website-how-to.md#cli-find-url) |
|**Modulo di Azure PowerShell** | [Trovare l'URL del sito Web con PowerShell](storage-blob-static-website-how-to.md#powershell-find-url) |

L'URL del sito contiene un codice a livello di area. Ad esempio l'URL `https://contosoblobaccount.z22.web.core.windows.net/` contiene il codice a livello di area `z22`.

Mentre tale codice deve rimanere all'URL, è solo per uso interno e non sarà necessario usare tale codice in qualsiasi altro modo.

Il documento di indice specificato quando si attiva l'hosting di siti Web statici, viene visualizzato quando gli utenti di aprire il sito e non specificano un file specifico (ad esempio: `https://contosoblobaccount.z22.web.core.windows.net`).  

Se il server restituisce un errore 404 e non è stato specificato un documento di errore quando il sito Web è stata abilitata, viene restituita una pagina 404 predefinita all'utente.

## <a name="impact-of-the-setting-the-public-access-level-of-the-web-container"></a>Impatto dell'impostazione del livello di accesso pubblico del contenitore web

È possibile modificare il livello di accesso pubblico del **$web** contenitore, ma ciò non ha alcun impatto sull'endpoint sito Web statico primario perché questi file vengono serviti tramite le richieste di accesso anonimo. Ciò significa che accesso pubblico (sola lettura) a tutti i file.

Lo screenshot seguente illustra l'impostazione del livello di accesso pubblico nel portale di Azure:

![Screenshot che illustra come impostare a livello di accesso pubblico nel portale](./media/storage-manage-access-to-resources/storage-manage-access-to-resources-0.png)

Mentre l'endpoint del sito Web statico primario non è interessato, una modifica a livello di accesso pubblico influisce l'endpoint del servizio blob primario.

Ad esempio, se si modifica il livello di accesso pubblico del **$web** contenitore dal **privato (Nessun accesso anonimo)** a **Blob (accesso in lettura anonimo solo per BLOB)** , il a livello di accesso pubblico all'endpoint del sito Web statico primario `https://contosoblobaccount.z22.web.core.windows.net/index.html` non cambia.

Tuttavia, l'accesso pubblico a quello primario blob endpoint del servizio `https://contosoblobaccount.blob.core.windows.net/$web/index.html` modificare da privato a pubblico. A questo punto gli utenti possono aprire il file usando uno di questi due endpoint.

## <a name="content-delivery-network-cdn-and-secure-socket-layer-ssl-support"></a>Rete di distribuzione di contenuti (CDN) e il supporto di Secure Socket Layer (SSL)

Per rendere disponibile i file del sito Web statico tramite il dominio personalizzato e HTTPS, vedere [tramite la rete CDN di Azure per accedere ai BLOB con domini personalizzati tramite HTTPS](storage-https-custom-domain-cdn.md). Come parte di questo processo, è necessario scegliere la rete CDN per il database primario *sito Web statico* endpoint anziché il database primario *servizio blob* endpoint. Si potrebbe essere necessario attendere alcuni minuti prima che il contenuto è visibile durante la configurazione della rete CDN non viene eseguita immediatamente.

Quando si aggiorna il sito Web statico, assicurarsi di cancellare il contenuto memorizzato nella cache nei server perimetrali della rete CDN eliminando l'endpoint della rete CDN. Per altre informazioni, vedere [Ripulire un endpoint della rete CDN di Azure](../../cdn/cdn-purge-endpoint.md).

> [!NOTE]
> HTTPS è supportato in modo nativo tramite l'endpoint web dell'account, in modo che l'endpoint web è accessibile tramite HTTP e HTTPS. Tuttavia, se l'account di archiviazione è configurato per richiedere il trasferimento sicuro tramite HTTPS, gli utenti devono utilizzare l'endpoint HTTPS. Per altre informazioni, vedere [richiedere il trasferimento sicuro in archiviazione di Azure](../common/storage-require-secure-transfer.md).
>
> L'uso di domini personalizzati tramite HTTPS richiede l'uso della rete CDN di Azure in questo momento.

## <a name="custom-domain-names"></a>Nomi di dominio personalizzati

È possibile rendere disponibile il sito Web statico tramite un dominio personalizzato. Per altre informazioni, vedere [configurare un nome di dominio personalizzato per l'account di archiviazione di Azure](storage-custom-domain-name.md).

Per un esame approfondito che ospita il dominio in Azure, vedere [ospitare il dominio in DNS di Azure](../../dns/dns-delegate-domain-azure-dns.md).

## <a name="pricing"></a>Prezzi

È possibile abilitare l'hosting di siti Web statici senza oneri. La fatturazione solo per l'archiviazione di blob che usa il sito e i costi delle operazioni. Per altri dettagli sui prezzi di Archiviazione BLOB di Azure, consultare la [pagina dei prezzi di Archiviazione BLOB di Azure](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="metrics"></a>metrics

È possibile abilitare le metriche sulle pagine di sito Web statico. Dopo aver abilitato le metriche, il traffico delle statistiche sui file nei **$web** contenitore vengono segnalate nel dashboard di metriche.

Per abilitare le metriche nelle pagine del sito Web statico, vedere [abilitare le metriche sulle pagine di siti Web statici](storage-blob-static-website-how-to.md#metrics).

## <a name="next-steps"></a>Passaggi successivi

* [Ospitare un sito Web statico in archiviazione di Azure](storage-blob-static-website-how-to.md)
* [Usare la rete CDN di Azure per accedere ai BLOB con domini personalizzati tramite HTTPS](storage-https-custom-domain-cdn.md)
* [Configurare un nome di dominio personalizzato per l'endpoint BLOB o Web](storage-custom-domain-name.md)
* [Funzioni di Azure](/azure/azure-functions/functions-overview)
* [Servizio app di Azure](/azure/app-service/overview)
* [Compilare la prima app Web serverless](https://docs.microsoft.com/azure/functions/tutorial-static-website-serverless-api-with-database)
* [Esercitazione: Ospitare un dominio in DNS di Azure](../../dns/dns-delegate-domain-azure-dns.md)
