---
title: Configurare il metodo di routing del traffico multivalore in Gestione traffico di Azure
description: Questo articolo descrive come configurare Gestione traffico per instradare il traffico agli endpoint A/AAAA.
services: traffic-manager
documentationcenter: ''
author: asudbring
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: allensu
ms.openlocfilehash: 5db8e2932a43a2d6c6cb8a99c4f32b37a4a5a3f8
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67050922"
---
# <a name="configure-multivalue-routing-method-in-traffic-manager"></a>Configurare il metodo di routing multivalore in Gestione traffico

Questo articolo descrive come configurare il metodo di routing del traffico multivalore. Il metodo di routing del traffico **multivalore** consente di restituire più endpoint integri e di aumentare l'affidabilità dell'applicazione, poiché i client hanno più opzioni per riprovare senza dover eseguire un'altra ricerca DNS. Il routing multivalore è abilitato solo per i profili i cui endpoint vengono tutti specificati usando indirizzi IPv4 o IPv6. Quando si riceve una query per questo profilo, tutti gli endpoint integri vengono restituiti in base al numero massimo di restituzioni configurabili specificato. 

>[!NOTE]
> In questa fase l'aggiunta di endpoint con indirizzi IPv4 o IPv6 è supportata solo per gli endpoint di tipo **esterno** quindi il routing multivalore è anche supportato solo per tali endpoint.

## <a name="sign-in-to-azure"></a>Accedi ad Azure 

Accedere al portale di Azure all'indirizzo https://portal.azure.com.
## <a name="create-a-resource-group"></a>Creare un gruppo di risorse
Creare un gruppo di risorse per il profilo di Gestione traffico.
1. Nel riquadro sinistro del portale di Azure selezionare **Gruppi di risorse**.
2. Nella parte superiore della pagina selezionare **Aggiungi** in **Gruppi di risorse**.
3. In **Nome gruppo di risorse** digitare il nome *myResourceGroupTM1*. Per **Località del gruppo di risorse** selezionare **Stati Uniti orientali**, quindi selezionare **OK**.

## <a name="create-a-traffic-manager-profile"></a>Creare un profilo di Gestione traffico
Creare un profilo di Gestione traffico che indirizza il traffico degli utenti verso l'endpoint con latenza più bassa.

1. In alto a sinistra nello schermo selezionare **Crea una risorsa** > **Rete** > **Profilo di Gestione traffico** > **Crea**.
2. In **Crea profilo di Gestione traffico** immettere o selezionare le informazioni seguenti, accettare i valori predefiniti per le impostazioni rimanenti e quindi selezionare **Crea**:
    
    | Impostazione                 | Valore                                              |
    | ---                     | ---                                                |
    | Name                   | Questo nome deve essere univoco all'interno della zona trafficmanager.net e determina il nome DNS, trafficmanager.net, che viene usato per accedere al profilo di Gestione traffico.                                   |
    | Metodo di routing          | Selezionare il metodo di routing **multivalore**.                                       |
    | Sottoscrizione            | Selezionare la propria sottoscrizione.                          |
    | Gruppo di risorse          | Selezionare *myResourceGroupTM1*. |
    | Località                | Questa impostazione indica la località del gruppo di risorse e non ha alcun impatto sul profilo di Gestione traffico che sarà distribuito a livello globale.                              |
   |        |           | 
  
   ![Creare un profilo di Gestione traffico](./media/traffic-manager-multivalue-routing-method/create-traffic-manager-profile.png)

## <a name="add-traffic-manager-endpoints"></a>Aggiungere endpoint di Gestione traffico

Aggiungere due indirizzi IP come endpoint esterni al profilo multivalore di Gestione traffico creato nel passaggio precedente.

1. Nella barra di ricerca del portale cercare il nome del profilo di Gestione traffico creato nella sezione precedente e selezionarlo nei risultati visualizzati.
2. In **Profilo di Gestione traffico**, nella sezione **Impostazioni**, fare clic su **Endpoint** e quindi su **Aggiungi**.
3. Immettere o selezionare le informazioni seguenti, accettare le impostazioni predefinite rimanenti e quindi scegliere **OK**:

    | Impostazione                 | Value                                              |
    | ---                     | ---                                                |
    | Type                    | Endpoint esterno                                   |
    | NOME           | myEndpoint1                                        |
    | Nome di dominio completo (FQDN) o IP           | Digitare l'indirizzo IP pubblico dell'endpoint che si vuole aggiungere a questo profilo di Gestione traffico                         |
    |        |           |

4. Ripetere i passaggi 2 e 3 per aggiungere un altro endpoint denominato *myEndpoint2*, per il **nome di dominio completo (FQDN) o IP** immettere l'indirizzo IP pubblico del secondo endpoint.
5. Una volta completata l'aggiunta di entrambi gli endpoint, essi vengono visualizzati in **Profilo di Gestione traffico** insieme al relativo stato di monitoraggio **Online**.

   ![Aggiungere un endpoint di Gestione traffico](./media/traffic-manager-multivalue-routing-method/add-endpoint.png)
 
## <a name="next-steps"></a>Passaggi successivi

- Informazioni sul [metodo di routing del traffico Ponderato](traffic-manager-configure-weighted-routing-method.md).
- Informazioni sul [metodo di routing Priorità](traffic-manager-configure-priority-routing-method.md).
- Altre informazioni sul [metodo di routing Prestazioni](traffic-manager-configure-performance-routing-method.md)
- Informazioni sul [metodo di routing Geografico](traffic-manager-configure-geographic-routing-method.md).


