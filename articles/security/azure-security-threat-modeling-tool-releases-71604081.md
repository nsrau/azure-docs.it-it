---
title: Versioni di Threat Modeling Tool - Microsoft Threat Modeling Tool - Azure | Microsoft Docs
description: Documentazione delle note sulla versione per lo strumento di modellazione delle minacce
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/03/2019
ms.author: jegeib
ms.openlocfilehash: 502c1e8a422eb9e74586e5a6820d5b12ec4ae2a4
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/11/2019
ms.locfileid: "59501904"
---
# <a name="threat-modeling-tool-update-release-71604081---492019"></a>Threat Modeling Tool aggiornamento versione 7.1.60408.1 - 4 o 9/2019

Versione 7.1.60408.1 di Microsoft Threat Modeling Tool (TMT) è stata rilasciata nel mese di aprile 2019 9 e contiene le seguenti modifiche:

- Nuovo stencil per gestione traffico di Azure e Azure Key Vault
- Numero di versione TMT è ora visualizzata nella schermata iniziale
- Collegamenti di supporto sono stati aggiornati
- Correzioni di bug

## <a name="feature-changes"></a>Modifiche apportate alle funzionalità

### <a name="new-stencils-for-azure-key-vault-and-azure-traffic-manager"></a>Nuovo stencil per gestione traffico di Azure e Azure Key Vault

![Azure Key Vault Stencil](./media/azure-security-threat-modeling-tool-releases-71604081/tmt_keyvault_trafficmanager.PNG)

Nuovo stencil e le minacce per Azure Key Vault e gestione traffico di Azure sono stati aggiunti al set di uno stencil di Azure. Quando si apre modelli basati su set di uno stencil di Azure, gli utenti verranno richiesto di aggiornare il modello associato al modello. Aggiornamento di un modello in base al set di uno stencil di Azure può anche essere avviato manualmente dall'utilizzando il comando "Applica modello" nel menu "File" e riapplicare il file Services.tb7 Cloud di Azure più recente.

### <a name="tmt-version-number-is-now-shown-on-the-home-screen"></a>Numero di versione TMT è ora visualizzata nella schermata iniziale

La versione del client di Threat Modeling Tool è ora visualizzata nella schermata iniziale dell'applicazione di per facilitare l'accesso.

![Azure Key Vault Stencil](./media/azure-security-threat-modeling-tool-releases-71604081/tmt_version.PNG)

### <a name="support-links-have-been-updated"></a>Collegamenti di supporto sono stati aggiornati

Tutti i collegamenti nello strumento di supporto sono stati aggiornati per indirizzare gli utenti al [ tmtextsupport@microsoft.com ](mailto:tmtextsupport@microsoft.com) anziché un forum MSDN.

## <a name="system-requirements"></a>Requisiti di sistema

- Sistemi operativi supportati
  - [Microsoft Windows 10 Anniversary Update](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) o versione successiva
- Versione .NET richiesta
  - [.Net 4.7.1](http://go.microsoft.com/fwlink/?LinkId=863262) o versione successiva
- Requisiti aggiuntivi
  - Per ricevere aggiornamenti dello strumento e dei modelli, è necessaria una connessione Internet.

## <a name="documentation-and-feedback"></a>Documentazione e feedback

- La documentazione relativa a Threat Modeling Tool si trova su [docs.microsoft.com](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool) e include le informazioni [sull'uso dello strumento](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-getting-started).

## <a name="next-steps"></a>Passaggi successivi

Scaricare la versione più recente di [Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool).