---
title: Integrare soluzioni di sicurezza nel Centro sicurezza di Azure | Microsoft Docs
description: Informazioni sull'integrazione del Centro sicurezza di Azure con i partner per una migliore sicurezza complessiva delle risorse di Azure.
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 6af354da-f27a-467a-8b7e-6cbcf70fdbcb
ms.service: security-center
ms.topic: hero-article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: yurid
ms.openlocfilehash: 42cbc442d03cdca04d380d05d9e904355476099e
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2017
---
# <a name="integrate-security-solutions-in-azure-security-center"></a>Integrare soluzioni di sicurezza nel Centro sicurezza di Azure
Questo documento aiuta a gestire le soluzioni di sicurezza già connesse al Centro sicurezza di Azure e ad aggiungerne di nuove.

## <a name="integrated-azure-security-solutions"></a>Soluzioni di sicurezza di Azure integrate
Il Centro sicurezza semplifica l'abilitazione di soluzioni di sicurezza integrate in Azure. I vantaggi includono:

- **Distribuzione semplificata**: il Centro sicurezza offre provisioning semplificato delle soluzioni dei partner integrate. Per le soluzioni come quelle antimalware e di valutazione delle vulnerabilità, il Centro sicurezza può effettuare il provisioning dell'agente necessario nelle macchine virtuali, mentre per gli appliance firewall il Centro sicurezza può occuparsi di gran parte delle attività di configurazione di rete necessarie.
- **Rilevamenti integrati**: gli eventi di sicurezza delle soluzioni dei partner vengono raccolti, aggregati e visualizzati automaticamente come parte degli avvisi e degli eventi imprevisti del Centro sicurezza. Questi eventi vengono anche combinati con i rilevamenti di altre origini per offrire funzionalità avanzate di rilevamento delle minacce.
- **Gestione e monitoraggio dell'integrità unificati**: i clienti possono usare eventi di integrità integrati per monitorare tutte le soluzioni dei partner in un attimo. Sono disponibili funzionalità di gestione di base, con un facile accesso alla configurazione avanzata con la soluzione partner.

Attualmente le soluzioni di sicurezza integrate includono:

- Endpoint Protection ([Trend Micro](https://help.deepsecurity.trendmicro.com/azure-marketplace-getting-started-with-deep-security.html), Symantec, Windows Defender e System Center Endpoint Protection)
- Web application firewall ([Barracuda](https://www.barracuda.com/products/webapplicationfirewall), [F5](https://support.f5.com/kb/en-us/products/big-ip_asm/manuals/product/bigip-ve-web-application-firewall-microsoft-azure-12-0-0.html), [Imperva](https://www.imperva.com/Products/WebApplicationFirewall-WAF), [Fortinet](https://www.fortinet.com/resources.html?limit=10&search=&document-type=data-sheets) e [gateway applicazione di Azure](https://azure.microsoft.com/blog/azure-web-application-firewall-waf-generally-available/))
- Firewall di nuova generazione ([Check Point](https://www.checkpoint.com/products/vsec-microsoft-azure/), [Barracuda](https://campus.barracuda.com/product/nextgenfirewallf/article/NGF/AzureDeployment/), [Fortinet](http://docs.fortinet.com/d/fortigate-fortios-handbook-the-complete-guide-to-fortios-5.2) e [Cisco](http://www.cisco.com/c/en/us/td/docs/security/firepower/quick_start/azure/ftdv-azure-qsg.html))
- Valutazione della vulnerabilità ([Qualys](https://www.qualys.com/public-clouds/microsoft-azure/))  

L'esperienza di integrazione di Endpoint Protection può variare in base alla soluzione. La tabella seguente include altri dettagli sull'esperienza relativa a ogni soluzione:

| Endpoint Protection               | Piattaforme                             | Installazione del Centro sicurezza | Individuazione del Centro sicurezza |
|-----------------------------------|---------------------------------------|------------------------------|---------------------------|
| Windows Defender (Microsoft Antimalware)                  | Windows Server 2016                   | No, predefinito nel sistema operativo           | Sì                       |
| System Center Endpoint Protection (Microsoft Antimalware) | Windows Server 2012 R2, 2012, 2008 R2 | Tramite estensione                | Sì                       |
| Trend Micro – Tutte le versioni         | Famiglia Windows Server                 | Tramite estensione                | Sì                       |
| Symantec v12.1.1100+                     | Famiglia Windows Server                 | No                           | Sì                        |
| MacAfee                           | Famiglia Windows Server                 | No                           | No                        |
| Kaspersky                         | Famiglia Windows Server                 | No                           | No                        |
| Sophos                            | Famiglia Windows Server                 | No                           | No                        |



## <a name="how-security-solutions-are-integrated"></a>Modalità di integrazione delle soluzioni di sicurezza
Le soluzioni di sicurezza di Azure distribuite dal Centro sicurezza vengono connesse automaticamente. È anche possibile connettere altre origini dati di sicurezza, tra cui:

- Azure AD Identity Protection
- Computer in esecuzione in locale o in altri cloud
- Soluzione di sicurezza che supporta Common Event Format (CEF)
- Microsoft Advanced Threat Analytics

![Integrazione di soluzioni di partner](./media/security-center-partner-integration/security-center-partner-integration-fig8.png)

## <a name="manage-integrated-azure-security-solutions-and-other-data-sources"></a>Gestire soluzioni di sicurezza di Azure integrate e altre origini dati

Dopo la distribuzione, è possibile visualizzare informazioni sull'integrità della soluzione di sicurezza di Azure integrata ed eseguire attività di gestione di base. È anche possibile connettere altri tipi di origini dati di sicurezza, ad esempio log dei firewall e avvisi di protezione di Azure Active Directory Identity Protection nel formato CEF (Common Event Format). Nel dashboard del Centro sicurezza selezionare Soluzioni di sicurezza.

### <a name="connected-solutions"></a>Soluzioni connesse

La sezione **Soluzioni connesse** include le soluzioni di sicurezza attualmente connesse al Centro sicurezza e informazioni sullo stato di integrità di ogni soluzione.  

![Soluzioni connesse](./media/security-center-partner-integration/security-center-partner-integration-fig4.png)

### <a name="discovered-solutions"></a>Discovered solutions (Soluzioni individuate)

La sezione **Soluzioni individuate** mostra tutte le soluzioni aggiunte tramite Azure, nonché tutte le soluzioni consigliate dal Centro sicurezza per la connessione.

![Discovered solutions (Soluzioni individuate)](./media/security-center-partner-integration/security-center-partner-integration-fig5.png)

Il Centro sicurezza individua automaticamente altre soluzioni di sicurezza in esecuzione in Azure. Sono incluse le soluzioni di Azure, ad esempio [Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection), e le soluzioni dei partner in esecuzione in Azure. Per integrare queste soluzioni nel Centro sicurezza, selezionare **CONNETTI**.

### <a name="add-data-sources"></a>Aggiungere origini dati

La sezione **Aggiungi origini dati** include altre origini dati disponibili che è possibile connettere. Per istruzioni sull'aggiunta di dati da una qualsiasi di queste origini, fare clic su **AGGIUNGI**.

![Origini dati](./media/security-center-partner-integration/security-center-partner-integration-fig7.png)


## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato illustrato come integrare soluzioni partner nel Centro sicurezza. Per altre informazioni sul Centro sicurezza, vedere gli articoli seguenti:

* [Guida alla pianificazione e alla gestione del Centro sicurezza di Azure](security-center-planning-and-operations-guide.md)
* [Connecting Microsoft Advanced Threat Analytics to Azure Security Center (Connessione di Microsoft Advanced Threat Analytics al Centro sicurezza di Azure)](security-center-ata-integration.md)
* [Connessione di Azure Active Directory Identity Protection al Centro sicurezza di Azure](security-center-aadip-integration.md)
* [Monitoraggio dell'integrità della sicurezza nel Centro sicurezza di Azure](security-center-monitoring.md). Informazioni su come monitorare l'integrità delle risorse di Azure.
* [Monitoraggio delle soluzioni dei partner con il Centro sicurezza di Azure](security-center-partner-solutions.md). Informazioni su come monitorare lo stato integrità delle soluzioni dei partner.
* [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md). Risposte alle domande frequenti sull'uso del Centro sicurezza.
* [Blog sulla sicurezza di Azure](http://blogs.msdn.com/b/azuresecurity/). Post di blog sulla sicurezza e sulla conformità di Azure.
