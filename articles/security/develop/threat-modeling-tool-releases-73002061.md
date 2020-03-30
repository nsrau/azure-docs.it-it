---
title: Microsoft Threat Modeling Tool versione 02/11/2020 - Azure
description: Documentazione delle note sulla versione per lo strumento di modellazione delle minacce
author: jegeib
ms.author: jegeib
ms.service: security
ms.topic: article
ms.date: 02/25/2020
ms.openlocfilehash: 354707aec90375b4bf25aea6e1baa743d85f20aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77624839"
---
# <a name="threat-modeling-tool-update-release-73002061---02112020"></a>Aggiornamento di Threat Modeling Tool versione 7.3.00206.1 - 11/02/02/2020

La versione 7.3.00206.1 di Microsoft Threat Modeling Tool (TMT) è stata rilasciata l'11 febbraio 2020 e contiene le seguenti modifiche:

- Correzioni di bug

## <a name="notable-bug-fixes"></a>Correzioni di bug notevoli

### <a name="errors-related-to-priority-values-outside-of-the-expected-ranges"></a>Errori relativi a valori di priorità al di fuori degli intervalli previsti

Alcuni clienti avevano segnalato la ricezione del seguente messaggio di errore quando si aprono i file creati in "Threat Modeling Tool 2016" o modelli personalizzati:

    System.InvalidOperationException: Invalid Priority value. Accepted values are [0..4] and 'High', 'Medium', 'Low' at ThreatModeling.Model.Threat.get_Priority()
    
    System.ArgumentOutOfRangeException: Accepted values are 'High', 'Medium', and 'Low' Parameter name: value Actual value was 5.6. at ThreatModeling.Model.Threat.set_Priority(String value)

Questo problema è stato risolto in questa versione.

## <a name="system-requirements"></a>Requisiti di sistema

- Sistemi operativi supportati
  - [Aggiornamento dell'anniversario di Microsoft Windows 10](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) o versione successiva
- Versione .NET richiesta
  - [.Net 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) o versione successiva
- Requisiti aggiuntivi
  - Per ricevere aggiornamenti dello strumento e dei modelli, è necessaria una connessione Internet.

## <a name="documentation-and-feedback"></a>Documentazione e feedback

- La documentazione relativa a Threat Modeling Tool si trova su [docs.microsoft.com](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool) e include le informazioni [sull'uso dello strumento](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-getting-started).

## <a name="next-steps"></a>Passaggi successivi

Scaricare la versione più recente di [Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool).
