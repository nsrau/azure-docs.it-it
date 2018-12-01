---
title: Uso di dati di monitoraggio di Azure Stack | Microsoft Docs
description: Informazioni sulle opzioni per l'utilizzo di dati di monitoraggio di Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/01/2018
ms.author: mabrigg
ms.openlocfilehash: fbd4552a9e40f16a6fedec4e04be0d7d6d39351d
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2018
ms.locfileid: "52724486"
---
# <a name="how-to-consume-monitoring-data-from-azure-stack"></a>Come usare i dati di monitoraggio di Azure Stack

*Si applica a: i sistemi integrati di Azure Stack*

È possibile trovare i dati di monitoraggio in un'unica posizione con la pipeline di monitoraggio di Azure, semplicemente, ad esempio monitoraggio di Azure in Azure globale. Ma non tutti i dati di monitoraggio disponibili in Azure globale è disponibile in Azure Stack. In questo articolo, è possibile trovare un riepilogo dei vari metodi che a livello di codice è possibile inserire i dati di monitoraggio dal servizio.
 
## <a name="options-for-data-consumption"></a>Opzioni per l'uso di dati

| Tipo di dati | Categoria | Servizi supportati | Metodi di accesso |
|-------------------------------------------------------------|----------|------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------|
| Metriche a livello di piattaforma di Monitoraggio di Azure | Metriche | [Metriche supportate con monitoraggio di Azure in Azure Stack](azure-stack-metrics-supported.md) | API REST |
| Calcolare le metriche del sistema operativo guest (ad esempio, il conteggio delle prestazioni) | Metriche | Windows e macchine virtuali Linux | Blob o tabella di archiviazione:<br>Diagnostica di Azure per Linux o Windows <br>Hub eventi:<br>Diagnostica di Azure per Windows |
| Metriche di archiviazione | Metrica | Archiviazione di Azure | Tabella di archiviazione:<br>di Analisi archiviazione |
| Log attività | Eventi | Tutti i servizi di Azure | API REST:<br>API Event di monitoraggio di Azure |
| Calcolo di log del sistema operativo guest (ad esempio, IIS, ETW, Syslog) | Eventi | Windows e macchine virtuali Linux | Blob o tabella di archiviazione:<br>Diagnostica di Azure per Linux o Windows <br>Hub eventi:<br>Diagnostica di Azure per Windows |
| Log di archiviazione | Eventi | Archiviazione di Azure | Tabella di archiviazione:<br>di Analisi archiviazione |

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulle [il monitoraggio di Azure in Azure Stack](azure-stack-metrics-azure-data.md).
