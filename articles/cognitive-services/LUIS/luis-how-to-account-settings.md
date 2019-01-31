---
title: Gestire le impostazioni
titleSuffix: Language Understanding - Azure Cognitive Services
description: Usare il sito Web LUIS per gestire le impostazioni dell'account utente e la chiave di creazione usata per tutte le app.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 12/07/2018
ms.author: diberry
ms.openlocfilehash: 51b0d3f753ab89e5809e610f5754355d1c5090b0
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55228628"
---
# <a name="manage-account-and-authoring-key"></a>Gestire l'account e la chiave di creazione
Le informazioni principali di un account LUIS sono l'account utente e la chiave di creazione. Le informazioni di accesso sono gestite in [account.microsoft.com](https://account.microsoft.com). La chiave di creazione viene gestita dalla pagina [Settings](luis-reference-regions.md) (Impostazioni) del sito Web **LUIS**. 

## <a name="authoring-key"></a>Chiave di creazione

La chiave di creazione specifica dell'area nella pagina **Settings** (Impostazioni) consente di creare tutte le app dal sito Web [LUIS](luis-reference-regions.md) e dalle [API di creazione](https://aka.ms/luis-authoring-api). Per praticità, la chiave di creazione è autorizzata a creare un numero [limitato](luis-boundaries.md) di query di endpoint al mese. 

[![Pagina delle impostazioni di LUIS](./media/luis-how-to-account-settings/account-settings.png)](./media/luis-how-to-account-settings/account-settings.png#lightbox)

La chiave di creazione viene usata per le app di cui si è titolari e per le app di cui si è collaboratori.

## <a name="authoring-key-regions"></a>Aree delle chiavi di creazione
La chiave di creazione è specifica dell'[area di creazione](luis-reference-regions.md#publishing-regions). La chiave non può essere usata in un'area diversa. 

## <a name="reset-authoring-key"></a>Reimpostare la chiave di creazione
Se la chiave di creazione è danneggiata, reimpostare la chiave. La chiave viene reimpostata in tutte le app nel sito Web [LUIS](luis-reference-regions.md). Se le app vengono create mediante le API di creazione, è necessario modificare il valore di `Ocp-Apim-Subscription-Key` con la nuova chiave. 

## <a name="delete-account"></a>Eliminare l'account
Per informazioni sui dati che vengono eliminati quando viene eliminato l'account, vedere [Data storage and removal](luis-concept-data-storage.md#accounts) (Archiviazione e rimozione dei dati). 

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulla [chiave di creazione](luis-concept-keys.md#authoring-key). 

