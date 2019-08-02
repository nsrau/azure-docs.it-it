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
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/03/2019
ms.author: jegeib
ms.openlocfilehash: df4eb5c247d4ec2c3da266c64899527c3ebe447d
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68728012"
---
# <a name="threat-modeling-tool-update-release-71604081---492019"></a>Versione di aggiornamento Threat Modeling Tool 7.1.60408.1-4/9/2019

La versione 7.1.60408.1 del Microsoft Threat Modeling Tool (TMT) è stata rilasciata il 9 2019 aprile e contiene le modifiche seguenti:

- Nuovi stencil per Azure Key Vault e gestione traffico di Azure
- Il numero di versione di TMT è ora visualizzato nella schermata iniziale
- Sono stati aggiornati i collegamenti di supporto
- Correzioni di bug

## <a name="feature-changes"></a>Modifiche apportate alle funzionalità

### <a name="new-stencils-for-azure-key-vault-and-azure-traffic-manager"></a>Nuovi stencil per Azure Key Vault e gestione traffico di Azure

![Stencil Azure Key Vault](./media/threat-modeling-tool-releases-71604081/tmt_keyvault_trafficmanager.PNG)

Al set di stencil di Azure sono stati aggiunti nuovi stencil e minacce per Azure Key Vault e gestione traffico di Azure. Quando si aprono modelli basati sul set di stencil di Azure, agli utenti verrà richiesto di aggiornare il modello associato al modello. L'aggiornamento di un modello basato sul set di stencil di Azure può anche essere avviato manualmente usando il comando "Applica modello" nel menu "file" e riapplicando il file TB7 di servizi cloud di Azure più recente.

### <a name="tmt-version-number-is-now-shown-on-the-home-screen"></a>Il numero di versione di TMT è ora visualizzato nella schermata iniziale

La versione client del Threat Modeling Tool viene ora visualizzata nella schermata iniziale dell'applicazione di per semplificare l'accesso.

![Stencil Azure Key Vault](./media/threat-modeling-tool-releases-71604081/tmt_version.PNG)

### <a name="support-links-have-been-updated"></a>Sono stati aggiornati i collegamenti di supporto

Tutti i collegamenti di supporto all'interno dello strumento sono stati aggiornati per [tmtextsupport@microsoft.com](mailto:tmtextsupport@microsoft.com) indirizzare gli utenti a anziché a un forum di MSDN.

## <a name="system-requirements"></a>Requisiti di sistema

- Sistemi operativi supportati
  - [Microsoft Windows 10 Anniversary Update](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) o versione successiva
- Versione .NET richiesta
  - [.Net 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) o versione successiva
- Requisiti aggiuntivi
  - Per ricevere aggiornamenti dello strumento e dei modelli, è necessaria una connessione Internet.

## <a name="documentation-and-feedback"></a>Documentazione e feedback

- La documentazione relativa a Threat Modeling Tool si trova su [docs.microsoft.com](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool) e include le informazioni [sull'uso dello strumento](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-getting-started).

## <a name="next-steps"></a>Passaggi successivi

Scaricare la versione più recente di [Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool).
