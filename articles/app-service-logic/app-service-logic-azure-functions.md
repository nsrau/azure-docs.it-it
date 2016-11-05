---
title: Uso delle funzioni di Azure con le app per la logica | Microsoft Docs
description: Informazioni sull'uso delle funzioni di Azure con le app per la logica
services: logic-apps,functions
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: dwrede
editor: ''

ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 09/01/2016
ms.author: jehollan

---
# Uso delle funzioni di Azure con le app per la logica
È possibile eseguire frammenti di codice personalizzati di C# o node.js usando le funzioni di Azure da un'app per la logica. Le [funzioni di Azure](../azure-functions/functions-overview.md) offrono funzionalità di calcolo indipendenti dal server in Microsoft Azure, utili per eseguire le attività seguenti:

* Formattazione del valore di un'azione (ad esempio, conversione da DateTime a una stringa di data)
* Esecuzione di calcoli all'interno di un flusso di lavoro
* Estensione della funzionalità delle app per la logica con funzioni supportate in C# o node.js

## Creare una funzione per le app per la logica
È consigliabile creare una nuova funzione nel portale Funzioni di Azure usando i modelli **Generic Webhook - Node** (Webhook generico - Node) o **Generic Webhook - C#** (Webhook generico - C#). Verrà popolato automaticamente un modello che accetta `application/json` da un'app per la logica. Le funzioni che usano questi modelli vengono individuate automaticamente ed elencate nella finestra di progettazione delle app per la logica in **Funzioni di Azure nell'area**.

Le funzioni webhook accettano una richiesta e la passano al metodo tramite una variabile `data`. È possibile accedere alle proprietà del payload usando una notazione punto come `data.foo`. Ad esempio, una semplice funzione JavaScript che converte un valore DateTime in una stringa di dati ha un aspetto simile a quello dell'esempio seguente:

```
function start(req, res){
    var data = req.body;
    res = {
        body: data.date.ToDateString();
    }
}
```

## Chiamata di funzioni di Azure da un'app per la logica
Se nella finestra di progettazione si fa clic sul menu **Azioni**, è possibile selezionare **Funzioni di Azure nell'area**. Vengono elencati i contenitori della sottoscrizione ed è possibile scegliere la funzione da chiamare.

Dopo la selezione della funzione, viene richiesto di specificare un oggetto payload di input. Questo è il messaggio inviato dall'app per la logica alla funzione e deve essere un oggetto JSON. Se ad esempio si vuole passare la data **Data ultima modifica** da un trigger Salesforce, il payload della funzione potrebbe avere un aspetto analogo al seguente:

![Data dell'ultima modifica][1]

## Attivare app per la logica da una funzione
È anche possibile attivare un'app per la logica dall'interno di una funzione. A tale scopo, è sufficiente creare un'app per la logica con un trigger manuale. Per altre informazioni, vedere [App per la logica come endpoint che è possibile chiamare](app-service-logic-http-endpoint.md). All'interno della funzione, generare un HTTP POST all'URL del trigger manuale con il payload che da inviare nell'app per la logica.

### Creazione di una funzione dalla finestra di progettazione
È anche possibile creare una funzione webhook node.js dall'interno della finestra di progettazione. Selezionare prima di tutto **Funzioni di Azure nell'area**, quindi scegliere un contenitore per la funzione. Se non è ancora disponibile un contenitore, è necessario crearne uno dal [portale delle funzioni di Azure](https://functions.azure.com/signin). Selezionare quindi **Crea nuovo**.

Per generare un modello in base ai dati da elaborare, specificare l'oggetto di contesto che si prevede di passare a una funzione. Deve trattarsi di un oggetto JSON. Se, ad esempio, si passa il contenuto del file da un'azione FTP, il payload di contesto sarà simile al seguente:

![Payload di contesto][2]

> [!NOTE]
> Poiché non è stato eseguito il cast per convertire questo oggetto in stringa, il contenuto viene aggiunto direttamente al payload JSON. Viene tuttavia generato un errore se non è un token JSON, ad esempio una stringa o un oggetto/matrice JSON. Per eseguire il cast come stringa, è sufficiente aggiungere le virgolette, come mostrato nella prima figura di questo articolo.
> 
> 

La finestra di progettazione genera quindi un modello di funzione che è possibile creare inline. Le variabili vengono create prima in base al contesto che si intende passare alla funzione.

<!--Image references-->
[1]: ./media/app-service-logic-azure-functions/callFunction.png
[2]: ./media/app-service-logic-azure-functions/createFunction.png

<!---HONumber=AcomDC_0907_2016-->