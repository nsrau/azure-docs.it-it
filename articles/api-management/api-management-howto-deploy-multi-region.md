---
title: Distribuire i servizi di gestione API di Azure in più aree di Azure
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
ms.date: 04/20/2020
ms.author: apimpm
ms.openlocfilehash: 427ebfe865002612be2f9aeb9db416f5c2f41e52
ms.sourcegitcommit: d8b8768d62672e9c287a04f2578383d0eb857950
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/11/2020
ms.locfileid: "88065455"
---
# <a name="how-to-deploy-an-azure-api-management-service-instance-to-multiple-azure-regions"></a>Come distribuire un'istanza del servizio Gestione API di Azure in più aree di Azure

Gestione API di Azure supporta la distribuzione in più aree, che consente agli editori di API di distribuire un singolo servizio gestione API di Azure in qualsiasi numero di aree di Azure supportate. La funzionalità in più aree consente di ridurre la latenza delle richieste percepita dai consumer di API distribuiti geograficamente e di migliorare la disponibilità del servizio se un'area è offline.

Un nuovo servizio gestione API di Azure contiene inizialmente una sola [unità][unit] in una singola area di Azure, ovvero l'area primaria. È possibile aggiungere unità aggiuntive alle aree primarie o secondarie. Un componente gateway di gestione API viene distribuito in ogni area primaria e secondaria selezionata. Le richieste API in ingresso vengono indirizzate automaticamente all'area più vicina. Se un'area passa alla modalità offline, le richieste API verranno indirizzate automaticamente intorno all'area non riuscita al gateway successivo più vicino.

> [!NOTE]
> Solo il componente gateway di gestione API viene distribuito in tutte le aree. Il componente di gestione dei servizi e il portale per sviluppatori sono ospitati solo nell'area primaria. Pertanto, in caso di interruzione dell'area primaria, l'accesso al portale per sviluppatori e la possibilità di modificare la configurazione, ad esempio l'aggiunta di API, l'applicazione di criteri, saranno compromessi fino a quando l'area primaria non tornerà online. Mentre l'area primaria è disponibile offline, le aree secondarie continueranno a gestire il traffico API usando la configurazione più recente disponibile.

>[!IMPORTANT]
> La funzionalità che consente di archiviare i dati dei clienti in una singola area è attualmente disponibile solo nell'area Asia sudorientale (Singapore) del Asia Pacifico Geo. Per tutte le altre aree, i dati dei clienti vengono archiviati in geografico.

[!INCLUDE [premium.md](../../includes/api-management-availability-premium.md)]

## <a name="deploy-api-management-service-to-a-new-region"></a><a name="add-region"> </a>Distribuire il servizio gestione API in una nuova area

> [!NOTE]
> Se non è ancora stata creata un'istanza del servizio Gestione API, vedere [Creare un'istanza di Gestione API][create an api management service instance].

1. Nel portale di Azure passare al servizio gestione API e fare clic sulla voce **locations (percorsi** ) nel menu.
2. Fare clic su **+ Aggiungi** nella barra superiore.
3. Selezionare la posizione dall'elenco a discesa e impostare il numero di unità con il dispositivo di scorrimento.
4. Fare clic sul pulsante **Aggiungi** per confermare.
5. Ripetere questo processo fino a quando non si configurano tutte le posizioni.
6. Fare clic su **Salva** nella barra superiore per avviare il processo di distribuzione.

## <a name="delete-an-api-management-service-location"></a><a name="remove-region"> </a>Eliminare un percorso del servizio gestione API

1. Nel portale di Azure passare al servizio gestione API e fare clic sulla voce **locations (percorsi** ) nel menu.
2. Per la località che si vuole rimuovere aprire il menu di scelta rapida usando il pulsante **...** sul lato destro della tabella. Selezionare l'opzione **Elimina**.
3. Confermare l'eliminazione e fare clic su **Salva** per applicare le modifiche.

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
> È anche possibile rivolgersi ai servizi back-end con [Gestione traffico di Azure](https://azure.microsoft.com/services/traffic-manager/), indirizzare le chiamate API a gestione traffico e consentire la risoluzione automatica del routing.

## <a name="use-custom-routing-to-api-management-regional-gateways"></a><a name="custom-routing"> </a>Usare il routing personalizzato ai gateway regionali di gestione API

Gestione API instrada le richieste a un _gateway_ a livello di area in base [alla latenza più bassa](../traffic-manager/traffic-manager-routing-methods.md#performance). Sebbene non sia possibile eseguire l'override di questa impostazione in gestione API, è possibile usare gestione traffico con regole di routing personalizzate.

1. Creare [Gestione traffico di Azure](https://azure.microsoft.com/services/traffic-manager/).
1. Se si usa un dominio personalizzato, [usarlo con gestione traffico invece che con](../traffic-manager/traffic-manager-point-internet-domain.md) il servizio gestione API.
1. [Configurare gli endpoint regionali di gestione API in gestione traffico](../traffic-manager/traffic-manager-manage-endpoints.md). Gli endpoint internazionali seguono il modello di URL `https://<service-name>-<region>-01.regional.azure-api.net` , ad esempio `https://contoso-westus2-01.regional.azure-api.net` .
1. [Configurare gli endpoint di stato locale di gestione API in gestione traffico](../traffic-manager/traffic-manager-monitoring.md). Gli endpoint di stato regionali seguono il modello di URL `https://<service-name>-<region>-01.regional.azure-api.net/status-0123456789abcdef` , ad esempio `https://contoso-westus2-01.regional.azure-api.net/status-0123456789abcdef` .
1. Specificare [il metodo di routing](../traffic-manager/traffic-manager-routing-methods.md) di gestione traffico.

[create an api management service instance]: get-started-create-service-instance.md
[get started with azure api management]: get-started-create-service-instance.md
[deploy an api management service instance to a new region]: #add-region
[delete an api management service instance from a region]: #remove-region
[unit]: https://azure.microsoft.com/pricing/details/api-management/
[premium]: https://azure.microsoft.com/pricing/details/api-management/
