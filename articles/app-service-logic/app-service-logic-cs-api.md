<properties
   pageTitle="App C# Api"
   description="App C# Api"
   services="app-service\logic"
   documentationCenter=".net"
   authors="jeffhollan"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="10/17/2015"
   ms.author="jehollan"/>

#App C# API
L'applicazione dell'API C# offre un modo semplice per eseguire semplici espressioni C#*mentre è in esecuzione l'applicazione di logica*.

##Quando è necessario utilizzare questa applicazione API?
Lo scenario chiave per questa applicazione API è quando si desidera che il ciclo di vita del codice che è possibile scrivere sia lo stesso dell'applicazione logica e se *non* si desidera che il codice possa essere richiamato in qualsiasi altro scenario.

D'altra parte, se si desidera un frammento di codice che dispone di un ciclo di vita indipendente dell'applicazione logica riutilizzabile, si deve utilizzare l'app processi Web API per creare espressioni di codice semplice e richiamarle da app logica.

Infine, se si desidera includere tutti i pacchetti aggiuntivi, è necessario passare nell'assembly (DLL) al connettore come stringa binaria (ad esempio, l'output dall'archiviazione blob) con codifica Base64. Se si desidera maggiore flessibilità su pacchetti e assembly, un processo Web sarebbe forse un'opzione migliore.

Utilizzare il[App API JavaScript](app-service-logic-javascript-api.md)se non si desidera scrivere espressioni in JS.

##Creazione di un'applicazione APIC#
Per utilizzare l'applicazione dell'API C#, è necessario creare innanzitutto un'istanza dell'applicazione API C#. È possibile eseguire questa operazione inline durante la creazione di un'app per la logica o tramite la selezione dell'app C# API da Azure Marketplace.

##Utilizzo di App API C# nell'area di progettazione logica App
###Trigger
È possibile creare un trigger che eseguirà il polling del servizio App logica (su un intervallo è definire), e, se viene restituito qualsiasi elemento diverso da `false`, verrà eseguita l'applicazione di logica, in caso contrario, si attende l'intervallo di polling successivo per poi verificare nuovamente.

Gli input per i trigger sono:-**espressione C#** - verrà valutata un'espressione. Verrà richiamato in una funzione e deve restituire`false`quando non si desidera che l'applicazione logica venga eseguita e può restituire qualsiasi altro elemento quando si desidera che l'applicazione logica venga eseguita. Sarà possibile utilizzare il contenuto della risposta nelle azioni dell'applicazione logica.

Ad esempio, si potrebbe disporre di un semplice trigger che eseguirà l'applicazione di logica tra il: 15 e le: 30 dell'ora:

```
var d = new DateTime.Now; return (d.Minute > 15) && (d.Minute < 30);
```

###Azione

Analogamente, è possibile fornire un'azione da eseguire.

Gli input per l'azione sono:-**espressione C#**: sarà valutata un'espressione. È necessario includere una`return`istruzione per visualizzare qualsiasi contenuto. -**Oggetti contesto**- un oggetto di contesto facoltativo che può essere passato all'interno del trigger. È possibile definire tutte le proprietà desiderate, ma la base deve essere un JObject`{ ... }`e gli oggetti possono essere utilizzati nello script tramite il nome della chiave (il valore viene passato come un corrispondente JToken per nome).-**Librerie**- una matrice facoltativa di file con estensione .dll da includere nella compilazione dello script. La matrice utilizza la seguente struttura e funziona meglio accanto a un connettore di archiviazione blob con .dll come output:

```javascript
[{"filename": "name.dll", "assembly": {Base64StringFromConnector}, "usingstatment": "using Library.Reference;"}]
```

Si supponga, ad esempio, di utilizzare il trigger di Office 365**nuovo messaggio di posta elettronica**. Che restituisce l'oggetto seguente:

```javascript
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

Tuttavia, si desidera caricare tali allegati a un post Yammer. Sfortunatamente, lo schema per gli allegati Yammer è leggermente diverso. È ora possibile analizzarlo ora all'interno dell'applicazione logica. Per il passaggio solo oggetto di contesto:`@triggerBody()`e per l'espressione passaggio:

```javascript
JArray YammerAttachments = new JObject();
foreach(var obj in (JArray)Attachments)
{
	JObject att = new JObject();
	att["Content"] = obj["content"];
	att["FileName"] = obj["Name"];
	YammerAttachments.Add(att);	
}
return YammerAttachments;
```

L'azione restituisce l'oggetto restituito dalla funzione in un oggetto di risultati. Pertanto, nell'app API Yammer è possibile fare riferimento a `@body('csapi').results` per la proprietà degli **allegati**.

## Altri vantaggi del connettore
Dopo aver creato il connettore, è possibile aggiungerlo a un flusso aziendale usando un'app per la logica. Vedere [Cosa sono le app per la logica?](app-service-logic-what-are-logic-apps.md)

È anche possibile esaminare le statistiche relative alle prestazioni e controllare la sicurezza del connettore. Vedere l'articolo relativo alla [gestione e al monitoraggio delle app per le API e del connettore](../app-service-api/app-service-api-manage-in-portal.md).

<!--References -->

<!--Links -->
[Creating a Logic App]: app-service-logic-create-a-logic-app.md

<!---HONumber=Oct15_HO4-->