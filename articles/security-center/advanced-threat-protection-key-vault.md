---
title: Configurare Advanced Threat Protection per Azure Key Vault
description: Questo articolo illustra come configurare Advanced Threat Protection per Azure Key Vault nel centro sicurezza di Azure
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 07924201d4208a502aa979dda476c57cf6edd749
ms.sourcegitcommit: dfa543fad47cb2df5a574931ba57d40d6a47daef
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/18/2020
ms.locfileid: "77430551"
---
# <a name="set-up-advanced-threat-protection-for-azure-key-vault-preview"></a>Configurare Advanced Threat Protection per Azure Key Vault (anteprima)

Advanced Threat Protection per Azure Key Vault fornisce un ulteriore livello di intelligence per la sicurezza. Questo strumento rileva tentativi potenzialmente dannosi di accesso o exploit degli account Key Vault. Usando il Native Advanced Threat Protection nel centro sicurezza di Azure, è possibile risolvere le minacce senza essere un esperto di sicurezza e senza dover apprendere altri sistemi di monitoraggio della sicurezza.

Quando il Centro sicurezza rileva attività anomale, Visualizza gli avvisi. Invia inoltre un e-mail all'amministratore della sottoscrizione con i dettagli dell'attività sospetta e consigli su come analizzare e correggere le minacce identificate.

## <a name="set-up-advanced-threat-protection-from-azure-security-center"></a>Configurare Advanced Threat Protection dal centro sicurezza di Azure

Per impostazione predefinita, Advanced Threat Protection è abilitato per tutti gli account di Key Vault quando si sottoscrive il livello standard del Centro sicurezza. Per altre informazioni, vedere [Prezzi](security-center-pricing.md).

Per abilitare o disabilitare la protezione per una sottoscrizione specifica, attenersi alla seguente procedura.

1. Nel riquadro sinistro del Centro sicurezza selezionare **prezzi & impostazioni**.
1. Selezionare la sottoscrizione con gli account di archiviazione per cui si vuole abilitare o disabilitare la protezione dalle minacce.
1. Selezionare **Piano tariffario**.
1. Dal gruppo **selezionare il piano tariffario per tipo di risorsa** individuare la riga **Key Vault** e selezionare **abilitato** o **disabilitato**.

    [![abilitazione o disabilitazione di Advanced Threat Protection per Key Vault nel centro sicurezza di Azure](media/advanced-threat-protection-key-vault/atp-for-akv-enable-atp-for-akv.png)](media/advanced-threat-protection-key-vault/atp-for-akv-enable-atp-for-akv.png#lightbox)
1. Selezionare **Salva**.


## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come abilitare e disabilitare Advanced Threat Protection per Azure Key Vault. 

Per altri materiali correlati, vedere l'articolo seguente:

- [Rilevamento delle minacce per i livelli dei servizi di Azure nel centro sicurezza](security-center-alerts-service-layer.md): questo articolo descrive gli avvisi relativi alla protezione avanzata dalle minacce per Azure Key Vault.
