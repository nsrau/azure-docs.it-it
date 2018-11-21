---
title: Abbinamento e allineamento di frasi - Custom Translator
titleSuffix: Azure Cognitive Services
description: Durante l'esecuzione del training, le frasi presenti in documenti paralleli vengono abbinate o allineate. Custom Translator apprende le traduzioni una frase alla volta, leggendo una frase e la relativa traduzione. Allinea quindi ogni parola e ogni espressione l'una all'altra in queste due frasi.
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.component: custom-translator
ms.date: 11/13/2018
ms.author: v-rada
ms.topic: conceptual
ms.openlocfilehash: 557cd8d3af0c774d4dd0558d5d25dba8eec07268
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2018
ms.locfileid: "51626902"
---
# <a name="sentence-pairing-and-alignment-in-parallel-documents"></a>Abbinamento e allineamento di frasi in documenti paralleli

Durante l'esecuzione del training, le frasi presenti in documenti paralleli vengono abbinate o allineate. Custom Translator segnala il numero di frasi che è stato in grado di abbinare come frasi allineate in ogni set di dati.

## <a name="pairing-and-alignment-process"></a>Processo di abbinamento e allineamento

Custom Translator apprende le traduzioni delle frasi una frase alla volta. Legge una frase dall'origine e quindi la traduzione di tale frase dalla destinazione. Allinea quindi ogni parola e ogni espressione l'una all'altra in queste due frasi. Questo processo consente di creare un mapping tra le parole e le espressioni contenute in una frase e le parole ed espressioni equivalenti nella traduzione della frase stessa. L'allineamento cerca di assicurare che il training del sistema venga eseguito su frasi che sono traduzioni le une delle altre.

## <a name="pre-aligned-documents"></a>Documenti preallineati

Se si è certi di disporre di documenti paralleli, è possibile saltare il processo di allineamento delle frasi fornendo file di testo preallineati. È possibile estrarre tutte le frasi di entrambi i documenti in un file di testo, organizzato con una frase per riga, e caricarlo con l'estensione `.align`. L'estensione `.align` segnala a Custom Translator di saltare il processo di allineamento delle frasi.

Per ottenere risultati ottimali, verificare che i file di testo contengano una frase per riga. La presenza di caratteri di nuova riga in una frase comprometterebbe l'allineamento.

## <a name="suggested-minimum-number-of-extracted-and-aligned-sentences"></a>Numero minimo suggerito di frasi estratte e allineate

La tabella seguente mostra il numero minimo di frasi estratte e frasi allineate necessarie in ogni set di dati affinché un training abbia esito positivo. Il numero minimo suggerito di frasi estratte è molto più elevato del numero minimo di frasi allineate, in quanto occorre tenere conto del fatto che il processo di allineamento delle frasi potrebbe non riuscire ad allineare correttamente tutte le frasi estratte.

| Set di dati   | Numero minimo suggerito di frasi estratte | Numero minimo suggerito di frasi allineate | Numero massimo di frasi allineate |
|------------|--------------------------------------------|------------------------------------------|--------------------------------|
| Training   | 10.000                                     | 2.000                                    | Nessun limite massimo                 |
| Ottimizzazione     | 2.000                                      | 500                                      | 2.500                          |
| Test    | 2.000                                      | 500                                      | 2.500                          |
| Dizionario | 0                                          | 0                                        | Nessun limite massimo                 |

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come usare un [dizionario](what-is-dictionary.md) in Custom Translator.
