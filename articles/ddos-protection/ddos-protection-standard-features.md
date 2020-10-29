---
title: Funzionalità di protezione DDoS di Azure
description: Informazioni sulle funzionalità di protezione DDoS di Azure
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: 133a27d8aef6c9df16ffcabfb4fac6c118665890
ms.sourcegitcommit: 693df7d78dfd5393a28bf1508e3e7487e2132293
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92905385"
---
# <a name="azure-ddos-protection-standard-features"></a>Funzionalità standard di protezione DDoS di Azure

Le sezioni seguenti descrivono le caratteristiche principali del servizio Protezione DDoS di Azure Standard.

## <a name="always-on-traffic-monitoring"></a>Monitoraggio del traffico always on

Protezione DDoS Standard monitora l'utilizzo effettivo del traffico e lo confronta continuamente con le soglie definite nei criteri DDoS. Quando la soglia di traffico viene superata, viene avviata automaticamente la mitigazione DDoS. Quando il traffico torna sotto la soglia, la mitigazione viene rimossa.

![Mitigazione standard di protezione DDoS di Azure](./media/ddos-protection-overview/mitigation.png)

Durante la mitigazione il traffico inviato alla risorsa protetta viene reindirizzato dal servizio Protezione DDoS e vengono eseguiti diversi controlli, ad esempio i controlli seguenti:

- Verificare che i pacchetti siano conformi alle specifiche Internet e abbiano un formato valido.
- Interagire con il client per determinare se il traffico è stato potenzialmente falsificato, ad esempio usando una tecnica SYN Auth o SYN Cookie oppure eliminando un pacchetto in modo che l'origine lo trasmetta di nuovo.
- Limitare la frequenza dei pacchetti se non è possibile usare altri metodi di imposizione.

La protezione DDoS blocca il traffico degli attacchi e inoltra il traffico rimanente alla destinazione prevista. Entro pochi minuti dal rilevamento degli attacchi, si riceve una notifica in base alle metriche di Monitoraggio di Azure. Configurando la registrazione per la telemetria di Protezione DDoS Standard, è possibile scrivere log disponibili per analisi future. I dati delle metriche di Monitoraggio di Azure per Protezione DDoS Standard vengono mantenuti per 30 giorni.

## <a name="adaptive-real-time-tuning"></a>Ottimizzazione in tempo reale adattiva

Il servizio Protezione DDoS di Azure Basic aiuta a proteggere i clienti e a prevenire gli impatti su altri clienti. Se ad esempio viene eseguito il provisioning di un servizio per un volume tipico di traffico legittimo in entrata minore della *frequenza trigger* dei criteri di Protezione DDoS per l'intera infrastruttura, potrebbe passare inosservato un attacco DDoS alle risorse di quel cliente. Più in generale, la natura complessa degli attacchi recenti (ad esempio DDoS multi-vettore), nonché i comportamenti specifici dell'applicazione dei tenant, richiedono criteri di protezione personalizzati per ogni cliente. Il servizio realizza questa personalizzazione in due modi:

- Apprendimento automatico dei modelli di traffico per ogni cliente (per ogni IP) per i livelli 3 e 4.

- Riduzione al minimo dei falsi positivi, considerando che la scalabilità di Azure consente di assorbire una quantità significativa di traffico.

![Diagramma del funzionamento di Protezione DDoS Standard, con "Policy Generation" (Generazione di criteri) cerchiato](./media/ddos-best-practices/image-5.png)

## <a name="ddos-protection-telemetry-monitoring-and-alerting"></a>Telemetria, monitoraggio e avvisi di Protezione DDoS

Protezione DDoS Standard espone dati di telemetria avanzata tramite [Monitoraggio di Azure](/azure/azure-monitor/overview) durante un attacco DDoS. È possibile configurare gli avvisi per qualsiasi metrica di Monitoraggio di Azure usata da Protezione DDoS. È possibile integrare la registrazione con Splunk (hub eventi di Azure), i log di monitoraggio di Azure e archiviazione di Azure per l'analisi avanzata tramite l'interfaccia di diagnostica di monitoraggio di Azure.

### <a name="ddos-mitigation-policies"></a>Criteri di mitigazione della protezione DDoS

Nella portale di Azure selezionare **monitoraggio**  >  **metriche** . Nel riquadro **Metriche** selezionare il gruppo di risorse, il tipo di risorsa **Indirizzo IP pubblico** e l'indirizzo IP pubblico di Azure. Le metriche DDoS sono visibili nel riquadro **Metriche disponibili** .

Protezione DDoS Standard applica tre criteri di mitigazione ottimizzati automaticamente (TCP SYN, TCP e UDP) per ogni indirizzo IP pubblico della risorsa protetta, nella rete virtuale in cui è abilitata la protezione DDoS. È possibile visualizzare le soglie dei criteri selezionando i **pacchetti in ingresso metrica per attivare la mitigazione DDoS** .

![Metriche disponibili e grafico delle metriche](./media/ddos-best-practices/image-7.png)

Le soglie dei criteri vengono configurate automaticamente tramite la profilatura del traffico di rete basata su Machine Learning. La mitigazione dei rischi DDoS ha luogo per un indirizzo IP sotto attacco solo quando viene superata la soglia dei criteri.

### <a name="metric-for-an-ip-address-under-ddos-attack"></a>Metrica per un indirizzo IP sotto attacco DDoS

Se l'indirizzo IP pubblico è sotto attacco, il valore della metrica **Under DDoS attack or not** (Sotto attacco DDoS o no) passa a 1 quando Protezione DDoS esegue la mitigazione sul traffico di attacco.

![Metrica e grafico "Under DDoS attack or not" (Sotto attacco DDoS o no)](./media/ddos-best-practices/image-8.png)

È consigliabile configurare un avviso per questa metrica. Si riceverà quindi una notifica in caso di mitigazione DDoS attiva sull'indirizzo IP pubblico.

Per altre informazioni, vedere [Gestire Protezione DDoS di Azure Standard nel portale di Azure](manage-ddos-protection.md).

## <a name="web-application-firewall-for-resource-attacks"></a>Web application firewall per attacchi alle risorse

Per gli attacchi specifici alle risorse a livello di applicazione, è necessario configurare un Web Application Firewall (WAF) per contribuire a proteggere le applicazioni Web. Un Web application firewall analizza il traffico Web in ingresso per bloccare attacchi SQL injection, cross-site scripting, DDoS e altri attacchi di livello 7. Azure fornisce un [Web application firewall (WAF) del gateway applicazione](/azure/application-gateway/application-gateway-web-application-firewall-overview) per la protezione centralizzata delle applicazioni Web da exploit e vulnerabilità comuni. In [Azure marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?search=WAF&page=1) i partner di Azure mettono a disposizione altri tipi di Web application firewall che possono essere più adatti alle proprie esigenze.

Anche i Web application firewall sono vulnerabili agli attacchi volumetrici e a esaurimento di stato. È consigliabile attivare Protezione DDoS Standard nella rete virtuale WAF per proteggere dagli attacchi volumetrici e di protocollo. Per altre informazioni, vedere la sezione [Architetture di riferimento di Protezione DDoS](ddos-protection-reference-architectures.md).

## <a name="protection-planning"></a>Pianificazione della protezione

La pianificazione e la preparazione sono essenziali per comprendere come un sistema si comporterà durante un attacco DDoS. L'elaborazione di un piano di intervento per la gestione di eventi imprevisti rientra in questa attività.

Se è disponibile Protezione DDoS Standard, assicurarsi che sia attivato nella rete virtuale degli endpoint con connessione Internet. La configurazione degli avvisi DDoS consente di monitorare costantemente eventuali attacchi all'infrastruttura. 

Monitorare le applicazioni in modo indipendente. comprendere il comportamento normale di un'applicazione. Prepararsi ad agire se l'applicazione non si comporta come previsto durante un attacco DDoS. 

Scopri in che modo i tuoi servizi rispondono a un attacco eseguendo il [test tramite simulazioni](test-through-simulations.md).

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [creare un piano di protezione DDoS](manage-ddos-protection.md).