---
title: Tradurre in ambienti protetti da firewall - API Traduzione testuale
titlesuffix: Azure Cognitive Services
description: Informazioni su come tradurre in ambienti protetti da firewall IP con l'API Traduzione testuale.
services: cognitive-services
author: rajdeep-in
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: v-pawal
ms.openlocfilehash: 6507d133051decc8f97ffab42ec77b0ab0989cc8
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/30/2019
ms.locfileid: "66389528"
---
# <a name="how-to-translate-behind-ip-firewalls-with-the-translator-text-api"></a>Come tradurre in ambienti protetti da firewall IP con l'API Traduzione testuale

L'API Traduzione testuale consente di tradurre in ambienti protetti da firewall usando il filtro basato su nome di dominio o IP. Il metodo preferito è il filtro basato su nome di dominio. **Non è consigliabile** eseguire Microsoft Translator in un ambiente protetto da firewall con filtro basato su IP. Il programma di installazione è soggetto a errori in futuro senza preavviso.

## <a name="translator-ip-addresses"></a>Indirizzi IP di Translator
Gli indirizzi IP per api.cognitive.microsofttranslator.com, ovvero l'API Traduzione testuale Microsoft Translator al 20 novembre 2018 sono i seguenti:

* **Asia Pacifico:** 40.90.139.163, 104.44.89.44
* **Europa:** 40.90.138.4, 40.90.141.99
* **America del Nord:** 40.90.139.36, 40.90.139.2


## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"]
> [Tradurre in ambienti protetti da firewall IP nella chiamata API di Translator](reference/v3-0-translate.md)
