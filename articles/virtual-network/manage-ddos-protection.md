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
ms.date: 03/29/2018
ms.author: jdial
ms.openlocfilehash: dd094f2b9cdb9b5eb164dda2925d094cafa7cd89
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/08/2018
ms.locfileid: "33895613"
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
    | NOME            | myVirtualNetwork                                             |
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

È anche possibile leggere altre informazioni relative alla [configurazione dei webhook](../monitoring-and-diagnostics/insights-webhooks-alerts.md?toc=%2fazure%2fvirtual-network%2ftoc.json) e alle [app per la logica](../logic-apps/logic-apps-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) per la creazione di avvisi.

## <a name="configure-logging-for-ddos-protection-metrics"></a>Configurare la registrazione per le metriche della protezione DDoS

1. Selezionare **Tutti i servizi** nella parte superiore sinistra del portale.
2. Immettere *Monitoraggio* nella casella **Filtro**. Selezionare **Monitoraggio** quando viene visualizzato nei risultati.
3. Selezionare **Impostazioni di diagnostica** in **IMPOSTAZIONI**.
4. Selezionare la **sottoscrizione** e il **gruppo di risorse** contenenti l'indirizzo IP pubblico che si vuole registrare.
5. Selezionare **Indirizzo IP pubblico** per **Tipo di risorsa**, quindi selezionare l'indirizzo IP pubblico specifico per il quale si vogliono registrare le metriche.
6. Selezionare **Turn on diagnostics to collect the following data** (Attiva diagnostica per raccogliere i dati seguenti) e quindi selezionare tutte le opzioni necessarie tra le seguenti:

    - **Archivia in un account di archiviazione**: i dati vengono scritti in un account di archiviazione di Azure. Per altre informazioni su questa opzione, vedere [Archiviare i log di diagnostica](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - **Streaming in un hub eventi**: consente a un ricevitore di log di prelevare i log usando un Hub eventi di Azure. Gli hub eventi consentono l'integrazione con Splunk o altri sistemi SIEM. Per altre informazioni su questa opzione, vedere [Trasmettere log di diagnostica a un hub eventi](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - **Invia a Log Analytics**: scrive i log nel servizio Azure OMS Log Analytics. Per altre informazioni su questa opzione, vedere [Raccolta di log da usare in Log Analytics](../log-analytics/log-analytics-azure-storage.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Per simulare un attacco DDoS per convalidare la registrazione, vedere [Convalidare il rilevamento della protezione DDoS](#validate-ddos-detection).

## <a name="use-ddos-protection-telemetry"></a>Usare la telemetria di Protezione DDoS

I dati di telemetria per un attacco vengono forniti da Monitoraggio di Azure in tempo reale. La telemetria è disponibile solo per l'intervallo di tempo durante cui viene applicata la mitigazione per un indirizzo IP pubblico. Prima o dopo la mitigazione di un attacco, non vengono visualizzati dati di telemetria.

1. Selezionare **Tutti i servizi** nella parte superiore sinistra del portale.
2. Immettere *Monitoraggio* nella casella **Filtro**. Selezionare **Monitoraggio** quando viene visualizzato nei risultati.
3. Selezionare **Metriche** in **SERVIZI CONDIVISI**.
4. Selezionare la **sottoscrizione** e il **gruppo di risorse** contenenti l'indirizzo IP pubblico per il quale si vogliono ottenere i dati di telemetria.
5. Selezionare **Indirizzo IP pubblico** per **Tipo di risorsa**, quindi selezionare l'indirizzo IP pubblico specifico per il quale si vogliono ottenere i dati di telemetria.
6. A sinistra dello schermo viene visualizzata una serie di **Metriche disponibili**. Queste metriche, quando selezionate, vengono rappresentate nel **grafo delle metriche di Monitoraggio di Azure** nella schermata di panoramica.

I nomi delle metriche si riferiscono a diversi tipi di pacchetto e byte rispetto ai pacchetti, con una struttura di base correlata ai nomi di tag per ogni metrica, come indicato di seguito:

- **Dropped tag name** (Nome tag eliminato), ad esempio **Inbound Packets Dropped DDoS** (Pacchetti in ingresso eliminati da DDoS): numero di pacchetti eliminati/sottoposti a scrubbing dal sistema di protezione DDoS.
- **Forwarded tag name** (Nome tag inoltrato), ad esempio **Inbound Packets Forwarded DDoS** (Pacchetti in ingresso inoltrati da DDoS): numero di pacchetti inoltrati dal sistema DDoS all'indirizzo VIP di destinazione (il traffico non è stato filtrato).
- **No tag name** (Nessun nome di tag), ad esempio **Inbound Packets DDoS** (Pacchetti in ingresso elaborati da DDoS): numero totale di pacchetti arrivati nel sistema di scrubbing, che rappresenta la somma dei pacchetti eliminati e di quelli inoltrati.

Per simulare un attacco DDoS per convalidare la telemetria, vedere [Convalidare il rilevamento della protezione DDoS](#validate-ddos-detection).

## <a name="view-ddos-mitigation-policies"></a>Visualizzare i criteri di mitigazione della protezione DDoS

La protezione DDoS standard applica tre criteri di mitigazione ottimizzati automaticamente (TCP SYN, TCP e UDP) per ogni indirizzo IP pubblico della risorsa protetta, nella rete virtuale in cui è abilitata la protezione DDoS. È possibile visualizzare le soglie dei criteri selezionando le metriche **Inbound TCP packets to trigger DDoS mitigation** (Pacchetti TCP in ingresso per attivare la mitigazione DDoS) e **Inbound UDP packets to trigger DDoS mitigation** (Pacchetti UDP in ingresso per attivare la mitigazione DDoS), come illustrato nell'immagine seguente:

![Visualizzare i criteri di mitigazione](./media/manage-ddos-protection/view-mitigation-policies.png)

Le soglie dei criteri vengono configurate automaticamente tramite la profilatura del traffico di rete basato sull'apprendimento automatico di Azure. La mitigazione DDoS si attiva per l'indirizzo IP sotto attacco solo quando viene superata la soglia dei criteri.

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