---
title: Creare un criterio di firewall applicazione web per l'ingresso principale di Azure usando il portale di Azure
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
ms.author: kumud;tyao
ms.openlocfilehash: 15a80dac0e0601480e22ad960f2827f3d8f290c0
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66479037"
---
# <a name="create-a-waf-policy-for-azure-front-door-by-using-the-azure-portal"></a>Creare un criterio di Web Application firewall per l'ingresso principale di Azure usando il portale di Azure

Questo articolo descrive come creare un criterio di firewall (WAF) dell'applicazione web di Azure di base e applicarlo a un host front-end alla porta di ingresso di Azure.

## <a name="prerequisites"></a>Prerequisiti

Creare un profilo Frontdoor seguendo le istruzioni descritte nell'articolo [Avvio rapido: Creare un profilo Frontdoor](quickstart-create-front-door.md). 

## <a name="create-a-waf-policy"></a>Creare un criterio di Web Application firewall

In primo luogo, creare un criterio di Web Application firewall basic con gestito predefinito regola impostato (DRS) tramite il portale. 

1. Nella parte superiore sinistra della schermata, selezionare **crea una risorsa**> cercare **WAF**> selezionare **Web application firewall (anteprima)** > selezionare  **Creare**.
2. Nel **nozioni di base** scheda della finestra di **creare un criterio di WAF** pagina immettere o selezionare le informazioni seguenti, accettare le impostazioni predefinite per le altre impostazioni e quindi selezionare **revisione + Crea**:

    | Impostazione                 | Value                                              |
    | ---                     | ---                                                |
    | Sottoscrizione            |Selezionare il nome della sottoscrizione porta principale.|
    | Gruppo di risorse          |Selezionare il nome di gruppo di risorse di ingresso principale.|
    | Nome criterio             |Immettere un nome univoco per il criterio di Web Application firewall.|

   ![Creare un criterio di Web Application firewall](./media/waf-front-door-create-portal/basic.png)

3. Nel **Association** scheda della finestra di **creare un criterio di Web Application firewall** pagina, selezionare **Aggiungi host front-end**, immettere le impostazioni seguenti e quindi selezionare **Aggiungi**:

    | Impostazione                 | Value                                              |
    | ---                     | ---                                                |
    | Porta principale              | Selezionare il nome del profilo di porta principale.|
    | Host front-end           | Selezionare il nome dell'host di ingresso principale, quindi selezionare **Add**.|
    
    > [!NOTE]
    > Se l'host di front-end è associato a un criterio di WAF, viene visualizzato come in grigio. È necessario innanzitutto rimuovere l'host di front-end dal criterio associato e quindi associare di nuovo l'host di front-end in un nuovo criterio di Web Application firewall.
1. Selezionare **revisione + Crea**, quindi selezionare **crea**.

## <a name="configure-waf-rules-optional"></a>Configurare le regole del Web Application firewall (facoltative)

### <a name="change-mode"></a>Cambia modalità

Quando si crea un criterio di Web Application firewall, per il firewall WAF predefinita dei criteri si trova in **rilevamento** modalità. Nelle **rilevamento** modalità, Web Application firewall non blocchi le richieste, invece, vengono registrate le richieste corrispondenti le regole WAF log WAF.
Per visualizzare Web Application firewall in azione, è possibile modificare le impostazioni della modalità dal **rilevamento** al **prevenzione**. Nelle **prevenzione** modalità richiede che le regole di corrispondenza definiti nella predefinito regola impostato (DRS) sono bloccate e connesso in cui i log WAF.

 ![Modalità dei criteri di modifica Web Application firewall](./media/waf-front-door-create-portal/policy.png)

### <a name="custom-rules"></a>Regole personalizzate

È possibile creare una regola personalizzata selezionando **Aggiungi regola personalizzata** sotto il **regole personalizzate** sezione. Verrà avviata la pagina di configurazione di regola personalizzata. Di seguito è riportato un esempio di configurazione di una regola personalizzata per bloccare una richiesta se la stringa di query contiene **blockme**.

![Modalità dei criteri di modifica Web Application firewall](./media/waf-front-door-create-portal/customquerystring2.png)

### <a name="default-rule-set-drs"></a>Set di regole predefinite (DRS)

Gestito da Azure Set di regole predefinito è abilitato per impostazione predefinita. Per disabilitare una regola singola all'interno di un gruppo di regole, espandere le regole all'interno di tale gruppo di regole, seleziona la **casella di controllo** davanti il numero di regole e selezionare **disabilitare** nella scheda precedente. Per modificare i tipi di azioni per singole regole all'interno della regola impostata, selezionare la casella di controllo davanti il numero di regole e quindi selezionare il **Modifica azione** scheda riportato sopra.

 ![Modificare Set di regole WAF](./media/waf-front-door-create-portal/managed2.png)

## <a name="next-steps"></a>Passaggi successivi

- Scopri [firewall applicazione web di Azure](waf-overview.md).
- Altre informazioni sulle [porta d'ingresso Azure](front-door-overview.md).
