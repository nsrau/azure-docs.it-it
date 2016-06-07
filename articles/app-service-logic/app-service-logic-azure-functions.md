<properties
   pageTitle="Uso delle funzioni di Azure con le app per la logica | Microsoft Azure"
   description="Informazioni sull'uso delle funzioni di Azure con le app per la logica"
   services="app-service\logic,functions"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="05/14/2016"
   ms.author="jehollan"/>
   
# Uso delle funzioni di Azure con le app per la logica

È possibile eseguire frammenti personalizzati di C# o Node. js usando le funzioni di Azure da un'app per la logica. [Funzioni di Azure](../azure-functions/functions-overview.md) è un'offerta che consente di elaborare i dati senza server in Microsoft Azure. È utile nell'ambito delle app per la logica in molti degli scenari riportati di seguito:

* Formattazione di un valore di un'azione, ad esempio, conversione di DateTime in una stringa di data
* Esecuzione di calcoli all'interno di un flusso di lavoro
* Estensione delle funzionalità delle app per la logica con funzioni supportate in C# o Node. js

## Creazione di una funzione di Azure per le app per la logica

È consigliabile creare una nuova funzione di Azure nel portale delle funzioni usando come modello un webhook nodo generico o un webhook C# generico. In questo modo verrà compilato automaticamente un modello che accetta `application/json` da un'app per la logica e le funzioni che usano i modelli verranno automaticamente rilevate ed elencate nella finestra di progettazione delle app per la logica, nella sezione delle funzioni di Azure per l'area specifica.

Le funzioni webhook accettano una richiesta e la passano al metodo usando una variabile `data`. È possibile accedere alle proprietà del payload usando una notazione punto come `data.foo`. Ad esempio, una funzione javascript semplice che converte un valore DateTime in una stringa di data ha questo aspetto:

```
function start(req, res){
    var data = req.body;
    res = {
        body: data.date.ToDateString();
    }
}
```

## Chiamata di funzioni di Azure da un'app per la logica

Nella finestra di progettazione se si sceglie il menu a discesa per le azioni è possibile selezionare le funzioni di Azure disponibili nella propria area. Verranno elencati i contenitori presenti nel proprio abbonamento e sarà possibile scegliere la funzione da chiamare. Dopo la selezione verrà chiesto di specificare un oggetto payload di input. Questo è il messaggio che l'app per la logica invia alla funzione e deve essere un oggetto JSON. Ad esempio, per passare la data "Ultima modifica" da un trigger di Salesforce, il payload della funzione può avere un aspetto simile al seguente:

![][1]

## Attivazione di app per la logica da una funzione di Azure

È anche possibile attivare un'app per la logica dall'interno di una funzione. A tale scopo, è sufficiente creare un'app per la logica con un trigger manuale (vedere [qui](app-service-logic-http-endpoint.md) i dettagli). All'interno della funzione di Azure, generare un HTTP POST all'URL del trigger manuale con il payload che da inviare nell'app per la logica.

### Creazione di una funzione dalla finestra di progettazione

È anche possibile creare una funzione webhook node.js dall'interno della finestra di progettazione. Per prima cosa scegliere un contenitore per la funzione nella sezione delle funzioni di Azure disponibili nella propria area. Se non si ha ancora un contenitore, sarà necessario crearlo dal [portale delle funzioni di Azure](https://functions.azure.com/signin). È quindi possibile selezionare "Crea nuovo". Per generare un modello in base ai dati da elaborare, specificare l'oggetto di contesto che si prevede di passare a una funzione. Deve trattarsi di un oggetto JSON. Ad esempio, se si passa il contenuto del file da un'azione FTP, il payload di contesto sarà simile al seguente:

![][2]

>[AZURE.NOTE] Poiché non è stato eseguito il cast per convertire questo oggetto in stringa aggiungendo le virgolette, il contenuto viene aggiunto direttamente al payload JSON. Viene generato un errore se non è un token JSON, ad esempio una stringa o un oggetto/matrice JSON. Per eseguire il cast come stringa, è sufficiente aggiungere le virgolette come nell'esempio di Salesforce precedente.

La finestra di progettazione genera quindi un modello di funzione che è possibile creare inline. Le variabili vengono create prima in base al contesto che si intende passare alla funzione.

<!--Image references-->
[1]: ./media/app-service-logic-azure-functions/callFunction.png
[2]: ./media/app-service-logic-azure-functions/createFunction.png

<!---HONumber=AcomDC_0525_2016-->