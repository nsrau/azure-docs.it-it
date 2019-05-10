---
title: Panoramica di Protezione DDoS di Azure Standard | Microsoft Docs
description: Informazioni sul servizio Protezione DDoS di Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/13/2018
ms.author: kumud
ms.openlocfilehash: 41e9d88df49d153089e6dc7a12c5873ccc167279
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65209450"
---
# <a name="azure-ddos-protection-standard-overview"></a>Panoramica di Protezione DDoS di Azure Standard

Gli attacchi Distributed Denial of Service (DDoS) sono tra le principali preoccupazioni che riguardano la disponibilità e la sicurezza per quei clienti che spostano le loro applicazioni nel cloud. Un attacco DDoS tenta di esaurire le risorse di un'applicazione, che quindi non risulta più disponibile per gli utenti legittimi. Gli attacchi DDoS possono avere come obiettivo qualsiasi endpoint che è raggiungibile pubblicamente tramite Internet.

Protezione DDoS di Azure, insieme alle procedure consigliate di progettazione delle applicazioni, offre un meccanismo di difesa dagli attacchi DDoS. Protezione DDos di Azure offre i livelli di servizio seguenti:

- **Basic**: Viene abilitato automaticamente come parte della piattaforma Azure. Il monitoraggio del traffico sempre attivo e la mitigazione in tempo reale degli attacchi comuni a livello di rete forniscono le stesse difese usate dai Servizi online Microsoft. È possibile usare la scalabilità completa della rete globale di Azure per distribuire e mitigare il traffico degli attacchi nelle diverse aree. La protezione viene fornita per gli [indirizzi IP pubblici](virtual-network-public-ip-address.md) di Azure IPv4 e IPv6.
- **Standard**: Fornisce funzionalità aggiuntive di mitigazione tramite il livello di servizio Basic ottimizzate in modo specifico per le risorse di rete virtuale di Azure. Protezione DDoS Standard è semplice da abilitare e non richiede alcuna modifica alle applicazioni. I criteri di protezione sono ottimizzati tramite il monitoraggio del traffico dedicato e algoritmi di apprendimento automatico. I criteri vengono applicati agli indirizzi IP pubblici associati alle risorse distribuite nelle reti virtuali, ad esempio le istanze di Azure Load Balancer, del gateway applicazione di Azure e di Azure Service Fabric, ma questa protezione non si applica agli ambienti del servizio app. La telemetria in tempo reale è disponibile tramite le viste di Monitoraggio di Azure durante un attacco e come informazione cronologica. Sono disponibili funzionalità complete di analisi della mitigazione degli attacchi tramite le impostazioni di diagnostica. È possibile aggiungere protezioni a livello dell'applicazione tramite il [web application firewall del gateway applicazione di Azure](../application-gateway//application-gateway-web-application-firewall-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) o installando un firewall di terze parti da Microsoft Azure Marketplace. La protezione viene fornita per gli [indirizzi IP pubblici](virtual-network-public-ip-address.md) di Azure IPv4 e IPv6.

![Protezione DDoS di Azure Basic e Standard](./media/ddos-protection-overview/ddoscomparison.png)

## <a name="types-of-ddos-attacks-that-ddos-protection-standard-mitigates"></a>Tipi di attacchi DDoS mitigati da Protezione DDoS Standard

Protezione DDoS Standard consente di mitigare i seguenti tipi di attacchi:

- **Attacchi volumetrici**: L'obiettivo dell'attacco è quello di intasare il livello di rete con una notevole quantità di traffico in apparenza legittimo. Sono inclusi attacchi flood UDP, attacchi flood con amplificazione e attacchi flood con pacchetti falsificati. Protezione DDoS Standard mitiga questi attacchi con dimensioni potenziali di molti gigabyte assorbendoli ed eseguendone lo scrubbing, sfruttando automaticamente la scalabilità di rete globale di Azure.
- **Attacchi ai protocolli**: Questi attacchi rendono inaccessibile una destinazione, sfruttando un punto debole di livello 3 e stack di protocolli 4 di livello. Sono inclusi attacchi di tipo SYN flood, attacchi di tipo reflection e altri attacchi contro i protocolli. Protezione DDoS Standard mitiga questi attacchi distinguendo tra traffico dannoso e legittimo tramite l'interazione con il client e bloccando il traffico dannoso. 
- **Attacchi a livello di risorsa (applicazione)**: Questi attacchi colpiscono i pacchetti di applicazione web, per interrompere la trasmissione dei dati tra host. Gli attacchi includono violazioni del protocollo HTTP, attacchi SQL injection, script intersito e altri attacchi di livello 7. Usare il [web application firewall del gateway applicazione](../application-gateway/application-gateway-web-application-firewall-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) con Protezione DDoS Standard per fornire la protezione da questi attacchi. Sono disponibili anche offerte di terze parti di web application firewall nel [Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?page=1&search=web%20application%20firewall).

Protezione DDoS Standard protegge le risorse in una rete virtuale, inclusi gli indirizzi IP pubblici associati alle macchine virtuali, i bilanciamenti del carico e i gateway applicazione. Se usato in combinazione con il web application firewall del gateway applicazione firewall, il servizio Protezione DDoS Standard può fornire funzionalità di mitigazione complete dal livello 3 al livello 7.

## <a name="ddos-protection-standard-features"></a>Funzionalità di Protezione DDoS Standard

![Funzionalità DDoS](./media/ddos-protection-overview/ddosfeatures.png)

Le funzionalità di Protezione DDoS Standard includono:

- **Integrazione nella piattaforma nativa:** integrazione nativa in Azure. Include la configurazione tramite il portale di Azure. Protezione DDoS Standard comprende le risorse e la loro configurazione.
- **Protezione pronta all'uso:** la configurazione semplificata consente di proteggere immediatamente tutte le risorse in una rete virtuale non appena il servizio Protezione DDoS Standard viene abilitato. Non è necessaria alcuna definizione dell'utente o intervento. Il servizio Protezione DDoS Standard mitiga istantaneamente e automaticamente gli attacchi non appena vengono rilevati.
- **Monitoraggio del traffico sempre attivo:** i modelli di traffico delle applicazioni vengono monitorati 24 ore su 24, 7 giorni su 7, cercando gli indicatori di attacchi DDoS. La mitigazione viene applicata quando vengono superati i criteri di protezione.
- **Ottimizzazione adattiva:** Profilatura intelligente del traffico apprende il traffico dell'applicazione nel corso del tempo e seleziona e aggiorna il profilo che è più adatto per il servizio. Il profilo viene modificato in base ai cambiamenti del traffico nel tempo.
- **Protezione a più livelli:** offre protezione DDoS dello stack completo se usata con un web application firewall.
- **Scala di mitigazione completa:** è possibile mitigare più di 60 tipi diversi di attacchi con capacità globale, per una protezione dai più noti attacchi DDoS.
- **Attacco analitica:** Ottieni report dettagliati con incrementi di cinque minuti durante un attacco e un riepilogo completo al termine dell'attacco. Esegui lo streaming dei log del flusso di mitigazione in un sistema di informazioni di sicurezza e gestione degli eventi offline per il monitoraggio quasi in tempo reale durante un attacco.
- **Metriche degli attacchi:** tramite Monitoraggio di Azure è possibile accedere al riepilogo delle metriche per ogni attacco.
- **Avviso in caso di attacco:** Gli avvisi possono essere configurati all'inizio e alla fine di un attacco e failover dell'attacco, usando le metriche di attacco incorporato. Gli avvisi si integrano nel software operativo, ad esempio i log di monitoraggio di Microsoft Azure, Splunk, archiviazione di Azure, messaggio di posta elettronica e il portale di Azure.
- **Garanzia sui costi:** crediti per il servizio di trasferimento dati e scale-out dell'applicazione per attacchi DDoS documentati.

## <a name="ddos-protection-standard-mitigation"></a>Mitigazione di Protezione DDoS Standard

Protezione DDoS Standard monitora l'utilizzo effettivo del traffico e lo confronta continuamente con le soglie definite nei criteri DDoS. Quando la soglia di traffico viene superata, viene avviata automaticamente la mitigazione DDoS. Quando il traffico torna sotto la soglia, la mitigazione viene rimossa.

![Attenuazione](./media/ddos-protection-overview/mitigation.png)

Durante la mitigazione il traffico inviato alla risorsa protetta viene reindirizzato dal servizio Protezione DDoS e vengono eseguiti diversi controlli, ad esempio i controlli seguenti:

- Verificare che i pacchetti siano conformi alle specifiche Internet e abbiano un formato valido.
- Interagire con il client per determinare se il traffico è potenzialmente un falsificato (ad esempio: SYN Auth o SYN Cookie oppure eliminando un pacchetto per l'origine lo trasmetta di nuovo).
- Limitare la frequenza dei pacchetti se non è possibile usare altri metodi di imposizione.

Protezione DDoS blocca il traffico degli attacchi e inoltra il traffico rimanente verso la destinazione prevista. Entro pochi minuti dal rilevamento degli attacchi, si riceve una notifica in base alle metriche di Monitoraggio di Azure. Configurando la registrazione per la telemetria di Protezione DDoS Standard, è possibile scrivere log disponibili per analisi future. I dati delle metriche di Monitoraggio di Azure per Protezione DDoS Standard vengono mantenuti per 30 giorni.

Microsoft ha collaborato con [BreakingPoint Cloud](https://www.ixiacom.com/products/breakingpoint-cloud) per compilare un'interfaccia in cui è possibile generare il traffico per abilitare la protezione DDoS agli indirizzi IP pubblici per simulazioni. La simulazione del punto di interruzione Cloud consente di:

- Convalidare come la Protezione DDoS Standard di Microsoft Azure consente di proteggere le risorse di Azure da attacchi DDoS
- Ottimizzare il processo di risposta agli eventi imprevisti sotto attacco DDoS
- Documentare la conformità DDoS
- Eseguire il training dei team di sicurezza di rete

## <a name="next-steps"></a>Passaggi successivi

- [Configurare Protezione DDoS Standard](manage-ddos-protection.md)
