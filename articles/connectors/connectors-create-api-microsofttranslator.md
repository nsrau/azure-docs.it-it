---
title: Aggiungere Microsoft Translator nelle app per la logica | Documentazione Microsoft
description: Panoramica del connettore Microsoft Translator con i parametri dell&quot;API REST.
services: 
suite: 
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: da782baf-8bf8-4973-8238-e469865f5328
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/07/2016
ms.author: mandia
translationtype: Human Translation
ms.sourcegitcommit: b92f954680603891ced503a1134791312b5214f0
ms.openlocfilehash: add307420b9e16a60b0e57b0ba08188b2509978e


---
# <a name="get-started-with-the-microsoft-translator-connector"></a>Introduzione al connettore Microsoft Translator
Connettersi a Microsoft Translator per tradurre il testo, individuare una lingua e così via. Con Microsoft Translator è possibile: 

* Creare il flusso aziendale in base ai dati ottenuti da Microsoft Translator. 
* Usare le azioni per tradurre il testo, individuare una lingua e così via. Queste azioni ottengono una risposta e quindi rendono l'output disponibile per altre azioni. Ad esempio, quando viene creato un nuovo file in Dropbox, è possibile tradurre il testo nel file in un'altra lingua tramite Microsoft Translator.

Per aggiungere un'operazione nelle app per la logica, vedere [Creare un'app per la logica](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Trigger e azioni
Microsoft Translator include le azioni seguenti. Non sono disponibili trigger.

| Trigger | Azioni |
| --- | --- |
| Nessuno |<ul><li>Rileva lingua</li><li>Testo di cui eseguire il riconoscimento vocale</li><li>Traduci testo</li><li>Recupera lingue</li><li>Recupera lingue per il riconoscimento vocale</li></ul> |

Tutti i connettori supportano dati nei formati JSON e XML.

## <a name="create-a-connection-to-microsoft-translator"></a>Creare una connessione a Microsoft Translator
> [!INCLUDE [Steps to create a connection to Microsoft Translator](../../includes/connectors-create-api-microsofttranslator.md)]
> 
> 

## <a name="swagger-rest-api-reference"></a>Riferimento all'API REST di Swagger
Si applica alla versione 1.0.

### <a name="detect-language"></a>Rileva lingua
Rileva la lingua di origine di un testo specificato.  
```GET: /Detect```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| query |string |yes |query |Nessuno |Testo di cui verrà identificata la lingua |

#### <a name="response"></a>Risposta
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| default |Operazione non riuscita. |

### <a name="text-to-speech"></a>Testo di cui eseguire il riconoscimento vocale
Converte un testo specificato in parlato come flusso audio in formato wave.  
```GET: /Speak```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| query |string |yes |query |Nessuno |Testo da convertire |
| Lingua |string |yes |query |nessuno |Codice della lingua in cui generare il riconoscimento vocale, ad esempio 'it-IT' |

#### <a name="response"></a>Response
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| default |Operazione non riuscita. |

### <a name="translate-text"></a>Traduci testo
Traduce il testo in una lingua specificata tramite Microsoft Translator.  
```GET: /Translate```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| query |string |yes |query |Nessuno |Testo da tradurre |
| languageTo |string |yes |query |Nessuno |Codice lingua di destinazione, ad esempio 'fr' |
| languageFrom |string |no |query |Nessuno |Codice lingua di origine, ad esempio 'it'. Se non viene specificato, Microsoft Translator tenterà di rilevarlo automaticamente. |
| category |string |no |query |generale |Categoria di traduzione (valore predefinito: 'generale') |

#### <a name="response"></a>Risposta
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| default |Operazione non riuscita. |

### <a name="get-languages"></a>Recupera lingue
Recupera tutte le lingue supportate da Microsoft Translator.  
```GET: /TranslatableLanguages```

Non sono disponibili parametri per questa chiamata. 

#### <a name="response"></a>Risposta
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| default |Operazione non riuscita. |

### <a name="get-speech-languages"></a>Recupera lingue per il riconoscimento vocale
Recupera le lingue disponibili per la sintesi vocale.  
```GET: /SpeakLanguages``` 

Non sono disponibili parametri per questa chiamata.

#### <a name="response"></a>Risposta
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| default |Operazione non riuscita. |

## <a name="object-definitions"></a>Definizioni oggetto
#### <a name="language-language-model-for-microsoft-translator-translatable-languages"></a>Language: modello di lingua per le lingue traducibili di Microsoft Translator
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| Codice |string |no |
| Nome |string |no |

## <a name="next-steps"></a>Passaggi successivi
[Creare un'app per la logica](../logic-apps/logic-apps-create-a-logic-app.md).

Tornare all' [elenco di API](apis-list.md).

<!--References-->
[5]: https://datamarket.azure.com/developer/applications/
[6]: ./media/connectors-create-api-microsofttranslator/register-your-application.png



<!--HONumber=Feb17_HO2-->


