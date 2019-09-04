---
title: Aumentare la quota dell'endpoint-LUIS
titleSuffix: Azure Cognitive Services
description: Language Understanding (LUIS) offre la possibilità di aumentare la quota di endpoint oltre la quota della singola chiave. Questa operazione viene eseguita creando più chiavi per LUIS e aggiungendole all'applicazione LUIS sulla pagina **Publish** (Pubblica) nella sezione **Resources and Keys** (Risorse e chiavi).
author: diberry
manager: nitinme
ms.custom: seodec18
services: cognitive-services
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: diberry
ms.openlocfilehash: c4ea9c5663755a4feb1693dd925d99b10c466140
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70256600"
---
# <a name="use-microsoft-azure-traffic-manager-to-manage-endpoint-quota-across-keys"></a>Usare Gestione traffico di Microsoft Azure per gestire la quota di endpoint tra le chiavi
Language Understanding (LUIS) offre la possibilità di aumentare la quota di endpoint oltre la quota della singola chiave. Questa operazione viene eseguita creando più chiavi per LUIS e aggiungendole all'applicazione LUIS sulla pagina **Publish** (Pubblica) nella sezione **Resources and Keys** (Risorse e chiavi). 

Spetta all'applicazione client gestire il traffico tra le chiavi. LUIS non esegue questa attività. 

Questo articolo illustra come gestire il traffico tra chiavi con [Gestione traffico][traffic-manager-marketing]di Azure. È necessario disporre di un'app LUIS già sottoposta a training e pubblicata. In mancanza, seguire la [guida introduttiva](luis-get-started-create-app.md) relativa al dominio predefinito. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="connect-to-powershell-in-the-azure-portal"></a>Connettersi a PowerShell nel portale di Azure
Nel portale di [Azure][azure-portal] aprire la finestra di PowerShell. L'icona per la finestra di PowerShell è **> _** nella barra di spostamento in alto. Usando PowerShell dal portale, si ottiene la versione più recente di PowerShell e l'utente viene autenticato. PowerShell nel portale richiede un account di [Archiviazione di Azure](https://azure.microsoft.com/services/storage/). 

![Screenshot del portale di Azure con la finestra di Powershell aperta](./media/traffic-manager/azure-portal-powershell.png)

Le sezioni seguenti usano i [cmdlet di PowerShell di Gestione traffico](https://docs.microsoft.com/powershell/module/az.trafficmanager/#traffic_manager).

## <a name="create-azure-resource-group-with-powershell"></a>Creare un gruppo di risorse di Azure con PowerShell
Prima di creare le risorse di Azure, creare un gruppo di risorse per contenere tutte le risorse. Denominare il gruppo di risorse `luis-traffic-manager` e usare l'area `West US`. L'area del gruppo di risorse archivia i metadati del gruppo. Le risorse non subiranno un rallentamento se si trovano in un'altra area. 

Creare un gruppo di risorse con il cmdlet **[New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup)** :

```powerShell
New-AzResourceGroup -Name luis-traffic-manager -Location "West US"
```

## <a name="create-luis-keys-to-increase-total-endpoint-quota"></a>Creare le chiavi LUIS per aumentare la quota totale di endpoint
1. Nel portale di Azure creare due chiavi **Language Understanding**, una in `West US` e l'altra in `East US`. Usare il gruppo di risorse esistente denominato `luis-traffic-manager`, creato nella sezione precedente. 

    ![Screenshot del portale di Azure con due chiavi LUIS nel gruppo di risorse luis-traffic-manager](./media/traffic-manager/luis-keys.png)

2. Nella sezione **Gestisci** del sito Web [Luis][LUIS] , nella pagina **risorse di Azure** assegnare chiavi all'app e ripubblicare l'app selezionando il pulsante **pubblica** nel menu in alto a destra. 

    L'URL di esempio nella colonna **endpoint** usa una richiesta GET con la chiave endpoint come parametro di query. Copiare gli URL degli endpoint delle due nuove chiavi. Verranno usati come parte della configurazione di Gestione traffico più avanti in questo articolo.

## <a name="manage-luis-endpoint-requests-across-keys-with-traffic-manager"></a>Gestire le richieste di endpoint LUIS tra le chiavi con Gestione traffico
Gestione traffico crea un nuovo punto di accesso DNS per gli endpoint. Non opera come un gateway o un proxy, ma esclusivamente a livello di DNS. Questo esempio non modifica alcun record DNS. Usa una libreria DNS per comunicare con Gestione traffico per ottenere l'endpoint corretto per la richiesta specifica. _Ogni_ richiesta destinata a LUIS necessita prima di una richiesta di Gestione traffico per determinare l'endpoint LUIS da usare. 

### <a name="polling-uses-luis-endpoint"></a>Il polling usa l'endpoint LUIS
Gestione traffico esegue il polling degli endpoint periodicamente per verificare che l'endpoint sia ancora disponibile. L'URL di Gestione traffico sottoposto a polling deve essere accessibile con una richiesta GET e restituire un valore 200. L'URL dell'endpoint sulla pagina **Publish** (Pubblica) esegue questa operazione. Poiché ogni chiave endpoint ha una route e parametri di stringa di query diversi, per ogni chiave endpoint è necessario un percorso di polling diverso. Ogni volta che Gestione traffico esegue il polling, comporta un costo in termini di richiesta di quota. Il parametro della stringa di query **q** dell'endpoint LUIS è l'espressione inviata a LUIS. Questo parametro, anziché inviare un'espressione, viene usato per aggiungere il polling di Gestione traffico al registro di endpoint LUIS come tecnica di debug mentre Gestione traffico viene configurato.

Poiché ogni endpoint LUIS deve avere il proprio percorso, ha bisogno di un proprio profilo di Gestione traffico. Per la gestione tra i profili, creare un'architettura di [Gestione traffico _nidificata_](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-nested-profiles). Un profilo padre punta ai profili figlio e gestisce il traffico tra di loro.

Dopo aver configurato Gestione traffico, ricordarsi di modificare il percorso per usare il parametro della stringa di query logging=false, in modo che tutto lo spazio del registro non venga occupato con il polling.

## <a name="configure-traffic-manager-with-nested-profiles"></a>Configurare Gestione traffico con i profili nidificati
Nelle sezioni seguenti si creano due profili figlio, uno per la chiave LUIS per l'area orientale e l'altro per la chiave LUIS per l'area occidentale. Si crea quindi un profilo padre e si aggiungono i due profili figlio al profilo padre. 

### <a name="create-the-east-us-traffic-manager-profile-with-powershell"></a>Creare il profilo di Gestione traffico per l'area Stati Uniti orientali con PowerShell
La creazione del profilo di Gestione traffico per l'area Stati Uniti orientali prevede vari passaggi: creazione del profilo, aggiunta dell'endpoint e impostazione dell'endpoint. Un profilo di Gestione traffico può contenere molti endpoint, ma ogni endpoint ha lo stesso percorso di convalida. Poiché gli URL degli endpoint LUIS per le sottoscrizioni per l'area orientale e per quella occidentale sono diversi a causa dell'area della chiave endpoint, ogni endpoint LUIS deve costituire un singolo endpoint nel profilo. 

1. Creare un profilo con il cmdlet **[New-AzTrafficManagerProfile](https://docs.microsoft.com/powershell/module/az.trafficmanager/new-aztrafficmanagerprofile)**

    Usare il cmdlet seguente per creare il profilo. Assicurarsi di modificare `appIdLuis` e `subscriptionKeyLuis`. SubscriptionKey è per la chiave LUIS per l'area Stati Uniti orientali. Se il percorso non è corretto con l'inclusione dell'ID dell'applicazione LUIS e della chiave endpoint, lo stato del polling di Gestione traffico è `degraded`, poiché Gestione traffico non può richiedere correttamente l'endpoint LUIS. Verificare che il valore di `q` sia `traffic-manager-east`, in modo da poter visualizzare questo valore nei log di endpoint LUIS.

    ```powerShell
    $eastprofile = New-AzTrafficManagerProfile -Name luis-profile-eastus -ResourceGroupName luis-traffic-manager -TrafficRoutingMethod Performance -RelativeDnsName luis-dns-eastus -Ttl 30 -MonitorProtocol HTTPS -MonitorPort 443 -MonitorPath "/luis/v2.0/apps/<appID>?subscription-key=<subscriptionKey>&q=traffic-manager-east"
    ```
    
    Questa tabella illustra ogni variabile nel cmdlet:
    
    |Parametro di configurazione|Nome o valore della variabile|Scopo|
    |--|--|--|
    |-Name|luis-profile-eastus|Nome di Gestione traffico nel portale di Azure|
    |-ResourceGroupName|luis-traffic-manager|Creato nella sezione precedente|
    |-TrafficRoutingMethod|Prestazioni|Per altre informazioni, vedere [Metodi di routing di Gestione traffico][routing-methods]. Se si utilizza performance, la richiesta di URL a Gestione traffico deve provenire dall'area dell'utente. Se si passa attraverso un chatbot o un'altra applicazione, è responsabilità del chatbot simulare l'area nella chiamata a Gestione traffico. |
    |-RelativeDnsName|luis-dns-eastus|Questo è il sottodominio per il servizio: luis-dns-eastus.trafficmanager.net|
    |-Ttl|30|Intervallo di polling, 30 secondi|
    |-MonitorProtocol<BR>-MonitorPort|HTTPS<br>443|Il protocollo e la porta per LUIS sono HTTPS/443|
    |-MonitorPath|`/luis/v2.0/apps/<appIdLuis>?subscription-key=<subscriptionKeyLuis>&q=traffic-manager-east`|Sostituire `<appIdLuis>` e `<subscriptionKeyLuis>` con valori personalizzati.|
    
    Una richiesta con esito positivo non ha risposta.

2. Aggiungere un endpoint degli Stati Uniti orientali con il cmdlet **[Add-AzTrafficManagerEndpointConfig](https://docs.microsoft.com/powershell/module/az.trafficmanager/add-aztrafficmanagerendpointconfig)**

    ```powerShell
    Add-AzTrafficManagerEndpointConfig -EndpointName luis-east-endpoint -TrafficManagerProfile $eastprofile -Type ExternalEndpoints -Target eastus.api.cognitive.microsoft.com -EndpointLocation "eastus" -EndpointStatus Enabled
    ```
    Questa tabella illustra ogni variabile nel cmdlet:

    |Parametro di configurazione|Nome o valore della variabile|Scopo|
    |--|--|--|
    |-EndpointName|luis-east-endpoint|Nome dell'endpoint visualizzato nel profilo|
    |-TrafficManagerProfile|$eastprofile|Usare l'oggetto profilo creato nel passaggio 1|
    |-Type|ExternalEndpoints|Per altre informazioni, vedere [endpoint di gestione traffico][traffic-manager-endpoints] |
    |-Target|eastus.api.cognitive.microsoft.com|Questo è il dominio per l'endpoint LUIS.|
    |-EndpointLocation|"eastus"|Area dell'endpoint|
    |-EndpointStatus|Attivato|Abilitare l'endpoint al momento della creazione|

    Una risposta con esito positivo ha un aspetto simile al seguente:

    ```console
    Id                               : /subscriptions/<azure-subscription-id>/resourceGroups/luis-traffic-manager/providers/Microsoft.Network/trafficManagerProfiles/luis-profile-eastus
    Name                             : luis-profile-eastus
    ResourceGroupName                : luis-traffic-manager
    RelativeDnsName                  : luis-dns-eastus
    Ttl                              : 30
    ProfileStatus                    : Enabled
    TrafficRoutingMethod             : Performance
    MonitorProtocol                  : HTTPS
    MonitorPort                      : 443
    MonitorPath                      : /luis/v2.0/apps/<luis-app-id>?subscription-key=f0517d185bcf467cba5147d6260bb868&q=traffic-manager-east
    MonitorIntervalInSeconds         : 30
    MonitorTimeoutInSeconds          : 10
    MonitorToleratedNumberOfFailures : 3
    Endpoints                        : {luis-east-endpoint}
    ```

3. Impostare l'endpoint degli Stati Uniti orientali con il cmdlet **[set-AzTrafficManagerProfile](https://docs.microsoft.com/powershell/module/az.trafficmanager/set-aztrafficmanagerprofile)**

    ```powerShell
    Set-AzTrafficManagerProfile -TrafficManagerProfile $eastprofile
    ```

    Una risposta con esito positivo sarà uguale a quella del passaggio 2.

### <a name="create-the-west-us-traffic-manager-profile-with-powershell"></a>Creare il profilo di Gestione traffico per l'area Stati Uniti occidentali con PowerShell
Per creare il profilo di Gestione traffico per l'area Stati Uniti occidentali, seguire gli stessi passaggi: creazione del profilo, aggiunta dell'endpoint e impostazione dell'endpoint.

1. Creare un profilo con il cmdlet **[New-AzTrafficManagerProfile](https://docs.microsoft.com/powershell/module/az.TrafficManager/New-azTrafficManagerProfile)**

    Usare il cmdlet seguente per creare il profilo. Assicurarsi di modificare `appIdLuis` e `subscriptionKeyLuis`. SubscriptionKey è per la chiave LUIS per l'area Stati Uniti orientali. Se il percorso non è corretto con l'inclusione dell'ID dell'applicazione LUIS e della chiave endpoint, lo stato del polling di Gestione traffico è `degraded`, poiché Gestione traffico non può richiedere correttamente l'endpoint LUIS. Verificare che il valore di `q` sia `traffic-manager-west`, in modo da poter visualizzare questo valore nei log di endpoint LUIS.

    ```powerShell
    $westprofile = New-AzTrafficManagerProfile -Name luis-profile-westus -ResourceGroupName luis-traffic-manager -TrafficRoutingMethod Performance -RelativeDnsName luis-dns-westus -Ttl 30 -MonitorProtocol HTTPS -MonitorPort 443 -MonitorPath "/luis/v2.0/apps/<appIdLuis>?subscription-key=<subscriptionKeyLuis>&q=traffic-manager-west"
    ```
    
    Questa tabella illustra ogni variabile nel cmdlet:
    
    |Parametro di configurazione|Nome o valore della variabile|Scopo|
    |--|--|--|
    |-Name|luis-profile-westus|Nome di Gestione traffico nel portale di Azure|
    |-ResourceGroupName|luis-traffic-manager|Creato nella sezione precedente|
    |-TrafficRoutingMethod|Prestazioni|Per altre informazioni, vedere [Metodi di routing di Gestione traffico][routing-methods]. Se si utilizza performance, la richiesta di URL a Gestione traffico deve provenire dall'area dell'utente. Se si passa attraverso un chatbot o un'altra applicazione, è responsabilità del chatbot simulare l'area nella chiamata a Gestione traffico. |
    |-RelativeDnsName|luis-dns-westus|Questo è il sottodominio per il servizio: luis-dns-westus.trafficmanager.net|
    |-Ttl|30|Intervallo di polling, 30 secondi|
    |-MonitorProtocol<BR>-MonitorPort|HTTPS<br>443|Il protocollo e la porta per LUIS sono HTTPS/443|
    |-MonitorPath|`/luis/v2.0/apps/<appIdLuis>?subscription-key=<subscriptionKeyLuis>&q=traffic-manager-west`|Sostituire `<appId>` e `<subscriptionKey>` con valori personalizzati. Tenere presente che questa chiave endpoint è diversa dalla chiave endpoint orientale|
    
    Una richiesta con esito positivo non ha risposta.

2. Aggiungere un endpoint degli Stati Uniti occidentali con il cmdlet **[Add-AzTrafficManagerEndpointConfig](https://docs.microsoft.com/powershell/module/az.TrafficManager/Add-azTrafficManagerEndpointConfig)**

    ```powerShell
    Add-AzTrafficManagerEndpointConfig -EndpointName luis-west-endpoint -TrafficManagerProfile $westprofile -Type ExternalEndpoints -Target westus.api.cognitive.microsoft.com -EndpointLocation "westus" -EndpointStatus Enabled
    ```

    Questa tabella illustra ogni variabile nel cmdlet:

    |Parametro di configurazione|Nome o valore della variabile|Scopo|
    |--|--|--|
    |-EndpointName|luis-west-endpoint|Nome dell'endpoint visualizzato nel profilo|
    |-TrafficManagerProfile|$westprofile|Usare l'oggetto profilo creato nel passaggio 1|
    |-Type|ExternalEndpoints|Per altre informazioni, vedere [endpoint di gestione traffico][traffic-manager-endpoints] |
    |-Target|westus.api.cognitive.microsoft.com|Questo è il dominio per l'endpoint LUIS.|
    |-EndpointLocation|"westus"|Area dell'endpoint|
    |-EndpointStatus|Attivato|Abilitare l'endpoint al momento della creazione|

    Una risposta con esito positivo ha un aspetto simile al seguente:

    ```console
    Id                               : /subscriptions/<azure-subscription-id>/resourceGroups/luis-traffic-manager/providers/Microsoft.Network/trafficManagerProfiles/luis-profile-westus
    Name                             : luis-profile-westus
    ResourceGroupName                : luis-traffic-manager
    RelativeDnsName                  : luis-dns-westus
    Ttl                              : 30
    ProfileStatus                    : Enabled
    TrafficRoutingMethod             : Performance
    MonitorProtocol                  : HTTPS
    MonitorPort                      : 443
    MonitorPath                      : /luis/v2.0/apps/c3fc5d1e-5187-40cc-af0f-fbde328aa16b?subscription-key=e3605f07e3cc4bedb7e02698a54c19cc&q=traffic-manager-west
    MonitorIntervalInSeconds         : 30
    MonitorTimeoutInSeconds          : 10
    MonitorToleratedNumberOfFailures : 3
    Endpoints                        : {luis-west-endpoint}
    ```

3. Impostare l'endpoint degli Stati Uniti occidentali con il cmdlet **[set-AzTrafficManagerProfile](https://docs.microsoft.com/powershell/module/az.TrafficManager/Set-azTrafficManagerProfile)**

    ```powerShell
    Set-AzTrafficManagerProfile -TrafficManagerProfile $westprofile
    ```

    Una risposta con esito positivo è uguale a quella del passaggio 2.

### <a name="create-parent-traffic-manager-profile"></a>Creare un profilo di Gestione traffico padre
Creare il profilo di Gestione traffico padre e collegare ad esso i due profili di Gestione traffico figlio.

1. Creare un profilo padre con il cmdlet **[New-AzTrafficManagerProfile](https://docs.microsoft.com/powershell/module/az.TrafficManager/New-azTrafficManagerProfile)**

    ```powerShell
    $parentprofile = New-AzTrafficManagerProfile -Name luis-profile-parent -ResourceGroupName luis-traffic-manager -TrafficRoutingMethod Performance -RelativeDnsName luis-dns-parent -Ttl 30 -MonitorProtocol HTTPS -MonitorPort 443 -MonitorPath "/"
    ```

    Questa tabella illustra ogni variabile nel cmdlet:

    |Parametro di configurazione|Nome o valore della variabile|Scopo|
    |--|--|--|
    |-Name|luis-profile-parent|Nome di Gestione traffico nel portale di Azure|
    |-ResourceGroupName|luis-traffic-manager|Creato nella sezione precedente|
    |-TrafficRoutingMethod|Prestazioni|Per altre informazioni, vedere [Metodi di routing di Gestione traffico][routing-methods]. Se si utilizza performance, la richiesta di URL a Gestione traffico deve provenire dall'area dell'utente. Se si passa attraverso un chatbot o un'altra applicazione, è responsabilità del chatbot simulare l'area nella chiamata a Gestione traffico. |
    |-RelativeDnsName|luis-dns-parent|Questo è il sottodominio per il servizio: luis-dns-parent.trafficmanager.net|
    |-Ttl|30|Intervallo di polling, 30 secondi|
    |-MonitorProtocol<BR>-MonitorPort|HTTPS<br>443|Il protocollo e la porta per LUIS sono HTTPS/443|
    |-MonitorPath|`/`|Questo percorso non è rilevante in quanto vengono usati i percorsi degli endpoint figlio.|

    Una richiesta con esito positivo non ha risposta.

2. Aggiunta del profilo figlio Stati Uniti orientali al padre con il tipo **[Add-AzTrafficManagerEndpointConfig](https://docs.microsoft.com/powershell/module/az.TrafficManager/Add-azTrafficManagerEndpointConfig)** e **NestedEndpoints**

    ```powerShell
    Add-AzTrafficManagerEndpointConfig -EndpointName child-endpoint-useast -TrafficManagerProfile $parentprofile -Type NestedEndpoints -TargetResourceId $eastprofile.Id -EndpointStatus Enabled -EndpointLocation "eastus" -MinChildEndpoints 1
    ```

    Questa tabella illustra ogni variabile nel cmdlet:

    |Parametro di configurazione|Nome o valore della variabile|Scopo|
    |--|--|--|
    |-EndpointName|child-endpoint-useast|Profilo area orientale|
    |-TrafficManagerProfile|$parentprofile|Profilo a cui assegnare a questo endpoint|
    |-Type|NestedEndpoints|Per ulteriori informazioni, vedere [Add-AzTrafficManagerEndpointConfig](https://docs.microsoft.com/powershell/module/az.trafficmanager/Add-azTrafficManagerEndpointConfig). |
    |-TargetResourceId|$eastprofile.Id|ID del profilo figlio|
    |-EndpointStatus|Attivato|Stato dell'endpoint dopo l'aggiunta al padre|
    |-EndpointLocation|"eastus"|[Nome dell'area di Azure](https://azure.microsoft.com/global-infrastructure/regions/) della risorsa|
    |-MinChildEndpoints|1|Numero minimo per gli endpoint figlio|

    Una risposta con esito positivo ha un aspetto simile al seguente e include il nuovo endpoint `child-endpoint-useast`:    

    ```console
    Id                               : /subscriptions/<azure-subscription-id>/resourceGroups/luis-traffic-manager/providers/Microsoft.Network/trafficManagerProfiles/luis-profile-parent
    Name                             : luis-profile-parent
    ResourceGroupName                : luis-traffic-manager
    RelativeDnsName                  : luis-dns-parent
    Ttl                              : 30
    ProfileStatus                    : Enabled
    TrafficRoutingMethod             : Performance
    MonitorProtocol                  : HTTPS
    MonitorPort                      : 443
    MonitorPath                      : /
    MonitorIntervalInSeconds         : 30
    MonitorTimeoutInSeconds          : 10
    MonitorToleratedNumberOfFailures : 3
    Endpoints                        : {child-endpoint-useast}
    ```

3. Aggiungere il profilo figlio degli Stati Uniti occidentali al padre con il cmdlet **[Add-AzTrafficManagerEndpointConfig](https://docs.microsoft.com/powershell/module/az.TrafficManager/Add-azTrafficManagerEndpointConfig)** e il tipo **NestedEndpoints**

    ```powerShell
    Add-AzTrafficManagerEndpointConfig -EndpointName child-endpoint-uswest -TrafficManagerProfile $parentprofile -Type NestedEndpoints -TargetResourceId $westprofile.Id -EndpointStatus Enabled -EndpointLocation "westus" -MinChildEndpoints 1
    ```

    Questa tabella illustra ogni variabile nel cmdlet:

    |Parametro di configurazione|Nome o valore della variabile|Scopo|
    |--|--|--|
    |-EndpointName|child-endpoint-uswest|Profilo area occidentale|
    |-TrafficManagerProfile|$parentprofile|Profilo a cui assegnare a questo endpoint|
    |-Type|NestedEndpoints|Per ulteriori informazioni, vedere [Add-AzTrafficManagerEndpointConfig](https://docs.microsoft.com/powershell/module/az.trafficmanager/Add-azTrafficManagerEndpointConfig). |
    |-TargetResourceId|$westprofile.Id|ID del profilo figlio|
    |-EndpointStatus|Attivato|Stato dell'endpoint dopo l'aggiunta al padre|
    |-EndpointLocation|"westus"|[Nome dell'area di Azure](https://azure.microsoft.com/global-infrastructure/regions/) della risorsa|
    |-MinChildEndpoints|1|Numero minimo per gli endpoint figlio|

    Una risposta con esito positivo ha un aspetto simile al seguente e include sia il precedente endpoint `child-endpoint-useast` che il nuovo endpoint `child-endpoint-uswest`:

    ```console
    Id                               : /subscriptions/<azure-subscription-id>/resourceGroups/luis-traffic-manager/providers/Microsoft.Network/trafficManagerProfiles/luis-profile-parent
    Name                             : luis-profile-parent
    ResourceGroupName                : luis-traffic-manager
    RelativeDnsName                  : luis-dns-parent
    Ttl                              : 30
    ProfileStatus                    : Enabled
    TrafficRoutingMethod             : Performance
    MonitorProtocol                  : HTTPS
    MonitorPort                      : 443
    MonitorPath                      : /
    MonitorIntervalInSeconds         : 30
    MonitorTimeoutInSeconds          : 10
    MonitorToleratedNumberOfFailures : 3
    Endpoints                        : {child-endpoint-useast, child-endpoint-uswest}
    ```

4. Impostare gli endpoint con il cmdlet **[set-AzTrafficManagerProfile](https://docs.microsoft.com/powershell/module/az.TrafficManager/Set-azTrafficManagerProfile)** 

    ```powerShell
    Set-AzTrafficManagerProfile -TrafficManagerProfile $parentprofile
    ```

    Una risposta con esito positivo è uguale a quella del passaggio 3.

### <a name="powershell-variables"></a>Variabili di PowerShell
Nelle sezioni precedenti sono state create tre variabili di PowerShell: `$eastprofile`, `$westprofile`, `$parentprofile`. Queste variabili vengono usate verso la fine della configurazione di Gestione traffico. Se si sceglie di non creare le variabili o si è omesso o si verifica il timeout della finestra di PowerShell, è possibile usare il cmdlet di PowerShell **[Get-AzTrafficManagerProfile](https://docs.microsoft.com/powershell/module/az.TrafficManager/Get-azTrafficManagerProfile)** per ottenere nuovamente il profilo e assegnarlo a una variabile. 

Sostituire gli elementi tra parentesi acute `<>` con i valori corretti per ognuno dei tre profili necessari. 

```powerShell
$<variable-name> = Get-AzTrafficManagerProfile -Name <profile-name> -ResourceGroupName luis-traffic-manager
```

## <a name="verify-traffic-manager-works"></a>Verificare il funzionamento di Gestione traffico
Per verificare il funzionamento dei profili di Gestione traffico, lo stato dei profili deve essere `Online`. Questo stato si basa sul percorso di polling dell'endpoint. 

### <a name="view-new-profiles-in-the-azure-portal"></a>Visualizzare i nuovi profili nel portale di Azure
È possibile verificare l'avvenuta creazione di tutti i tre profili esaminando le risorse nel gruppo di risorse `luis-traffic-manager`.

![Screenshot del gruppo di risorse di Azure luis-traffic-manager](./media/traffic-manager/traffic-manager-profiles.png)

### <a name="verify-the-profile-status-is-online"></a>Verificare che lo stato del profilo sia Online
Gestione traffico esegue il polling del percorso di ogni endpoint per verificare che sia online. Se è online, lo stato dei profili figlio è `Online`. Tale stato viene visualizzato nella pagina di **panoramica** di ogni profilo. 

![Screenshot della pagina di panoramica del profilo di Gestione traffico di Azure che mostra lo stato di monitoraggio Online](./media/traffic-manager/profile-status-online.png)

### <a name="validate-traffic-manager-polling-works"></a>Convalidare il funzionamento di polling di Gestione traffico
Un altro modo per convalidare il funzionamento di polling di Gestione traffico è offerto dai log endpoint LUIS. Nella pagina dell'elenco delle app del sito Web [Luis][LUIS] esportare il log dell'endpoint per l'applicazione. Poiché Gestione traffico esegue frequentemente il polling per i due endpoint, i log contengono voci anche in caso di attività di pochi minuti. Ricordarsi di cercare le voci in cui la query inizia la query con `traffic-manager-`.

```console
traffic-manager-west    6/7/2018 19:19  {"query":"traffic-manager-west","intents":[{"intent":"None","score":0.944767}],"entities":[]}
traffic-manager-east    6/7/2018 19:20  {"query":"traffic-manager-east","intents":[{"intent":"None","score":0.944767}],"entities":[]}
```

### <a name="validate-dns-response-from-traffic-manager-works"></a>Convalidare il funzionamento della risposta DNS da Gestione traffico
Per convalidare che la risposta DNS restituisca un endpoint LUIS, richiedere il DNS del profilo padre di Gestione traffico usando una libreria client DNS. Il nome DNS per il profilo padre è `luis-dns-parent.trafficmanager.net`.

Il codice Node.js seguente effettua una richiesta per il profilo padre e restituisce un endpoint LUIS:

```javascript
const dns = require('dns');

dns.resolveAny('luis-dns-parent.trafficmanager.net', (err, ret) => {
  console.log('ret', ret);
});
```

La risposta con esito positivo con l'endpoint LUIS è:

```json
[
    {
        value: 'westus.api.cognitive.microsoft.com', 
        type: 'CNAME'
    }
]
```

## <a name="use-the-traffic-manager-parent-profile"></a>Usare il profilo padre di Gestione traffico
Per gestire il traffico tra gli endpoint, è necessario inserire una chiamata al DNS di Gestione traffico per trovare l'endpoint LUIS. Questa chiamata viene effettuata per ogni richiesta dell'endpoint LUIS e deve simulare la posizione geografica dell'utente dell'applicazione client LUIS. Aggiungere il codice di risposta DNS tra l'applicazione client LUIS e la richiesta inviata a LUIS per la previsione dell'endpoint. 

## <a name="resolving-a-degraded-state"></a>Risoluzione di uno stato danneggiato

Abilitare i [log di diagnostica](../../traffic-manager/traffic-manager-diagnostic-logs.md) per Gestione traffico per visualizzare il motivo per cui lo stato dell'endpoint è danneggiato.

## <a name="clean-up"></a>Eseguire la pulizia
Rimuovere le due chiavi endpoint LUIS, i tre profili di Gestione traffico e il gruppo di risorse che conteneva queste cinque risorse. Questa operazione viene eseguita dal portale di Azure. Si eliminano le cinque risorse dall'elenco delle risorse. Quindi si elimina il gruppo di risorse. 

## <a name="next-steps"></a>Passaggi successivi

Esaminare le opzioni del [middleware](https://docs.microsoft.com/azure/bot-service/bot-builder-create-middleware?view=azure-bot-service-4.0&tabs=csaddmiddleware%2Ccsetagoverwrite%2Ccsmiddlewareshortcircuit%2Ccsfallback%2Ccsactivityhandler) in BotFramework v4 per comprendere come aggiungere questo codice di gestione del traffico a un bot BotFramework. 

[traffic-manager-marketing]: https://azure.microsoft.com/services/traffic-manager/
[traffic-manager-docs]: https://docs.microsoft.com/azure/traffic-manager/
[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
[azure-portal]: https://portal.azure.com/
[azure-storage]: https://azure.microsoft.com/services/storage/
[routing-methods]: https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods
[traffic-manager-endpoints]: https://docs.microsoft.com/azure/traffic-manager/traffic-manager-endpoint-types
