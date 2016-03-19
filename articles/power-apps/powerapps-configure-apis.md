<properties
	pageTitle="Modificare o aggiornare le proprietà API PowerApps nel portale di Azure | Microsoft Azure"
	description="Aggiungere un'icona personalizzata, aggiornare il criterio XML o aggiornare la definizione Swagger dell'API PowerApps"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="MandiOhlinger"
	manager="dwrede"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="11/25/2015"
   ms.author="guayan"/>

# Aggiornare un'API esistente e le relative proprietà

L'API che si registra nell'ambiente del servizio app è essenzialmente un proxy al servizio back-end. Dopo aver creato l'API, è possibile modificarne le proprietà. Ad esempio, è possibile:

- Aggiungere un'icona personalizzata per l'API.
- Modificare il metodo di protezione del back-end utilizzato dall'API. 
- Aggiornare il nome visualizzato dell'API con un nome più descrittivo.


#### Prerequisiti per iniziare

- Iscrizione a [PowerApps Enterprise](powerapps-get-started-azure-portal.md).
- Creare un [ambiente di servizio app](powerapps-get-started-azure-portal.md).
- Registrare un'[API](powerapps-register-from-available-apis) nell’ambiente.

## Aggiungere un'icona personalizzata o un nome visualizzato più intuitivo

1. Nel [portale di Azure](https://portal.azure.com) aprire il pannello relativo all'API.
2. Selezionare **Tutte le impostazioni**.
3. In **Impostazioni** selezionare **Generali**: ![][11]

In generale, è possibile modificare le impostazioni seguenti:

Impostazione | Descrizione
--- | ---
Nome visualizzato | Questo nome viene utilizzato quando si elencano le *Connessioni disponibili* in PowerApps. Per impostazione predefinita, utilizza il nome risorsa dell'API, che potrebbe non essere il nome migliore per gli utenti PowerApps. È possibile immettere un nome visualizzato intuitivo. Ad esempio, è possibile denominarlo **Nuovi ordini cliente** o **Visualizzazione cronologia vendite**.  
URL icona | È possibile aggiungere un'icona personalizzata per l’API. L’icona viene utilizzata l'icona quando si elencano le *Connessioni disponibili* e le *Connessioni personali* in PowerApps. Per impostazione predefinita, viene utilizzata l'icona seguente: <br/><br/>![][12] <br/><br/>quando si utilizza un'icona personalizzata:<br/><ul><li>l'URL dell'icona deve essere accessibile pubblicamente.</li><li>Può trattarsi di un file PNG o SVG. Quando si utilizza un file PNG, è preferibile che abbia dimensioni di 40 x 40 pixel.</li></ul>
Schema dell'URL | Scegliere gli schemi che si desidera siano supportati dall'API. È possibile scegliere **HTTP**, **HTTPS** o **HTTP e HTTPS**. Per impostazione predefinita, HTTP e HTTPS sono abilitati. <br/><br/>L'ambiente del servizio app consente di configurare automaticamente lo schema in base alla configurazione back-end. Nel caso di ulteriori operazioni che è necessario configurare, è possibile sviluppare o modificare il servizio back-end. 
Autenticazione con il servizio back-end | Dopo la registrazione del servizio back-end nell'ambiente del servizio app, è buona norma proteggere il back-end in modo che i client lo richiamino solo tramite l'API. In base a dove viene distribuito il back-end, sono disponibili le seguenti opzioni:<br/><br/><ul><li><strong>Accessibile solo tramite l'API</strong>: questa opzione è disponibile solo quando il back-end viene distribuito nell'ambiente del servizio app. Se selezionata, disabilita qualsiasi nome host nel back-end. Poiché il proxy dell'API viene inoltre eseguito nello stesso ambiente del servizio app, può ancora accedere al back-end.</li><li><strong>Autenticazione di base HTTP</strong>: questa opzione è disponibile indipendentemente dal punto di distribuzione del back-end. Quando è selezionata, immettere un nome utente e una password. Quando il proxy chiama il back-end, usa l’autenticazione di base HTTP per passare il nome utente e la password nell'intestazione di autorizzazione HTTP. Infine, il servizio back-end deve verificare (autenticare) il nome utente e la password immessi.<br/><br/>Per ulteriori informazioni sull'implementazione dell’autenticazione di base HTTP in ASP.NET Web API 2, vedere [Filtri di autenticazione in ASP.NET Web API 2](http://www.asp.net/web-api/overview/security/authentication-filters).</li></ul>


## Aggiornare lo Swagger dell’API

1. Nel [portale di Azure](https://portal.azure.com) aprire il pannello relativo all'API.
2. Selezionare **Tutte le impostazioni**.
3. In **Impostazioni**, selezionare **Definizione dell'API**: ![][13]

**Swagger 2.0** è il formato di definizione API supportato. La definizione dell'API corrente è nell'editor JSON integrato. È possibile eseguire modifiche in linea o caricare un nuovo file JSON. Dopo aver **salvato** le modifiche, gli eventuali errori vengono visualizzati nel pannello, compresi gli errori con la definizione dell'API.

- Per ulteriori informazioni su Swagger 2.0, vedere il [sito Web ufficiale di Swagger](http://swagger.io).
- Per ulteriori informazioni su come ottenere Swagger 2.0 quando si sviluppa l'API, vedere:  
	- [Creare un'app per le API ASP.NET nel servizio app di Azure](../app-service-dotnet-create-api-app.md)
	- [Compilare e distribuire un'app per le API Java nel servizio app di Azure](../app-service-api-java-api-app.md)
	- [Compilare e distribuire un'app per le API Node.js in Servizio app di Azure](../app-service-api-nodejs-api-app.md)
	- [Personalizzare le definizioni delle API generate da Swashbuckle](../app-service-api-dotnet-swashbuckle-customize.md)
- Per ulteriori informazioni sulle procedure consigliate dell'utilizzo di Swagger 2.0 per PowerApps, vedere [Sviluppare un'API per PowerApps](powerapps-develop-api.md).

## Aggiornare i criteri XML dell'API

1. Nel [portale di Azure](https://portal.azure.com) aprire il pannello relativo all'API.
2. Selezionare **Tutte le impostazioni**.
3. In **Impostazioni** selezionare **Criterio**: ![][14]

Questo criterio è lo stesso supportato da [Gestione API di Azure](https://azure.microsoft.com/services/api-management/). Il criterio corrente è nell'editor XML integrato. È possibile modificare in linea o caricare un nuovo file XML. Dopo aver **salvato** le modifiche, gli eventuali errori vengono visualizzati in questo pannello, compresi gli errori del criterio dell'API.

[Criteri in Gestione API di Azure](../api-management-howto-policies.md) è un'ottima risorsa per ulteriori informazioni sulla configurazione e sui criteri.


## Riepilogo e passaggi successivi
Dopo aver creato l'API, è possibile utilizzare i passaggi in questo argomento per modificare le impostazioni e personalizzare alcune impostazioni.

Per altre informazioni sulle PowerApps, vedere gli articoli e le risorse correlati seguenti.

- [Configurare un'API per la connessione al back-end protetto da ADD ](powerapps-configure-apis-aad.md)
- [Sviluppare un'API per PowerApps](powerapps-develop-api.md)

[11]: ./media/powerapps-configure-apis/api-settings-general.png
[12]: ./media/powerapps-configure-apis/api-default-icon.png
[13]: ./media/powerapps-configure-apis/api-settings-api-definition.png
[14]: ./media/powerapps-configure-apis/api-settings-policy.png

<!---HONumber=AcomDC_0128_2016-->