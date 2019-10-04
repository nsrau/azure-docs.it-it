---
title: Creare un criterio di web application firewall per lo sportello anteriore di Azure usando il portale di Azure
titlesuffix: Azure web application firewall
description: Informazioni su come creare un criterio di web application firewall (WAF) usando il portale di Azure.
services: frontdoor
documentationcenter: na
author: KumudD
manager: twooley
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/31/2019
ms.author: kumud
ms.reviewer: tyao
ms.openlocfilehash: abaef0fb521d848134885a06591b0656c60c67e6
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2019
ms.locfileid: "67846379"
---
# <a name="create-a-waf-policy-for-azure-front-door-by-using-the-azure-portal"></a>Creare un criterio WAF per lo sportello anteriore di Azure usando il portale di Azure

Questo articolo descrive come creare un criterio di base di Azure web application firewall (WAF) e applicarlo a un host front-end in Azure front door.

## <a name="prerequisites"></a>Prerequisiti

Creare un profilo Frontdoor seguendo le istruzioni descritte nell'articolo [Avvio rapido: Creare un profilo Frontdoor](quickstart-create-front-door.md). 

## <a name="create-a-waf-policy"></a>Creare un criterio WAF

Per prima cosa, creare un criterio di base di WAF con il set di regole gestito predefinito (DRS) usando il portale. 

1. Nella parte superiore sinistra della schermata selezionare **Crea una risorsa**> cercare **WAF**> selezionare **Web application firewall (anteprima)** > selezionare **Crea**.
2. Nella scheda **nozioni di base** della pagina **creare un criterio WAF** immettere o selezionare le informazioni seguenti, accettare le impostazioni predefinite per le altre impostazioni e quindi selezionare **Verifica + crea**:

    | Impostazione                 | Value                                              |
    | ---                     | ---                                                |
    | Sottoscrizione            |Selezionare il nome della sottoscrizione della porta anteriore.|
    | Gruppo di risorse          |Selezionare il nome del gruppo di risorse della porta anteriore.|
    | Nome criterio             |Immettere un nome univoco per i criteri di WAF.|

   ![Creare un criterio WAF](./media/waf-front-door-create-portal/basic.png)

3. Nella scheda **associazione** della pagina **creare un criterio WAF** Selezionare **Aggiungi host**front-end, immettere le impostazioni seguenti e quindi selezionare **Aggiungi**:

    | Impostazione                 | Valore                                              |
    | ---                     | ---                                                |
    | Sportello anteriore              | Selezionare il nome del profilo della porta anteriore.|
    | Host front-end           | Selezionare il nome dell'host della porta anteriore, quindi selezionare **Aggiungi**.|
    
    > [!NOTE]
    > Se l'host front-end è associato a un criterio WAF, viene visualizzato come grigio. È necessario innanzitutto rimuovere l'host front-end dal criterio associato e quindi riassociare l'host front-end a un nuovo criterio WAF.
1. Selezionare **Verifica + crea**, quindi selezionare **Crea**.

## <a name="configure-waf-rules-optional"></a>Configurare le regole di WAF (facoltativo)

### <a name="change-mode"></a>Cambia modalità

Quando si crea un criterio WAF, i criteri predefiniti di WAF sono in modalità di **rilevamento** . In modalità di **rilevamento** , WAF non blocca le richieste, ma le richieste corrispondenti alle regole WAF vengono registrate nei log di WAF.
Per visualizzare WAF in azione, è possibile modificare le impostazioni della modalità dal **rilevamento** alla **prevenzione**. In modalità di **prevenzione** , le richieste che corrispondono alle regole definite nel set di regole predefinito (DRS) vengono bloccate e registrate nei registri WAF.

 ![Modificare la modalità dei criteri di WAF](./media/waf-front-door-create-portal/policy.png)

### <a name="custom-rules"></a>Regole personalizzate

È possibile creare una regola personalizzata selezionando **Aggiungi regola personalizzata** nella sezione **regole personalizzate** . Verrà avviata la pagina Configurazione regola personalizzata. Di seguito è riportato un esempio di configurazione di una regola personalizzata per bloccare una richiesta se la stringa di query contiene **blockme**.

![Modificare la modalità dei criteri di WAF](./media/waf-front-door-create-portal/customquerystring2.png)

### <a name="default-rule-set-drs"></a>Set di regole predefinito (DRS)

Il set di regole predefinite gestito da Azure è abilitato per impostazione predefinita. Per disabilitare una singola regola in un gruppo di regole, espandere le regole all'interno di tale gruppo, selezionare la **casella di controllo** davanti al numero della regola e selezionare **Disabilita** nella scheda sopra. Per modificare i tipi di azioni per le singole regole all'interno del set di regole, selezionare la casella di controllo davanti al numero della regola, quindi selezionare la scheda **Modifica azione** sopra.

 ![Modificare il set di regole WAF](./media/waf-front-door-create-portal/managed2.png)

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sul [web application firewall di Azure](waf-overview.md).
- Altre informazioni su [Azure front door](front-door-overview.md).
