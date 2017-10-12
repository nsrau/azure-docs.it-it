---
title: Protezione della rete nel Centro sicurezza di Azure | Documentazione Microsoft
description: "Questo documento illustra le raccomandazioni presenti nel Centro sicurezza di Azure che facilitano la protezione della rete di Azure e garantiscano la conformità ai criteri di sicurezza."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 96c55a02-afd6-478b-9c1f-039528f3dea0
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/16/2016
ms.author: terrylan
ms.openlocfilehash: 00b715507a7c3a4d784b800e7bf0c700f6ea6ff1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="protecting-your-network-in-azure-security-center"></a>Protezione della rete nel Centro sicurezza di Azure
Il Centro sicurezza di Azure analizza lo stato di sicurezza delle risorse di Azure. Quando il Centro sicurezza identifica potenziali vulnerabilità della sicurezza, crea raccomandazioni utili per definire il processo di configurazione dei controlli necessari.  Le raccomandazioni sono applicabili ai tipi di risorse di Azure, ovvero macchine virtuali, risorse di rete, SQL e applicazioni.

Questo articolo illustra le raccomandazioni applicabili alla rete.  Le raccomandazioni per le risorse di rete sono incentrate sui firewall di nuova generazione, sui gruppi di sicurezza di rete, sulla configurazione delle regole di traffico in ingresso e altro ancora.  Usare la tabella seguente come riferimento per conoscere le raccomandazioni disponibili per la rete e gli effetti che producono se si decide di metterle in pratica.

## <a name="available-network-recommendations"></a>Indicazioni disponibili per la rete
| Raccomandazione | Descrizione |
| --- | --- |
| [Aggiungi un firewall di nuova generazione](security-center-add-next-generation-firewall.md) |Il Centro sicurezza di Azure consiglia di aggiungere un firewall di nuova generazione di un partner Microsoft per aumentare i meccanismi di protezione per la sicurezza. |
| [Indirizza il traffico solo tramite il firewall di nuova generazione](security-center-add-next-generation-firewall.md#route-traffic-through-ngfw-only) |Consiglia di configurare le regole del gruppo di sicurezza di rete che forzano il traffico in ingresso alla VM tramite il firewall di nuova generazione. |
| [Abilita i gruppi di sicurezza di rete sulle subnet o sulle macchine virtuali](security-center-enable-network-security-groups.md) |Consiglia di attivare i gruppo di sicurezza di rete sulle subnet o sulle macchine virtuali. |
| [Limita l'accesso tramite un endpoint con connessione Internet](security-center-restrict-access-through-internet-facing-endpoints.md) |Consiglia di configurare le regole del traffico in ingresso per i gruppi di sicurezza di rete. |

## <a name="see-also"></a>Vedere anche
Per altre informazioni sulle raccomandazioni applicabili ad altri tipi di risorse di Azure, vedere gli argomenti seguenti:

* [Protezione delle macchine virtuali nel Centro sicurezza di Azure](security-center-virtual-machine-recommendations.md)
* [Protecting your applications in Azure Security Center](security-center-application-recommendations.md)
* [Protezione del servizio SQL di Azure nel Centro sicurezza di Azure](security-center-sql-service-recommendations.md)

Per altre informazioni sul Centro sicurezza, vedere gli argomenti seguenti:

* [Impostazione dei criteri di sicurezza nel Centro sicurezza di Azure](security-center-policies.md) : informazioni su come configurare i criteri di sicurezza per le sottoscrizioni e i gruppi di risorse di Azure.
* [Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure](security-center-managing-and-responding-alerts.md) : informazioni su come gestire e rispondere agli avvisi di sicurezza.
* [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md) : domande frequenti sull'uso del servizio.
