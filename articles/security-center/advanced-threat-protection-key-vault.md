---
title: Protezione dalle minacce per Azure Key Vault
description: Questo articolo illustra come configurare Advanced Threat Protection per Azure Key Vault nel centro sicurezza di Azure
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 449096590df6145c9f80dcf2c97726931909a2ae
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77914807"
---
# <a name="threat-protection-for-azure-key-vault-preview"></a>Protezione dalle minacce per Azure Key Vault (anteprima)

Advanced Threat Protection per Azure Key Vault fornisce un ulteriore livello di intelligence per la sicurezza. Questo strumento rileva tentativi potenzialmente dannosi di accesso o exploit degli account Key Vault. Usando il Native Advanced Threat Protection nel centro sicurezza di Azure, è possibile risolvere le minacce senza essere un esperto di sicurezza e senza dover apprendere altri sistemi di monitoraggio della sicurezza.

Quando il Centro sicurezza rileva attività anomale, Visualizza gli avvisi. Invia inoltre un e-mail all'amministratore della sottoscrizione con i dettagli dell'attività sospetta e consigli su come analizzare e correggere le minacce identificate.

## <a name="configuring-threat-protection-from-security-center"></a>Configurazione della protezione dalle minacce dal centro sicurezza

Per impostazione predefinita, Advanced Threat Protection è abilitato per tutti gli account di Key Vault quando si sottoscrive il piano tariffario standard del Centro sicurezza. Per altre informazioni, vedere [Prezzi](security-center-pricing.md).

Per abilitare o disabilitare la protezione per una sottoscrizione specifica:

1. Nel riquadro sinistro del Centro sicurezza selezionare **prezzi & impostazioni**.

1. Selezionare la sottoscrizione con gli account di archiviazione per cui si vuole abilitare o disabilitare la protezione dalle minacce.

1. Selezionare **Piano tariffario**.

1. Dal gruppo **selezionare il piano tariffario per tipo di risorsa** individuare la riga **Key Vault** e selezionare **abilitato** o **disabilitato**.

    [![Abilitazione o disabilitazione di Advanced Threat Protection per Key Vault nel centro sicurezza di Azure](media/advanced-threat-protection-key-vault/atp-for-akv-enable-atp-for-akv.png)](media/advanced-threat-protection-key-vault/atp-for-akv-enable-atp-for-akv.png#lightbox)

1. Selezionare **Salva**.


## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come abilitare e disabilitare Advanced Threat Protection per Azure Key Vault. 

Per materiale correlato, vedere gli articoli seguenti:

- [Protezione dalle minacce nel centro sicurezza di Azure](threat-protection.md): questo articolo descrive le origini degli avvisi di sicurezza nel centro sicurezza di Azure.
- [Key Vault gli avvisi di sicurezza](alerts-reference.md#alerts-azurekv): la sezione Key Vault della tabella di riferimento per tutti gli avvisi del Centro sicurezza di Azure