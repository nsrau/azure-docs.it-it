---
title: Che cosa sono i progetti e le aree di lavoro? - Custom Translator
titleSuffix: Azure Cognitive Services
description: Un'area di lavoro è uno spazio per la composizione e la compilazione del sistema di traduzione personalizzato. Un'area di lavoro può contenere più progetti, modelli e documenti. Un progetto è un wrapper per modelli, documenti e test. Ogni progetto include automaticamente tutti i documenti caricati nell'area di lavoro con la coppia di lingue corretta.
services: cognitive-services
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: v-pawal
ms.topic: conceptual
ms.openlocfilehash: 312688ddccd6a65745b919e4c396a95f40a58d3e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66382330"
---
# <a name="what-is-a-custom-translator-workspace"></a>Che cos'è un'area di lavoro di Custom Translator?

Un'area di lavoro è uno spazio per la composizione e la compilazione del sistema di traduzione personalizzato. Un'area di lavoro può contenere più progetti, modelli e documenti. Tutte le operazioni effettuate in Custom Translator si trovano in un'area di lavoro specifica.

L'accesso all'area di lavoro è riservato all'utente e agli utenti invitati nell'area di lavoro. Gli utenti invitati non hanno accesso al contenuto dell'area di lavoro. È possibile invitare un qualsiasi numero di utenti nell'area di lavoro e modificarne o rimuoverne l'accesso in qualsiasi momento. È anche possibile creare una nuova area di lavoro. Per impostazione predefinita, un'area di lavoro non contiene progetti o documenti che si trovano in altre aree di lavoro.

## <a name="what-is-a-custom-translator-project"></a>Che cos'è un progetto di Custom Translator?

Un progetto è un wrapper per modelli, documenti e test. Ogni progetto include automaticamente tutti i documenti caricati nell'area di lavoro con la coppia di lingue corretta. Ad esempio, se si dispone di un progetto da inglese a spagnolo e di un progetto da spagnolo a inglese, gli stessi documenti verranno inclusi in entrambi i progetti. Ogni progetto ha un ID di categoria associato che viene usato quando si eseguono query all'[API V3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl) per le traduzioni. Il parametro dell'ID di categoria viene usato per ottenere le traduzioni da un sistema personalizzato compilato con Custom Translator.

## <a name="project-categories"></a>Categorie di progetto

La categoria identifica il dominio, ovvero l'area di stile e terminologia da usare, per il progetto. È necessario scegliere la categoria più rilevante per i documenti. In alcuni casi, la scelta della categoria influisce direttamente sul comportamento di Custom Translator.

Sono disponibili due set di modelli di base: Generale e Tecnologia. Se è selezionata la categoria **Tecnologia**, saranno usati i modelli di base Tecnologia. Per qualsiasi altra categoria selezionata vengono usati i modelli di base Generale. Il modello di base Tecnologia è adatto per il dominio della tecnologia e meno adatto se le frasi utilizzate per la traduzione non rientrano nel dominio della tecnologia. Si consiglia ai clienti di selezionare la categoria Tecnologia solo se le frasi rientrano esclusivamente nel dominio della tecnologia.

Nella stessa area di lavoro, è possibile creare progetti per la stessa coppia di lingue in categorie diverse. Custom Translator impedisce la creazione di un progetto duplicato con la stessa coppia di lingue e categoria. L'applicazione di un'etichetta al progetto consente di evitare questa limitazione. Non usare le etichette a meno che non si stiano creando sistemi di traduzione per più client, in quanto l'aggiunta di un'etichetta univoca al progetto ha conseguenze sull'ID di categoria del progetto.

## <a name="project-labels"></a>Etichette di progetto

Custom Translator consente di assegnare un'etichetta al progetto. L'etichetta del progetto consente di distinguere tra più progetti con la stessa coppia di lingue e categoria. Come procedura consigliata, evitare di usare etichette di progetto a meno che non sia necessario.

L'etichetta di progetto viene usata come parte dell'ID di categoria. Se l'etichetta di progetto non viene impostata o è impostata in modo identico tra più progetti, i progetti con la stessa categoria e *diversa* coppia di lingue condividono lo stesso ID di categoria. Questo approccio è vantaggioso perché consente all'utente o al cliente di alternare le lingue quando si usa l'API di traduzione del testo, senza doversi preoccupare di un ID di categoria univoco per ogni progetto.

Ad esempio, se si vogliono abilitare traduzioni nel dominio tecnologia dall'inglese al francese e dal francese all'inglese, si creano due progetti: uno per inglese -\> francese e uno per francese -\> inglese. Si specifica la stessa categoria (tecnologia) per entrambi e si lascia vuota l'etichetta di progetto. L'ID di categoria per entrambi i progetti corrisponderebbe, quindi è possibile eseguire una query all'API per le traduzioni in inglese e francese senza dover modificare l'ID di categoria.

Se un provider di servizi linguistici vuole lavorare per più clienti con diversi modelli che condividono la stessa categoria e coppia di lingue, può essere molto utile usare un'etichetta di progetto per differenziare i clienti.

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su come creare un modello di traduzione in modo efficiente, vedere [Training e modelli](training-and-model.md).
