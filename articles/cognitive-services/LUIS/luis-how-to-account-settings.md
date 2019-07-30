---
title: Gestisci account e chiavi-LUIS
titleSuffix: Azure Cognitive Services
description: Le informazioni principali di un account LUIS sono l'account utente e la chiave di creazione. Le informazioni di accesso sono gestite in account.microsoft.com. La chiave di creazione viene gestita dalla pagina delle impostazioni del portale LUIS.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: 28f8a15922a3a3e4e3b464b86bdfea07d329a848
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68638279"
---
# <a name="manage-account-and-authoring-key"></a>Gestire l'account e la chiave di creazione

Le informazioni principali di un account LUIS sono l'account utente e la chiave di creazione. Le informazioni di accesso sono gestite in [account.microsoft.com](https://account.microsoft.com). La chiave di creazione viene gestita dalla pagina **delle impostazioni** del portale [Luis](luis-reference-regions.md) .

## <a name="authoring-key"></a>Chiave di creazione

Questa singola chiave di creazione specifica dell'area, nella pagina **Impostazioni** , consente di creare tutte le app dal portale [Luis](luis-reference-regions.md) , oltre che dalle [API di creazione](https://go.microsoft.com/fwlink/?linkid=2092087). Per praticità, la chiave di creazione è autorizzata a creare un numero [limitato](luis-boundaries.md) di query di endpoint al mese.

[![Pagina delle impostazioni di LUIS](./media/luis-how-to-account-settings/account-settings.png)](./media/luis-how-to-account-settings/account-settings.png#lightbox)

La chiave di creazione viene usata per le app di cui si è titolari e per le app di cui si è collaboratori.

## <a name="authoring-key-regions"></a>Aree delle chiavi di creazione

La chiave di creazione è specifica dell'[area di creazione](luis-reference-regions.md#publishing-regions). La chiave non può essere usata in un'area diversa.

## <a name="reset-authoring-key"></a>Reimpostare la chiave di creazione

Se la chiave di creazione è danneggiata, reimpostare la chiave. La chiave viene reimpostata su tutte le app nel portale [Luis](luis-reference-regions.md) . Se le app vengono create mediante le API di creazione, è necessario modificare il valore di `Ocp-Apim-Subscription-Key` con la nuova chiave.

## <a name="delete-account"></a>Eliminare l'account

Per informazioni sui dati che vengono eliminati quando viene eliminato l'account, vedere [Data storage and removal](luis-concept-data-storage.md#accounts) (Archiviazione e rimozione dei dati).

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulla [chiave di creazione](luis-concept-keys.md#authoring-key).

