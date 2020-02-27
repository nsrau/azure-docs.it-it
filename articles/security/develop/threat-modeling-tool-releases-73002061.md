---
title: Microsoft Threat Modeling Tool versione 02/11/2020-Azure
description: Documentazione delle note sulla versione per lo strumento di modellazione delle minacce
author: jegeib
ms.author: jegeib
ms.service: security
ms.topic: article
ms.date: 02/25/2020
ms.openlocfilehash: 354707aec90375b4bf25aea6e1baa743d85f20aa
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/26/2020
ms.locfileid: "77624839"
---
# <a name="threat-modeling-tool-update-release-73002061---02112020"></a>Versione di aggiornamento Threat Modeling Tool 7.3.00206.1-02/11/2020

La versione 7.3.00206.1 del Microsoft Threat Modeling Tool (TMT) è stata rilasciata il 11 2020 febbraio e contiene le modifiche seguenti:

- Correzioni di bug

## <a name="notable-bug-fixes"></a>Correzioni di bug rilevanti

### <a name="errors-related-to-priority-values-outside-of-the-expected-ranges"></a>Errori correlati a valori di priorità non compresi negli intervalli previsti

Alcuni clienti hanno segnalato la ricezione del messaggio di errore seguente durante l'apertura dei file creati in "Threat Modeling Tool 2016" o nei modelli personalizzati:

    System.InvalidOperationException: Invalid Priority value. Accepted values are [0..4] and 'High', 'Medium', 'Low' at ThreatModeling.Model.Threat.get_Priority()
    
    System.ArgumentOutOfRangeException: Accepted values are 'High', 'Medium', and 'Low' Parameter name: value Actual value was 5.6. at ThreatModeling.Model.Threat.set_Priority(String value)

Questo problema è stato risolto in questa versione.

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
