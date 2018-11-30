---
title: Configurare la scalabilità automatica di un'istanza di Gestione API di Azure | Microsoft Docs
description: Questo argomento descrive come impostare il comportamento di scalabilità automatica per un'istanza di gestione API di Azure.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 06/20/2018
ms.author: apimpm
ms.openlocfilehash: a01e50debf11daf2f1163a56726f5574f7e3e379
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/28/2018
ms.locfileid: "52444803"
---
# <a name="automatically-scale-an-azure-api-management-instance"></a>Ridimensionare automaticamente un'istanza di Gestione API di Azure  

L'istanza del servizio Gestione API di Azure esegue la scalabilità automaticamente in base a un set di regole. Questo comportamento può essere abilitato e configurato tramite Monitoraggio di Azure ed è supportato solo nei livelli **Standard** e **Premium** del servizio Gestione API di Azure.

L'articolo illustra il processo di configurazione della scalabilità automatica e suggerisce una configurazione ottimale delle regole di scalabilità automatica.

## <a name="prerequisites"></a>Prerequisiti

Per eseguire i passaggi in questo articolo è necessario:

+ Avere una sottoscrizione di Azure attiva.
+ Disporre di un'istanza di Gestione API. Per altre informazioni, vedere [Create an Azure API Management instance](get-started-create-service-instance.md) (Creare un'istanza di Gestione API di Azure).
+ Comprendere il concetto di [Capacità di un'istanza di Gestione API di Azure](api-management-capacity.md).
+ Comprendere il [processo di scalabilità manuale di un'istanza di Gestione API di Azure](upgrade-and-scale.md), incluse le conseguenze di costo.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="azure-api-management-autoscale-limitations"></a>Limitazioni di scalabilità automatica per Gestione API di Azure

Alcune limitazioni e conseguenze di decisioni di scalabilità devono essere prese in considerazione prima di configurare il comportamento di scalabilità automatica.

+ La scalabilità automatica può essere abilitata solo per i livelli **Standard** e **Premium** servizio Gestione API di Azure.
+ I piani tariffari specificano anche il numero massimo di unità per un'istanza del servizio.
+ Il processo di scalabilità richiederà almeno 20 minuti.
+ Se il servizio è bloccato da un'altra operazione, la scalabilità richiesta avrà esito negativo e verrà ritentata automaticamente.
+ In caso di un servizio con distribuzioni in più aree, solo le unità presenti nella **posizione primaria** possono essere ridimensionate. Le unità presenti in altre posizioni non possono essere ridimensionate.

## <a name="enable-and-configure-autoscale-for-azure-api-management-service"></a>Abilitare e configurare la scalabilità automatica per il servizio Gestione API di Azure

Attenersi alla procedura seguente per configurare la scalabilità automatica per un servizio Gestione API di Azure:

1. Nel portale di Azure, passare all'istanza di **Monitoraggio**.

    ![Monitoraggio di Azure](media/api-management-howto-autoscale/01.png)

2. Selezionare **Scalabilità automatica** nel menu a sinistra.

    ![Risorsa di scalabilità automatica di Monitoraggio di Azure](media/api-management-howto-autoscale/02.png)

3. Individuare il servizio Gestione API di Azure avvalendosi dei filtri nei menu a discesa.
4. Selezionare l'istanza del servizio Gestione API di Azure desiderata.
5. Nella sezione appena aperta, fare clic sul pulsante **Abilita scalabilità automatica**.

    ![Abilitare la scalabilità automatica di Monitoraggio di Azure](media/api-management-howto-autoscale/03.png)

6. Nella sezione **Regole**, fare clic su **+ Aggiungi una regola**.

    ![Aggiungere una regola di scalabilità automatica di Monitoraggio di Azure](media/api-management-howto-autoscale/04.png)

7. Definire una nuova regola di scalabilità orizzontale.

   Ad esempio, una regola di scalabilità orizzontale consente di attivare un'aggiunta di un'unità di Gestione API di Azure, quando la metrica capacità media negli ultimi 30 minuti supera l'80%. La tabella seguente fornisce la configurazione per tale regola.

    | Parametro             | Valore             | Note                                                                                                                                                                                                                                                                           |
    |-----------------------|-------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Origine della metrica         | Risorsa corrente  | Definire la regola in base alle metriche delle risorse di Gestione API di Azure correnti.                                                                                                                                                                                                     |
    | *Criteri*            |                   |                                                                                                                                                                                                                                                                                 |
    | Aggregazione temporale      | Media           |                                                                                                                                                                                                                                                                                 |
    | Nome metrica           | Capacity          | La metrica della capacità è una metrica di Gestione API di Azure che riflette l'utilizzo delle risorse di un'istanza di Gestione API di Azure.                                                                                                                                                            |
    | Statistica intervallo di tempo  | Media           |                                                                                                                                                                                                                                                                                 |
    | Operatore              | Maggiore di      |                                                                                                                                                                                                                                                                                 |
    | Soglia             | 80%               | Soglia per la metrica della capacità media.                                                                                                                                                                                                                                 |
    | Durata (in minuti) | 30                | L'intervallo di tempo per calcolare la media della metrica della capacità è specifico per i modelli di utilizzo. Più lungo è l'intervallo, più uniforme sarà la reazione, ovvero i picchi intermittenti avranno un effetto minore sulla decisione di scalabilità orizzontale. Tuttavia, verrà ritardato anche il trigger di Scalabilità orizzontale. |
    | *Azione*              |                   |                                                                                                                                                                                                                                                                                 |
    | Operazione             | Aumenta numero di |                                                                                                                                                                                                                                                                                 |
    | Numero di istanze        | 1                 | Scalare orizzontalmente un'istanza di Gestione API di Azure di 1 unità.                                                                                                                                                                                                                          |
    | Disattiva regole dopo (minuti)   | 60                | Sono necessari almeno 20 minuti perché il servizio Gestione API di Azure possa essere scalato orizzontalmente. Nella maggior parte dei casi, l'intervallo di disattivazione delle regole di 60 minuti impedisce l'attivazione di più scale-out.                                                                                                  |

8. Fare clic su **Add** (Aggiungi) per salvare la regola.

    ![Regola di scalabilità orizzontale di Monitoraggio di Azure](media/api-management-howto-autoscale/05.png)

9. Fare clic nuovamente su **+ Aggiungi una regola**.

    Questa volta dovrà essere definita una regola di riduzione. Ciò garantisce che le risorse non vengano sprecate quando diminuisce l'utilizzo delle API.

10. Definire una nuova regola di riduzione.

    Ad esempio, una regola di riduzione consente di rimuovere un'unità di Gestione API di Azure, quando la metrica della capacità media negli ultimi 30 minuti è inferiore al 35%. La tabella seguente fornisce la configurazione per tale regola.

    | Parametro             | Valore             | Note                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
    |-----------------------|-------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Origine della metrica         | Risorsa corrente  | Definire la regola in base alle metriche delle risorse di Gestione API di Azure correnti.                                                                                                                                                                                                                                                                                                                                                                                                                         |
    | *Criteri*            |                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Aggregazione temporale      | Media           |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Nome metrica           | Capacity          | Metrica identica a quella usata per la regola di ridimensionamento.                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
    | Statistica intervallo di tempo  | Media           |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Operatore              | Minore di         |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Soglia             | 35%               | Proprio come per la regola di scalabilità orizzontale, questo valore dipende in modo rilevante dai modelli di utilizzo di Gestione API di Azure. |
    | Durata (in minuti) | 30                | Valore identico a quello usato per la regola di ridimensionamento.                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
    | *Azione*              |                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Operazione             | Riduci numero di | Valore opposto a quello usato per la regola di ridimensionamento.                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
    | Numero di istanze        | 1                 | Valore identico a quello usato per la regola di ridimensionamento.                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
    | Disattiva regole dopo (minuti)   | 90                | La riduzione deve essere più conservativa rispetto a una scalabilità orizzontale, pertanto il periodo di disattivazione delle regole dovrà essere più lungo.                                                                                                                                                                                                                                                                                                                                                                                                    |

11. Fare clic su **Add** (Aggiungi) per salvare la regola.

    ![Regola di riduzione di Monitoraggio di Azure](media/api-management-howto-autoscale/06.png)

12. Impostare il numero **massimo** di unità di Gestione API di Azure.

    > [!NOTE]
    > Gestione API di Azure prevede un limite per le unità che un'istanza può scalare orizzontalmente. Il limite dipende da un livello di servizio.

    ![Regola di riduzione di Monitoraggio di Azure](media/api-management-howto-autoscale/07.png)

13. Fare clic su **Save**. La scalabilità automatica è stata configurata.

## <a name="next-steps"></a>Passaggi successivi

+ [Come distribuire un'istanza del servizio Gestione API di Azure in più aree di Azure](api-management-howto-deploy-multi-region.md)
