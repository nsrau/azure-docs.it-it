---
title: Azure Security Control - Logging and Monitoring
description: Registrazione e monitoraggio del controllo di sicurezza di AzureAzure Security Control Logging and Monitoring
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: e58df0997c62a131a3c9987369f8e076a62d6654
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81408335"
---
# <a name="security-control-logging-and-monitoring"></a>Controllo di sicurezza: registrazione e monitoraggio

La registrazione e il monitoraggio della sicurezza sono incentrati sulle attività correlate all'abilitazione, acquisizione e archiviazione dei log di controllo per i servizi di Azure.Security logging and monitoring focuses on activities related to enabling, acquiring, and storing audit logs for Azure services.

## <a name="21-use-approved-time-synchronization-sources"></a>2.1: Utilizzare origini di sincronizzazione dell'ora approvate

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 2.1 | 6.1 | Microsoft |

Microsoft gestisce le origini ora per le risorse di Azure, tuttavia, è possibile gestire le impostazioni di sincronizzazione dell'ora per le risorse di calcolo.

- [Come configurare la sincronizzazione dell'ora per le risorse di calcolo di Windows di AzureHow to configure time synchronization for Azure Windows compute resources](https://docs.microsoft.com/azure/virtual-machines/windows/time-sync)

- [Come configurare la sincronizzazione dell'ora per le risorse di calcolo di Azure LinuxHow to configure time synchronization for Azure Linux compute resources](https://docs.microsoft.com/azure/virtual-machines/linux/time-sync)

## <a name="22-configure-central-security-log-management"></a>2.2: Configurare la gestione dei registri di protezione centralizzati

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 2.2 | 6.5, 6.6 | Customer |

Eseguire l'inserimento dei log tramite Monitoraggio di Azure per aggregare i dati di sicurezza generati da dispositivi endpoint, risorse di rete e altri sistemi di sicurezza. In Monitoraggio di Azure usare Log Analytics Workspace(s) per eseguire query ed eseguire analisi e gli account di archiviazione di Azure per l'archiviazione a lungo termine/archiviazione.

In alternativa, è possibile abilitare e on-board i dati di Azure Sentinel o un SIEM di terze parti. 

- [Come eseguire l'onboarding di Azure SentinelHow to onboard Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

- [Come raccogliere i log e le metriche della piattaforma con Monitoraggio di AzureHow to collect platform logs and metrics with Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings)

- [Come raccogliere i log host interni della macchina virtuale di Azure con Monitoraggio di AzureHow to collect Azure Virtual Machine internal host logs with Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/learn/quick-collect-azurevm)

- [Come iniziare a usare Monitoraggio di Azure e l'integrazione SIEM di terze parti](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

## <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Abilitare la registrazione di controllo per le risorse di Azure2.3: Abilitare la registrazione di controllo per le risorse di Azure2.

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 2.3 | 6.2, 6.3 | Customer |

Abilitare le impostazioni di diagnostica nelle risorse di Azure per l'accesso ai log di controllo, sicurezza e diagnostica. I log attività, che sono automaticamente disponibili, includono l'origine dell'evento, la data, l'utente, il timestamp, gli indirizzi di origine, gli indirizzi di destinazione e altri elementi utili.

- [Come raccogliere i log e le metriche della piattaforma con Monitoraggio di AzureHow to collect platform logs and metrics with Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings)

- [Informazioni sulla registrazione e tipi di log diversi in AzureUnderstand logging and different log types in Azure](https://docs.microsoft.com/azure/azure-monitor/platform/platform-logs-overview)

## <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Raccogliere i registri di sicurezza dai sistemi operativi

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 2.4 | 6.2, 6.3 | Customer |

Se la risorsa di calcolo è di proprietà di Microsoft, Microsoft è responsabile del monitoraggio. Se la risorsa di calcolo è di proprietà dell'organizzazione, è responsabilità dell'utente monitorarla. È possibile usare il Centro sicurezza di Azure per monitorare il sistema operativo. I dati raccolti dal Centro sicurezza dal sistema operativo includono il tipo e la versione del sistema operativo, i sistemi operativi (Registri eventi di Windows), i processi in esecuzione, il nome del computer, gli indirizzi IP e l'utente connesso. Log Analytics Agent raccoglie anche i file di dump di arresto anomalo del sistema.

- [Come raccogliere i log host interni della macchina virtuale di Azure con Monitoraggio di AzureHow to collect Azure Virtual Machine internal host logs with Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/learn/quick-collect-azurevm)

- [Informazioni sulla raccolta dei dati del Centro sicurezza di AzureUnderstand Azure Security Center data collection](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection)

## <a name="25-configure-security-log-storage-retention"></a>2.5: Configurare la conservazione dello spazio di archiviazione del registro di protezione

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 2.5 | 6.4 | Customer |

In Monitoraggio di Azure impostare il periodo di conservazione dell'area di lavoro di Log Analytics in base alle normative di conformità dell'organizzazione. Usare gli account di archiviazione di Azure per l'archiviazione a lungo termine/archiviazione.

- [Modificare il periodo di conservazione dei dati in Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

- [Come configurare i criteri di conservazione per i log dell'account di Archiviazione di AzureHow to configure retention policy for Azure Storage account logs](https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account#configure-logging)

## <a name="26-monitor-and-review-logs"></a>2.6: Monitorare ed esaminare i registri

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 2.6 | 6.7 | Customer |

Analizzare e monitorare i registri per verificare il comportamento anomalo ed esaminare regolarmente i risultati. Usare l'area di lavoro Log Analytics di Monitoraggio di Azure per esaminare i log ed eseguire query sui dati di log.

In alternativa, è possibile abilitare e on-board i dati di Azure Sentinel o di terze parti SIEM. 

- [Come eseguire l'onboarding di Azure SentinelHow to onboard Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

- [Informazioni sull'area di lavoro di Log AnalyticsUnderstand Log Analytics Workspace](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)

- [Come eseguire query personalizzate in Monitoraggio di AzureHow to perform custom queries in Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries)

## <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Abilitare gli avvisi per le attività anomale

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 2.7 | 6.8 | Customer |

Usare il Centro sicurezza di Azure con log Analytics Workspace per il monitoraggio e gli avvisi sulle attività anomale rilevate nei log e negli eventi di sicurezza.

In alternativa, è possibile abilitare e on-board i dati in Azure Sentinel.Alternatively, you may enable and on-board data to Azure Sentinel.

- [Come eseguire l'onboarding di Azure SentinelHow to onboard Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

- [Come gestire gli avvisi nel Centro sicurezza di AzureHow to manage alerts in Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)

- [Come avvisare i dati del log di log di log](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response)

## <a name="28-centralize-anti-malware-logging"></a>2.8: Centralizzare la registrazione antimalware

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 2.8 | 8.6 | Customer |

Abilitare la raccolta di eventi antimalware per Macchine virtuali di Azure e Servizi cloud.

- [Come configurare Microsoft Antimalware per le macchine virtuali](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azurevmmicrosoftantimalwareextension?view=azuresmps-4.0.0)

- [Come configurare Microsoft Antimalware per i servizi cloud](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azureserviceantimalwareextension?view=azuresmps-4.0.0)

- [Informazioni su Microsoft Antimalware](https://docs.microsoft.com/azure/security/fundamentals/antimalware)

## <a name="29-enable-dns-query-logging"></a>2.9: Abilitare la registrazione delle query DNS

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 2,9 | 8.7 | Customer |

Implementare una soluzione di terze parti da Azure Marketplace per la soluzione di registrazione DNS in base alle esigenze dell'organizzazione.  

## <a name="210-enable-command-line-audit-logging"></a>2.10: Abilitare la registrazione di controllo della riga di comando

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 2,10 | 8.8 | Customer |

Usare Microsoft Monitoring Agent in tutte le macchine virtuali Windows di Azure supportate per registrare l'evento di creazione del processo e il campo Riga di comando.   Per le macchine virtuali Linux di Azure supportate, è possibile configurare manualmente la registrazione della console in base al nodo e usare Syslog per archiviare i dati.  Usare inoltre l'area di lavoro Log Analytics di Monitoraggio di Azure per esaminare i log ed eseguire query sui dati registrati dalle macchine virtuali di Azure.Also, use Azure Monitor's Log Analytics workspace to review logs and perform queries on logged data from Azure Virtual machines. 

- [Raccolta dati nel Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection#data-collection-tier)

- [Come eseguire query personalizzate in Monitoraggio di AzureHow to perform custom queries in Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries)

- [Origini dati Syslog in Monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-syslog)


## <a name="next-steps"></a>Passaggi successivi

- Vedere il prossimo controllo di sicurezza: [controllo di identità e controllo di accessoSee](security-control-identity-access-control.md) the next Security Control: Identity and Access Control