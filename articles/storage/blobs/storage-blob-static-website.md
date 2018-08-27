---
title: Hosting di siti Web statici in archiviazione di Azure (anteprima) | Microsoft Docs
description: Archiviazione di Azure offre ora l'hosting di siti Web statici (anteprima), fornendo una soluzione economica e scalabile per l'hosting di applicazioni Web moderne.
services: storage
author: MichaelHauss
ms.service: storage
ms.topic: article
ms.date: 08/17/18
ms.author: mihauss
ms.component: blobs
ms.openlocfilehash: 65a1cd85baf18ac1f0d193e7e6d6c3139919fb59
ms.sourcegitcommit: a62cbb539c056fe9fcd5108d0b63487bd149d5c3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/22/2018
ms.locfileid: "42617398"
---
# <a name="static-website-hosting-in-azure-storage-preview"></a>Hosting di siti Web statici in Archiviazione di Azure (anteprima)
Archiviazione di Azure offre ora l'hosting di siti Web statici (anteprima), consentendo di distribuire applicazioni Web moderne, economiche e scalabili in Azure. In un sito Web statico, le pagine Web hanno contenuti statici e JavaScript o altro codice lato client. Al contrario, i siti Web dinamici dipendono dal codice lato server e possono essere ospitati usando le [App Web di Azure](/azure/app-service/app-service-web-overview).

Poiché le distribuzioni si stanno orientando verso modelli elastici ed economici, la capacità di distribuire contenuti Web senza la necessità di gestione del server è essenziale. L'introduzione di hosting di siti Web statici in Archiviazione di Azure rende ciò possibile, con l'abilitazione delle funzionalità avanzate di back-end con architetture serverless sfruttando [Funzioni di Azure](/azure/azure-functions/functions-overview) e altri servizi PaaS.

## <a name="how-does-it-work"></a>Come funziona?
Quando si attivano i siti Web statici del proprio account di archiviazione, viene creato un nuovo endpoint del servizio web nel formato `<account-name>.<zone-name>.web.core.windows.net`.

L'endpoint del servizio Web consente l'accesso anonimo in lettura, restituisce sempre pagine HTML formattate in risposta a errori del servizio e consente solo operazioni di lettura degli oggetti. L'endpoint del servizio Web restituisce il documento di indicizzazione nella directory richiesta per la radice e tutte le sottodirectory. Quando il servizio di archiviazione restituisce un errore 404 l'endpoint Web restituisce un documento di errore personalizzato, se questo è stato configurato.

Il contenuto del sito Web statico si trova in un contenitore speciale denominato "$web". Come parte del processo di abilitazione, "$web" viene creato automaticamente se non esiste già. È possibile accedere al contenuto in "$web" nella radice dell'account usando l'endpoint Web. Ad esempio `https://contoso.z4.web.core.windows.net/` restituisce il documento di indicizzazione configurato per il sito Web, se un documento con questo nome è presente nella directory radice di $web.

Quando si carica contenuto nel sito Web, usare l'endpoint di archiviazione BLOB. Per caricare un oggetto BLOB denominato 'image.jpg' a cui è possibile accedere alla radice dell'account usare l'URL `https://contoso.blob.core.windows.net/$web/image.jpg` seguente. L'immagine caricata può essere visualizzata in un Web browser in corrispondenza dell'endpoint Web `https://contoso.z4.web.core.windows.net/image.jpg`.


## <a name="custom-domain-names"></a>Nomi di dominio personalizzati
È possibile usare un dominio personalizzato per ospitare il contenuto Web. Per come eseguire questa operazione, seguire le indicazioni in [Configurare un nome di dominio personalizzato per l'endpoint di Archiviazione di Azure](storage-custom-domain-name.md). Per accedere al sito Web ospitato in un nome di dominio personalizzato tramite HTTPS, vedere [Uso della rete CDN di Azure per accedere a BLOB con domini personalizzati tramite HTTPS](storage-https-custom-domain-cdn.md). Scegliere la rete CDN per l'endpoint Web invece che per l'endpoint BLOB e ricordare che la configurazione CDN non avviene immediatamente, pertanto potrebbe essere necessario attendere alcuni minuti prima che il contenuto sia visibile.

## <a name="pricing-and-billing"></a>Prezzi e fatturazione
L'hosting di siti Web statici viene fornito senza alcun costo aggiuntivo. Per altri dettagli sui prezzi di Archiviazione BLOB di Azure, consultare la [pagina dei prezzi di Archiviazione BLOB di Azure](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="quickstart"></a>Guida introduttiva
### <a name="azure-portal"></a>Portale di Azure
Se non è ancora avvenuta la [creazione di un account di archiviazione GPv2](../common/storage-quickstart-create-account.md) per avviare l'hosting dell'applicazione Web è possibile configurare la funzionalità tramite il portale di Azure, facendo clic su "Sito Web statico (anteprima)" in "Impostazioni" nella barra di spostamento a sinistra. Fera clic su "Abilitato" e immettere il nome del documento di indicizzazione e (facoltativamente) il percorso del documento di errore personalizzato.

![](media/storage-blob-static-website/storage-blob-static-website-portal-config.PNG)

Caricare gli asset Web nel contenitore "$web" creato come parte dell'abilitazione del sito Web statico. È possibile farlo direttamente nel portale di Azure oppure è possibile sfruttare [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) per caricare le strutture di directory complete. Assicurarsi di includere un documento di indice con il nome che è stato configurato. In questo esempio, il nome del documento è "index.html".

> [!NOTE]
> Il nome del documento fa distinzione tra maiuscole e minuscole e deve pertanto corrispondere esattamente al nome del file nell'archiviazione.

Infine, passare all'endpoint Web per testare il sito Web.

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure
Installare l'estensione di anteprima di archiviazione:

```azurecli-interactive
az extension add --name storage-preview
```
Abilitare la funzionalità:

```azurecli-interactive
az storage blob service-properties update --account-name <account-name> --static-website --404-document <error-doc-name> --index-document <index-doc-name>
```
Query per l'URL dell'endpoint Web:

```azurecli-interactive
az storage account show -n <account-name> -g <resource-group> --query "primaryEndpoints.web" --output tsv
```

Caricare oggetti nel contenitore $web:

```azurecli-interactive
az storage blob upload-batch -s deploy -d $web --account-name <account-name>
```

## <a name="faq"></a>Domande frequenti
**I siti Web statici sono disponibili per tutti i tipi di account di archiviazione?**  
No, l'hosting dei siti Web statici è disponibile solo negli account di archiviazione standard GPv2.

**Storage VNET e le regole del firewall sono supportati sul nuovo endpoint Web?**  
Sì, il nuovo endpoint Web rispetta le regole VNET e del firewall configurate per l'account di archiviazione.

**L'endpoint Web fa distinzione tra maiuscole e minuscole?**  
Sì, l'endpoint Web fa distinzione tra maiuscole e minuscole esattamente come l'endpoint blob. 

## <a name="next-steps"></a>Passaggi successivi
* [Usare la rete CDN di Azure per accedere ai BLOB con domini personalizzati tramite HTTPS](storage-https-custom-domain-cdn.md)
* [Configurare un nome di dominio personalizzato per l'endpoint BLOB o Web](storage-custom-domain-name.md)
* [Funzioni di Azure](/azure/azure-functions/functions-overview)
* [App Web di Azure](/azure/app-service/app-service-web-overview)
* [Compilare la prima app Web serverless](https://aka.ms/static-serverless-webapp)
