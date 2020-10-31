---
title: Gestire Protezione DDoS di Azure Standard nel portale di Azure
description: Informazioni su come usare protezione DDoS di Azure standard per attenuare un attacco.
services: ddos-protection
documentationcenter: na
author: KumudD
manager: mtillman
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: ddos-protection
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/17/2019
ms.author: kumud
ms.openlocfilehash: fc60ca462a2891cc022847e056e32239f2675f70
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93094575"
---
# <a name="quickstart-create-and-configure-azure-ddos-protection-standard"></a>Guida introduttiva: creare e configurare la protezione DDoS di Azure standard

Per iniziare a usare la protezione DDoS di Azure standard, è necessario usare la portale di Azure. 

Un piano di protezione DDoS definisce un set di reti virtuali in cui è abilitata la protezione DDoS standard per le sottoscrizioni. È possibile configurare un piano di protezione DDoS standard per l'organizzazione e collegare reti virtuali da più sottoscrizioni allo stesso piano. 

In questa Guida introduttiva si creerà un piano di protezione DDoS che verrà collegato a una rete virtuale. 

## <a name="prerequisites"></a>Prerequisiti

- Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.
- Accedere al portale di Azure all'indirizzo https://portal.azure.com. Verificare che l'account sia assegnato al ruolo [collaboratore rete](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) o a un [ruolo personalizzato](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) a cui sono assegnate le azioni appropriate elencate nella Guida alle procedure sulle [autorizzazioni](manage-permissions.md).

## <a name="create-a-ddos-protection-plan"></a>Creare un piano di protezione DDoS

1. Selezionare **Crea una risorsa** nell'angolo superiore sinistro del portale di Azure.
2. Cercare il termine *DDoS* . Quando il **piano di protezione DDoS** viene visualizzato nei risultati della ricerca, selezionarlo.
3. Selezionare **Crea** .
4. Immettere o selezionare i valori seguenti e quindi selezionare **Crea** :

    |Impostazione        |Valore                                              |
    |---------      |---------                                          |
    |Nome           | Immettere _MyDdosProtectionPlan_ .                     |
    |Subscription   | Selezionare la propria sottoscrizione.                         |
    |Resource group | Selezionare **Crea nuovo** e immettere _MyResourceGroup_ .|
    |Location       | Immettere _Stati Uniti orientali_ .                                  |

## <a name="enable-ddos-protection-for-a-virtual-network"></a>Abilitare la protezione DDoS per una rete virtuale

### <a name="enable-ddos-protection-for-a-new-virtual-network"></a>Abilitare la protezione DDoS per una nuova rete virtuale

1. Selezionare **Crea una risorsa** nell'angolo superiore sinistro del portale di Azure.
2. Selezionare **rete e quindi** **rete virtuale** .
3. Immettere o selezionare i valori seguenti, accettare le impostazioni predefinite rimanenti e quindi selezionare **Crea** :

    | Impostazione         | Valore                                           |
    | ---------       | ---------                                       |
    | Nome            | Immettere _MyVnet_ .                                 |
    | Subscription    | Selezionare la propria sottoscrizione.                                    |
    | Resource group  | Selezionare **Usa esistente** e quindi selezionare **MyResourceGroup** |
    | Location        | Immettere _Stati Uniti orientali_                                                    |
    | Protezione DDoS Standard | Selezionare **Abilita** . Il piano selezionato può essere nella stessa sottoscrizione della rete virtuale o in una diversa, ma entrambe le sottoscrizioni devono essere associate allo stesso tenant di Azure Active Directory.|

Non è possibile spostare una rete virtuale in un altro gruppo di risorse o in un'altra sottoscrizione quando la protezione DDoS standard è abilitata per la rete virtuale. Se si vuole spostare una rete virtuale con la protezione DDoS standard abilitata, disabilitare innanzitutto la protezione, spostare la rete virtuale e quindi abilitare la protezione DDoS standard. Dopo lo spostamento, vengono reimpostate le soglie dei criteri ottimizzati automaticamente per tutti gli indirizzi IP pubblici protetti nella rete virtuale.

### <a name="enable-ddos-protection-for-an-existing-virtual-network"></a>Abilitare la protezione DDoS per una rete virtuale esistente

1. Creare un piano di protezione DDoS completando i passaggi descritti in [Creare un piano di protezione DDoS](#create-a-ddos-protection-plan), se non si dispone di un piano di protezione DDoS esistente.
2. Selezionare **Crea una risorsa** nell'angolo superiore sinistro del portale di Azure.
3. Immettere il nome della rete virtuale per la quale si vuole abilitare la protezione DDoS standard nella casella **Cerca risorse, servizi e documentazione** nella parte superiore del portale. Quando il nome della rete virtuale viene visualizzato nei risultati della ricerca, selezionarlo.
4. Selezionare **Protezione DDoS** in **Impostazioni** .
5. Selezionare **Standard** . In **DDoS protection plan** (Piano di protezione DDoS) selezionare un piano di protezione DDoS esistente o il piano creato nel passaggio 1, quindi selezionare **Salva** . Il piano selezionato può essere nella stessa sottoscrizione della rete virtuale o in una diversa, ma entrambe le sottoscrizioni devono essere associate allo stesso tenant di Azure Active Directory.

## <a name="validate-and-test"></a>Convalida e verifica

Per prima cosa, verificare i dettagli del piano di protezione DDoS:

1. Selezionare **Tutti i servizi** nella parte superiore sinistra del portale.
2. Immettere *DDoS* nella casella **Filtro** . Selezionare **Piani di protezione DDoS** quando viene visualizzato nei risultati della ricerca.
3. Selezionare il piano di protezione DDoS dall'elenco.

Verrà visualizzata la rete virtuale _MyVnet_ . 

## <a name="clean-up-resources"></a>Pulire le risorse

È possibile salvare le risorse per l'esercitazione successiva. Se non è più necessario, eliminare il gruppo di risorse _MyResourceGroup_ . Quando si elimina il gruppo di risorse, viene eliminato anche il piano di protezione DDoS e tutte le risorse correlate. Se non si prevede di usare questo piano di protezione DDoS, è consigliabile rimuovere le risorse per evitare addebiti superflui.

   >[!WARNING]
   >Questa azione è irreversibile.

1. Nel portale di Azure cercare e selezionare **Gruppi di risorse** oppure scegliere **Gruppi di risorse** dal menu del portale di Azure.

2. Filtrare o scorrere verso il basso per trovare il gruppo di risorse _MyResourceGroup_ .

3. Selezionare il gruppo di risorse e quindi **Elimina gruppo di risorse** .

4. Digitare il nome del gruppo di risorse per verifica, quindi selezionare **Elimina** .

Per disabilitare la protezione DDoS per una rete virtuale: 

1. Immettere il nome della rete virtuale per la quale si vuole disabilitare la protezione DDoS standard nella casella **Cerca risorse, servizi e documentazione** nella parte superiore del portale. Quando il nome della rete virtuale viene visualizzato nei risultati della ricerca, selezionarlo.
2. Selezionare **in protezione DDoS standard** , selezionare **Disabilita** .

Se si vuole eliminare un piano di protezione DDoS, è necessario innanzitutto annullare l'associazione di tutte le reti virtuali. 

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come visualizzare e configurare i dati di telemetria per il piano di protezione DDoS, continuare con le esercitazioni.

> [!div class="nextstepaction"]
> [Visualizzare e configurare i dati di telemetria della protezione DDoS](telemetry-monitoring-alerting.md)
