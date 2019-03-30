---
title: Glossario - Servizio API Viso
titleSuffix: Azure Cognitive Services
description: Il glossario include spiegazioni dei termini che è possibile incontrare durante l'uso del servizio API Viso.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: d627c3c4419affa0d71cdb23df945c96d9fd7585
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2019
ms.locfileid: "58652434"
---
# <a name="glossary"></a>Glossario

## <a name="a"></a>Una 

#### <a name="attributes"></a>Attributi

Gli attributi sono le funzionalità di volti facoltativo che possono essere rilevate, ad esempio [età](#age-attribute), [sesso](#gender-attribute), [posa head](#head-pose-attribute), [sorriso facciale](#facial-hair-attribute)e [smile](#smile-attribute). Può essere ottenuti dall'API di rilevamento specificando la _returnFaceAttributes_ parametro di query.

Per un elenco completo degli attributi facciali, vedere la documentazione di riferimento: [Viso: rilevare](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="age-attribute"></a>Età (attributo)

L'età è uno degli [attributi](#attributes) che descrivono l'età di un particolare viso. L'attributo dell'età è facoltativo nei risultati del rilevamento e può essere controllato con una richiesta di rilevamento specificando il parametro returnFaceAttributes.

Per altre informazioni, vedere la documentazione di riferimento: [Viso: rilevare](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

## <a name="c"></a>C

#### <a name="candidate"></a>Candidato

I candidati sono essenzialmente risultati dell'[identificazione](#identification) (ad esempio persone identificate e livello di attendibilità dei rilevamenti). Un candidato è rappresentato da [ID persona](#person-id) e [attendibilità](#confidence), che indica che la persona viene identificata con un elevato livello di attendibilità.

Per altre informazioni, vedere la documentazione di riferimento: [Viso: identificare](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

#### <a name="confidence"></a>Attendibilità

L'attendibilità è una misurazione che indica la somiglianza tra [visi](#face) o [persone](#person) in forma di valori numerici, usati nell'[identificazione](#identification) e nella [verifica](#verification) per indicare le similitudini dei risultati cercati, identificati e verificati.

Per altre informazioni, vedere la documentazione di riferimento: [Face - Find Similar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) (Viso - Ricerca di simili), [Face - Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) (Viso - Identificazione), [Face - Verify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a) (Viso - Verifica).

## <a name="d"></a>D

#### <a name="detectionface-detection"></a>Rilevamento/rilevamento del viso

Il rilevamento del viso è l'azione di individuazione dei visi nelle immagini. Gli utenti possono caricare un'immagine o specificare l'URL di un'immagine nella richiesta. I visi rilevati vengono restituiti con [ID viso](#face-id) che indica un'identità univoca nell'API Viso. I rettangoli indicano la posizione dei visi nell'immagine in pixel, così come gli [attributi](#attributes) facoltativi per ogni tipo di viso, come [età](#age-attribute), [sesso](#gender-attribute), [posizione della testa](#head-pose-attribute), [peli del volto](#facial-hair-attribute) e [sorriso](#smile-attribute).

Per altre informazioni, vedere la documentazione di riferimento: [Viso: rilevare](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

## <a name="e"></a>E

#### <a name="emotion-attribute"></a>Emozioni (attributo)

Emozioni sono uno dei [gli attributi facciali](#attributes). Quando sottoposto a query, restituisce un elenco di emozioni e le probabilità di rilevamento per il viso specificato. Vengono normalizzati i punteggi di confidenza: i punteggi in tutte le emozioni verranno aggiunto a una. Le emozioni restituite sono felicità, tristezza, neutro, rabbia, biasimo, disgusto, sorpresa e la paura.

Per altre informazioni, vedere la documentazione di riferimento: [Viso: rilevare](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

## <a name="f"></a>F

#### <a name="face"></a>Viso

Viso è un termine unificato per i risultati derivati dall'API Viso correlati ai visi rilevati. In definitiva, volto è rappresentato da un'identità unificata ([Face ID](#face-id)), un'area specificata nelle immagini ([rettangolo per il viso](#face-rectangle)) e attributi aggiuntivi relativi ai visi, ad esempio [age](#age-attribute), [gender](#gender-attribute), punti di riferimento e [posa head](#head-pose-attribute). I visi possono inoltre essere restituiti dal rilevamento.

Per altre informazioni, vedere la documentazione di riferimento: [Viso: rilevare](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="face-api"></a>API Viso

L'API Viso è un'API basata sul cloud che fornisce gli algoritmi più avanzati per il rilevamento e il riconoscimento dei visi. Le funzionalità principali dell'API Viso possono essere suddivise in due categorie: rilevamento del viso con attributi e [riconoscimento](#recognition) del viso.

Per altre informazioni, vedere la documentazione di riferimento: [Face API Overview](./Overview.md) (Panoramica dell'API Viso), [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) (Viso - Rilevamento), [Face - Find Similar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) (Viso - Ricerca di simili), [Face - Group](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238) (Viso - Raggruppamento), [Face - Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) (Viso - Identificazione), [Face - Verify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a) (Viso - Verifica).

#### <a name="face-attributesfacial-attributes"></a>Attributi del viso/attributi facciali

Vedere [Attributi](#attributes).

#### <a name="face-id"></a>ID viso

L'ID viso viene derivato dai risultati del rilevamento, in cui una stringa rappresenta un [viso](#face) nell'[API Viso](#face-api).

Per altre informazioni, vedere la documentazione di riferimento: [Viso: rilevare](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="face-landmarksfacial-landmarks"></a>Punti di riferimento del viso/punti di riferimento facciali

I punti di riferimento sono facoltativi nei risultati del rilevamento. Si tratta dei punti facciali semantici, come occhi, naso e bocca (illustrati nella figura seguente). I punti di riferimento possono essere controllati con una richiesta di rilevamento da returnFaceLandmarks con valore booleano. Se returnFaceLandmarks è impostato su true, per i visi restituiti saranno disponibili gli attributi dei punti di riferimento.

Per altre informazioni, vedere la documentazione di riferimento: [Viso: rilevare](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

![HowToDetectFace](./Images/landmarks.1.jpg)

#### <a name="face-rectangle"></a>Rettangolo del viso

Il rettangolo del viso viene derivato dai risultati del rilevamento e si tratta di un rettangolo verticale (sinistra, alto, larghezza, altezza) nelle immagini in pixel. L'angolo superiore sinistro di un [viso](#face) (sinistra, alto), oltre alla larghezza e all'altezza, indica le dimensioni del viso rispettivamente sugli assi x e y.

Per altre informazioni, vedere la documentazione di riferimento: [Viso: rilevare](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="facial-hair-attribute"></a>Peli del volto (attributo)

I peli del volto sono uno degli [attributi](#attributes) usati per descrivere la lunghezza dei peli dei visi disponibili. L'attributo per i peli del volto è facoltativo nei risultati del rilevamento e può essere controllato con una richiesta di rilevamento da returnFaceAttributes. Se returnFaceAttributes contiene 'facialHair', per i visi restituiti saranno disponibili gli attributi per i peli del volto.

Per altre informazioni, vedere la documentazione di riferimento: [Viso: rilevare](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="facelist"></a>FaceList

FaceList è una raccolta di [PersistedFace](#persistedface) ed è l'unità per l'API [Ricerca di simili](#find-similar). FaceList include un [ID FaceList](#facelist-id), oltre ad altri attributi, come nome e dati utente.

Per altre informazioni, vedere la documentazione di riferimento: [FaceList - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b) (FaceList - Creazione), [FaceList - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c) (FaceList - Recupero).

#### <a name="facelist-id"></a>ID FaceList

ID FaceList è una stringa fornita dall'utente usata come identificatore di un [FaceList](#facelist). L'ID FaceList deve essere univoco all'interno della sottoscrizione.

Per altre informazioni, vedere la documentazione di riferimento: [FaceList - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b) (FaceList - Creazione), [FaceList - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c) (FaceList - Recupero).

#### <a name="find-similar"></a>Ricerca di simili

Questa API viene usata per la ricerca di visi simili in base a una raccolta di visi. I visi e le raccolte di visi per le query sono rappresentati come [ID viso](#face-id) o [ID FaceList](#facelist-id)/[ID LargeFaceList](#largefacelist-id) nella richiesta. Nei risultati di ricerca vengono cercati visi simili, rappresentati da [ID viso](#face-id) oppure ID PersistedFace.

Per altre informazioni, vedere la documentazione di riferimento: [Face - Find Similar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) (Viso - Ricerca di simili), [LargeFaceList - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc) (LargeFaceList - Creazione), [FaceList - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b) (FaceList - Creazione).

## <a name="g"></a>G

#### <a name="gender-attribute"></a>Sesso (attributo)

Il sesso è uno degli [attributi](#attributes) usati per descrivere il sesso dei visi disponibili. L'attributo del sesso è facoltativo nei risultati del rilevamento e può essere controllato con una richiesta di rilevamento da returnFaceAttributes. Se returnfaceAttributes contiene 'gender', per i visi restituiti saranno disponibili gli attributi del sesso.

Per altre informazioni, vedere la documentazione di riferimento: [Viso: rilevare](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="grouping"></a>Raggruppamento

Il raggruppamento dei visi è il raggruppamento di una raccolta di visi in base a somiglianze facciali. Le raccolte di visi sono indicate da raccolte di ID viso nella richiesta. Come risultato del raggruppamento, i visi simili vengono riuniti in [gruppi](#groups) e i visi non simili ad altri vengono riuniti in un gruppo misto. Nel risultato del raggruppamento è presente al massimo un solo [gruppo misto](#messy-group).

Per altre informazioni, vedere la documentazione di riferimento: [Viso - gruppo](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).

#### <a name="groups"></a>Gruppi

I gruppi derivano dai risultati del [raggruppamento](#grouping). Ogni gruppo contiene una raccolta di visi simili, indicati da [ID viso](#face-id).

Per altre informazioni, vedere la documentazione di riferimento: [Viso - gruppo](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).

## <a name="h"></a>H

#### <a name="head-pose-attribute"></a>Posizione della testa (attributo)

La posizione della testa è uno degli [attributi](#attributes) che rappresenta l'orientamento del visto nello spazio 3D in base agli angoli di inclinazione, rotazione attorno all'asse x e rotazione attorno all'asse y, come illustrato nella figura seguente. Gli intervalli di valori per inclinazione e rotazione attorno all'asse y sono [-180, 180] e [-90, 90] in gradi. Nella versione corrente il valore di rotazione attorno all'asse x restituito dal rilevamento è sempre 0. L'attributo della posizione della testa è facoltativo nei risultati di rilevamento e può essere controllato con una richiesta di rilevamento dal parametro returnFaceAttributes. Se il parametro returnFaceAttributes contiene 'headPose', per i visi restituiti saranno disponibili gli attributi per la posizione della testa.

Per altre informazioni, vedere la documentazione di riferimento: [Viso: rilevare](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

![GlossaryHeadPose](./Images/headpose.1.jpg)

## <a name="i"></a>I

#### <a name="identification"></a>Identificazione

L'identificazione consiste nell'identificare uno o più visi da un LargePersonGroup/PersonGroup.
Un [PersonGroup](#persongroup)/[LargePersonGroup](#largepersongroup) è una raccolta di [Person](#person).
I visi e LargePersonGroup/PersonGroup sono rappresentati rispettivamente dagli [ID viso](#face-id) e dagli [ID LargePersonGroup](#largepersongroup-id)/[ID PersonGroup](#persongroup-id) nella richiesta.
I risultati identificati sono [candidati](#candidate), rappresentati da [Person](#person) con attendibilità.
Più visi nell'input vengono considerati separatamente e per ogni viso sarà disponibile un risultato identificato specifico.

> [!NOTE]
> È necessario completare correttamente il training di LargePersonGroup/PersonGroup prima dell'identificazione. Se non viene eseguito il training di LargePersonGroup/PersonGroup o lo [stato](#status-train) del training non è 'succeeded' (ad esempio 'running', 'failed', or 'timeout'), la risposta alla richiesta è 400.
> 

Per altre informazioni, vedere la documentazione di riferimento: [Face - Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) (Viso - Identificazione), [LargePersonGroup Person - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40) (LargePersonGroup Person - Creazione), [LargePersonGroup - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d) (LargePersonGroup - Creazione), [LargePersonGroup - Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4) (LargePersonGroup - Training), [PersonGroup Person - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c) (PersonGroup Person - Creazione), [PersonGroup - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) (PersonGroup - Creazione), [PersonGroup - Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249) (PersonGroup - Training).

#### <a name="isidentical"></a>IsIdentical

IsIdentical è un campo booleano dei risultati di [verifica](#verification) che indica se due visi appartengono alla stessa persona.

Per altre informazioni, vedere la documentazione di riferimento: [Viso: verificare](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

## <a name="l"></a>L

#### <a name="landmarks"></a>Punti di riferimento

Vedere i punti di riferimento del viso.

#### <a name="largefacelist"></a>LargeFaceList

LargeFaceList è una raccolta di [PersistedFace](#persistedface) ed è l'unità dell'API [Ricerca di simili](#find-similar). LargeFaceList include un [ID LargeFaceList](#largefacelist-id) oltre ad altri attributi, come nome e dati utente.

Per altre informazioni, vedere la documentazione di riferimento: [LargeFaceList - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc) (LargeFaceList - Creazione), [LargeFaceList - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a15827cd2de3616c086f2ce) (LargeFaceList - Recupero), [LargeFaceList - List Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158db4d2de3616c086f2d6) (LargeFaceList - Elenco visi).

#### <a name="largefacelist-id"></a>ID LargeFaceList

ID LargeFaceList è una stringa fornita dall'utente e usata come identificatore di un [LargeFaceList](#largefacelist). L'ID LargeFaceList deve essere univoco all'interno della sottoscrizione.

Per altre informazioni, vedere la documentazione di riferimento: [LargeFaceList - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc) (LargeFaceList - Creazione), [LargeFaceList - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a15827cd2de3616c086f2ce) (LargeFaceList - Recupero).

#### <a name="largepersongroup"></a>LargePersonGroup

LargePersonGroup è una raccolta di [Person](#person) ed è l'unità di [identificazione](#identification). LargePersonGroup include un [ID LargePersonGroup](#largepersongroup-id) oltre ad altri attributi, come nome e dati utente.

Per altre informazioni, vedere la documentazione di riferimento: [LargePersonGroup - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d) (LargePersonGroup - Creazione), [LargePersonGroup - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acebb6ac60f11b48b5a9e) (LargePersonGroup - Recupero), [LargePersonGroup Person - List](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adda06ac60f11b48b5aa1) (LargePersonGroup Person - Elenco).

#### <a name="largepersongroup-id"></a>ID LargePersonGroup

ID LargePersonGroup è una stringa fornita dall'utente usata come identificatore di un [LargePersonGroup](#largepersongroup). L'ID LargePersonGroup deve essere univoco all'interno della sottoscrizione.

Per altre informazioni, vedere la documentazione di riferimento: [LargePersonGroup - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d) (LargePersonGroup - Creazione), [LargePersonGroup - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acebb6ac60f11b48b5a9e) (LargePersonGroup - Recupero).

## <a name="m"></a>M

#### <a name="messy-group"></a>Gruppo misto

Un gruppo misto deriva dai risultati del [raggruppamento](#grouping) e contiene i visi non simili ad altri. Ogni viso in un gruppo misto è indicato dall'[ID viso](#face-id).

Per altre informazioni, vedere la documentazione di riferimento: [Viso - gruppo](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).

## <a name="n"></a>N

#### <a name="name-person"></a>Nome (persona)

Nome è una stringa descrittiva semplici da usare per [persona](#person). A differenza dell'[ID persona](#person-id), il nome delle persone può essere duplicato in un gruppo.

Per altre informazioni, vedere la documentazione di riferimento: [LargePersonGroup Person - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40) (LargePersonGroup Person - Creazione), [LargePersonGroup Person - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599add376ac60f11b48b5aa0) (LargePersonGroup Person - Recupero), [PersonGroup Person - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c) (PersonGroup Person - Creazione), [PersonGroup Person - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523f) (PersonGroup Person - Recupero).

#### <a name="name-largepersongrouppersongroup"></a>Nome (LargePersonGroup/PersonGroup)

Nome corrisponde a una stringa descrittiva semplici da usare per [LargePersonGroup](#largepersongroup)/[gruppo di persone](#persongroup). A differenza di [ID LargePersonGroup](#largepersongroup-id)/[ID PersonGroup](#persongroup-id), il nome di LargePersonGroup/PersonGroup può essere duplicato all'interno di una sottoscrizione.

Per altre informazioni, vedere la documentazione di riferimento: [LargePersonGroup - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d) (LargePersonGroup - Creazione), [LargePersonGroup - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acebb6ac60f11b48b5a9e) (LargePersonGroup - Recupero), [PersonGroup - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) (PersonGroup - Creazione), [PersonGroup - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246) (PersonGroup - Recupero).

## <a name="p"></a>P

#### <a name="persistedface"></a>PersistedFace

PersistedFace è una struttura di dati nell'API Viso. PersistedFace include un ID PersistedFace oltre ad altri attributi, come nome e dati utente.

Per altre informazioni, vedere la documentazione di riferimento: [LargeFaceList - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3) (LargeFaceList - Aggiunta viso), [FaceList - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) (FaceList - Aggiunta viso), [LargePersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42) (LargePersonGroup Person - Aggiunta viso), [PersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) (PersonGroup Person - Aggiunta viso).

#### <a name="person-id"></a>ID persona

ID persona viene generato quando viene creata correttamente una struttura [PersistedFace](#persistedface). Viene creata una stringa per rappresentare questo viso nell'[API Viso](#face-api).

Per altre informazioni, vedere la documentazione di riferimento: [LargeFaceList - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3) (LargeFaceList - Aggiunta viso), [FaceList - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) (FaceList - Aggiunta viso), [LargePersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42) (LargePersonGroup Person - Aggiunta viso), [PersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) (PersonGroup Person - Aggiunta viso).

#### <a name="person"></a>Person

Person è una struttura di dati gestita nell'API Viso. Person include un [ID persona](#person-id) oltre ad altri attributi come nome, una raccolta di [PersistedFace](#persistedface) e dati utente.

Per altre informazioni, vedere la documentazione di riferimento: [LargePersonGroup Person - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40) (LargePersonGroup Person - Creazione), [LargePersonGroup Person - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599add376ac60f11b48b5aa0) (LargePersonGroup Person - Recupero), [PersonGroup Person - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c) (PersonGroup Person - Creazione), [PersonGroup Person - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523f) (PersonGroup Person - Recupero).

#### <a name="person-id"></a>ID persona

ID persona viene generato quando viene creata correttamente una struttura [Person](#person). Viene creata una stringa per rappresentare questa persona nell'[API Viso](#face-api).

Per altre informazioni, vedere la documentazione di riferimento: [LargePersonGroup Person - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40) (LargePersonGroup Person - Creazione), [LargePersonGroup Person - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599add376ac60f11b48b5aa0) (LargePersonGroup Person - Recupero), [PersonGroup Person - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c) (PersonGroup Person - Creazione), [PersonGroup Person - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523f) (PersonGroup Person - Recupero).

#### <a name="persongroup"></a>PersonGroup

PersonGroup è una raccolta di [Person](#person) ed è l'unità di [identificazione](#identification). PersonGroup include un [ID PersonGroup](#persongroup-id) oltre ad altri attributi, come nome e dati utente.

Per altre informazioni, vedere la documentazione di riferimento: [PersonGroup - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) (PersonGroup - Creazione), [PersonGroup - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246) (PersonGroup - Recupero), [PersonGroup Person - List](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395241) (PersonGroup Person - Elenco).

#### <a name="persongroup-id"></a>ID PersonGroup

ID PersonGroup è una stringa fornita dall'utente usata come identificatore di un [PersonGroup](#persongroup). L'ID del gruppo deve essere univoco all'interno della sottoscrizione.

Per altre informazioni, vedere la documentazione di riferimento: [PersonGroup - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) (PersonGroup - Creazione), [PersonGroup - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246) (PersonGroup - Recupero).

#### <a name="pose-attribute"></a>Posizione (attributo)

Vedere [Posizione della testa](#head-pose-attribute).

## <a name="r"></a>R

#### <a name="recognition"></a>Riconoscimento

Il riconoscimento è un'area di applicazione comune per le tecnologie di riconoscimento facciale come [ricerca di simili](#find-similar), [raggruppamento](#grouping), [identificazione](#identification), [verifica se due visi sono uguali o meno](#verification).

Per altre informazioni, vedere la documentazione di riferimento: [Face - Find Similar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) (Viso - Ricerca di simili), [Face - Group](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238) (Viso - Raggruppamento), [Face - Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) (Viso - Identificazione), [Face - Verify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a) (Viso - Verifica).

#### <a name="rectangle-face"></a>Rettangolo (viso)

Vedere [rettangolo del viso](#face-rectangle).

## <a name="s"></a>S

#### <a name="similar-face-searching"></a>Ricerca di visi simili

Vedere [Ricerca di simili](#find-similar).

#### <a name="smile-attribute"></a>Sorriso (attributo)

Il sorriso è uno degli [attributi](#attributes) usati per descrivere l'espressione sorridente dei visi disponibili. L'attributo per il sorriso è facoltativo nei risultati del rilevamento e può essere controllato con una richiesta di rilevamento da returnFaceAttributes. Se returnFaceAttributes contiene 'smile', per i visi restituiti saranno disponibili gli attributi per il sorriso.

Per altre informazioni, vedere la documentazione di riferimento: [Viso: rilevare](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="snapshot"></a>Snapshot

Uno snapshot è un archivio remoto temporaneo per determinati tipi di dati di Viso. Agisce come una sorta di area appunti per copiare i dati da una sottoscrizione a un'altra. Innanzitutto l'utente "crea" uno snapshot dei dati nella sottoscrizione di origine e quindi la "applica" a un nuovo oggetto dati nella sottoscrizione di destinazione. 

Per altre informazioni, vedere la [guida alla migrazione di Viso](./face-api-how-to-topics/how-to-migrate-face-data.md) e la documentazione di riferimento (REST) [Snapshot - Take](/rest/api/cognitiveservices/face/snapshot/take) e [Snapshot - Apply](/rest/api/cognitiveservices/face/snapshot/apply).

#### <a name="status-train"></a>Stato (training)

Lo stato è una stringa usata per descrivere la procedura per il training di LargeFaceList/LargePersonGroups/PersonGroups, inclusi 'notstarted', 'running', 'succeeded' e 'failed'.

Per altre informazioni, vedere la documentazione di riferimento: [LargeFaceList - Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158422d2de3616c086f2d1), [LargePersonGroup - Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4), [gruppo di persone - Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249).

#### <a name="subscription-key"></a>Chiave della sottoscrizione

La chiave della sottoscrizione è una stringa che è necessario specificare come parametro di stringa di query per richiamare qualsiasi API Viso. La chiave di sottoscrizione è reperibile nella pagina delle sottoscrizioni personali dopo aver eseguito l'accesso al portale di Servizi cognitivi Microsoft. A ogni sottoscrizione saranno associate due chiavi: una chiave primaria e una chiave secondaria. Entrambe possono essere usate per richiamare l'API in modo identico. È necessario proteggere le chiavi della sottoscrizione ed è possibile rigenerare le chiavi della sottoscrizione in qualsiasi momento sempre dalla pagina Sottoscrizioni personali.

## <a name="t"></a>T

#### <a name="train-largefacelistlargepersongrouppersongroup"></a>Training (LargeFaceList/LargePersonGroup/PersonGroup)

Questa API viene usata per pre-elaborare [LargeFaceList](#largefacelist)/[LargePersonGroup](#largepersongroup)/[PersonGroup](#persongroup) per assicurare buoni prestazioni per [ricerca di simili](#find-similar)/[identificazione](#identification). Se non viene eseguito il training o se lo [stato del training](#status-train) non risulta completato, l'identificazione per questo PersonGroup genererà un errore.

Per altre informazioni, vedere la documentazione di riferimento: [LargeFaceList - Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158422d2de3616c086f2d1) (LargeFaceList - Training), [LargePersonGroup - Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4) (LargePersonGroup - Training), [PersonGroup - Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249) (PersonGroup - Training), [Face - Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) (Viso - Identificazione).

## <a name="u"></a>U

#### <a name="userdatauser-data"></a>UserData/dati utente

I dati utente sono informazioni aggiuntive associate a [Person](#person) e [PersonGroup](#persongroup)/[LargePersonGroup](#largepersongroup). I dati utente vengono impostati dagli utenti per semplificare l'uso, la comprensione e la memorizzazione dei dati.

Per altre informazioni, vedere la documentazione di riferimento: [LargePersonGroup - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d) (LargePersonGroup - Creazione), [LargePersonGroup - Update](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acfc83a7b9412a4d53f3f) (LargePersonGroup - Aggiornamento), [LargePersonGroup Person - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40) (LargePersonGroup Person - Creazione), [LargePersonGroup Person - Update](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ade043a7b9412a4d53f41) (LargePersonGroup Person - Aggiornamento), [PersonGroup - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) (PersonGroup - Creazione), [PersonGroup - Update](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524a) (PersonGroup - Aggiornamento), [PersonGroup Person - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c) (PersonGroup Person - Creazione), [PersonGroup Person - Update](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395242) (PersonGroup Person - Aggiornamento).

## <a name="v"></a>V

#### <a name="verification"></a>Verifica

Questa API viene usata per verificare se due visi sono uguali o meno. Entrambi i visi vengono rappresentati come ID viso nella richiesta. I risultati verificati contengono un campo booleano (isIdentical) che indica che i visi sono uguali se true e un campo numerico ([attendibilità](#confidence)) che indica il livello di attendibilità.

Per altre informazioni, vedere la documentazione di riferimento: [Viso: verificare](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).
