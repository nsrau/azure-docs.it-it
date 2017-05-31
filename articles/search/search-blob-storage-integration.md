---
title: Aggiunta di Ricerca di Azure all&quot;archiviazione BLOB | Microsoft Docs
description: Creare un indice nel codice tramite l&quot;API REST HTTP di Ricerca di Azure.
services: search
documentationcenter: 
author: ashmaka
manager: jhubbard
ms.service: search
ms.topic: article
ms.date: 05/04/2017
ms.author: ashmaka
ms.translationtype: Human Translation
ms.sourcegitcommit: 9ae7e129b381d3034433e29ac1f74cb843cb5aa6
ms.openlocfilehash: 0cd0cbb05c465d32a9ef02f9350ebdf9ccea36c5
ms.contentlocale: it-it
ms.lasthandoff: 05/08/2017

---

# <a name="searching-blob-storage-with-azure-search"></a>Ricerca nell'archiviazione BLOB tramite Ricerca di Azure

La ricerca in un'ampia gamma di tipi di contenuto inclusi nell'archiviazione BLOB di Azure può rivelarsi un problema complesso da risolvere. Tuttavia, è possibile indicizzare e cercare il contenuto dei BLOB in pochi clic usando Ricerca di Azure. La ricerca nell'archiviazione BLOB richiede il provisioning di un servizio di Ricerca di Azure. I diversi limiti del servizio e i piani tariffari di Ricerca di Azure sono disponibili nella [pagina dei prezzi](https://aka.ms/azspricing).

## <a name="what-is-azure-search"></a>Che cos'è Ricerca di Azure?
[Ricerca di Azure](https://aka.ms/whatisazsearch) è una soluzione di ricerca che aiuta gli sviluppatori a integrare solide esperienze di ricerca full-text in applicazioni per dispositivi mobili e Web. In qualità di servizio, Ricerca di Azure elimina la necessità di gestire qualsiasi infrastruttura di ricerca, offrendo un [contratto di servizio con tempo di attività del 99,9%](https://aka.ms/azuresearchsla).

Con supporto avanzato per 56 lingue, Ricerca di Azure può analizzare il contenuto e gestire in modo intelligente i costrutti specifici di ogni lingua. Ricerca di Azure offre inoltre gli strumenti necessari per creare un'esperienza di ricerca avanzata per gli utenti. Con Ricerca di Azure è facile aggiungere alle interfacce utente funzionalità come l'esplorazione in base a facet, i suggerimenti di ricerca con completamento automatico e l'evidenziazione dei risultati. Per informazioni sulle funzionalità di Ricerca di Azure, vedere la [documentazione](https://aka.ms/azsearchdocs) del servizio.

## <a name="crack-open-and-search-through-the-content-of-enterprise-document-formats"></a>Estrazione e ricerca nel contenuto con formati di documento aziendali
Grazie al supporto per l'[estrazione di documenti](https://aka.ms/azsblobindexer) nell'archiviazione BLOB di Azure, Ricerca di Azure può indicizzare il contenuto di un'ampia gamma di tipi di file archiviati in BLOB:
- PDF
- Microsoft Office: DOCX/DOC, XLSX/XLS, PPTX/PPT, MSG (messaggi di posta elettronica di Outlook)
- HTML
- File di testo normale

L'estrazione di testo e metadati di questi tipi di file semplifica la ricerca tra più formati di file con un'unica query per trovare i documenti più pertinenti, indipendentemente dal tipo. Indicizzando il contenuto e i metadati di documenti di Microsoft Office, PDF e messaggi di posta elettronica, è possibile creare una solida soluzione di gestione del contenuto aziendale tramite l'archiviazione BLOB e Ricerca di Azure.

## <a name="search-through-your-blob-metadata"></a>Ricerca nei metadati dei BLOB
Uno scenario comune che semplifica l'ordinamento nei BLOB di qualsiasi tipo di contenuto consiste nell'indicizzare i metadati dei BLOB personalizzati e definiti dall'utente, nonché le proprietà di sistema per ognuno dei BLOB. In questo modo, vengono indicizzate le informazioni per ogni BLOB indipendentemente dal tipo di documento nel BLOB, in modo da poter ordinare ed esplorare in base a facet tutto il contenuto dell'archiviazione BLOB con semplicità.

[Altre informazioni sull'indicizzazione dei metadati dei BLOB.](https://aka.ms/azsblobmetadataindexing)

## <a name="image-search"></a>Ricerca di immagini
Le funzionalità di ricerca full-text, esplorazione in base a facet e ordinamento di Ricerca di Azure possono ora essere applicate ai metadati delle immagini archiviate in BLOB.

Se queste immagini vengono pre-elaborate con l'[API Visione artificiale](https://www.microsoft.com/cognitive-services/computer-vision-api) di Servizi cognitivi di Microsoft, è possibile indicizzare il contenuto visivo di ogni immagine, anche con OCR e riconoscimento della grafia. Microsoft sta lavorando all'aggiunta di OCR e di altre funzionalità di elaborazione delle immagini direttamente a Ricerca di Azure. Chiunque sia interessato a queste funzionalità può immettere una richiesta in [UserVoice](https://aka.ms/azsuv) o inviare un [messaggio di posta elettronica](mailto:azscustquestions@microsoft.com).

## <a name="index-and-search-through-json-blobs"></a>Indicizzare e cercare contenuto in BLOB JSON
È possibile configurare Ricerca di Azure in modo da estrarre il contenuto strutturato dei BLOB che contengono JSON. Ricerca di Azure può leggere BLOB JSON e analizzare il contenuto strutturato nei campi appropriati di un documento di Ricerca di Azure. Ricerca di Azure può anche usare BLOB che contengono una matrice di oggetti JSON ed eseguire il mapping di ogni elemento a un documento di Ricerca di Azure separato.

Notare che l'analisi JSON non è attualmente configurabile tramite il portale. [Altre informazioni sull'analisi JSON in Ricerca di Azure.](https://aka.ms/azsjsonblobindexing)

## <a name="quick-start"></a>Avvio rapido
È possibile aggiungere Ricerca di Azure ai BLOB direttamente dal pannello del portale dell'archiviazione BLOB.

![](./media/search-blob-storage-integration/blob-blade.png)

Facendo clic sull'opzione "Aggiungi Ricerca di Azure", viene avviato un flusso in cui è possibile selezionare un servizio di Ricerca di Azure esistente o crearne uno nuovo. Se si crea un nuovo servizio, non si sarà più connessi al portale dell'account di archiviazione. Sarà necessario riaccedere al pannello del portale di archiviazione e selezionare di nuovo l'opzione "Aggiungi Ricerca di Azure", in cui selezionare il servizio esistente.

### <a name="next-steps"></a>Passaggi successivi
Altre informazioni sull'indicizzatore BLOB di Ricerca di Azure sono disponibili nella [documentazione](https://aka.ms/azsblobindexer) completa.

