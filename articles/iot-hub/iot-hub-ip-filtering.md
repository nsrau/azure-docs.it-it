---
title: Filtri connessioni IP dell'hub IoT di Azure | Microsoft Docs
description: Come usare i filtri IP per bloccare le connessioni da indirizzi IP specifici all'hub IoT di Azure. È possibile bloccare le connessioni da singoli indirizzi IP o da intervalli di indirizzi IP.
author: rezasherafat
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/23/2017
ms.author: rezas
ms.openlocfilehash: 02059409000ee5b68fbcb8695f580980c95effe6
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56731714"
---
# <a name="use-ip-filters"></a>Usare i filtri IP

La sicurezza è un aspetto importante di qualsiasi soluzione IoT basata su un hub IoT di Azure. Talvolta è necessario specificare in modo esplicito gli indirizzi IP da cui possono connettersi i dispositivi come parte della configurazione di sicurezza. La funzionalità *Filtro IP* consente di configurare regole per rifiutare o accettare il traffico da specifici indirizzi IPv4.

## <a name="when-to-use"></a>Quando usare le autorizzazioni

Esistono due casi d'uso specifici in cui è utile bloccare gli endpoint dell'hub IoT per determinati indirizzi IP:

* L'hub IoT deve ricevere il traffico solo da un intervallo di indirizzi IP specificato e rifiutare tutto il resto. Si usa ad esempio l'hub IoT con [Azure ExpressRoute](https://azure.microsoft.com/documentation/articles/expressroute-faqs/#supported-services) per creare connessioni private tra un hub IoT e l'infrastruttura locale.

* È necessario rifiutare il traffico proveniente da indirizzi IP identificati come sospetti dall'amministratore dell'hub IoT.

## <a name="how-filter-rules-are-applied"></a>Come vengono applicate le regole di filtro

Le regole del filtro IP vengono applicate a livello del servizio dell'hub IoT. Le regole del filtro IP vengono quindi applicate a tutte le connessioni provenienti dai dispositivi e dalle app back-end con qualsiasi protocollo supportato.

Qualsiasi tentativo di connessione da un indirizzo IP corrispondente a una regola di rifiuto nell'hub IoT riceve un codice di stato 401 - Non autorizzato e la descrizione. Il messaggio di risposta non indica la regola IP.

## <a name="default-setting"></a>Impostazione predefinita

Per impostazione predefinita, la griglia **Filtro IP** nel portale di un hub IoT è vuota. Questa impostazione predefinita indica che l'hub in uso accetta connessioni da qualsiasi indirizzo IP. Questa impostazione predefinita equivale a una regola che accetta l'intervallo di indirizzi IP 0.0.0.0/0.

![Impostazioni predefinite del filtro IP dell'hub IoT](./media/iot-hub-ip-filtering/ip-filter-default.png)

## <a name="add-or-edit-an-ip-filter-rule"></a>Aggiungere o modificare una regola del filtro IP

Quando si aggiunge una regola del filtro IP, vengono richiesti i valori seguenti:

* Un **nome della regola del filtro IP** che deve essere una stringa univoca, senza distinzione tra maiuscole e minuscole e alfanumerica con un massimo di 128 caratteri. Sono ammessi solo caratteri alfanumerici ASCII a 7 bit più `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}`.

* Selezionare il **rifiuto** o l'**accettazione** come **azione** per la regola del filtro IP.

* Specificare un singolo indirizzo IPv4 o un blocco di indirizzi IP in notazione CIDR. In notazione CIDR, ad esempio, 192.168.100.0/22 rappresenta gli indirizzi IPv4 1024 da 192.168.100.0 a 192.168.103.255.

![Aggiungere una regola di filtro IP a un hub IoT](./media/iot-hub-ip-filtering/ip-filter-add-rule.png)

Dopo aver salvato la regola viene visualizzato un avviso che informa che l'aggiornamento è in corso.

![Notifica sul salvataggio di una regola di filtro IP](./media/iot-hub-ip-filtering/ip-filter-save-new-rule.png)

L'opzione **Aggiungi** è disabilitata quando si raggiunge il numero massimo di dieci regole del filtro IP.

È possibile modificare una regola esistente facendo doppio clic sulla riga corrispondente.

> [!NOTE]
> Il rifiuto di indirizzi IP può impedire l'interazione di altri servizi di Azure (ad esempio Analisi di flusso di Azure, Macchine virtuali di Azure o Device Explorer nel portale) con l'hub IoT.

> [!WARNING]
> Se si usa Analisi di flusso di Azure (ASA) per leggere i messaggi da un hub IoT con i filtri IP abilitati, usare il nome e l'endpoint compatibile con l'hub eventi dell'hub IoT nella stringa di connessione ASA.

## <a name="delete-an-ip-filter-rule"></a>Eliminare una regola del filtro IP

Selezionare una o più regole del filtro IP nella griglia e fare clic su **Elimina**.

![Eliminare una regola del filtro IP dell'hub IoT](./media/iot-hub-ip-filtering/ip-filter-delete-rule.png)

## <a name="retrieve-and-update-ip-filters-using-azure-cli"></a>Recuperare e aggiornare i filtri IP tramite l'interfaccia della riga di comando di Azure

I filtri IP dell'hub IoT possono essere recuperati e aggiornati tramite l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest). 

Per recuperare i filtri IP correnti dell'hub IoT, eseguire:

```azurecli-interactive
az resource show -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs
```

Verrà restituito un oggetto JSON in cui i filtri IP esistenti sono elencati sotto la chiave `properties.ipFilterRules`:

```json
{
...
    "properties": {
        "ipFilterRules": [
        {
            "action": "Reject",
            "filterName": "MaliciousIP",
            "ipMask": "6.6.6.6/6"
        },
        {
            "action": "Allow",
            "filterName": "GoodIP",
            "ipMask": "131.107.160.200"
        },
        ...
        ],
    },
...
}
```

Per aggiungere un nuovo filtro IP per l'hub IoT, eseguire:

```azurecli-interactive
az resource update -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs --add properties.ipFilterRules "{\"action\":\"Reject\",\"filterName\":\"MaliciousIP\",\"ipMask\":\"6.6.6.6/6\"}"
```

Per rimuovere un filtro IP esistente nell'hub IoT, eseguire:

```azurecli-interactive
az resource update -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs --add properties.ipFilterRules <ipFilterIndexToRemove>
```

Si noti che `<ipFilterIndexToRemove>` deve corrispondere all'ordinamento dei filtri IP nella chiave `properties.ipFilterRules` dell'hub IoT.


## <a name="retrieve-and-update-ip-filters-using-azure-powershell"></a>Recuperare e aggiornare i filtri IP tramite Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

I filtri IP dell'IoT Hub possono essere recuperati e impostati tramite [Azure PowerShell](/powershell/azure/overview). 

```powershell
# Get your IoT Hub resource using its name and its resource group name
$iothubResource = Get-AzResource -ResourceGroupName <resourceGroupNmae> -ResourceName <iotHubName> -ExpandProperties

# Access existing IP filter rules
$iothubResource.Properties.ipFilterRules |% { Write-host $_ }

# Construct a new IP filter
$filter = @{'filterName'='MaliciousIP'; 'action'='Reject'; 'ipMask'='6.6.6.6/6'}

# Add your new IP filter rule
$iothubResource.Properties.ipFilterRules += $filter

# Remove an existing IP filter rule using its name, e.g., 'GoodIP'
$iothubResource.Properties.ipFilterRules = @($iothubResource.Properties.ipFilterRules | Where 'filterName' -ne 'GoodIP')

# Update your IoT Hub resource with your updated IP filters
$iothubResource | Set-AzResource -Force
```

## <a name="update-ip-filter-rules-using-rest"></a>Aggiornare le regole di filtro IP tramite REST

È possibile recuperare e modificare il filtro IP dell'hub IoT anche usando l'endpoint REST del provider di risorse di Azure. Vedere `properties.ipFilterRules` nel [metodo createorupdate](https://docs.microsoft.com/rest/api/iothub/iothubresource/createorupdate).


## <a name="ip-filter-rule-evaluation"></a>Valutazione delle regole del filtro IP

Le regole del filtro IP vengono applicate in ordine e la prima regola corrispondente all'indirizzo IP determina l'azione di accettazione o rifiuto.

Se ad esempio si vogliono accettare gli indirizzi nell'intervallo 192.168.100.0/22 e rifiutare tutti gli altri, la prima regola nella griglia dovrà accettare l'intervallo di indirizzi 192.168.100.0/22. La regola successiva dovrà rifiutare tutti gli indirizzi usando l'intervallo 0.0.0.0/0.

È possibile modificare l'ordine delle regole del filtro IP nella griglia facendo clic sui tre punti verticali all'inizio di una riga e trascinando la selezione.

Per salvare il nuovo ordine delle regole del filtro IP, fare clic su **Salva**.

![Modificare l'ordine delle regole del filtro IP dell'hub IoT](./media/iot-hub-ip-filtering/ip-filter-rule-order.png)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle funzionalità dell'hub IoT, vedere:

* [Monitoraggio delle operazioni](iot-hub-operations-monitoring.md)
* [Metriche di hub IoT](iot-hub-metrics.md)
