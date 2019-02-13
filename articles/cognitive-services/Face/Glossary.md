---
title: Glossario - Servizio API Viso
titleSuffix: Azure Cognitive Services
description: Il glossario include spiegazioni dei termini che è possibile incontrare durante l'uso del servizio API Viso.
services: cognitive-services
author: SteveMSFT
manager: cgronlun
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: 06c385b1ca43277b59c337646c41cae360ce48a5
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/07/2019
ms.locfileid: "55816561"
---
# <a name="glossary"></a>Glossario

## <a name="a"></a>A

#### <a name="attributes"></a>Attributi

Gli attributi sono facoltativi nei risultati del rilevamento, ad esempio [età](#Age-Attribute), [sesso](#Gender-Attribute), [posizione della testa](#Head-Pose-Attribute), [peli del volto](#Facial-Hair-Attribute) e [sorriso](#Smile-Attribute).
Possono essere ottenuti dall'API di rilevamento specificando il parametro di query returnFaceAttributes. Questi attributi offrono informazioni aggiuntive relative ai [visi](#Face) selezionati, oltre all'[ID viso](#Face-ID) e al [rettangolo](#Face-Rectangle).

Per altri dettagli, vedere la guida [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) (Viso - Rilevamento).

#### <a name="age-attribute"></a>Età (attributo)

L'età è uno degli [attributi](#Attributes) che descrivono l'età di un particolare viso. L'attributo dell'età è facoltativo nei risultati del rilevamento e può essere controllato con una richiesta di rilevamento specificando il parametro returnFaceAttributes.

Per altri dettagli, vedere la guida [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) (Viso - Rilevamento).

## <a name="b"></a>B

## <a name="c"></a>C

#### <a name="candidate"></a>Candidato

I candidati sono essenzialmente risultati dell'[identificazione](#Identification) (ad esempio persone identificate e livello di attendibilità dei rilevamenti). Un candidato è rappresentato da [ID persona](#Person-ID) e [attendibilità](#Confidence), che indica che la persona viene identificata con un elevato livello di attendibilità.

Per altri dettagli, vedere la guida [Face - Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) (Viso - Identificazione).

#### <a name="confidence"></a>Attendibilità

L'attendibilità è una misurazione che indica la somiglianza tra [visi](#Face) o [persone](#Person) in forma di valori numerici, usati nell'[identificazione](#Identification) e nella [verifica](#Verification) per indicare le similitudini dei risultati cercati, identificati e verificati.

Per altri dettagli, vedere le guide seguenti: [Face - Find Similar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) (Viso - Ricerca di simili), [Face - Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) (Viso - Identificazione), [Face - Verify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a) (Viso - Verifica).

## <a name="d"></a>D

#### <a name="detectionface-detection"></a>Rilevamento/rilevamento del viso

Il rilevamento del viso è l'azione di individuazione dei visi nelle immagini. Gli utenti possono caricare un'immagine o specificare l'URL di un'immagine nella richiesta. I visi rilevati vengono restituiti con [ID viso](#Face-ID) che indica un'identità univoca nell'API Viso. I rettangoli indicano la posizione dei visi nell'immagine in pixel, così come gli [attributi](#Attributes) facoltativi per ogni tipo di viso, come [età](#Age-Attribute), [sesso](#Gender-Attribute), [posizione della testa](#Head-Pose-Attribute), [peli del volto](#Facial-Hair-Attribute) e [sorriso](#Smile-Attribute).

Per altri dettagli, vedere la guida [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) (Viso - Rilevamento).

## <a name="e"></a>E

## <a name="f"></a>F

#### <a name="face"></a>Viso

Viso è un termine unificato per i risultati derivati dall'API Viso correlati ai visi rilevati. In definitiva, il viso è rappresentato da un'identità unificata ([ID viso](#Face-ID)), un'area specificata nelle immagini ([rettangolo del viso](#Face-Rectangle)) e altri attributi correlati al viso, come[età](#Age-Attribute), [sesso](#Gender-Attribute), punti di riferimento e [posizione della testa](#Head-Pose-Attribute). I visi possono inoltre essere restituiti dal rilevamento.

Per altri dettagli, vedere la guida [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) (Viso - Rilevamento).

#### <a name="face-api"></a>API Viso

L'API Viso è un'API basata sul cloud che fornisce gli algoritmi più avanzati per il rilevamento e il riconoscimento dei visi. Le funzionalità principali dell'API Viso possono essere suddivise in due categorie: rilevamento del viso con attributi e [riconoscimento](#Recognition) del viso.

Per altri dettagli, vedere le guide seguenti: [Face API Overview](./Overview.md) (Panoramica dell'API Viso), [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) (Viso - Rilevamento), [Face - Find Similar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) (Viso - Ricerca di simili), [Face - Group](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238) (Viso - Raggruppamento), [Face - Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) (Viso - Identificazione), [Face - Verify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a) (Viso - Verifica).

#### <a name="face-attributesfacial-attributes"></a>Attributi del viso/attributi facciali

Vedere [Attributi](#Attributes).

#### <a name="face-id"></a>ID viso

L'ID viso viene derivato dai risultati del rilevamento, in cui una stringa rappresenta un [viso](#Face) nell'[API Viso](#Face-API).

Per altri dettagli, vedere la guida [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) (Viso - Rilevamento).

#### <a name="face-landmarksfacial-landmarks"></a>Punti di riferimento del viso/punti di riferimento facciali

I punti di riferimento sono facoltativi nei risultati del rilevamento. Si tratta dei punti facciali semantici, come occhi, naso e bocca (illustrati nella figura seguente). I punti di riferimento possono essere controllati con una richiesta di rilevamento da returnFaceLandmarks con valore booleano. Se returnFaceLandmarks è impostato su true, per i visi restituiti saranno disponibili gli attributi dei punti di riferimento.

Per altri dettagli, vedere la guida [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) (Viso - Rilevamento).

![HowToDetectFace](./Images/landmarks.1.jpg)

#### <a name="face-rectangle"></a>Rettangolo del viso

Il rettangolo del viso viene derivato dai risultati del rilevamento e si tratta di un rettangolo verticale (sinistra, alto, larghezza, altezza) nelle immagini in pixel. L'angolo superiore sinistro di un [viso](#Face) (sinistra, alto), oltre alla larghezza e all'altezza, indica le dimensioni del viso rispettivamente sugli assi x e y.

Per altri dettagli, vedere la guida [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) (Viso - Rilevamento).

#### <a name="facial-hair-attribute"></a>Peli del volto (attributo)

I peli del volto sono uno degli [attributi](#Attributes) usati per descrivere la lunghezza dei peli dei visi disponibili. L'attributo per i peli del volto è facoltativo nei risultati del rilevamento e può essere controllato con una richiesta di rilevamento da returnFaceAttributes. Se returnFaceAttributes contiene 'facialHair', per i visi restituiti saranno disponibili gli attributi per i peli del volto.

Per altri dettagli, vedere la guida [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) (Viso - Rilevamento).

#### <a name="facelist"></a>FaceList

FaceList è una raccolta di [PersistedFace](#PersistedFace) ed è l'unità per l'API [Ricerca di simili](#Find-Similar). FaceList include un [ID FaceList](#FaceList-ID), oltre ad altri attributi, come nome e dati utente.

Per altri dettagli, vedere le guide seguenti: [FaceList - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b) (FaceList - Creazione), [FaceList - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c) (FaceList - Recupero).

#### <a name="facelist-id"></a>ID FaceList

ID FaceList è una stringa fornita dall'utente usata come identificatore di un [FaceList](#FaceList). L'ID FaceList deve essere univoco all'interno della sottoscrizione.

Per altri dettagli, vedere le guide seguenti: [FaceList - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b) (FaceList - Creazione), [FaceList - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c) (FaceList - Recupero).

#### <a name="find-similar"></a>Ricerca di simili

Questa API viene usata per la ricerca di visi simili in base a una raccolta di visi. I visi e le raccolte di visi per le query sono rappresentati come [ID viso](#Face-ID) o [ID FaceList](#FaceList-ID)/[ID LargeFaceList](#LargeFaceList-ID) nella richiesta. Nei risultati di ricerca vengono cercati visi simili, rappresentati da [ID viso](#Face-ID) oppure ID PersistedFace.

Per altri dettagli, vedere le guide seguenti: [Face - Find Similar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) (Viso - Ricerca di simili), [LargeFaceList - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc) (LargeFaceList - Creazione), [FaceList - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b) (FaceList - Creazione).

## <a name="g"></a>G

#### <a name="gender-attribute"></a>Sesso (attributo)

Il sesso è uno degli [attributi](#Attributes) usati per descrivere il sesso dei visi disponibili. L'attributo del sesso è facoltativo nei risultati del rilevamento e può essere controllato con una richiesta di rilevamento da returnFaceAttributes. Se returnfaceAttributes contiene 'gender', per i visi restituiti saranno disponibili gli attributi del sesso.

Per altri dettagli, vedere la guida [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) (Viso - Rilevamento).

#### <a name="grouping"></a>Raggruppamento

Il raggruppamento dei visi è il raggruppamento di una raccolta di visi in base a somiglianze facciali. Le raccolte di visi sono indicate da raccolte di ID viso nella richiesta. Come risultato del raggruppamento, i visi simili vengono riuniti in [gruppi](#Groups) e i visi non simili ad altri vengono riuniti in un gruppo misto. Nel risultato del raggruppamento è presente al massimo un solo [gruppo misto](#Messy-Group).

Per altri dettagli, vedere la guida [Face - Group](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238) (Viso - Raggruppamento).

#### <a name="groups"></a>Gruppi

I gruppi derivano dai risultati del [raggruppamento](#Grouping). Ogni gruppo contiene una raccolta di visi simili, indicati da [ID viso](#Face-ID).

Per altri dettagli, vedere la guida [Face - Group](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238) (Viso - Raggruppamento).

## <a name="h"></a>H

#### <a name="head-pose-attribute"></a>Posizione della testa (attributo)

La posizione della testa è uno degli [attributi](#Attributes) che rappresenta l'orientamento del visto nello spazio 3D in base agli angoli di inclinazione, rotazione attorno all'asse x e rotazione attorno all'asse y, come illustrato nella figura seguente. Gli intervalli di valori per inclinazione e rotazione attorno all'asse y sono [-180, 180] e [-90, 90] in gradi. Nella versione corrente il valore di rotazione attorno all'asse x restituito dal rilevamento è sempre 0. L'attributo della posizione della testa è facoltativo nei risultati di rilevamento e può essere controllato con una richiesta di rilevamento dal parametro returnFaceAttributes. Se il parametro returnFaceAttributes contiene 'headPose', per i visi restituiti saranno disponibili gli attributi per la posizione della testa.

Per altri dettagli, vedere la guida [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) (Viso - Rilevamento).

![GlossaryHeadPose](./Images/headpose.1.jpg)

## <a name="i"></a>I

#### <a name="identification"></a>Identificazione

L'identificazione consiste nell'identificare uno o più visi da un LargePersonGroup/PersonGroup.
Un [PersonGroup](#PersonGroup)/[LargePersonGroup](#LargePersonGroup) è una raccolta di [Person](#Person).
I visi e LargePersonGroup/PersonGroup sono rappresentati rispettivamente dagli [ID viso](#Face-ID) e dagli [ID LargePersonGroup](#LargePersonGroup-ID)/[ID PersonGroup](#PersonGroup-ID) nella richiesta.
I risultati identificati sono [candidati](#Candidate), rappresentati da [Person](#Person) con attendibilità.
Più visi nell'input vengono considerati separatamente e per ogni viso sarà disponibile un risultato identificato specifico.

> [!NOTE]
> È necessario completare correttamente il training di LargePersonGroup/PersonGroup prima dell'identificazione. Se non viene eseguito il training di LargePersonGroup/PersonGroup o lo [stato](#Status-Train) del training non è 'succeeded' (ad esempio 'running', 'failed', or 'timeout'), la risposta alla richiesta è 400.
> 

Per altri dettagli, vedere le guide seguenti: [Face - Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) (Viso - Identificazione), [LargePersonGroup Person - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40) (LargePersonGroup Person - Creazione), [LargePersonGroup - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d) (LargePersonGroup - Creazione), [LargePersonGroup - Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4) (LargePersonGroup - Training), [PersonGroup Person - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c) (PersonGroup Person - Creazione), [PersonGroup - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) (PersonGroup - Creazione), [PersonGroup - Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249) (PersonGroup - Training).

#### <a name="isidentical"></a>IsIdentical

IsIdentical è un campo booleano dei risultati di [verifica](#Verification) che indica se due visi appartengono alla stessa persona.

Per altri dettagli, vedere la guida [Face - Verify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a) (Viso - Verifica).

## <a name="j"></a>J

## <a name="k"></a>K

## <a name="l"></a>L

#### <a name="landmarks"></a>Punti di riferimento

Vedere i punti di riferimento del viso.

#### <a name="largefacelist"></a>LargeFaceList

LargeFaceList è una raccolta di [PersistedFace](#PersistedFace) ed è l'unità dell'API [Ricerca di simili](#Find-Similar). LargeFaceList include un [ID LargeFaceList](#LargeFaceList-ID) oltre ad altri attributi, come nome e dati utente.

Per altri dettagli, vedere le guide seguenti: [LargeFaceList - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc) (LargeFaceList - Creazione), [LargeFaceList - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a15827cd2de3616c086f2ce) (LargeFaceList - Recupero), [LargeFaceList - List Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158db4d2de3616c086f2d6) (LargeFaceList - Elenco visi).

#### <a name="largefacelist-id"></a>ID LargeFaceList

ID LargeFaceList è una stringa fornita dall'utente e usata come identificatore di un [LargeFaceList](#LargeFaceList). L'ID LargeFaceList deve essere univoco all'interno della sottoscrizione.

Per altri dettagli, vedere le guide seguenti: [LargeFaceList - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc) (LargeFaceList - Creazione), [LargeFaceList - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a15827cd2de3616c086f2ce) (LargeFaceList - Recupero).

#### <a name="largepersongroup"></a>LargePersonGroup

LargePersonGroup è una raccolta di [Person](#Person) ed è l'unità di [identificazione](#Identification). LargePersonGroup include un [ID LargePersonGroup](#LargePersonGroup-ID) oltre ad altri attributi, come nome e dati utente.

Per altri dettagli, vedere le guide seguenti: [LargePersonGroup - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d) (LargePersonGroup - Creazione), [LargePersonGroup - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acebb6ac60f11b48b5a9e) (LargePersonGroup - Recupero), [LargePersonGroup Person - List](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adda06ac60f11b48b5aa1) (LargePersonGroup Person - Elenco).

#### <a name="largepersongroup-id"></a>ID LargePersonGroup

ID LargePersonGroup è una stringa fornita dall'utente usata come identificatore di un [LargePersonGroup](#LargePersonGroup). L'ID LargePersonGroup deve essere univoco all'interno della sottoscrizione.

Per altri dettagli, vedere le guide seguenti: [LargePersonGroup - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d) (LargePersonGroup - Creazione), [LargePersonGroup - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acebb6ac60f11b48b5a9e) (LargePersonGroup - Recupero).

## <a name="m"></a>M

#### <a name="messy-group"></a>Gruppo misto

Un gruppo misto deriva dai risultati del [raggruppamento](#Grouping) e contiene i visi non simili ad altri. Ogni viso in un gruppo misto è indicato dall'[ID viso](#Face-ID).

Per altri dettagli, vedere la guida [Face - Group](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238) (Viso - Raggruppamento).

## <a name="n"></a>N

#### <a name="name-person"></a>Nome (persona)

Nome è una stringa descrittiva per [Person](#Person). A differenza dell'[ID persona](#Person-ID), il nome delle persone può essere duplicato in un gruppo.

Per altri dettagli, vedere le guide seguenti: [LargePersonGroup Person - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40) (LargePersonGroup Person - Creazione), [LargePersonGroup Person - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599add376ac60f11b48b5aa0) (LargePersonGroup Person - Recupero), [PersonGroup Person - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c) (PersonGroup Person - Creazione), [PersonGroup Person - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523f) (PersonGroup Person - Recupero).

#### <a name="name-largepersongrouppersongroup"></a>Nome (LargePersonGroup/PersonGroup)

Nome è anche una stringa descrittiva per [LargePersonGroup](#LargePersonGroup)/[PersonGroup](#PersonGroup). A differenza di [ID LargePersonGroup](#LargePersonGroup-ID)/[ID PersonGroup](#PersonGroup-ID), il nome di LargePersonGroup/PersonGroup può essere duplicato all'interno di una sottoscrizione.

Per altri dettagli, vedere le guide seguenti: [LargePersonGroup - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d) (LargePersonGroup - Creazione), [LargePersonGroup - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acebb6ac60f11b48b5a9e) (LargePersonGroup - Recupero), [PersonGroup - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) (PersonGroup - Creazione), [PersonGroup - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246) (PersonGroup - Recupero).

## <a name="o"></a>O

## <a name="p"></a>P

#### <a name="persistedface"></a>PersistedFace

PersistedFace è una struttura di dati nell'API Viso. PersistedFace include un ID PersistedFace oltre ad altri attributi, come nome e dati utente.

Per altri dettagli, vedere le guide seguenti: [LargeFaceList - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3) (LargeFaceList - Aggiunta viso), [FaceList - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) (FaceList - Aggiunta viso), [LargePersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42) (LargePersonGroup Person - Aggiunta viso), [PersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) (PersonGroup Person - Aggiunta viso).

#### <a name="person-id"></a>ID persona

ID persona viene generato quando viene creata correttamente una struttura [PersistedFace](#PersistedFace). Viene creata una stringa per rappresentare questo viso nell'[API Viso](#Face-API).

Per altri dettagli, vedere le guide seguenti: [LargeFaceList - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3) (LargeFaceList - Aggiunta viso), [FaceList - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) (FaceList - Aggiunta viso), [LargePersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42) (LargePersonGroup Person - Aggiunta viso), [PersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) (PersonGroup Person - Aggiunta viso).

#### <a name="person"></a>Person

Person è una struttura di dati gestita nell'API Viso. Person include un [ID persona](#Person-ID) oltre ad altri attributi come nome, una raccolta di [PersistedFace](#PersistedFace) e dati utente.

Per altri dettagli, vedere le guide seguenti: [LargePersonGroup Person - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40) (LargePersonGroup Person - Creazione), [LargePersonGroup Person - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599add376ac60f11b48b5aa0) (LargePersonGroup Person - Recupero), [PersonGroup Person - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c) (PersonGroup Person - Creazione), [PersonGroup Person - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523f) (PersonGroup Person - Recupero).

#### <a name="person-id"></a>ID persona

ID persona viene generato quando viene creata correttamente una struttura [Person](#Person). Viene creata una stringa per rappresentare questa persona nell'[API Viso](#Face-API).

Per altri dettagli, vedere le guide seguenti: [LargePersonGroup Person - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40) (LargePersonGroup Person - Creazione), [LargePersonGroup Person - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599add376ac60f11b48b5aa0) (LargePersonGroup Person - Recupero), [PersonGroup Person - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c) (PersonGroup Person - Creazione), [PersonGroup Person - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523f) (PersonGroup Person - Recupero).

#### <a name="persongroup"></a>PersonGroup

PersonGroup è una raccolta di [Person](#Person) ed è l'unità di [identificazione](#Identification). PersonGroup include un [ID PersonGroup](#PersonGroup-ID) oltre ad altri attributi, come nome e dati utente.

Per altri dettagli, vedere le guide seguenti: [PersonGroup - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) (PersonGroup - Creazione), [PersonGroup - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246) (PersonGroup - Recupero), [PersonGroup Person - List](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395241) (PersonGroup Person - Elenco).

#### <a name="persongroup-id"></a>ID PersonGroup

ID PersonGroup è una stringa fornita dall'utente usata come identificatore di un [PersonGroup](#PersonGroup). L'ID del gruppo deve essere univoco all'interno della sottoscrizione.

Per altri dettagli, vedere le guide seguenti: [PersonGroup - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) (PersonGroup - Creazione), [PersonGroup - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246) (PersonGroup - Recupero).

#### <a name="pose-attribute"></a>Posizione (attributo)

Vedere [Posizione della testa](#Head-Pose-Attribute).

## <a name="q"></a>Q

## <a name="r"></a>R

#### <a name="recognition"></a>Riconoscimento

Il riconoscimento è un'area di applicazione comune per le tecnologie di riconoscimento facciale come [ricerca di simili](#Find-Similar), [raggruppamento](#Grouping), [identificazione](#Identification), [verifica se due visi sono uguali o meno](#Verification).

Per altri dettagli, vedere le guide seguenti: [Face - Find Similar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) (Viso - Ricerca di simili), [Face - Group](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238) (Viso - Raggruppamento), [Face - Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) (Viso - Identificazione), [Face - Verify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a) (Viso - Verifica).

#### <a name="rectangle-face"></a>Rettangolo (viso)

Vedere [rettangolo del viso](#Face-Rectangle).

## <a name="s"></a>S

#### <a name="similar-face-searching"></a>Ricerca di visi simili

Vedere [Ricerca di simili](#Find-Similar).

#### <a name="smile-attribute"></a>Sorriso (attributo)

Il sorriso è uno degli [attributi](#Attributes) usati per descrivere l'espressione sorridente dei visi disponibili. L'attributo per il sorriso è facoltativo nei risultati del rilevamento e può essere controllato con una richiesta di rilevamento da returnFaceAttributes. Se returnFaceAttributes contiene 'smile', per i visi restituiti saranno disponibili gli attributi per il sorriso.

Per altri dettagli, vedere la guida [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) (Viso - Rilevamento).

#### <a name="snapshot"></a>Snapshot

Uno snapshot è un archivio remoto temporaneo per determinati tipi di dati di Viso. Agisce come una sorta di area appunti per copiare i dati da una sottoscrizione a un'altra. Innanzitutto l'utente "crea" uno snapshot dei dati nella sottoscrizione di origine e quindi la "applica" a un nuovo oggetto dati nella sottoscrizione di destinazione. 

Per altre informazioni, vedere la [guida alla migrazione di Viso](./face-api-how-to-topics/how-to-migrate-face-data.md) e la documentazione di riferimento (REST) [Snapshot - Take](https://docs.microsoft.com/rest/api/cognitiveservices/face/snapshot/take) e [Snapshot - Apply](https://docs.microsoft.com/rest/api/cognitiveservices/face/snapshot/apply).

#### <a name="status-train"></a>Stato (training)

Lo stato è una stringa usata per descrivere la procedura per il training di LargeFaceList/LargePersonGroups/PersonGroups, inclusi 'notstarted', 'running', 'succeeded' e 'failed'.

Per altri dettagli, vedere le guide seguenti: [LargeFaceList - Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158422d2de3616c086f2d1) (LargeFaceList - Training), [LargePersonGroup - Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4) (LargePersonGroup - Training), [PersonGroup - Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249) (PersonGroup - Training).

#### <a name="subscription-key"></a>Chiave della sottoscrizione

La chiave della sottoscrizione è una stringa che è necessario specificare come parametro di stringa di query per richiamare qualsiasi API Viso. La chiave di sottoscrizione è reperibile nella pagina delle sottoscrizioni personali dopo aver eseguito l'accesso al portale di Servizi cognitivi Microsoft. A ogni sottoscrizione saranno associate due chiavi: una chiave primaria e una chiave secondaria. Entrambe possono essere usate per richiamare l'API in modo identico. È necessario proteggere le chiavi della sottoscrizione ed è possibile rigenerare le chiavi della sottoscrizione in qualsiasi momento sempre dalla pagina Sottoscrizioni personali.

## <a name="t"></a>T

#### <a name="train-largefacelistlargepersongrouppersongroup"></a>Training (LargeFaceList/LargePersonGroup/PersonGroup)

Questa API viene usata per pre-elaborare [LargeFaceList](#LargeFaceList)/[LargePersonGroup](#LargePersonGroup)/[PersonGroup](#PersonGroup) per assicurare buoni prestazioni per [ricerca di simili](#Find-Similar)/[identificazione](#Identification). Se non viene eseguito il training o se lo [stato del training](#Status-Train) non risulta completato, l'identificazione per questo PersonGroup genererà un errore.

Per altri dettagli, vedere le guide seguenti: [LargeFaceList - Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158422d2de3616c086f2d1) (LargeFaceList - Training), [LargePersonGroup - Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4) (LargePersonGroup - Training), [PersonGroup - Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249) (PersonGroup - Training), [Face - Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) (Viso - Identificazione).

## <a name="u"></a>U

#### <a name="userdatauser-data"></a>UserData/dati utente

I dati utente sono informazioni aggiuntive associate a [Person](#Person) e [PersonGroup](#PersonGroup)/[LargePersonGroup](#LargePersonGroup). I dati utente vengono impostati dagli utenti per semplificare l'uso, la comprensione e la memorizzazione dei dati.

Per altri dettagli, vedere le guide seguenti: [LargePersonGroup - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d) (LargePersonGroup - Creazione), [LargePersonGroup - Update](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acfc83a7b9412a4d53f3f) (LargePersonGroup - Aggiornamento), [LargePersonGroup Person - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40) (LargePersonGroup Person - Creazione), [LargePersonGroup Person - Update](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ade043a7b9412a4d53f41) (LargePersonGroup Person - Aggiornamento), [PersonGroup - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) (PersonGroup - Creazione), [PersonGroup - Update](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524a) (PersonGroup - Aggiornamento), [PersonGroup Person - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c) (PersonGroup Person - Creazione), [PersonGroup Person - Update](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395242) (PersonGroup Person - Aggiornamento).

## <a name="v"></a>V

#### <a name="verification"></a>Verifica

Questa API viene usata per verificare se due visi sono uguali o meno. Entrambi i visi vengono rappresentati come ID viso nella richiesta. I risultati verificati contengono un campo booleano (isIdentical) che indica che i visi sono uguali se true e un campo numerico ([attendibilità](#Confidence)) che indica il livello di attendibilità.

Per altri dettagli, vedere la guida [Face - Verify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a) (Viso - Verifica).

## <a name="w"></a>W

## <a name="x"></a>X

## <a name="y"></a>S

## <a name="z"></a>Z
