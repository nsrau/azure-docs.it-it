---
title: Microsoft Threat Modeling Tool versione 10/16/2019-Azure
description: Documentazione delle note sulla versione per Threat Modeling Tool versione 7.1.61015.1.
author: jegeib
ms.author: jegeib
ms.service: security
ms.topic: article
ms.date: 10/16/2019
ms.openlocfilehash: e45d1347993611130d1bb5036ad4638210c15517
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91317940"
---
# <a name="threat-modeling-tool-update-release-71610151---10162019"></a>Aggiornamento di Threat Modeling Tool versione 7.1.61015.1 - 10/16/2019

La versione 7.1.61015.1 del Microsoft Threat Modeling Tool (TMT) è stata rilasciata il 16 2019 ottobre e contiene le modifiche seguenti:

- Miglioramenti all'accessibilità
- Correzioni di bug
- Nuovi stencil per app per la logica di Azure e Azure Esplora dati

## <a name="notable-bug-fixes"></a>Correzioni di bug rilevanti

### <a name="improved-backward-compatibility-with-files-created-in-threat-modeling-tool-2016"></a>Compatibilità con le versioni precedenti migliorata con i file creati in "Threat Modeling Tool 2016"

Sono stati corretti diversi bug correlati all'apertura o alla visualizzazione dei file del modello di minaccia creati in "Threat Modeling Tool 2016".

## <a name="feature-enhancements"></a>Miglioramenti delle funzionalità

### <a name="new-stencils-for-azure-logic-apps-and-azure-data-explorer"></a>Nuovi stencil per app per la logica di Azure e Azure Esplora dati

Sono stati aggiunti nuovi stencil per app per la logica di Azure e Esplora dati Azure allo stencil di Azure insieme alle relative minacce e mitigazioni associate.

![App per la logica di Azure e stencil di Azure Esplora dati](./media/threat-modeling-tool-releases-71610151/tmt-logic-apps.png)

## <a name="known-issues"></a>Problemi noti

### <a name="errors-related-to-priority-values-outside-of-the-expected-ranges"></a>Errori correlati a valori di priorità non compresi negli intervalli previsti

Alcuni clienti hanno segnalato la ricezione del messaggio di errore seguente all'apertura dei file creati in "Threat Modeling Tool 2016" o nei modelli personalizzati:

```output
System.InvalidOperationException: Invalid Priority value. Accepted values are [0..4] and 'High', 'Medium', 'Low' at ThreatModeling.Model.Threat.get_Priority()

System.ArgumentOutOfRangeException: Accepted values are 'High', 'Medium', and 'Low' Parameter name: value Actual value was 5.6. at ThreatModeling.Model.Threat.set_Priority(String value)
```

Questo problema è in fase di analisi

## <a name="system-requirements"></a>Requisiti di sistema

- Sistemi operativi supportati
  - [Aggiornamento dell'anniversario di Microsoft Windows 10](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) o versione successiva
- Versione .NET richiesta
  - [.NET 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) o versione successiva
- Requisiti aggiuntivi
  - Per ricevere aggiornamenti dello strumento e dei modelli, è necessaria una connessione Internet.

## <a name="documentation-and-feedback"></a>Documentazione e feedback

- La documentazione relativa a Threat Modeling Tool si trova su [docs.microsoft.com](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool) e include le informazioni [sull'uso dello strumento](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-getting-started).

## <a name="next-steps"></a>Passaggi successivi

Scaricare la versione più recente di [Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool).
