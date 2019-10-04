---
title: Come creare un progetto - Custom Translator
titleSuffix: Azure Cognitive Services
description: Come creare un progetto in Custom Translator
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: a62cc6133fe01bf7478166c526e32b3215ceebf1
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68595744"
---
# <a name="create-a-project"></a>Creare un progetto

Un progetto è un contenitore per modelli, documenti e test. Ogni progetto include automaticamente tutti i documenti caricati nell'area di lavoro con la coppia di lingue corretta.

La creazione del progetto è il primo passaggio per la creazione di un modello.

## <a name="create-a-project"></a>Creare un progetto:

1.  Nel portale di [Custom Translator](https://portal.customtranslator.azure.ai) fare clic su Create project (Crea progetto).

    ![Crea progetto](media/how-to/how-to-create-project.png)

2.  Immettere i dettagli seguenti relativi al progetto nella finestra di dialogo:

    a.  Project name (Nome progetto) - obbligatorio: assegnare al progetto un nome univoco e significativo. Non è necessario citare le lingue all'interno del titolo.

    b.  Descrizione: una breve descrizione del progetto. Questa descrizione non influisce sul comportamento di Custom Translator o sul sistema personalizzato risultante, ma consente di distinguere i diversi progetti.

    c.  Language pair (Coppia di lingue) - obbligatorio: selezionare la lingua di origine e di destinazione per la traduzione.

    d.  Category (Categoria) - obbligatorio: selezionare la categoria più appropriata per il progetto. La categoria descrive la terminologia e lo stile dei documenti che si intende tradurre.

    e.  Category description (Descrizione categoria): usare questo campo per descrivere meglio il campo o il settore specifico in cui si lavora. Ad esempio, se la categoria è medicina, si potrebbe aggiungere un documento particolare, ad esempio con tema chirurgia o pediatria. La descrizione non influisce sul comportamento di Custom Translator o sul sistema personalizzato risultante.

    f.  Project label (Etichetta progetto): l'[etichetta del progetto](workspace-and-project.md#project-labels) consente di distinguere tra progetti con la stessa coppia di lingue e categoria. Come procedura consigliata, usare un'etichetta *solo* se si prevede di creare più progetti per la stessa coppia di lingue e la stessa categoria e si vuole accedere a questi progetti con un ID di categoria diverso. Non usare questo campo se si creano sistemi per una sola categoria. Un'etichetta di progetto non è obbligatoria e non è utile per distinguere tra coppie di lingue. È possibile usare la stessa etichetta per più progetti.

    ![Finestra di dialogo per la creazione del progetto](media/how-to/how-to-create-project-dialog.png)

3.  Fare clic su Crea

## <a name="view-project-details"></a>Visualizza i dettagli del progetto

La pagina di destinazione di Custom Translator mostra i primi 10 progetti nell'area di lavoro. Visualizza il nome del progetto, la coppia di lingue, la categoria, lo stato e il punteggio BLEU.

Dopo aver selezionato un progetto, si noteranno le informazioni seguenti nella pagina del progetto:

- Category ID (ID categoria): l'ID della categoria viene creato concatenando l'ID dell'area di lavoro, l'etichetta del progetto e il codice di categoria. Usare l'ID di categoria con l'API Traduzione testuale per ottenere traduzioni personalizzate.

- Pulsante per l'esecuzione del training: usare questo pulsante per avviare il [training di un modello](how-to-train-model.md).

- Pulsante per l'aggiunta di documenti: usare questo pulsante per [caricare documenti](how-to-upload-document.md).

- Pulsante per filtrare i documenti: usare questo pulsante per filtrare e cercare documenti specifici.

    ![Visualizza i dettagli del progetto](media/how-to/how-to-view-project.png)

## <a name="next-steps"></a>Passaggi successivi

- Scoprire [come cercare, eliminare e modificare progetti](how-to-search-edit-delete-projects.md).
- Scoprire [come caricare documenti](how-to-upload-document.md) per compilare modelli di traduzione.
