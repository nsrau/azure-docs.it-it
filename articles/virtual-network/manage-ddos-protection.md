---
title: Gestire Protezione DDoS di Azure Standard nel portale di Azure | Microsoft Docs
description: Informazioni su come usare la telemetria di Protezione DDoS di Azure Standard in Monitoraggio di Azure per mitigare un attacco.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/06/2018
ms.author: jdial
ms.openlocfilehash: 39525c7e25e247504429ea36210986a5e3300cc1
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/17/2018
ms.locfileid: "53537174"
---
# <a name="manage-azure-ddos-protection-standard-using-the-azure-portal"></a>Gestire Protezione DDoS di Azure Standard nel portale di Azure

Informazioni su come abilitare e disabilitare la protezione dall'attacco Distributed Denial of Service (DDoS) e usare la telemetria per mitigare un attacco DDoS con Protezione DDoS Standard di Azure. Protezione DDoS Standard protegge le risorse di Azure, come le macchine virtuali, i bilanciamenti del carico e i gateway applicazione a cui è stato assegnato un [indirizzo IP pubblico](virtual-network-public-ip-address.md) di Azure. Per altre informazioni su Protezione DDoS Standard e sulle relative funzionalità, vedere [Panoramica di Protezione DDoS Standard di Azure](ddos-protection-overview.md).

Prima di completare qualsiasi passaggio in questa esercitazione, accedere al portale di Azure all'indirizzo https://portal.azure.com con un account assegnato al ruolo [Collaboratore Rete](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) o a un [ruolo personalizzato](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) a cui sono assegnate le operazioni appropriate elencate nelle [Autorizzazioni](#permissions).

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="create-a-ddos-protection-plan"></a>Creare un piano di protezione DDoS

Un piano di protezione DDoS definisce un set di reti virtuali in cui è abilitata la protezione DDoS standard per le sottoscrizioni. È possibile configurare un piano di protezione DDoS standard per l'organizzazione e collegare reti virtuali da più sottoscrizioni allo stesso piano. Il piano di protezione DDoS è a sua volta associato a una sottoscrizione che viene selezionata durante la creazione del piano. Alla sottoscrizione a cui è associato il piano viene addebitato il costo mensile relativo al piano, nonché i costi in eccedenza, nel caso in cui gli indirizzi IP pubblici protetti siano più di 100. Per altre informazioni sui prezzi di DDoS, vedere i [dettagli sui prezzi](https://azure.microsoft.com/pricing/details/ddos-protection/).

Per la maggior parte delle organizzazioni, non è necessaria la creazione di più piani. Non è possibile spostare un piano tra le sottoscrizioni. Se si vuole modificare la sottoscrizione di un piano, è necessario [eliminare il piano esistente](#work-with-ddos-protection-plans) e crearne uno nuovo.

1. Selezionare **Crea una risorsa** nell'angolo in alto a sinistra del portale di Azure.
2. Cercare *DDoS*. Selezionare **DDoS protection plan** (Piano di protezione DDoS) quando viene visualizzato nei risultati della ricerca.
3. Selezionare **Create**.
4. Immettere o selezionare i valori personalizzati, oppure immettere o selezionare i valori di esempio seguenti, quindi selezionare **Crea**:

    |Impostazione        |Valore                                              |
    |---------      |---------                                          |
    |NOME           | myDdosProtectionPlan                              |
    |Sottoscrizione   | Selezionare la propria sottoscrizione.                         |
    |Gruppo di risorse | Selezionare **Crea nuovo** e immettere *myResourceGroup* |
    |Località       | Stati Uniti orientali                                           |

## <a name="enable-ddos-for-a-new-virtual-network"></a>Abilitare DDoS per una nuova rete virtuale

1. Selezionare **Crea una risorsa** nell'angolo in alto a sinistra del portale di Azure.
2. Selezionare **Rete** e quindi **Rete virtuale**.
3. Immettere o selezionare i valori personalizzati, oppure immettere o selezionare i valori di esempio seguenti, accettare i risultati predefiniti restanti e quindi selezionare **Crea**:

    | Impostazione         | Valore                                                        |
    | ---------       | ---------                                                    |
    | Nome            | myVirtualNetwork                                             |
    | Sottoscrizione    | Selezionare la propria sottoscrizione.                                    |
    | Gruppo di risorse  | Selezionare **Usa esistente** e quindi **myResourceGroup** |
    | Località        | Stati Uniti orientali                                                      |
    | Protezione DDoS | Selezionare **Standard** e quindi in **Protezione DDoS** selezionare **myDdosProtectionPlan** Il piano selezionato può essere nella stessa sottoscrizione della rete virtuale o in una diversa, ma entrambe le sottoscrizioni devono essere associate allo stesso tenant di Azure Active Directory.|

Non è possibile spostare una rete virtuale in un altro gruppo di risorse o in un'altra sottoscrizione quando la protezione DDoS standard è abilitata per la rete virtuale. Se si vuole spostare una rete virtuale con la protezione DDoS standard abilitata, disabilitare innanzitutto la protezione, spostare la rete virtuale e quindi abilitare la protezione DDoS standard. Dopo lo spostamento, vengono reimpostate le soglie dei criteri ottimizzati automaticamente per tutti gli indirizzi IP pubblici protetti nella rete virtuale.

## <a name="enable-ddos-for-an-existing-virtual-network"></a>Abilitare la protezione DDoS per una rete virtuale esistente

1. Creare un piano di protezione DDoS completando i passaggi descritti in [Creare un piano di protezione DDoS](#create-a-ddos-protection-plan), se non si dispone di un piano di protezione DDoS esistente.
2. Selezionare **Crea una risorsa** nell'angolo in alto a sinistra del portale di Azure.
3. Immettere il nome della rete virtuale per la quale si vuole abilitare la protezione DDoS standard nella casella **Cerca risorse, servizi e documentazione** nella parte superiore del portale. Quando il nome della rete virtuale viene visualizzato nei risultati della ricerca, selezionarlo.
4. Selezionare **Protezione DDoS** in **Impostazioni**.
5. Selezionare **Standard**. In **DDoS protection plan** (Piano di protezione DDoS) selezionare un piano di protezione DDoS esistente o il piano creato nel passaggio 1, quindi selezionare **Salva**. Il piano selezionato può essere nella stessa sottoscrizione della rete virtuale o in una diversa, ma entrambe le sottoscrizioni devono essere associate allo stesso tenant di Azure Active Directory.

## <a name="disable-ddos-for-a-virtual-network"></a>Disabilitare DDoS per una rete virtuale

1. Immettere il nome della rete virtuale per la quale si vuole disabilitare la protezione DDoS standard nella casella **Cerca risorse, servizi e documentazione** nella parte superiore del portale. Quando il nome della rete virtuale viene visualizzato nei risultati della ricerca, selezionarlo.
2. Selezionare **Protezione DDoS** in **Impostazioni**.
3. Selezionare **Basic** in **DDoS protection plan** (Piano di protezione DDoS) e quindi selezionare **Salva**.

## <a name="work-with-ddos-protection-plans"></a>Utilizzo dei piani di protezione DDoS

1. Selezionare **Tutti i servizi** nella parte superiore sinistra del portale.
2. Immettere *DDoS* nella casella **Filtro**. Selezionare **Piani di protezione DDoS** quando viene visualizzato nei risultati della ricerca.
3. Selezionare nell'elenco il piano di protezione che si vuole visualizzare.
4. Tutte le reti virtuali associate al piano sono elencate.
5. Se si vuole eliminare un piano, è innanzitutto necessario annullare l'associazione di tutte le reti virtuali. Per annullare l'associazione di un piano a una rete virtuale, vedere [Disabilitare DDoS per una rete virtuale](#disable-ddos-for-a-virtual-network).

## <a name="configure-alerts-for-ddos-protection-metrics"></a>Configurare gli avvisi per le metriche di protezione DDoS

Usando la configurazione degli avvisi di Monitoraggio di Azure, è possibile selezionare una delle metriche di protezione DDoS disponibili per ricevere un avviso nel caso in cui ci sia una mitigazione attiva durante un attacco. Quando vengono soddisfatte le condizioni, si riceve un messaggio di posta elettronica di avviso all'indirizzo specificato:

1. Selezionare **Tutti i servizi** nella parte superiore sinistra del portale.
2. Immettere *Monitoraggio* nella casella **Filtro**. Selezionare **Monitoraggio** quando viene visualizzato nei risultati.
3. Selezionare **Metriche** in **SERVIZI CONDIVISI**.
4. Immettere o selezionare i valori personalizzati, oppure immettere i valori di esempio seguenti, accettare i risultati predefiniti restanti e quindi selezionare **OK**:

    |Impostazione                  |Valore                                                                                               |
    |---------                |---------                                                                                           |
    |NOME                     | myDdosAlert                                                                                        |
    |Sottoscrizione             | Selezionare la sottoscrizione che contiene l'indirizzo IP pubblico per il quale si vogliono ricevere avvisi.        |
    |Gruppo di risorse           | Selezionare il gruppo di risorse che contiene l'indirizzo IP pubblico per il quale si vogliono ricevere avvisi.      |
    |Risorsa                 | Selezionare l'indirizzo IP pubblico che contiene l'indirizzo IP pubblico per il quale si vogliono ricevere avvisi. La protezione DDoS esegue il monitoraggio degli indirizzi IP pubblici assegnati alle risorse all'interno di una rete virtuale. Se non si dispone di risorse con indirizzi IP pubblici nella rete virtuale, è innanzitutto necessario creare una risorsa con un indirizzo IP pubblico. È possibile eseguire il monitoraggio dell'indirizzo IP pubblico di tutte le risorse distribuite tramite Resource Manager (distribuzione classica) elencate in [Rete virtuale per servizi di Azure](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network), ad eccezione degli ambienti di Servizio app di Azure e del gateway VPN di Azure. Per continuare questa esercitazione, è possibile creare rapidamente una macchina virtuale [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) o [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json).                   |
    |Metrica                   | Sotto attacco DDoS o no                                                                            |
    |Soglia                | 1 - **1** indica che è in corso un attacco **0** indica che non è in corso un attacco                         |
    |Periodo                   | Selezionare un valore di propria scelta                                                                   |
    |Notifica tramite posta elettronica         | Selezionare la casella di controllo                                                                                  |
    |Amministratore aggiuntivo | Immettere l'indirizzo di posta elettronica se non si è proprietario, collaboratore o lettore di posta elettronica per la sottoscrizione |

    Entro pochi minuti dal rilevamento dell'attacco, si riceve un messaggio di posta elettronica dalle metriche di Monitoraggio di Azure simile a quello dell'immagine seguente:

    ![Avviso di attacco](./media/manage-ddos-protection/ddos-alert.png)


Per simulare un attacco DDoS per convalidare l'avviso, vedere [Convalidare il rilevamento della protezione DDoS](#validate-ddos-detection).

È anche possibile leggere altre informazioni relative alla [configurazione dei webhook](../azure-monitor/platform/alerts-webhooks.md?toc=%2fazure%2fvirtual-network%2ftoc.json) e alle [app per la logica](../logic-apps/logic-apps-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) per la creazione di avvisi.

## <a name="use-ddos-protection-telemetry"></a>Usare la telemetria di protezione DDoS

I dati di telemetria per un attacco vengono forniti da Monitoraggio di Azure in tempo reale. La telemetria è disponibile solo per l'intervallo di tempo durante cui viene applicata la mitigazione per un indirizzo IP pubblico. Prima o dopo la mitigazione di un attacco, non vengono visualizzati dati di telemetria.

1. Selezionare **Tutti i servizi** nella parte superiore sinistra del portale.
2. Immettere *Monitoraggio* nella casella **Filtro**. Selezionare **Monitoraggio** quando viene visualizzato nei risultati.
3. Selezionare **Metriche** in **SERVIZI CONDIVISI**.
4. Selezionare la **sottoscrizione** e il **gruppo di risorse** contenenti l'indirizzo IP pubblico per il quale si vogliono ottenere i dati di telemetria.
5. Selezionare **Indirizzo IP pubblico** per **Tipo di risorsa**, quindi selezionare l'indirizzo IP pubblico specifico per il quale si vogliono ottenere i dati di telemetria.
6. A sinistra dello schermo viene visualizzata una serie di **Metriche disponibili**. Queste metriche, quando selezionate, vengono rappresentate nel **grafo delle metriche di Monitoraggio di Azure** nella schermata di panoramica.

I nomi delle metriche si riferiscono a diversi tipi di pacchetto e byte rispetto ai pacchetti, con una struttura di base correlata ai nomi di tag per ogni metrica, come indicato di seguito:

- **Nome tag eliminato** (ad esempio, **DDoS pacchetti in ingresso eliminati**): Il numero di pacchetti eliminati/sottoposti a scrubbing dal sistema di protezione DDoS.
- **Nome tag inoltrato** (ad esempio, **DDoS pacchetti in ingresso inoltrati**): numero di pacchetti inoltrati dal sistema DDoS all'indirizzo VIP di destinazione (il traffico non è stato filtrato).
- **Nessun nome di tag** (ad esempio **Inbound Packets DDoS** (Pacchetti in ingresso DDoS)): numero totale di pacchetti arrivati nel sistema di scrubbing, che rappresenta la somma dei pacchetti eliminati e di quelli inoltrati.

Per simulare un attacco DDoS per convalidare la telemetria, vedere [Convalidare il rilevamento della protezione DDoS](#validate-ddos-detection).

## <a name="view-ddos-mitigation-policies"></a>Visualizzare i criteri di mitigazione della protezione DDoS

La protezione DDoS standard applica tre criteri di mitigazione ottimizzati automaticamente (TCP SYN, TCP e UDP) per ogni indirizzo IP pubblico della risorsa protetta, nella rete virtuale in cui è abilitata la protezione DDoS. È possibile visualizzare le soglie dei criteri selezionando le metriche **Inbound TCP packets to trigger DDoS mitigation** (Pacchetti TCP in ingresso per attivare la mitigazione DDoS) e **Inbound UDP packets to trigger DDoS mitigation** (Pacchetti UDP in ingresso per attivare la mitigazione DDoS), come illustrato nell'immagine seguente:

![Visualizzare i criteri di mitigazione](./media/manage-ddos-protection/view-mitigation-policies.png)

Le soglie dei criteri vengono configurate automaticamente tramite la profilatura del traffico di rete basato sull'apprendimento automatico di Azure. La mitigazione DDoS si attiva per l'indirizzo IP sotto attacco solo quando viene superata la soglia dei criteri.

## <a name="configure-ddos-attack-analytics"></a>Configurare l'analisi degli attacchi DDoS
Lo standard Protezione DDoS di Azure fornisce informazioni dettagliate sugli attacchi e ne consente la visualizzazione con l'analisi degli attacchi DDoS. I clienti che proteggono le reti virtuali da attacchi DDoS hanno visibilità dettagliata sul traffico degli attacchi e sulle azioni intraprese per mitigare l'attacco tramite i report sulla mitigazione dell'attacco e i log del flusso di mitigazione. 

## <a name="configure-ddos-attack-mitigation-reports"></a>Configurare i report sulla mitigazione degli attacchi DDoS
I report sulla mitigazione degli attacchi usano i dati del protocollo Netflow che vengono aggregati per fornire informazioni dettagliate sull'attacco alla risorsa. Ogni volta che una risorsa IP pubblica è sotto attacco, la generazione di report verrà avviata contemporaneamente alla mitigazione. Verranno generati un report incrementale ogni 5 minuti e un report post-mitigazione per l'intero periodo della mitigazione. Lo scopo è quello di assicurare che, qualora l'attacco DDoS prosegua per un periodo di tempo più lungo, sia possibile visualizzare lo snapshot più aggiornato del report sulla mitigazione ogni 5 minuti e un riepilogo completo al termine della mitigazione dell'attacco. 

1. Selezionare **Tutti i servizi** nella parte superiore sinistra del portale.
2. Immettere *Monitoraggio* nella casella **Filtro**. Selezionare **Monitoraggio** quando viene visualizzato nei risultati.
3. Selezionare **Impostazioni di diagnostica** in **IMPOSTAZIONI**.
4. Selezionare la **sottoscrizione** e il **gruppo di risorse** contenenti l'indirizzo IP pubblico che si vuole registrare.
5. Selezionare **Indirizzo IP pubblico** per **Tipo di risorsa**, quindi selezionare l'indirizzo IP pubblico specifico per il quale si vogliono registrare le metriche.
6. Selezionare **Turn on diagnostics to collect the DDoSMitigationReports log** (Attiva diagnostica per raccogliere il log DDoSMitigationReports) e quindi selezionare tutte le opzioni necessarie tra le seguenti:

    - **Archiviare in un account di archiviazione**: I dati vengono scritti in un account di archiviazione di Azure. Per altre informazioni su questa opzione, vedere [Archiviare i log di diagnostica](../azure-monitor/platform/archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - **Streaming in un hub eventi**: consente a un ricevitore di log di prelevare i log usando un Hub eventi di Azure. Gli hub eventi consentono l'integrazione con Splunk o altri sistemi SIEM. Per altre informazioni su questa opzione, vedere [Trasmettere log di diagnostica a un hub eventi](../azure-monitor/platform/diagnostic-logs-stream-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - **Invia a Log Analytics**: scrive i log nel servizio Azure Log Analytics. Per altre informazioni su questa opzione, vedere [Raccolta di log da usare in Log Analytics](../azure-monitor/platform/collect-azure-metrics-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Sia i report incrementali che quello sulla mitigazione successivo all'attacco includono i campi seguenti
- Attack vectors (Vettori di attacco)
- Traffic statistics (Statistiche sul traffico)
- Reason for dropped packets (Motivo dei pacchetti eliminati)
- Protocols involved (Protocolli interessati)
- Top 10 source countries or regions (Primi 10 paesi o aree di origine)
- Top 10 source ASNs (Primi 10 ASN di origine)

## <a name="configure-ddos-attack-mitigation-flow-logs"></a>Configurare i log del flusso di mitigazione degli attacchi DDoS
I log del flusso di mitigazione degli attacchi consentono di esaminare il traffico eliminato, il traffico inoltrato e punti dati interessanti durante un attacco DDoS attivo in tempo quasi reale. È possibile inserire il flusso costante di questi dati nei sistemi SIEM tramite l'hub eventi per il monitoraggio in tempo quasi reale, eseguire potenziali azioni e soddisfare l'esigenza di operazioni di difesa. 

1. Selezionare **Tutti i servizi** nella parte superiore sinistra del portale.
2. Immettere *Monitoraggio* nella casella **Filtro**. Selezionare **Monitoraggio** quando viene visualizzato nei risultati.
3. Selezionare **Impostazioni di diagnostica** in **IMPOSTAZIONI**.
4. Selezionare la **sottoscrizione** e il **gruppo di risorse** contenenti l'indirizzo IP pubblico che si vuole registrare.
5. Selezionare **Indirizzo IP pubblico** per **Tipo di risorsa**, quindi selezionare l'indirizzo IP pubblico specifico per il quale si vogliono registrare le metriche.
6. Selezionare **Turn on diagnostics to collect the DDoSMitigationFlowLogs log** (Attiva diagnostica per raccogliere il log DDoSMitigationFlowLogs) e quindi selezionare tutte le opzioni necessarie tra le seguenti:

    - **Archiviare in un account di archiviazione**: I dati vengono scritti in un account di archiviazione di Azure. Per altre informazioni su questa opzione, vedere [Archiviare i log di diagnostica](../azure-monitor/platform/archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - **Streaming in un hub eventi**: consente a un ricevitore di log di prelevare i log usando un Hub eventi di Azure. Gli hub eventi consentono l'integrazione con Splunk o altri sistemi SIEM. Per altre informazioni su questa opzione, vedere [Trasmettere log di diagnostica a un hub eventi](../azure-monitor/platform/diagnostic-logs-stream-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - **Invia a Log Analytics**: scrive i log nel servizio Azure Log Analytics. Per altre informazioni su questa opzione, vedere [Raccolta di log da usare in Log Analytics](../azure-monitor/platform/collect-azure-metrics-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
1. Per visualizzare i dati dei log del flusso nel dashboard di analisi di Azure, è possibile importare il dashboard di esempio da https://github.com/Anupamvi/Azure-DDoS-Protection/raw/master/flowlogsbyip.zip

I log del flusso conterranno i campi seguenti: 
- IP di origine
- IP di destinazione
- Porta di origine 
- Porta di destinazione 
- Protocol type (Tipo di protocollo) 
- Action taken during mitigation (Azione eseguita durante la mitigazione)



## <a name="validate-ddos-detection"></a>Convalidare il rilevamento della protezione DDoS

Microsoft ha collaborato con [BreakingPoint Cloud](https://www.ixiacom.com/products/breakingpoint-cloud) per compilare un'interfaccia in cui è possibile generare il traffico per abilitare la protezione DDoS agli indirizzi IP pubblici per simulazioni. La simulazione BreakPoint Cloud consente di:

- Convalidare la modalità in cui la Protezione DDoS di Microsoft Azure protegge le risorse di Azure da attacchi DDoS
- Ottimizzare il processo di risposta agli eventi imprevisti durante un attacco DDoS
- Documentare la conformità DDoS
- Eseguire il training dei team di sicurezza di rete

## <a name="permissions"></a>Autorizzazioni

Per lavorare con i piani di protezione DDoS, l'account deve essere assegnato al ruolo [Collaboratore Rete](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) o a un ruolo [personalizzato](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) a cui sono assegnate le operazioni appropriate elencate nella tabella seguente:

| Azione                                            | NOME                                     |
| ---------                                         | -------------                            |
| Microsoft.Network/ddosProtectionPlans/read        | Leggere un piano di protezione DDoS              |
| Microsoft.Network/ddosProtectionPlans/write       | Creare o aggiornare un piano di protezione DDoS  |
| Microsoft.Network/ddosProtectionPlans/delete      | Eliminare un piano di protezione DDoS            |
| Microsoft.Network/ddosProtectionPlans/join/action | Aggiungere un piano di Protezione DDoS              |

Per abilitare la protezione DDoS per una rete virtuale, all'account devono anche essere assegnate le appropriate [operazioni per le reti virtuali](manage-virtual-network.md#permissions).

## <a name="next-steps"></a>Passaggi successivi

- Creare e applicare i [criteri di Azure](policy-samples.md) per le reti virtuali