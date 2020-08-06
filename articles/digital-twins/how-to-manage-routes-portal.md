---
title: Gestire endpoint e Route (portale)
titleSuffix: Azure Digital Twins
description: Vedere come configurare e gestire gli endpoint e le route di eventi per i dati di Azure Digital gemelli usando il portale di Azure.
author: v-lakast
ms.author: v-lakast
ms.date: 7/22/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 7786f970f612d2856948e2286ed234e2b0895072
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2020
ms.locfileid: "87836964"
---
# <a name="manage-endpoints-and-routes-in-azure-digital-twins-portal"></a>Gestire endpoint e route nei dispositivi gemelli digitali di Azure (portale)

[!INCLUDE [digital-twins-route-selector.md](../../includes/digital-twins-route-selector.md)]

Nei dispositivi gemelli digitali di Azure è possibile instradare le [notifiche degli eventi](how-to-interpret-event-data.md) ai servizi downstream o alle risorse di calcolo connesse. Questa operazione viene eseguita impostando innanzitutto gli **endpoint** che possono ricevere gli eventi. È quindi possibile creare [**Route di eventi**](concepts-route-events.md) che specificano gli eventi generati dai dispositivi gemelli digitali di Azure che vengono recapitati a quali endpoint.

Questo articolo illustra il processo di creazione di endpoint e route usando il [portale di Azure](https://portal.azure.com).

È anche possibile gestire endpoint e route con le [API EventRoutes](how-to-use-apis-sdks.md), [.NET (C#) SDK](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core)o l'interfaccia della riga di comando di [Azure Digital gemelli](how-to-use-cli.md). Per una versione di questo articolo che usa questi meccanismi anziché il portale, vedere [*How-to: Manage Endpoints and routes (API e CLI)*](how-to-manage-routes-apis-cli.md).

## <a name="prerequisites"></a>Prerequisiti

* È necessario un **account Azure** (è possibile impostarne uno gratuitamente [qui](https://azure.microsoft.com/free/?WT.mc_id=A261C142F))
* È necessaria un' **istanza di Azure Digital Twins** nella sottoscrizione di Azure. Se non si dispone già di un'istanza, è possibile crearne una usando la procedura descritta in [*procedura: configurare un'istanza e l'autenticazione*](how-to-set-up-instance-scripted.md). Per usare più avanti in questo articolo, è possibile usare i valori seguenti del programma di installazione:
    - Nome istanza
    - Gruppo di risorse

È possibile trovare questi dettagli nell' [portale di Azure](https://portal.azure.com) dopo aver configurato l'istanza di. Accedere al portale e cercare il nome dell'istanza nella barra di ricerca del portale.
 
:::image type="content" source="media/how-to-manage-routes-portal/search-field-portal.png" alt-text="Screenshot della barra di ricerca portale di Azure.":::

Selezionare l'istanza dai risultati per visualizzare la pagina dei dettagli per l'istanza:

:::image type="content" source="media/how-to-manage-routes-portal/instance-details.png" alt-text="Schermata dei dettagli dell'istanza di ADT." border="false":::

## <a name="create-an-endpoint-for-azure-digital-twins"></a>Creare un endpoint per i dispositivi gemelli digitali di Azure

Questi sono i tipi di endpoint supportati che è possibile creare per l'istanza:
* [Griglia di eventi](../event-grid/overview.md) 
* [Hub eventi](../event-hubs/event-hubs-about.md)
* [Bus di servizio](../service-bus-messaging/service-bus-messaging-overview.md)

Per altre informazioni sui diversi tipi di endpoint, vedere [*scegliere tra i servizi di messaggistica di Azure*](../event-grid/compare-messaging-services.md).

Per collegare un endpoint ai dispositivi gemelli digitali di Azure, è necessario che l'argomento della griglia di eventi, l'hub eventi o il bus di servizio usato per l'endpoint sia già esistente. 

### <a name="create-an-event-grid-endpoint"></a>Creare un endpoint di griglia di eventi

**Prerequisito**: creare un argomento di griglia di eventi attenendosi alla procedura descritta nella [sezione *creare un argomento personalizzato* ](../event-grid/custom-event-quickstart-portal.md#create-a-custom-topic) della Guida introduttiva *agli eventi personalizzati* di griglia di eventi.

Dopo aver creato l'argomento, è possibile collegarlo a dispositivi gemelli digitali di Azure dalla pagina dell'istanza di Azure Digital gemelli nella [portale di Azure](https://portal.azure.com) (è possibile trovare l'istanza inserendone il nome nella barra di ricerca del portale).

Scegliere _endpoint_dal menu istanza. Quindi, dalla pagina *endpoint* che segue, selezionare *+ Crea un endpoint*. 

Nella pagina *Crea un endpoint* visualizzato è possibile creare un endpoint di tipo _griglia di eventi_ selezionando il pulsante di opzione corrispondente. Completare gli altri dettagli: immettere un nome per l'endpoint nel campo _nome_ , scegliere la _sottoscrizione_ dall'elenco a discesa e scegliere l'argomento di griglia di _eventi_ creato in precedenza dal terzo elenco a discesa.

Quindi, creare l'endpoint colpendo _Save_.

:::image type="content" source="media/how-to-manage-routes-portal/create-endpoint-event-grid.png" alt-text="Screenshot della creazione di un endpoint di tipo griglia di eventi.":::

È possibile verificare che l'endpoint sia stato creato correttamente selezionando l'icona di notifica nella barra portale di Azure superiore: 

:::image type="content" source="media/how-to-manage-routes-portal/create-endpoint-notifications.png" alt-text="Screenshot della notifica per verificare la creazione dell'endpoint." border="false":::

È anche possibile visualizzare l'endpoint che è stato creato nuovamente nella pagina *endpoint* per l'istanza di Azure Digital gemelli.

Se la creazione dell'endpoint ha esito negativo, osservare il messaggio di errore e riprovare dopo alcuni minuti.

A questo punto, l'argomento di griglia di eventi è disponibile come endpoint all'interno di dispositivi gemelli digitali di Azure, con il nome specificato nel campo _nome_ . Questo nome viene in genere usato come destinazione di una **Route di eventi**, che verrà creata [più avanti in questo articolo](#event-routes).

### <a name="create-an-event-hubs-endpoint"></a>Creare un endpoint di hub eventi

**Prerequisiti**: 
* Sono necessari uno _spazio dei nomi di hub eventi_ e un _Hub eventi_. Per creare entrambi i passaggi, attenersi alla procedura descritta in hub eventi creare una guida introduttiva a [*Hub eventi*](../event-hubs/event-hubs-create.md) .
* È necessaria una _regola di autorizzazione_. Per crearlo, vedere l'articolo relativo all' [*autorizzazione dell'accesso alle risorse di hub eventi tramite firme di accesso condiviso*](../event-hubs/authorize-access-shared-access-signature.md) .

Passare alla pagina dei dettagli per l'istanza di Azure Digital Twins nel [portale di Azure](https://portal.azure.com) (è possibile trovarla immettendo il relativo nome nella barra di ricerca del portale).

Scegliere _endpoint_dal menu istanza. Quindi, dalla pagina *endpoint* che segue, selezionare *+ Crea un endpoint*. 

Nella pagina *Crea un endpoint* visualizzato è possibile creare un endpoint di tipo _Hub eventi_ selezionando il pulsante di opzione corrispondente. Immettere un nome per l'endpoint nel campo _nome_ . Selezionare quindi la _sottoscrizione_e lo _spazio dei nomi dell'hub eventi_, l' _Hub eventi_e la _regola di autorizzazione_ creati in precedenza dai rispettivi elenchi a discesa.

Quindi, creare l'endpoint colpendo _Save_.

:::image type="content" source="media/how-to-manage-routes-portal/create-endpoint-event-hub.png" alt-text="Screenshot della creazione di un endpoint di tipo hub eventi.":::

È possibile verificare che l'endpoint venga creato correttamente selezionando l'icona di notifica nella parte superiore della barra portale di Azure. 

Se la creazione dell'endpoint ha esito negativo, osservare il messaggio di errore e riprovare dopo alcuni minuti.

A questo punto, l'hub eventi è disponibile come endpoint all'interno di dispositivi gemelli digitali di Azure, con il nome specificato nel campo _nome_ . Questo nome viene in genere usato come destinazione di una **Route di eventi**, che verrà creata [più avanti in questo articolo](#event-routes).

### <a name="create-a-service-bus-endpoint"></a>Creare un endpoint del bus di servizio

**Prerequisiti**: 
* Sono necessari uno _spazio dei nomi del bus di servizio_ e un argomento del bus di _servizio_. Per crearli, seguire i passaggi descritti nella Guida introduttiva [*creare argomenti e sottoscrizioni*](../service-bus-messaging/service-bus-quickstart-topics-subscriptions-portal.md) del bus di servizio. Non è necessario completare la sezione [*creare sottoscrizioni per l'argomento*](../service-bus-messaging/service-bus-quickstart-topics-subscriptions-portal.md#create-subscriptions-to-the-topic) .
* È necessaria una _regola di autorizzazione_. Per crearlo, vedere l'articolo [*autenticazione e autorizzazione*](../service-bus-messaging/service-bus-authentication-and-authorization.md#shared-access-signature) del bus di servizio.

Passare alla pagina dei dettagli per l'istanza di Azure Digital Twins nel [portale di Azure](https://portal.azure.com) (è possibile trovarla immettendo il relativo nome nella barra di ricerca del portale).

Scegliere _endpoint_dal menu istanza. Quindi, dalla pagina *endpoint* che segue, selezionare *+ Crea un endpoint*. 

Nella pagina *Crea un endpoint* visualizzato è possibile creare un endpoint di tipo _bus di servizio_ selezionando il pulsante di opzione corrispondente. Immettere un nome per l'endpoint nel campo _nome_ . Selezionare quindi la _sottoscrizione_e lo _spazio dei nomi del bus di servizio_, l' _argomento del bus di servizio_e la regola di _autorizzazione_ creati in precedenza dai rispettivi elenchi a discesa.

Quindi, creare l'endpoint colpendo _Save_.

:::image type="content" source="media/how-to-manage-routes-portal/create-endpoint-service-bus.png" alt-text="Screenshot della creazione di un endpoint di tipo bus di servizio.":::

È possibile verificare che l'endpoint venga creato correttamente selezionando l'icona di notifica nella parte superiore della barra portale di Azure. 

Se la creazione dell'endpoint ha esito negativo, osservare il messaggio di errore e riprovare dopo alcuni minuti.

A questo punto, l'argomento del bus di servizio è disponibile come endpoint all'interno di dispositivi gemelli digitali di Azure, con il nome specificato nel campo _nome_ . Questo nome viene in genere usato come destinazione di una **Route di eventi**, che verrà creata [più avanti in questo articolo](#event-routes).

## <a name="event-routes"></a>Route di eventi

Per inviare effettivamente i dati dai dispositivi gemelli digitali di Azure a un endpoint, è necessario definire una **route dell'evento**. Queste route consentono agli sviluppatori di collegare il flusso degli eventi, in tutto il sistema e ai servizi downstream. Per altre informazioni sulle route di eventi, vedere [*concetti relativi al routing di eventi gemelli digitali di Azure*](concepts-route-events.md).

**Prerequisito**: è necessario creare endpoint come descritto in precedenza in questo articolo prima di poter passare alla creazione di una route. È possibile procedere alla creazione di una route di eventi al termine della configurazione degli endpoint.

>[!NOTE]
>Se gli endpoint sono stati distribuiti di recente, verificare che la distribuzione sia terminata **prima** di provare a usarli per una nuova route di eventi. Se non si è in grado di configurare la route perché gli endpoint non sono pronti, attendere qualche minuto e riprovare.

### <a name="create-an-event-route"></a>Creare una route di eventi 

Una definizione di route dell'evento può contenere gli elementi seguenti:
* ID della route che si vuole usare
* Nome dell'endpoint che si desidera utilizzare
* Filtro che definisce gli eventi che vengono inviati all'endpoint

Se non è presente alcun ID di route, nessun messaggio viene instradato all'esterno dei dispositivi gemelli digitali di Azure.
Se è presente un ID route e il filtro è `true` , tutti i messaggi vengono instradati all'endpoint.
Se è presente un ID di route e viene aggiunto un filtro diverso, i messaggi verranno filtrati in base al filtro.

Una route deve consentire la selezione di più notifiche e tipi di evento.

Per creare una route di eventi, passare alla pagina dei dettagli per l'istanza di Azure Digital Twins nel [portale di Azure](https://portal.azure.com) (è possibile trovare l'istanza inserendo il nome nella barra di ricerca del portale).

Scegliere _Route eventi_dal menu istanza. Dalla pagina *Route eventi* seguente selezionare *+ Crea una route*per gli eventi. 

Nella pagina *Crea una route di evento* visualizzata, scegliere almeno un nome per la route nel campo _nome_ e selezionare l' _endpoint_ che si vuole usare per creare la route dall'elenco a discesa.

:::image type="content" source="media/how-to-manage-routes-portal/create-event-route-no-filter.png" alt-text="Screenshot della creazione della route dell'evento per l'istanza.":::

Al termine, fare clic sul pulsante _Salva_ per creare la route dell'evento.

### <a name="filter-events"></a>Filtrare gli eventi

Senza filtro, gli endpoint ricevono una serie di eventi da dispositivi gemelli digitali di Azure:
* Telemetria generata dai dispositivi [gemelli digitali](concepts-twins-graph.md) con l'API del servizio di dispositivi digitali gemelli di Azure
* Notifiche delle modifiche delle proprietà dei dispositivi gemelli, attivate per le modifiche delle proprietà per qualsiasi dispositivo gemello nell'istanza di Azure
* Eventi del ciclo di vita, generati quando vengono creati o eliminati gemelli o relazioni
* Eventi di modifica del modello, generati quando i [modelli](concepts-models.md) configurati in un'istanza di Azure Digital Twins vengono aggiunti o eliminati

È possibile limitare gli eventi inviati aggiungendo un **filtro** per un endpoint alla route dell'evento.

Per aggiungere un filtro durante la creazione di una route di eventi, usare la sezione _aggiungere un filtro di route_ per gli eventi della pagina *creare una route* di evento. 

È possibile scegliere tra le opzioni di filtro comuni di base oppure usare le opzioni di filtro avanzate per scrivere filtri personalizzati.

#### <a name="use-the-basic-filters"></a>Usare i filtri di base

Per usare i filtri di base, espandere l'opzione _tipi di evento_ e selezionare le caselle di controllo corrispondenti agli eventi su cui si vuole applicare il filtro. 

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-manage-routes-portal/create-event-route-filter-basic-1.png" alt-text="Screenshot della creazione di una route di eventi con un filtro di base. Selezione delle caselle di controllo degli eventi.":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

La casella di testo filtro verrà popolata automaticamente con il testo del filtro selezionato:

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-manage-routes-portal/create-event-route-filter-basic-2.png" alt-text="Screenshot della creazione di una route di eventi con un filtro di base. Visualizzazione del testo del filtro popolato automaticamente dopo aver selezionato gli eventi.":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

#### <a name="use-the-advanced-filters"></a>Usare i filtri avanzati

In alternativa, è possibile usare l'opzione filtro avanzato per scrivere filtri personalizzati.

Per creare una route di eventi con opzioni di filtro avanzate, attivare o disattivare l'opzione per l' _Editor avanzato_ . È quindi possibile scrivere filtri eventi personalizzati nella casella *filtro* :

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-manage-routes-portal/create-event-route-filter-advanced.png" alt-text="Screenshot della creazione di una route di eventi con un filtro avanzato.":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Ecco i filtri di route supportati. Il dettaglio nella colonna *schema testo filtro* è il testo che è possibile immettere nella casella filtro.

[!INCLUDE [digital-twins-route-filters](../../includes/digital-twins-route-filters.md)]

[!INCLUDE [digital-twins-route-metrics](../../includes/digital-twins-route-metrics.md)]

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sui diversi tipi di messaggi di evento che è possibile ricevere, vedere:
* [*Procedura: interpretare i dati degli eventi*](how-to-interpret-event-data.md)
