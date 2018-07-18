---
title: Personalizzazione dell'API Traduzione testuale Microsoft Translator | Microsoft Docs
description: Usare l'hub di Microsoft Translator per creare il proprio sistema di traduzione automatica usando la terminologia e lo stile preferiti.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.component: translator-text
ms.topic: article
ms.date: 05/10/2018
ms.author: v-jansko
ms.openlocfilehash: 1db22a414c41f338c4e7fd6ce9dc7ac739fa9237
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "35377516"
---
# <a name="customize-your-text-translations"></a>Personalizzare le traduzioni di testo

L'anteprima di Microsoft Custom Translator è una funzionalità del servizio Microsoft Translator che consente agli utenti di personalizzare la traduzione automatica avanzata neurale durante la traduzione di testi tramite l'API Traduzione testuale Microsoft (solo versione 3). 

La funzionalità può anche essere usata per personalizzare la traduzione vocale quando usata con l'[anteprima di voce dei Servizi cognitivi](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/).

## <a name="custom-translator"></a>Custom Translator
Con Custom Translator è possibile creare sistemi di traduzione neurali che comprendono la terminologia usata per la propria attività e azienda. Il sistema di traduzione personalizzata verrà quindi integrato nelle applicazioni, flussi di lavoro e siti Web esistenti. 

### <a name="how-does-it-work"></a>Come funziona?
Usare i documenti tradotti in precedenza (opuscoli, pagine Web, documentazione e così via) per creare un sistema di traduzione che riflette la terminologia specifica del dominio e lo stile meglio di un sistema di traduzione generico. Gli utenti possono caricare documenti TMX, XLIFF, TXT, DOCX e XLSX.  

Il sistema accetta anche i dati paralleli a livello di documento, ma non ancora allineati a livello di frase. Se gli utenti hanno accesso alle versioni dello stesso contenuto in più lingue, ma in documenti separati, Custom Translator sarà in grado di associare automaticamente frasi tra documenti.  Il sistema può anche usare dati monolingue in una o entrambe le lingue per integrare i dati di training paralleli per migliorare le traduzioni. 

Il sistema personalizzato diventa quindi disponibile tramite una chiamata normale per l'API Traduzione testuale di Microsoft usando il parametro di categoria.

Usando Custom Translator, con il tipo e la quantità appropriati di dati di training, non è insolito prevedere guadagni compresi tra 5 e 10 o anche di più punti BLEU nella qualità della traduzione.

Altre informazioni sui vari livelli di personalizzazione in base ai dati disponibili sono reperibili nel [manuale dell'utente di Custom Translator](http://aka.ms/CustomTranslatorDocs).


## <a name="microsoft-translator-hub"></a>Hub di Microsoft Translator

L'hub di Microsoft Translator legacy può essere usato per traduzione automatica statistica. [Altre informazioni](https://www.microsoft.com/en-us/translator/hub.aspx) 

## <a name="custom-translator-versus-hub"></a>Custom Translator e Hub

|   | **Hub** | **Custom Translator**|
|:-----|:----:|:----:|
|Stato delle funzionalità di personalizzazione   | Disponibilità generale  | Preview |
| Versione API per testo  | Solo V2   | Solo V3 |
| Personalizzazione SMT | Sì   | No  | 
| Personalizzazione NMT | No     | Sì |
| Personalizzazione dei nuovi servizi voce unificati | No     | Sì | 
| [Nessuna traccia](http://www.aka.ms/notrace) | Sì   | Sì | 

## <a name="collaborative-translations-framework"></a>Framework di Collaborative Translations

> [!NOTE]
> A partire da 1 febbraio 2018 AddTranslation() e AddTranslationArray() non sono più disponibili per l'uso con l'API Traduzione testuale versione 2.0. Questi metodi non riusciranno e non verrà scritto nulla. L'API Traduzione testuale V3.0 non supporta questi metodi.

>Una funzionalità simile è disponibile nell'API Translator Hub. Vedere [https://hub.microsofttranslator.com/swagger](https://hub.microsofttranslator.com/swagger). 

## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"]
> [Configurare un sistema di lingua personalizzato usando Custom Translator](http://aka.ms/CustomTranslatorDocs)
