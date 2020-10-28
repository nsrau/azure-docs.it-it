---
title: 'Esercitazione: Configurare il routing del traffico geografico con Gestione traffico di Azure'
description: Questa esercitazione spiega come configurare il metodo di routing del traffico geografico tramite Gestione traffico di Azure
services: traffic-manager
author: duongau
manager: kumudD
ms.service: traffic-manager
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/15/2020
ms.author: duau
ms.openlocfilehash: 29b3cdde328a994e5806df810db15b529a6da9af
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/19/2020
ms.locfileid: "92208114"
---
# <a name="tutorial-configure-the-geographic-traffic-routing-method-using-traffic-manager"></a>Esercitazione: Configurare il metodo di routing del traffico Geografico tramite Gestione traffico

Il metodo di routing del traffico Geografico consente di indirizzare il traffico a specifici endpoint in base alla posizione geografica da cui ha origine la richiesta. In questa esercitazione viene illustrato come creare un profilo di Gestione traffico con questo metodo di routing e come configurare gli endpoint in modo da ricevere il traffico da aree geografiche specifiche.

In questa esercitazione verranno illustrate le procedure per:
> [!div class="checklist"]
> - Creare un profilo di Gestione traffico con routing geografico.
> - Configurare un endpoint annidato.
> - Usare il profilo di Gestione traffico.
> - Eliminare il profilo di Gestione traffico.

## <a name="prerequisites"></a>Prerequisiti

* Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-traffic-manager-profile"></a>Creazione di un profilo di Gestione traffico

1. Da un browser accedere al [portale di Azure](https://portal.azure.com).

1. Selezionare **+ Crea una risorsa** a sinistra. Cercare **Profilo di Gestione traffico** e selezionare **Crea** .

    :::image type="content" source="./media/traffic-manager-geographic-routing-method/create-traffic-manager.png" alt-text="Creare un profilo di Gestione traffico":::

1. Nella pagina *Crea profilo di Gestione traffico* definire le impostazioni seguenti:

    | Impostazione         | Valore                                              |
    | ---             | ---                                                |
    | Nome            | Specificare un nome per il profilo. Questo nome deve essere univoco all'interno della zona trafficmanager.net. Per accedere al profilo di Gestione traffico, usare il nome DNS `<profilename>.trafficmanager.net`. |    
    | Metodo di routing  | Selezionare **Geografico** . |
    | Subscription    | Selezionare la propria sottoscrizione. |
    | Resource group   | Usare un gruppo di risorse esistente oppure crearne uno nuovo per posizionare il profilo. Se si sceglie di creare un nuovo gruppo di risorse, usare il menu a discesa *Località del gruppo di risorse* per specificare la località del gruppo di risorse. Questa impostazione indica la località del gruppo di risorse e non ha alcun impatto sul profilo di Gestione traffico che viene distribuito a livello globale. |

1. Selezionare **Crea** per distribuire il profilo di Gestione traffico.

    :::image type="content" source="./media/traffic-manager-geographic-routing-method/create-traffic-manager-profile.png" alt-text="Creare un profilo di Gestione traffico":::

## <a name="add-endpoints"></a>Aggiungere endpoint

1. Selezionare il profilo di Gestione traffico nell'elenco.

    :::image type="content" source="./media/traffic-manager-geographic-routing-method/traffic-manager-list-geographic.png" alt-text="Creare un profilo di Gestione traffico":::

1. Selezionare **Endpoint** in *Impostazioni* , quindi selezionare **+ Aggiungi** per aggiungere un nuovo endpoint.

    :::image type="content" source="./media/traffic-manager-geographic-routing-method/add-geographic-endpoint.png" alt-text="Creare un profilo di Gestione traffico":::

1. Selezionare o immettere le impostazioni seguenti: 

    | Impostazione                | Valore                                              |
    | ---                    | ---                                                |
    | Tipo                   | Selezionare il tipo di endpoint. Per i profili di routing geografico usati nell'ambiente di produzione è consigliabile usare i tipi di endpoint annidati che contengono un profilo figlio con più di un endpoint. Per altre informazioni, vedere le [domande frequenti sui metodi di routing del traffico geografico](traffic-manager-FAQs.md). |    
    | Name                   | Assegnare un nome per identificare questo endpoint. |
    | Tipo di risorsa di destinazione   | Selezionare il tipo di risorsa per la destinazione. |
    | Risorsa di destinazione        | Selezionare la risorsa nell'elenco. |

    > [!Note]
    > Determinati campi di questa pagina dipendono dal tipo di endpoint che si vuole aggiungere:
    > 1. Se si aggiunge un endpoint di Azure, selezionare **Tipo di risorsa di destinazione** e **Destinazione** in base alla risorsa a cui si desidera indirizzare il traffico
    > 1. Se si aggiunge un endpoint **esterno** , indicare il **nome di dominio completo (FQDN)** per l'endpoint.
    > 1. Se si aggiunge un **endpoint annidato** , selezionare la **risorsa di destinazione** che corrisponde al profilo figlio che si desidera usare e specificare il **numero minimo di endpoint figlio** .

1. Nella sezione *Mapping geografico* usare l'elenco a discesa per aggiungere le aree da cui inviare il traffico diretto a questo endpoint. È necessario aggiungere almeno un'area. È possibile eseguire il mapping di più aree.

1. Ripetere l'ultimo passaggio per tutti gli endpoint da aggiungere in questo profilo, quindi selezionare **Salva** .

    :::image type="content" source="./media/traffic-manager-geographic-routing-method/traffic-manager-add-endpoint.png" alt-text="Creare un profilo di Gestione traffico":::

## <a name="use-the-traffic-manager-profile"></a>Usare il profilo di Gestione traffico

1.  Nella barra di ricerca del portale cercare il nome del **profilo di Gestione traffico** creato nella sezione precedente e selezionarlo nei risultati visualizzati.
    
    :::image type="content" source="./media/traffic-manager-geographic-routing-method/search-traffic-manager-profile.png" alt-text="Creare un profilo di Gestione traffico":::

1. Il **Profilo di Gestione traffico** visualizza il nome DNS del profilo di Gestione traffico appena creato. Il nome può essere usato da qualsiasi client (ad esempio raggiungendolo tramite un Web browser) per essere instradato all'endpoint corretto in base al tipo di routing. Nel caso del routing geografico, Gestione traffico analizza l'indirizzo IP di origine della richiesta in ingresso e determina l'area da cui viene generato. Se l'area è mappata a un endpoint, il traffico viene indirizzato in quella posizione. Se l'area non è mappata a un endpoint, Gestione traffico restituisce una risposta NODATA alla query.

    :::image type="content" source="./media/traffic-manager-geographic-routing-method/traffic-manager-geographic-overview.png" alt-text="Creare un profilo di Gestione traffico":::

## <a name="clean-up-resources"></a>Pulire le risorse

Se il profilo di Gestione traffico non è più necessario, individuarlo e selezionare **Elimina profilo** .

:::image type="content" source="./media/traffic-manager-geographic-routing-method/delete-traffic-manager-profile.png" alt-text="Creare un profilo di Gestione traffico":::

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul metodo di routing geografico, vedere:

> [!div class="nextstepaction"]
> [Metodo di routing del traffico geografico](traffic-manager-routing-methods.md#geographic)
