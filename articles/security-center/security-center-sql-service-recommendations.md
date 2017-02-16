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
ms.date: 12/20/2016
ms.author: terrylan
translationtype: Human Translation
ms.sourcegitcommit: 7b5df25f46a2824acb361401ab90c960b8e5978f
ms.openlocfilehash: 8ce47dd649d1d945df506ed65c871cf3afe2f004


---
# <a name="protecting-azure-sql-service-and-data-in-azure-security-center"></a>Protezione del servizio SQL di Azure e dei dati nel Centro sicurezza di Azure
Il Centro sicurezza di Azure analizza lo stato di sicurezza delle risorse di Azure. Quando il Centro sicurezza identifica potenziali vulnerabilità della sicurezza, crea raccomandazioni utili per definire il processo di configurazione dei controlli necessari.  Le raccomandazioni sono applicabili a diversi tipi di risorse di Azure, ovvero macchine virtuali, risorse di rete, SQL, dati e applicazioni.

Questo articolo illustra le raccomandazioni applicabili al servizio SQL di Azure e ai dati e relative all'abilitazione del controllo per i database SQL Azure e della crittografia per i database SQL e dell'account di archiviazione di Azure.  Usare la tabella seguente come riferimento per conoscere le raccomandazioni disponibili per il servizio SQL e per i dati e i relativi effetti se vengono applicate.

## <a name="available-sql-service-and-data-recommendations"></a>Raccomandazioni disponibili per il servizio SQL e i dati
| Raccomandazione | Descrizione |
| --- | --- |
| [Abilitare il servizio di controllo SQL per i server](security-center-enable-auditing-on-sql-servers.md) |Suggerisce di attivare il controllo per i server SQL di Azure. Solo il servizio SQL di Azure, non SQL in esecuzione nelle macchine virtuali. |
| [Abilitare il servizio di controllo SQL per i database](security-center-enable-auditing-on-sql-databases.md) |Suggerisce di attivare il controllo per i database SQL di Azure. Solo il servizio SQL di Azure, non SQL in esecuzione nelle macchine virtuali. |
| [Abilitare Transparent Data Encryption sui database SQL](security-center-enable-transparent-data-encryption.md) |Consiglia di abilitare la crittografia per i database SQL (solo il servizio di SQL Azure). |
| [Abilitare la crittografia per l'account di archiviazione di Azure](security-center-enable-encryption-for-storage-account.md) | Consiglia di abilitare la crittografia del servizio Archiviazione di Azure per i dati inattivi che applica la crittografia ai dati quando vengono scritti nell'archiviazione di Azure e li decrittografa prima del recupero. La crittografia del servizio Archiviazione di Azure è attualmente disponibile solo per il servizio BLOB di Azure e può essere usata per BLOB in blocchi, BLOB di pagine e BLOB di aggiunta. Per altre informazioni, vedere [Crittografia del servizio di archiviazione di Azure per dati inattivi](../storage/storage-service-encryption.md).</br>La crittografia del servizio Archiviazione di Azure è supportata solo negli account di archiviazione di Resource Manager. Gli account di archiviazione classici non sono attualmente supportati. Per informazioni sui modelli di distribuzione classico e di Resource Manager, vedere [Azure deployment models](../azure-classic-rm.md) (Modelli di distribuzione di Azure). |

## <a name="see-also"></a>Vedere anche
Per altre informazioni sulle raccomandazioni applicabili ad altri tipi di risorse di Azure, vedere gli argomenti seguenti:

* [Protezione delle macchine virtuali nel Centro sicurezza di Azure](security-center-virtual-machine-recommendations.md)
* [Protecting your applications in Azure Security Center](security-center-application-recommendations.md)
* [Protezione della rete nel Centro sicurezza di Azure](security-center-network-recommendations.md)

Per altre informazioni sul Centro sicurezza, vedere gli argomenti seguenti:

* [Impostazione dei criteri di sicurezza nel Centro sicurezza di Azure](security-center-policies.md) : informazioni su come configurare i criteri di sicurezza per le sottoscrizioni e i gruppi di risorse di Azure.
* [Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure](security-center-managing-and-responding-alerts.md) : informazioni su come gestire e rispondere agli avvisi di sicurezza.
* [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md) : domande frequenti sull'uso del servizio.



<!--HONumber=Jan17_HO1-->


