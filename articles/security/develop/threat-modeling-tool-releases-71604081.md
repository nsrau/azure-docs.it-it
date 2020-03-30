---
title: Microsoft Threat Modeling Tool versione 4/9/2019
titleSuffix: Azure
description: Documentazione delle note sulla versione per lo strumento di modellazione delle minacce
author: jegeib
ms.author: jegeib
ms.service: security
ms.subservice: security-develop
ms.topic: article
ms.date: 04/03/2019
ms.openlocfilehash: 59d385ba7de5bf7bceae4dc8ddadbca813046094
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78269720"
---
# <a name="threat-modeling-tool-update-release-71604081---492019"></a>Aggiornamento di Threat Modeling Tool versione 7.1.60408.1 - 4/9/2019

La versione 7.1.60408.1 di Microsoft Threat Modeling Tool (TMT) è stata rilasciata il 9 aprile 2019 e contiene le seguenti modifiche:

- Nuovi stencil per L'insieme di credenziali delle chiavi di Azure e Gestione traffico di AzureNew Stencils for Azure Key Vault and Azure Traffic Manager
- Il numero di versione TMT viene ora visualizzato nella schermata iniziale
- I link di supporto sono stati aggiornati
- Correzioni di bug

## <a name="feature-changes"></a>Modifiche apportate alle funzionalità

### <a name="new-stencils-for-azure-key-vault-and-azure-traffic-manager"></a>Nuovi stencil per L'insieme di credenziali delle chiavi di Azure e Gestione traffico di AzureNew Stencils for Azure Key Vault and Azure Traffic Manager

![Stencil dell'insieme di credenziali delle chiavi di AzureAzure Key Vault Stencil](./media/threat-modeling-tool-releases-71604081/tmt_keyvault_trafficmanager.PNG)

Nuovi stencil e minacce per L'insieme di credenziali delle chiavi di Azure e Gestione traffico di Azure sono stati aggiunti al set di stencil di Azure.New stencils and threats for Azure Key Vault and Azure Traffic Manager have been added to the Azure stencil set. Quando si aprono modelli basati sullo stencil di Azure, agli utenti verrà richiesto di aggiornare il modello associato al modello. L'aggiornamento di un modello basato sullo stencil di Azure può anche essere avviato manualmente usando il comando "Applica modello" nel menu "File" e riapplicando il file di Azure Cloud Services.tb7 più recente.

### <a name="tmt-version-number-is-now-shown-on-the-home-screen"></a>Il numero di versione TMT viene ora visualizzato nella schermata iniziale

La versione client di Threat Modeling Tool viene ora visualizzata nella schermata iniziale dell'applicazione per facilitare l'accesso.

![Stencil dell'insieme di credenziali delle chiavi di AzureAzure Key Vault Stencil](./media/threat-modeling-tool-releases-71604081/tmt_version.PNG)

### <a name="support-links-have-been-updated"></a>I link di supporto sono stati aggiornati

Tutti i collegamenti di supporto all'interno [tmtextsupport@microsoft.com](mailto:tmtextsupport@microsoft.com) dello strumento sono stati aggiornati per indirizzare gli utenti a anziché un forum MSDN.

## <a name="system-requirements"></a>Requisiti di sistema

- Sistemi operativi supportati
  - [Aggiornamento dell'anniversario di Microsoft Windows 10](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) o versione successiva
- Versione .NET richiesta
  - [.Net 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) o versione successiva
- Requisiti aggiuntivi
  - Per ricevere aggiornamenti dello strumento e dei modelli, è necessaria una connessione Internet.

## <a name="documentation-and-feedback"></a>Documentazione e feedback

- La documentazione relativa a Threat Modeling Tool si trova su [docs.microsoft.com](threat-modeling-tool.md) e include le informazioni [sull'uso dello strumento](threat-modeling-tool-getting-started.md).

## <a name="next-steps"></a>Passaggi successivi

Scaricare la versione più recente di [Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool).
