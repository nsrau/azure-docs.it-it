---
title: Chiamare gli endpoint REST con il connettore HTTP + Swagger per le App per la logica di Azure | Microsoft Docs
description: Connettersi agli endpoint REST dalle app per la logica tramite Swagger con il connettore HTTP + Swagger
services: logic-apps
author: jeffhollan
manager: anneta
editor: 
documentationcenter: 
tags: connectors
ms.assetid: eccfd87c-c5fe-4cf7-b564-9752775fd667
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/18/2016
ms.author: jehollan; LADocs
ms.openlocfilehash: 3e9229d94e96aad7b769d0e55d208d856e3b80bc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-the-http--swagger-action"></a>Introduzione all'azione HTTP + Swagger

È possibile creare un connettore di prima classe a qualsiasi endpoint REST tramite un [documento di Swagger](https://swagger.io) quando si usa l'azione HTTP + Swagger nel flusso di lavoro dell'app per la logica. È anche possibile estendere app per la logica per chiamare qualsiasi endpoint REST con un'eccellente esperienza di progettazione delle app per la logica.

Per informazioni su come creare app per la logica con i connettori, consultare [Creare una nuova app per la logica](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="use-http--swagger-as-a-trigger-or-an-action"></a>Usare HTTP + Swagger come trigger o azione

Il trigger e l'azione HTTP + Swagger funzionano come l'[azione HTTP](connectors-native-http.md), ma offrono un'esperienza migliore in Progettazione app per la logica, mostrando la forma dell'API e i risultati grazie ai [metadati Swagger](https://swagger.io). È anche possibile usare il connettore HTTP + Swagger come trigger. Se si desidera implementare un trigger di poll, seguire il modello di polling descritto in [Creazione di un'API personalizzata da usare con le app per la logica](../logic-apps/logic-apps-create-api-app.md#polling-triggers).

Altre informazioni sui [trigger e le azioni nelle app per la logica](connectors-overview.md).

Di seguito è riportato un esempio di come usare l'operazione HTTP + Swagger come azione in un flusso di lavoro in un'app per la logica.

1. Fare clic sul pulsante **Nuovo passaggio** .
2. Selezionare **Aggiungi un'azione**.
3. Nella casella di ricerca azione digitare **swagger** per elencare l'azione HTTP + Swagger.
   
    ![Selezionare l'azione HTTP + Swagger](./media/connectors-native-http-swagger/using-action-1.png)
4. Digitare l'URL per un documento di Swagger:
   
   * Per funzionare dalla finestra di progettazione app per la logica, l'URL deve essere un endpoint HTTPS e avere CORS abilitato.
   * Se il documento di Swagger non soddisfa questo requisito, è possibile usare [Archiviazione di Azure con CORS abilitato](#hosting-swagger-from-storage) per archiviare il documento.
5. Fare clic su **Avanti** per leggere e visualizzare il documento di Swagger.
6. Aggiungere i parametri richiesti per la chiamata HTTP.
   
    ![Completare l'azione HTTP](./media/connectors-native-http-swagger/using-action-2.png)
7. Per salvare e pubblicare un'app per la logica, fare clic su **Salva** nella barra degli strumenti della finestra di progettazione.

### <a name="host-swagger-from-azure-storage"></a>Ospitare Swagger da Archiviazione di Azure
Si consiglia di fare riferimento a un documento di Swagger che non sia ospitato o non soddisfi i requisiti di sicurezza e origini multiple per la finestra di progettazione. Per risolvere questo problema, è possibile archiviare il documento di Swagger in Archiviazione di Azure e abilitare CORS a fare riferimento al documento.  

Ecco i passaggi per creare, configurare e archiviare i documenti di Swagger in Archiviazione di Azure:

1. [Creare un account di Archiviazione di Azure con Archiviazione BLOB di Azure](../storage/common/storage-create-storage-account.md). Per eseguire questo passaggio, impostare le autorizzazioni su **Accesso pubblico**.

2. Abilitare CORS nel BLOB. 

   Per configurare tale impostazione automaticamente è possibile usare [questo script di PowerShell](https://github.com/logicappsio/EnableCORSAzureBlob/blob/master/EnableCORSAzureBlob.ps1).

3. Caricare il file di Swagger nel BLOB. 

   È possibile eseguire questo passaggio nel [portale di Azure](https://portal.azure.com) o con uno strumento come [Esplora archivi di Azure](http://storageexplorer.com/).

4. Rimandare un collegamento HTTPS al documento nell'archiviazione BLOB di Azure 

   Il collegamento usa questo formato:

   `https://*storageAccountName*.blob.core.windows.net/*container*/*filename*`

## <a name="technical-details"></a>Dettagli tecnici
Di seguito sono riportati i dettagli per i trigger e le azioni supportati da questo connettore HTTP + Swagger.

## <a name="http--swagger-triggers"></a>Trigger HTTP + Swagger
Un trigger è un evento che può essere usato per avviare il flusso di lavoro definito in un'app per la logica. [Altre informazioni sui trigger.](connectors-overview.md) Il connettore HTTP + Swagger supporta un solo trigger.

| Trigger | Descrizione |
| --- | --- |
| HTTP + Swagger |Esegue una chiamata HTTP e restituisce il contenuto della risposta |

## <a name="http--swagger-actions"></a>Azioni HTTP + Swagger
Un'azione è un'operazione eseguita dal flusso di lavoro e definita in un'app per la logica. [Ulteriori informazioni sulle azioni.](connectors-overview.md) Il connettore HTTP + Swagger supporta una sola azione possibile.

| Azione | Descrizione |
| --- | --- |
| HTTP + Swagger |Esegue una chiamata HTTP e restituisce il contenuto della risposta |

### <a name="action-details"></a>Informazioni dettagliate sulle azioni
Il connettore HTTP + Swagger include una sola azione possibile. Di seguito sono riportate le informazioni su ognuna delle azioni, i relativi campi di input obbligatori e facoltativi e i corrispondenti dettagli di output associati al loro uso.

#### <a name="http--swagger"></a>HTTP + Swagger
Eseguire una richiesta HTTP in uscita con l'assistenza dei metadati Swagger.
L'asterisco (*) indica un campo obbligatorio.

| Nome visualizzato | Nome proprietà | Descrizione |
| --- | --- | --- |
| Metodo* |statico |Verbo HTTP da usare. |
| URI* |Uri |URI per la richiesta HTTP. |
| Headers |Headers |Un oggetto JSON delle intestazioni HTTP da includere. |
| Corpo |Corpo |Il corpo della richiesta HTTP. |
| Autenticazione |Autenticazione |Autenticazione da usare per la richiesta. Per altre informazioni, vedere il [connettore HTTP](connectors-native-http.md#authentication). |

**Dettagli dell'output**

Risposta HTTP

| Nome proprietà | Tipo di dati | Descrizione |
| --- | --- | --- |
| headers |object |Intestazioni della risposta |
| Corpo |object |Oggetto della risposta |
| Codice di stato |int |Stato codice HTTP |

### <a name="http-responses"></a>Risposte HTTP
Quando si eseguono chiamate a varie azioni, è possibile ottenere determinate risposte. Di seguito è riportata una tabella contenente le risposte e le descrizioni corrispondenti.

| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| 202 |Accepted |
| 400 |Richiesta non valida |
| 401 |Non autorizzata |
| 403 |Accesso negato |
| 404 |Non trovato |
| 500 |Errore interno del server. Si è verificato un errore sconosciuto. |

- - -
## <a name="next-steps"></a>Passaggi successivi

* [Creare un'app per la logica](../logic-apps/logic-apps-create-a-logic-app.md)
* [Trovare altri connettori](apis-list.md)