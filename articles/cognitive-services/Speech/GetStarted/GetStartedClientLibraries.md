---
title: Introduzione all'API Riconoscimento vocale Microsoft tramite le librerie client | Microsoft Docs
description: Usare le librerie client di Riconoscimento vocale Microsoft in Servizi cognitivi Microsoft per sviluppare applicazioni per la conversione di audio parlato in testo.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/15/2017
ms.author: zhouwang
ms.openlocfilehash: 5abe5bc48c2bd73d0facf33e41a8076df2972153
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "35373801"
---
# <a name="get-started-with-speech-service-client-libraries"></a>Introduzione alle librerie client di Riconoscimento vocale

Oltre a eseguire richieste HTTP dirette tramite un'API REST, Riconoscimento vocale fornisce agli sviluppatori delle librerie client per il riconoscimento vocale in varie lingue. Le librerie client per il riconoscimento vocale:

- Supportano funzionalità più avanzate di riconoscimento vocale, ad esempio risultati intermedi in tempo reale, flussi audio lunghi (fino a 10 minuti) e riconoscimento continuo.
- Forniscono un'API semplice e idiomatica nella lingua scelta.
- Nascondono i dettagli delle comunicazioni di basso livello.

Attualmente sono disponibili le seguenti librerie client per il riconoscimento vocale:

- [Libreria desktop C#](GetStartedCSharpDesktop.md)
- [Libreria di servizi C#](GetStartedCSharpServiceLibrary.md)
- [Libreria JavaScript](GetStartedJSWebsockets.md)
- [Libreria Java per Android](GetStartedJavaAndroid.md)
- [Libreria Objective-C per iOS](Get-Started-ObjectiveC-iOS.md)

## <a name="additional-resources"></a>Risorse aggiuntive

- La pagina degli [esempi](../samples.md) fornisce degli esempi completi per usare le librerie client per il riconoscimento vocale.
- Se è necessaria una libreria client che non è ancora supportata, è possibile creare il proprio SDK. Implementare il [protocollo WebSocket di Riconoscimento vocale](../API-Reference-REST/websocketprotocol.md) nella piattaforma e usare la lingua scelta.

## <a name="license"></a>Licenza

Tutti gli SDK e gli esempi di Servizi cognitivi sono concessi su licenza con la licenza MIT. Per altre informazioni, vedere [License](https://github.com/Microsoft/Cognitive-Speech-STT-JavaScript/blob/master/LICENSE.md) (Licenza).
