---
title: Configurare il provisioning di dispositivi con l'interfaccia della riga di comando di Azure | Microsoft Docs
description: Guida introduttiva di Azure - Configurare il servizio Device Provisioning in hub IoT di Azure usando l'interfaccia della riga di comando di Azure
services: iot-dps
keywords: 
author: JimacoMS2
ms.author: v-jamebr
ms.date: 02/26/2018
ms.topic: hero-article
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 09b212a5abe2ebb9c123f3adcbdfa59390d16c10
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2018
---
# <a name="set-up-the-iot-hub-device-provisioning-service-with-azure-cli"></a>Configurare il servizio Device Provisioning in hub IoT con l'interfaccia della riga di comando di Azure

L'interfaccia della riga di comando di Azure viene usata per creare e gestire le risorse di Azure dalla riga di comando o negli script. Questa guida introduttiva illustra l'uso dell'interfaccia della riga di comando di Azure per creare un hub IoT e un servizio Device Provisioning in hub IoT e collegare i due servizi tra loro. 

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

> [!IMPORTANT]
> L'hub IoT e il servizio di provisioning creati in questa guida introduttiva saranno individuabili pubblicamente come endpoint DNS. Evitare di indicare informazioni riservate se si decide di modificare i nomi usati per queste risorse.
>


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]


## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse con il comando [az group create](/cli/azure/group#az_group_create). Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. 

L'esempio seguente crea un gruppo di risorse denominato *my-sample-resource-group* nella località *westus*.

```azurecli-interactive 
az group create --name my-sample-resource-group --location westus
```

> [!TIP]
> L'esempio crea il gruppo di risorse nella località Stati Uniti occidentali. È possibile visualizzare un elenco di località disponibili eseguendo il comando `az account list-locations -o table`.
>
>

## <a name="create-an-iot-hub"></a>Creare un hub IoT

Creare un hub IoT con il comando [az iot hub create](/cli/azure/iot/hub#az_iot_hub_create). 

L'esempio seguente crea un hub IoT denominato *my-sample-hub* nella località *westus*.  

```azurecli-interactive 
az iot hub create --name my-sample-hub --resource-group my-sample-resource-group --location westus
```

## <a name="create-a-provisioning-service"></a>Creare un servizio di provisioning

Creare un servizio di provisioning con il comando [az iot dps create](/cli/azure/iot/dps#az_iot_dps_create). 

L'esempio seguente crea un servizio di provisioning denominato *my-sample-dps* nella località *westus*.  

```azurecli-interactive 
az iot dps create --name my-sample-dps --resource-group my-sample-resource-group --location westus
```

> [!TIP]
> L'esempio crea un servizio di provisioning nella località Stati Uniti occidentali. Per un elenco delle località disponibili, è possibile eseguire il comando `az provider show --namespace Microsoft.Devices --query "resourceTypes[?resourceType=='ProvisioningServices'].locations | [0]" --out table` oppure passare alla pagina [Stato di Azure](https://azure.microsoft.com/status/) e cercare "Servizio Device Provisioning". Nei comandi è possibile specificare le località nel formato a una o più parole, ad esempio westus, West US, WEST US e così via. Il valore non distingue tra maiuscole e minuscole. Se si usa il formato a più parole per specificare la località, racchiudere il valore tra virgolette, ad esempio `-- location "West US"`.
>


## <a name="get-the-connection-string-for-the-iot-hub"></a>Ottenere la stringa di connessione dell'hub IoT

È necessaria la stringa di connessione dell'hub IoT per collegare l'hub al servizio Device Provisioning. Usare il comando [az iot hub show-connection-string](/cli/azure/iot/hub#az_iot_hub_show_connection_string) per ottenere la stringa di connessione e usare l'output per impostare una variabile che verrà usata per collegare le due risorse. 

L'esempio seguente imposta la variabile *hubConnectionString* sul valore della stringa di connessione per la chiave primaria del criterio *iothubowner* dell'hub. È possibile specificare un criterio diverso con il parametro `--policy-name`. Il comando usa le opzioni [query](/cli/azure/query-azure-cli) e [output](/cli/azure/format-output-azure-cli#tsv-output-format) dell'interfaccia della riga di comando di Azure per estrarre la stringa di connessione dall'output del comando.

```azurecli-interactive 
hubConnectionString=$(az iot hub show-connection-string --name my-sample-hub --key primary --query connectionString -o tsv)
```

È possibile usare il comando `echo` per visualizzare la stringa di connessione.

```azurecli-interactive 
echo $hubConnectionString
```

> [!NOTE]
> Questi due comandi sono validi per un host in esecuzione in Bash. Se si usa una shell Windows/CMD locale o un host di PowerShell, è necessario modificare i comandi per l'uso della sintassi corretta per tale ambiente.
>

## <a name="link-the-iot-hub-and-the-provisioning-service"></a>Collegare l'hub IoT e il servizio di provisioning

Collegare l'hub IoT e il servizio di provisioning con il comando [az iot dps linked-hub create](/cli/azure/iot/dps/linked-hub#az_iot_dps_linked_hub_create). 

L'esempio seguente collega un hub IoT denominato *my-sample-hub* nella località *westus* e un servizio Device Provisioning denominato *my-sample-dps*. Usa una stringa di connessione per *my-sample-hub* archiviata nella variabile *hubConnectionString* del passaggio precedente.

```azurecli-interactive 
az iot dps linked-hub create --dps-name my-sample-dps --resource-group my-sample-resource-group --connection-string $hubConnectionString --location westus
```

## <a name="verify-the-provisioning-service"></a>Verificare il servizio di provisioning

Ottenere i dettagli del servizio di provisioning con il comando [az iot dps show](/cli/azure/iot/dps#az_iot_dps_show).

L'esempio seguente ottiene i dettagli di un servizio di provisioning denominato *my-sample-dps*. L'hub IoT collegato viene visualizzato nella raccolta *properties.iotHubs*.

```azurecli-interactive
az iot dps show --name my-sample-dps
```

## <a name="clean-up-resources"></a>Pulire le risorse

Altre guide introduttive della raccolta si basano su questa. Se si prevede di continuare a usare le guide introduttive o le esercitazioni successive, non eliminare le risorse create in questa guida introduttiva. Se non si prevede di continuare, è possibile usare i comandi seguenti per eliminare il servizio di provisioning, l'hub IoT o il gruppo di risorse e tutte le relative risorse.

Per eliminare il servizio di provisioning, eseguire il comando [az iot dps delete](/cli/azure/iot/dps#az_iot_dps_delete):

```azurecli-interactive
az iot dps delete --name my-sample-dps --resource-group my-sample-resource-group
```
Per eliminare l'hub IoT, eseguire il comando [az iot hub delete](/cli/azure/iot/hub#az_iot_hub_delete):

```azurecli-interactive
az iot hub delete --name my-sample-hub --resource-group my-sample-resource-group
```

Per eliminare un gruppo di risorse e tutte le relative risorse, usare il comando [az group delete](/cli/azure/group#az_group_delete):

```azurecli-interactive
az group delete --name my-sample-resource-group
```

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva sono stati distribuiti un hub IoT e un'istanza del servizio Device Provisioning e le due risorse sono state collegate. Per informazioni su come usare questa configurazione per eseguire il provisioning di un dispositivo simulato, proseguire con la guida introduttiva per la creazione di un dispositivo simulato.

> [!div class="nextstepaction"]
> [Guida introduttiva per la creazione di un dispositivo simulato](./quick-create-simulated-device.md)

