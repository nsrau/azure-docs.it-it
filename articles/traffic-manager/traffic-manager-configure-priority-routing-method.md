---
title: 'Esercitazione: Configurare il routing del traffico prioritario con Gestione traffico di Azure'
description: Questa esercitazione spiega come configurare il metodo di routing del traffico Priorità in Gestione traffico
services: traffic-manager
documentationcenter: ''
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/16/2020
ms.author: duau
ms.openlocfilehash: 1835377f4690097c8390957bf7d897242ba7aace
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/19/2020
ms.locfileid: "92208057"
---
# <a name="tutorial-configure-priority-traffic-routing-method-in-traffic-manager"></a>Esercitazione: Configurare il metodo di routing del traffico Priorità di Gestione traffico

Questa esercitazione illustra come usare Gestione traffico di Azure per instradare il traffico degli utenti a specifici endpoint usando il metodo di routing prioritario. In questo metodo di routing viene definito l'ordine di ogni endpoint che rientra nella configurazione del profilo di Gestione traffico. Il traffico proveniente dagli utenti verrà instradato all'endpoint nell'ordine elencato. Questo metodo di routing è utile quando si vuole configurare il failover del servizio. L'endpoint primario ottiene il numero di priorità '1' e gestirà tutte le richieste in ingresso. Gli endpoint con priorità più bassa agiranno invece da backup.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> - Creare un profilo di Gestione traffico con routing prioritario.
> - Aggiungere gli endpoint.
> - Configurare la priorità degli endpoint.
> - Usare il profilo di Gestione traffico.
> - Eliminare il profilo di Gestione traffico.

## <a name="prerequisites"></a>Prerequisiti

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="to-configure-the-priority-traffic-routing-method"></a>Per configurare il metodo di routing del traffico Priorità
1. Da un browser accedere al [portale di Azure](https://portal.azure.com).

1. Selezionare **+ Crea una risorsa** a sinistra. Cercare **Profilo di Gestione traffico** e selezionare **Crea** .

    :::image type="content" source="./media/traffic-manager-priority-routing-method/create-traffic-manager-priority-profile.png" alt-text="Creare un profilo prioritario di Gestione traffico":::

1. Nella pagina *Crea profilo di Gestione traffico* definire le impostazioni seguenti:

    | Impostazione         | Valore                                              |
    | ---             | ---                                                |
    | Nome            | Specificare un nome per il profilo. Questo nome deve essere univoco all'interno della zona trafficmanager.net. Per accedere al profilo di Gestione traffico, usare il nome DNS `<profilename>.trafficmanager.net`. |    
    | Metodo di routing  | Selezionare **Priorità** . |
    | Subscription    | Selezionare la propria sottoscrizione. |
    | Resource group   | Usare un gruppo di risorse esistente oppure crearne uno nuovo per posizionare il profilo. Se si sceglie di creare un nuovo gruppo di risorse, usare il menu a discesa *Località del gruppo di risorse* per specificare la località del gruppo di risorse. Questa impostazione indica la località del gruppo di risorse e non ha alcun impatto sul profilo di Gestione traffico che viene distribuito a livello globale. |

1. Selezionare **Crea** per distribuire il profilo di Gestione traffico.

    :::image type="content" source="./media/traffic-manager-priority-routing-method/create-traffic-manager-profile-priority.png" alt-text="Creare un profilo prioritario di Gestione traffico":::

## <a name="add-endpoints"></a>Aggiungere endpoint

1. Selezionare il profilo di Gestione traffico nell'elenco.

    :::image type="content" source="./media/traffic-manager-priority-routing-method/traffic-manager-profile-list.png" alt-text="Creare un profilo prioritario di Gestione traffico":::

1. Selezionare **Endpoint** in *Impostazioni* , quindi selezionare **+ Aggiungi** per aggiungere un nuovo endpoint.

    :::image type="content" source="./media/traffic-manager-priority-routing-method/traffic-manager-add-endpoints.png" alt-text="Creare un profilo prioritario di Gestione traffico":::

1. Selezionare o immettere le impostazioni seguenti: 

    | Impostazione                | Valore                                              |
    | ---                    | ---                                                |
    | Tipo                   | Selezionare il tipo di endpoint. |    
    | Name                   | Assegnare un nome per identificare questo endpoint. |
    | Tipo di risorsa di destinazione   | Selezionare il tipo di risorsa per la destinazione. |
    | Risorsa di destinazione        | Selezionare la risorsa nell'elenco. |
    | Priorità               | Assegnare un numero di priorità a questo endpoint. 1 è la priorità più alta. |


1. Selezionare **Aggiungi** per aggiungere l'endpoint. Ripetere i passaggi 2 e 3 per aggiungere altri endpoint. Assicurarsi di impostare il numero di priorità appropriato.

    :::image type="content" source="./media/traffic-manager-priority-routing-method/add-endpoint.png" alt-text="Creare un profilo prioritario di Gestione traffico":::

1. Nella pagina **Endpoint** esaminare l'ordine di priorità per gli endpoint. Quando si seleziona il metodo di routing del traffico **Priorità** , l'ordine degli endpoint selezionati è rilevante. Verificare l'ordine di priorità degli endpoint.  L'endpoint primario si trova nella parte superiore. Controllare l'ordine visualizzato. Tutte le richieste verranno instradate al primo endpoint. Se Gestione traffico rileva che non è integro, verrà automaticamente eseguito il failover del traffico all'endpoint successivo. 

    :::image type="content" source="./media/traffic-manager-priority-routing-method/endpoints-list.png" alt-text="Creare un profilo prioritario di Gestione traffico":::

1. Per cambiare l'ordine di priorità di un endpoint, selezionarlo, cambiare il valore della priorità, quindi selezionare **Salva** per salvare le impostazioni.

## <a name="use-the-traffic-manager-profile"></a>Usare il profilo di Gestione traffico

1.  Nella barra di ricerca del portale cercare il nome del **profilo di Gestione traffico** creato nella sezione precedente e selezionarlo nei risultati visualizzati.

    :::image type="content" source="./media/traffic-manager-priority-routing-method/search-traffic-manager-profile.png" alt-text="Creare un profilo prioritario di Gestione traffico":::

1.  La pagina di panoramica **Profilo di Gestione traffico** visualizza il nome DNS del profilo di Gestione traffico appena creato. Questo nome può essere usato da qualsiasi client (ad esempio raggiungendolo tramite un Web browser) che deve essere indirizzato all'endpoint corretto in base al tipo di routing. In questo caso, tutte le richieste vengono instradate al primo endpoint. Se Gestione traffico rileva che non è integro, viene eseguito automaticamente il failover del traffico sull'endpoint successivo.

    :::image type="content" source="./media/traffic-manager-priority-routing-method/traffic-manager-profile-dns-name.png" alt-text="Creare un profilo prioritario di Gestione traffico":::

1. Dopo aver verificato il funzionamento del profilo di Gestione traffico, modificare il record DNS sul server DNS autorevole per fare in modo che il nome del dominio aziendale punti al nome di dominio di Gestione traffico.

## <a name="clean-up-resources"></a>Pulire le risorse

Se il profilo di Gestione traffico non è più necessario, individuarlo e selezionare **Elimina profilo** .

:::image type="content" source="./media/traffic-manager-priority-routing-method/traffic-manager-delete-priority-profile.png" alt-text="Creare un profilo prioritario di Gestione traffico":::

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul metodo di routing prioritario, vedere:

> [!div class="nextstepaction"]
> [Metodo di routing prioritario](traffic-manager-routing-methods.md#priority-traffic-routing-method)
