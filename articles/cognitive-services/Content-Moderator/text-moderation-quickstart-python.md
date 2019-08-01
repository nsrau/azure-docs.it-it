---
title: 'Guida introduttiva: Analizzare il contenuto di testo in Python - Content Moderator'
titleSuffix: Azure Cognitive Services
description: Come analizzare il contenuto del testo alla ricerca di materiale non appropriato di diverso tipo usando Content Moderator SDK per Python
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 07/03/2019
ms.author: pafarley
ms.openlocfilehash: ceaaa9bdb5f6e5c977e0c1a71af080f22d52a5e7
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68561217"
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

```bash
pip install azure-cognitiveservices-vision-contentmoderator
```

## <a name="import-modules"></a>Importare i moduli

Creare un nuovo script Python denominato _ContentModeratorQS.py_ e aggiungere il codice seguente per importare le parti necessarie dell'SDK. È incluso il modulo di pretty-printing per agevolare la lettura della risposta JSON.

[!code-python[](~/cognitive-services-content-moderator-samples/documentation-samples/python/content_moderator_quickstart.py?name=imports)]


## <a name="initialize-variables"></a>Inizializzare le variabili

Aggiungere quindi le variabili per l'URL dell'endpoint e la chiave di sottoscrizione di Content Moderator. Aggiungere il nome `CONTENT_MODERATOR_SUBSCRIPTION_KEY` alle variabili di ambiente con la chiave di sottoscrizione come valore. Per l'URL dell'endpoint di base, aggiungere `CONTENT_MODERATOR_ENDPOINT` alle variabili di ambiente con l'URL specifico dell'area come valore, ad esempio `https://westus.api.cognitive.microsoft.com`. Le chiavi di sottoscrizione per la versione di valutazione gratuita vengono generate nell'area **westus**.

[!code-python[](~/cognitive-services-content-moderator-samples/documentation-samples/python/content_moderator_quickstart.py?name=authentication)]

Una stringa di testo su più righe da un file verrà moderata. Includere il file [content_moderator_text_moderation.txt](https://github.com/Azure-Samples/cognitive-services-content-moderator-samples/blob/master/documentation-samples/python/content_moderator_text_moderation.txt)nella cartella radice locale e aggiungere il nome del file alle variabili:

[!code-python[](~/cognitive-services-content-moderator-samples/documentation-samples/python/content_moderator_quickstart.py?name=textModerationFile)]

## <a name="query-the-moderator-service"></a>Eseguire una query sul servizio Moderator

Creare un'istanza di **ContentModeratorClient** usando la chiave di sottoscrizione e l'URL dell'endpoint. 

[!code-python[](~/cognitive-services-content-moderator-samples/documentation-samples/python/content_moderator_quickstart.py?name=client)]

Usare quindi il client con l'istanza di **TextModerationOperations** membro per chiamare l'API di moderazione con la funzione `screen_text`. Per altre informazioni su come eseguire la chiamata, vedere la documentazione di riferimento su **[screen_text](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.textmoderationoperations?view=azure-python)** .

[!code-python[](~/cognitive-services-content-moderator-samples/documentation-samples/python/content_moderator_quickstart.py?name=textModeration)]

## <a name="check-the-printed-response"></a>Verificare la risposta stampata

Eseguire l'esempio e verificare la risposta. Se viene completato correttamente, verrà restituita un'istanza della **schermata**. Di seguito è riportato il risultato di un'esecuzione riuscita:

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
