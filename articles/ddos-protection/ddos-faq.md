---
title: Domande frequenti sugli standard di protezione DDoS di Azure
description: Informazioni su Protezione DDoS di Azure Standard che, in combinazione con le procedure consigliate per la progettazione di applicazione, offre un meccanismo di difesa dagli attacchi DDoS.
services: virtual-network
documentationcenter: na
author: yitoh
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/28/2020
ms.author: yitoh
ms.openlocfilehash: 0873705e105710873be5d024269d40deb1c85e2b
ms.sourcegitcommit: 4bee52a3601b226cfc4e6eac71c1cb3b4b0eafe2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/11/2020
ms.locfileid: "94505428"
---
# <a name="azure-ddos-protection-standard-frequent-asked-questions"></a>Domande frequenti sugli standard di protezione DDoS di Azure

Questo articolo risponde a domande comuni su protezione DDoS di Azure standard. 

## <a name="what-is-a-distributed-denial-of-service-ddos-attack"></a>Che cos'è un attacco distribuito di tipo Denial of Service (DDoS)?
Un attacco di tipo Denial of Service, o DDoS, è un tipo di attacco in cui un utente malintenzionato invia più richieste a un'applicazione che l'applicazione è in grado di gestire. L'effetto risultante è l'esaurimento delle risorse, che influiscono sulla disponibilità e sulla capacità dell'applicazione di servire i clienti. Negli ultimi anni, il settore ha riscontrato un forte aumento degli attacchi, con attacchi che diventano più sofisticati e di dimensioni maggiori. Gli attacchi DDoS possono avere come obiettivo qualsiasi endpoint raggiungibile pubblicamente tramite Internet.

## <a name="what-is-azure-ddos-protection-standard-service"></a>Che cos'è il servizio protezione DDoS standard di Azure?
Protezione DDoS di Azure standard, in combinazione con le procedure consigliate per la progettazione di applicazioni, fornisce funzionalità avanzate di mitigazione DDoS per la difesa da attacchi DDoS. Viene ottimizzata automaticamente per aiutare a proteggere le risorse di Azure specifiche in una rete virtuale. La protezione è semplice da abilitare in qualsiasi rete virtuale nuova o esistente e non richiede alcuna modifica di applicazioni o risorse. Presenta diversi vantaggi rispetto al servizio Basic, compresa la registrazione, gli avvisi e i dati di telemetria. Vedere [Panoramica di protezione DDoS di Azure standard](ddos-protection-overview.md). 

## <a name="what-about-protection-at-the-service-layer-layer-7"></a>E per quanto riguarda la protezione a livello di servizio (livello 7)?
I clienti possono usare il servizio protezione DDoS di Azure in combinazione con lo [SKU WAF del gateway applicazione](https://docs.microsoft.com/azure/web-application-firewall/ag/ag-overview) a per la protezione a livello di rete (livello 3 e 4, offerto dal servizio di protezione DDoS di Azure) e a livello di applicazione (livello 7, offerto dallo SKU WAF del gateway applicazione).

## <a name="are-services-unsafe-in-azure-without-the-service"></a>I servizi non sono sicuri in Azure senza il servizio?
I servizi in esecuzione in Azure sono intrinsecamente protetti da protezione DDoS di Azure Basic, per proteggere l'infrastruttura di Azure. Tuttavia, la protezione che protegge l'infrastruttura ha una soglia molto più elevata rispetto alla capacità di gestione della maggior parte delle applicazioni e non fornisce dati di telemetria o avvisi, quindi mentre un volume di traffico può essere percepito come innocuo dalla piattaforma, può risultare devastante per l'applicazione che lo riceve. 

Con l'onboarding nel servizio protezione standard di Azure DDoS, l'applicazione ottiene il monitoraggio dedicato per rilevare gli attacchi e le soglie specifiche dell'applicazione. Un servizio verrà protetto con un profilo ottimizzato per il volume di traffico previsto, garantendo una difesa molto più restrittiva contro gli attacchi DDoS.

## <a name="what-are-the-supported-protected-resource-types"></a>Quali sono i tipi di risorse protette supportati?
Gli indirizzi IP pubblici nei reti virtuali basati su ARM sono attualmente l'unico tipo di risorsa protetta. I servizi PaaS (multi-tenant) non sono supportati nella presentazione. Vedere le [architetture di riferimento standard di protezione DDoS di Azure](ddos-protection-reference-architectures.md).

## <a name="are-classicrdfe-protected-resources-supported"></a>Sono supportate le risorse protette classiche/RDFE?
In anteprima sono supportate solo le risorse protette basate su ARM. Le macchine virtuali nelle distribuzioni classiche/RDFE non sono supportate. Il supporto non è attualmente pianificato per le risorse classiche/RDFE. Vedere le [architetture di riferimento standard di protezione DDoS di Azure](ddos-protection-reference-architectures.md).

## <a name="can-i-protect-my-on-premise-resources-using-ddos-protection"></a>È possibile proteggere le risorse locali usando la protezione DDoS?
Per abilitare la protezione DDoS, è necessario che gli endpoint pubblici del servizio siano associati a un VNet in Azure. Le progettazioni di esempio includono:
- Siti Web (IaaS) in Azure e database back-end nel Data Center locale. 
- Il gateway applicazione in Azure (protezione DDoS abilitata sul gateway app/WAF) e i siti Web nei data center locali.

Vedere le [architetture di riferimento standard di protezione DDoS di Azure](ddos-protection-reference-architectures.md).

## <a name="can-i-register-a-domain-outside-of-azure-and-associate-that-to-a-protected-resource-like-vm-or-elb"></a>È possibile registrare un dominio all'esterno di Azure e associarlo a una risorsa protetta, ad esempio VM o ELB?
Per gli scenari di indirizzi IP pubblici, il servizio protezione DDoS supporterà qualsiasi applicazione indipendentemente dalla posizione in cui il dominio associato è registrato o ospitato a condizione che l'indirizzo IP pubblico associato sia ospitato in Azure. 

## <a name="can-i-manually-configure-the-ddos-policy-applied-to-the-vnetspublic-ips"></a>È possibile configurare manualmente i criteri DDoS applicati agli indirizzi IP pubblici/reti virtuali?
No, la personalizzazione dei criteri non è disponibile in questo momento.

## <a name="can-i-allowlistbloclist-specific-ip-addresses"></a>È possibile consentire indirizzi IP specifici di un utente o di un blocco?
No, la configurazione manuale non è disponibile in questo momento.

## <a name="how-can-i-test-ddos-protection"></a>Come è possibile testare la protezione DDoS?
Vedere [test tramite simulazioni](test-through-simulations.md).

## <a name="how-long-does-it-take-for-the-metrics-to-load-on-portal"></a>Quanto tempo è necessario per il caricamento delle metriche nel portale?
Le metriche devono essere visibili nel portale entro 5 minuti. Se la risorsa è sotto attacco, altre metriche verranno visualizzate nel portale entro 5-7 minuti. 
    



