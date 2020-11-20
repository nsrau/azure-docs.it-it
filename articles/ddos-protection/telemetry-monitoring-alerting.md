---
title: Visualizzare e configurare i dati di telemetria della protezione DDoS
description: Informazioni su come visualizzare e configurare la telemetria di protezione DDoS.
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
ms.openlocfilehash: 5c80e5e611c275c2a2262963aa0759075fca836b
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94989395"
---
# <a name="view-and-configure-ddos-protection-telemetry"></a>Visualizzare e configurare i dati di telemetria della protezione DDoS

Lo standard Protezione DDoS di Azure fornisce informazioni dettagliate sugli attacchi e ne consente la visualizzazione con l'analisi degli attacchi DDoS. I clienti che proteggono le reti virtuali da attacchi DDoS hanno visibilità dettagliata sul traffico degli attacchi e sulle azioni intraprese per mitigare l'attacco tramite i report sulla mitigazione dell'attacco e i log del flusso di mitigazione. I dati di telemetria avanzati vengono esposti tramite monitoraggio di Azure, incluse le metriche dettagliate durante la durata di un attacco DDoS. Gli avvisi possono essere configurati per una delle metriche di monitoraggio di Azure esposte dalla protezione DDoS. La registrazione può essere integrata ulteriormente con [Azure Sentinel](../sentinel/connect-azure-ddos-protection.md), Splunk (hub eventi di Azure), OMS log Analytics e archiviazione di Azure per l'analisi avanzata tramite l'interfaccia di diagnostica di monitoraggio di Azure.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Configurare gli avvisi per le metriche di protezione DDoS
> * Usare la telemetria di protezione DDoS
> * Visualizzare i criteri di mitigazione della protezione DDoS
> * Visualizzare gli avvisi di protezione DDoS nel centro sicurezza di Azure

## <a name="prerequisites"></a>Prerequisiti

- Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.
- Prima di eseguire i passaggi di questa esercitazione, è necessario creare prima di tutto un [piano di protezione standard DDoS di Azure](manage-ddos-protection.md).

## <a name="configure-alerts-for-ddos-protection-metrics"></a>Configurare gli avvisi per le metriche di protezione DDoS

Usando la configurazione degli avvisi di Monitoraggio di Azure, è possibile selezionare una delle metriche di protezione DDoS disponibili per ricevere un avviso nel caso in cui ci sia una mitigazione attiva durante un attacco. Quando vengono soddisfatte le condizioni, si riceve un messaggio di posta elettronica di avviso all'indirizzo specificato:

1. Selezionare **Tutti i servizi** nella parte superiore sinistra del portale.
2. Immettere *Monitoraggio* nella casella **Filtro**. Selezionare **Monitoraggio** quando viene visualizzato nei risultati.
3. Selezionare **Metriche** in **SERVIZI CONDIVISI**.
4. Immettere o selezionare i valori personalizzati, oppure immettere i valori di esempio seguenti, accettare i risultati predefiniti restanti e quindi selezionare **OK**:

    |Impostazione                  |Valore                                                                                               |
    |---------                |---------                                                                                           |
    |Nome                     | Immettere _MyDdosAlert_.                                                                                |
    |Sottoscrizione             | Selezionare la sottoscrizione che contiene l'indirizzo IP pubblico per il quale si vogliono ricevere avvisi.        |
    |Resource group           | Selezionare il gruppo di risorse che contiene l'indirizzo IP pubblico per il quale si vogliono ricevere avvisi.      |
    |Risorsa                 | Selezionare l'indirizzo IP pubblico che contiene l'indirizzo IP pubblico per il quale si vogliono ricevere avvisi. La protezione DDoS esegue il monitoraggio degli indirizzi IP pubblici assegnati alle risorse all'interno di una rete virtuale. Se non si dispone di risorse con indirizzi IP pubblici nella rete virtuale, è innanzitutto necessario creare una risorsa con un indirizzo IP pubblico. È possibile eseguire il monitoraggio dell'indirizzo IP pubblico di tutte le risorse distribuite tramite Resource Manager (distribuzione classica) elencate in [Rete virtuale per servizi di Azure](../virtual-network/virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network), ad eccezione degli ambienti di Servizio app di Azure e del gateway VPN di Azure. Per continuare questa esercitazione, è possibile creare rapidamente una macchina virtuale [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) o [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json).                   |
    |Metrica                   | Selezionare **sotto DDoS Attack (attacco DDoS**).                                                                |
    |Soglia                | 1 - **1** indica che è in corso un attacco **0** indica che non è in corso un attacco                         |
    |Periodo                   | Selezionare un valore di propria scelta                                                                   |
    |Notifica tramite posta elettronica         | Selezionare la casella di controllo.                                                                                 |
    |Amministratore aggiuntivo | Immettere l'indirizzo di posta elettronica se non si è proprietario, collaboratore o lettore di posta elettronica per la sottoscrizione |

    Entro pochi minuti dal rilevamento dell'attacco, si riceve un messaggio di posta elettronica dalle metriche di Monitoraggio di Azure simile a quello dell'immagine seguente:

    ![Avviso di attacco](./media/manage-ddos-protection/ddos-alert.png)


Per simulare un attacco DDoS per convalidare l'avviso, vedere [Convalidare il rilevamento della protezione DDoS](test-through-simulations.md).

È anche possibile leggere altre informazioni relative alla [configurazione dei webhook](../azure-monitor/platform/alerts-webhooks.md?toc=%2fazure%2fvirtual-network%2ftoc.json) e alle [app per la logica](../logic-apps/logic-apps-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) per la creazione di avvisi.

## <a name="use-ddos-protection-telemetry"></a>Usare la telemetria di protezione DDoS

I dati di telemetria per un attacco vengono forniti da Monitoraggio di Azure in tempo reale. La telemetria è disponibile solo per l'intervallo di tempo durante cui viene applicata la mitigazione per un indirizzo IP pubblico. Prima o dopo la mitigazione di un attacco, non vengono visualizzati dati di telemetria.

1. Selezionare **Tutti i servizi** nella parte superiore sinistra del portale.
2. Immettere *Monitoraggio* nella casella **Filtro**. Selezionare **Monitoraggio** quando viene visualizzato nei risultati.
3. Selezionare **metriche** in **servizi condivisi**.
4. Selezionare la **sottoscrizione** e il **gruppo di risorse** contenenti l'indirizzo IP pubblico per il quale si vogliono ottenere i dati di telemetria.
5. Selezionare **Indirizzo IP pubblico** per **Tipo di risorsa**, quindi selezionare l'indirizzo IP pubblico specifico per il quale si vogliono ottenere i dati di telemetria.
6. A sinistra dello schermo viene visualizzata una serie di **Metriche disponibili**. Queste metriche, quando selezionate, vengono rappresentate nel **grafo delle metriche di Monitoraggio di Azure** nella schermata di panoramica.
7. Selezionare il tipo di **aggregazione** come **Max**

I nomi delle metriche si riferiscono a diversi tipi di pacchetto e byte rispetto ai pacchetti, con una struttura di base correlata ai nomi di tag per ogni metrica, come indicato di seguito:

- **Dropped tag name** (Nome tag eliminato), ad esempio **Inbound Packets Dropped DDoS** (Pacchetti in ingresso eliminati da DDoS): numero di pacchetti eliminati/sottoposti a scrubbing dal sistema di protezione DDoS.
- **Forwarded tag name** (Nome tag inoltrato), ad esempio **Inbound Packets Forwarded DDoS** (Pacchetti in ingresso inoltrati da DDoS): numero di pacchetti inoltrati dal sistema DDoS all'indirizzo VIP di destinazione (il traffico non è stato filtrato).
- **No tag name** (Nessun nome di tag), ad esempio **Inbound Packets DDoS** (Pacchetti in ingresso elaborati da DDoS): numero totale di pacchetti arrivati nel sistema di scrubbing, che rappresenta la somma dei pacchetti eliminati e di quelli inoltrati.

Questa [regola di avviso di monitoraggio di Azure](https://github.com/Azure/Azure-Network-Security/tree/master/Azure%20DDoS%20Protection/Azure%20Monitor%20Alert%20-%20DDoS%20Mitigation%20Started) consente di eseguire una query semplice per rilevare quando si verifica una mitigazione DDoS attiva. Per simulare un attacco DDoS per convalidare la telemetria, vedere [Convalidare il rilevamento della protezione DDoS](test-through-simulations.md). 

## <a name="view-ddos-mitigation-policies"></a>Visualizzare i criteri di mitigazione della protezione DDoS

La protezione DDoS standard applica tre criteri di mitigazione ottimizzati automaticamente (TCP SYN, TCP e UDP) per ogni indirizzo IP pubblico della risorsa protetta, nella rete virtuale in cui è abilitata la protezione DDoS. È possibile visualizzare le soglie dei criteri selezionando i  **pacchetti TCP in ingresso per attivare la mitigazione DDoS** e i **pacchetti UDP in ingresso per attivare le metriche di mitigazione DDoS** con tipo di **aggregazione** come "Max", come illustrato nell'immagine seguente:

![Visualizzare i criteri di mitigazione](./media/manage-ddos-protection/view-mitigation-policies.png)

Le soglie dei criteri vengono configurate automaticamente tramite la profilatura del traffico di rete basato sull'apprendimento automatico di Azure. La mitigazione DDoS si attiva per l'indirizzo IP sotto attacco solo quando viene superata la soglia dei criteri.

## <a name="view-ddos-protection-alerts-in-azure-security-center"></a>Visualizzare gli avvisi di protezione DDoS nel centro sicurezza di Azure

Il Centro sicurezza di Azure fornisce un elenco di [avvisi di sicurezza](../security-center/security-center-managing-and-responding-alerts.md), con informazioni che consentono di analizzare e correggere i problemi. Questa funzionalità consente di ottenere una visualizzazione unificata degli avvisi, inclusi gli avvisi relativi agli attacchi DDoS e le azioni intraprese per attenuare l'attacco in tempi più prossimi.
Esistono due avvisi specifici che verranno visualizzati per qualsiasi rilevamento e mitigazione degli attacchi DDoS:

- **Attacco DDoS rilevato per l'indirizzo IP pubblico**: questo avviso viene generato quando il servizio protezione DDoS rileva che uno degli indirizzi IP pubblici è la destinazione di un attacco DDoS.
- **Attacco DDoS mitigato per IP pubblico**: questo avviso viene generato quando è stato mitigato un attacco all'indirizzo IP pubblico.
Per visualizzare gli avvisi, aprire il **Centro sicurezza** nel portale di Azure. In **protezione dalle minacce** selezionare **avvisi di sicurezza**. Lo screenshot seguente mostra un esempio degli avvisi di attacco DDoS.

![Avviso DDoS nel centro sicurezza di Azure](./media/manage-ddos-protection/ddos-alert-asc.png)

Gli avvisi includono informazioni generali sull'indirizzo IP pubblico che si trova sotto attacco, informazioni sull'Intelligence per le minacce e sulla Geo e procedure correttive.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state illustrate le procedure per:

- Configurare gli avvisi per le metriche di protezione DDoS
- Usare la telemetria di protezione DDoS
- Visualizzare i criteri di mitigazione della protezione DDoS
- Visualizzare gli avvisi di protezione DDoS nel centro sicurezza di Azure

Per informazioni su come configurare i report di mitigazione degli attacchi e i log di flusso, continuare con l'esercitazione successiva.

> [!div class="nextstepaction"]
> [Configurare i report e i log dei flussi per la mitigazione degli attacchi DDoS](reports-and-flow-logs.md)