---
title: Usare l'analisi del comportamento dell'entità per rilevare le minacce avanzate | Microsoft Docs
description: Abilitare l'analisi del comportamento di utenti ed entità in Sentinel di Azure e configurare le origini dati
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/15/2020
ms.author: yelevin
ms.openlocfilehash: c55ea0e7753faa6dc21b955d63a57d96e3849f70
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90997118"
---
# <a name="enable-user-and-entity-behavior-analytics-ueba-in-azure-sentinel"></a>Abilitare l'analisi del comportamento dell'utente e dell'entità (offrono dati) in Sentinel di Azure 



## <a name="prerequisites"></a>Prerequisiti

- È necessario che all'utente siano assegnati i ruoli di amministratore **globale** o di **amministratore della sicurezza** in Azure ad per abilitare o disabilitare offrono dati, ma non per eseguirlo.

## <a name="how-to-enable-user-and-entity-behavior-analytics"></a>Come abilitare l'analisi del comportamento dell'utente e dell'entità

1. Dal menu di navigazione di Azure Sentinel selezionare **comportamento entità (anteprima)**.

1. Sotto l'intestazione **attiva**, impostare l'interruttore **su on**.

1. Fare clic sul pulsante **Seleziona origini dati** .

1. Nel riquadro di **selezione dell'origine dati** selezionare le caselle di controllo accanto alle origini dati in cui si desidera abilitare offrono dati, quindi selezionare **applica**.

    > [!NOTE]
    >
    > Nella metà inferiore del riquadro di **selezione dell'origine dati** viene visualizzato un elenco di origini dati supportate da offrono dati che non sono ancora state abilitate. 
    >
    > Dopo aver abilitato offrono dati, si avrà l'opzione, quando si connettono nuove origini dati, per abilitarle per offrono dati direttamente dal riquadro connettore dati, se sono compatibili con offrono dati.

1. Selezionare **Vai a ricerca entità**. Verrà visualizzato il riquadro ricerca entità, che da ora in poi sarà ciò che viene visualizzato quando si sceglie **comportamento entità** dal menu principale di Azure Sentinel.

## <a name="next-steps"></a>Passaggi successivi
In questo documento si è appreso come abilitare e configurare l'analisi del comportamento dell'utente e dell'entità (offrono dati) in Sentinel di Azure. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).
