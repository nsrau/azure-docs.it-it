---
title: Distribuire i servizi di gestione delle API di Azure in più aree di AzureDeploy Azure API Management services to multiple Azure regions
titleSuffix: Azure API Management
description: Informazioni su come distribuire un'istanza del servizio Gestione API di Azure in più aree di Azure.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/12/2019
ms.author: apimpm
ms.openlocfilehash: 5c71f37741de06b8633e7eafaae2f29823214f74
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75442658"
---
# <a name="how-to-deploy-an-azure-api-management-service-instance-to-multiple-azure-regions"></a>Come distribuire un'istanza del servizio Gestione API di Azure in più aree di Azure

Gestione API di Azure supporta la distribuzione in più aree, che consente agli editori di API di distribuire un singolo servizio di gestione delle API di Azure in un numero qualsiasi di aree di Azure supportate. La funzionalità in più aree consente di ridurre la latenza delle richieste percepita dai consumer di API geograficamente distribuiti e migliora la disponibilità del servizio se un'area non è in linea.

Un nuovo servizio Gestione API di Azure contiene inizialmente una sola [unità][unit] in una singola area di Azure, l'area primaria. È possibile aggiungere altre aree alle aree primaria o secondaria. Un componente del gateway di Gestione API viene distribuito in ogni area primaria e secondaria selezionata. Le richieste API in ingresso vengono indirizzate automaticamente all'area più vicina. Se un'area non è in linea, le richieste API verranno automaticamente instradate intorno all'area non riuscita al gateway più vicino successivo.

> [!NOTE]
> Solo il componente gateway di Gestione API viene distribuito in tutte le aree. Il componente di gestione dei servizi e il portale per sviluppatori sono ospitati solo nell'area primaria. Pertanto, in caso di interruzione dell'area primaria, l'accesso al portale per sviluppatori e la possibilità di modificare la configurazione (ad esempio, l'aggiunta di API, l'applicazione di criteri) saranno compromessi fino a quando l'area primaria torna online. Mentre l'area principale è disponibile Aree secondarie continueranno a servire il traffico API utilizzando la configurazione più recente disponibile.

[!INCLUDE [premium.md](../../includes/api-management-availability-premium.md)]

## <a name="deploy-an-api-management-service-instance-to-a-new-region"></a><a name="add-region"> </a>Distribuire un'istanza del servizio Gestione API in una nuova area

> [!NOTE]
> Se non è ancora stata creata un'istanza del servizio Gestione API, vedere [Creare un'istanza di Gestione API][create an api management service instance].

Passare alla pagina **Scalabilità** nel portale di Azure per l'istanza del servizio di Gestione API.

![Scheda Scalabilità][api-management-scale-service]

Per distribuire una nuova area, fare clic su **+ Aggiungi area** dalla barra degli strumenti.

![Aggiungere un'area][api-management-add-region]

Selezionare la posizione dall'elenco a discesa e impostare il numero di unità con il dispositivo di scorrimento.

![Specificare le unità][api-management-select-location-units]

Fare clic su **Aggiungi** per inserire la selezione nella tabella Posizioni.

Ripetere l'operazione per configurare tutte le posizioni e fare clic su **Salva** dalla barra degli strumenti per avviare il processo di distribuzione.

## <a name="delete-an-api-management-service-instance-from-a-location"></a><a name="remove-region"> </a>Eliminare un'istanza del servizio di Gestione API da una posizione

Passare alla pagina **Scalabilità** nel portale di Azure per l'istanza del servizio di Gestione API.

![Scheda Scalabilità][api-management-scale-service]

Per la località che si vuole rimuovere aprire il menu di scelta rapida usando il pulsante **...** sul lato destro della tabella. Selezionare l'opzione **Elimina**.

Confermare l'eliminazione e fare clic su **Salva** per applicare le modifiche.

## <a name="route-api-calls-to-regional-backend-services"></a><a name="route-backend"> </a>Chiamate di route API per servizi back-end a livello di area

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
> È anche possibile anteporre i servizi back-end a Gestione traffico di [Azure,](https://azure.microsoft.com/services/traffic-manager/)indirizzare le chiamate API a Gestione traffico e lasciare che risolva automaticamente il routing.

## <a name="use-custom-routing-to-api-management-regional-gateways"></a><a name="custom-routing"> </a>Usare il routing personalizzato per i gateway regionali di Gestione APIUse custom routing to API Management regional gateways

Gestione API indirizza le richieste a un _gateway_ regionale in base [alla latenza più bassa.](../traffic-manager/traffic-manager-routing-methods.md#performance) Sebbene non sia possibile eseguire l'override di questa impostazione in Gestione API, è possibile usare Gestione traffico con regole di routing personalizzate.

1. Creare [Gestione traffico](https://azure.microsoft.com/services/traffic-manager/)di Azure personalizzato.
1. Se si usa un dominio personalizzato, [usarlo con Gestione traffico](../traffic-manager/traffic-manager-point-internet-domain.md) anziché con il servizio Gestione API.
1. [Configurare gli endpoint regionali di Gestione API in Gestione traffico.](../traffic-manager/traffic-manager-manage-endpoints.md) Gli endpoint regionali seguono `https://<service-name>-<region>-01.regional.azure-api.net`il modello `https://contoso-westus2-01.regional.azure-api.net`di URL di , ad esempio .
1. [Configurare gli endpoint di stato regionali di Gestione API in Gestione traffico.](../traffic-manager/traffic-manager-monitoring.md) Gli endpoint di stato regionali `https://<service-name>-<region>-01.regional.azure-api.net/status-0123456789abcdef`seguono `https://contoso-westus2-01.regional.azure-api.net/status-0123456789abcdef`il modello di URL di , ad esempio .
1. Specificare [il metodo](../traffic-manager/traffic-manager-routing-methods.md) di routing di Gestione traffico.

[api-management-management-console]: ./media/api-management-howto-deploy-multi-region/api-management-management-console.png
[api-management-scale-service]: ./media/api-management-howto-deploy-multi-region/api-management-scale-service.png
[api-management-add-region]: ./media/api-management-howto-deploy-multi-region/api-management-add-region.png
[api-management-select-location-units]: ./media/api-management-howto-deploy-multi-region/api-management-select-location-units.png
[api-management-remove-region]: ./media/api-management-howto-deploy-multi-region/api-management-remove-region.png
[create an api management service instance]: get-started-create-service-instance.md
[get started with azure api management]: get-started-create-service-instance.md
[deploy an api management service instance to a new region]: #add-region
[delete an api management service instance from a region]: #remove-region
[unit]: https://azure.microsoft.com/pricing/details/api-management/
[premium]: https://azure.microsoft.com/pricing/details/api-management/
