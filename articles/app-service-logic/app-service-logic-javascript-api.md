<properties
   pageTitle="Uso dell'app per le API JavaScript in un'app per la logica | Microsoft Azure"
   description="Connettore o app per le API JavaScript"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="stepsic-microsoft-com"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="02/22/2016"
   ms.author="stepsic"/>

#App API JavaScript

>[AZURE.NOTE] Questa versione dell'articolo si applica alla versione dello schema 2014-12-01-preview delle app per la logica.

L'applicazione dell'API JavaScript offre un modo semplice per eseguire semplici espressioni JavaScript*mentre è in esecuzione l'applicazione di logica*.

##Quando è necessario utilizzare questa applicazione API?
Lo scenario chiave per questa applicazione API è quando si desidera che il ciclo di vita del codice che è possibile scrivere sia lo stesso dell'applicazione logica e se *non* si desidera che il codice possa essere richiamato in qualsiasi altro scenario.

D'altra parte, se si desidera un frammento di codice che dispone di un ciclo di vita indipendente dell'applicazione logica riutilizzabile, si deve utilizzare l'app processi Web API per creare espressioni di codice semplice e richiamarle da app logica.

Infine, se si vogliono includere tutti i pacchetti aggiuntivi, è necessario usare anche l'app per le API WebJobs, perché non è possibile aggiungere tutte librerie con l'app per le API JavaScript.

Utilizzare la[App API C#](app-service-logic-cs-api.md)se non si desidera scrivere espressioni in C#.

##Creazione di un'applicazione API JavaScript
Per utilizzare l'applicazione dell'API JavaScript, è necessario creare innanzitutto un'istanza dell'applicazione API JavaScript. È possibile eseguire questa operazione inline durante la creazione di un'app per la logica o tramite la selezione dell'app JavaScript API da Azure Marketplace.

##Utilizzo di App API JavaScript nell'area di progettazione logica App
###Trigger
È possibile creare un trigger che attiva il polling, in base a un intervallo definito dall'utente, da parte del servizio app per la logica. Se viene restituito contenuto, esegue l'app per la logica; in caso contrario, attende fino al successivo intervallo di polling per controllare di nuovo.

Gli input per il trigger sono:
- **Espressione JavaScript**: espressione che viene valutata. Viene chiamata in una funzione e deve restituire `false` quando non si vuole eseguire l'app per la logica. Può restituire qualsiasi altro valore quando si vuole eseguire l'app per la logica. È possibile usare il contenuto della risposta nelle azioni dell'app per la logica.
- **Oggetto contesto**: oggetto facoltativo che può essere passato nel trigger. È possibile definire tutte le proprietà desiderate, ma l'entità di primo livello deve essere un oggetto, ad esempio`{ "bar" : 0}`.

Ad esempio, è possibile avere un semplice trigger che eseguirà l'app per la logica solo tra i valori :15 e :30 dell'ora:

```
var d = new Date(); return (d.getMinutes() > 15) && (d.getMinutes() < 30);
```

###Azione

Analogamente, è possibile fornire un'azione da eseguire.

Gli input per l'azione sono:
- **Espressione JavaScript**: espressione che viene valutata. È necessario includere l'istruzione `return` per visualizzare qualsiasi contenuto. 
- **Oggetto contesto**: oggetto facoltativo che può essere passato nel trigger. È possibile definire tutte le proprietà desiderate, ma l'entità di primo livello deve essere un oggetto, ad esempio`{ "bar" : 0}`.

Si supponga, ad esempio, di utilizzare il trigger di Office 365**nuovo messaggio di posta elettronica**. Che restituisce l'oggetto seguente:
```
{
	...
	"Attachments" : [
		{
			"name" : "Picture.png",
			"content" : {
				"ContentData" : "iVBORw0KGgoAAAANSUhEUgAAAAUAAAAFAQMAAAC3obSmAAAABGdBTUEAALGPC/xhBQAAAAFzUkdCAK7OHOkAAAAGUExURf///wAAAFXC034AAAASSURBVAjXY2BgCGBgYOhgKAAABEIBSWDJEbYAAAAASUVORK5CYII=",
				"ContentType" : "image/png",
				"ContentTransferEncoding" : "Base64"
			}
		},	
		{
			"name" : "File.txt",
			"content" : {
				"ContentData" : "Don't worry, be happy!",
				"ContentType" : "text/plain",
				"ContentTransferEncoding" : "None"
			}
		}	
	]
}
```

Tuttavia, si desidera caricare tali allegati a un post Yammer. Sfortunatamente, lo schema per gli allegati Yammer è leggermente diverso. È possibile analizzarlo ora all'interno dell'app per la logica. Per il passaggio solo oggetto di contesto:`@triggerBody()`e per l'espressione passaggio:

```
return Attachments.map(function(obj){var a = obj.Content; a.FileName = obj.Name; return a;})
```

L'azione restituisce il JSON restituito dalla funzione. Pertanto, nell'app API Yammer è possibile fare riferimento a `@body('javascriptapi')` per la proprietà degli **allegati**.

## Più vantaggi con il connettore
Dopo aver creato il connettore, è possibile aggiungerlo a un flusso aziendale usando un'app per la logica. Vedere [Cosa sono le app per la logica?](app-service-logic-what-are-logic-apps.md)

 

<!--References -->

<!--Links -->
[Creating a Logic App]: app-service-logic-create-a-logic-app.md

<!---HONumber=AcomDC_0413_2016-->