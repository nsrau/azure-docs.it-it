---
title: Uso delle funzioni di Azure con le app per la logica | Documentazione Microsoft
description: Informazioni sull&quot;uso delle funzioni di Azure con le app per la logica
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
ms.sourcegitcommit: dc8c9eac941f133bcb3a9807334075bfba15de46
ms.openlocfilehash: de073c5a5cda5b4b1caacd0163f785f6295f35ba


---
# <a name="using-azure-functions-with-logic-apps"></a>Uso delle funzioni di Azure con le app per la logica
È possibile eseguire frammenti di codice personalizzati di C# o node.js usando le funzioni di Azure da un'app per la logica.  Le [funzioni di Azure](../azure-functions/functions-overview.md) offrono funzionalità di calcolo indipendenti dal server in Microsoft Azure, utili per eseguire le attività seguenti:

* Formattazione avanzata o calcolo di campi in un'app per la logica
* Esecuzione di calcoli all'interno di un flusso di lavoro
* Estensione della funzionalità delle app per la logica con funzioni supportate in C# o node.js

## <a name="create-a-function-for-logic-apps"></a>Creare una funzione per le app per la logica
È consigliabile creare una nuova funzione nel portale Funzioni di Azure usando i modelli **Generic Webhook - Node** (Webhook generico - Node) o **Generic Webhook - C#** (Webhook generico - C#). Verrà popolato automaticamente un modello che accetta `application/json` da un'app per la logica.  Se si seleziona la scheda **Integrazione** in Funzioni di Azure, l'opzione **Modalità** dovrebbe essere impostata su **Webhook** e il **Tipo di webhook** dovrebbe essere **Generic JSON** (JSON generico).  Le funzioni che usano questi modelli vengono individuate automaticamente ed elencate nella finestra di progettazione delle app per la logica in **Funzioni di Azure nell'area**

Le funzioni webhook accettano una richiesta e la passano al metodo tramite una variabile `data` . È possibile accedere alle proprietà del payload usando una notazione punto come `data.foo`.  Ad esempio, una semplice funzione JavaScript che converte un valore DateTime in una stringa di dati ha un aspetto simile a quello dell'esempio seguente:

```
function start(req, res){
    var data = req.body;
    res = {
        body: data.date.ToDateString();
    }
}
```

## <a name="call-azure-functions-from-a-logic-app"></a>Chiamata di funzioni di Azure da un'app per la logica
Se nella finestra di progettazione si fa clic sul menu **Azioni**, è possibile selezionare **Azure Functions in my Region** (Funzioni di Azure nell'area).  Vengono elencati i contenitori della sottoscrizione ed è possibile scegliere la funzione da chiamare.  

Dopo la selezione della funzione, viene richiesto di specificare un oggetto payload di input. Questo è il messaggio inviato dall'app per la logica alla funzione e deve essere un oggetto JSON. Se ad esempio si vuole passare la data **Data ultima modifica** da un trigger Salesforce, il payload della funzione potrebbe avere un aspetto analogo al seguente:

![Data dell'ultima modifica][1]

## <a name="trigger-logic-apps-from-a-function"></a>Attivare app per la logica da una funzione
È anche possibile attivare un'app per la logica dall'interno di una funzione.  A tale scopo, è sufficiente creare un'app per la logica con un trigger manuale. Per altre informazioni, vedere [App per la logica come endpoint che è possibile chiamare](logic-apps-http-endpoint.md).  All'interno della funzione, generare un HTTP POST all'URL del trigger manuale con il payload che da inviare nell'app per la logica.

### <a name="create-a-function-from-the-designer"></a>Creazione di una funzione dalla finestra di progettazione
È anche possibile creare una funzione webhook node.js dall'interno della finestra di progettazione. Selezionare prima di tutto **Funzioni di Azure nell'area** , quindi scegliere un contenitore per la funzione.  Se non è ancora disponibile un contenitore, è necessario crearne uno dal [portale delle funzioni di Azure](https://functions.azure.com/signin). Selezionare quindi **Crea nuovo**.  

Per generare un modello in base ai dati da elaborare, specificare l'oggetto di contesto che si prevede di passare a una funzione. Deve trattarsi di un oggetto JSON. Se, ad esempio, si passa il contenuto del file da un'azione FTP, il payload di contesto sarà simile al seguente:

![Payload di contesto][2]

> [!NOTE]
> Poiché non è stato eseguito il cast per convertire questo oggetto in stringa, il contenuto viene aggiunto direttamente al payload JSON. Viene tuttavia generato un errore se non è un token JSON, ad esempio una stringa o un oggetto/matrice JSON. Per eseguire il cast come stringa, è sufficiente aggiungere le virgolette, come mostrato nella prima figura di questo articolo.
> 
> 

La finestra di progettazione genera quindi un modello di funzione che è possibile creare inline. Le variabili vengono create prima in base al contesto che si intende passare alla funzione.

<!--Image references-->
[1]: ./media/logic-apps-azure-functions/callfunction.png
[2]: ./media/logic-apps-azure-functions/createfunction.png



<!--HONumber=Jan17_HO3-->


