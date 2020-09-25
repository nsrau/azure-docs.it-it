---
title: Benchmark di sicurezza di Azure V2-registrazione e rilevamento minacce
description: Registrazione del benchmark di sicurezza di Azure V2 e rilevamento delle minacce
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/20/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 9ada1956af2dabdb8e5deff33722af7a8691dd19
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91295465"
---
# <a name="security-control-v2-logging-and-threat-detection"></a>Controllo di sicurezza V2: registrazione e rilevamento minacce

Registrazione e rilevamento delle minacce copre i controlli per il rilevamento di minacce in Azure e l'abilitazione, la raccolta e l'archiviazione dei log di controllo per i servizi di Azure. Questo include l'abilitazione dei processi di rilevamento, analisi e correzione con i controlli per generare avvisi di alta qualità con il rilevamento delle minacce nativo nei servizi di Azure. include anche la raccolta di log con monitoraggio di Azure, la centralizzazione dell'analisi della sicurezza con Sentinel di Azure, la sincronizzazione dell'ora e la conservazione dei log. 

## <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1: abilitazione del rilevamento delle minacce per le risorse di Azure

| ID Azure | Controlli CIS v 7.1 ID/i | ID NIST SP800-53 R4 |
|--|--|--|--|
| LT-1 | 6.7 | AU-3, AU-6, AU-12, SI-4 |

Assicurarsi di monitorare diversi tipi di risorse di Azure per potenziali minacce e anomalie. Concentrati su come ottenere avvisi di alta qualità per ridurre i falsi positivi per gli analisti. Gli avvisi possono essere originati da dati di log, agenti o altri dati.

Usare la funzionalità di rilevamento delle minacce incorporata nel centro sicurezza di Azure, basata sul monitoraggio della telemetria dei servizi di Azure e sull'analisi dei log dei servizi. I dati vengono raccolti utilizzando l'agente di Log Analytics, che legge varie configurazioni correlate alla sicurezza e registri eventi dal sistema e copia i dati nell'area di lavoro per l'analisi. 

Inoltre, è possibile usare Sentinel di Azure per creare regole di analisi, che cercano minacce corrispondenti a criteri specifici nell'ambiente. Le regole generano eventi imprevisti quando vengono confrontati i criteri, in modo da poter esaminare ogni evento imprevisto. Azure Sentinel può inoltre importare funzionalità di intelligence per le minacce di terze parti per migliorare la funzionalità di rilevamento delle minacce. 

- [Protezione dalle minacce nel Centro sicurezza di Azure](../../security-center/threat-protection.md)

- [Guida di riferimento agli avvisi di sicurezza del Centro sicurezza di Azure](../../security-center/alerts-reference.md)

- [Creare regole di analisi personalizzate per rilevare le minacce](../../sentinel/tutorial-detect-threats-custom.md)

- [Intelligence per le minacce informatiche con Azure Sentinel](/azure/architecture/example-scenario/data/sentinel-threat-intelligence)

**Responsabilità**: Customer

**Stakeholder** per la sicurezza dei clienti ([altre informazioni](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Sicurezza dell'infrastruttura e degli endpoint](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Operazioni per la sicurezza](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Gestione della postura](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)   

- [Sicurezza delle applicazioni e DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [Intelligence per le minacce](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: abilitare il rilevamento delle minacce per la gestione delle identità e degli accessi di Azure

| ID Azure | Controlli CIS v 7.1 ID/i | ID NIST SP800-53 R4 |
|--|--|--|--|
| LT-2 | 6.8 | AU-3, AU-6, AU-12, SI-4 |

Azure AD fornisce i log utente seguenti che possono essere visualizzati in Azure AD Reporting o integrati con monitoraggio di Azure, Azure Sentinel o altri strumenti di SIEM/monitoraggio per i casi d'uso più sofisticati di monitoraggio e analisi: 
-   Accessi: il report degli accessi fornisce informazioni sull'uso delle applicazioni gestite e sulle attività di accesso degli utenti.

-   Log di controllo: i log consentono la tracciabilità di tutte le modifiche apportate da varie funzionalità all'interno di Azure AD. I log di controllo registrano, ad esempio, le modifiche apportate a qualsiasi risorsa di Azure AD, ad esempio l'aggiunta o la rimozione di utenti, app, gruppi, ruoli e criteri.

-   Accessi a rischio. Un accesso rischioso è indicativo di un tentativo di accesso che potrebbe essere stato eseguito da qualcuno che non è il legittimo proprietario di un account utente.

-   Utenti contrassegnati per il rischio. Un utente rischioso è indicativo di un account utente che potrebbe essere stato compromesso.

Il Centro sicurezza di Azure può anche inviare avvisi su determinate attività sospette, ad esempio un numero eccessivo di tentativi di autenticazione non riusciti e gli account deprecati nella sottoscrizione. Oltre al monitoraggio dell'igiene di base della sicurezza, il modulo di protezione dalle minacce del Centro sicurezza di Azure può raccogliere anche avvisi di sicurezza più approfonditi dalle singole risorse di calcolo di Azure (ad esempio macchine virtuali, contenitori, servizio app), risorse di dati (ad esempio database SQL e archiviazione) e livelli di servizio di Azure. Questa funzionalità consente di visualizzare le anomalie degli account all'interno delle singole risorse.

- [Report delle attività di controllo in Azure AD](../../active-directory/reports-monitoring/concept-audit-logs.md)

- [Abilitare Azure Identity Protection](../../active-directory/identity-protection/overview-identity-protection.md)

- [Protezione dalle minacce nel Centro sicurezza di Azure](../../security-center/threat-protection.md)

**Responsabilità**: Customer

**Stakeholder** per la sicurezza dei clienti ([altre informazioni](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Sicurezza dell'infrastruttura e degli endpoint](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Operazioni per la sicurezza](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Gestione della postura](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)   

- [Sicurezza delle applicazioni e DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [Intelligence per le minacce](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3: abilitare la registrazione per le attività di rete di Azure

| ID Azure | Controlli CIS v 7.1 ID/i | ID NIST SP800-53 R4 |
|--|--|--|--|
| LT-3 | 9,3, 12,2, 12,5, 12,8 | AU-3, AU-6, AU-12, SI-4 |

Abilitare e raccogliere i log delle risorse del gruppo di sicurezza di rete (NSG), i log di flusso di NSG, i log del firewall di Azure e i log di Web Application Firewall (WAF) per l'analisi della sicurezza per supportare le indagini sugli eventi imprevisti, la ricerca di minacce e la generazione È possibile inviare i log di flusso a un'area di lavoro di monitoraggio di Azure Log Analytics e quindi usare Analisi del traffico per fornire informazioni dettagliate. Assicurarsi di raccogliere i log di query DNS per supportare la correlazione di altri dati di rete.

- [Come abilitare i log dei flussi dei gruppi di sicurezza di rete](../../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Log e metriche di Firewall di Azure](../../firewall/logs-and-metrics.md)

- [Come abilitare e usare Analisi del traffico](../../network-watcher/traffic-analytics.md)

- [Monitoraggio con Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md)

- [Soluzioni di monitoraggio di rete di Azure in Monitoraggio di Azure](../../azure-monitor/insights/azure-networking-analytics.md)

- [Raccogliere informazioni dettagliate sull'infrastruttura DNS con la soluzione Analisi DNS](../../azure-monitor/insights/dns-analytics.md)

**Responsabilità**: Customer

**Stakeholder** per la sicurezza dei clienti ([altre informazioni](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Sicurezza dell'infrastruttura e degli endpoint](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Operazioni per la sicurezza](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Gestione della postura](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)   

- [Sicurezza delle applicazioni e DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [Intelligence per le minacce](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: abilitare la registrazione per le risorse di Azure

| ID Azure | Controlli CIS v 7.1 ID/i | ID NIST SP800-53 R4 |
|--|--|--|--|
| LT-4 | 6,2, 6,3, 8,8 | AU-3, AU-12 |

Abilitare la registrazione per le risorse di Azure in modo da soddisfare i requisiti di conformità, rilevamento delle minacce, caccia e analisi degli eventi imprevisti. 

È possibile usare il Centro sicurezza di Azure e i criteri di Azure per abilitare i log delle risorse e la raccolta dei dati di log sulle risorse di Azure per l'accesso ai log di controllo, sicurezza e risorse. I log attività, che sono automaticamente disponibili, includono origine evento, data, utente, timestamp, indirizzi di origine, indirizzi di destinazione e altri elementi utili. 

- [Informazioni sulla registrazione e sui diversi tipi di log in Azure](../../azure-monitor/platform/platform-logs-overview.md)

- [Informazioni sulla raccolta dati del Centro sicurezza di Azure](../../security-center/security-center-enable-data-collection.md)

- [Abilitare e configurare il monitoraggio antimalware](../fundamentals/antimalware.md#enable-and-configure-antimalware-monitoring-using-powershell-cmdlets)

**Responsabilità**: Condiviso

**Stakeholder** per la sicurezza dei clienti ([altre informazioni](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Operazioni per la sicurezza](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

Sicurezza dell'infrastruttura e degli endpoint 

- [Sicurezza delle applicazioni e DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [Intelligence per le minacce](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: centralizzare la gestione e l'analisi dei log di sicurezza

| ID Azure | Controlli CIS v 7.1 ID/i | ID NIST SP800-53 R4 |
|--|--|--|--|
| LT-5 | 6,5, 6,6 | AU-3, SI-4 |

Centralizzare l'archiviazione e l'analisi di registrazione per abilitare la correlazione. Per ogni origine di log, verificare di avere assegnato un proprietario di dati, le linee guida per l'accesso, il percorso di archiviazione, gli strumenti usati per elaborare e accedere ai dati e i requisiti di conservazione dei dati.

Assicurarsi di integrare i log attività di Azure nella registrazione centrale. Inserire i log tramite monitoraggio di Azure per aggregare i dati di sicurezza generati dai dispositivi endpoint, le risorse di rete e altri sistemi di sicurezza. In monitoraggio di Azure usare le aree di lavoro Log Analytics per eseguire query ed eseguire analisi e usare gli account di archiviazione di Azure per l'archiviazione a lungo termine e di archiviazione.

Inoltre, abilitare e caricare i dati in Sentinel di Azure o in un SIEM di terze parti.

Molte organizzazioni scelgono di usare Sentinel di Azure per i dati "attivi" usati di frequente e archiviazione di Azure per dati "a freddo" usati con minore frequenza. 

- [Come raccogliere i log e le metriche della piattaforma con monitoraggio di Azure](../../azure-monitor/platform/diagnostic-settings.md)

- [Come eseguire l'onboarding di Azure Sentinel](../../sentinel/quickstart-onboard.md)

**Responsabilità**: Customer

**Stakeholder** per la sicurezza dei clienti ([altre informazioni](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Architettura di sicurezza](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Sicurezza delle applicazioni e DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [Sicurezza dell'infrastruttura e degli endpoint](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

## <a name="lt-6-configure-log-storage-retention"></a>LT-6: configurare la conservazione dell'archiviazione dei log

| ID Azure | Controlli CIS v 7.1 ID/i | ID NIST SP800-53 R4 |
|--|--|--|--|
| LT-6 | 6.4 | AU-3, AU-11 |

Configurare la conservazione dei log in base ai requisiti aziendali, normativi e di conformità. 

In monitoraggio di Azure è possibile impostare il periodo di conservazione dell'area di lavoro Log Analytics in base alle normative di conformità dell'organizzazione. Usare archiviazione di Azure, Data Lake o Log Analytics account dell'area di lavoro per l'archiviazione a lungo termine e di archiviazione.

- [Modificare il periodo di conservazione dei dati in Log Analytics](../../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

- [Come configurare i criteri di conservazione per i log dell'account di archiviazione di Azure](../../storage/common/storage-monitor-storage-account.md#configure-logging)

- [Esportazione avvisi e raccomandazioni del Centro sicurezza di Azure](../../security-center/continuous-export.md)

**Responsabilità**: Customer

**Stakeholder** per la sicurezza dei clienti ([altre informazioni](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Architettura di sicurezza](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Sicurezza delle applicazioni e DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [Operazioni per la sicurezza](/azure/cloud-adoption-framework/organize/cloud-security-operations-center) 

- [Gestione della conformità alla sicurezza](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="lt-7-use-approved-time-synchronization-sources"></a>LT-7: usare le origini di sincronizzazione dell'ora approvate

| ID Azure | Controlli CIS v 7.1 ID/i | ID NIST SP800-53 R4 |
|--|--|--|--|
| LT-7 | 6.1 | AU-8 |

Microsoft mantiene le origini temporali per la maggior parte dei servizi SaaS e PaaS di Azure. Per le macchine virtuali, usare il server NTP predefinito Microsoft per la sincronizzazione dell'ora, a meno che non si disponga di un requisito specifico.  Se è necessario configurare un server NTP (Network Time Protocol), assicurarsi di proteggere la porta del servizio UDP 123.

Tutti i log generati dalle risorse in Azure forniscono indicatori temporali con il fuso orario specificato per impostazione predefinita.

- [Come configurare la sincronizzazione dell'ora per le risorse di calcolo Windows di Azure](../../virtual-machines/windows/time-sync.md)

- [Come configurare la sincronizzazione dell'ora per le risorse di calcolo Linux di Azure](../../virtual-machines/linux/time-sync.md)

- [Come disabilitare UDP in ingresso per i servizi di Azure](https://support.microsoft.com/help/4558520/how-to-disable-inbound-udp-for-azure-services)

**Responsabilità**: Condiviso

**Stakeholder** per la sicurezza dei clienti ([altre informazioni](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Criteri e standard](/azure/cloud-adoption-framework/organize/cloud-security-policy-standards)

- [Sicurezza delle applicazioni e DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [Sicurezza dell'infrastruttura e degli endpoint](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

