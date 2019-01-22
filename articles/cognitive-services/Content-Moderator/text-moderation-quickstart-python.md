---
title: 'Avvio rapido: Analizzare il contenuto di testo in Python - Content Moderator'
titlesuffix: Azure Cognitive Services
description: Come analizzare il contenuto del testo alla ricerca di materiale non appropriato di diverso tipo usando Content Moderator SDK per Python
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: quickstart
ms.date: 01/10/2019
ms.author: pafarley
ms.openlocfilehash: 6aace9301050877d452b69bd7d1c741f50dae558
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/14/2019
ms.locfileid: "54264094"
---
# <a name="quickstart-analyze-text-content-for-objectionable-material-in-python"></a>Avvio rapido: Analizzare il contenuto del testo per individuare materiale non appropriato in Python

Questo articolo fornisce informazioni ed esempi di codice utili per iniziare a usare Content Moderator SDK per Python. Si imparerà a filtrare e classificare il contenuto di testo in base a determinati termini allo scopo di sottoporre a moderazione materiale potenzialmente sgradevole.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare. 

## <a name="prerequisites"></a>Prerequisiti
- Una chiave di sottoscrizione di Content Moderator. Seguire le istruzioni in [Creare un account Servizi cognitivi](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) per effettuare la sottoscrizione a Content Moderator e ottenere la chiave.
- [Python 2.7 o 3.5 e versioni successive](https://www.python.org/downloads/)
- Strumento [pip](https://pip.pypa.io/en/stable/installing/)

## <a name="get-the-content-moderator-sdk"></a>Ottenere Content Moderator SDK

Installare Content Moderator SDK per Python aprendo un prompt dei comandi ed eseguendo il comando seguente:

```
pip install azure-cognitiveservices-vision-contentmoderator
```

## <a name="import-modules"></a>Importare i moduli

Creare un nuovo script Python denominato _ContentModeratorQS.py_ e aggiungere il codice seguente per importare le parti necessarie dell'SDK.

[!code-python[](~/cognitive-services-content-moderator-samples/documentation-samples/python/text-moderation-quickstart-python.py?range=1-10)]

Importare anche la funzione di riformattazione per gestire l'output finale.

[!code-python[](~/cognitive-services-content-moderator-samples/documentation-samples/python/text-moderation-quickstart-python.py?range=12)]


## <a name="initialize-variables"></a>Inizializzare le variabili

Aggiungere quindi le variabili per l'URL dell'endpoint e la chiave di sottoscrizione di Content Moderator. Sarà necessario sostituire `<your subscription key>` con il valore della chiave. Potrebbe anche essere necessario modificare il valore di `endpoint_url` per usare l'identificatore di area corrispondente alla chiave di sottoscrizione. Le chiavi di sottoscrizione per la versione di valutazione gratuita vengono generate nell'area **westus**.

[!code-python[](~/cognitive-services-content-moderator-samples/documentation-samples/python/text-moderation-quickstart-python.py?range=14-16)]


Per semplicità, si analizzerà il testo direttamente dallo script. Definire una nuova stringa di contenuto di testo da moderare:

[!code-python[](~/cognitive-services-content-moderator-samples/documentation-samples/python/text-moderation-quickstart-python.py?range=18-21)]


## <a name="query-the-moderator-service"></a>Eseguire una query sul servizio Moderator

Creare un'istanza di **ContentModeratorClient** usando la chiave di sottoscrizione e l'URL dell'endpoint. Usare quindi l'istanza di **TextModerationOperations** membro per chiamare l'API di moderazione. Per altre informazioni su come eseguire la chiamata, vedere la documentazione di riferimento su **[screen_text](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.textmoderationoperations?view=azure-python#screen-text)**.

[!code-python[](~/cognitive-services-content-moderator-samples/documentation-samples/python/text-moderation-quickstart-python.py?range=23-36)]

## <a name="print-the-response"></a>Stampare la risposta

Infine, controllare che la chiamata sia stata completata correttamente e abbia restituito un'istanza di **Screen**. Stampare quindi i dati restituiti nella console.

[!code-python[](~/cognitive-services-content-moderator-samples/documentation-samples/python/text-moderation-quickstart-python.py?range=38-39)]


Il testo di esempio usato in questa guida di avvio rapido produce l'output seguente:

```console
{'auto_corrected_text': '" Is this a garbage email abide@ abed. com, phone: '
                        '6657789887, IP: 255. 255. 255. 255, 1 Microsoft Way, '
                        'Redmond, WA 98052. Crap is the profanity here. Is '
                        'this information PII? phone 3144444444\\ n"',
 'classification': {'category1': {'score': 0.00025233393535017967},
                    'category2': {'score': 0.18468093872070312},
                    'category3': {'score': 0.9879999756813049},
                    'review_recommended': True},
 'language': 'eng',
 'normalized_text': '" Is this a garbage email abide@ abed. com, phone: '
                    '6657789887, IP: 255. 255. 255. 255, 1 Microsoft Way, '
                    'Redmond, WA 98052. Crap is the profanity here. Is this '
                    'information PII? phone 3144444444\\ n"',
 'original_text': '"Is this a grabage email abcdef@abcd.com, phone: '
                  '6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, '
                  'WA 98052. Crap is the profanity here. Is this information '
                  'PII? phone 3144444444\\n"',
 'pii': {'address': [{'index': 82,
                      'text': '1 Microsoft Way, Redmond, WA 98052'}],
         'email': [{'detected': 'abcdef@abcd.com',
                    'index': 25,
                    'sub_type': 'Regular',
                    'text': 'abcdef@abcd.com'}],
         'ipa': [{'index': 65, 'sub_type': 'IPV4', 'text': '255.255.255.255'}],
         'phone': [{'country_code': 'US', 'index': 49, 'text': '6657789887'},
                   {'country_code': 'US', 'index': 177, 'text': '3144444444'}]},
 'status': {'code': 3000, 'description': 'OK'},
 'terms': [{'index': 118, 'list_id': 0, 'original_index': 118, 'term': 'crap'}],
 'tracking_id': 'b253515c-e713-4316-a016-8397662a3f1a'}
```

## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido è stato sviluppato un semplice script Python che usa il servizio Content Moderator per restituire informazioni rilevanti su un determinato esempio di testo. Nel prossimo articolo si imparerà il significato dei diversi flag e delle diverse classificazioni, in modo da poter decidere quali dati sono necessari e in che modo devono essere gestiti dall'app.

> [!div class="nextstepaction"]
> [Guida alla moderazione del testo](text-moderation-api.md)