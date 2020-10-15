---
title: Microsoft Threat Modeling Tool versione 4/9/2019
titleSuffix: Azure
description: Documentazione delle note sulla versione per Threat Modeling Tool versione 7.1.60408.1.
author: jegeib
ms.author: jegeib
ms.service: security
ms.subservice: security-develop
ms.topic: article
ms.date: 04/03/2019
ms.openlocfilehash: 8d24eb868a13e7b2d9ff3d2b625302c1c1fcb908
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91318008"
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

Tutti i collegamenti di supporto all'interno dello strumento sono stati aggiornati per indirizzare gli utenti a [tmtextsupport@microsoft.com](mailto:tmtextsupport@microsoft.com) anziché a un forum di MSDN.

## <a name="system-requirements"></a>Requisiti di sistema

- Sistemi operativi supportati
  - [Aggiornamento dell'anniversario di Microsoft Windows 10](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) o versione successiva
- Versione .NET richiesta
  - [.NET 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) o versione successiva
- Requisiti aggiuntivi
  - Per ricevere aggiornamenti dello strumento e dei modelli, è necessaria una connessione Internet.

## <a name="documentation-and-feedback"></a>Documentazione e feedback

- La documentazione relativa a Threat Modeling Tool si trova su [docs.microsoft.com](threat-modeling-tool.md) e include le informazioni [sull'uso dello strumento](threat-modeling-tool-getting-started.md).

## <a name="next-steps"></a>Passaggi successivi

Scaricare la versione più recente di [Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool).
