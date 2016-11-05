---
title: Aggiungere Microsoft Translator alle app per la logica | Microsoft Docs
description: Panoramica del connettore Microsoft Translator con i parametri dell'API REST.
services: ''
suite: ''
documentationcenter: ''
author: MandiOhlinger
manager: erikre
editor: ''
tags: connectors

ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/18/2016
ms.author: mandia

---
# Introduzione al connettore Microsoft Translator
Connettersi a Microsoft Translator per tradurre il testo, individuare una lingua e così via. Con Microsoft Translator è possibile:

* Creare il flusso aziendale in base ai dati ottenuti da Microsoft Translator.
* Usare le azioni per tradurre il testo, individuare una lingua e così via. Queste azioni ottengono una risposta e quindi rendono l'output disponibile per altre azioni. Ad esempio, quando viene creato un nuovo file in Dropbox, è possibile tradurre il testo nel file in un'altra lingua tramite Microsoft Translator.

Per aggiungere un'operazione alle app per la logica, vedere [Creare una nuova app per la logica che connette servizi SaaS](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Trigger e azioni
Microsoft Translator include le azioni seguenti. Non sono disponibili trigger.

| Trigger | Azioni |
| --- | --- |
| None |<ul><li>Detect language (Rileva lingua)</li><li>Text to speech (Sintesi vocale)</li><li>Translate text (Traduci testo)</li><li>Get languages (Ottieni lingue)</li><li>Get speech languages (Ottieni lingue per il riconoscimento vocale)</li></ul> |

Tutti i connettori supportano dati nei formati JSON e XML.

## Creare una connessione a Microsoft Translator
> [!INCLUDE [Passaggi per la creazione di una connessione a Microsoft Translator](../../includes/connectors-create-api-microsofttranslator.md)]
> 
> 

## Riferimento all'API REST di Swagger
Si applica alla versione 1.0.

### Rileva lingua
Rileva la lingua di origine di un testo specificato. ```GET: /Detect```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| query |string |yes |query |nessuno |Testo di cui verrà identificata la lingua |

#### Response
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| default |Operazione non riuscita. |

### Testo di cui eseguire il riconoscimento vocale
Converte un testo specificato in parlato come flusso audio in formato Wave. ```GET: /Speak```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| query |string |yes |query |nessuno |Testo da convertire |
| Lingua |stringa |yes |query |nessuno |Codice della lingua in cui generare il riconoscimento vocale, ad esempio 'it-IT' |

#### Response
| Name | Descrizione |
| --- | --- |
| 200 |OK |
| default |Operazione non riuscita. |

### Traduci testo
Traduce il testo in una lingua specificata tramite Microsoft Translator. ```GET: /Translate```

| Name | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| query |string |yes |query |nessuno |Testo da tradurre |
| languageTo |stringa |yes |query |nessuno |Codice lingua di destinazione, ad esempio 'fr' |
| languageFrom |string |no |query |nessuno |Codice lingua di origine, ad esempio 'it'. Se non viene specificato, Microsoft Translator tenterà di rilevarlo automaticamente. |
| category |stringa |no |query |generale |Categoria di traduzione (valore predefinito: 'generale') |

#### Response
| Name | Descrizione |
| --- | --- |
| 200 |OK |
| default |Operazione non riuscita. |

### Recupera lingue
Recupera tutte le lingue supportate da Microsoft Translator. ```GET: /TranslatableLanguages```

Non sono disponibili parametri per questa chiamata.

#### Response
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| default |Operazione non riuscita. |

### Recupera lingue per il riconoscimento vocale
Recupera le lingue disponibili per la sintesi vocale. ```GET: /SpeakLanguages```

Non sono disponibili parametri per questa chiamata.

#### Response
| Name | Descrizione |
| --- | --- |
| 200 |OK |
| default |Operazione non riuscita. |

## Definizioni oggetti
#### Language: modello di lingua per le lingue traducibili di Microsoft Translator
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| Codice |stringa |no |
| Nome |stringa |no |

## Passaggi successivi
[Creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md).

Tornare all'[elenco di API](apis-list.md).

<!--References-->
[5]: https://datamarket.azure.com/developer/applications/
[6]: ./media/connectors-create-api-microsofttranslator/register-your-application.png

<!---HONumber=AcomDC_0824_2016-->