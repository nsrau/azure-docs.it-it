---
title: Creare una risorsa LUIS
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/20/2020
ms.author: aahi
ms.openlocfilehash: ee7fd384a198c5eff672b14b6cb479aac26cfe54
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95972514"
---
<a name="create-luis-resources"></a>

## <a name="create-luis-resources-in-the-azure-portal"></a>Creare risorse LUIS nel portale di Azure

1. Seguire [questo collegamento](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) per iniziare a creare risorse LUIS nel portale di Azure.

1. Immettere tutte le impostazioni richieste:

    |Nome|Scopo|
    |--|--|
    |Subscription | La sottoscrizione in cui verrà fatturata la risorsa.|
    |Resource group| Il nome di un gruppo di risorse personalizzato scelto o creato. I gruppi di risorse consentono di raggruppare le risorse di Azure per l'accesso e la gestione.|
    |Nome| Un nome personalizzato scelto. Viene usato come sottodominio personalizzato per le query sugli endpoint di creazione e previsione.|
    |Posizione di creazione|L'area associata al modello.|
    |Piano tariffario di creazione|Determina il numero massimo di transazioni al secondo e al mese.|
    |Percorso di stima|L'area associata al runtime dell'endpoint di previsione pubblicato.|
    |Piano tariffario per le previsioni|Determina il numero massimo di transazioni al secondo e al mese.|

    > [!div class="mx-imgBorder"]
    > [![Screenshot che mostra la scheda Informazioni di base nella sezione Crea.](../media/luis-how-to-azure-subscription/create-resource-in-azure-small.png)](../media/luis-how-to-azure-subscription/create-resource-in-azure-small.png#lightbox)

1. Selezionare **Rivedi e crea** e attendere che venga creata la risorsa.
1. Dopo aver creato entrambe le risorse, sempre nel portale di Azure selezionare la nuova risorsa di creazione. Selezionare quindi **Chiavi ed endpoint** per ottenere l'**URL** e la **chiave** dell'endpoint per la creazione a livello di codice.

> [!TIP]
> Per usare le risorse, [assegnarle](../luis-how-to-azure-subscription.md#assign-an-authoring-resource-in-the-luis-portal-for-all-apps) nel portale LUIS.
