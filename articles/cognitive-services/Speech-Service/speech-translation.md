---
title: Informazioni sulla traduzione vocale
description: Panoramica delle funzionalità di traduzione vocale
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 04/28/2018
ms.author: v-jerkin
ms.openlocfilehash: f80c0f3cdc114b53c002266820e8d9b8773acc5d
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/28/2018
ms.locfileid: "47432190"
---
# <a name="about-the-speech-translation-api"></a>Informazioni sull'API Traduzione vocale

Microsoft Speech API consente di aggiungere la traduzione vocale end-to-end in tempo reale e in più lingue alle applicazioni, agli strumenti e ai dispositivi. La stessa API può essere usata sia per la traduzione vocale che per la traduzione con riconoscimento vocale.

Con l'API Traduzione vocale di Microsoft, le applicazioni client trasmettono l'audio vocale al servizio e ricevono un flusso di risultati. Questi risultati includono il testo riconosciuto nella lingua di origine e la traduzione nella lingua di destinazione. Possono essere fornite traduzioni provvisorie fino al completamento di un'espressione, quando viene fornita una traduzione finale.

È facoltativamente possibile preparare una versione audio sintetizzata della traduzione finale, abilitando la vera traduzione vocale.

L'API Traduzione vocale usa un protocollo WebSocket per fornire un canale di comunicazione full-duplex tra il client e il server, ma non è necessario occuparsi dei WebSocket, che vengono gestiti automaticamente da Speech SDK.

L'API Traduzione vocale usa le stesse tecnologie alla base di diversi prodotti e servizi Microsoft. Questo servizio è già usato da migliaia di aziende in tutto il mondo per le applicazioni e i flussi di lavoro.

## <a name="about-the-technology"></a>Informazioni sulla tecnologia

Il motore di traduzione di Microsoft sottostante prevede due diversi approcci: traduzione automatica statistica e traduzione automatica neurale. Il secondo, un approccio basato sull'intelligenza artificiale che si avvale di reti neurali, è l'approccio più moderno alla traduzione automatica. La traduzione automatica neurale fornisce traduzioni migliori, non solo più accurate, ma anche più scorrevoli e naturali. La ragione principale di questa fluidità consiste nel fatto che la traduzione automatica neurale usa il contesto completo di una frase per tradurre le parole.

Microsoft ha già eseguito la migrazione alla traduzione automatica neurale per le lingue più diffuse, avvalendosi della traduzione automatica statistica solo per le lingue di uso meno frequente. Tutte le [lingue disponibili per la traduzione vocale](language-support.md#speech-translation) usano la tecnologia basata sulla traduzione automatica neurale. La traduzione con riconoscimento vocale può usare la traduzione automatica statistica o la traduzione automatica neurale a seconda della coppia di lingue. Se la lingua di destinazione è supportata dalla traduzione automatica neurale, la traduzione completa è basata sulla traduzione automatica neurale. Se la lingua di destinazione non è supportata dalla traduzione automatica neurale, la traduzione è un ibrido di traduzione automatica neurale e traduzione automatica statistica, che usa la lingua inglese come "perno" tra le due lingue.

Le differenze tra i modelli sono interne al motore di traduzione. Gli utenti finali notano solo la migliore qualità della traduzione, specialmente per cinese, giapponese e arabo.

> [!NOTE]
> Per altre informazioni sulla tecnologia alla base del motore di traduzione di Microsoft, vedere [Machine Translation](https://www.microsoft.com/en-us/translator/mt.aspx) (Traduzione automatica).

## <a name="next-steps"></a>Passaggi successivi

* [Ottenere una sottoscrizione di valutazione gratuita del Servizio di riconoscimento vocale](https://azure.microsoft.com/try/cognitive-services/)
* [Informazioni sulla traduzione vocale in C#](how-to-translate-speech-csharp.md)
* [Informazioni sulla traduzione vocale in C++](how-to-translate-speech-cpp.md)
* [Informazioni sulla traduzione vocale in Java](how-to-translate-speech-java.md)
