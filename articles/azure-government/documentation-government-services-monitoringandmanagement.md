---
title: Monitoraggio e gestione di Azure per enti pubblici | Microsoft Docs
description: "Fornisce un confronto delle funzionalità e alcune linee guida sullo sviluppo di applicazioni per Azure per enti pubblici."
services: azure-government
cloud: gov
documentationcenter: 
author: ryansoc
manager: zakramer
ms.assetid: 4b7720c1-699e-432b-9246-6e49fb77f497
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 3/13/2017
ms.author: ryansoc
translationtype: Human Translation
ms.sourcegitcommit: c1cd1450d5921cf51f720017b746ff9498e85537
ms.openlocfilehash: 4d7de786dc902cb1c32e70a1f69bc74282de44f1
ms.lasthandoff: 03/14/2017


---
# <a name="azure-government-monitoring--management"></a>Monitoraggio e gestione di Azure per enti pubblici
Questo articolo descrive le variazioni ai servizi di monitoraggio e gestione e presenta alcune considerazioni sull'ambiente di Azure per enti pubblici.

## <a name="automation"></a>Automazione
Automazione è disponibile a livello generale in Azure per enti pubblici.

### <a name="variations"></a>Varianti
Le funzionalità di Automazione seguenti non sono attualmente disponibili in Azure per enti pubblici.

* Creazione di credenziali dell'entità servizio per l'autenticazione

Per altre informazioni, vedere la [documentazione pubblica di Automazione](../automation/automation-intro.md).

## <a name="backup"></a>Backup
Backup è disponibile a livello generale in Azure per enti pubblici.

Per altre informazioni, vedere [Azure Government Backup](documentation-government-services-backup.md) (Backup di Azure per enti pubblici).

## <a name="resource-policy"></a>Criteri delle risorse

I [criteri delle risorse di Azure](../azure-resource-manager/resource-manager-policy.md) non sono disponibili in Azure per enti pubblici.

## <a name="site-recovery"></a>Site Recovery
Azure Site Recovery è disponibile a livello generale in Azure per enti pubblici.

Per altre informazioni, vedere la [documentazione pubblica su Site Recovery](../site-recovery/site-recovery-overview.md).

### <a name="variations"></a>Varianti
Le funzionalità di Site Recovery seguenti non sono attualmente disponibili in Azure per enti pubblici:

* Insieme di credenziali per Site Recovery per Azure Resource Manager
* Notifica tramite posta elettronica

| Site Recovery | Classico | Gestione risorse |
| --- | --- | --- |
| Server fisico/VMware  | GA | GA |
| Hyper-V | GA | GA |
| Da sito a sito | GA | GA |

>[!NOTE]
>La tabella è valida per US Gov Virginia e US Gov Iowa.

Gli URL per Site Recovery sono diversi in Azure per enti pubblici:

| Azure Public | Azure Government | Note |
| --- | --- | --- |
| \*.hypervrecoverymanager.windowsazure.com | \*.hypervrecoverymanager.windowsazure.us | Accesso al servizio Site Recovery |
| \*.backup.windowsazure.com  | \*.backup.windowsazure.us | Accesso al servizio di protezione |
| \*.blob.core.windows.net | \*.blob.core.usgovcloudapi.net | Per l'archiviazione di snapshot di VM |
| http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi | http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi | Per scaricare MySQL |

## <a name="log-analytics"></a>Log Analytics
Log Analytics è disponibile a livello generale in Azure per enti pubblici.

### <a name="variations"></a>Varianti
Le funzionalità e le soluzioni di Log Analytics seguenti non sono attualmente disponibili in Azure per enti pubblici.

* Soluzioni in anteprima in Microsoft Azure, tra cui:
  * Soluzione di monitoraggio della rete
  * Elenco dei servizi
  * Soluzioni di Office 365
  * Soluzione di analisi di aggiornamento di Windows 10
  * Soluzione Application Insights
  * Soluzione di analisi della rete di Azure
  * Soluzione Log Analytics per Automazione di Azure
  * Soluzione Log Analytics per l'insieme di credenziali delle chiavi
* Soluzioni e funzionalità che richiedono aggiornamenti del software locale, tra cui:
  * Soluzione Surface Hub
* Funzionalità in anteprima in Azure pubblico, tra cui:
  * Esportazione di dati in Power BI
* Metriche e diagnostica di Azure
* Applicazioni per dispositivi mobili di Operations Management Suite

Gli URL per Log Analytics sono diversi in Azure per enti pubblici:

| Azure Public | Azure Government | Note |
| --- | --- | --- |
| mms.microsoft.com |oms.microsoft.us |Portale di Log Analytics |
| *workspaceId*.ods.opinsights.azure.com |*workspaceId*.ods.opinsights.azure.us |[API di raccolta dati](../log-analytics/log-analytics-data-collector-api.md) |
| \*.ods.opinsights.azure.com |\*.ods.opinsights.azure.us |Comunicazione tra gli agenti: [configurazione delle impostazioni del firewall](../log-analytics/log-analytics-proxy-firewall.md) |
| \*.oms.opinsights.azure.com |\*.oms.opinsights.azure.us |Comunicazione tra gli agenti: [configurazione delle impostazioni del firewall](../log-analytics/log-analytics-proxy-firewall.md) |
| \*.blob.core.windows.net |\*.blob.core.usgovcloudapi.net |Comunicazione tra gli agenti: [configurazione delle impostazioni del firewall](../log-analytics/log-analytics-proxy-firewall.md) |

Le funzionalità di Log Analytics seguenti hanno un comportamento diverso in Azure per enti pubblici:

* Per connettere il server di gestione di System Center Operations Manager a Log Analytics, è necessario scaricare e importare i Management Pack aggiornati.
  + System Center Operations Manager 2016
    1. Installare l'[aggiornamento cumulativo 2 per System Center Operations Manager 2016](https://support.microsoft.com/help/3209591).
    2. Importare i management pack inclusi nell'aggiornamento cumulativo 2 in Operations Manager. Per informazioni su come importare un Management Pack da un disco, vedere [Come importare un Management Pack di Operations Manager](http://technet.microsoft.com/library/hh212691.aspx).
    3. Per connettere Operations Manager a Log Analytics, seguire i passaggi in [Connettere Operations Manager a Log Analytics](../log-analytics/log-analytics-om-agents.md).
  + System Center Operations Manager 2012 R2 UR3 (o versione successiva)/Operations Manager 2012 SP1 UR7 (o versione successiva)
    1. Scaricare e salvare i [Management Pack aggiornati](http://go.microsoft.com/fwlink/?LinkId=828749).
    2. Decomprimere il file scaricato.
    3. Importare i Management Pack in Operations Manager. Per informazioni su come importare un Management Pack da un disco, vedere [Come importare un Management Pack di Operations Manager](http://technet.microsoft.com/library/hh212691.aspx).
    4. Per connettere Operations Manager a Log Analytics, seguire i passaggi in [Connettere Operations Manager a Log Analytics](../log-analytics/log-analytics-om-agents.md).
  
* Per usare i [gruppi di computer da System Center Configuration Manager 2016](../log-analytics/log-analytics-sccm.md), è necessario usare [Technical Preview 1701](https://docs.microsoft.com/en-us/sccm/core/get-started/technical-preview) o versione successiva.

### <a name="frequently-asked-questions"></a>Domande frequenti
* È possibile eseguire la migrazione di dati da Log Analytics in Microsoft Azure ad Azure per enti pubblici?
  * No. Non è possibile spostare dati o l'area di lavoro da Microsoft Azure ad Azure per enti pubblici.
* È possibile passare tra le aree di lavoro di Microsoft Azure e Azure per enti pubblici dal portale di Log Analytics di Operations Management Suite?
  * No. I portali per Microsoft Azure e Azure per enti pubblici sono separati e non condividono informazioni.

Per altre informazioni, vedere la [documentazione pubblica su Log Analytics](../log-analytics/log-analytics-overview.md).

## <a name="next-steps"></a>Passaggi successivi
Per informazioni aggiuntive e aggiornamenti, sottoscrivere il <a href="https://blogs.msdn.microsoft.com/azuregov/">blog di Microsoft Azure per enti pubblici. </a>

