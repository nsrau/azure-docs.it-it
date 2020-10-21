---
title: Guida di orientamento alla preparazione del Centro sicurezza di Azure | Microsoft Docs
description: Questo documento fornisce una guida di orientamento alla preparazione per iniziare a usare il Centro sicurezza di Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: fece670cc-df70-445d-9773-b32cbaba8d4a
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/03/2018
ms.author: memildin
ms.openlocfilehash: 38e1fc7f062908f8c4a4b9aa903ca09dd7fb9ea5
ms.sourcegitcommit: f88074c00f13bcb52eaa5416c61adc1259826ce7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92342332"
---
# <a name="azure-security-center-readiness-roadmap"></a>Guida di orientamento alla preparazione del Centro sicurezza di Azure
Questo documento fornisce una guida di orientamento alla preparazione utile per iniziare a usare il Centro sicurezza di Azure.

## <a name="understanding-security-center"></a>Informazioni sul Centro sicurezza
Il Centro sicurezza di Azure offre la gestione unificata della sicurezza e la protezione avanzata dalle minacce per carichi di lavoro in esecuzione in Azure, in locale e in altri cloud. 

Usare le risorse seguenti per iniziare a usare il Centro sicurezza.

Articoli
- [Introduzione al Centro sicurezza di Azure](security-center-introduction.md)
- [Guida introduttiva per il Centro sicurezza di Azure](security-center-get-started.md)

Video
- [Breve video introduttivo](https://azure.microsoft.com/resources/videos/introduction-to-azure-security-center/)
- [Overview of Security Center Prevention, Detection and Response Capabilities](https://azure.microsoft.com/resources/videos/azurecon-2015-new-azure-security-center-helps-you-prevent-detect-and-respond-to-threats/) (Panoramica delle funzionalità di prevenzione, rilevamento e risposta del Centro sicurezza)

## <a name="planning-and-operations"></a>Pianificazione e gestione

Per sfruttare tutti i vantaggi del Centro sicurezza, è importante sapere come i vari utenti o team dell'organizzazione usano il servizio per soddisfare le esigenze di gestione, monitoraggio, governance e risposta agli eventi imprevisti in modo sicuro.

Usare le risorse seguenti per semplificare i processi di pianificazione e gestione.

- [Guida alla pianificazione e alla gestione del Centro sicurezza di Azure](security-center-planning-and-operations-guide.md)


### <a name="onboarding-computers-to-security-center"></a>Onboarding di computer nel Centro sicurezza
Il Centro sicurezza rileva automaticamente le sottoscrizioni o le aree di lavoro di Azure non protette da Azure Defender. Sono incluse le sottoscrizioni di Azure che usano il Centro sicurezza gratuito e le aree di lavoro in cui non è abilitata la soluzione di sicurezza.

Usare le risorse seguenti per semplificare i processi di onboarding.

- [Eseguire il caricamento di computer non Azure](quickstart-onboard-machines.md)
- [Azure Security Center Hybrid - Overview](https://youtu.be/NMa4L_M597k) (Centro sicurezza di Azure ibrido - Panoramica)

## <a name="mitigating-security-issues-using-security-center"></a>Riduzione dei problemi di sicurezza tramite il Centro sicurezza
Il Centro sicurezza raccoglie, analizza e integra automaticamente i dati di log delle risorse di Azure, della rete e delle soluzioni dei partner connesse, come soluzioni di protezione endpoint e firewall, per rilevare le minacce reali e ridurre i falsi positivi.

Usare le risorse seguenti per semplificare la gestione degli avvisi di sicurezza e la protezione delle risorse.

Articoli    
- [Monitoraggio dell'integrità della sicurezza nel Centro sicurezza di Azure](./security-center-monitoring.md)
- [Protezione della rete nel Centro sicurezza di Azure](./security-center-network-recommendations.md)
- [Protezione del servizio SQL di Azure e dei dati nel Centro sicurezza di Azure](./security-center-remediate-recommendations.md)


Video    
- [Mitigating Security Issues using Azure Security Center](https://channel9.msdn.com/Blogs/Azure-Security-Videos/Mitigating-Security-Issues-using-Azure-Security-Center) (Riduzione dei problemi di sicurezza tramite il Centro sicurezza di Azure)

### <a name="security-center-for-incident-response"></a>Centro sicurezza per la risposta agli eventi imprevisti
Per ridurre i costi e i danni è importante implementare un piano di risposta agli eventi imprevisti prima che si verifichi un attacco. Centro sicurezza di Azure può essere usato nelle diverse fasi della risposta agli eventi imprevisti.

Usare le risorse seguenti per ottenere informazioni su come incorporare il Centro sicurezza nel processo di risposta agli eventi imprevisti.

Video    
* [Azure Security Center in Incident Response](https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Security-Center-in-Incident-Response) (Centro sicurezza di Azure per la risposta agli eventi imprevisti)
* [Respond quickly to threats with next-generation security operation, and investigation](https://youtu.be/e8iFCz5RM4g) (Rispondere rapidamente alle minacce con operazioni e indagini di sicurezza di nuova generazione)

Articoli    
* [Uso del Centro sicurezza di Azure per rispondere a un evento imprevisto](./tutorial-security-incident.md)
* [Automatizzare le risposte con l'automazione del flusso di lavoro](workflow-automation.md)

## <a name="advanced-cloud-defense"></a>Difesa cloud avanzata

Le macchine virtuali di Azure possono sfruttare i vantaggi delle funzionalità di difesa cloud avanzata del Centro sicurezza. Queste funzionalità includono l'accesso just-in-time alle macchine virtuali e i controlli applicazioni adattivi.

Usare le risorse seguenti per ottenere informazioni sull'uso di queste funzionalità nel Centro sicurezza.

Video    
* [Centro sicurezza di Azure - Accesso JIT alle macchine virtuali](https://youtu.be/UOQb2FcdQnU)
* [Azure Security Center - Adaptive Application Controls](https://youtu.be/wWWekI1Y9ck) (Centro sicurezza di Azure - Controlli delle applicazioni adattivi)

Articoli    
* [Gestire l'accesso alle macchine virtuali con la funzionalità just-in-time](./security-center-just-in-time.md)
* [Controlli delle applicazioni adattivi nel Centro sicurezza di Azure](./security-center-adaptive-application.md)

## <a name="hands-on-activities"></a>Attività pratiche

* [Security Center hands-on lab](https://www.microsoft.com/handsonlabs/SelfPacedLabs/?storyGuid=78871abf-6f35-4aa0-840f-d801f5cdbd72) (Laboratorio pratico sul Centro sicurezza)
* [Web Application Firewall (WAF) recommendation playbook in Security Center](https://gallery.technet.microsoft.com/ASC-Playbook-Protect-38bd47ff) (Playbook di consigli per web application firewall nel Centro sicurezza)
* [Azure Security Center Playbook: Security Alerts](https://gallery.technet.microsoft.com/Azure-Security-Center-f621a046) (Playbook del Centro sicurezza di Azure: Avvisi di sicurezza)

## <a name="additional-resources"></a>Risorse aggiuntive
* [Pagina di documentazione del Centro sicurezza](./index.yml)
* [Pagina di documentazione dell'API REST del Centro sicurezza](/previous-versions/azure/reference/mt704034(v=azure.100))
* [Domande frequenti sul Centro sicurezza di Azure](./faq-general.md)
* [Pagina dei prezzi del Centro sicurezza](https://azure.microsoft.com/pricing/details/security-center/)
* [Procedure consigliate per la sicurezza delle identità](../security/fundamentals/identity-management-best-practices.md)
* [Procedure consigliate per la sicurezza di rete](../security/fundamentals/network-best-practices.md)
* [Consigli relativi a PaaS](../security/fundamentals/paas-deployments.md)
* [Conformità](https://www.microsoft.com/trustcenter/compliance/due-diligence-checklist)
* [I clienti di Log Analytics possono ora usare il Centro sicurezza di Azure per proteggere i propri carichi di lavoro cloud ibridi](/archive/blogs/msoms/oms-customers-can-now-use-azure-security-center-to-protect-their-hybrid-cloud-workloads)

## <a name="community-resources"></a>Risorse della community

* [UserVoice per il Centro sicurezza](https://feedback.azure.com/forums/347535-azure-security-center)
* [D&una pagina per il Centro sicurezza](/answers/topics/azure-security-center.html)