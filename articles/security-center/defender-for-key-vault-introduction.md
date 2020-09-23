---
title: 'Azure Defender per Key Vault: vantaggi e funzionalità'
description: Scopri i vantaggi e le funzionalità di Azure Defender per Key Vault.
author: memildin
ms.author: memildin
ms.date: 9/12/2020
ms.topic: conceptual
ms.service: security-center
ms.custom: references_regions
manager: rkarlin
ms.openlocfilehash: 06818e443568918e2ee87bbfbec81836ea85648b
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90939879"
---
# <a name="introduction-to-azure-defender-for-key-vault"></a>Introduzione ad Azure Defender per Key Vault

Azure Key Vault è un servizio cloud che protegge le chiavi di crittografia e i segreti, come certificati, stringhe di connessione e password. 

Abilita **Azure Defender per Key Vault** per la protezione avanzata dalle minacce di Azure nativa per Azure Key Vault, offrendo un ulteriore livello di intelligence per la sicurezza. 

## <a name="availability"></a>Disponibilità

|Aspetto|Dettagli|
|----|:----|
|Stato versione:|Disponibile a livello generale|
|Prezzi|**Azure Defender per Key Vault** viene fatturato come indicato nella [pagina dei prezzi](security-center-pricing.md)|
|Cloud:|![Sì](./media/icons/yes-icon.png) Cloud commerciali<br>![No](./media/icons/no-icon.png) Nazionale/sovrano (US Gov, Cina gov, altri gov)|
|||

## <a name="what-are-the-benefits-of-azure-defender-for-key-vault"></a>Quali sono i vantaggi di Azure Defender per Key Vault?

Azure Defender rileva tentativi insoliti e potenzialmente dannosi di accesso o exploit Key Vault account. Questo livello di protezione consente di affrontare le minacce senza dover essere esperti di sicurezza e senza bisogno di gestire sistemi di monitoraggio della sicurezza di terze parti.  

Quando si verificano attività anomale, Azure Defender Mostra gli avvisi e, facoltativamente, li invia tramite posta elettronica ai membri pertinenti dell'organizzazione. Questi avvisi includono i dettagli delle attività sospette e raccomandazioni su come analizzare e risolvere le minacce. 

## <a name="azure-defender-for-key-vault-alerts"></a>Avvisi di Azure Defender per Key Vault
Quando si riceve un avviso da Azure Defender per Key Vault, è consigliabile esaminare e rispondere all'avviso come descritto in [rispondere ad Azure Defender per Key Vault](defender-for-key-vault-usage.md). Azure Defender per Key Vault protegge le applicazioni e le credenziali, quindi anche se si ha familiarità con l'applicazione o l'utente che ha attivato l'avviso, è importante verificare la situazione che circonda ogni avviso.

Gli avvisi vengono visualizzati nella pagina **sicurezza** di Key Vault, nel dashboard di Azure Defender e nella pagina degli avvisi del Centro sicurezza.

:::image type="content" source="./media/defender-for-key-vault-intro/key-vault-security-page.png" alt-text="Pagina sicurezza di Azure Key Vault":::

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come Azure Defender per Key Vault.

Per i materiali correlati, vedere gli articoli seguenti: 

- [Key Vault gli avvisi di sicurezza](alerts-reference.md#alerts-azurekv): la sezione Key Vault della tabella di riferimento per tutti gli avvisi del Centro sicurezza di Azure
- [Esportazione di avvisi in un SIEM](continuous-export.md)
- [Non visualizzare avvisi da Azure Defender](alerts-suppression-rules.md)