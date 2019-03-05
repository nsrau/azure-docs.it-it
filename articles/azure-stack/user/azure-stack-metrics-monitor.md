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
ms.date: 03/04/2018
ms.author: mabrigg
ms.lastreviewed: 12/01/2018
ms.openlocfilehash: e2144f293c446a311a9c903ff620c8ef1d1d4efc
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57341443"
---
# <a name="how-to-consume-monitoring-data-from-azure-stack"></a>Come usare i dati di monitoraggio di Azure Stack

*Si applica a: Sistemi integrati di Azure Stack*

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
