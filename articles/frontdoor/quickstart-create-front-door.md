---
title: 'Avvio rapido: Configurare la disponibilità elevata con il servizio Frontdoor di Azure - Portale di Azure'
description: Questo argomento di avvio rapido mostra come usare il servizio Frontdoor di Azure per le applicazioni Web globali con disponibilità e prestazioni elevate mediante il portale di Azure.
services: front-door
documentationcenter: na
author: duongau
manager: KumudD
Customer intent: As an IT admin, I want to direct user traffic to ensure high availability of web applications.
ms.service: frontdoor
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/16/2020
ms.author: duau
ms.openlocfilehash: 4846438f8479fe622570aa515a4d8b40cccc57b8
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91252307"
---
# <a name="quickstart-create-a-front-door-for-a-highly-available-global-web-application"></a>Guida introduttiva: Creare una frontdoor per un'applicazione Web globale a disponibilità elevata

Per iniziare a usare Frontdoor di Azure, configurare la disponibilità elevata per un'applicazione Web tramite il portale di Azure.

In questo argomento di avvio rapido Frontdoor di Azure raggruppa due istanze di un'applicazione Web eseguite in aree di Azure diverse. La configurazione di Frontdoor viene creata in base a back-end di peso uguale e con la stessa priorità. Questa configurazione indirizza il traffico al sito più vicino che esegue l'applicazione. Frontdoor di Azure monitora continuamente l'applicazione Web. Il servizio fornisce il failover automatico al sito successivo disponibile quando il sito più vicino non è disponibile.

## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-two-instances-of-a-web-app"></a>Creare due istanze di un'app Web

Per questo argomento di avvio rapido, è necessario creare due istanze di un'applicazione Web eseguite in aree di Azure diverse. Entrambe le istanze dell'applicazione Web vengono eseguite in modalità *attiva/attiva*, in modo una possa acquisire traffico. Questa configurazione è diversa da una configurazione *attiva/standby*, in cui un'istanza agisce da failover.

Se non è già disponibile un'app Web, seguire questa procedura per configurarne una di esempio.

1. Accedere al portale di Azure all'indirizzo https://portal.azure.com.

1. Nell'angolo in alto a sinistra dello schermo selezionare **Crea una risorsa** >  **App Web**.

    :::image type="content" source="media/quickstart-create-front-door/front-door-create-web-app.png" alt-text="Creare un'app Web nel portale di Azure":::

1. Nella scheda **Informazioni di base** della pagina **Crea app Web** immettere o selezionare le informazioni seguenti.

    | Impostazione                 | Valore                                              |
    | ---                     | ---                                                |
    | **Sottoscrizione**               | Selezionare la propria sottoscrizione. |    
    | **Gruppo di risorse**       | Selezionare **Crea nuovo** e immettere *FrontDoorQS_rg1* nella casella di testo.|
    | **Nome**                   | Immettere un **Nome** univoco per l'app Web. Questo esempio usa *WebAppContoso-1*. |
    | **Pubblica** | Selezionare **Codice**. |
    | **Stack di runtime**         | Selezionare **.NET Core 2.1 (LTS)** . |
    | **Sistema operativo**          | Selezionare **Windows**. |
    | **Area**           | Selezionare **Stati Uniti centrali**. |
    | **Piano Windows** | Selezionare **Crea nuovo** e immettere *myAppServicePlanCentralUS* nella casella di testo. |
    | **SKU e dimensioni** | Selezionare **Standard S1 100 ACU totali, 1,75 GB di memoria**. |

1. Selezionare **Rivedi e crea**, esaminare la scheda **Riepilogo** e quindi selezionare **Crea**. Il completamento della distribuzione potrebbe richiedere diversi minuti.

    :::image type="content" source="media/quickstart-create-front-door/create-web-app.png" alt-text="Creare un'app Web nel portale di Azure":::

Al termine della distribuzione, creare una seconda app Web. Seguire la stessa procedura con gli stessi valori, ad eccezione dei valori seguenti:

| Impostazione          | valore     |
| ---              | ---  |
| **Gruppo di risorse**   | Selezionare **Crea nuovo** e immettere *FrontDoorQS_rg2* |
| **Nome**             | Immettere un nome univoco per l'app Web, in questo esempio *WebAppContoso-2*  |
| **Area**           | Scegliere un'area diversa, in questo esempio *Stati Uniti centro-meridionali* |
| **Piano di servizio app** > **Piano Windows**         | Selezionare **Nuovo**, immettere *myAppServicePlanSouthCentralUS*, quindi selezionare **OK** |

## <a name="create-a-front-door-for-your-application"></a>Creare una frontdoor per l'applicazione

Configurare Frontdoor di Azure per indirizzare il traffico degli utenti in base alla latenza più bassa tra i due server di app Web. Per iniziare, aggiungere un host front-end per Frontdoor di Azure.

1. Nella home page o nel menu di Azure selezionare **Crea una risorse**. Selezionare **Rete** > **Visualizza tutto** > **Frontdoor**.

1. Nella scheda **Informazioni di base** della pagina **Crea una frontdoor** immettere o selezionare le informazioni seguenti e quindi selezionare **Avanti: Configurazione**.

    | Impostazione | Valore |
    | --- | --- |
    | **Sottoscrizione** | Selezionare la propria sottoscrizione. |    
    | **Gruppo di risorse** | Selezionare **Crea nuovo** e immettere *FrontDoorQS_rg0* nella casella di testo.|
    | **Località del gruppo di risorse** | Selezionare **Stati Uniti centrali**. |

1. In **Front-end/domini** selezionare **+** per aprire **Aggiungi un host front-end**.

1. Per **Nome host**, immettere un nome host univoco globale. Questo esempio usa *contoso-frontend*. Selezionare **Aggiungi**.

    :::image type="content" source="media/quickstart-create-front-door/add-frontend-host-azure-front-door.png" alt-text="Creare un'app Web nel portale di Azure":::

Successivamente, creare un pool back-end che contenga le due app Web.

1. Sempre in **Crea una frontdoor**, in **Pool back-end**, selezionare **+** per aprire **Aggiungi un pool back-end**.

1. In **Nome** immettere *myBackendPool*, quindi selezionare **Aggiungi un back-end**.

    :::image type="content" source="media/quickstart-create-front-door/front-door-add-backend-pool.png" alt-text="Creare un'app Web nel portale di Azure":::

1. Nel pannello **Aggiungi un back-end** selezionare le informazioni seguenti e quindi selezionare **Aggiungi**.

    | Impostazione | Valore |
    | --- | --- |
    | **Tipo di host back-end** | Selezionare **Servizio app**. |   
    | **Sottoscrizione** | Selezionare la propria sottoscrizione. |    
    | **Nome host back-end** | Selezionare la prima app Web creata. In questo esempio l'app Web è *WebAppContoso-1*. |

    **Lasciare i valori predefiniti per tutti gli altri campi.*

    :::image type="content" source="media/quickstart-create-front-door/front-door-add-a-backend.png" alt-text="Creare un'app Web nel portale di Azure":::

1. Selezionare di nuovo **Aggiungi un back-end**. Selezionare le informazioni seguenti e quindi selezionare **Aggiungi**.

    | Impostazione | Valore |
    | --- | --- |
    | **Tipo di host back-end** | Selezionare **Servizio app**. |   
    | **Sottoscrizione** | Selezionare la propria sottoscrizione. |    
    | **Nome host back-end** | Selezionare la seconda app Web creata. In questo esempio l'app Web è *WebAppContoso-2*. |

    **Lasciare i valori predefiniti per tutti gli altri campi.*

1. Selezionare **Aggiungi** nel pannello **Aggiungi un pool back-end** per completare la configurazione del pool back-end.

    :::image type="content" source="media/quickstart-create-front-door/front-door-add-backend-pool-complete.png" alt-text="Creare un'app Web nel portale di Azure":::

Infine, aggiungere una regola di routing. Una regola di routing esegue il mapping dell'host front-end al pool back-end. La regola inoltra una richiesta di `contoso-frontend.azurefd.net` a **myBackendPool**.

1. Sempre in **Crea una frontdoor**, in **Regole di routing**, selezionare **+** per configurare una regola di routing.

1. In **Aggiungi una regola**, per **Nome**, immettere *LocationRule*. Accettare tutti i valori predefiniti, quindi selezionare **Aggiungi** per aggiungere la regola di routing.

    :::image type="content" source="media/quickstart-create-front-door/front-door-add-a-rule.png" alt-text="Creare un'app Web nel portale di Azure":::

   >[!WARNING]
   > È **necessario** assicurarsi che ognuno degli host front-end nella frontdoor abbia una regola di routing con un percorso predefinito (`\*`) associato. Questo vuol dire che tra tutte le regole di gestione deve esisterne almeno una per ognuno degli host front-end definita nel percorso predefinito (`\*`). In caso contrario, è possibile che il traffico degli utenti finali non venga instradato correttamente.

1. Selezionare **Rivedi e crea** e quindi **Crea**.

    :::image type="content" source="media/quickstart-create-front-door/configuration-azure-front-door.png" alt-text="Creare un'app Web nel portale di Azure":::

## <a name="view-azure-front-door-in-action"></a>Visualizzare Frontdoor di Azure in azione

Dopo aver creato una frontdoor, occorreranno alcuni minuti perché la configurazione venga distribuita a livello globale. Al termine, accedere all'host front-end creato. In un browser passare all'indirizzo `contoso-frontend.azurefd.net`. La richiesta verrà automaticamente instradata al server più vicino tra quelli specificati nel pool back-end.

Se queste app sono state create in questo argomento di avvio rapido, verrà visualizzata una pagina di informazioni.

Per testare il failover globale istantaneo, provare a eseguire i passaggi seguenti:

1. Aprire un browser, come descritto sopra, e passare all'indirizzo del front-end `contoso-frontend.azurefd.net`.

1. Nel portale di Azure cercare e selezionare *Servizi app*. Scorrere verso il basso per trovare una delle app Web, in questo esempio **WebAppContoso-1**.

1. Selezionare l'app Web, quindi selezionare **Arresta**e **Sì** per verifica.

1. Aggiornare il browser. Si dovrebbe visualizzare la stessa pagina di informazioni.

   >[!TIP]
   >Per queste azioni si verifica un leggero ritardo. Può essere necessario eseguire di nuovo l'aggiornamento.

1. Trovare l'altra app Web e arrestarla.

1. Aggiornare il browser. Questa volta dovrebbe essere visualizzato un messaggio di errore.

   :::image type="content" source="media/quickstart-create-front-door/web-app-stopped-message.png" alt-text="Creare un'app Web nel portale di Azure":::

## <a name="clean-up-resources"></a>Pulire le risorse

Al termine, è possibile rimuovere tutti gli elementi creati. Con l'eliminazione di un gruppo di risorse viene eliminato anche il relativo contenuto. Se non si intende usare questa frontdoor, è consigliabile rimuovere le risorse pe evitare addebiti non necessari.

1. Nel portale di Azure cercare e selezionare **Gruppi di risorse** oppure scegliere **Gruppi di risorse** dal menu del portale di Azure.

1. Filtrare o scorrere verso il basso per trovare un gruppo di risorse, ad esempio **FrontDoorQS_rg0**.

1. Selezionare il gruppo di risorse e quindi **Elimina gruppo di risorse**.

   >[!WARNING]
   >Questa azione è irreversibile.

1. Digitare il nome del gruppo di risorse per verifica, quindi selezionare **Elimina**.

Ripetere la procedura per gli altri due gruppi.

## <a name="next-steps"></a>Passaggi successivi

Passare all'articolo successivo per informazioni su come aggiungere un dominio personalizzato alla frontdoor.
> [!div class="nextstepaction"]
> [Aggiungere un dominio personalizzato](front-door-custom-domain.md)
