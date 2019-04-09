---
title: Distribuire servizi di Gestione API di Azure in più aree di Azure | Documentazione Microsoft
description: Informazioni su come distribuire un'istanza del servizio Gestione API di Azure in più aree di Azure.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/04/2019
ms.author: apimpm
ms.openlocfilehash: d22da92355616c208c7616b4b0e8c26b7f9e7006
ms.sourcegitcommit: b4ad15a9ffcfd07351836ffedf9692a3b5d0ac86
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/05/2019
ms.locfileid: "59058040"
---
# <a name="how-to-deploy-an-azure-api-management-service-instance-to-multiple-azure-regions"></a>Come distribuire un'istanza del servizio Gestione API di Azure in più aree di Azure

Gestione API di Azure supporta la distribuzione in più aree che consente agli autori di API di distribuire un solo servizio Gestione API do Azure in qualsiasi numero di aree di Azure. Ciò consente di ridurre la latenza delle richieste percepita dagli utenti dell'API distribuiti geograficamente, oltre a migliorare la disponibilità del servizio se un'area viene portata offline.

Un nuovo servizio Gestione API di Azure contiene inizialmente una sola [unità] [ unit] in una singola area di Azure, nell'area primaria. È possibile aggiungere facilmente altre aree tramite il portale di Azure. Un server gateway di gestione API viene distribuito in ogni area e il traffico delle chiamate verrà indirizzato al gateway più vicino in termini di latenza. Quando un'area passa offline, il traffico viene automaticamente reindirizzato al gateway successivo più vicino.

> [!NOTE]
> Gestione API di Azure replica solo il componente gateway API tra le aree. Il componente di gestione del servizio è ospitato solo nell'area primaria. In caso di interruzione nell'area primaria, non è possibile applicare modifiche alla configurazione di un'istanza del servizio Gestione API di Azure, tra cui le impostazioni o gli aggiornamenti di criteri.

[!INCLUDE [premium.md](../../includes/api-management-availability-premium.md)]

## <a name="add-region"></a>Distribuire un'istanza del servizio Gestione API in una nuova area

> [!NOTE]
> Se non è ancora stata creata un'istanza del servizio Gestione API, vedere [Creare un'istanza di Gestione API][Create an API Management service instance].

Passare alla pagina **Scalabilità** nel portale di Azure per l'istanza del servizio di Gestione API. 

![Scheda Scalabilità][api-management-scale-service]

Per distribuire una nuova area, fare clic su **+ Aggiungi area** dalla barra degli strumenti.

![Aggiungere un'area][api-management-add-region]

Selezionare la posizione dall'elenco a discesa e impostare il numero di unità con il dispositivo di scorrimento.

![Specificare le unità][api-management-select-location-units]

Fare clic su **Aggiungi** per inserire la selezione nella tabella Posizioni. 

Ripetere l'operazione per configurare tutte le posizioni e fare clic su **Salva** dalla barra degli strumenti per avviare il processo di distribuzione.

## <a name="remove-region"> </a>Eliminare un'istanza del servizio di Gestione API da una posizione

Passare alla pagina **Scalabilità** nel portale di Azure per l'istanza del servizio di Gestione API. 

![Scheda Scalabilità][api-management-scale-service]

Per la località che si vuole rimuovere aprire il menu di scelta rapida usando il pulsante **...** sul lato destro della tabella. Selezionare l'opzione **Elimina**.

Confermare l'eliminazione e fare clic su **Salva** per applicare le modifiche.

## <a name="route-backend"> </a>Chiamate di route API per servizi back-end a livello di area

Gestione API di Azure include un solo URL del servizio back-end. Anche se sono presenti istanze di gestione API di Azure in diverse aree, il gateway API inoltrerà comunque le richieste al servizio back-end stesso, che viene distribuito in una sola area. In questo caso, il miglioramento delle prestazioni proverrà solo dalle risposte memorizzate nella cache in Gestione API di Azure in un'area specifica per la richiesta, ma il tentativo di contattare il back-end in tutto il mondo potrebbe comunque causare una latenza elevata.

Per sfruttare completamente la distribuzione geografica del sistema, è necessario disporre di servizi di back-end distribuiti nelle stesse aree come istanze di gestione API di Azure. Quindi, usando i criteri e la proprietà `@(context.Deployment.Region)`, è possibile instradare il traffico alle istanze locali di back-end.

1. Passare all'istanza di gestione API di Azure e fare clic su **API** nel menu a sinistra.
2. Selezionare l'API desiderata.
3. Fare clic su **editor di codice** dall'elenco a discesa sulla freccia nel **l'elaborazione in ingresso**.

    ![Editor di codice API](./media/api-management-howto-deploy-multi-region/api-management-api-code-editor.png)

4. Usare i criteri `set-backend` combinati con condizionale`choose` per creare un criterio di routing appropriato nella `<inbound> </inbound>` sezione del file.

    Ad esempio, il file XML seguente funzionerà per le aree Stati Uniti occidentali e Asia orientale:

    ```xml
    <policies>
        <inbound>
            <base />
            <choose>
                <when condition="@("West US".Equals(context.Deployment.Region, StringComparison.OrdinalIgnoreCase))">
                    <set-backend-service base-url="http://contoso-us.com/" />
                </when>
                <when condition="@("East Asia".Equals(context.Deployment.Region, StringComparison.OrdinalIgnoreCase))">
                    <set-backend-service base-url="http://contoso-asia.com/" />
                </when>
                <otherwise>
                    <set-backend-service base-url="http://contoso-other.com/" />
                </otherwise>
            </choose>
        </inbound>
        <backend>
            <base />
        </backend>
        <outbound>
            <base />
        </outbound>
        <on-error>
            <base />
        </on-error>
    </policies>
    ```

> [!TIP]
> Si possono anche front i servizi back-end con [gestione traffico di Azure](https://azure.microsoft.com/services/traffic-manager/), indirizzare le chiamate all'API di gestione traffico e lasciare che risolvere automaticamente il routing.

## <a name="custom-routing"> </a>Usare il routing personalizzato ai gateway di gestione API a livello di area

Gestione API di routing delle richieste a un regional *gateway* base [latenza più bassa](../traffic-manager/traffic-manager-routing-methods.md#performance). Anche se non è possibile eseguire l'override di questa impostazione in Gestione API, è possibile utilizzare il proprio profilo di gestione con regole di routing personalizzate.

1. Creare il proprio [gestione traffico di Azure](https://azure.microsoft.com/services/traffic-manager/).
1. Se si usa un dominio personalizzato, [usarlo con gestione traffico](../traffic-manager/traffic-manager-point-internet-domain.md) anziché il servizio Gestione API.
1. [Configurare gli endpoint a livello di area di gestione API in Gestione traffico](../traffic-manager/traffic-manager-manage-endpoints.md). Gli endpoint a livello di area sono conformi al criterio URL `https://<service-name>-<region>-01.regional.azure-api.net`, ad esempio `https://contoso-westus2-01.regional.azure-api.net`.
1. [Configurare gli endpoint di stato a livello di area di gestione API in Gestione traffico](../traffic-manager/traffic-manager-monitoring.md). Gli endpoint di stato a livello di area sono conformi al criterio URL `https://<service-name>-<region>-01.regional.azure-api.net/status-0123456789abcdef`, ad esempio `https://contoso-westus2-01.regional.azure-api.net/status-0123456789abcdef`.
1. Specificare [il metodo di routing](../traffic-manager/traffic-manager-routing-methods.md) di gestione traffico.


[api-management-management-console]: ./media/api-management-howto-deploy-multi-region/api-management-management-console.png

[api-management-scale-service]: ./media/api-management-howto-deploy-multi-region/api-management-scale-service.png
[api-management-add-region]: ./media/api-management-howto-deploy-multi-region/api-management-add-region.png
[api-management-select-location-units]: ./media/api-management-howto-deploy-multi-region/api-management-select-location-units.png
[api-management-remove-region]: ./media/api-management-howto-deploy-multi-region/api-management-remove-region.png

[Create an API Management service instance]: get-started-create-service-instance.md
[Get started with Azure API Management]: get-started-create-service-instance.md

[Deploy an API Management service instance to a new region]: #add-region
[Delete an API Management service instance from a region]: #remove-region

[unit]: https://azure.microsoft.com/pricing/details/api-management/
[Premium]: https://azure.microsoft.com/pricing/details/api-management/
