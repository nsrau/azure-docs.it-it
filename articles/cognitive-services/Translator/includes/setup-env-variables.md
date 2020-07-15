---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: 54ee19f3e278b424384ff55d7065713584235df1
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86144239"
---
## <a name="set-up"></a>Configurare

### <a name="create-a-translator-resource"></a>Creare una risorsa per Translator

I Servizi cognitivi di Azure sono rappresentati dalle risorse di Azure a cui si effettua la sottoscrizione. Creare una risorsa per Translator usando il [portale di Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) o l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) nel computer locale. È anche possibile:

* Visualizzare una risorsa esistente nel [portale di Azure](https://portal.azure.com/).

Dopo aver ottenuto una chiave dalla sottoscrizione di valutazione o dalla risorsa, creare due [variabili di ambiente](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication):

* `TRANSLATOR_TEXT_SUBSCRIPTION_KEY`: chiave di sottoscrizione per la risorsa di Translator.
* `TRANSLATOR_TEXT_ENDPOINT`: endpoint globale per Translator. Usare `https://api.cognitive.microsofttranslator.com/`.
