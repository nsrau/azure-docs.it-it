<properties
   pageTitle="Publish-WebApplicationWebSite (script di Windows PowerShell) | Microsoft Azure"
   description="Informazioni su come pubblicare un progetto Web in un sito Web di Azure. Se non sono presenti, lo script crea le risorse necessarie nella sottoscrizione di Azure."
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# Publish-WebApplicationWebSite (script di Windows PowerShell)

##Sintassi

Pubblica un progetto Web in un sito Web di Azure. Se non sono presenti, lo script crea le risorse necessarie nella sottoscrizione di Azure.

	Publish-WebApplicationWebSite
	–Configuration <configuration>
	-SubscriptionName <subscriptionName>
	-WebDeployPackage <packageName>
	-DatabaseServerPassword @{Name = "name"; Password = "password"}
	-SendHostMessagesToOutput
	-Verbose


## Configurazione

Percorso del file di configurazione JSON che descrive i dettagli della distribuzione.

|Parametro|Valore predefinito|
|---|---|
|Alias|nessuno|
|Obbligatorio?|true|
|Posizione|denominata|
|Valore predefinito|nessuno|
|Input pipeline accettato?|false|
|Caratteri jolly accettati?|false|

## SubscriptionName

Nome della sottoscrizione di Azure in cui si vuole creare il sito Web.

|Parametro|Valore predefinito|
|---|---|
|Alias|nessuno|
|Obbligatorio?|false|
|Posizione|denominata|
|Valore predefinito|nessuno|
|Input pipeline accettato?|false|
|Caratteri jolly accettati?|false|

## WebDeployPackage

Percorso al pacchetto di distribuzione Web da pubblicare nel sito Web. È possibile creare questo pacchetto usando la pubblicazione Web guidata di Visual Studio. Per ulteriori informazioni, vedere [Introduzione a Servizi cloud di Azure e ASP.NET](http://go.microsoft.com/fwlink/p/?LinkID=623089).

|Parametro|Valore predefinito|
|---|---|
|Alias|nessuno|
|Obbligatorio?|false|
|Posizione|denominata|
|Valore predefinito|nessuno|
|Input pipeline accettato?|false|
|Caratteri jolly accettati?|false|

## DatabaseServerPassword

Nome utente e password per il database SQL di Azure.

|Parametro|Valore predefinito|
|---|---|
|Alias|nessuno|
|Obbligatorio?|false|
|Posizione|denominata|
|Valore predefinito|nessuno|
|Input pipeline accettato?|false|
|Caratteri jolly accettati?|false|

## SendHostMessagesToOutput

Se impostato su true, stampa i messaggi dallo script al flusso di output.

|Parametro|Valore predefinito|
|---|---|
|Alias|nessuno|
|Obbligatorio?|false|
|Posizione|denominata|
|Valore predefinito|false|
|Input pipeline accettato?|false|
|Caratteri jolly accettati?|false|

## Osservazioni

Per una spiegazione completa sull'uso dello script per creare ambienti di sviluppo e test, vedere [Uso degli script di Windows PowerShell per la pubblicazione in ambienti di sviluppo e test](vs-azure-tools-publishing-using-powershell-scripts.md).

Il file di configurazione JSON specifica i dettagli degli elementi da distribuire. Include le informazioni specificate al momento della creazione del progetto, ad esempio il nome e il nome utente per il sito Web. Se esiste, include anche il database di cui eseguire il provisioning. Il codice seguente mostra un esempio di file di configurazione JSON:

	{
	    "environmentSettings": {
	        "webSite": {
	            "name": "WebApplication10554",
	            "location": "West US"
	        },
	        "databases": [
	            {
	                "connectionStringName": "DefaultConnection",
	                "databaseName": "WebApplication10554_db",
	                "serverName": "iss00brc88",
	                "user": "sqluser2",
	                "password": "",
	                "edition": "",
	                "size": "",
	                "collation": "",
	                "location": "West US"
	            }
	        ]
	    }
	}

È possibile modificare il file di configurazione JSON per cambiare gli elementi da distribuire. La sezione webSite è obbligatoria, ma la sezione del database è facoltativa.

## Passaggi successivi

Per ulteriori informazioni, vedere [Publish-WebApplicationVM (script di Windows PowerShell)](vs-azure-tools-publish-webapplicationvm.md)

<!---HONumber=AcomDC_0817_2016-->