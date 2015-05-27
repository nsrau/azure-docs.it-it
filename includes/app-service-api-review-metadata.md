## Esaminare i metadati dell'app per le API

I metadati che permettono lo sviluppo del progetto di API Web come app per le API sono inclusi nel file *apiapp.json* e nella cartella *Metadata*.

![](./media/app-service-api-review-metadata/metadatainse.png)

I contenuti predefiniti del file *apiapp.json* sono analoghi a quelli dell'esempio seguente:

		{
		    "$schema": "http://json-schema.org/schemas/2014-11-01/apiapp.json#",
		    "id": "ContactsList",
		    "namespace": "microsoft.com",
		    "gateway": "2015-01-14",
		    "version": "1.0.0",
		    "title": "ContactsList",
		    "summary": "",
		    "author": "",
		    "endpoints": {
		        "apiDefinition": "/swagger/docs/v1",
		        "status": null
		    }
		}

Per questa esercitazione è possibile accettare le impostazioni predefinite. La sezione [Metadati delle app per le API](#api-app-metadata) più avanti nell'esercitazione illustra come personalizzare i metadati.
<!--HONumber=54-->