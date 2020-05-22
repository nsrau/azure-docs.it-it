---
title: File di inclusione
ms.topic: include
ms.custom: include file
ms.date: 5/04/2020
ms.openlocfilehash: 819dfa3127eb91a2f08687a76ea0586439b650a4
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83590988"
---
|Livello|Impostazione dell'interfaccia utente|Impostazione API|Informazioni|
|--|--|--|--|
|App|Make endpoints public (Rendi pubblici gli endpoint)|`Public`|Tutti possono accedere all'app pubblica se hanno una chiave di previsione e conoscono l'ID app. |
|Versione|Use non-deterministic training (Usa training non deterministico)|`UseAllTrainingData`|Il training usa una piccola percentuale di campionamento negativo. Se si vogliono usare tutti i dati invece del campionamento negativo ridotto, impostare su `true`. |
|Versione|Normalize diacritics (Normalizza segni diacritici)|`NormalizeDiacritics`|La normalizzazione dei segni diacritici consente di sostituire i caratteri con segni diacritici in espressioni con caratteri regolari.|
|Versione|Normalize punctuation (Normalizza punteggiatura)|`NormalizePunctuation`|Quando si normalizza la punteggiatura, la punteggiatura verrà rimossa dalle espressioni prima di procedere al training dei modelli e alla previsione delle query dell'endpoint.|
|Versione|Normalize word forms (Normalizza forme delle parole)|`NormalizeWordForm`|Ignora le forme delle parole oltre la radice.|

Vedere i [concetti](../luis-concept-utterance.md#utterance-normalization-for-diacritics-and-punctuation) di normalizzazione e sull'uso delle API [app](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58aeface39e2bb03dcd5909e) e [version](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) per aggiornare queste impostazioni oppure usare la pagina **Application Settings** (Impostazioni applicazione) nella sezione **Manage** (Gestisci) del portale LUIS.