---
title: Protezione del servizio SQL di Azure nel Centro sicurezza di Azure | Microsoft Docs
description: Questo documento illustra le raccomandazioni presenti nel Centro sicurezza di Azure che facilitano la protezione del servizio SQL di Azure e garantiscano la conformità ai criteri di sicurezza.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''

ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/04/2016
ms.author: terrylan

---
# Protezione del servizio SQL di Azure nel Centro sicurezza di Azure
Il Centro sicurezza di Azure analizza lo stato di sicurezza delle risorse di Azure. Quando il Centro sicurezza identifica potenziali vulnerabilità della sicurezza, crea raccomandazioni utili per definire il processo di configurazione dei controlli necessari. Le raccomandazioni sono applicabili ai tipi di risorse di Azure, ovvero macchine virtuali, risorse di rete, SQL e applicazioni.

Questo articolo illustra le raccomandazioni applicabili al servizio SQL di Azure. Le raccomandazioni sul servizio SQL di Azure sono incentrate sull'abilitazione dei controlli per i server e i database SQL di Azure e sull'abilitazione della crittografia per i database SQL. Usare la tabella seguente come riferimento per conoscere le raccomandazioni disponibili per il servizio SQL e gli effetti che producono se si decide di metterle in pratica.

## Consigli disponibili sul servizio SQL
| Raccomandazione | Descrizione |
| --- | --- |
| [Abilitare il servizio di controllo SQL per i server](security-center-enable-auditing-on-sql-servers.md) |Suggerisce di attivare il controllo per i server SQL di Azure. Solo il servizio SQL di Azure, non SQL in esecuzione nelle macchine virtuali. |
| [Abilitare il servizio di controllo SQL per i database](security-center-enable-auditing-on-sql-databases.md) |Suggerisce di attivare il controllo per i database SQL di Azure. Solo il servizio SQL di Azure, non SQL in esecuzione nelle macchine virtuali. |
| [Abilitare Transparent Data Encryption sui database SQL](security-center-enable-transparent-data-encryption.md) |Consiglia di abilitare la crittografia per i database SQL (solo il servizio di SQL Azure). |

## Vedere anche
Per altre informazioni sulle raccomandazioni applicabili ad altri tipi di risorse di Azure, vedere gli argomenti seguenti:

* [Protezione delle macchine virtuali nel Centro sicurezza di Azure](security-center-virtual-machine-recommendations.md)
* [Protecting your applications in Azure Security Center](security-center-application-recommendations.md) (Protezione delle applicazioni nel Centro sicurezza di Azure)
* [Protezione della rete nel Centro sicurezza di Azure](security-center-network-recommendations.md)

Per altre informazioni sul Centro sicurezza, vedere gli argomenti seguenti:

* [Impostazione dei criteri di sicurezza nel Centro sicurezza di Azure](security-center-policies.md): informazioni su come configurare i criteri di sicurezza per le sottoscrizioni e i gruppi di risorse di Azure.
* [Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure](security-center-managing-and-responding-alerts.md): informazioni su come gestire e rispondere agli avvisi di sicurezza.
* [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md): domande frequenti sull'uso del servizio.

<!---HONumber=AcomDC_0810_2016-->