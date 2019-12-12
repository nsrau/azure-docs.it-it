---
title: Incorporare il client dell'analizzatore Internet | Microsoft Docs
description: In questo articolo viene illustrato come incorporare il client JavaScript dell'analizzatore Internet in un'applicazione.
services: internet-analyzer
author: mattcalder
ms.service: internet-analyzer
ms.topic: quickstart
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: f9ecb8d731945847160b49c68c554fafdd7285d9
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74896375"
---
# <a name="embed-the-internet-analyzer-client"></a>Incorporare il client dell'analizzatore Internet

In questo articolo viene illustrato come incorporare il client JavaScript in un'applicazione. L'installazione di questo client è necessaria per eseguire test e ricevere analisi scorecard. **Il client JavaScript specifico del profilo viene fornito dopo la configurazione del primo test.** A questo punto, è possibile continuare ad aggiungere o rimuovere test da tale profilo senza dover incorporare un nuovo script. Per altre informazioni sull'analizzatore Internet, vedere la [panoramica](internet-analyzer-overview.md). 

> [!IMPORTANT]
> L'anteprima pubblica viene messa a disposizione senza contratto di servizio e non deve essere usata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate, potrebbero avere funzioni limitate o potrebbero non essere disponibili in tutte le località di Azure. Vedere [Condizioni supplementari per l'uso delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="before-you-begin"></a>Prima di iniziare

L'analizzatore Internet richiede l'accesso ad Azure e ad altri servizi Microsoft per funzionare correttamente. Prima di incorporare il client, consentire l'accesso alla rete per `fpc.msedge.net` ed eventuali URL di endpoint preconfigurati (visibili tramite l'[interfaccia della riga di comando](internet-analyzer-cli.md)).

## <a name="find-the-client-script-url"></a>Trovare l'URL dello script client

L'URL dello script può essere trovato tramite il portale di Azure o l'interfaccia della riga di comando di Azure dopo la configurazione di un test. Per altre informazioni, vedere [Creare una risorsa analizzatore Internet](internet-analyzer-create-test-portal.md).

Opzione 1. Nella portale di Azure usare [questo collegamento](https://aka.ms/InternetAnalyzerPreviewPortal) per aprire la pagina del portale di anteprima per l'analizzatore Internet di Azure. Passare al profilo dell'analizzatore Internet per visualizzare l'URL dello script selezionando **Impostazioni > Configurazione**.

Opzione 2. Usando l'interfaccia della riga di comando di Azure, controllare la proprietà `scriptFileUri`.
```azurecli-interactive
    az extension add --name internet-analyzer    
    az internet-analyzer test list --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile"
```

## <a name="client-details"></a>Dettagli del client

Lo script viene generato in modo specifico per il profilo e i test. Dopo il caricamento, lo script verrà eseguito con un ritardo di due secondi. Prima di tutto contatta il servizio analizzatore Internet per recuperare l'elenco degli endpoint configurati nei test. Esegue quindi le misurazioni e carica i risultati temporizzati nel servizio analizzatore Internet.

## <a name="client-examples"></a>Esempi client

Questi esempi illustrano alcuni metodi di base per incorporare il codice JavaScript del client in una pagina Web o in un'applicazione. Viene usato `0bfcb32638b44927935b9df86dcfe397` come ID profilo di esempio per l'URL dello script.

### <a name="run-on-page-load"></a>Eseguire lo script al caricamento della pagina
Il metodo più semplice consiste nell'usare il tag script all'interno del blocco di tag META. Questo tag eseguirà lo script una volta per ogni caricamento della pagina.

```html
<html>
<meta>
    <script src="//fpc.msedge.net/client/v2/0bfcb32638b44927935b9df86dcfe397/ab.min.js"></script>
</meta>
<body></body>
</html>
```

## <a name="next-steps"></a>Passaggi successivi

Leggere le [Domande frequenti sull'analizzatore Internet](internet-analyzer-faq.md)
