---
title: Partnership con protezione DDoS di Azure standard
description: Informazioni sulle opportunità di partner abilitate da protezione DDoS di Azure standard.
ms.service: ddos-protection
documentationcenter: na
author: yitoh
mms.devlang: na
ms.topic: how-to
ms.date: 08/28/2020
ms.author: kumud
ms.openlocfilehash: 70793b585a11da2eccb43fd6cf47053774f1e991
ms.sourcegitcommit: 693df7d78dfd5393a28bf1508e3e7487e2132293
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92905405"
---
# <a name="partnering-with-azure-ddos-protection-standard"></a>Partnership con protezione DDoS di Azure standard
Questo articolo descrive le opportunità di collaborazione abilitate dallo standard di protezione DDoS di Azure. Questo articolo è stato progettato per aiutare i responsabili dei prodotti e i ruoli di sviluppo aziendali a comprendere i percorsi di investimento e fornire informazioni sulle proposte di valore per i partner.

## <a name="background"></a>Background
Gli attacchi Distributed Denial of Service (DDoS) rappresentano una delle principali problematiche di disponibilità e sicurezza espresse dai clienti che spostano le applicazioni nel cloud. Con estorsione e hacktivism sono le motivazioni comuni dietro gli attacchi DDoS, sono state costantemente aumentate in base a tipo, scalabilità e frequenza di occorrenza poiché sono relativamente semplici e convenienti da avviare.

Protezione DDoS di Azure fornisce contromisure contro le minacce DDoS più sofisticate, sfruttando la scala globale della rete di Azure. Il servizio fornisce funzionalità avanzate di mitigazione DDoS per applicazioni e risorse distribuite in reti virtuali.

I partner tecnologici possono proteggere le risorse dei clienti in modo nativo con protezione DDoS di Azure standard per risolvere i problemi di disponibilità e affidabilità dovuti ad attacchi DDoS.

## <a name="introduction-to-azure-ddos-protection-standard"></a>Introduzione a protezione DDoS di Azure standard
Protezione DDoS di Azure standard offre funzionalità avanzate di mitigazione DDoS per gli attacchi DDoS di livello 3 e di livello 4. Di seguito sono riportate le funzionalità chiave del servizio protezione standard DDoS.

### <a name="adaptive-real-time-tuning"></a>Ottimizzazione adattiva in tempo reale
Per ogni applicazione protetta, protezione DDoS di Azure standard ottimizza automaticamente le soglie dei criteri di mitigazione DDoS in base ai modelli di profilo di traffico dell'applicazione. Il servizio realizza questa personalizzazione in due modi:

- Apprendimento automatico dei modelli di traffico per ogni cliente (per ogni IP) per i livelli 3 e 4.
- Riduzione al minimo dei falsi positivi, considerando che la scalabilità di Azure consente di assorbire una quantità significativa di traffico.

![Ottimizzazione in tempo reale adattiva](./media/ddos-protection-partner-onboarding/real-time-tuning.png)

### <a name="attack-analytics-telemetry-monitoring-and-alerting"></a>Analisi degli attacchi, telemetria, monitoraggio e avvisi
Protezione DDoS di Azure identifica e mitiga gli attacchi DDoS senza alcun intervento da parte dell'utente.

- Se la risorsa protetta si trova nella sottoscrizione coperta dal centro sicurezza di Azure, protezione DDoS standard invia automaticamente un avviso al centro sicurezza ogni volta che viene rilevato un attacco DDoS e mitigato dall'applicazione protetta.
- In alternativa, per ricevere una notifica in caso di mitigazione attiva per un indirizzo IP pubblico protetto, è possibile [configurare un avviso](telemetry-monitoring-alerting.md#configure-alerts-for-ddos-protection-metrics) sulla metrica sotto attacco DDoS.
- È anche possibile scegliere di creare avvisi per le altre metriche DDoS e [configurare l'analisi degli attacchi](telemetry-monitoring-alerting.md) per comprendere la scalabilità dell'attacco, il traffico da eliminare, i vettori di attacco, i collaboratori principali e altri dettagli.

![Metriche DDoS](./media/ddos-protection-partner-onboarding/ddos-metrics.png)

### <a name="ddos-rapid-response-drr"></a>Risposta rapida DDoS (DRR)
I clienti di protezione DDoS standard possono accedere al [team di risposta rapida](ddos-rapid-response.md) durante un attacco attivo. DRR può essere utile per l'analisi degli attacchi durante un attacco, nonché per l'analisi post-attacco.

### <a name="sla-guarantee-and-cost-protection"></a>Garanzia del contratto di sicurezza e protezione dei costi
Il servizio protezione DDoS standard è coperto da un contratto di servizio del 99,99% e la protezione dei costi fornisce crediti di risorse per la scalabilità orizzontale durante un attacco documentato. Per altre informazioni, vedere [SLA per la protezione DDoS di Azure](https://azure.microsoft.com/support/legal/sla/ddos-protection/v1_0/).

## <a name="featured-partner-scenarios"></a>Scenari con partner in primo piano
Di seguito sono riportati i vantaggi principali che è possibile derivare grazie all'integrazione con la protezione DDoS di Azure standard:

- I servizi offerti dai partner (bilanciamento del carico, web application firewall, firewall e così via) ai clienti vengono protetti automaticamente (con etichetta bianca) da protezione DDoS di Azure standard nel back-end.
- I partner hanno accesso all'analisi di attacco standard di protezione DDoS di Azure e alla telemetria che possono integrare con i propri prodotti, offrendo un'esperienza unificata per i clienti.  
- I partner hanno accesso al supporto di risposta rapida DDoS anche in assenza di risposta rapida di Azure, per problemi relativi a DDoS.
- Le applicazioni protette dei partner sono supportate da una garanzia del contratto di contratto DDoS e dalla protezione dei costi in caso di attacchi DDoS.

## <a name="technical-integration-overview"></a>Panoramica dell'integrazione tecnica
Le opportunità di collaborazione standard di protezione DDoS di Azure vengono rese disponibili tramite portale di Azure, API e CLI/PS.

### <a name="integrate-with-ddos-protection-standard"></a>Eseguire l'integrazione con protezione DDoS standard
Per configurare l'integrazione con protezione DDoS di Azure standard, è necessario eseguire i passaggi seguenti:
1. Creare un piano di protezione DDoS nella sottoscrizione desiderata (partner). Per istruzioni dettagliate, vedere [creare un piano di protezione DDoS standard](manage-ddos-protection.md#create-a-ddos-protection-plan).
   > [!NOTE]
   > È necessario creare solo 1 piano di protezione DDoS per un determinato tenant. 
2. Distribuire un servizio con l'endpoint pubblico nelle sottoscrizioni (partner), ad esempio il servizio di bilanciamento del carico, i firewall e web application firewall. 
3. Abilitare protezione DDoS di Azure standard nella rete virtuale del servizio con endpoint pubblici che usano il piano di protezione DDoS creato nel primo passaggio. Per istruzioni Stpe, vedere [Enable DDoS standard Protection Plan](manage-ddos-protection.md#enable-ddos-protection-for-an-existing-virtual-network) .
   > [!IMPORTANT] 
   > Dopo che la protezione DDoS di Azure standard è stata abilitata in una rete virtuale, tutti gli indirizzi IP pubblici all'interno della rete virtuale vengono protetti automaticamente. L'origine di questi indirizzi IP pubblici può essere all'interno di Azure (sottoscrizione client) o all'esterno di Azure. 
4. Facoltativamente, è possibile integrare la telemetria standard di protezione DDoS di Azure e l'analisi degli attacchi nel Dashboard specifico dell'applicazione. Per altre informazioni sull'uso della telemetria, vedere [visualizzare e configurare la telemetria di protezione DDoS](telemetry-monitoring-alerting.md). 

### <a name="onboarding-guides-and-technical-documentation"></a>Guide di onboarding e documentazione tecnica

- [Pagina del prodotto protezione DDoS di Azure](https://azure.microsoft.com/services/ddos-protection/)
- [Documentazione sulla protezione DDoS di Azure](ddos-protection-overview.md)
- [Informazioni di riferimento sulle API protezione DDoS di Azure](https://docs.microsoft.com/rest/api/virtualnetwork/ddosprotectionplans)
- [Informazioni di riferimento sulle API di rete virtuale di Azure](https://docs.microsoft.com/rest/api/virtualnetwork/virtualnetworks)

### <a name="get-help"></a>Ottenere aiuto

- In caso di domande sull'integrazione di applicazioni, servizi o prodotti con protezione DDoS di Azure standard, contattare la community di [sicurezza di Azure](https://techcommunity.microsoft.com/t5/security-identity/bd-p/Azure-Security).
- Segui le discussioni su [stack overflow](https://stackoverflow.com/tags/azure-ddos/).

### <a name="get-to-market"></a>Vai al mercato

- Il programma principale per la collaborazione con Microsoft è la [Microsoft Partner Network](https://partner.microsoft.com/). -Le integrazioni di sicurezza Microsoft Graph rientrano nel Track [ISV (Independent Software Vendor) di MPN](https://partner.microsoft.com/saas-solution-guide) .
- [Microsoft Intelligent Security Association](https://www.microsoft.com/security/business/intelligent-security-association?rtc=1) è il programma specifico per i partner Microsoft per la sicurezza, che consente di arricchire i prodotti per la sicurezza e migliorare l'individuabilità dei clienti delle integrazioni ai prodotti Microsoft per la sicurezza.

## <a name="next-steps"></a>Passaggi successivi
Visualizzazione delle integrazioni partner esistenti:

- [Barracuda WAF-As-a-Service](https://www.barracuda.com/waf-as-a-service)
- [Azure cloud WAF da Radware](https://www.radware.com/resources/microsoft-azure/)
