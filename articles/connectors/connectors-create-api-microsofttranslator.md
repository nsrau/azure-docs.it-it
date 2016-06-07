<properties
    pageTitle="Aggiungere l'API di Microsoft Translator a PowerApps Enterprise o alle app per la logica | Microsoft Azure"
    description="Panoramica del connettore Microsoft Translator con i parametri dell'API REST."
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
   ms.date="05/18/2016"
   ms.author="mandia"/>

# Introduzione al connettore Microsoft Translator
Connettersi a Microsoft Translator per tradurre il testo, individuare una lingua e così via. Il connettore Microsoft Translator può essere usato da:

- App per la logica 
- PowerApps

> [AZURE.SELECTOR]
- [App per la logica](../articles/connectors/connectors-create-api-microsofttranslator.md)
- [PowerApps Enterprise](../articles/power-apps/powerapps-create-api-microsofttranslator.md)

Con Microsoft Translator è possibile:

- Creare il flusso aziendale in base ai dati ottenuti da Microsoft Translator. 
- Usare le azioni per tradurre il testo, individuare una lingua e così via. Queste azioni ottengono una risposta e quindi rendono l'output disponibile per altre azioni. Ad esempio, quando viene creato un nuovo file in Dropbox, è possibile tradurre il testo nel file in un'altra lingua tramite Microsoft Translator.
- Aggiungere il connettore Microsoft Translator a PowerApps Enterprise, in modo che gli utenti possano usarlo nelle proprie app. 

Per informazioni su come aggiungere un connettore in PowerApps Enterprise, vedere la pagina relativa alla [registrazione dei connettori in PowerApps](../power-apps/powerapps-register-from-available-apis.md).

Per aggiungere un'operazione nelle app per la logica, vedere [Creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Trigger e azioni
Microsoft Translator include le azioni seguenti. Non sono disponibili trigger.

Trigger | Azioni
--- | ---
Nessuno | <ul><li>Rileva lingua</li><li>Sintesi vocale</li><li>Traduci testo</li><li>Ottieni lingue</li><li>Ottieni lingue per comandi vocali</li></ul>

Tutti i connettori supportano dati nei formati JSON e XML.


## Creare una connessione a Microsoft Translator

>[AZURE.INCLUDE [Passaggi per la creazione di una connessione a Microsoft Translator](../../includes/connectors-create-api-microsofttranslator.md)]


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
Converte un testo specificato in parlato come flusso audio in formato wave. ```GET: /Speak```

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
Recupera le lingue disponibili per la sintesi vocale. ```GET: /SpeakLanguages```

Non sono disponibili parametri per questa chiamata.

#### Risposta
|Nome|Descrizione|
|---|---|
|200|OK|
|default|Operazione non riuscita.|

## Definizioni oggetto

#### Language: modello di lingua per le lingue traducibili di Microsoft Translator

|Nome proprietà | Tipo di dati | Obbligatorio|
|---|---|---|
|Codice|string|no|
|Nome|string|no|


## Passaggi successivi

[Creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md).

Tornare all'[elenco di API](apis-list.md).


<!--References-->
[5]: https://datamarket.azure.com/developer/applications/
[6]: ./media/connectors-create-api-microsofttranslator/register-your-application.png

<!---HONumber=AcomDC_0525_2016-->