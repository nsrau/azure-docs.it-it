---
title: 'Esercitazione: Creare un criterio di web application firewall per Frontdoor di Azure usando il portale di Azure'
description: Questa esercitazione illustra come creare un criterio di web application firewall (WAF) tramite il portale di Azure.
author: vhorne
ms.service: web-application-firewall
services: web-application-firewall
ms.topic: tutorial
ms.date: 09/07/2019
ms.author: victorh
ms.openlocfilehash: 602a8620ef35ea247c5865d97495ea43a7d7d93c
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73508905"
---
# <a name="tutorial-create-a-web-application-firewall-policy-on-azure-front-door-using-the-azure-portal"></a>Esercitazione: Creare un criterio di web application firewall in Frontdoor di Azure usando il portale di Azure

Questa esercitazione illustra come creare un criterio di base di web application firewall (WAF) di Azure e applicarlo a un host front-end in Frontdoor di Azure.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare un criterio WAF
> * Associarlo a un host front-end
> * Configurare le regole per web application firewall

## <a name="prerequisites"></a>Prerequisiti

Creare un profilo Frontdoor seguendo le istruzioni descritte nell'articolo [Avvio rapido: Creare un profilo Frontdoor](../../frontdoor/quickstart-create-front-door.md). 

## <a name="create-a-web-application-firewall-policy"></a>Creare un criterio di web application firewall

Come prima cosa, creare un criterio WAF di base con il set di regole predefinite gestito usando il portale. 

1. Nella parte superiore sinistra della schermata selezionare **Crea una risorsa**, cercare **WAF**, selezionare **Web application firewall (anteprima)** e infine selezionare **Crea**.
2. Nella scheda **Generale** della pagina **Crea un criterio WAF** immettere o selezionare le informazioni seguenti, accettare le impostazioni predefinite per le opzioni rimanenti e quindi selezionare **Rivedi e crea**:

    | Impostazione                 | Valore                                              |
    | ---                     | ---                                                |
    | Subscription            |Selezionare il nome della sottoscrizione di Frontdoor.|
    | Resource group          |Selezionare il nome del gruppo di risorse di Frontdoor.|
    | Nome criterio             |Immettere un nome univoco per il criterio WAF.|

   ![Creare un criterio WAF](../media/waf-front-door-create-portal/basic.png)

3. Nella scheda **Associazione** della pagina **Crea un criterio WAF** selezionare **Aggiungi host front-end**, immettere le impostazioni seguenti e quindi selezionare **Aggiungi**:

    | Impostazione                 | Valore                                              |
    | ---                     | ---                                                |
    | Frontdoor              | Selezionare il nome del profilo Frontdoor.|
    | Host front-end           | Selezionare il nome dell'host Frontdoor, quindi selezionare **Aggiungi**.|
    
    > [!NOTE]
    > Se l'host front-end è associato a un criterio WAF, appare disattivato. Occorre prima rimuovere l'host front-end dal criterio associato e quindi riassociare l'host front-end a un nuovo criterio WAF.
1. Selezionare **Rivedi e crea** e quindi **Crea**.

## <a name="configure-web-application-firewall-rules-optional"></a>Configurare le regole di web application firewall (facoltativo)

### <a name="change-mode"></a>Cambiare modalità

Quando si crea un criterio WAF, per impostazione predefinita questo è in modalità **Rilevamento**. In modalità **Rilevamento ** WAF non blocca alcuna richiesta e le richieste che soddisfano le regole WAF vengono registrate nei log di WAF.
Per vedere WAF in azione, è possibile cambiare le impostazioni della modalità da **Rilevamento** a **Prevenzione**. In modalità **Prevenzione** le richieste che soddisfano le regole definite nel set di regole predefinite vengono bloccate e registrate nei log WAF.

 ![Cambiare la modalità del criterio WAF](../media/waf-front-door-create-portal/policy.png)

### <a name="custom-rules"></a>Regole personalizzate

È possibile creare una regola personalizzata selezionando **Aggiungi regola personalizzata** nella sezione **Regole personalizzate**. Viene avviata la pagina di configurazione delle regole personalizzate. Di seguito è riportato un esempio di configurazione di una regola personalizzata per bloccare una richiesta se la stringa di query contiene **blockme**.

![Cambiare la modalità del criterio WAF](../media/waf-front-door-create-portal/customquerystring2.png)

### <a name="default-rule-set-drs"></a>Set di regole predefinite

Il set di regole predefinite gestito da Azure è abilitato per impostazione predefinita. Per disabilitare una singola regola in un gruppo di regole, espandere le regole all'interno di tale gruppo, selezionare la **casella di controllo** davanti al numero della regola e quindi selezionare **Disabilita** nella scheda visualizzata sopra. Per cambiare i tipi di azioni per le singole regole all'interno del set di regole, selezionare la casella di controllo davanti al numero della regola, quindi selezionare la scheda **Modifica azione**.

 ![Modificare il set di regole WAF](../media/waf-front-door-create-portal/managed2.png)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni su web application firewall di Azure](../overview.md)
> [Altre informazioni su Frontdoor di Azure](../../frontdoor/front-door-overview.md)