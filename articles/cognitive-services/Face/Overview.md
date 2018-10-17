---
title: Che cos'è il servizio API Viso?
titleSuffix: Azure Cognitive Services
description: Il glossario include spiegazioni dei termini che è possibile incontrare durante l'uso del servizio API Viso.
author: SteveMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: face-api
ms.topic: overview
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: 15de899be5ab85e9fe84ba1b6284bc9419fcf8a1
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/18/2018
ms.locfileid: "46123471"
---
# <a name="what-is-the-face-api-service"></a>Che cos'è il servizio API Viso?

Il servizio API Viso è un servizio basato sul cloud che offre gli algoritmi facciali più avanzati. L'API Viso ha due funzioni principali: il rilevamento viso con attributi e il riconoscimento volto.

## <a name="face-detection"></a>Rilevamento viso

L'API Viso rileva fino 64 visi umani con posizione del viso ad alta precisione in un'immagine. È inoltre possibile specificare un'immagine in base al file in byte o a un URL valido.

![Panoramica - Rilevamento viso](./Images/Face.detection.jpg)

Con ogni viso rilevato viene restituito il rettangolo del viso (lato sinistro, superiore, larghezza e altezza) che indica la posizione del viso nell'immagine. Facoltativamente, il rilevamento viso estrae una serie di attributi relativi al viso, ad esempio posa, sesso, età, posizione della testa, barba/baffi e occhiali. Per altre informazioni, vedere [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

## <a name="face-recognition"></a>Riconoscimento volto

Il riconoscimento del volto viene ampiamente usato in molti scenari, tra cui sicurezza, interfaccia utente naturale, analisi e gestione del contenuto di immagini, app per dispositivi mobili e robotica. Sono disponibili quattro funzioni di riconoscimento del volto: verifica viso, individuazione di visi simili, raggruppamento visi e identificazione della persona.

### <a name="face-verification"></a>Verifica viso

La verifica dell'API Viso esegue un'autenticazione su due visi rilevati o un'autenticazione da un viso rilevato a un oggetto persona. Per informazioni più dettagliate, vedere [Face - Verify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

### <a name="finding-similar-face"></a>Individuazione di visi simili

Con un viso rilevato di destinazione e un set di visi candidati tra cui cercare, il servizio individua una piccola selezione dei visi più simili al viso di destinazione. Sono supportate due modalità operative: `matchFace` e `matchPerson`. La modalità `matchPerson` restituisce visi simili dopo aver applicato una soglia della stessa persona derivata da [Face - Verify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a). La modalità `matchFace` ignora la soglia della stessa persona e restituisce i visi candidati più simili. Nell'esempio seguente i visi candidati sono riportati di seguito.
![Panoramica - Individuazione di visi simili](./Images/FaceFindSimilar.Candidates.jpg) E il viso di query è ![Panoramica - Individuazione di visi simili](./Images/FaceFindSimilar.QueryFace.jpg)

Per individuare quattro visi simili, la modalità `matchPerson` restituisce (a) e (b), che appartengono alla stessa persona con il viso di query. La modalità `matchFace` restituisce (a), (b), (c) e (d), esattamente quattro candidati anche se la somiglianza è minore. Per altre informazioni, vedere [Face - Find Similar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237).

### <a name="face-grouping"></a>Raggruppamento visi

In un set di visi sconosciuti, l'API di raggruppamento visi li divide automaticamente in più gruppi in base alla somiglianza. Ogni gruppo è un sottoinsieme proprio indipendente del set di visi sconosciuti originale e contiene visi che si somigliano. Tutti i visi nello stesso gruppo, inoltre, possono essere considerati come appartenenti allo stesso oggetto persona. Per altre informazioni, vedere [Face - Group](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).

### <a name="face-identification"></a>Identificazione viso

È possibile usare l'API Viso per identificare le persone in base a un viso rilevato e un database di persone (definito come un oggetto LargePersonGroup/PersonGroup). Creare questo database in anticipo. Il database può essere modificato nel tempo.

La figura seguente è un esempio di un oggetto LargePersonGroup/PersonGroup denominato "myfriends". Ogni gruppo può contenere fino a 1.000.000/10.000 oggetti persona. Per ogni oggetto persona è possibile registrare fino a 248 visi.

![Panoramica - LargePersonGroup/PersonGroup](./Images/person.group.clare.jpg)

Dopo che un oggetto LargePersonGroup/PersonGroup è stato creato e sottoposto a training sarà possibile eseguire l'identificazione in base al gruppo e a un nuovo viso rilevato. Se il viso viene identificato come un oggetto persona nel gruppo, viene restituito l'oggetto persona.

Per altre informazioni sull'identificazione di persone, vedere le guide dell'API seguenti:

[Face - Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)
[PersonGroup - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244)
[PersonGroup Person - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c)
[PersonGroup - Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249)
[LargePersonGroup - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d)
[LargePersonGroup Person - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40)
[LargePersonGroup - Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4)

### <a name="face-storage"></a>Archiviazione visi

Con una sottoscrizione Standard, Archiviazione visi consente di archiviare visi persistenti aggiuntivi quando si usano oggetti LargePersonGroup/PersonGroup Person ([PersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b)/[LargePersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42)) o LargeFaceLists/FaceLists ([FaceList - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250)/[LargeFaceList - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3)) per l'identificazione o la ricerca di somiglianze con l'API Viso. Il costo delle immagini archiviate è di 0,5 dollari per 1000 visi e alla tariffa viene applicata la ripartizione proporzionale giornaliera. Le sottoscrizioni del livello gratuito sono gratuite, ma limitate a un totale di 1.000 persone.

Ai prezzi di Archiviazione visi viene applicata una ripartizione proporzionale giornaliera. Se, ad esempio, l'account usa 10.000 visi persistenti ogni giorno per la prima metà del mese e nessuno per la seconda metà, verranno addebitati solo 10.000 visi per i giorni di archiviazione. Se invece ogni giorno durante il mese vengono salvati in modo permanente 1.000 visi per alcune ore ed eliminati ogni sera, verrà comunque fatturato il costo di 1.000 visi persistenti ogni giorno.

## <a name="getting-started-tutorials"></a>Esercitazioni introduttive

Le esercitazioni seguenti illustrano le funzionalità di base e i processi delle sottoscrizioni dell'API Viso:

- [Getting Started with Face API in CSharp Tutorial](Tutorials/FaceAPIinCSharpTutorial.md) (Esercitazione introduttiva per l'API Viso in CSharp)
- [Getting Started with Face API in Java for Android Tutorial](Tutorials/FaceAPIinJavaForAndroidTutorial.md) (Esercitazione introduttiva per l'API Viso in Java per Android)
- [Esercitazione introduttiva per l'API Viso in Python](Tutorials/FaceAPIinPythonTutorial.md)

## <a name="sample-apps"></a>App di esempio

In queste applicazioni di esempio viene usata l'API Viso.

- [API Viso Microsoft: libreria client ed esempio Windows](https://github.com/Microsoft/Cognitive-Face-Windows)
  - App di esempio WPF che illustra diversi scenari di rilevamento, analisi e identificazione del volto.
- [App UWP FamilyNotes](https://github.com/Microsoft/Windows-appsample-familynotes)
  - App di esempio della piattaforma UWP (Universal Windows Platform) che dimostra l'uso delle funzionalità di riconoscimento vocale, Cortana, input penna e fotocamera attraverso uno scenario di condivisione note in una famiglia.
- [Esempio di analisi di fotogrammi video](https://github.com/microsoft/cognitive-samples-videoframeanalysis)
  - App di esempio della piattaforma UWP (Universal Windows Platform) che dimostra l'analisi dei flussi video live quasi in tempo reale usando le API Viso, Visione artificiale ed Emozioni.

## <a name="related-topics"></a>Argomenti correlati

- [Face API Version 1.0 Release Notes](ReleaseNotes.md) (Note sulla versione dell'API Viso versione 1.0)
- [How to Detect Faces in Image](Face-API-How-to-Topics/HowtoDetectFacesinImage.md) (Come rilevare visi nelle immagini)
- [How to Identify Faces in Image](Face-API-How-to-Topics/HowtoIdentifyFacesinImage.md) (Come identificare visi nelle immagini)
