---
title: Hosting di siti Web statici in Archiviazione di Azure
description: Hosting di siti Web statici in Archiviazione di Azure che fornisce una soluzione economica e scalabile per l'hosting di applicazioni Web moderne.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 10/19/18
ms.author: tamram
ms.component: blobs
ms.openlocfilehash: 36b4fbac13e012de2fbef137c6637fd7e2daea8f
ms.sourcegitcommit: ebf2f2fab4441c3065559201faf8b0a81d575743
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2018
ms.locfileid: "52161294"
---
# <a name="static-website-hosting-in-azure-storage"></a>Hosting di siti Web statici in Archiviazione di Azure
Gli account per utilizzo generico v2 di Archiviazione di Azure consentono di usare contenuti statici (file HTML, CSS, JavaScript e di immagine) direttamente da un contenitore di archiviazione denominato *$web*. Sfruttando i vantaggi dell'hosting in Archiviazione di Azure è possibile di usare le architetture serverless tra cui [Funzioni di Azure](/azure/azure-functions/functions-overview) e altri servizi PaaS.

A differenza dell'hosting di siti Web statici, i siti dinamici che dipendono dal codice sul lato server sono meglio ospitati con [App Web di Azure](/azure/app-service/app-service-web-overview).

## <a name="how-does-it-work"></a>Come funziona?
Quando si abilita l'hosting di siti Web statici nell'account di archiviazione, è possibile selezionare il nome del file predefinito e, facoltativamente, fornire un percorso a una pagina 404 personalizzata. Poiché la funzionalità è abilitata, viene creato un contenitore denominato *$web* se non esiste già. 

I file nel contenitore *$web* sono:

- gestiti tramite richieste di accesso anonimo
- disponibili solo tramite operazioni di lettura oggetti
- fa distinzione tra maiuscole e minuscole
- disponibili sul Web pubblico seguendo questo modello: 
    - `https://<ACCOUNT_NAME>.<ZONE_NAME>.web.core.windows.net/<FILE_NAME>`
- disponibili tramite un endpoint di archiviazione Blob seguendo questo modello: 
    - `https://<ACCOUNT_NAME>.blob.core.windows.net/$web/<FILE_NAME>`

È possibile usare l'endpoint di archiviazione Blob per caricare i file. Ad esempio, il file caricato in questo percorso:

```bash
https://contoso.blob.core.windows.net/$web/image.png
```

è disponibile nel browser in un percorso simile al seguente:

```bash
https://contoso.z4.web.core.windows.net/image.png
```

Quando non viene fornito un nome file, viene usato il nome file predefinito selezionato a livello di radice con tutte le sottodirectory. Se il server restituisce un errore 404 e non si specifica un percorso del documento di errore, all'utente verrà restituita una pagina 404 predefinita.

## <a name="cdn-and-ssl-support"></a>Supporto della rete CDN e di SSL

Per rendere disponibili i file di un sito Web statico tramite HTTPS, vedere [Uso della rete CDN di Azure per accedere a BLOB con domini personalizzati tramite HTTPS](storage-https-custom-domain-cdn.md). Come parte di questo processo, è necessario che *la rete CDN punti all'endpoint Web* invece che all'endpoint BLOB. Potrebbe essere necessario attendere alcuni minuti prima che il contenuto sia visibile perché la configurazione della rete CDN non viene eseguita immediatamente.


## <a name="custom-domain-names"></a>Nomi di dominio personalizzati

È possibile [configurare un nome di dominio personalizzato per l'account di Archiviazione di Azure](storage-custom-domain-name.md) per rendere disponibile il sito Web statico tramite un dominio personalizzato. Per informazioni dettagliate su come ospitare un dominio in Azure, vedere [Ospitare il dominio in DNS di Azure](../../dns/dns-delegate-domain-azure-dns.md).

## <a name="pricing"></a>Prezzi
L'hosting di siti Web statici viene fornito senza alcun costo aggiuntivo. Per altri dettagli sui prezzi di Archiviazione BLOB di Azure, consultare la [pagina dei prezzi di Archiviazione BLOB di Azure](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="quickstart"></a>Guida introduttiva

### <a name="azure-portal"></a>Portale di Azure
Iniziare aprendo il portale di Azure all'indirizzo https://portal.azure.com ed eseguire i passaggi seguenti nell'account di archiviazione per utilizzo generico v2:

1. Fare clic su **Impostazioni**.
2. Fare clic su **Sito Web statico**.
3. Immettere un *nome del documento di indice*. Un valore comune è *index.html*.
4. Immettere facoltativamente un *percorso del documento di errore* di una pagina 404 personalizzata. Un valore comune è *404.html*.

![](media/storage-blob-static-website/storage-blob-static-website-portal-config.PNG)

Caricare quindi gli asset nel contenitore *$web* tramite il portale di Azure o con [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) per caricare tutte le directory. Assicurarsi di includere un file che corrisponda al *nome del documento di indice* selezionato durante l'abilitazione della funzionalità.

Infine, passare all'endpoint Web per testare il sito Web.

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure
Installare l'estensione di anteprima di archiviazione:

```azurecli-interactive
az extension add --name storage-preview
```
Nel caso di più sottoscrizioni impostare l'interfaccia della riga di comando nella sottoscrizione dell'account di archiviazione per utilizzo generico v2 da abilitare:

```azurecli-interactive
az account set --subscription <SUBSCRIPTION_ID>
```
Abilitare la funzionalità. Assicurarsi di sostituire tutti i valori segnaposto, incluse le parentesi, con i propri valori:

```azurecli-interactive
az storage blob service-properties update --account-name <ACCOUNT_NAME> --static-website --404-document <ERROR_DOCUMENT_NAME> --index-document <INDEX_DOCUMENT_NAME>
```
Query per l'URL dell'endpoint Web:

```azurecli-interactive
az storage account show -n <ACCOUNT_NAME> -g <RESOURCE_GROUP> --query "primaryEndpoints.web" --output tsv
```

Caricare gli oggetti nel contenitore *$web* da una directory di origine:

```azurecli-interactive
az storage blob upload-batch -s <SOURCE_PATH> -d $web --account-name <ACCOUNT_NAME>
```

## <a name="deployment"></a>Distribuzione

Di seguito sono riportati i metodi disponibili per la distribuzione del contenuto in un contenitore di archiviazione:

- [AzCopy](../common/storage-use-azcopy.md)
- [Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)
- [Azure Pipelines](https://code.visualstudio.com/tutorials/static-website/deploy-VSTS)
- [Estensione di Visual Studio Code](https://code.visualstudio.com/tutorials/static-website/getting-started)

In tutti i casi assicurarsi di copiare i file nel contenitore *$web*.

## <a name="metrics"></a>Metriche

Per abilitare le metriche nelle pagine dei siti Web statici, fare clic su **Impostazioni** > **Monitoraggio** > **Metriche**.

I dati delle metriche vengono generati mediante l'associazione a diverse API di metrica. Il portale visualizza solo i membri delle API usati in un determinato intervallo di tempo per concentrarsi solo sui membri che restituiscono dati. Per assicurasi di poter selezionare il membro dell'API necessario, il primo passaggio è quello di espandere l'intervallo di tempo.

Fare clic sul pulsante dell'intervallo di tempo e selezionare **Ultime 24 ore** e quindi fare clic su **Applica**. 

![Intervallo di tempo delle metriche dei siti Web statici di Archiviazione di Azure](./media/storage-blob-static-website/storage-blob-static-website-metrics-time-range.png)

Selezionare quindi **BLOB** dall'elenco a discesa *Spazio dei nomi*.

![Spazio dei nomi delle metriche dei siti Web statici di Archiviazione di Azure](./media/storage-blob-static-website/storage-blob-static-website-metrics-namespace.png)

Selezionare quindi la metrica **Dati in uscita**.

![Metrica Dati in uscita delle metriche dei siti Web statici di Archiviazione di Azure](./media/storage-blob-static-website/storage-blob-static-website-metrics-metric.png)

Selezionare **Somma** dal selettore *Aggregazione*.

![Aggregazione delle metriche dei siti Web statici di Archiviazione di Azure](./media/storage-blob-static-website/storage-blob-static-website-metrics-aggregation.png)

Fare quindi clic sul pulsante **Aggiungi filtro** scegliere **Nome API** dal selettore *Proprietà*.

![Nome API delle metriche dei siti Web statici di Archiviazione di Azure](./media/storage-blob-static-website/storage-blob-static-website-metrics-api-name.png)

Selezionare infine la casella accanto a **GetWebContent** nel selettore *Valori* per popolare il report sulle metriche.

![GetWebContent delle metriche dei siti Web statici di Archiviazione di Azure](./media/storage-blob-static-website/storage-blob-static-website-metrics-getwebcontent.png)

Una volta abilitata, le statistiche sul traffico per i file del contenitore *$web* vengono riportate nel dashboard delle metriche.

## <a name="faq"></a>Domande frequenti

**La funzionalità dei siti Web statici è disponibile per tutti i tipi di account di archiviazione?**  
No, l'hosting dei siti Web statici è disponibile solo negli account di archiviazione standard GPv2.

**Storage VNET e le regole del firewall sono supportati sul nuovo endpoint Web?**  
Sì, il nuovo endpoint Web rispetta le regole VNET e del firewall configurate per l'account di archiviazione.

**L'endpoint web fa distinzione tra maiuscole e minuscole?**  
Sì, l'endpoint Web fa distinzione tra maiuscole e minuscole esattamente come l'endpoint BLOB. 

## <a name="next-steps"></a>Passaggi successivi
* [Usare la rete CDN di Azure per accedere ai BLOB con domini personalizzati tramite HTTPS](storage-https-custom-domain-cdn.md)
* [Configurare un nome di dominio personalizzato per l'endpoint BLOB o Web](storage-custom-domain-name.md)
* [Funzioni di Azure](/azure/azure-functions/functions-overview)
* [App Web di Azure](/azure/app-service/app-service-web-overview)
* [Compilare la prima app Web serverless](https://docs.microsoft.com/azure/functions/tutorial-static-website-serverless-api-with-database)
* [Esercitazione: Ospitare un dominio in DNS di Azure](../../dns/dns-delegate-domain-azure-dns.md)
