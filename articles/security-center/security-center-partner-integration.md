---
title: Integrazione dei partner nel Centro sicurezza di Azure | Microsoft Docs
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
ms.date: 08/18/2017
ms.author: yurid
ms.translationtype: HT
ms.sourcegitcommit: 847eb792064bd0ee7d50163f35cd2e0368324203
ms.openlocfilehash: 44beafeff5cbe58ac8ca37632879f6ffc2b67e53
ms.contentlocale: it-it
ms.lasthandoff: 08/19/2017

---
# <a name="partner-integration-in-azure-security-center"></a>Integrazione dei partner nel Centro sicurezza di Azure

Questo articolo descrive l'integrazione del Centro sicurezza di Azure con i partner per una migliore sicurezza complessiva. Il Centro sicurezza di Azure offre un'esperienza integrata in Azure e sfrutta Azure Marketplace per la fatturazione e la certificazione dei partner.

> [!NOTE] 
> A partire da giugno 2017, il Centro sicurezza usa Microsoft Monitoring Agent per raccogliere e archiviare i dati. Per altre informazioni, vedere [Migrazione della piattaforma del Centro sicurezza di Azure](security-center-platform-migration.md). Le informazioni contenute in questo articolo si riferiscono alle funzionalità del Centro sicurezza dopo la transizione a Microsoft Monitoring Agent.
>

## <a name="why-deploy-partner-solutions-from-security-center"></a>Perché distribuire soluzioni partner dal Centro sicurezza

I quattro motivi principali per sfruttare l'integrazione dei partner nel Centro sicurezza sono i seguenti:

- **Facilità di distribuzione**. Distribuire una soluzione partner seguendo le raccomandazioni del Centro sicurezza è molto più semplice. Il processo di distribuzione può essere completamente automatizzato usando una configurazione e una topologia di rete predefinite. In alternativa, i clienti possono scegliere un'opzione semi-automatizzata per ottenere un livello superiore di flessibilità e personalizzazione.
- **Rilevamenti integrati**. Gli eventi di sicurezza delle soluzioni partner vengono raccolti, aggregati e visualizzati automaticamente nell'ambito degli avvisi e degli eventi imprevisti del Centro sicurezza. Questi eventi vengono anche combinati con i rilevamenti di altre origini per offrire funzionalità avanzate di rilevamento delle minacce.
- **Gestione e monitoraggio dell'integrità unificati**. I clienti possono usare gli eventi di integrità integrati per monitorare in modo immediato tutte le soluzioni partner. Sono disponibili funzionalità di gestione di base, con un facile accesso alla configurazione avanzata con la soluzione partner.
- **Esportazione in SIEM**. I clienti possono esportare tutti gli avvisi in formato CEF (Common Event Format) del Centro sicurezza e dei partner in sistemi SIEM (Security Information and Event Management) locali usando l'integrazione dei log di Azure (anteprima).


## <a name="partners-that-integrate-with-security-center"></a>Partner integrati con il Centro sicurezza

Il Centro sicurezza offre attualmente l'integrazione con le soluzioni seguenti:

- Protezione endpoint ([Trend Micro](https://help.deepsecurity.trendmicro.com/azure-marketplace-getting-started-with-deep-security.html), Symantec e [Microsoft Antimalware per Servizi cloud e Macchine virtuali di Azure](https://docs.microsoft.com/azure/security/azure-security-antimalware)) 
- Web application firewall ([Barracuda](https://www.barracuda.com/products/webapplicationfirewall), [F5](https://support.f5.com/kb/en-us/products/big-ip_asm/manuals/product/bigip-ve-web-application-firewall-microsoft-azure-12-0-0.html), [Imperva](https://www.imperva.com/Products/WebApplicationFirewall-WAF), [Fortinet](https://www.fortinet.com/resources.html?limit=10&search=&document-type=data-sheets) e [gateway applicazione di Azure](https://azure.microsoft.com/blog/azure-web-application-firewall-waf-generally-available/)) 
- Firewall di nuova generazione ([Check Point](https://www.checkpoint.com/products/vsec-microsoft-azure/), [Barracuda](https://campus.barracuda.com/product/nextgenfirewallf/article/NGF/AzureDeployment/), [Fortinet](http://docs.fortinet.com/d/fortigate-fortios-handbook-the-complete-guide-to-fortios-5.2) e [Cisco](http://www.cisco.com/c/en/us/td/docs/security/firepower/quick_start/azure/ftdv-azure-qsg.html)) 
- Valutazione della vulnerabilità ([Qualys](https://www.qualys.com/public-clouds/microsoft-azure/))  

Nel corso del tempo, il Centro sicurezza estenderà il numero di partner all'interno di queste categorie e aggiungerà nuove categorie. 

## <a name="deploy-a-partner-solution"></a>Distribuire una soluzione partner

In base alla configurazione dell'ambiente Azure e ai criteri di sicurezza definiti, il Centro sicurezza potrebbe raccomandare la distribuzione di una soluzione partner. La raccomandazione del Centro sicurezza descrive il processo di selezione e installazione di una soluzione partner. L'esperienza complessiva di distribuzione può variare a seconda del tipo di partner e di soluzione in uso. Per altre informazioni, vedere gli articoli seguenti:

- [Installare Endpoint Protection](security-center-install-endpoint-protection.md)
- [Aggiungere un Web Application Firewall](security-center-add-web-application-firewall.md)
- [Aggiungere un firewall di nuova generazione](security-center-add-next-generation-firewall.md)
- [La valutazione della vulnerabilità non è installata](security-center-vulnerability-assessment-recommendations.md)

## <a name="manage-partner-solutions"></a>Gestire le soluzioni partner

Dopo la distribuzione, per visualizzare informazioni sull'integrità della soluzione ed eseguire attività di gestione di base selezionare l'opzione **Soluzioni partner** nel pannello **Centro sicurezza**. Per altre informazioni sulla gestione delle soluzioni partner nel Centro sicurezza, leggere [Monitoraggio delle soluzioni dei partner con il Centro sicurezza di Azure](security-center-partner-solutions.md).

![Integrazione dei partner](./media/security-center-partner-integration/security-center-partner-integration-fig1-new2.png)

> [!NOTE]
> Il supporto per Symantec Endpoint Protection è limitato all'individuazione. Non sono disponibili avvisi sull'integrità.
>

## <a name="see-also"></a>Vedere anche

In questo articolo è stato illustrato come integrare soluzioni partner nel Centro sicurezza di Azure. Per altre informazioni sul Centro sicurezza, vedere gli articoli seguenti:

* [Guida alla pianificazione e alla gestione del Centro sicurezza di Azure](security-center-planning-and-operations-guide.md)
* [Gestire e rispondere agli avvisi di sicurezza in Centro sicurezza](security-center-managing-and-responding-alerts.md)
* [Avvisi di sicurezza per tipo nel Centro sicurezza di Azure](security-center-alerts-type.md)
* [Monitoraggio dell'integrità della sicurezza nel Centro sicurezza di Azure](security-center-monitoring.md). Informazioni su come monitorare l'integrità delle risorse di Azure.
* [Monitoraggio delle soluzioni dei partner con il Centro sicurezza di Azure](security-center-partner-solutions.md). Informazioni su come monitorare lo stato integrità delle soluzioni dei partner.
* [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md). Risposte alle domande frequenti sull'uso del servizio.
* [Blog sulla sicurezza di Azure](http://blogs.msdn.com/b/azuresecurity/). Post di blog sulla sicurezza e sulla conformità di Azure.

