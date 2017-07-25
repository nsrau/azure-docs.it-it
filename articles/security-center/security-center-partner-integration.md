---
title: Integrazione dei partner nel Centro sicurezza di Azure | Microsoft Docs
description: Questo documento illustra come il Centro sicurezza di Azure si integra con i partner per migliorare la sicurezza complessiva delle risorse di Azure.
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
ms.date: 06/23/2017
ms.author: yurid
ms.translationtype: Human Translation
ms.sourcegitcommit: 7c69630688e4bcd68ab3b4ee6d9fdb0e0c46d04b
ms.openlocfilehash: 85460ea37d251b53074de9d832610d253700f2c1
ms.contentlocale: it-it
ms.lasthandoff: 06/24/2017


---
# Integrazione dei partner nel Centro sicurezza di Azure
<a id="partner-integration-in-azure-security-center" class="xliff"></a>
Questo documento illustra come il Centro sicurezza di Azure si integra con i partner per migliorare la sicurezza complessiva e offrire un'esperienza integrata in Azure, sfruttando i vantaggi di Azure Marketplace per la certificazione e la fatturazione dei partner.

>[!NOTE] 
>A partire dall'inizio di giugno 2017, il Centro sicurezza usa Microsoft Monitoring Agent per raccogliere e archiviare i dati. Per altre informazioni, vedere [Migrazione della piattaforma del Centro sicurezza di Azure](security-center-platform-migration.md). Le informazioni contenute in questo articolo si riferiscono alle funzionalità del Centro sicurezza dopo la transizione a Microsoft Monitoring Agent.
>

## Perché distribuire soluzioni dei partner dal Centro sicurezza?
<a id="why-deploy-partners-solutions-from-security-center" class="xliff"></a>

I quattro motivi principali per sfruttare l'integrazione di partner nel Centro sicurezza sono:

- **Facilità di distribuzione**: la distribuzione della soluzione di un partner seguendo le indicazioni del Centro sicurezza è molto più semplice. Il processo di distribuzione può essere completamente automatizzato tramite una topologia di rete e una configurazione predefinite oppure i clienti possono scegliere un'opzione semi-automatica per consentire una maggiore flessibilità e personalizzazione della configurazione.
- **Rilevamenti integrati**: gli eventi di sicurezza dalle soluzioni dei partner vengono automaticamente raccolti, aggregati e visualizzati come parte degli avvisi e degli eventi imprevisti del Centro sicurezza. Questi eventi vengono inoltre aggregati con i rilevamenti da altre origini per offrire una funzionalità avanzata di rilevamento delle minacce.
- **Gestione e monitoraggio dell'integrità unificati**: gli eventi di integrità integrati consentono ai clienti di monitorare tutte le soluzioni dei partner a colpo d'occhio. La gestione di base è disponibile tramite accesso semplice a una configurazione avanzata usando la soluzione dei partner.
- **Esportazione in SIEM**: i clienti possono ora esportare tutti gli avvisi del Centro sicurezza e dei partner nel formato CEF per i sistemi SIEM locali usando l'integrazione dei log di Microsoft Azure (anteprima).


## Quali partner sono integrati con il Centro sicurezza?
<a id="what-partners-are-integrated-with-security-center" class="xliff"></a>
Il Centro sicurezza attualmente si integra con le soluzioni seguenti:

- Endpoint Protection ([Trend Micro](https://help.deepsecurity.trendmicro.com/azure-marketplace-getting-started-with-deep-security.html), Symantec, [Microsoft Antimalware per Azure](https://docs.microsoft.com/azure/security/azure-security-antimalware)) 
- Web application firewall ([Barracuda](https://www.barracuda.com/products/webapplicationfirewall), [F5](https://support.f5.com/kb/en-us/products/big-ip_asm/manuals/product/bigip-ve-web-application-firewall-microsoft-azure-12-0-0.html), [Imperva](https://www.imperva.com/Products/WebApplicationFirewall-WAF), [Fortinet](https://www.fortinet.com/resources.html?limit=10&search=&document-type=data-sheets), [App Gateway WAF](https://azure.microsoft.com/blog/azure-web-application-firewall-waf-generally-available/)) 
- Firewall di nuova generazione ([Check Point](https://www.checkpoint.com/products/vsec-microsoft-azure/), [Barracuda](https://campus.barracuda.com/product/nextgenfirewallf/article/NGF/AzureDeployment/), [Fortinet](http://docs.fortinet.com/d/fortigate-fortios-handbook-the-complete-guide-to-fortios-5.2) e [Cisco](http://www.cisco.com/c/en/us/td/docs/security/firepower/quick_start/azure/ftdv-azure-qsg.html)) 
- Valutazione della vulnerabilità ([Qualys](https://www.qualys.com/public-clouds/microsoft-azure/))  

Nel corso del tempo, il Centro sicurezza estenderà il numero di partner all'interno di queste categorie esistenti e aggiungerà nuove categorie. 

## Come distribuire soluzioni dei partner?
<a id="how-to-deploy-a-partner-solution" class="xliff"></a>

In base alla configurazione dell'ambiente Azure e ai criteri di sicurezza definiti, Centro sicurezza può consigliare che venga distribuita una soluzione partner. La raccomandazione guiderà l'utente attraverso il processo di selezione e installazione di una soluzione partner. L'esperienza di distribuzione globale a questo punto può variare a seconda del partner e del tipo di soluzione. Per altre informazioni, vedere i collegamenti seguenti:

- [Aggiungere un Web Application Firewall](security-center-add-web-application-firewall.md)
- [Aggiungi un firewall di nuova generazione](security-center-add-next-generation-firewall.md)
- [Installa Endpoint Protection](security-center-install-endpoint-protection.md)
- [La valutazione della vulnerabilità non è installata](security-center-vulnerability-assessment-recommendations.md)

## Come gestire le soluzioni partner?
<a id="how-to-manage-partner-solutions" class="xliff"></a>

Dopo la distribuzione di una soluzione partner, è possibile visualizzare le informazioni sullo stato della soluzione ed eseguire attività di gestione di base dal riquadro Soluzione partner nel dashboard principale del Centro sicurezza. Per altre informazioni sulla gestione delle soluzioni partner nel Centro sicurezza, leggere [Monitoring partner solutions with Azure Security Center](security-center-partner-solutions.md) (Monitoraggio delle soluzioni partner con il Centro sicurezza di Azure).

![Integrazione dei partner](./media/security-center-partner-integration/security-center-partner-integration-fig1-1-newUI.png)

> [!NOTE]
> Il supporto per Symantec Endpoint Protection è limitato all'individuazione, ma gli avvisi sull'integrità non sono disponibili.
>

## Vedere anche
<a id="see-also" class="xliff"></a>
In questo documento è stato illustrato come integrare soluzioni partner nel Centro sicurezza di Azure. Per altre informazioni sul Centro sicurezza, vedere gli argomenti seguenti:

* [Guida alla pianificazione e alla gestione del Centro sicurezza di Azure](security-center-planning-and-operations-guide.md)
* [Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure](security-center-managing-and-responding-alerts.md)
* [Avvisi di sicurezza per tipo nel Centro sicurezza di Azure](security-center-alerts-type.md)
* [Monitoraggio dell'integrità della sicurezza nel Centro sicurezza di Azure](security-center-monitoring.md) : informazioni su come monitorare l'integrità delle risorse di Azure.
* [Monitoraggio delle soluzioni dei partner con il Centro sicurezza di Azure](security-center-partner-solutions.md) : informazioni su come monitorare l'integrità delle soluzioni dei partner.
* [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md) : domande frequenti sull'uso del servizio.
* [Blog sulla sicurezza di Azure](http://blogs.msdn.com/b/azuresecurity/) : post di blog sulla sicurezza e sulla conformità di Azure.

