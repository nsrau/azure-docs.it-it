---
title: Aggiungere codice personalizzato alle app per la logica con le Funzioni di Azure | Documentazione Microsoft
description: Creare codice personalizzato per le app per la logica di Azure con le Funzioni di Azure
services: logic-apps,functions
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: anneta
editor: 
ms.assetid: 9fab1050-cfbc-4a8b-b1b3-5531bee92856
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 10/18/2016
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: c63dde728eaaf8237970e05cc524c6220b69a074
ms.openlocfilehash: 8b68f017a2c7a17603508438b0d4bd760bec4f78
ms.lasthandoff: 02/15/2017


---
# <a name="add-custom-code-to-azure-logic-apps-with-azure-functions"></a>Aggiungere codice personalizzato alle app per la logica di Azure con le Funzioni di Azure

È possibile eseguire frammenti di codice personalizzati di C# o node.js usando le Funzioni di Azure da app per la logica. 
Le [Funzioni di Azure](../azure-functions/functions-overview.md) offrono funzionalità di calcolo indipendenti dal server in Microsoft Azure e sono utili per eseguire queste attività:

* Formattazione avanzata o calcolo di campi nelle app per la logica
* Esecuzione di calcoli in un flusso di lavoro.
* Estensione della funzionalità delle app per la logica con funzioni supportate in C# o node.js

## <a name="create-functions-for-logic-apps"></a>Creazione di funzioni per le app per la logica

È consigliabile creare una funzione nel portale Funzioni di Azure dai modelli **Generic Webhook - Node** (Webhook generico - Node) o **Generic Webhook - C#** (Webhook generico - C#). Il risultato crea un modello popolato automaticamente che accetta `application/json` da un'app per la logica. Le funzioni che vengono create da questi modelli sono individuate automaticamente ed elencate nella finestra di progettazione delle app per la logica in **Funzioni di Azure nell'area**.

Nel portale di Azure, nel riquadro **Integrazione** per la funzione il modello deve visualizzare **Modalità** impostata su **Webhook** e **Tipo di webhook** impostato su **Generic JSON** (JSON generico). 

Le funzioni webhook accettano una richiesta e la passano al metodo tramite una variabile `data` . È possibile accedere alle proprietà del payload usando una notazione punto come `data.foo`. Ad esempio, una semplice funzione JavaScript che converte un valore DateTime in una stringa di dati ha un aspetto simile a quello dell'esempio seguente:

```
function start(req, res){
    var data = req.body;
    res = {
        body: data.date.ToDateString();
    }
}
```

## <a name="call-azure-functions-from-logic-apps"></a>Chiamare Funzioni di Azure da app per la logica

Per elencare i contenitori nella sottoscrizione e selezionare la funzione che si desidera chiamare, in Progettazione app per la logica fare clic sul menu **Azioni**e scegliere da **Azure Functions in my Region** (Funzioni di Azure nella mia area).

Dopo la selezione della funzione, viene richiesto di specificare un oggetto payload di input. Questo oggetto è il messaggio inviato dall'app per la logica alla funzione e deve essere un oggetto JSON. Se ad esempio si vuole passare la **Data ultima modifica** da un trigger Salesforce, il payload della funzione potrebbe avere un aspetto analogo all'esempio seguente:

![Data ultima modifica][1]

## <a name="trigger-logic-apps-from-a-function"></a>Attivare app per la logica da una funzione

È possibile attivare un'app per la logica all'interno di una funzione. Vedere [App per la logica come endpoint che è possibile chiamare](logic-apps-http-endpoint.md). Creare un'app per la logica dotata di trigger manuale, quindi dall'interno della funzione generare un HTTP POST all'URL del trigger manuale con il payload che da inviare all'app per la logica.

### <a name="create-a-function-from-logic-app-designer"></a>Creare una funzione dalla finestra di progettazione delle app per la logica

È anche possibile creare una funzione webhook node.js dalla finestra di progettazione. Selezionare prima di tutto **Funzioni di Azure nell'area** , quindi scegliere un contenitore per la funzione. Se non è ancora disponibile un contenitore, è necessario crearne uno dal [portale delle funzioni di Azure](https://functions.azure.com/signin). Selezionare quindi **Crea nuovo**.  

Per generare un modello in base ai dati da elaborare, specificare l'oggetto di contesto che si prevede di passare a una funzione. Deve trattarsi di un oggetto JSON. Se, ad esempio, si passa il contenuto del file da un'azione FTP, il payload di contesto è simile all'esempio seguente:

![Payload di contesto][2]

> [!NOTE]
> Poiché non è stato eseguito il cast per convertire questo oggetto in stringa, il contenuto viene aggiunto direttamente al payload JSON. Viene tuttavia generato un errore se l'oggetto non è un token JSON, ad esempio una stringa o un oggetto/matrice JSON. Per eseguire il cast come stringa, aggiungere le virgolette, come mostrato nella prima figura di questo articolo.
> 

La finestra di progettazione genera quindi un modello di funzione che è possibile creare inline. Le variabili vengono create prima in base al contesto che si intende passare alla funzione.

<!--Image references-->
[1]: ./media/logic-apps-azure-functions/callfunction.png
[2]: ./media/logic-apps-azure-functions/createfunction.png

