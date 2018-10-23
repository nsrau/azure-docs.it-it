---
title: Che cos'è il servizio API Viso?
titleSuffix: Azure Cognitive Services
description: Questo argomento illustra il servizio API Viso e i termini correlati.
author: SteveMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: face-api
ms.topic: overview
ms.date: 10/11/2018
ms.author: sbowles
ms.openlocfilehash: 6c1e0d0a51bc01c581c05e7ce3215f5501b4be76
ms.sourcegitcommit: 3a02e0e8759ab3835d7c58479a05d7907a719d9c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2018
ms.locfileid: "49310369"
---
# <a name="what-is-the-face-api-service"></a>Che cos'è il servizio API Viso?

Il servizio API Viso è un servizio basato sul cloud che fornisce algoritmi per l'analisi dei visi umani in immagini e video. L'API Viso ha due funzioni principali: il rilevamento del viso con attributi e il riconoscimento del viso.

## <a name="face-detection"></a>Rilevamento del viso

L'API Viso può rilevare fino a 64 visi umani con posizione del viso ad alta precisione in un'immagine. L'immagine può essere specificata tramite un file (flusso di byte) o con un URL valido.

![Panoramica - Rilevamento viso](./Images/Face.detection.jpg)

Con ogni viso rilevato viene restituito il rettangolo del viso (lato sinistro, superiore, larghezza e altezza) che indica la posizione del viso nell'immagine. Facoltativamente, il rilevamento viso estrae una serie di attributi relativi al viso, ad esempio posa, sesso, età, posizione della testa, barba/baffi e occhiali. Per altre informazioni, vedere [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

## <a name="face-recognition"></a>Riconoscimento del viso

La possibilità di identificare visi umani è importante in molti scenari, tra cui sicurezza, interfaccia utente naturale, analisi e gestione del contenuto di immagini, app per dispositivi mobili e robotica. Il servizio API Viso offre quattro funzioni di riconoscimento del viso: verifica del viso, individuazione di visi simili, raggruppamento dei visi e identificazione della persona.

### <a name="face-verification"></a>Verifica del viso

La verifica del viso esegue un'autenticazione su due visi rilevati o un'autenticazione da un viso rilevato a un oggetto persona. Per informazioni più dettagliate, vedere [Face - Verify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

### <a name="finding-similar-faces"></a>Individuazione di visi simili

Con un viso rilevato di destinazione e un set di visi candidati tra cui cercare, il servizio individua una piccola selezione dei visi più simili al viso di destinazione. Sono supportate due modalità di utilizzo, ovvero **matchFace** e **matchPerson**. La modalità **matchPerson** restituisce visi simili dopo aver applicato una soglia per la stessa persona derivata da [Face - Verify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a). La modalità **matchFace** ignora la soglia per la stessa persona e restituisce i visi candidati più simili. Nell'esempio seguente sono elencati i visi candidati.
![Panoramica - Trovare visi simili](./Images/FaceFindSimilar.Candidates.jpg) Questo è il viso della query.
![Panoramica - Trovare visi simili](./Images/FaceFindSimilar.QueryFace.jpg)

Per trovare quattro visi simili, la modalità **matchPerson** restituirebbe (a) e (b), che rappresentano la stessa persona, come viso della query. La modalità **matchFace** restituisce (a), (b), (c) e (d), esattamente quattro candidati anche se la somiglianza è minore. Per altre informazioni, vedere [Face - Find Similar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237).

### <a name="face-grouping"></a>Raggruppamento dei visi

In un set di visi sconosciuti, l'API di raggruppamento dei visi li divide automaticamente in più gruppi in base alla somiglianza. Ogni gruppo è un sottoinsieme proprio indipendente del set di visi sconosciuti originale e contiene visi che si somigliano. Tutti i visi nello stesso gruppo possono essere considerati come appartenenti alla stessa persona. Per altre informazioni, vedere [Face - Group](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).

### <a name="person-identification"></a>Identificazione delle persone

L'API Viso può essere usata per identificare le persone in base a un viso rilevato e a un database di persone. È possibile creare questo database in anticipo e modificarlo nel tempo.

La figura seguente è un esempio di un database denominato "myfriends". Ogni gruppo può contenere fino a 1.000.000/10.000 oggetti persona diversi. Per ogni oggetto persona è possibile registrare fino a 248 visi.

![Panoramica - LargePersonGroup/PersonGroup](./Images/person.group.clare.jpg)

Dopo la creazione e il training di un database, è possibile eseguire l'identificazione in base al gruppo con un nuovo viso rilevato. Se il viso viene identificato come una persona nel gruppo, viene restituito l'oggetto persona.

Per altre informazioni sull'identificazione di persone, vedere le guide dell'API seguenti:

[Face - Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)  
[PersonGroup - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244)  
[PersonGroup Person - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c)  
[PersonGroup - Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249)  
[LargePersonGroup - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d)  
[LargePersonGroup Person - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40)  
[LargePersonGroup - Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4)  

#### <a name="face-storage-and-pricing"></a>Archiviazione visi e prezzi

Con una sottoscrizione Standard, Archiviazione visi consente di archiviare visi persistenti aggiuntivi quando si usano oggetti LargePersonGroup/PersonGroup Person ([PersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b)/[LargePersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42)) o LargeFaceLists/FaceLists ([FaceList - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250)/[LargeFaceList - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3)) per l'identificazione o la ricerca di somiglianze con l'API Viso. Il costo delle immagini archiviate è di 0,50 dollari per 1.000 visi e alla tariffa viene applicata la ripartizione proporzionale giornaliera. Le sottoscrizioni del livello gratuito sono limitate a un totale di 1.000 persone.

Se, ad esempio, l'account usa 10.000 visi persistenti ogni giorno per la prima metà del mese e nessuno per la seconda metà, verranno addebitati solo 10.000 visi per i giorni di archiviazione. Se invece ogni giorno durante il mese vengono salvati in modo permanente 1.000 visi per alcune ore ed eliminati ogni sera, verrà comunque fatturato il costo di 1.000 visi persistenti ogni giorno.

## <a name="sample-apps"></a>App di esempio

In queste applicazioni di esempio viene usata l'API Viso.

- [API Viso Microsoft: libreria client ed esempio Windows](https://github.com/Microsoft/Cognitive-Face-Windows)
  - App di esempio WPF che illustra diversi scenari di rilevamento, analisi e identificazione del volto.
- [App UWP FamilyNotes](https://github.com/Microsoft/Windows-appsample-familynotes)
  - App di esempio della piattaforma UWP (Universal Windows Platform) che dimostra l'uso delle funzionalità di riconoscimento vocale, Cortana, input penna e fotocamera attraverso uno scenario di condivisione note in una famiglia.
- [Esempio di analisi di fotogrammi video](https://github.com/microsoft/cognitive-samples-videoframeanalysis)
  - App di esempio Win32 che dimostra l'analisi dei flussi video live quasi in tempo reale usando le API Viso, Visione artificiale ed Emozioni.

## <a name="tutorials"></a>Esercitazioni
Le esercitazioni seguenti illustrano le funzionalità di base e i processi delle sottoscrizioni dell'API Viso:
- [Esercitazione introduttiva per l'API Viso in CSharp](Tutorials/FaceAPIinCSharpTutorial.md)
- [Getting Started with Face API in Java for Android Tutorial](Tutorials/FaceAPIinJavaForAndroidTutorial.md) (Esercitazione introduttiva per l'API Viso in Java per Android)
- [Esercitazione introduttiva per l'API Viso in Python](Tutorials/FaceAPIinPythonTutorial.md)

## <a name="next-steps"></a>Passaggi successivi

Provare una guida introduttiva per implementare un semplice scenario con l'API Viso.
- [Guida introduttiva: Rilevare i visi in un'immagine con C#](quickstarts/csharp.md) (disponibile per altri linguaggi)
