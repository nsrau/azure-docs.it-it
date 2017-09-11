---
title: Introduzione alla visualizzazione dei gruppi di sicurezza in Azure Network Watcher | Microsoft Docs
description: "Questa pagina fornisce una panoramica della funzionalità di visualizzazione della sicurezza di Network Watcher"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: ad27ab85-9d84-4759-b2b9-e861ef8ea8d8
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/26/2017
ms.author: jdial
ms.translationtype: Human Translation
ms.sourcegitcommit: a3ca1527eee068e952f81f6629d7160803b3f45a
ms.openlocfilehash: 2c581a2d152a6d3f16de8f249e27a426aa9f844f
ms.contentlocale: it-it
ms.lasthandoff: 04/27/2017

---

# <a name="introduction-to-network-security-group-view-in-azure-network-watcher"></a>Introduzione alla visualizzazione dei gruppi di sicurezza di rete in Azure Network Watcher

I gruppi di sicurezza di rete sono associati a un livello di subnet o a un livello di scheda di interfaccia di rete. Se associato a livello di subnet, si applica a tutte le istanze delle VM della subnet. La visualizzazione dei gruppi di sicurezza di rete restituisce tutti i gruppi di sicurezza di rete configurati e le regole associate a livello di scheda di interfaccia di rete e di subnet per una macchina virtuale, offrendo così informazioni approfondite sulla configurazione. Vengono anche restituite le regole di sicurezza effettive per ogni scheda di interfaccia di rete in una VM. Usando la visualizzazione dei gruppi di sicurezza di rete, è possibile valutare le vulnerabilità di rete di una VM, ad esempio le porte aperte. È anche possibile verificare se il gruppo di sicurezza di rete funziona come previsto [confrontando le regole di sicurezza configurate e quelle effettive](network-watcher-nsg-auditing-powershell.md).

Un caso d'uso più esteso include la conformità alla sicurezza e il controllo della sicurezza. È possibile definire un set prescrittivo di regole di sicurezza come modello per la governance della sicurezza nell'organizzazione. Un controllo della conformità periodico può essere implementato in modo programmatico confrontando le regole prescrittive con le regole effettive per ogni VM della rete.

Nel portale le regolo sono divise in Valide, Subnet, Interfaccia di rete e Predefinite. Si ottiene così una semplice visualizzazione delle regole applicate a una macchina virtuale. È disponibile un pulsante per il download per scaricare facilmente tutte le regole di sicurezza indipendentemente dalla scheda in un file CSV.

![Visualizzazione di un gruppo di sicurezza][1]

Le regole possono essere selezionate e si apre un nuovo pannello che visualizza il gruppo di sicurezza di rete e i prefissi di origine e di destinazione. Da questo pannello è possibile passare direttamente alla risorsa del gruppo di sicurezza di rete.

![Drill-down][2]

### <a name="next-steps"></a>Passaggi successivi

Per informazioni su come controllare le impostazioni del gruppo di sicurezza di rete, vedere [Audit Network Security Group settings with PowerShell](network-watcher-nsg-auditing-powershell.md) (Controllare le impostazioni di un gruppo di sicurezza di rete con PowerShell)

[1]: ./media/network-watcher-security-group-view-overview/securitygroupview.png
[2]: ./media/network-watcher-security-group-view-overview/figure1.png










