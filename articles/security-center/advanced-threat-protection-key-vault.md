---
title: Protezione dalle minacce per l'insieme di credenziali delle chiavi di Azure
description: Questo articolo illustra come configurare la protezione avanzata dalle minacce per l'insieme di credenziali delle chiavi di Azure nel Centro sicurezza di AzureThis article explains how to set up advanced threat protection for Azure Key Vault in Azure Security Center
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 449096590df6145c9f80dcf2c97726931909a2ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77914807"
---
# <a name="threat-protection-for-azure-key-vault-preview"></a>Protezione dalle minacce per l'insieme di credenziali delle chiavi di Azure (anteprima)Threat protection for Azure Key Vault (preview)

La protezione avanzata dalle minacce per Azure Key Vault offre un ulteriore livello di intelligence di sicurezza. Questo strumento rileva i tentativi potenzialmente dannosi di accedere o sfruttare gli account key Vault. Usando la protezione avanzata dalle minacce nativa nel Centro sicurezza di Azure, è possibile affrontare le minacce senza essere esperti di sicurezza e senza apprendere altri sistemi di monitoraggio della sicurezza.

Quando il Centro sicurezza rileva attività anomale, visualizza avvisi. Invia inoltre un messaggio di posta elettronica all'amministratore della sottoscrizione con i dettagli delle attività sospette e consigli su come analizzare e correggere le minacce identificate.

## <a name="configuring-threat-protection-from-security-center"></a>Configurazione della protezione dalle minacce dal Centro sicurezza

Per impostazione predefinita, la protezione avanzata dalle minacce è abilitata per tutti gli account dell'insieme di credenziali delle chiavi quando si sottoscrive il piano tariffario standard del Centro sicurezza. Per altre informazioni, vedere [Prezzi](security-center-pricing.md).

Per abilitare o disabilitare la protezione per una sottoscrizione specifica:

1. Nel riquadro sinistro del Centro sicurezza selezionare **Impostazioni di & dei prezzi**.

1. Selezionare la sottoscrizione con gli account di archiviazione per cui si vuole abilitare o disabilitare la protezione dalle minacce.

1. Selezionare **Piano prezzi**.

1. Nel gruppo **Seleziona piano prezzi per tipo di risorsa** individuare la riga **Insiemi di** credenziali delle chiavi e selezionare **Abilitato** o **Disabilitato**.

    [![Abilitazione o disabilitazione della protezione avanzata dalle minacce per l'insieme di credenziali delle chiavi nel Centro sicurezza di AzureEnabling or disabling advanced threat protection for Key Vault in Azure Security Center](media/advanced-threat-protection-key-vault/atp-for-akv-enable-atp-for-akv.png)](media/advanced-threat-protection-key-vault/atp-for-akv-enable-atp-for-akv.png#lightbox)

1. Selezionare **Salva**.


## <a name="next-steps"></a>Passaggi successivi

In this article, you learned how to enable and disable advanced threat protection for Azure Key Vault. 

Per il materiale correlato, vedere i seguenti articoli:

- [Protezione dalle minacce nel Centro sicurezza](threat-protection.md)di Azure: in questo articolo vengono descritte le origini degli avvisi di sicurezza nel Centro sicurezza di Azure.Threat protection in Azure Security Center --This article describes the sources of security alerts in Azure Security Center.
- Avvisi di [sicurezza dell'insieme](alerts-reference.md#alerts-azurekv)di credenziali delle chiavi: sezione Key Vault della tabella di riferimento per tutti gli avvisi del Centro sicurezza di AzureKey Vault security alerts -- The Key Vault section of the reference table for all Azure Security Center alerts