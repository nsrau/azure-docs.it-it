---
title: Tradurre dietro i firewall-Translator
titleSuffix: Azure Cognitive Services
description: Il convertitore di servizi cognitivi di Azure può tradurre dietro i firewall usando il filtro IP o il nome di dominio.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: swmachan
ms.openlocfilehash: 78a53c99f5f184c1b6b45d59d86c23efb898d7dc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "83996959"
---
# <a name="how-to-translate-behind-ip-firewalls-with-translator"></a>Come tradurre dietro i firewall IP con Translator

Translator può tradurre dietro i firewall usando il nome di dominio o il filtro IP. Il metodo preferito è il filtro basato su nome di dominio. **Non è consigliabile** eseguire Microsoft Translator in un ambiente protetto da firewall con filtro basato su IP. Il programma di installazione è soggetto a errori in futuro senza preavviso.

## <a name="translator-ip-addresses"></a>Indirizzi IP di Translator
Indirizzi IP per api.cognitive.microsofttranslator.com-Translator a partire dal 21 agosto 2019:

* **Asia Pacifico:** 20.40.125.208, 20.43.88.240, 20.184.58.62, 40.90.139.163, 104.44.89.44
* **Europa:** 40.90.138.4, 40.90.141.99, 51.105.170.64, 52.155.218.251
* **America del Nord:** 40.90.139.36, 40.90.139.2, 40.119.2.134, 52.224.200.129, 52.249.207.163

## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"]
> [Tradurre dietro i firewall IP in Translator](reference/v3-0-translate.md)
