---
title: 'Avvio rapido: Creare un profilo per la disponibilità elevata delle applicazioni - Portale di Azure - Gestione traffico di Azure'
description: Questo articolo di avvio rapido descrive come creare un profilo di Gestione traffico per creare un'applicazione Web a disponibilità elevata tramite il portale di Azure.
services: traffic-manager
author: duongau
manager: twooley
Customer intent: As an IT admin, I want to direct user traffic to ensure high availability of web applications.
ms.service: traffic-manager
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/15/2020
ms.author: duau
ms.openlocfilehash: 45489d3500a4a744f2aeb34dc21122d180797133
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92101323"
---
# <a name="quickstart-create-a-traffic-manager-profile-using-the-azure-portal"></a>Guida introduttiva: Creare un profilo di Gestione traffico usando il portale di Azure

Questo argomento di avvio rapido descrive come creare un profilo di Gestione traffico che fornisce disponibilità elevata per l'applicazione Web.

In questo argomento di avvio rapido verranno illustrate due istanze di un'applicazione Web. Ognuna di esse è in esecuzione in un'area di Azure diversa. Si creerà un profilo di Gestione traffico basato sulla [priorità degli endpoint](traffic-manager-routing-methods.md#priority-traffic-routing-method). Il profilo indirizza il traffico utente al sito primario che esegue l'applicazione Web. Gestione traffico monitora continuamente l'applicazione Web. Se il sito primario non è disponibile, fornisce il failover automatico al sito di backup.

Se non si ha una sottoscrizione di Azure, creare ora un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Prerequisiti

Per questo avvio rapido, saranno necessarie due istanze di un'applicazione Web distribuita in due aree di Azure diverse ( *Stati Uniti orientali* ed *Europa occidentale* ). Ognuna verrà usata come endpoint primario e di failover per Gestione traffico.

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Nell'angolo in alto a sinistra dello schermo selezionare **Crea una risorsa** . Cercare **App Web** e selezionare **Crea** .

1. In **Crea un'app Web** digitare o selezionare i valori seguenti nella scheda **Informazioni di base** :

    | Impostazione                 | Valore |
    | ---                     | --- |
    | Subscription            | Selezionare la propria sottoscrizione. |    
    | Resource group          | Selezionare **Crea nuovo** e immettere *myResourceGroupTM1* nella casella di testo.|
    | Name                    | Immettere un **Nome** univoco per l'app Web. Questo esempio usa *myWebAppEastUS* . |
    | Pubblica                 | Selezionare **Codice** . |
    | Stack di runtime           | Selezionare **ASP.NET V4.7** . |
    | Sistema operativo        | Selezionare **Windows** . |
    | Region                  | Selezionare **Stati Uniti orientali** . |
    | Piano Windows            | Selezionare **Crea nuovo** e immettere *myAppServicePlanEastUS* nella casella di testo. |
    | SKU e dimensioni            | Selezionare **Standard S1 100 ACU totali, 1,75 GB di memoria** . |
   
1. Selezionare la scheda **Monitoraggio** oppure selezionare **Avanti: Monitoraggio** .  In **Monitoraggio** impostare **Application Insights** > **Abilita Application Insights** su **No** .

1. Selezionare **Rivedi e crea** .

1. Rivedere le impostazioni e quindi selezionare **Crea** .  Quando l'app Web viene distribuita correttamente, crea un sito Web predefinito.

1. Seguire i passaggi 1-6 per creare una seconda app Web denominata *myWebAppWestEurope* . Il nome di **Gruppo di risorse** è *myResourceGroupTM2* , con **Area** *Europa occidentale* e nome di **Piano di servizio app** **myAppServicePlanWestEurope** . Tutte le altre impostazioni sono uguali a *myWebAppEastUS* .

## <a name="create-a-traffic-manager-profile"></a>Creare un profilo di Gestione traffico

Creare un profilo di Gestione traffico che indirizza il traffico utente in base alla priorità degli endpoint.

1. Nell'angolo in alto a sinistra dello schermo selezionare **Crea una risorsa** . Cercare quindi **Profilo di Gestione traffico** e selezionare **Crea** .
1. In **Crea profilo di Gestione traffico** immettere o selezionare queste impostazioni:

    | Impostazione | Valore |
    | --------| ----- |
    | Nome | Immettere un nome univoco per il profilo di Gestione traffico.|
    | Metodo di routing | Selezionare **Priorità** .|
    | Subscription | Selezionare la sottoscrizione a cui si vuole applicare il profilo di Gestione traffico. |
    | Resource group | Selezionare *myResourceGroupTM1* .|
    | Location |Questa impostazione si riferisce alla località del gruppo di risorse. Non ha alcun effetto sul profilo di Gestione traffico che verrà distribuito a livello globale.|

1. Selezionare **Create** (Crea).

## <a name="add-traffic-manager-endpoints"></a>Aggiungere endpoint di Gestione traffico

Aggiungere il sito Web nell'area *Stati Uniti orientali* come endpoint primario per instradare tutto il traffico utente. Aggiungere il sito Web nell'area *Europa occidentale* come endpoint di failover. Quando l'endpoint primario non è disponibile, il traffico viene instradato automaticamente all'endpoint di failover.

1. Nella barra di ricerca del portale immettere il nome del profilo di Gestione traffico creato nella sezione precedente.
1. Selezionare il profilo dai risultati della ricerca.
1. In **Profilo di Gestione traffico** selezionare **Endpoint** e quindi **Aggiungi** nella sezione **Impostazioni** .

    :::image type="content" source="./media/quickstart-create-traffic-manager-profile/traffic-manager-endpoint-menu.png" alt-text="Impostazioni degli endpoint nel profilo di Gestione traffico":::

1. Immettere o selezionare queste impostazioni:

    | Impostazione | Valore |
    | ------- | ------|
    | Tipo | Selezionare **Endpoint Azure** . |
    | Name | Immettere *myPrimaryEndpoint* . |
    | Tipo di risorsa di destinazione | Selezionare **Servizio app** . |
    | Risorsa di destinazione | Selezionare **Scegliere un servizio app** > **Stati Uniti orientali** . |
    | Priorità | Selezionare **1** . Tutto il traffico passa a questo endpoint quando è integro. |

    :::image type="content" source="./media/quickstart-create-traffic-manager-profile/add-traffic-manager-endpoint.png" alt-text="Impostazioni degli endpoint nel profilo di Gestione traffico":::
    
1. Selezionare **OK** .
1. Per creare un endpoint di failover per la seconda area di Azure, ripetere i passaggi 3 e 4 con queste impostazioni:

    | Impostazione | Valore |
    | ------- | ------|
    | Tipo | Selezionare **Endpoint Azure** . |
    | Name | Immettere *myFailoverEndpoint* . |
    | Tipo di risorsa di destinazione | Selezionare **Servizio app** . |
    | Risorsa di destinazione | Selezionare **Scegliere un servizio app** > **Europa occidentale** . |
    | Priorità | Selezionare **2** . Tutto il traffico passa a questo endpoint di failover, se l'endpoint primario non è integro. |

1. Selezionare **OK** .

I due endpoint, dopo essere stati aggiunti, vengono visualizzati in **Profilo di Gestione traffico** . Si noti che ora lo stato del monitoraggio è **Online** .

## <a name="test-traffic-manager-profile"></a>Testare il profilo di Gestione traffico

In questa sezione, si controllerà il nome di dominio del profilo di Gestione traffico. Si configurerà anche l'endpoint primario come non disponibile. Si osserverà infine che l'app Web è ancora disponibile. Gestione traffico infatti invia il traffico all'endpoint di failover.

### <a name="check-the-dns-name"></a>Controllare il nome DNS

1. Nella barra di ricerca del portale cercare il nome del **Profilo di Gestione traffico** creato nella sezione precedente.
1. Selezionare il profilo di Gestione traffico. Viene visualizzata la **panoramica** .
1. Il **Profilo di Gestione traffico** visualizza il nome DNS del profilo di Gestione traffico appena creato.
  
    :::image type="content" source="./media/quickstart-create-traffic-manager-profile/traffic-manager-dns-name.png" alt-text="Impostazioni degli endpoint nel profilo di Gestione traffico":::

### <a name="view-traffic-manager-in-action"></a>Visualizzare Gestione traffico in azione

1. In un Web browser immettere il nome DNS del profilo di Gestione traffico per visualizzare il sito Web predefinito dell'app Web.

    > [!NOTE]
    > Nello scenario di questo avvio rapido tutte le richieste vengono instradate all'endpoint primario, che è impostato su **Priorità 1** .

    :::image type="content" source="./media/quickstart-create-traffic-manager-profile/traffic-manager-test.png" alt-text="Impostazioni degli endpoint nel profilo di Gestione traffico":::

1. Per visualizzare il failover di Gestione traffico in azione, disabilitare il sito primario:
    1. Nella pagina Profilo di Gestione traffico, nella sezione **Panoramica** selezionare **myPrimaryEndpoint** .
    1. In *myPrimaryEndpoint* selezionare **Disabilitato** > **Salva** .
    1. Chiudere **myPrimaryEndpoint** . Si noti che lo stato è ora **Disabilitato** .
1. Copiare il nome DNS del profilo di Gestione traffico del passaggio precedente per visualizzare il sito Web in una nuova sessione del Web browser.
1. Verificare che l'app Web sia ancora disponibile.

L'endpoint primario non è disponibile, quindi si viene indirizzati all'endpoint di failover.

## <a name="clean-up-resources"></a>Pulire le risorse

Al termine, eliminare i gruppi di risorse, le applicazioni Web e tutte le risorse correlate. A questo scopo, selezionare ogni singolo elemento dal dashboard e selezionare **Elimina** nella parte superiore di ogni pagina.

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido è stato creato un profilo di Gestione traffico che consente di indirizzare il traffico utente per le applicazioni Web a disponibilità elevata. Per altre informazioni su come instradare il traffico, passare alle esercitazioni di Gestione traffico.

> [!div class="nextstepaction"]
> [Esercitazioni di Gestione traffico](tutorial-traffic-manager-improve-website-response.md)
