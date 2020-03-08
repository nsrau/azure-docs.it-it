---
title: Linee guida per la denominazione delle parole chiave-servizio vocale
titleSuffix: Azure Cognitive Services
description: La creazione di una parola chiave efficace è essenziale per garantire che il dispositivo risponda in modo coerente e accurato.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/11/2019
ms.author: dapine
ms.openlocfilehash: 3c3943f7269fa2d0ed25acf2215549635b5f16ac
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78360713"
---
# <a name="guidelines-for-creating-an-effective-keyword"></a>Linee guida per la creazione di una parola chiave efficace

La creazione di una parola chiave efficace è essenziale per garantire che il dispositivo risponda in modo coerente e accurato. Personalizzare la parola chiave è un modo efficace per distinguere il dispositivo e rafforzare la personalizzazione. In questo articolo vengono illustrati alcuni principi guida per la creazione di una parola chiave efficace.

## <a name="choose-an-effective-keyword"></a>Scegliere una parola chiave valida

Quando si sceglie una parola chiave, tenere presenti le linee guida seguenti:

* La parola chiave deve essere una parola o una frase in inglese.

* Per pronunciarla, non devono servire più di due secondi.

* Le parole composte da 4-7 sillabe sono ottimali. Ad esempio, "Hey, computer" è una parola chiave corretta. "Ehi" è una parola inappropriata.

* Le parole chiave devono seguire le regole comuni di pronuncia della lingua inglese.

* Una parola particolare o addirittura inventata che segue le normali regole di pronuncia italiana potrebbe ridurre i falsi positivi. Ad esempio, "computerama" potrebbe essere una parola chiave corretta.

* Non scegliere una parola comune. Ad esempio, "mangiare" e "andare" sono parole che le persone ripetono spesso parlando normalmente e potrebbero essere falsi trigger per il dispositivo.

* Evitare di usare una parola chiave che potrebbe avere pronunce alternative. Gli utenti dovranno conoscere la pronuncia "corretta" per fare in modo che il dispositivo risponda. Ad esempio, "509" può essere pronunciato "cinquecentonove", "cinque zero nove" o "cinquecento nove". "U.F.O." può essere pronunciato "U-F-O" oppure "ufo". "Ancora" può essere pronunciata "àncora" o "ancóra".

* Non usare caratteri speciali, simboli o cifre. Ad esempio, "go #" e "20 + Cats" potrebbero essere parole chiave problematiche. mentre "vai euro" o "venti più gatti" possono andare bene. È comunque possibile usare i simboli nella personalizzazione e usare il marketing e la documentazione per ribadire la pronuncia corretta.

> [!NOTE]
> Se si sceglie una parola con marchio come parola chiave, assicurarsi di essere proprietari del marchio o di avere l'autorizzazione del proprietario del marchio a usare la parola. Microsoft non è responsabile di eventuali problemi legali che potrebbero derivare dalla scelta della parola chiave.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [creare una parola chiave personalizzata usando speech studio](speech-devices-sdk-create-kws.md).
