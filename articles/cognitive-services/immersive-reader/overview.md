---
title: Informazioni sull'API dello strumento di lettura immersiva
titleSuffix: Azure Cognitive Services
description: Informazioni sull'API dello strumento di lettura immersiva.
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: overview
ms.date: 06/20/2019
ms.author: metan
ms.openlocfilehash: f84804fbad07357706e786fc449fce9e42a5ec4a
ms.sourcegitcommit: d585cdda2afcf729ed943cfd170b0b361e615fae
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2019
ms.locfileid: "68688341"
---
# <a name="what-is-immersive-reader"></a>Cos'è lo strumento di lettura immersiva?

Lo [strumento di lettura immersiva](https://www.onenote.com/learningtools) è uno strumento incluso che implementa tecniche comprovate per migliorare la comprensione della lettura per lettori emergenti, studenti di lingue e persone con differenze nell'apprendimento, come la dislessia.

È possibile usare lo strumento di lettura immersiva nell'applicazione Web con Immersive Reader SDK.

## <a name="what-does-immersive-reader-do"></a>Funzione dello strumento di lettura immersiva

Lo strumento di lettura immersiva è progettato per rendere la lettura più accessibile a tutti.

* Mostra il contenuto in una visualizzazione di lettura minimalista

  ![Strumento di lettura immersiva](./media/immersive-reader.png)

* Visualizza le immagini delle parole di uso comune

  ![Dizionario di immagini](./media/picture-dictionary.png)

* Evidenzia nomi, verbi, aggettivi e avverbi

  ![Parti del discorso](./media/parts-of-speech.png)

* Legge il contenuto ad alta voce

  ![Lettura ad alta voce](./media/read-aloud.png)

* Traduce il contenuto in un'altra lingua

  ![Traduzione](./media/translation.png)

* Suddivide le parole in sillabe

  ![Sillabazione](./media/syllabification.png)

## <a name="how-does-immersive-reader-work"></a>Funzionamento dello strumento di lettura immersiva

Lo strumento di lettura immersiva è un'app Web autonoma che può essere richiamata tramite Immersive Reader JavaScript SDK e viene visualizzata nella parte superiore dell'app Web esistente per mezzo di un `iframe`. Quando si chiama l'API per avviare lo strumento di lettura immersiva, si specifica anche il contenuto da visualizzare nello strumento. L'SDK gestisce la creazione e lo stile dell'`iframe` e le comunicazioni con il servizio back-end dello strumento di lettura immersiva, che elabora il contenuto per l'identificazione delle parti del discorso, la sintesi vocale, la traduzione e così via.

## <a name="next-steps"></a>Passaggi successivi

Iniziare a usare lo strumento di lettura immersiva:

* Accedere all'[argomento di avvio rapido](./quickstart.md)
* Esplorare [Immersive Reader SDK in GitHub](https://github.com/microsoft/immersive-reader-sdk)
* Leggere le [informazioni di riferimento su Immersive Reader SDK](./reference.md)