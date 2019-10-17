---
title: Domande frequenti sulle Network Watcher di Azure | Microsoft Docs
description: Questo articolo fornisce le risposte alle domande frequenti sul servizio Network Watcher di Azure.
services: network-watcher
documentationcenter: na
author: damendo
manager: ''
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/10/2019
ms.author: damendo
ms.openlocfilehash: ef46c1a631a79dd1c50b2bf7d263538298de233f
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72333313"
---
# <a name="frequently-asked-questions-faq-about-azure-network-watcher"></a>Domande frequenti su Azure Network Watcher
Il servizio [Azure Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) offre una suite di strumenti per monitorare, diagnosticare, visualizzare le metriche e abilitare o disabilitare i log per le risorse in una rete virtuale di Azure. Questo articolo risponde a domande comuni sul servizio.

## <a name="general"></a>Informazioni di carattere generale

### <a name="what-is-network-watcher"></a>Che cos'è Network Watcher?
Network Watcher è progettato per monitorare e ripristinare lo stato di integrità della rete dei componenti IaaS (Infrastructure-as-a-Service), che include macchine virtuali, reti virtuali, gateway applicazione, servizi di bilanciamento del carico e altre risorse in una rete virtuale di Azure. Non si tratta di una soluzione per il monitoraggio dell'infrastruttura PaaS (piattaforma distribuita come servizio) o di analisi Web/per dispositivi mobili.

### <a name="what-tools-does-network-watcher-provide"></a>Quali strumenti Network Watcher fornire?
Network Watcher offre tre principali set di funzionalità
* Monitorare
  * La [visualizzazione topologia](https://docs.microsoft.com/azure/network-watcher/view-network-topology) Mostra le risorse nella rete virtuale e le relazioni tra di esse.
  * Il [monitoraggio della connessione](https://docs.microsoft.com/azure/network-watcher/connection-monitor) consente di monitorare la connettività e la latenza tra una macchina virtuale e un'altra risorsa di rete.
  * [Monitoraggio prestazioni rete](https://docs.microsoft.com/azure/azure-monitor/insights/network-performance-monitor) consente di monitorare la connettività e le latenze tra architetture di rete ibride, circuiti Expressroute e endpoint di servizio/applicazione.  
* Diagnostica
  * La [Verifica del flusso IP](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview) consente di rilevare i problemi di filtro del traffico a livello di macchina virtuale.
  * L' [hop successivo](https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview) consente di verificare le route del traffico e di rilevare i problemi di routing.
  * La [risoluzione dei problemi di connessione](https://docs.microsoft.com/azure/network-watcher/network-watcher-connectivity-portal) consente una connettività unica e il controllo della latenza tra una macchina virtuale e un'altra risorsa di rete.
  * L' [acquisizione di pacchetti](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview) consente di acquisire tutto il traffico in una macchina virtuale nella rete virtuale.
  * La [risoluzione dei problemi relativi alla VPN](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-overview) esegue più controlli di diagnostica sui gateway VPN e sulle connessioni per facilitare il debug dei problemi.
* Registrazione
  * [NSG Flow log](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview) consente di registrare tutto il traffico nei [gruppi di sicurezza di rete (gruppi)](https://docs.microsoft.com/azure/virtual-network/security-overview)
  * [Analisi del traffico](https://docs.microsoft.com/azure/network-watcher/traffic-analytics) elabora i dati del log di flusso NSG che consentono di visualizzare, eseguire query, analizzare e comprendere il traffico di rete.


Per informazioni più dettagliate, vedere la [pagina di panoramica Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview).


### <a name="how-does-network-watcher-pricing-work"></a>Come funzionano i prezzi Network Watcher?
Visitare la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/network-watcher/) per Network Watcher componenti e i relativi prezzi.

### <a name="which-regions-is-network-watcher-available-in"></a>Quali aree sono Network Watcher disponibili in?
È possibile visualizzare l'ultima disponibilità a livello di area nella [pagina disponibilità dei servizi di Azure](https://azure.microsoft.com/global-infrastructure/services/?products=network-watcher)

## <a name="nsg-flow-logs"></a>Log di flusso NSG

### <a name="what-does-nsg-flow-logs-do"></a>Quali sono i registri dei flussi di NSG?
Le risorse di rete di Azure possono essere combinate e gestite tramite [gruppi di sicurezza di rete (gruppi)](https://docs.microsoft.com/azure/virtual-network/security-overview). I log di flusso NSG consentono di registrare le informazioni sul flusso di 5 tuple relative a tutto il traffico attraverso gruppi. I log dei flussi non elaborati vengono scritti in un account di archiviazione di Azure da cui possono essere ulteriormente elaborati, analizzati, sottoposti a query o esportati in base alle esigenze.

### <a name="are-there-caveats-for-using-nsg-flow-logs"></a>Sono presenti avvertenze per l'uso dei log di flusso NSG?
Non sono previsti prerequisiti per l'uso dei log di flusso NSG. Tuttavia, esistono due limitazioni
- **Gli endpoint di servizio non devono essere presenti in VNET**: i log dei flussi NSG vengono emessi dagli agenti nelle macchine virtuali agli account di archiviazione. Tuttavia, oggi è possibile creare solo i log direttamente negli account di archiviazione e non è possibile usare un endpoint di servizio aggiunto alla VNET.

Esistono due modi per risolvere il problema:

*Opzione 1: riconfigurare i log di flusso NSG per la generazione nell'account di archiviazione di Azure senza endpoint VNET*

* Trovare subnet con endpoint:

    - Nel portale di Azure cercare **gruppi di risorse** nel campo di ricerca globale nella parte superiore
    - Passare al gruppo di risorse contenente il gruppo di sicurezza di rete che si sta usando
    - Usare il secondo elenco a discesa per filtrare in base al tipo e selezionare **reti virtuali**
    - Fare clic sulla rete virtuale contenente gli endpoint servizio
    - Selezionare **Endpoint servizio** in **Impostazioni** nel riquadro sinistro
    - Prendere nota delle subnet in cui **Microsoft.Storage** è abilitato

* Disabilitare gli endpoint servizio:

    - Proseguendo da sopra, selezionare **Subnet** in **Impostazioni** nel riquadro sinistro
    * Fare clic sulla subnet contenente gli endpoint servizio
    - Nella sezione **Endpoint servizio**, in **Servizi**, deselezionare **Microsoft.Storage**

È possibile controllare i log di archiviazione dopo alcuni minuti. Dovrebbe essere visualizzato un TimeStamp aggiornato o un nuovo file JSON creato.

*Opzione 2: disabilitare i log di flusso NSG*

Se gli endpoint servizio di Microsoft.Storage sono necessari, occorrerà disabilitare i log dei flussi dei gruppi di sicurezza di rete.


- Gli **account di archiviazione non devono essere protetti da firewall**: a causa di limitazioni interne, è necessario che gli account di archiviazione siano accessibili tramite la rete Internet pubblica affinché i log di flusso NSG funzionino con loro. Il traffico verrà comunque instradato internamente tramite Azure e non verranno addebitati addebiti in uscita aggiuntivi.

Questo problema viene risolto abilitando "tutte le reti" ad accedere all'account di archiviazione:

* Trovare il nome dell'account di archiviazione individuando il gruppo di sicurezza di rete nella [pagina di panoramica dei log dei flussi dei gruppi di sicurezza di rete](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs)
* Passare all'account di archiviazione digitando il nome dell'account di archiviazione nella ricerca globale nel portale
* Nella sezione **IMPOSTAZIONI** selezionare **Firewall e reti virtuali**
* Selezionare **Tutte le reti** e salvare. Se l'opzione è già selezionata, non occorre modificare niente.  

Entrambe queste limitazioni dovrebbero essere risolte da Jan 2020.

### <a name="what-is-the-difference-between-flow-logs-versions-1--2"></a>Qual è la differenza tra i log dei flussi versioni 1 & 2?
Log dei flussi versione 2 introduce il concetto di *stato del flusso* & archivia le informazioni su byte e pacchetti trasmessi. [Altre informazioni](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview#log-file).

## <a name="next-steps"></a>Fasi successive
 - Passare alla [pagina Panoramica della documentazione](https://docs.microsoft.com/azure/network-watcher/) per alcune esercitazioni per iniziare a usare Network Watcher.
