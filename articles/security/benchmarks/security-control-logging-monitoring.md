---
title: 'Controllo della sicurezza di Azure: registrazione e monitoraggio'
description: Registrazione e monitoraggio del controllo di sicurezza di Azure
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 8492e986b378ea19f8c3c4cb7f94fcb0f155b427
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96487848"
---
# <a name="security-control-logging-and-monitoring"></a>Controllo di sicurezza: registrazione e monitoraggio

La registrazione e il monitoraggio della sicurezza sono incentrati sulle attività correlate all'abilitazione, all'acquisizione e all'archiviazione dei log di controllo per i servizi di Azure.

## <a name="21-use-approved-time-synchronization-sources"></a>2.1: usare origini di sincronizzazione ora approvate

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 2.1 | 6.1 | Microsoft |

Microsoft mantiene le origini temporali per le risorse di Azure, tuttavia, è possibile gestire le impostazioni di sincronizzazione dell'ora per le risorse di calcolo.

- [Come configurare la sincronizzazione dell'ora per le risorse di calcolo Windows di Azure](../../virtual-machines/windows/time-sync.md)

- [Come configurare la sincronizzazione dell'ora per le risorse di calcolo Linux di Azure](../../virtual-machines/linux/time-sync.md)

## <a name="22-configure-central-security-log-management"></a>2.2: configurare la gestione dei log di sicurezza centralizzata

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 2.2 | 6,5, 6,6 | Customer |

Inserire i log tramite monitoraggio di Azure per aggregare i dati di sicurezza generati dai dispositivi endpoint, le risorse di rete e altri sistemi di sicurezza. In Monitoraggio di Azure usare una o più aree di lavoro Log Analytics per eseguire query ed effettuare analisi, quindi usare gli account di archiviazione di Azure per l'archiviazione/memorizzazione a lungo termine.

In alternativa, è possibile abilitare ed eseguire l'onboarding dei dati in Azure Sentinel o in un SIEM di terze parti. 

- [Come eseguire l'onboarding di Azure Sentinel](../../sentinel/quickstart-onboard.md)

- [Come raccogliere i log e le metriche della piattaforma con monitoraggio di Azure](../../azure-monitor/platform/diagnostic-settings.md)

- [Come raccogliere i log degli host interni della macchina virtuale di Azure con monitoraggio di Azure](../../azure-monitor/learn/quick-collect-azurevm.md)

- [Come iniziare a usare Monitoraggio di Azure e l'integrazione SIEM di terze parti](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

## <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: abilitare la registrazione di controllo per le risorse di Azure

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 2.3 | 6,2, 6,3 | Customer |

Abilitare le impostazioni di diagnostica nelle risorse di Azure per l'accesso ai log di controllo, sicurezza e diagnostica. I log attività, che sono automaticamente disponibili, includono origine evento, data, utente, timestamp, indirizzi di origine, indirizzi di destinazione e altri elementi utili.

- [Come raccogliere i log e le metriche della piattaforma con monitoraggio di Azure](../../azure-monitor/platform/diagnostic-settings.md)

- [Informazioni sulla registrazione e sui diversi tipi di log in Azure](../../azure-monitor/platform/platform-logs-overview.md)

## <a name="24-collect-security-logs-from-operating-systems"></a>2.4: raccogliere i log di sicurezza dai sistemi operativi

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 2.4 | 6,2, 6,3 | Customer |

Se la risorsa di calcolo è di proprietà di Microsoft, Microsoft è responsabile del monitoraggio. Se la risorsa di calcolo è di proprietà dell'organizzazione, è responsabilità dell'utente monitorarla. È possibile usare il Centro sicurezza di Azure per monitorare il sistema operativo. I dati raccolti dal centro sicurezza dal sistema operativo includono il tipo di sistema operativo e la versione, il sistema operativo (registri eventi di Windows), i processi in esecuzione, il nome del computer, gli indirizzi IP e l'utente connesso. L'agente di Log Analytics raccoglie anche i file di dump di arresto anomalo del sistema.

- [Come raccogliere i log degli host interni della macchina virtuale di Azure con monitoraggio di Azure](../../azure-monitor/learn/quick-collect-azurevm.md)

- [Informazioni sulla raccolta dati del Centro sicurezza di Azure](../../security-center/security-center-enable-data-collection.md)

## <a name="25-configure-security-log-storage-retention"></a>2.5: configurare la conservazione dell'archiviazione dei log di sicurezza

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 2.5 | 6.4 | Customer |

In monitoraggio di Azure impostare il periodo di conservazione dell'area di lavoro Log Analytics in base alle normative di conformità dell'organizzazione. Usare gli account di archiviazione di Azure per l'archiviazione/memorizzazione a lungo termine.

- [Modificare il periodo di conservazione dei dati in Log Analytics](../../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

- [Come configurare i criteri di conservazione per i log dell'account di archiviazione di Azure](../../storage/common/storage-monitor-storage-account.md#configure-logging)

## <a name="26-monitor-and-review-logs"></a>2.6: monitorare ed esaminare i log

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 2.6 | 6.7 | Customer |

Analizza e monitora i log per un comportamento anomalo e verifica regolarmente i risultati. Usare l'area di lavoro Log Analytics di monitoraggio di Azure per esaminare i log ed eseguire query sui dati di log.

In alternativa, è possibile abilitare ed eseguire l'onboarding dei dati in Azure Sentinel o in un SIEM di terze parti. 

- [Come eseguire l'onboarding di Azure Sentinel](../../sentinel/quickstart-onboard.md)

- [Informazioni sull'area di lavoro Log Analytics](../../azure-monitor/log-query/log-analytics-tutorial.md)

- [Come eseguire query personalizzate in Monitoraggio di Azure](../../azure-monitor/log-query/get-started-queries.md)

## <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: abilitare gli avvisi per le attività anomale

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 2.7 | 6.8 | Customer |

Usare il Centro sicurezza di Azure con Log Analytics area di lavoro per il monitoraggio e l'invio di avvisi sulle attività anomale riscontrate in registri di sicurezza ed eventi.

In alternativa, è possibile abilitare e caricare i dati in Sentinel di Azure.

- [Come eseguire l'onboarding di Azure Sentinel](../../sentinel/quickstart-onboard.md)

- [Come gestire gli avvisi nel centro sicurezza di Azure](../../security-center/security-center-managing-and-responding-alerts.md)

- [Come inviare un avviso sui dati del log di log Analytics](../../azure-monitor/learn/tutorial-response.md)

## <a name="28-centralize-anti-malware-logging"></a>2.8: centralizzare la registrazione antimalware

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 2.8 | 8.6 | Customer |

Abilitare la raccolta di eventi antimalware per le macchine virtuali e i servizi cloud di Azure.

- [Come configurare Microsoft antimalware per le macchine virtuali](/powershell/module/servicemanagement/azure.service/set-azurevmmicrosoftantimalwareextension?view=azuresmps-4.0.0)

- [Come configurare Microsoft antimalware per i servizi cloud](/powershell/module/servicemanagement/azure.service/set-azureserviceantimalwareextension?view=azuresmps-4.0.0)

- [Informazioni su Microsoft antimalware](../fundamentals/antimalware.md)

## <a name="29-enable-dns-query-logging"></a>2.9: abilitare la registrazione delle query DNS

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 2,9 | 8.7 | Customer |

Implementare una soluzione di terze parti da Azure Marketplace per la soluzione di registrazione DNS in base alle esigenze dell'organizzazione.  

## <a name="210-enable-command-line-audit-logging"></a>2.10: abilitare la registrazione di controllo da riga di comando

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 2.10 | 8.8 | Customer |

Usare Microsoft Monitoring Agent in tutte le macchine virtuali Windows di Azure supportate per registrare l'evento di creazione del processo e il campo CommandLine.   Per le macchine virtuali Linux di Azure supportate, è possibile configurare manualmente la registrazione della console in base ai singoli nodi e usare syslog per archiviare i dati.  Usare anche l'area di lavoro Log Analytics di monitoraggio di Azure per esaminare i log ed eseguire query sui dati registrati dalle macchine virtuali di Azure. 

- [Raccolta dati nel Centro sicurezza di Azure](../../security-center/security-center-enable-data-collection.md#data-collection-tier)

- [Come eseguire query personalizzate in Monitoraggio di Azure](../../azure-monitor/log-query/get-started-queries.md)

- [Origini dati Syslog in Monitoraggio di Azure](../../azure-monitor/platform/data-sources-syslog.md)


## <a name="next-steps"></a>Passaggi successivi

- Vedere il controllo di sicurezza successivo: [identità e controllo di accesso](security-control-identity-access-control.md)