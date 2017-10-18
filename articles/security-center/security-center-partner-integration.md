---
title: Integrazione di partner e soluzioni nel Centro sicurezza di Azure | Microsoft Docs
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
ms.date: 09/12/2017
ms.author: yurid
ms.openlocfilehash: a6998c997840f1a9f349b85a4274908b611cd315
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="partner-and-solutions-integration-in-azure-security-center"></a>Integrazione di partner e soluzioni nel Centro sicurezza di Azure

Questo articolo descrive l'integrazione del Centro sicurezza di Azure con i partner per una migliore sicurezza complessiva. Il Centro sicurezza di Azure offre un'esperienza integrata in Azure e sfrutta Azure Marketplace per la fatturazione e la certificazione dei partner.

## <a name="deploy-partner-solutions-from-security-center"></a>Distribuire soluzioni partner dal Centro sicurezza

Esistono quattro motivi principali per sfruttare l'integrazione dei partner nel Centro sicurezza:

- **Facilità di distribuzione**. Distribuire una soluzione partner seguendo le raccomandazioni del Centro sicurezza è molto più semplice. Il processo di distribuzione può essere completamente automatizzato usando una configurazione e una topologia di rete predefinite. In alternativa, i clienti possono scegliere un'opzione semi-automatizzata per ottenere un livello superiore di flessibilità e personalizzazione.
- **Rilevamenti integrati**. Gli eventi di sicurezza delle soluzioni partner vengono raccolti, aggregati e visualizzati automaticamente nell'ambito degli avvisi e degli eventi imprevisti del Centro sicurezza. Questi eventi vengono anche combinati con i rilevamenti di altre origini per offrire funzionalità avanzate di rilevamento delle minacce.
- **Gestione e monitoraggio dell'integrità unificati**. I clienti possono usare gli eventi di integrità integrati per monitorare in modo immediato tutte le soluzioni partner. Sono disponibili funzionalità di gestione di base, con un facile accesso alla configurazione avanzata con la soluzione partner.
- **Esportazione in SIEM**. I clienti possono esportare tutti gli avvisi in formato CEF (Common Event Format) del Centro sicurezza e dei partner in sistemi SIEM (Security Information and Event Management) locali usando l'integrazione dei log di Azure (anteprima).


## <a name="partners-that-integrate-with-security-center"></a>Partner integrati con il Centro sicurezza

Le integrazioni native di soluzioni dei partner disponibili in Azure Marketplace con il Centro sicurezza sono attualmente le seguenti.

- **Protezione endpoint**: [Trend Micro](https://help.deepsecurity.trendmicro.com/azure-marketplace-getting-started-with-deep-security.html), Symantec e [Microsoft Antimalware per Servizi cloud e Macchine virtuali di Azure](https://docs.microsoft.com/azure/security/azure-security-antimalware).
- **Web application firewall**: [Barracuda](https://www.barracuda.com/products/webapplicationfirewall), [F5](https://support.f5.com/kb/en-us/products/big-ip_asm/manuals/product/bigip-ve-web-application-firewall-microsoft-azure-12-0-0.html), [Imperva](https://www.imperva.com/Products/WebApplicationFirewall-WAF), [Fortinet](https://www.fortinet.com/resources.html?limit=10&search=&document-type=data-sheets) e [gateway applicazione di Azure](https://azure.microsoft.com/blog/azure-web-application-firewall-waf-generally-available/). 
- **Firewall di nuova generazione**: [Check Point](https://www.checkpoint.com/products/vsec-microsoft-azure/), [Barracuda](https://campus.barracuda.com/product/nextgenfirewallf/article/NGF/AzureDeployment/), [Fortinet](http://docs.fortinet.com/d/fortigate-fortios-handbook-the-complete-guide-to-fortios-5.2) e [Cisco](http://www.cisco.com/c/en/us/td/docs/security/firepower/quick_start/azure/ftdv-azure-qsg.html). 
- **Valutazione della vulnerabilità**: [Qualys](https://www.qualys.com/public-clouds/microsoft-azure/). 

Nel corso del tempo, il Centro sicurezza estenderà il numero di partner all'interno di queste categorie e aggiungerà nuove categorie. 

## <a name="deploy-a-partner-solution"></a>Distribuire una soluzione partner

In base alla configurazione dell'ambiente Azure e ai criteri di sicurezza definiti, il Centro sicurezza potrebbe raccomandare la distribuzione di una soluzione partner. La raccomandazione del Centro sicurezza descrive il processo di selezione e installazione di una soluzione partner. L'esperienza complessiva di distribuzione può variare a seconda del tipo di partner e di soluzione in uso. Per altre informazioni, vedere gli articoli seguenti:

- [Installare Endpoint Protection](security-center-install-endpoint-protection.md)
- [Aggiungere un Web Application Firewall](security-center-add-web-application-firewall.md)
- [Aggiungere un firewall di nuova generazione](security-center-add-next-generation-firewall.md)
- [La valutazione della vulnerabilità non è installata](security-center-vulnerability-assessment-recommendations.md)

## <a name="manage-partner-solutions"></a>Gestire le soluzioni partner

Dopo la distribuzione, per visualizzare informazioni sull'integrità della soluzione ed eseguire attività di gestione di base, selezionare **Soluzioni partner** nel dashboard **Centro sicurezza**.

![Integrazione di soluzioni di partner](./media/security-center-partner-integration/security-center-partner-integration-fig8.png)

Il contenuto visualizzato quando si apre Soluzioni di sicurezza potrebbe variare in base all'infrastruttura. Usando l'immagine precedente come esempio, questa pagina include tre sezioni.

- **Soluzioni connesse**: visualizza le soluzioni connesse al Centro sicurezza.
- **Soluzioni individuate**: visualizza le soluzioni non connesse al Centro sicurezza. È possibile connettere queste soluzioni, che verranno quindi visualizzate in **Soluzioni connesse**. Se il Centro sicurezza non rileva soluzioni non connesse, questa sezione è nascosta.
- **Aggiungi origini dati**: visualizza origini dati Azure e non Azure che possono essere aggiunte al Centro sicurezza.

### <a name="connected-solutions"></a>Soluzioni connesse

La sezione **Soluzioni connesse** visualizza tutte le soluzioni di sicurezza attualmente connesse con il Centro sicurezza. 

![Soluzioni connesse](./media/security-center-partner-integration/security-center-partner-integration-fig4.png)

Le informazioni visualizzate potrebbero variare in base alla soluzione. Alcune informazioni disponibili in ogni riquadro possono includere:

- **Icona aziendale del partner**. Se nel Centro sicurezza non è disponibile l'icona aziendale, vengono visualizzati i primi caratteri del nome del partner.
- **Tipo di soluzione**. Viene visualizzato il tipo di soluzione.
- **Nome computer**. Viene visualizzato il nome del computer.
- **Stato di integrità**. Se non viene inviato un indicatore di integrità, il Centro sicurezza visualizza la data e l'ora dell'ultimo evento ricevuto per indicare se l'appliance invia report o meno. Se il Centro sicurezza non riceve l'indicatore di integrità da una particolare soluzione, il riquadro della soluzione non viene visualizzato in questa sezione.

> [!NOTE]
> Il Centro sicurezza visualizza la data e l'ora dell'ultimo evento ricevuto per indicare se l'appliance invia report o meno. Le soluzioni che non inviano indicatori di integrità vengono visualizzate come connesse se sono presenti avvisi o sono stati inviati eventi negli ultimi 14 giorni.
>  

Alcune di queste soluzioni possono essere completamente integrate in Azure, altre possono essere locali. Dato che il Centro sicurezza supporta il formato [CEF](https://docs.microsoft.com/azure/operations-management-suite/oms-security-connect-products#what-is-cef), può connettersi a soluzioni che usano tale formato, ad esempio un firewall che supporta CEF. Dopo l'aggiunta della soluzione al Centro sicurezza, il firewall invia log in formato CEF al Centro sicurezza, che li inoltra ad [Azure Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview). Il firewall è una risorsa non Azure che invia eventi, ma non indicatori di integrità. L'unica informazione che il Centro sicurezza ha sull'integrità è l'ultima volta che questa appliance ha inviato un evento. Per tutte le risorse non Azure, il Centro sicurezza visualizza nell'area del riquadro relativa all'integrità la data e l'ora dell'ultimo evento ricevuto. Queste informazioni indicano che la risorsa non Azure invia ancora report.

### <a name="discovered-solutions"></a>Soluzioni individuate

La sezione **Soluzioni individuate** mostra tutte le soluzioni aggiunte tramite Azure, nonché tutte le soluzioni consigliate dal Centro sicurezza per la connessione.

![Soluzioni individuate](./media/security-center-partner-integration/security-center-partner-integration-fig5.png)

Il Centro sicurezza può essere integrato con soluzioni di Azure predefinite, ad esempio [Azure Active Directory (Azure AD) Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection). Se si ha una licenza per Azure AD Identity Protection ma la soluzione non è connessa al Centro sicurezza, Azure AD Identity Protection viene elencata in **Soluzioni individuate**. Per integrare questa soluzione con il Centro sicurezza, nel riquadro **Azure AD Identity Protection** selezionare **CONNETTI**. Verrà visualizzata la pagina seguente:

![Azure AD Identity Protection](./media/security-center-partner-integration/security-center-partner-integration-fig6.png)

Per completare la connessione di Azure AD Identity Protection, selezionare un'area di lavoro in cui salvare i dati. Tutti i dati di Azure AD Identity Protection verranno trasmessi dall'area dell'area di lavoro selezionata in questo passaggio. Usare il selettore delle aree di lavoro per selezionare l'area di lavoro e avviare il flusso dei dati.

Per connettersi al Centro sicurezza è necessario essere un amministratore globale o un amministratore della sicurezza. Se non si hanno le autorizzazioni, il pulsante **Connetti** è disabilitato. Verrà visualizzato un messaggio che ne spiega il motivo.

Gli avvisi di Azure AD Identity Protection attraversano la pipe di rilevamento del Centro sicurezza. Si ricevono quindi avvisi sia dal Centro sicurezza che da Azure AD Identity Protection. Il Centro sicurezza unisce tutti gli avvisi che sembrano pertinenti per creare un [evento imprevisto della sicurezza](https://docs.microsoft.com/azure/security-center/security-center-incident). La descrizione dell'evento imprevisto della sicurezza offre maggiori informazioni sull'attività sospetta.

### <a name="add-data-sources"></a>Aggiungere origini dati

È possibile aggiungere computer Azure e non Azure per l'integrazione con il Centro sicurezza. L'aggiunta di computer non Azure consente di aggiungere computer locali o un'appliance che supporta il formato CEF. 

![Origini dati](./media/security-center-partner-integration/security-center-partner-integration-fig7.png)


## <a name="see-also"></a>Vedere anche

In questo articolo è stato illustrato come integrare soluzioni partner nel Centro sicurezza. Per altre informazioni sul Centro sicurezza, vedere gli articoli seguenti:

* [Guida alla pianificazione e alla gestione del Centro sicurezza di Azure](security-center-planning-and-operations-guide.md)
* [Gestire e rispondere agli avvisi di sicurezza in Centro sicurezza](security-center-managing-and-responding-alerts.md)
* [Avvisi di sicurezza per tipo nel Centro sicurezza di Azure](security-center-alerts-type.md)
* [Monitoraggio dell'integrità della sicurezza nel Centro sicurezza di Azure](security-center-monitoring.md). Informazioni su come monitorare l'integrità delle risorse di Azure.
* [Monitoraggio delle soluzioni dei partner con il Centro sicurezza di Azure](security-center-partner-solutions.md). Informazioni su come monitorare lo stato integrità delle soluzioni dei partner.
* [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md). Risposte alle domande frequenti sull'uso del Centro sicurezza.
* [Blog sulla sicurezza di Azure](http://blogs.msdn.com/b/azuresecurity/). Post di blog sulla sicurezza e sulla conformità di Azure.
