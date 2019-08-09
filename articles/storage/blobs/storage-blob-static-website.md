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
ms.openlocfilehash: 9a751956f73ca4a88545e034a32d699c0766dd1d
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/08/2019
ms.locfileid: "68855369"
---
# <a name="static-website-hosting-in-azure-storage"></a>Hosting di siti Web statici in Archiviazione di Azure

È possibile gestire il contenuto statico (file HTML, CSS, JavaScript e di immagine) direttamente da un contenitore di archiviazione denominato *$Web*. L'hosting del contenuto in archiviazione di Azure consente di usare architetture senza server che includono [funzioni di Azure](/azure/azure-functions/functions-overview) e altri servizi di piattaforma distribuita come servizio (PaaS).

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
> * [Estensione di Visual Studio Code](https://code.visualstudio.com/tutorials/static-website/getting-started)

## <a name="viewing-content"></a>Visualizzazione del contenuto

Gli utenti possono visualizzare il contenuto del sito da un browser usando l'URL pubblico del sito Web. È possibile trovare l'URL usando il portale di Azure, l'interfaccia della riga di comando di Azure o PowerShell. Usare questa tabella come riferimento.

|Strumento| Materiale sussidiario |
|----|----|
|**Portale di Azure** | [Trovare l'URL del sito Web usando il portale di Azure](storage-blob-static-website-how-to.md#portal-find-url) |
|**Interfaccia della riga di comando di Azure** | [Trovare l'URL del sito Web usando l'interfaccia della riga di comando di Azure](storage-blob-static-website-how-to.md#cli-find-url) |
|**Modulo di Azure PowerShell** | [Trovare l'URL del sito Web usando PowerShell](storage-blob-static-website-how-to.md#powershell-find-url) |

L'URL del sito contiene un codice regionale. Ad esempio, l' `https://contosoblobaccount.z22.web.core.windows.net/` URL contiene codice `z22`regionale.

Sebbene tale codice debba rimanere l'URL, è solo per uso interno e non sarà necessario usare tale codice in altro modo.

Il documento di indice specificato quando si Abilita l'hosting di siti web statici viene visualizzato quando gli utenti aprono il sito e non specificano un file `https://contosoblobaccount.z22.web.core.windows.net`specifico (ad esempio:).  

Se il server restituisce un errore 404 e non è stato specificato un documento di errore quando è stato abilitato il sito Web, all'utente viene restituita una pagina predefinita di 404.

## <a name="impact-of-the-setting-the-public-access-level-of-the-web-container"></a>Effetti dell'impostazione del livello di accesso pubblico del contenitore Web

È possibile modificare il livello di accesso pubblico del contenitore **$Web** , ma ciò non ha alcun effetto sull'endpoint del sito Web statico primario perché questi file vengono serviti tramite richieste di accesso anonimo. Questo significa che l'accesso pubblico (di sola lettura) a tutti i file.

Lo screenshot seguente mostra l'impostazione del livello di accesso pubblico nel portale di Azure:

![Screenshot che illustra come impostare il livello di accesso pubblico nel portale](./media/storage-manage-access-to-resources/storage-manage-access-to-resources-0.png)

Sebbene l'endpoint del sito Web statico primario non sia interessato, una modifica al livello di accesso pubblico influisce sull'endpoint del servizio BLOB primario.

Ad esempio, se si modifica il livello di accesso pubblico del contenitore **$Web** da **privato (nessun accesso anonimo)** a **BLOB (accesso in lettura anonimo solo per BLOB)** , il livello di accesso pubblico all'endpoint `https://contosoblobaccount.z22.web.core.windows.net/index.html`delsitoWebstaticoprimarionon cambia.

Tuttavia, l'accesso pubblico all'endpoint `https://contosoblobaccount.blob.core.windows.net/$web/index.html` di servizio BLOB primario passa da privato a pubblico. Ora gli utenti possono aprire il file usando uno di questi due endpoint.

## <a name="content-delivery-network-cdn-and-secure-socket-layer-ssl-support"></a>Rete per la distribuzione di contenuti (CDN) e supporto Secure Socket Layer (SSL)

Per rendere disponibili i file di siti web statici tramite il dominio personalizzato e HTTPS, vedere [uso della rete CDN di Azure per accedere ai BLOB con domini personalizzati tramite HTTPS](storage-https-custom-domain-cdn.md). Come parte di questo processo, è necessario puntare la rete CDN all'endpoint del *sito Web statico* primario anziché all'endpoint primario del *servizio BLOB* . Potrebbe essere necessario attendere alcuni minuti prima che il contenuto sia visibile perché la configurazione della rete CDN non è immediatamente eseguita.

Quando si aggiorna il sito Web statico, assicurarsi di cancellare il contenuto memorizzato nella cache nei server perimetrali della rete CDN ripulendo l'endpoint della rete CDN. Per altre informazioni, vedere [Ripulire un endpoint della rete CDN di Azure](../../cdn/cdn-purge-endpoint.md).

> [!NOTE]
> HTTPS è supportato in modo nativo tramite l'endpoint Web dell'account, quindi l'endpoint Web è accessibile tramite HTTP e HTTPS. Tuttavia, se l'account di archiviazione è configurato per richiedere il trasferimento sicuro tramite HTTPS, gli utenti devono usare l'endpoint HTTPS. Per altre informazioni, vedere [richiedere il trasferimento sicuro in archiviazione di Azure](../common/storage-require-secure-transfer.md).
>
> L'uso di domini personalizzati su HTTPS richiede l'uso della rete CDN di Azure in questo momento.

## <a name="custom-domain-names"></a>Nomi di dominio personalizzati

È possibile rendere disponibile il sito Web statico tramite un dominio personalizzato. Per altre informazioni, vedere [configurare un nome di dominio personalizzato per l'account di archiviazione di Azure](storage-custom-domain-name.md).

Per informazioni dettagliate sull'hosting del dominio in Azure, vedere [ospitare il dominio in DNS di Azure](../../dns/dns-delegate-domain-azure-dns.md).

## <a name="pricing"></a>Prezzi

È possibile abilitare l'hosting di siti web statici gratuitamente. Viene addebitato solo l'archiviazione BLOB utilizzata dal sito e i costi operativi. Per altri dettagli sui prezzi di Archiviazione BLOB di Azure, consultare la [pagina dei prezzi di Archiviazione BLOB di Azure](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="metrics"></a>metrics

È possibile abilitare le metriche nelle pagine statiche del sito Web. Dopo aver abilitato le metriche, le statistiche sul traffico sui file nel contenitore **$Web** vengono segnalate nel dashboard delle metriche.

Per abilitare le metriche nelle pagine del sito Web statico, vedere [abilitare le metriche nelle pagine del sito Web statico](storage-blob-static-website-how-to.md#metrics).

## <a name="next-steps"></a>Passaggi successivi

* [Ospitare un sito Web statico in archiviazione di Azure](storage-blob-static-website-how-to.md)
* [Usare la rete CDN di Azure per accedere ai BLOB con domini personalizzati tramite HTTPS](storage-https-custom-domain-cdn.md)
* [Configurare un nome di dominio personalizzato per l'endpoint BLOB o Web](storage-custom-domain-name.md)
* [Funzioni di Azure](/azure/azure-functions/functions-overview)
* [Servizio app di Azure](/azure/app-service/overview)
* [Compilare la prima app Web serverless](https://docs.microsoft.com/azure/functions/tutorial-static-website-serverless-api-with-database)
* [Esercitazione: Ospitare un dominio in DNS di Azure](../../dns/dns-delegate-domain-azure-dns.md)
