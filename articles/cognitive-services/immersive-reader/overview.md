---
title: Che cos'è lo strumento di lettura immersiva?
titleSuffix: Azure Cognitive Services
description: Lo strumento di lettura immersiva è progettato per facilitare la comprensione della lettura per persone con differenze di apprendimento, nuovi lettori e studenti di lingue.
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: overview
ms.date: 01/4/2020
ms.author: metang
ms.custom: cog-serv-seo-aug-2020
keywords: lettori, studenti di lingue, visualizzare immagini, migliorare la lettura, leggere contenuto, tradurre
ms.openlocfilehash: 0661bd5f917a2bf071f11393ad8ac9af53ddfe97
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92631240"
---
# <a name="what-is-immersive-reader"></a>Cos'è lo strumento di lettura immersiva?

Lo [strumento di lettura immersiva](https://www.onenote.com/learningtools) è stato progettato per l'inclusività e implementa tecniche comprovate per migliorare la comprensione della lettura per nuovi lettori, studenti di lingue e persone con differenze di apprendimento, come la dislessia. Con la libreria client dello strumento di lettura immersiva, è possibile sfruttare la stessa tecnologia usata in Microsoft Word e Microsoft OneNote per migliorare le applicazioni Web. 

## <a name="use-immersive-reader-to-improve-reading-accessibility"></a>Usare lo strumento di lettura immersiva per migliorare l'accessibilità della lettura 

Lo strumento di lettura immersiva è progettato per rendere la lettura più facile e accessibile a tutti. Verranno ora esaminate alcune funzionalità principali dello strumento di lettura immersiva.

### <a name="isolate-content-for-improved-readability"></a>Isolare il contenuto per migliorare la leggibilità

Lo strumento di lettura immersiva isola il contenuto per migliorare la leggibilità. 

  ![Isolare il contenuto per migliorare la leggibilità con lo strumento di lettura immersiva](./media/immersive-reader.png)

### <a name="display-pictures-for-common-words"></a>Visualizzare immagini per le parole comuni

Per i termini di uso comune, lo strumento di lettura immersiva visualizzerà un'immagine.

  ![Dizionario di immagini con lo strumento di lettura immersiva](./media/picture-dictionary.png)

### <a name="highlight-parts-of-speech"></a>Evidenziare parti del discorso

Lo strumento di lettura immersiva può essere usato per aiutare gli studenti a comprendere parte del discorso e della grammatica evidenziando verbi, sostantivi, pronomi e altro ancora.

  ![Visualizzare parti del discorso con lo strumento di lettura immersiva](./media/parts-of-speech.png)

### <a name="read-content-aloud"></a>Leggere il contenuto ad alta voce

Il servizio dello strumento di lettura immersiva include la sintesi vocale, che consente di selezionare il testo da leggere ad alta voce. 

  ![Leggere il testo ad alta voce con lo strumento di lettura immersiva](./media/read-aloud.png)

### <a name="translate-content-in-real-time"></a>Tradurre contenuto in tempo reale

Lo strumento di lettura immersiva è in grado di tradurre il testo in molte lingue in tempo reale. Questa funzione è utile per migliorare la comprensione per gli studenti di lingue.

  ![Tradurre il testo con lo strumento di lettura immersiva](./media/translation.png)

### <a name="split-words-into-syllables"></a>Dividere le parole in sillabe

Con lo strumento di lettura immersiva è possibile dividere le parole in sillabe per migliorare la leggibilità o ascoltare nuove parole.

  ![Dividere le parole in sillabe con lo strumento di lettura immersiva](./media/syllabification.png)

## <a name="how-does-immersive-reader-work"></a>Funzionamento dello strumento di lettura immersiva

Lo strumento di lettura immersiva è un'applicazione Web autonoma. Quando viene richiamato usando la libreria client, lo strumento di lettura immersiva viene visualizzato al di sopra dell'applicazione Web esistente in un `iframe`. Quando l'applicazione Web chiama il servizio dello strumento di lettura immersiva, si specifica il contenuto da mostrare. La libreria client dello strumento di lettura immersiva gestisce la creazione e lo stile dell'`iframe`, oltre alla comunicazione con il servizio back-end dello strumento. Il servizio dello strumento di lettura immersiva elabora il contenuto per rilevare parti del discorso, eseguire la sintesi vocale, generare traduzioni e altro ancora.

## <a name="get-started-with-immersive-reader"></a>Introduzione allo strumento di lettura immersiva

La libreria client dello strumento di lettura immersiva è disponibile in C#, JavaScript, Java (Android), Kotlin (Android) e Swift (iOS). Iniziare con:

* [Avvio rapido: Usare la libreria client dello strumento di lettura immersiva](quickstarts/client-libraries.md)

## <a name="next-steps"></a>Passaggi successivi

Iniziare a usare lo strumento di lettura immersiva:

* Vedere le [informazioni di riferimento sulla libreria client dello strumento di lettura immersiva](./reference.md)
* Esplorare la [libreria client dello strumento di lettura immersiva in GitHub](https://github.com/microsoft/immersive-reader-sdk)
