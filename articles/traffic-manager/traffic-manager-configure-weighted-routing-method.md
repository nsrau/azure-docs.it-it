---
title: 'Esercitazione: Configurare il routing del traffico round robin ponderato tramite Gestione traffico di Azure'
description: Questa esercitazione spiega come bilanciare il carico del traffico con un metodo round robin in Gestione traffico
services: traffic-manager
documentationcenter: ''
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/19/2020
ms.author: duau
ms.openlocfilehash: abcfce43b90c7371d5b38aa5b7a6d478e9d6a0dd
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/19/2020
ms.locfileid: "92207840"
---
# <a name="tutorial-configure-the-weighted-traffic-routing-method-in-traffic-manager"></a>Esercitazione: Configurare il metodo di routing del traffico Ponderato in Gestione traffico

Un modello comune di metodo di routing del traffico consiste nel fornire un set di endpoint identici, che includono servizi cloud e siti Web, e nell'inviare traffico a ciascuno di essi in modo equo. I passaggi seguenti illustrano come configurare questo tipo di metodo di routing del traffico.

> [!NOTE]
> L'app Web di Azure offre già funzionalità di bilanciamento del carico round robin per i siti Web che si trovano all'interno di un'area di Azure (la quale può contenere più data center). Gestione traffico consente di distribuire il traffico tra i siti Web in diversi data center.

In questa esercitazione verranno illustrate le procedure per:
> [!div class="checklist"]
> - Creare un profilo di Gestione traffico con routing ponderato.
> - Usare il profilo di Gestione traffico.
> - Eliminare il profilo di Gestione traffico.

## <a name="prerequisites"></a>Prerequisiti

* Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/).

## <a name="configure-the-weighted-traffic-routing-method"></a>Configurare il metodo di routing del traffico ponderato

1. Da un browser accedere al [portale di Azure](https://portal.azure.com).

1. Nella barra di ricerca del portale cercare il nome del **profilo di Gestione traffico** creato nella sezione precedente e selezionarlo nei risultati visualizzati.

    :::image type="content" source="./media/traffic-manager-weighted-routing-method/search-traffic-manager-weighted-profile.png" alt-text="Cercare il profilo di Gestione traffico":::

1. Selezionare **Configurazione** , quindi selezionare o immettere le impostazioni seguenti:

    | Impostazione         | Valore                                              |
    | ---             | ---                                                |
    | Metodo di routing            | Selezionare **Ponderato** . |    
    | Durata (TTL) DNS | Questo valore controlla la frequenza con cui il server dei nomi della cache locale del client esegue query sul sistema di Gestione traffico per ottenere voci DNS aggiornate. In questo periodo di tempo eventuali modifiche apportate in Gestione traffico, ad esempio al metodo di routing del traffico o alla disponibilità degli endpoint aggiunti, verranno aggiornate nell'intero sistema globale dei server DNS. |
    | Protocollo    | Selezionare un protocollo per il monitoraggio degli endpoint. *Opzioni: HTTP, HTTPS e TCP* |
    | Porta | Specificare il numero della porta. |
    | Percorso | Per monitorare gli endpoint, è necessario specificare un percorso e un nome file. Una barra ("/") è una voce valida per il percorso relativo e implica che il file si trovi nella directory radice (impostazione predefinita). |
    | Impostazioni intestazione personalizzata | Configurare le intestazioni personalizzate in formato host:contoso.com,newheader:newvalue. Il valore massimo supportato per la coppia è 8. Applicabili per i protocolli HTTP e HTTPS. Applicabili per tutti gli endpoint nel profilo |
    | Intervalli di codici di stato previsti (valore predefinito: 200) | Configurare gli intervalli di codici di stato nel formato 200-299,301-301. Il valore massimo supportato per l'intervallo è 8. Applicabili per i protocolli HTTP e HTTPS. Applicabili per tutti gli endpoint nel profilo |
    | Intervallo sondaggio | Configurare l'intervallo di tempo tra i probe di integrità degli endpoint. È possibile scegliere 10 o 30 secondi. |
    | Tollerare il numero di errori | Configurare il numero di errori dei probe di integrità tollerati prima che venga attivato un errore dell'endpoint. Immettere un numero compreso tra 0 e 9. | 
    | Timeout del probe | Configurare il tempo necessario prima del timeout del probe di integrità di un endpoint. Questo valore deve essere almeno 5 e minore del valore dell'intervallo dei probe. |

1. Selezionare **Salva** per completare la configurazione.

    :::image type="content" source="./media/traffic-manager-weighted-routing-method/traffic-manager-weighted-configuration.png" alt-text="Cercare il profilo di Gestione traffico"::: 

1. Selezionare **Endpoint** e configurare il peso di ogni endpoint. Il peso può essere compreso tra 1 e 1000. Maggiore è il peso, maggiore è la priorità.  

    :::image type="content" source="./media/traffic-manager-weighted-routing-method/traffic-manager-configure-endpoints-weighted.png" alt-text="Cercare il profilo di Gestione traffico"::: 

## <a name="use-the-traffic-manager-profile"></a>Usare il profilo di Gestione traffico

Il **Profilo di Gestione traffico** visualizza il nome DNS del profilo di Gestione traffico appena creato. Il nome può essere usato da qualsiasi client (ad esempio raggiungendolo tramite un Web browser) per essere instradato all'endpoint corretto in base al tipo di routing. In questo caso tutte le richieste vengono instradate a ogni endpoint secondo il metodo round-robin.

:::image type="content" source="./media/traffic-manager-weighted-routing-method/traffic-manager-weighted-overview.png" alt-text="Cercare il profilo di Gestione traffico"::: 

## <a name="clean-up-resources"></a>Pulire le risorse

Se il profilo di Gestione traffico non è più necessario, individuarlo e selezionare **Elimina profilo** .

:::image type="content" source="./media/traffic-manager-weighted-routing-method/delete-traffic-manager-weighted-profile.png" alt-text="Cercare il profilo di Gestione traffico":::

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul metodo di routing ponderato, vedere:

> [!div class="nextstepaction"]
> [Metodo di routing ponderato del traffico](traffic-manager-routing-methods.md#weighted)