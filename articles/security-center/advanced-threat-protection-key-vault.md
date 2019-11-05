---
title: Come configurare Advanced Threat Protection per Azure Key Vault | Microsoft Docs
description: Questo articolo illustra come configurare Advanced Threat Protection per Azure Key Vault nel centro sicurezza di Azure
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: b28f03f0997b6bb2c494c35cee9695a478906c47
ms.sourcegitcommit: 3f8017692169bd75483eefa96c225d45cd497f06
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73521878"
---
# <a name="how-to-set-up-advanced-threat-protection-for-azure-key-vault-preview"></a>Come configurare Advanced Threat Protection per Azure Key Vault (anteprima)

Advanced Threat Protection per Azure Key Vault fornisce un ulteriore livello di intelligence per la sicurezza. Questo strumento rileva tentativi potenzialmente dannosi di accesso o exploit degli account Key Vault. Grazie alla protezione avanzata dalle minacce nativa del Centro sicurezza, è possibile risolvere le minacce senza essere un esperto di sicurezza e senza dover apprendere altri sistemi di monitoraggio della sicurezza.

Quando il Centro sicurezza rileva attività anomale, Visualizza gli avvisi. Invia inoltre un e-mail all'amministratore della sottoscrizione con i dettagli dell'attività sospetta e consigli su come analizzare e correggere le minacce identificate. 

> [!NOTE]
> Advanced Threat Protection per Azure Key Vault è attualmente disponibile solo nelle aree America del Nord.

## <a name="to-set-up-advanced-threat-protection-from-azure-security-center"></a>Per configurare Advanced Threat Protection dal centro sicurezza di Azure

Per impostazione predefinita, Advanced Threat Protection è abilitato per tutti gli account di Key Vault quando si sottoscrive il livello standard del Centro sicurezza (vedere i [prezzi](security-center-pricing.md)). 

Per abilitare o disabilitare la protezione per una sottoscrizione specifica:

1. Dall'intestazione laterale del Centro sicurezza fare clic su **prezzi & impostazioni**.
1. Selezionare la sottoscrizione con gli account di archiviazione per cui si vuole abilitare o disabilitare la protezione dalle minacce.
1. Fare clic su **Piano tariffario**.
1. Dal gruppo **selezionare il piano tariffario per tipo di risorsa** individuare la riga insiemi di credenziali delle chiavi e fare clic su **abilitato** o **disabilitato**.
    [![abilitazione o disabilitazione di Advanced Threat Protection per Key Vault nel centro sicurezza di Azure](media/advanced-threat-protection-key-vault/atp-for-akv-enable-atp-for-akv.png)](media/advanced-threat-protection-key-vault/atp-for-akv-enable-atp-for-akv.png#lightbox)
1. Fare clic su **Save**.


## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come abilitare e disabilitare Advanced Threat Protection per Azure Key Vault. 

Per altri materiali correlati, vedere l'articolo seguente:

- [Rilevamento delle minacce per i livelli dei servizi di Azure nel centro sicurezza](security-center-alerts-service-layer.md) : questo articolo descrive gli avvisi relativi alla protezione avanzata dalle minacce per Azure Key Vault