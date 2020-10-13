---
title: Connettere origini dati ad Azure Sentinel | Microsoft Docs
description: Informazioni su come connettere origini dati come Microsoft 365 Defender (in precedenza Microsoft Threat Protection), Microsoft 365 e Office 365, Azure AD, ATP e Cloud App Security ad Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2020
ms.author: yelevin
ms.openlocfilehash: c1df52f027c23b2e3618ad17494b06c2ccecfaf6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91627143"
---
# <a name="connect-data-sources"></a>Connettere le origini dati

Una volta abilitata la funzionalità Sentinel di Azure, è prima di tutto necessario connettere le origini dati. Azure Sentinel è dotato di un numero di connettori per le soluzioni Microsoft, disponibile in modalità predefinita e fornisce l'integrazione in tempo reale, incluse soluzioni Microsoft 365 Defender (in precedenza Microsoft Threat Protection), origini Microsoft 365 (incluso Office 365), Azure AD, Microsoft Defender per Identity (in precedenza Azure ATP), Microsoft Cloud App Security e altro ancora. Inoltre, sono presenti connettori predefiniti a soluzioni non Microsoft, per l'ecosistema di sicurezza allargato. Per connettere le origini dati con Azure Sentinel è anche possibile usare CEF (Common Event Format), Syslog o API REST.

1. Nel menu selezionare **Data connectors** (Connettori dati). Questa pagina consente di visualizzare l'elenco completo dei connettori forniti da Azure Sentinel e il relativo stato. Selezionare il connettore che si vuole connettere e scegliere **Open connector page** (Apri la pagina del connettore). 

   ![Raccolta di connettori dati](./media/collect-data/collect-data-page.png)

1. Nella pagina del connettore specifico assicurarsi di aver soddisfatto tutti i prerequisiti e seguire le istruzioni per connettere i dati ad Azure Sentinel. L'avvio della sincronizzazione dei log con Azure Sentinel potrebbe richiedere tempo. Dopo la connessione viene visualizzato un riepilogo dei dati nel grafico **Data received** (Dati ricevuti), oltre allo stato di connettività dei tipi di dati.

   ![Configurare i connettori dati](./media/collect-data/opened-connector-page.png)
  
1. Fare clic sulla scheda **Next steps** (Passaggi successivi) per ottenere un elenco del contenuto predefinito fornito da Azure Sentinel per il tipo di dati specifico.

   ![Passaggi successivi per i connettori](./media/collect-data/data-insights.png)
 

## <a name="data-connection-methods"></a>Metodi di connessione dati

Azure Sentinel supporta i metodi di connessione dati seguenti:

- **Integrazione da servizio a servizio**:<br> alcuni servizi, come AWS e i servizi Microsoft, sono connessi in modo nativo. Tali servizi sfruttano la struttura di Azure per l'integrazione immediata, di conseguenza è possibile connettere le soluzioni seguenti in pochi clic:
    - [Amazon Web Services - CloudTrail](connect-aws.md)
    - Log di controllo [Azure Active Directory](connect-azure-active-directory.md) e log di accesso
    - [Attività di Azure](connect-azure-activity.md)
    - [Azure AD Identity Protection](connect-azure-ad-Identity-protection.md)
    - [Protezione DDoS di Azure](connect-azure-ddos-protection.md)
    - [Azure Defender per](connect-asc-iot.md) le cose (in precedenza il Centro sicurezza di Azure per tutto)
    - [Azure Information Protection](connect-azure-information-protection.md)
    - [Firewall di Azure](connect-azure-firewall.md)
    - [Centro sicurezza di Azure](connect-azure-security-center.md) : avvisi dalle soluzioni di Azure Defender
    - [Web Application Firewall (WAF) di Azure](connect-azure-waf.md) (in precedenza Microsoft WAF)
    - [Cloud App Security](connect-cloud-app-security.md)
    - [Domain Name Server](connect-dns.md)
    - [Microsoft 365 Defender](connect-microsoft-365-defender.md) -include dati non ELABORAti MDATP
    - [Microsoft Defender per endpoint](connect-microsoft-defender-advanced-threat-protection.md) (in precedenza Microsoft Defender Advanced Threat Protection)
    - [Microsoft Defender per Identity](connect-azure-atp.md) (in precedenza Azure Advanced Threat Protection)
    - [Microsoft Defender per office 365](connect-office-365-advanced-threat-protection.md) (in precedenza Office 365 Advanced Threat Protection)
    - [Office 365](connect-office-365.md) (ora con i team)
    - [Windows Firewall](connect-windows-firewall.md)
    - [Eventi di sicurezza di Windows](connect-windows-security-events.md)

- **Soluzioni esterne tramite API**: alcune origini dati vengono connesse tramite le API fornite dall'origine dati connessa. In genere, la maggior parte delle tecnologie di sicurezza fornisce un set di API tramite cui è possibile recuperare i registri eventi. Le API si connettono ad Azure Sentinel e raccolgono tipi di dati specifici da inviare ad Azure Log Analytics. Le appliance connesse tramite API includono:
    
    - [Alcide kAudit](connect-alcide-kaudit.md)
    - [Barracuda WAF](connect-barracuda.md)
    - [Barracuda CloudGen Firewall](connect-barracuda-cloudgen-firewall.md)
    - [Citrix Analytics (Security)](connect-citrix-analytics.md)
    - [F5 BIG-IP](connect-f5-big-ip.md)
    - [Forcepoint DLP](connect-forcepoint-dlp.md)
    - [Okta SSO](connect-okta-single-sign-on.md)
    - [Orca Security](connect-orca-security-alerts.md)
    - [Log di Perimeter 81](connect-perimeter-81-logs.md)
    - [Proofpoint TAP](connect-proofpoint-tap.md)
    - [Qualys VM](connect-qualys-vm.md)
    - [Squadra Technologies secRMM](connect-squadra-secrmm.md)
    - [Symantec ICDX](connect-symantec.md)
    - [VMware Carbon Black Cloud Endpoint Standard](connect-vmware-carbon-black.md)
    - [Zimperium](connect-zimperium-mtd.md)


- **Soluzioni esterne tramite Agent**: Azure Sentinel può essere connesso tramite un agente a qualsiasi altra origine dati in grado di eseguire lo streaming dei log in tempo reale usando il protocollo syslog.

    La maggior parte delle appliance usa il protocollo Syslog per inviare messaggi di evento che includono il log stesso e dati sul log. Il formato dei log varia, ma la maggior parte degli appliance supporta la formattazione basata su CEF per i dati di log. 

    L'agente Sentinel di Azure, che è in realtà l'agente Log Analytics, converte i log in formato CEF in un formato che può essere inserito da Log Analytics. A seconda del tipo di dispositivo, l'agente viene installato direttamente nell'appliance o in un server d'accesso di log basato su Linux dedicato. L'agente per Linux riceve gli eventi dal daemon Syslog tramite UDP, ma se è previsto che un computer Linux raccolga un numero elevato di eventi Syslog, questi vengono inviati tramite TCP dal daemon Syslog all'agente e quindi dall'agente a Log Analytics.

    - **Firewall, proxy ed endpoint-CEF:**
        - [AI Vectra Detect](connect-ai-vectra-detect.md)
        - [Check Point](connect-checkpoint.md)
        - [Cisco ASA](connect-cisco.md)
        - [ExtraHop Reveal(x)](connect-extrahop.md)
        - [F5 ASM](connect-f5.md)
        - [Prodotti Forcepoint](connect-forcepoint-casb-ngfw.md)
        - [Fortinet](connect-fortinet.md)
        - [Illusive Networks AMS](connect-illusive-attack-management-system.md)
        - [One Identity Safeguard](connect-one-identity.md)
        - [Palo Alto Networks](connect-paloalto.md)
        - [Trend Micro Deep Security](connect-trend-micro.md)
        - [Zscaler](connect-zscaler.md)
        - [Altre appliance basate su CEF](connect-common-event-format.md)
    - **Firewall, proxy ed endpoint-syslog:**
        - [Infoblox NIOS](connect-infoblox.md)
        - [Pulse Connect Secure](connect-pulse-connect-secure.md)
        - [Sophos XG](connect-sophos-xg-firewall.md)
        - [Symantec Proxy SG](connect-symantec-proxy-sg.md)
        - [Symantec VIP](connect-symantec-vip.md)
        - [Altre appliance basate su syslog](connect-syslog.md)
    - Soluzioni DLP
    - [Provider di intelligence sulle minacce](connect-threat-intelligence.md)
    - [Computer DNS](connect-dns.md) -agente installato direttamente nel computer DNS
    - [Macchine virtuali Azure Stack](connect-azure-stack.md)
    - Server Linux
    - Altri cloud
    
## <a name="agent-connection-options"></a>Opzioni di connessione tramite agente<a name="agent-options"></a>

Per connettere l'appliance esterna ad Azure Sentinel, l'agente deve essere distribuito in un computer dedicato (macchina virtuale o locale) per supportare la comunicazione tra l'appliance e Azure Sentinel. È possibile distribuire l'agente manualmente o automaticamente. La distribuzione automatica è disponibile solo se il computer dedicato è una nuova macchina virtuale che si sta creando in Azure. 


![CEF in Azure](./media/connect-cef/cef-syslog-azure.png)

In alternativa, è possibile distribuire l'agente manualmente in una macchina virtuale di Azure esistente, in una macchina virtuale in un altro cloud o in un computer locale.

![CEF in locale](./media/connect-cef/cef-syslog-onprem.png)

## <a name="map-data-types-with-azure-sentinel-connection-options"></a>Mappare i tipi di dati alle opzioni di connessione di Azure Sentinel


| **Tipo di dati** | **Modalità di connessione** | **Connettore dati?** | **Commenti** |
|------|---------|-------------|------|
| AWSCloudTrail | [Connettere AWS](connect-aws.md) | &#10003; | |
| AzureActivity | [Connettere il log attività di Azure](connect-azure-activity.md) e [Panoramica del log attività di Azure](../azure-monitor/platform/platform-logs-overview.md)| &#10003; | |
| AuditLogs | [Connettere Azure AD](connect-azure-active-directory.md)  | &#10003; | |
| SigninLogs | [Connettere Azure AD](connect-azure-active-directory.md)  | &#10003; | |
| AzureFirewall |[Diagnostica di Azure](../firewall/tutorial-diagnostics.md) | &#10003; | |
| InformationProtectionLogs_CL  | [Report di Azure Information Protection](https://docs.microsoft.com/azure/information-protection/reports-aip)<br>[Connettere Azure Information Protection](connect-azure-information-protection.md)  | &#10003; | Oltre al tipo di dati, in genere si usa la funzione **InformationProtectionEvents**. Per altre informazioni, vedere [Come modificare i report e creare query personalizzate](https://docs.microsoft.com/azure/information-protection/reports-aip#how-to-modify-the-reports-and-create-custom-queries)|
| AzureNetworkAnalytics_CL  | [Schema di analisi del traffico](../network-watcher/traffic-analytics.md) [Analisi del traffico](../network-watcher/traffic-analytics.md)  | | |
| CommonSecurityLog  | [Connettere CEF](connect-common-event-format.md)  | &#10003; | |
| OfficeActivity | [Connettere Office 365](connect-office-365.md) | &#10003; | |
| SecurityEvents | [Connettere gli eventi di sicurezza di Windows](connect-windows-security-events.md)  | &#10003; | Per le cartelle di lavoro dei protocolli non sicuri, vedere la pagina relativa alla [configurazione di cartelle di lavoro di protocolli non sicuri](/azure/sentinel/quickstart-get-visibility#use-built-in-workbooks).  |
| syslog | [Connettere Syslog](connect-syslog.md) | &#10003; | |
| Microsoft Web Application Firewall (WAF) - (AzureDiagnostics) |[Connettere Microsoft web application firewall](connect-microsoft-waf.md) | &#10003; | |
| SymantecICDx_CL | [Connettere Symantec](connect-symantec.md) | &#10003; | |
| ThreatIntelligenceIndicator  | [Connettere Intelligence sulle minacce](connect-threat-intelligence.md)  | &#10003; | |
| VMConnection <br> ServiceMapComputer_CL<br> ServiceMapProcess_CL|  [Elenco dei servizi di Monitoraggio di Azure](../azure-monitor/insights/service-map.md)<br>[Onboarding di Informazioni dettagliate macchina virtuale di Monitoraggio di Azure](../azure-monitor/insights/vminsights-onboard.md) <br> [Abilitare Informazioni dettagliate macchina virtuale di Monitoraggio di Azure](../azure-monitor/insights/vminsights-enable-overview.md) <br> [Uso dell'onboarding di singole macchine virtuali](../azure-monitor/insights/vminsights-enable-single-vm.md)<br>  [Uso dell'onboarding tramite criteri](../azure-monitor/insights/vminsights-enable-at-scale-policy.md)| &#10007; | Cartella di lavoro di Informazioni dettagliate macchina virtuale  |
| DnsEvents | [Connettere DNS](connect-dns.md) | &#10003; | |
| W3CIISLog | [Connettere i log di IIS](../azure-monitor/platform/data-sources-iis-logs.md)  | &#10007; | |
| WireData | [Connettere Dati in transito](../azure-monitor/insights/wire-data.md) | &#10007; | |
| WindowsFirewall | [Connettere Windows Firewall](connect-windows-firewall.md) | &#10003; | |
| SecurityAlert per Azure AD Identity Protection  | [Connettere Azure AD Identity Protection](connect-azure-ad-identity-protection.md)  | &#10003; | |
| SecurityAlert per Azure ATP  | [Connettere Microsoft Defender per Identity](connect-azure-atp.md) (in precedenza Azure ATP) | &#10003; | |
| SecurityAlert per Centro sicurezza di Azure  | [Connettere gli avvisi di Azure Defender](connect-azure-security-center.md) dal centro sicurezza di Azure  | &#10003; | |
| SecurityAlert per Microsoft Cloud App Security  | [Connettere Microsoft Cloud App Security](connect-cloud-app-security.md)  | &#10003; | |
| SecurityAlert | | | |
| Sysmon (evento) | [Connettere Sysmon](https://azure.microsoft.com/blog/detecting-in-memory-attacks-with-sysmon-and-azure-security-center)<br> [Connettere Eventi Windows](../azure-monitor/platform/data-sources-windows-events.md) <br> [Ottenere il parser Sysmon](https://github.com/Azure/Azure-Sentinel/blob/master/Parsers/Sysmon/Sysmon-v10.42-Parser.txt)| &#10007; | La raccolta Sysmon non è installata per impostazione predefinita nelle macchine virtuali. Per altre informazioni su come installare l'agente Sysmon, vedere [Sysmon](https://docs.microsoft.com/sysinternals/downloads/sysmon). |
| ConfigurationData  | [Automatizzare l'inventario delle macchine virtuali](../automation/automation-vm-inventory.md)| &#10007; | |
| ConfigurationChange  | [Automatizzare il rilevamento delle macchine virtuali](../automation/change-tracking.md) | &#10007; | |
| BIG-IP F5 | [Connettere BIG-IP F5](https://devcentral.f5.com/s/articles/Integrating-the-F5-BIGIP-with-Azure-Sentinel)  | &#10007; | |
| McasShadowItReporting  |  | &#10007; | |
| Barracuda_CL | [Connettere Barracuda](connect-barracuda.md) | &#10003; | |


## <a name="next-steps"></a>Passaggi successivi

- Per iniziare a usare Azure Sentinel, è necessario avere una sottoscrizione di Microsoft Azure. Se non si ha una sottoscrizione, è possibile iscriversi per una [versione di valutazione gratuita](https://azure.microsoft.com/free/).
- Informazioni su come [caricare i dati in Azure Sentinel](quickstart-onboard.md) e [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
