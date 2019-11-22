---
title: 'Avvio rapido: Creare un profilo per la disponibilità elevata delle applicazioni - Portale di Azure - Gestione traffico di Azure'
description: Questa guida introduttiva descrive come creare un profilo di Gestione traffico per creare applicazioni Web a disponibilità elevata.
services: traffic-manager
author: asudbring
manager: twooley
Customer intent: As an IT admin, I want to direct user traffic to ensure high availability of web applications.
ms.service: traffic-manager
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/28/2018
ms.author: allensu
ms.openlocfilehash: b6a015c7462f082d5114a51d83cfacb0c550b026
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74032706"
---
# <a name="quickstart-create-a-traffic-manager-profile-using-the-azure-portal"></a>Guida introduttiva: Creare un profilo di Gestione traffico usando il portale di Azure

Questo argomento di avvio rapido descrive come creare un profilo di Gestione traffico che fornisce disponibilità elevata per l'applicazione Web.

In questo argomento di avvio rapido verranno illustrate due istanze di un'applicazione Web. Ognuna di esse è in esecuzione in un'area di Azure diversa. Si creerà un profilo di Gestione traffico basato sulla [priorità degli endpoint](traffic-manager-routing-methods.md#priority). Il profilo indirizza il traffico utente al sito primario che esegue l'applicazione Web. Gestione traffico monitora continuamente l'applicazione Web. Se il sito primario non è disponibile, fornisce il failover automatico al sito di backup.

Se non si ha una sottoscrizione di Azure, creare ora un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al [portale di Azure](https://portal.azure.com).

## <a name="prerequisites"></a>Prerequisiti

Per questo avvio rapido, saranno necessarie due istanze di un'applicazione Web distribuita in due aree di Azure diverse (*Stati Uniti orientali* ed *Europa occidentale*). Ognuna verrà usata come endpoint primario e di failover per Gestione traffico.

1. In alto a sinistra nella schermata selezionare **Crea una risorsa** > **Web** > **App Web**.

1. In **Crea un'app Web** digitare o selezionare i valori seguenti nella scheda **Informazioni di base**:

   - **Sottoscrizione** > **Gruppo di risorse**: Selezionare **Crea nuovo** e quindi digitare **myResourceGroupTM1**.
   - **Dettagli dell'istanza** > **Nome**: Digitare *myWebAppEastUS*.
   - **Dettagli dell'istanza** > **Pubblica**: Selezionare **Codice**.
   - **Dettaglia dell'istanza** > **Stack di runtime**: Selezionare **ASP.NET V4.7**
   - **Dettagli dell'istanza** > **Sistema operativo**: Selezionare **Windows**.
   - **Dettagli istanza** > **Area**:  Selezionare **Stati Uniti orientali**.
   - **Piano di servizio app** > **Piano Windows (Stati Uniti orientali)** : Selezionare **Crea nuovo** e quindi digitare **myAppServicePlanEastUS**
   - **Piano di servizio app** > **SKU e dimensioni**: Selezionare **Standard S1**.
   
3. Selezionare la scheda **Monitoraggio** oppure selezionare **Passaggio successivo: Monitoraggio**.  In **Monitoraggio** impostare **Application Insights** > **Abilita Application Insights** su **No**.

4. Selezionare **Rivedi e crea**.

5. Rivedere le impostazioni e fare clic su **Crea**.  Quando l'app Web viene distribuita correttamente, crea un sito Web predefinito.

6. Seguire i passaggi per creare una seconda app Web denominata *myWebAppWestEurope*, assegnando al **Gruppo di risorse** il nome *myResourceGroupTM2*, impostando **Località** su *Europa occidentale*, specificando il nome **myAppServicePlanWestEurope** per **Piano di servizio app** e scegliendo gli stessi valori di *myWebAppEastUS* per tutte le altre impostazioni.

## <a name="create-a-traffic-manager-profile"></a>Creare un profilo di Gestione traffico

Creare un profilo di Gestione traffico che indirizza il traffico utente in base alla priorità degli endpoint.

1. In alto a sinistra nella schermata selezionare **Crea una risorsa** > **Rete** > **Profilo di Gestione traffico**.
2. In **Crea profilo di Gestione traffico** immettere o selezionare queste impostazioni:

    | Impostazione | Valore |
    | --------| ----- |
    | NOME | Immettere un nome univoco per il profilo di Gestione traffico.|
    | Metodo di routing | Selezionare **Priorità**.|
    | Subscription | Selezionare la sottoscrizione a cui si vuole applicare il profilo di Gestione traffico. |
    | Resource group | Selezionare *myResourceGroupTM1*.|
    | Location |Questa impostazione si riferisce alla località del gruppo di risorse. Non ha alcun effetto sul profilo di Gestione traffico che verrà distribuito a livello globale.|

3. Selezionare **Create** (Crea).

## <a name="add-traffic-manager-endpoints"></a>Aggiungere endpoint di Gestione traffico

Aggiungere il sito Web nell'area *Stati Uniti orientali* come endpoint primario per instradare tutto il traffico utente. Aggiungere il sito Web nell'area *Europa occidentale* come endpoint di failover. Quando l'endpoint primario non è disponibile, il traffico viene instradato automaticamente all'endpoint di failover.

1. Nella barra di ricerca del portale immettere il nome del profilo di Gestione traffico creato nella sezione precedente.
2. Selezionare il profilo dai risultati della ricerca.
3. In **Profilo di Gestione traffico** selezionare **Endpoint** e quindi **Aggiungi** nella sezione **Impostazioni**.
4. Immettere o selezionare queste impostazioni:

    | Impostazione | Valore |
    | ------- | ------|
    | Type | Selezionare **Endpoint Azure**. |
    | NOME | Immettere *myPrimaryEndpoint*. |
    | Tipo di risorsa di destinazione | Selezionare **Servizio app**. |
    | Risorsa di destinazione | Selezionare **Scegliere un servizio app** > **Stati Uniti orientali**. |
    | Priorità | Selezionare **1**. Tutto il traffico passa a questo endpoint quando è integro. |

    ![Screenshot che illustra dove si aggiunge un endpoint al profilo di Gestione traffico.](./media/quickstart-create-traffic-manager-profile/add-traffic-manager-endpoint.png)

5. Selezionare **OK**.
6. Per creare un endpoint di failover per la seconda area di Azure, ripetere i passaggi 3 e 4 con queste impostazioni:

    | Impostazione | Valore |
    | ------- | ------|
    | Type | Selezionare **Endpoint Azure**. |
    | NOME | Immettere *myFailoverEndpoint*. |
    | Tipo di risorsa di destinazione | Selezionare **Servizio app**. |
    | Risorsa di destinazione | Selezionare **Scegliere un servizio app** > **Europa occidentale**. |
    | Priorità | Selezionare **2**. Tutto il traffico passa a questo endpoint di failover, se l'endpoint primario non è integro. |

7. Selezionare **OK**.

I due endpoint, dopo essere stati aggiunti, vengono visualizzati in **Profilo di Gestione traffico**. Si noti che ora lo stato del monitoraggio è **Online**.

## <a name="test-traffic-manager-profile"></a>Testare il profilo di Gestione traffico

In questa sezione, si controllerà il nome di dominio del profilo di Gestione traffico. Si configurerà anche l'endpoint primario come non disponibile. Si osserverà infine che l'app Web è ancora disponibile. Gestione traffico infatti invia il traffico all'endpoint di failover.

### <a name="check-the-dns-name"></a>Controllare il nome DNS

1. Nella barra di ricerca del portale cercare il nome del **Profilo di Gestione traffico** creato nella sezione precedente.
2. Selezionare il profilo di Gestione traffico. Viene visualizzata la **panoramica**.
3. Il **Profilo di Gestione traffico** visualizza il nome DNS del profilo di Gestione traffico appena creato.
  
   ![Screenshot della posizione del nome DNS di Gestione traffico](./media/quickstart-create-traffic-manager-profile/traffic-manager-dns-name.png)

### <a name="view-traffic-manager-in-action"></a>Visualizzare Gestione traffico in azione

1. In un Web browser immettere il nome DNS del profilo di Gestione traffico per visualizzare il sito Web predefinito dell'app Web.

    > [!NOTE]
    > Nello scenario di questo avvio rapido tutte le richieste vengono instradate all'endpoint primario, che è impostato su **Priorità 1**.

    ![Screenshot della pagina Web di conferma della disponibilità del profilo di Gestione traffico](./media/quickstart-create-traffic-manager-profile/traffic-manager-test.png)

2. Per visualizzare il failover di Gestione traffico in azione, disabilitare il sito primario:
    1. Nella pagina Profilo di Gestione traffico, nella sezione **Panoramica** selezionare **myPrimaryEndpoint**.
    2. In *myPrimaryEndpoint* selezionare **Disabilitato** > **Salva**.
    3. Chiudere **myPrimaryEndpoint**. Si noti che lo stato è ora **Disabilitato**.
3. Copiare il nome DNS del profilo di Gestione traffico del passaggio precedente per visualizzare il sito Web in una nuova sessione del Web browser.
4. Verificare che l'app Web sia ancora disponibile.

L'endpoint primario non è disponibile, quindi si viene indirizzati all'endpoint di failover.

## <a name="clean-up-resources"></a>Pulire le risorse

Al termine, eliminare i gruppi di risorse, le applicazioni Web e tutte le risorse correlate. A questo scopo, selezionare ogni singolo elemento dal dashboard e selezionare **Elimina** nella parte superiore di ogni pagina.

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido è stato creato un profilo di Gestione traffico che consente di indirizzare il traffico utente per le applicazioni Web a disponibilità elevata. Per altre informazioni su come instradare il traffico, passare alle esercitazioni di Gestione traffico.

> [!div class="nextstepaction"]
> [Esercitazioni di Gestione traffico](tutorial-traffic-manager-improve-website-response.md)
