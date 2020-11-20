---
title: Introduzione alla visualizzazione delle regole di sicurezza effettive in Azure Network Watcher | Microsoft Docs
description: Questa pagina offre una panoramica della funzionalità di visualizzazione delle regole di sicurezza Network Watcher-valide
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/26/2017
ms.author: damendo
ms.openlocfilehash: d172ba7353b7ac4c490584cd7c84871c6244f214
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94961822"
---
# <a name="introduction-to-effective-security-rules-view-in-azure-network-watcher"></a>Introduzione alla visualizzazione delle regole di sicurezza effettive in Azure Network Watcher

I gruppi di sicurezza di rete sono associati a un livello di subnet o a un livello di scheda di interfaccia di rete. Se associato a livello di subnet, si applica a tutte le istanze delle VM della subnet. La vista regole di sicurezza effettive restituisce tutte le gruppi e le regole configurate associate a una scheda di interfaccia di rete e a un livello di subnet per una macchina virtuale che fornisce informazioni dettagliate sulla configurazione. Vengono anche restituite le regole di sicurezza effettive per ogni scheda di interfaccia di rete in una VM. Con la visualizzazione delle regole di sicurezza effettive, è possibile valutare una VM per le vulnerabilità di rete, ad esempio le porte aperte. È anche possibile verificare se il gruppo di sicurezza di rete funziona come previsto [confrontando le regole di sicurezza configurate e quelle approvate](network-watcher-nsg-auditing-powershell.md).

Un caso d'uso più esteso include la conformità alla sicurezza e il controllo della sicurezza. È possibile definire un set prescrittivo di regole di sicurezza come modello per la governance della sicurezza nell'organizzazione. Un controllo della conformità periodico può essere implementato in modo programmatico confrontando le regole prescrittive con le regole effettive per ogni VM della rete.

Le regole del portale vengono visualizzate per ogni interfaccia di rete e raggruppate per confronto in ingresso e in uscita. Si ottiene così una semplice visualizzazione delle regole applicate a una macchina virtuale. È disponibile un pulsante per il download per scaricare facilmente tutte le regole di sicurezza indipendentemente dalla scheda in un file CSV.

![Visualizzazione di un gruppo di sicurezza][1]

Le regole possono essere selezionate e si apre un nuovo pannello che visualizza il gruppo di sicurezza di rete e i prefissi di origine e di destinazione. Da questo pannello è possibile passare direttamente alla risorsa del gruppo di sicurezza di rete.

![Drill-down][2]

### <a name="next-steps"></a>Passaggi successivi

È anche possibile usare la funzionalità *gruppi di sicurezza effettivi* tramite altri metodi elencati di seguito:
* [REST API](/rest/api/virtualnetwork/NetworkInterfaces/ListEffectiveNetworkSecurityGroups)
* [PowerShell](/powershell/module/az.network/get-azeffectivenetworksecuritygroup?view=azps-4.4.0)
* [Interfaccia della riga di comando di Azure](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-list-effective-nsg)

Per informazioni su come controllare le impostazioni del gruppo di sicurezza di rete, vedere [Audit Network Security Group settings with PowerShell](network-watcher-nsg-auditing-powershell.md) (Controllare le impostazioni di un gruppo di sicurezza di rete con PowerShell)

[1]: ./media/network-watcher-security-group-view-overview/securitygroupview.png
[2]: ./media/network-watcher-security-group-view-overview/figure1.png