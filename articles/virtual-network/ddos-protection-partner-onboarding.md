---
title: Collaborazione con Azure DDoS Protection Standard
description: Comprendere le opportunità di partnership abilitate da Azure DDoS Protection Standard.Understand partnering opportunities enabled by Azure DDoS Protection Standard.
ms.service: virtual-network
documentationcenter: na
author: anupamvi
mms.devlang: na
ms.topic: article
ms.date: 01/28/2020
ms.author: kumud
ms.openlocfilehash: 39cb2f90b49725c78746df69505fad7160e1db92
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76849682"
---
# <a name="partnering-with-azure-ddos-protection-standard"></a>Collaborazione con Azure DDoS Protection Standard
Questo articolo descrive le opportunità di partnership abilitate dallo standard di protezione DDoS di Azure.This article describes partnering opportunities enabled by the Azure DDoS Protection Standard. Questo articolo è progettato per aiutare i responsabili di prodotto e i ruoli di sviluppo aziendale a comprendere i percorsi di investimento e fornire informazioni dettagliate sulle proposte di valore di partnership.

## <a name="background"></a>Background
Gli attacchi DDoS (Distributed Denial of Service) sono uno dei principali problemi di disponibilità e sicurezza espressi dai clienti che spostano le applicazioni nel cloud. Con estorsione e hacktivism sono le motivazioni comuni dietro gli attacchi DDoS, sono stati costantemente in aumento in tipo, scala, e la frequenza di occorrenza in quanto sono relativamente facile ed economico da lanciare.

Azure DDoS Protection fornisce contromisure contro le minacce DDoS più sofisticate, sfruttando la scala globale della rete di Azure.Azure DDoS Protection provides countermeasures against the most sophisticated DDoS threats, leveraging the global scale of Azure networking. Il servizio fornisce funzionalità di attenuazione DDoS avanzate per le applicazioni e le risorse distribuite nelle reti virtuali.

I partner tecnologici possono proteggere le risorse dei clienti in modo nativo con Azure DDoS Protection Standard per risolvere i problemi di disponibilità e affidabilità dovuti ad attacchi DDoS.

## <a name="introduction-to-azure-ddos-protection-standard"></a>Introduzione allo standard di protezione DDoS di AzureIntroduction to Azure DDoS Protection Standard
Azure DDoS Protection Standard offre funzionalità di attenuazione DDoS avanzate rispetto agli attacchi DDoS di livello 3 e livello 4. Di seguito sono riportate le funzionalità principali del servizio DDoS Protection Standard.

### <a name="adaptive-real-time-tuning"></a>Ottimizzazione adattiva in tempo reale
Per ogni applicazione protetta, Azure DDoS Protection Standard sintonizza automaticamente le soglie dei criteri di attenuazione DDoS in base ai modelli di profilo di traffico dell'applicazione. Il servizio realizza questa personalizzazione in due modi:

- Apprendimento automatico dei modelli di traffico per ogni cliente (per ogni IP) per i livelli 3 e 4.
- Riduzione al minimo dei falsi positivi, considerando che la scalabilità di Azure consente di assorbire una quantità significativa di traffico.

![Ottimizzazione adattiva in tempo reale](./media/ddos-protection-partner-onboarding/real-time-tuning.png)

### <a name="attack-analytics-telemetry-monitoring-and-alerting"></a>Analisi degli attacchi, telemetria, monitoraggio e avvisi
Azure DDoS Protection identifica e attenua gli attacchi DDoS senza alcun intervento da parte dell'utente.

- Se la risorsa protetta si trova nella sottoscrizione coperta dal Centro sicurezza di Azure, DDoS Protection Standard invia automaticamente un avviso al Centro sicurezza ogni volta che viene rilevato e attenuato un attacco DDoS rispetto all'applicazione protetta.
- In alternativa, per ricevere una notifica quando è presente un'attenuazione attiva per un indirizzo IP pubblico protetto, è possibile [configurare o](manage-ddos-protection.md#configure-alerts-for-ddos-protection-metrics) meno un avviso sulla metrica In caso di attacco DDoS.
- È inoltre possibile scegliere di creare avvisi per le altre metriche DDoS e [configurare l'analisi](manage-ddos-protection.md#configure-ddos-attack-analytics) degli attacchi per comprendere la portata dell'attacco, il traffico in fase di eliminazione, i vettori di attacco, i collaboratori principali e altri dettagli.

![Metriche DDoS](./media/ddos-protection-partner-onboarding/ddos-metrics.png)

### <a name="ddos-rapid-response-drr"></a>Risposta rapida DDoS (DRR)
I clienti DDoS Protection Standard hanno accesso al [team di Rapid Response](https://azure.microsoft.com/blog/ddos-protection-attack-analytics-rapid-response/) durante un attacco attivo. DRR può aiutare con l'indagine di attacco, le attenuazioni personalizzate durante un attacco e l'analisi post-attacco.

### <a name="sla-guarantee-and-cost-protection"></a>Garanzia SLA e protezione dei costi
Il servizio DDoS Protection Standard è coperto da un contratto di servizio del 99,99% e la protezione dei costi fornisce crediti di risorse per la scalabilità orizzontale durante un attacco documentato. Per altre informazioni, vedere Servizio di archiviazione per Azure DDoS Protection.For more information, see [SLA for Azure DDoS Protection](https://azure.microsoft.com/support/legal/sla/ddos-protection/v1_0/).

## <a name="featured-partner-scenarios"></a>Scenari partner in primo piano
The following are key benefits you can derive by integrating with the Azure DDoS Protection Standard:

- I servizi offerti dai partner (bilanciamento del carico, firewall delle applicazioni Web, firewall e così via) ai clienti vengono automaticamente protetti (etichettato in bianco) da Azure DDoS Protection Standard nel back-end.
- I partner hanno accesso all'analisi degli attacchi e ai dati di telemetria di Azure DDoS Protection Standard che possono integrare con i propri prodotti, offrendo un'esperienza cliente unificata.  
- I partner hanno accesso al supporto di risposta rapida DDoS anche in assenza di risposta rapida di Azure, per problemi correlati agli dDoS.
- Le applicazioni protette dei partner sono supportate da una garanzia DDoS SLA e la protezione dei costi in caso di attacchi DDoS.

## <a name="technical-integration-overview"></a>Panoramica dell'integrazione tecnica
Le opportunità di partnership standard di Azure DDoS Protection vengono rese disponibili tramite il portale di Azure, le API e l'interfaccia della riga di comando/PS.

### <a name="integrate-with-ddos-protection-standard"></a>Integrazione con lo standard di protezione DDoS
I passaggi seguenti sono necessari per i partner per configurare l'integrazione con Azure DDoS Protection Standard:The following steps are required for partners to configure integration with Azure DDoS Protection Standard:
1. Creare un piano di protezione DDoS nella sottoscrizione desiderata (partner). Per istruzioni dettagliate, vedere [Creare un piano di protezione standard DDoS](manage-ddos-protection.md#create-a-ddos-protection-plan).
   > [!NOTE]
   > Per un determinato tenant è necessario creare solo 1 piano di protezione DDoS. 
2. Distribuire un servizio con endpoint pubblico nelle sottoscrizioni (partner), ad esempio bilanciamento del carico, firewall e firewall applicazione Web.Deploy a service with public endpoint in your (partner) subscriptions, such as load balancer, firewalls, and web application firewall. 
3. Abilitare Azure DDoS Protection Standard nella rete virtuale del servizio con endpoint pubblici tramite il piano di protezione DDoS creato nel primo passaggio. Per istruzioni stpe-by-step, vedere [Abilitare il piano di protezione standard DDoS](manage-ddos-protection.md#enable-ddos-for-an-existing-virtual-network)
   > [!IMPORTANT] 
   > Dopo aver abilitato Lo standard di protezione DDoS di Azure in una rete virtuale, tutti gli indirizzi IP pubblici all'interno di tale rete virtuale vengono protetti automaticamente. L'origine di questi indirizzi IP pubblici può essere all'interno di Azure (sottoscrizione client) o all'esterno di Azure.The origin of these public IPs can be either within Azure (client subscription) or outside of Azure. 
4. Facoltativamente, integrare la telemetria standard di Azure DDoS Protection e attaccare l'analisi nel dashboard rivolto al cliente specifico dell'applicazione. Per altre informazioni sull'uso dei dati di telemetria, vedere Usare i dati di telemetria di protezione DDoS.For more information about using telemetry, see [Use DDoS Protection telemetry](manage-ddos-protection.md#use-ddos-protection-telemetry). Per altre informazioni sulla configurazione dell'analisi degli attacchi, vedere [Configurare l'analisi degli attacchi DDoSFor](manage-ddos-protection.md#configure-ddos-attack-analytics) more information about configuring attack analytics, see Configure DDoS attack analytics

### <a name="onboarding-guides-and-technical-documentation"></a>Guide all'onboarding e documentazione tecnica

- [Pagina del prodotto Azure DDoS Protection](https://azure.microsoft.com/services/ddos-protection/)
- [Documentazione di Azure DDoS ProtectionAzure DDoS Protection documentation](ddos-protection-overview.md)
- [Informazioni di riferimento sulle API di protezione DDoS di AzureAzure DDoS Protection API reference](https://docs.microsoft.com/rest/api/virtualnetwork/ddosprotectionplans)
- [Informazioni di riferimento sulle API di rete virtuale di AzureAzure virtual network API reference](https://docs.microsoft.com/rest/api/virtualnetwork/virtualnetworks)

### <a name="get-help"></a>Ottenere aiuto

- In caso di domande sulle integrazioni di applicazioni, servizi o prodotti con Azure DDoS Protection Standard, contattare la community di [sicurezza di Azure.](https://techcommunity.microsoft.com/t5/security-identity/bd-p/Azure-Security)
- Seguire le discussioni [sull'overflow dello stack](https://stackoverflow.com/tags/azure-ddos/).

### <a name="get-to-market"></a>Entra sul mercato

- Il programma principale per la collaborazione con Microsoft è [Microsoft Partner Network](https://partner.microsoft.com/). – Le integrazioni di Microsoft Graph Security rientrano nella traccia [MPN Independent Software Vendor (ISV).](https://partner.microsoft.com/saas-solution-guide)
- [Microsoft Intelligent Security Association](https://www.microsoft.com/security/business/intelligent-security-association?rtc=1) è il programma specifico per i partner microsoft di sicurezza per arricchire i prodotti di sicurezza e migliorare la rilevabilità dei clienti delle integrazioni con i prodotti Microsoft Security.

## <a name="next-steps"></a>Passaggi successivi
Visualizzare le integrazioni partner esistenti:

- [Barracuda WAF-as-a-service](https://www.barracuda.com/waf-as-a-service)
- [WAF cloud di Azure da Radware](https://www.radware.com/resources/microsoft-azure/)
