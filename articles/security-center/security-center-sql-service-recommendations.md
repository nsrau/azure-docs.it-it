---
title: Protezione del servizio SQL di Azure e dei dati nel Centro sicurezza di Azure | Documentazione Microsoft
description: "Questo documento illustra le raccomandazioni presenti nel Centro sicurezza di Azure che facilitano la protezione dei dati e del servizio SQL di Azure e garantiscano la conformità ai criteri di sicurezza."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: bcae6987-05d0-4208-bca8-6a6ce7c9a1e3
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/03/2017
ms.author: terrylan
ms.translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 0c3a11e9a86767641533b16de1b96b4c59bfdf51
ms.contentlocale: it-it
ms.lasthandoff: 04/03/2017

---
# <a name="protecting-azure-sql-service-and-data-in-azure-security-center"></a>Protezione del servizio SQL di Azure e dei dati nel Centro sicurezza di Azure
Il Centro sicurezza di Azure analizza lo stato di sicurezza delle risorse di Azure. Quando il Centro sicurezza identifica potenziali vulnerabilità della sicurezza, crea raccomandazioni utili per definire il processo di configurazione dei controlli necessari.  Le raccomandazioni sono applicabili a diversi tipi di risorse di Azure, ovvero macchine virtuali, risorse di rete, SQL, dati e applicazioni.

Questo articolo illustra le raccomandazioni applicabili al servizio SQL di Azure e ai dati e relative all'abilitazione del controllo per i database SQL Azure e della crittografia per i database SQL e dell'account di archiviazione di Azure.  Usare la tabella seguente come riferimento per conoscere le raccomandazioni disponibili per il servizio SQL e per i dati e i relativi effetti se vengono applicate.

## <a name="available-sql-service-and-data-recommendations"></a>Raccomandazioni disponibili per il servizio SQL e i dati
| Raccomandazione | Descrizione |
| --- | --- |
| [Abilitare il controllo e il rilevamento delle minacce nei server SQL](security-center-enable-auditing-on-sql-servers.md) |Consiglia di attivare il controllo e il rilevamento delle minacce per i server di Azure SQL (solo il servizio Azure SQL, non è incluso SQL in esecuzione nelle macchine virtuali). |
| [Abilitare il controllo e il rilevamento delle minacce nei database SQL](security-center-enable-auditing-on-sql-databases.md) |Consiglia di attivare il controllo e il rilevamento delle minacce per i database SQL di Azure (solo il servizio Azure SQL, non è incluso SQL in esecuzione nelle macchine virtuali). |
| [Abilitare Transparent Data Encryption sui database SQL](security-center-enable-transparent-data-encryption.md) |Consiglia di abilitare la crittografia per i database SQL (solo il servizio di SQL Azure). |

## <a name="see-also"></a>Vedere anche
Per altre informazioni sulle raccomandazioni applicabili ad altri tipi di risorse di Azure, vedere gli argomenti seguenti:

* [Protezione delle macchine virtuali nel Centro sicurezza di Azure](security-center-virtual-machine-recommendations.md)
* [Protecting your applications in Azure Security Center](security-center-application-recommendations.md)
* [Protezione della rete nel Centro sicurezza di Azure](security-center-network-recommendations.md)

Per altre informazioni sul Centro sicurezza, vedere gli argomenti seguenti:

* [Impostazione dei criteri di sicurezza nel Centro sicurezza di Azure](security-center-policies.md) : informazioni su come configurare i criteri di sicurezza per le sottoscrizioni e i gruppi di risorse di Azure.
* [Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure](security-center-managing-and-responding-alerts.md) : informazioni su come gestire e rispondere agli avvisi di sicurezza.
* [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md) : domande frequenti sull'uso del servizio.

