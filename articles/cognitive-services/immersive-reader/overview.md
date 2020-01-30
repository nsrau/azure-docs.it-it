---
title: Informazioni sull'API dello strumento di lettura immersiva
titleSuffix: Azure Cognitive Services
description: L'API dello strumento di lettura immersiva è uno strumento che può essere usato per gestire le persone con differenze di apprendimento o per aiutare nuovi lettori e studenti di lingue.
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: overview
ms.date: 01/4/2020
ms.author: metan
ms.openlocfilehash: 5acd6e4be178c7e645eb6855a79cd0f58303ba9a
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76846412"
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