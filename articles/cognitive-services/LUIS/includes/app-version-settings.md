---
title: File di inclusione
ms.topic: include
ms.custom: include file
ms.date: 5/17/2020
ms.openlocfilehash: 79a45825be66b44d869ef741917436de87a1f5d0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "83653174"
---
Vedere i [concetti](../luis-concept-utterance.md#utterance-normalization-for-diacritics-and-punctuation) di normalizzazione e sull'uso delle API [version](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) per aggiornare queste impostazioni oppure usare la pagina **Settings** (Impostazioni) nella sezione **Manage** (Gestisci) del portale LUIS.


|Impostazione dell'interfaccia utente|Impostazione API|Informazioni|
|--|--|--|
|Use non-deterministic training (Usa training non deterministico)|`UseAllTrainingData`|Il training usa una piccola percentuale di campionamento negativo. Se si vogliono usare tutti i dati invece del campionamento negativo ridotto, impostare su `true`. |
|Normalize diacritics (Normalizza segni diacritici)|`NormalizeDiacritics`|La normalizzazione dei segni diacritici consente di sostituire i caratteri con segni diacritici in espressioni con caratteri regolari. Questa impostazione è disponibile solo nelle [lingue](../luis-reference-application-settings.md#diacritics-normalization) che supportano i segni diacritici.|
|Normalize punctuation (Normalizza punteggiatura)|`NormalizePunctuation`|Quando si normalizza la punteggiatura, la punteggiatura verrà rimossa dalle espressioni prima di procedere al training dei modelli e alla previsione delle query dell'endpoint.|
|Normalize word forms (Normalizza forme delle parole)|`NormalizeWordForm`|Ignora le forme delle parole oltre la radice.|
