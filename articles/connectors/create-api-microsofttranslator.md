<properties
	pageTitle="Aggiungere l'API di Microsoft Translator a PowerApps Enterprise o alle app per la logica | Microsoft Azure"
	description="Panoramica dell'API di Microsoft Translator con i parametri dell'API REST."
	services=""
    suite=""
	documentationCenter="" 
	authors="MandiOhlinger"
	manager="erikre"
	editor=""
	tags="connectors"/>

<tags
   ms.service="multiple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="02/25/2016"
   ms.author="mandia"/>

# Introduzione all'API di Microsoft Translator
Connettersi a Microsoft Translator per tradurre il testo, individuare una lingua e così via. L'API di Microsoft Translator può essere usata da:

- PowerApps 
- App per la logica 

Con Microsoft Translator è possibile:

- Creare il flusso aziendale in base ai dati ottenuti da Microsoft Translator. 
- Usare le azioni per tradurre il testo, individuare una lingua e così via. Queste azioni ottengono una risposta e quindi rendono l'output disponibile per altre azioni. Ad esempio, quando viene creato un nuovo file in Dropbox, è possibile tradurre il testo nel file in un'altra lingua tramite Microsoft Translator.
- Aggiungere l'API di Microsoft Translator a PowerApps Enterprise, in modo che gli utenti possano usarla nelle proprie app. 

Per informazioni su come aggiungere un'API in PowerApps Enterprise, vedere l'articolo relativo alla [registrazione di un'API in PowerApps](../power-apps/powerapps-register-from-available-apis.md).

Per aggiungere un'operazione nelle app per la logica, vedere [Creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Trigger e azioni
Microsoft Translator include le azioni seguenti. Non sono disponibili trigger.

Trigger | Azioni
--- | ---
Nessuno | <ul><li>Rileva lingua</li><li>Testo di cui eseguire il riconoscimento vocale</li><li>Traduci testo</li><li>Recupera lingue</li><li>Recupera lingue per il riconoscimento vocale</li></ul>

Tutte le API supportano i dati nei formati JSON e XML.

## Creare una connessione a Microsoft Translator

### Aggiungere configurazioni aggiuntive in PowerApps
Quando si aggiunge Microsoft Translator a PowerApps Enterprise, immettere i valori **ID client** e **Segreto client** dell'applicazione Microsoft Translator. Se non si ha un'applicazione Microsoft Translator, è possibile crearne una:

1. Passare alla [Pagina per gli sviluppatori di Azure Data Market][5] e accedere con il proprio account Microsoft. 

2. Selezionare l'opzione per **registrare l'applicazione**:

	1. Immettere un valore per **ID client**.
	2. Immettere il **nome** dell'applicazione.
	3. Immettere un valore fittizio per l'**URL di reindirizzamento**, Ad esempio, immettere: *https://contosoredirecturl*.
	4. Immettere una **descrizione**.
	5. Selezionare **Create**.  

	![Registrare l'applicazione][6]

A questo punto, copiare e incollare i valori **ID client** e **Segreto client** nella configurazione di Translator nel portale di Azure.


## Riferimento all'API REST di Swagger
Si applica alla versione 1.0.

### Rileva lingua    
Rileva la lingua di origine di un testo specificato. ```GET: /Detect```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|query|string|yes|query|nessuno |Testo di cui verrà identificata la lingua|

#### Risposta
|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


### Testo di cui eseguire il riconoscimento vocale    
Converte un testo specificato in parlato come un flusso audio in formato wave. ```GET: /Speak```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|query|string|yes|query|nessuno |Testo da convertire|
|Lingua|string|yes|query|nessuno |Codice della lingua in cui generare il riconoscimento vocale, ad esempio 'it-IT'|

#### Risposta
|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


### Traduci testo    
Traduce il testo in una lingua specificata tramite Microsoft Translator. ```GET: /Translate```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|query|string|yes|query|nessuno |Testo da tradurre|
|languageTo|string|yes|query| nessuno|Codice lingua di destinazione, ad esempio 'fr'|
|languageFrom|string|no|query|nessuno |Codice lingua di origine, ad esempio 'it'. Se non viene specificato, Microsoft Translator tenterà di rilevarlo automaticamente.|
|category|string|no|query|generale |Categoria di traduzione (valore predefinito: 'generale')|

#### Risposta
|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


### Recupera lingue    
Recupera tutte le lingue supportate da Microsoft Translator. ```GET: /TranslatableLanguages```

Non sono disponibili parametri per questa chiamata.

#### Risposta
|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|


### Recupera lingue per il riconoscimento vocale    
Recupera le lingue disponibili per il riconoscimento vocale. ```GET: /SpeakLanguages```

Non sono disponibili parametri per questa chiamata.

#### Risposta
|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|

## Definizioni oggetto

#### Language: modello di lingua per le lingue traducibili di Microsoft Translator

| Nome | Tipo di dati | Obbligatorio|
|---|---|---|
|Codice|string|no|
|Nome|string|no|


## Passaggi successivi
Dopo aver aggiunto l'API di Microsoft Translator a PowerApps Enterprise, [concedere autorizzazioni agli utenti](../power-apps/powerapps-manage-api-connection-user-access.md) per l'uso dell'API nelle app.

[Creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md).


<!--References-->
[5]: https://datamarket.azure.com/developer/applications/
[6]: ./media/create-api-microsofttranslator/register-your-application.png

<!---HONumber=AcomDC_0302_2016-->

