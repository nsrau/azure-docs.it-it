---
title: Integrazione dei partner nel Centro sicurezza di Azure | Microsoft Docs
description: Questo documento illustra come il Centro sicurezza di Azure si integra con i partner per migliorare la sicurezza complessiva delle risorse di Azure.
services: security-center
documentationcenter: na
author: YuriDio
manager: swadhwa
editor: 
ms.assetid: 6af354da-f27a-467a-8b7e-6cbcf70fdbcb
ms.service: security-center
ms.topic: hero-article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/30/2017
ms.author: yurid
translationtype: Human Translation
ms.sourcegitcommit: 5001cd47b6ee51967d1286414ccefedd8e7e7813
ms.openlocfilehash: 095b5c9d1a888a4061450234f80c52c5834fbf53
ms.lasthandoff: 12/02/2016


---
# <a name="partner-integration-in-azure-security-center"></a>Integrazione dei partner nel Centro sicurezza di Azure
Questo documento illustra come il Centro sicurezza di Azure si integra con i partner per migliorare la sicurezza complessiva e offrire un'esperienza integrata in Azure, sfruttando i vantaggi di Azure Marketplace per la certificazione di partner e di fatturazione.

## <a name="why-deploy-partners-solutions-from-security-center"></a>Perché distribuire soluzioni dei partner dal Centro sicurezza?

I quattro motivi principali per usufruire dell'integrazione di partner nel Centro sicurezza sono:

- **Facilità di distribuzione**: la distribuzione della soluzione di un partner seguendo le indicazioni del Centro sicurezza è molto più semplice. Il processo di distribuzione può essere completamente automatizzato tramite una topologia di rete e una configurazione predefinita oppure i clienti possono scegliere un'opzione semi-automatica per consentire una maggiore flessibilità e personalizzazione della configurazione.
- **Rilevamenti integrati**: gli eventi di sicurezza dalle soluzioni dei partner vengono automaticamente raccolti, aggregati e visualizzati come parte degli avvisi e degli eventi imprevisti del Centro sicurezza. Questi eventi vengono inoltre aggregati con i rilevamenti da altre origini per offrire una funzionalità avanzata di rilevamento delle minacce.
- **Gestione e monitoraggio dell'integrità unificati**: gli eventi di integrità consentono ai clienti di monitorare tutte le soluzioni dei partner a colpo d'occhio. La gestione di base è disponibile tramite accesso semplice a una configurazione avanzata usando la soluzione dei partner.
- **Esportare in SIEM**: i clienti possono ora esportare tutti gli avvisi del Centro sicurezza e dei partner nel formato CEF per i sistemi SIEM locali usando l'integrazione del log di Microsoft Azure (anteprima)


## <a name="what-partners-are-integrated-with-security-center"></a>Quali partner sono integrati con il Centro sicurezza?
Il Centro sicurezza attualmente si integra con i partner seguenti:

- Endpoint Protection (Trend Micro), 
- Firewall per applicazioni Web (Barracuda, F5, Imperva e presto Microsoft WAF e Fortinet), 
- Soluzioni firewall di nuova generazione (Check Point, Barracuda e presto Fortinet eCisco). 
- Soluzioni di valutazione delle vulnerabilità (Qualys - anteprima). 

Nel corso del tempo, il Centro sicurezza estenderà il numero di partner all'interno di queste categorie esistenti e aggiungerà nuove categorie. 

## <a name="how-to-deploy-a-partner-solution"></a>Come distribuire soluzioni dei partner?

In base alla configurazione dell'ambiente Azure e ai criteri di sicurezza definiti, Centro sicurezza può consigliare che venga distribuita una soluzione partner. La raccomandazione guiderà l'utente attraverso il processo di selezione e installazione di una soluzione partner. L'esperienza di distribuzione globale a questo punto può variare a seconda del partner e del tipo di soluzione. Per altre informazioni, vedere i collegamenti seguenti:

- [Aggiungere un Web Application Firewall](security-center-add-web-application-firewall.md)
- [Aggiungi un firewall di nuova generazione](security-center-add-next-generation-firewall.md)
- [Installa Endpoint Protection](security-center-install-endpoint-protection.md)
- [La valutazione della vulnerabilità non è installata](security-center-vulnerability-assessment-recommendations.md)

## <a name="how-to-manage-partner-solutions"></a>Come gestire le soluzioni partner?

Dopo la distribuzione di una soluzione partner, è possibile visualizzare le informazioni sullo stato della soluzione ed eseguire attività di gestione di base dal riquadro Soluzione partner nel dashboard principale del Centro sicurezza. Per altre informazioni sulla gestione delle soluzioni partner nel Centro sicurezza, leggere [Monitoring partner solutions with Azure Security Center](security-center-partner-solutions.md) (Monitoraggio delle soluzioni partner con il Centro sicurezza di Azure).

![Integrazione dei partner](./media/security-center-partner-integration/security-center-partner-integration-fig1-new.png)


## <a name="see-also"></a>Vedere anche
In questo documento è stato illustrato come integrare soluzioni partner nel Centro sicurezza di Azure. Per altre informazioni sul Centro sicurezza, vedere gli argomenti seguenti:

* [Guida alla pianificazione e alla gestione del Centro sicurezza di Azure](security-center-planning-and-operations-guide.md)
* [Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure](security-center-managing-and-responding-alerts.md)
* [Avvisi di sicurezza per tipo nel Centro sicurezza di Azure](security-center-alerts-type.md)
* [Monitoraggio dell'integrità della sicurezza nel Centro sicurezza di Azure](security-center-monitoring.md) : informazioni su come monitorare l'integrità delle risorse di Azure.
* [Monitoraggio delle soluzioni dei partner con il Centro sicurezza di Azure](security-center-partner-solutions.md) : informazioni su come monitorare l'integrità delle soluzioni dei partner.
* [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md) : domande frequenti sull'uso del servizio.
* [Blog sulla sicurezza di Azure](http://blogs.msdn.com/b/azuresecurity/) : post di blog sulla sicurezza e sulla conformità di Azure.

