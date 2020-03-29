---
title: Abbinamento e allineamento di frasi - Custom Translator
titleSuffix: Azure Cognitive Services
description: Durante l'esecuzione del training, le frasi presenti in documenti paralleli vengono abbinate o allineate. Custom Translator apprende le traduzioni una frase alla volta, leggendo una frase e la relativa traduzione. Allinea quindi ogni parola e ogni espressione l'una all'altra in queste due frasi.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: cf5b2b84142c9104ea5b3afa3ad179fd0ec07449
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80370131"
---
# <a name="sentence-pairing-and-alignment-in-parallel-documents"></a>Abbinamento e allineamento di frasi in documenti paralleli

Durante l'esecuzione del training, le frasi presenti in documenti paralleli vengono abbinate o allineate. Custom Translator segnala il numero di frasi che è stato in grado di abbinare come frasi allineate in ogni set di dati.

## <a name="pairing-and-alignment-process"></a>Processo di abbinamento e allineamento

Custom Translator apprende le traduzioni delle frasi una frase alla volta. Legge una frase dall'origine e quindi la traduzione di tale frase dalla destinazione. Allinea quindi ogni parola e ogni espressione l'una all'altra in queste due frasi. Questo processo consente di creare un mapping tra le parole e le espressioni contenute in una frase e le parole ed espressioni equivalenti nella traduzione della frase stessa. L'allineamento cerca di assicurare che il training del sistema venga eseguito su frasi che sono traduzioni le une delle altre.

## <a name="pre-aligned-documents"></a>Documenti preallineati

Se si è certi di disporre di documenti paralleli, è possibile saltare il processo di allineamento delle frasi fornendo file di testo preallineati. È possibile estrarre tutte le frasi di entrambi i documenti in un file di testo, organizzato con una frase per riga, e caricarlo con l'estensione `.align`. L'estensione `.align` segnala a Custom Translator di saltare il processo di allineamento delle frasi.

Per ottenere risultati ottimali, verificare che i file di testo contengano una frase per riga.La presenza di caratteri di nuova riga in una frase comprometterebbe l'allineamento.

## <a name="suggested-minimum-number-of-sentences"></a>Numero minimo consigliato di frasi

Affinché una formazione abbia successo, la tabella seguente mostra il numero minimo di frasi richieste in ogni tipo di documento.Questa limitazione è una rete di sicurezza per garantire che le frasi parallele contengano un vocabolario univoco sufficiente per eseguire correttamente il training di un modello di traduzione. La linea guida generale è che più frasi parallele in-domain di qualità della traduzione umana dovrebbero produrre modelli di qualità superiore.

| Document type   | Conteggio frasi minimo consigliato | Numero massimo di frasi |
|------------|--------------------------------------------|--------------------------------|
| Formazione   | 10,000                                     | Nessun limite massimo                 |
| Ottimizzazione     | 500                                      | 2.500       |
| Test    | 500                                      | 2.500  |
| Dizionario | 0                                          | Nessun limite massimo                 |

> [!NOTE]
> - La formazione non inizierà e fallirà se non viene raggiunto il numero minimo di 10.000 frasi per l'allenamento. 
> - Le operazioni di ottimizzazione e test sono facoltative. Se non vengono forniti, il sistema rimuoverà una percentuale appropriata da Training da utilizzare per la convalida e il test. 
> - È possibile eseguire il training su un modello utilizzando solo i dati del dizionario. Si prega di fare riferimento a [Che cosa è Dizionario](https://docs.microsoft.com/azure/cognitive-services/translator/custom-translator/what-is-dictionary).

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come usare un [dizionario](what-is-dictionary.md) in Custom Translator.
