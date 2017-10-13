---
title: Panoramica di Protezione DDoS di Azure Standard | Microsoft Docs
description: Informazioni sul servizio Protezione DDoS di Azure.
services: virtual-network
documentationcenter: na
author: kumudD
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/22/2017
ms.author: kumud
ms.openlocfilehash: 0130823b6a6f5a4883ad640c9bcefc89b82b2cd1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-ddos-protection-standard-overview"></a>Panoramica di Protezione DDoS di Azure Standard

>[!IMPORTANT]
>Protezione DDoS di Azure Standard è attualmente in versione di anteprima. Il servizio Protezione DDoS Standard è supportato da un numero limitato di risorse di Azure e solo in aree specifiche. Per abilitare Protezione DDoS Standard per la sottoscrizione, è necessario [eseguire la registrazione per il servizio](http://aka.ms/ddosprotection) durante la fase di anteprima limitata. Dopo la registrazione, si verrà contattati dal team di Protezione DDoS di Azure che fornirà supporto per il processo di abilitazione. Protezione DDoS Standard è disponibile nelle aree Stati Uniti orientali, Stati Uniti occidentali e Stati Uniti centro-occidentali. Durante la fase di anteprima non verrà addebitato alcun costo per l'uso del servizio.

Gli attacchi Distributed Denial of Service (DDoS) rappresentano una delle principali preoccupazioni in relazione alla disponibilità e alla sicurezza per i clienti che spostano le applicazioni nel cloud. Un attacco DDoS tenta di esaurire le risorse di un'applicazione, che quindi non risulta più disponibile per gli utenti legittimi. Gli attacchi DDoS possono avere come obiettivo qualsiasi endpoint raggiungibile pubblicamente tramite Internet.

Protezione DDoS di Azure, in combinazione con le procedure consigliate di progettazione delle applicazioni, offre un meccanismo di difesa contro questi attacchi. Sono disponibili due livelli del servizio: 

- **Protezione DDoS di Azure Basic**: questo livello è abilitato automaticamente come parte della piattaforma di Azure senza costi aggiuntivi. Il monitoraggio del traffico sempre attivo e la mitigazione in tempo reale degli attacchi comuni a livello di rete forniscono le stesse difese usate dai servizi online Microsoft.  È possibile usare la scalabilità completa della rete globale di Azure per distribuire e mitigare il traffico degli attacchi nelle diverse aree. 
- **Protezione DDoS di Azure Standard**: offre funzionalità aggiuntive di mitigazione ottimizzate in modo specifico per le risorse di Rete virtuale. Il servizio è semplice da abilitare e non richiede alcuna modifica alle applicazioni. I criteri di protezione sono ottimizzati tramite monitoraggio del traffico dedicato e algoritmi di apprendimento automatico e vengono applicati agli indirizzi IP pubblici associati alle risorse di Rete virtuale, ad esempio le istanze di Load Balancer, del gateway applicazione e di Service Fabric.  La telemetria in tempo reale è disponibile tramite le viste di Monitoraggio di Azure durante un attacco e come informazione cronologica. È possibile aggiungere protezioni a livello di applicazione tramite il [web application firewall del gateway applicazione](https://azure.microsoft.com/services/application-gateway/). 

![Protezione DDoS di Azure Standard](./media/ddos-protection-overview/ddos-protection-overview-fig2.png)

Per gli scenari di sviluppo e test, è possibile provare Protezione DDoS Standard e usare queste risorse per fornire commenti e suggerimenti in merito alle proprie esperienze:
- [Protezione DDoS di Azure nel forum di Microsoft Azure](https://feedback.azure.com/forums/905032-azure-ddos-protection) 
- [Protezione DDoS di Azure nel forum MSDN](https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=azureddosprotection)
- [Protezione DDoS di Azure in Stack Overflow](https://stackoverflow.com/tags/azure-ddos/info)

In caso di problemi, è possibile [aprire un ticket di supporto di Azure](../azure-supportability/how-to-create-azure-support-request.md).

## <a name="types-of-ddos-attacks-that-ddos-protection-standard-mitigates"></a>Tipi di attacchi DDoS mitigati da Protezione DDoS Standard

Protezione DDoS Standard consente di mitigare questi tipi di attacchi:

- **Attacchi volumetrici**: l'obiettivo dell'attacco è quello di intasare il livello di rete con una notevole quantità di traffico in apparenza legittimo. Sono inclusi attacchi flood UDP, attacchi flood con amplificazione e attacchi flood con pacchetti falsificati. Protezione DDoS Standard mitiga questi attacchi con dimensioni potenziali di molti gigabyte assorbendoli ed eseguendone lo scrubbing, sfruttando automaticamente la scalabilità di rete globale di Azure. 
- **Attacchi ai protocolli**: questi attacchi rendono inaccessibile una destinazione sfruttando una vulnerabilità nello stack di protocolli di livello 3 e di livello 4. Sono inclusi attacchi di tipo SYN flood, attacchi di tipo reflection e altri attacchi contro i protocolli. Protezione DDoS Standard mitiga questi attacchi distinguendo tra traffico dannoso e legittimo tramite l'interazione con il client e bloccando il traffico dannoso. 
- **Attacchi a livello di applicazione**: questi attacchi colpiscono i pacchetti di applicazioni Web per interrompere la trasmissione dei dati tra host. Sono inclusi violazioni del protocollo HTTP, attacchi SQL injection, script intersito e altri attacchi di livello 7. L'uso della soluzione WAF del gateway applicazione con Protezione DDoS Standard fornisce la protezione da questi attacchi. 

Protezione DDoS Standard protegge le risorse in una rete virtuale, inclusi gli indirizzi IP pubblici associati alle macchine virtuali, i servizi di bilanciamento del carico interno e i gateway applicazione. Se usato in combinazione con lo SKU WAF del gateway applicazione, il servizio Protezione DDoS Standard può fornire funzionalità di mitigazione complete dal livello 3 al livello 7.

## <a name="ddos-protection-standard-features"></a>Funzionalità di Protezione DDoS Standard

![Funzionalità DDoS](./media/ddos-protection-overview/ddos-overview-fig1.png)

Le funzionalità di Protezione DDoS Standard includono: 

- **Integrazione della piattaforma nativa:** Protezione DDoS Standard si integra in modo nativo in Azure e consente la configurazione tramite il portale di Azure e PowerShell. Protezione DDoS Standard comprende le risorse e la loro configurazione.
- **Monitoraggio del traffico sempre attivo:** i modelli di traffico delle applicazioni vengono monitorati 24 ore su 24, 7 giorni su 7, cercando gli indicatori di attacchi DDoS. La mitigazione viene applicata quando vengono superati i criteri di protezione.
- **Protezione pronta all'uso:** la configurazione semplificata consente di proteggere immediatamente tutte le risorse in una rete virtuale non appena il servizio Protezione DDoS Standard viene abilitato. Non sono richiesti interventi né definizioni da parte dell'utente: il servizio Protezione DDoS Standard mitiga istantaneamente e automaticamente gli attacchi non appena vengono rilevati.
- **Ottimizzazione adattiva:** la profilatura intelligente del traffico apprende il modello di traffico dell'applicazione nel tempo e seleziona e aggiorna il profilo più adatto per il servizio. Il profilo viene modificato in base ai cambiamenti del traffico nel tempo.
- **Protezione dal livello 3 al livello 7 con un gateway applicazione:** le funzionalità WAF del gateway applicazione forniscono la protezione DDoS per l'intero stack.
- **Scala di mitigazione completa:** è possibile mitigare più di 60 tipi diversi di attacchi con capacità globale, per una protezione dai più noti attacchi DDoS. 
- **Metriche degli attacchi:** tramite Monitoraggio di Azure è possibile accedere al riepilogo delle metriche per ogni attacco.
- **Avviso in caso di attacco:** è possibile configurare avvisi all'inizio e alla fine di un attacco, oltre che nel corso dell'attacco, usando le metriche integrate relative agli attacchi. Gli avvisi si integrano nel software operativo, come OMS, Splunk, Archiviazione di Azure, posta elettronica e il portale di Azure.
- **Garanzia di costo:** crediti per il servizio di scalabilità orizzontale dell'applicazione e il trasferimento dei dati per gli attacchi DDoS documentati.

## <a name="ddos-protection-standard-mitigation"></a>Mitigazione di Protezione DDoS Standard

Il servizio Protezione DDoS di Microsoft monitora l'utilizzo effettivo del traffico e lo confronta continuamente con le soglie definite nei criteri DDoS. Quando la soglia di traffico viene superata, viene avviata automaticamente la mitigazione DDoS. Quando il traffico torna sotto la soglia, la mitigazione viene rimossa.

Durante l'applicazione della mitigazione, il traffico verso la risorsa protetta viene reindirizzato dal servizio Protezione DDoS e vengono eseguiti diversi controlli. Questi controlli hanno in genere gli scopi seguenti:

- Verificare che i pacchetti siano conformi alle specifiche Internet e abbiano un formato valido.
- Interagire con il client per determinare se un pacchetto è stato potenzialmente falsificato (ad esempio usando una tecnica SYN Auth o SYN Cookie oppure eliminando un pacchetto in modo che l'origine lo trasmetta di nuovo).
- Limitare la frequenza dei pacchetti se non è possibile usare altri metodi di imposizione.

Protezione DDoS blocca il traffico degli attacchi e inoltra il traffico rimanente verso la destinazione prevista. Entro pochi minuti dal rilevamento degli attacchi, si riceve una notifica in base alle metriche di Monitoraggio di Azure. Configurando la registrazione per la telemetria di Protezione DDoS Standard, è possibile scrivere log disponibili per analisi future. I dati delle metriche di Monitoraggio di Azure per Protezione DDoS Standard vengono attualmente mantenuti per 30 giorni.

Non si consiglia ai clienti di simulare attacchi DDoS. È invece possibile usare il canale di supporto per richiedere che venga eseguita una simulazione di attacco DDoS dalla rete di Azure. Si verrà contattati da un tecnico per definire i dettagli dell'attacco DDoS (porte, protocolli, indirizzi IP di destinazione) e concordare un orario per il test.

## <a name="next-steps"></a>Passaggi successivi

- Leggere le informazioni su come gestire Protezione DDoS Standard usando [Azure PowerShell](ddos-protection-manage-ps.md) o il [portale di Azure](ddos-protection-manage-portal.md).