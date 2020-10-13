---
title: Azure Defender per Key Vault - Vantaggi e funzionalità
description: Informazioni sui vantaggi e sulle funzionalità di Azure Defender per Key Vault.
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: overview
ms.service: security-center
ms.custom: references_regions
manager: rkarlin
ms.openlocfilehash: 6649a8d470a75922aac423bf0b411163bdd79f71
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91449086"
---
# <a name="introduction-to-azure-defender-for-key-vault"></a>Introduzione ad Azure Defender per Key Vault

Azure Key Vault è un servizio cloud che protegge le chiavi di crittografia e i segreti, come certificati, stringhe di connessione e password. 

Abilitare **Azure Defender per Key Vault** per implementare la protezione avanzata dalle minacce nativa di Azure per Azure Key Vault, offrendo un livello aggiuntivo di intelligence sulla sicurezza. 

## <a name="availability"></a>Disponibilità

|Aspetto|Dettagli|
|----|:----|
|Stato della versione:|Disponibile a livello generale|
|Prezzi:|**Azure Defender per Key Vault** è soggetto alle tariffe visualizzate nella [pagina dei prezzi](security-center-pricing.md)|
|Cloud:|![Sì](./media/icons/yes-icon.png) Cloud commerciali<br>![No](./media/icons/no-icon.png) Cloud nazionali/sovrani (US Gov, governo cinese, altri governi)|
|||

## <a name="what-are-the-benefits-of-azure-defender-for-key-vault"></a>Quali sono i vantaggi di Azure Defender per Key Vault?

Azure Defender rileva tentativi insoliti e potenzialmente dannosi di accesso o exploit degli account Key Vault. Questo livello di protezione consente di affrontare le minacce senza dover essere esperti di sicurezza e senza bisogno di gestire sistemi di monitoraggio della sicurezza di terze parti.  

In caso di attività anomale, Azure Defender mostra avvisi e, facoltativamente, li invia tramite posta elettronica ai membri pertinenti dell'organizzazione. Questi avvisi includono i dettagli delle attività sospette e raccomandazioni su come analizzare e risolvere le minacce. 

## <a name="azure-defender-for-key-vault-alerts"></a>Avvisi di Azure Defender per Key Vault
Quando si riceve un avviso da Azure Defender per Key Vault, è consigliabile esaminarlo e rispondere come descritto in [Rispondere agli avvisi di Azure Defender per Key Vault](defender-for-key-vault-usage.md). Azure Defender per Key Vault protegge le applicazioni e le credenziali, pertanto, anche se si conosce l'applicazione o l'utente che ha attivato l'avviso, è importante verificare la situazione circostante a ogni avviso.

Gli avvisi vengono visualizzati nella pagina **Sicurezza** di Key Vault, nel dashboard di Azure Defender e nella pagina degli avvisi del Centro sicurezza.

:::image type="content" source="./media/defender-for-key-vault-intro/key-vault-security-page.png" alt-text="Pagina Sicurezza di Azure Key Vault":::


> [!TIP]
> È possibile simulare gli avvisi di Azure Defender per Key Vault seguendo le istruzioni riportate nel post di blog sulla [convalida del rilevamento minacce di Azure Key Vault nel Centro sicurezza di Azure](https://techcommunity.microsoft.com/t5/azure-security-center/validating-azure-key-vault-threat-detection-in-azure-security/ba-p/1220336).


## <a name="next-steps"></a>Passaggi successivi

In questo articolo sono state fornite informazioni su Azure Defender per Key Vault.

Per i materiali correlati, vedere gli articoli seguenti: 

- [Avvisi di sicurezza di Key Vault](alerts-reference.md#alerts-azurekv): sezione relativa a Key Vault della tabella di riferimento per tutti gli avvisi del Centro sicurezza di Azure
- [Esportazione degli avvisi in un sistema di informazioni di sicurezza e gestione degli eventi](continuous-export.md)
- [Eliminare gli avvisi di Azure Defender](alerts-suppression-rules.md)