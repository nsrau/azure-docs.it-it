---
title: Microsoft Threat Modeling Tool versione 02/11/2020-Azure
description: Documentazione delle note sulla versione per Threat Modeling Tool versione 7.3.00206.1.
author: jegeib
ms.author: jegeib
ms.service: security
ms.topic: article
ms.date: 02/25/2020
ms.openlocfilehash: 9f9b162460cd2e7a624c1ad3f992011487d1b795
ms.sourcegitcommit: 5831eebdecaa68c3e006069b3a00f724bea0875a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/11/2020
ms.locfileid: "94516935"
---
# <a name="threat-modeling-tool-update-release-73002061---02112020"></a>Versione di aggiornamento Threat Modeling Tool 7.3.00206.1-02/11/2020

La versione 7.3.00206.1 del Microsoft Threat Modeling Tool (TMT) è stata rilasciata il 11 2020 febbraio e contiene le modifiche seguenti:

- Correzioni di bug

## <a name="notable-bug-fixes"></a>Correzioni di bug rilevanti

### <a name="errors-related-to-priority-values-outside-of-the-expected-ranges"></a>Errori correlati a valori di priorità non compresi negli intervalli previsti

Alcuni clienti hanno segnalato la ricezione del messaggio di errore seguente durante l'apertura dei file creati in "Threat Modeling Tool 2016" o nei modelli personalizzati:

```output
System.InvalidOperationException: Invalid Priority value. Accepted values are [0..4] and 'High', 'Medium', 'Low' at ThreatModeling.Model.Threat.get_Priority()

System.ArgumentOutOfRangeException: Accepted values are 'High', 'Medium', and 'Low' Parameter name: value Actual value was 5.6. at ThreatModeling.Model.Threat.set_Priority(String value)
```

Questo problema è stato risolto in questa versione.

## <a name="system-requirements"></a>Requisiti di sistema

- Sistemi operativi supportati
  - [Aggiornamento dell'anniversario di Microsoft Windows 10](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) o versione successiva
- Versione .NET richiesta
  - [.NET 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) o versione successiva
- Requisiti aggiuntivi
  - Per ricevere aggiornamenti dello strumento e dei modelli, è necessaria una connessione Internet.

## <a name="documentation-and-feedback"></a>Documentazione e feedback

- La documentazione relativa a Threat Modeling Tool si trova su [docs.microsoft.com](./threat-modeling-tool.md) e include le informazioni [sull'uso dello strumento](./threat-modeling-tool-getting-started.md).

## <a name="next-steps"></a>Passaggi successivi

Scaricare la versione più recente di [Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool).