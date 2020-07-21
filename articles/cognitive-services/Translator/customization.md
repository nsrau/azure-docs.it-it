---
title: Personalizzazione della traduzione-Translator
titleSuffix: Azure Cognitive Services
description: Usare l'hub di Microsoft Translator per creare il proprio sistema di traduzione automatica usando la terminologia e lo stile preferiti.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: swmachan
ms.openlocfilehash: 8d49d9b9d29116d95173c1daf5133622c3694de6
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86523996"
---
# <a name="customize-your-text-translations"></a>Personalizzare le traduzioni di testo

Il convertitore personalizzato è una funzionalità del servizio di conversione, che consente agli utenti di personalizzare la traduzione avanzata dei computer neurali di Microsoft Translator durante la conversione del testo tramite Translator (solo versione 3).

La funzionalità può anche essere usata per personalizzare la traduzione vocale quando usata con i [servizi Voce di Servizi cognitivi](https://docs.microsoft.com/azure/cognitive-services/speech-service/).

## <a name="custom-translator"></a>Custom Translator

Con Custom Translator è possibile creare sistemi di traduzione neurali che comprendono la terminologia usata per la propria attività e azienda. Il sistema di traduzione personalizzata verrà quindi integrato nelle applicazioni, flussi di lavoro e siti Web esistenti.

### <a name="how-does-it-work"></a>Come funziona?

USA i documenti tradotti in precedenza (volantini, pagine Web, documentazione e così via) per creare un sistema di traduzione che rispecchi la terminologia e lo stile specifici del dominio, meglio di un sistema di conversione standard. Gli utenti possono caricare documenti TMX, XLIFF, TXT, DOCX e XLSX.  

Il sistema accetta anche i dati paralleli a livello di documento, ma non ancora allineati a livello di frase. Se gli utenti hanno accesso alle versioni dello stesso contenuto in più lingue, ma in documenti separati, Custom Translator sarà in grado di associare automaticamente frasi tra documenti.  Il sistema può anche usare dati monolingue in una o entrambe le lingue per integrare i dati di training paralleli per migliorare le traduzioni.

Il sistema personalizzato è quindi disponibile tramite una normale chiamata a Translator utilizzando il parametro category.

Usando Custom Translator, con il tipo e la quantità appropriati di dati di training, non è insolito prevedere guadagni compresi tra 5 e 10 o anche di più punti BLEU nella qualità della traduzione.

Altre informazioni sui vari livelli di personalizzazione in base ai dati disponibili sono reperibili nel [manuale dell'utente di Custom Translator](https://aka.ms/CustomTranslatorDocs).


## <a name="microsoft-translator-hub"></a>Hub di Microsoft Translator

> [!NOTE]
> L'Hub Microsoft Translator legacy verrà ritirato il 17 maggio 2019. [Visualizzare le informazioni e le date importanti per la migrazione](https://www.microsoft.com/translator/business/hub/).  

## <a name="custom-translator-versus-hub"></a>Custom Translator e Hub

| Funzionalità | Hub | Custom Translator |
| ------- | :-: | :---------------: |
|Stato delle funzionalità di personalizzazione    | Disponibilità generale    | Disponibilità generale |
| Versione API per testo    | Solo V2    | Solo V3 |
| Personalizzazione SMT    | Sì    | No |
| Personalizzazione NMT    | No    | Sì |
| Personalizzazione dei nuovi servizi voce unificati    | No    | Sì |
| [Nessuna traccia](https://www.aka.ms/notrace) | Sì    | Sì |

## <a name="collaborative-translations-framework"></a>Framework di Collaborative Translations

> [!NOTE]
> A partire dal 1 ° febbraio 2018, AddTranslation () e AddTranslationArray () non sono più disponibili per l'utilizzo con Translator v 2.0. Questi metodi non riusciranno e non verrà scritto nulla. Translator v 3.0 non supporta questi metodi.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Configurare un sistema di lingua personalizzato usando Custom Translator](https://aka.ms/CustomTranslatorDocs)
