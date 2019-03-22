---
title: Personalizzazione della traduzione - API Traduzione testuale
titlesuffix: Azure Cognitive Services
description: Usare l'hub di Microsoft Translator per creare il proprio sistema di traduzione automatica usando la terminologia e lo stile preferiti.
services: cognitive-services
author: Jann-Skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: v-jansko
ms.openlocfilehash: e66387a904f0026eb862178b19fd1b005212d674
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58124015"
---
# <a name="customize-your-text-translations"></a>Personalizzare le traduzioni di testo

Il traduttore personalizzato Microsoft è una funzionalità del servizio Microsoft Translator che consente agli utenti di personalizzare la traduzione automatica neurale avanzata durante la traduzione di testo tramite l'API Traduzione testuale (solo versione 3).

La funzionalità può anche essere usata per personalizzare la traduzione vocale quando usata con i [servizi Voce di Servizi cognitivi](https://docs.microsoft.com/azure/cognitive-services/speech-service/).

## <a name="custom-translator"></a>Custom Translator

Con Custom Translator è possibile creare sistemi di traduzione neurali che comprendono la terminologia usata per la propria attività e azienda. Il sistema di traduzione personalizzata verrà quindi integrato nelle applicazioni, flussi di lavoro e siti Web esistenti.

### <a name="how-does-it-work"></a>Come funziona?

Usare i documenti tradotti in precedenza (opuscoli, pagine Web, documentazione e così via) per creare un sistema di traduzione che riflette la terminologia specifica del dominio e lo stile meglio di un sistema di traduzione generico. Gli utenti possono caricare documenti TMX, XLIFF, TXT, DOCX e XLSX.  

Il sistema accetta anche i dati paralleli a livello di documento, ma non ancora allineati a livello di frase. Se gli utenti hanno accesso alle versioni dello stesso contenuto in più lingue, ma in documenti separati, Custom Translator sarà in grado di associare automaticamente frasi tra documenti.  Il sistema può anche usare dati monolingue in una o entrambe le lingue per integrare i dati di training paralleli per migliorare le traduzioni.

Il sistema personalizzato diventa quindi disponibile tramite una chiamata normale per l'API Traduzione testuale di Microsoft usando il parametro di categoria.

Usando Custom Translator, con il tipo e la quantità appropriati di dati di training, non è insolito prevedere guadagni compresi tra 5 e 10 o anche di più punti BLEU nella qualità della traduzione.

Altre informazioni sui vari livelli di personalizzazione in base ai dati disponibili sono reperibili nel [manuale dell'utente di Custom Translator](https://aka.ms/CustomTranslatorDocs).


## <a name="microsoft-translator-hub"></a>Hub di Microsoft Translator

> [!NOTE]
> Microsoft Translator Hub legacy verrà ritirato il 30 aprile 2019. [Visualizzare le date e le informazioni sulla migrazione importante](https://www.microsoft.com/translator/business/hub/).  

## <a name="custom-translator-versus-hub"></a>Custom Translator e Hub

|   | **Hub** | **Custom Translator**|
|:-----|:----:|:----:|
|Stato delle funzionalità di personalizzazione   | Disponibilità generale  | Disponibilità generale |
| Versione API per testo  | Solo V2   | Solo V3 |
| Personalizzazione SMT | Sì   | No  |
| Personalizzazione NMT | No     | Sì |
| Personalizzazione dei nuovi servizi voce unificati | No     | Sì |
| [Nessuna traccia](https://www.aka.ms/notrace) | Sì  | Sì |

## <a name="collaborative-translations-framework"></a>Framework di Collaborative Translations

> [!NOTE]
> A partire da 1 febbraio 2018 AddTranslation() e AddTranslationArray() non sono più disponibili per l'uso con l'API Traduzione testuale versione 2.0. Questi metodi non riusciranno e non verrà scritto nulla. L'API Traduzione testuale V3.0 non supporta questi metodi.
> 
> Una funzionalità simile è disponibile nell'API Translator Hub. Vedere [https://hub.microsofttranslator.com/swagger](https://hub.microsofttranslator.com/swagger).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Configurare un sistema di lingua personalizzato usando Custom Translator](https://aka.ms/CustomTranslatorDocs)
