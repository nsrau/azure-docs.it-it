---
title: Configurare il servizio Device Provisioning in hub IoT di Azure con l'interfaccia della riga di comando di Azure
description: "Avvio rapido: Configurare il servizio Device Provisioning in hub IoT di Azure con l'interfaccia della riga di comando di Azure"
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.custom: mvc
ms.openlocfilehash: 6406929c3abc3612da2c27edc45e10fd84883d73
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74228552"
---
# <a name="quickstart-set-up-the-iot-hub-device-provisioning-service-with-azure-cli"></a>Guida introduttiva: Configurare il servizio Device Provisioning in hub IoT con l'interfaccia della riga di comando di Azure

L'interfaccia della riga di comando di Azure viene usata per creare e gestire le risorse di Azure dalla riga di comando o negli script. Questo argomento di avvio rapido illustra l'uso dell'interfaccia della riga di comando di Azure per creare un hub IoT e un servizio Device Provisioning in hub IoT e collegare i due servizi tra loro. 

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

> [!IMPORTANT]
> L'hub IoT e il servizio di provisioning creati in questo argomento di avvio rapido saranno individuabili pubblicamente come endpoint DNS. Evitare di indicare informazioni riservate se si decide di modificare i nomi usati per queste risorse.
>

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]


## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse con il comando [az group create](/cli/azure/group#az-group-create). Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. 

L'esempio seguente crea un gruppo di risorse denominato *my-sample-resource-group* nella località *westus*.

```azurecli-interactive 
az group create --name my-sample-resource-group --location westus
```

> [!TIP]
> L'esempio crea il gruppo di risorse nella località Stati Uniti occidentali. È possibile visualizzare un elenco di località disponibili eseguendo il comando `az account list-locations -o table`.
>
>

## <a name="create-an-iot-hub"></a>Creare un hub IoT

Creare un hub IoT con il comando [az iot hub create](/cli/azure/iot/hub#az-iot-hub-create).

L'esempio seguente crea un hub IoT denominato *my-sample-hub* nella località *westus*. Un nome dell'hub IoT deve essere univoco a livello globale in Azure, quindi potrebbe essere necessario aggiungere un prefisso o un suffisso univoco al nome di esempio oppure scegliere un nuovo nome. Assicurarsi che il nome segua le convenzioni di denominazione appropriate per un hub ioT: deve avere una lunghezza compresa tra 3 e 50 caratteri e può contenere solo caratteri alfanumerici maiuscoli o minuscoli oppure trattini ('-'). 

```azurecli-interactive 
az iot hub create --name my-sample-hub --resource-group my-sample-resource-group --location westus
```

## <a name="create-a-device-provisioning-service"></a>Creare un servizio Device Provisioning

Creare un servizio Device Provisioning con il comando [az iot dps create](/cli/azure/iot/dps#az-iot-dps-create). 

L'esempio seguente crea un servizio di provisioning denominato *my-sample-dps* nella località *westus*. Sarà anche necessario scegliere un nome univoco a livello globale per il proprio servizio di provisioning. Assicurarsi che il nome segua le convenzioni di denominazione appropriate per un servizio Device Provisioning in hub IoT: deve avere una lunghezza compresa tra 3 e 64 caratteri e può contenere solo caratteri alfanumerici maiuscoli o minuscoli oppure trattini ('-').

```azurecli-interactive 
az iot dps create --name my-sample-dps --resource-group my-sample-resource-group --location westus
```

> [!TIP]
> L'esempio crea un servizio di provisioning nella località Stati Uniti occidentali. Per un elenco delle località disponibili, è possibile eseguire il comando `az provider show --namespace Microsoft.Devices --query "resourceTypes[?resourceType=='ProvisioningServices'].locations | [0]" --out table` oppure passare alla pagina [Stato di Azure](https://azure.microsoft.com/status/) e cercare "Servizio Device Provisioning". Nei comandi è possibile specificare le località nel formato a una o più parole, ad esempio westus, West US, WEST US e così via. Il valore non distingue tra maiuscole e minuscole. Se si usa il formato a più parole per specificare la località, racchiudere il valore tra virgolette, ad esempio `-- location "West US"`.
>

## <a name="get-the-connection-string-for-the-iot-hub"></a>Ottenere la stringa di connessione dell'hub IoT

È necessaria la stringa di connessione dell'hub IoT per collegare l'hub al servizio Device Provisioning. Usare il comando [az iot hub show-connection-string](/cli/azure/iot/hub#az-iot-hub-show-connection-string) per ottenere la stringa di connessione e usare l'output per impostare una variabile che verrà usata per collegare le due risorse. 

L'esempio seguente imposta la variabile *hubConnectionString* sul valore della stringa di connessione per la chiave primaria del criterio *iothubowner* dell'hub (è possibile usare il parametro `--policy-name` per specificare un criterio diverso). Sostituire *my-sample-hub* con il nome univoco dell'hub ioT scelto in precedenza. Il comando usa le opzioni [query](/cli/azure/query-azure-cli) e [output](/cli/azure/format-output-azure-cli#tsv-output-format) dell'interfaccia della riga di comando di Azure per estrarre la stringa di connessione dall'output del comando.

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

Collegare l'hub IoT e il servizio di provisioning con il comando [az iot dps linked-hub create](/cli/azure/iot/dps/linked-hub#az-iot-dps-linked-hub-create). 

L'esempio seguente collega un hub IoT denominato *my-sample-hub* nella località *westus* e un servizio Device Provisioning denominato *my-sample-dps*. Sostituire questi nomi con i nomi univoci dell'hub ioT e del servizio Device Provisioning scelti in precedenza. Il comando usa la stringa di connessione dell'hub IoT archiviata nella variabile *hubConnectionString* del passaggio precedente.

```azurecli-interactive 
az iot dps linked-hub create --dps-name my-sample-dps --resource-group my-sample-resource-group --connection-string $hubConnectionString --location westus
```

Il completamento del comando può richiedere alcuni minuti.

## <a name="verify-the-provisioning-service"></a>Verificare il servizio di provisioning

Ottenere i dettagli del servizio di provisioning con il comando [az iot dps show](/cli/azure/iot/dps#az-iot-dps-show).

L'esempio seguente ottiene i dettagli di un servizio di provisioning denominato *my-sample-dps*. Sostituire questo nome con il proprio nome del servizio Device provisioning.

```azurecli-interactive
az iot dps show --name my-sample-dps
```
L'hub IoT collegato viene visualizzato nella raccolta *properties.iotHubs*.

![Verificare il servizio di provisioning](./media/quick-setup-auto-provision-cli/verify-provisioning-service.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Altre guide di avvio rapido di questa raccolta si basano sulla presente guida di avvio rapido. Se si prevede di continuare a usare le guide di avvio rapido successive o le esercitazioni, non pulire le risorse create in questa guida di avvio rapido. Se non si prevede di continuare, è possibile usare i comandi seguenti per eliminare il servizio di provisioning, l'hub IoT o il gruppo di risorse e tutte le relative risorse. Sostituire i nomi delle risorse riportate di seguito con i nomi delle proprie risorse.

Per eliminare il servizio di provisioning, eseguire il comando [az iot dps delete](/cli/azure/iot/dps#az-iot-dps-delete):

```azurecli-interactive
az iot dps delete --name my-sample-dps --resource-group my-sample-resource-group
```
Per eliminare l'hub IoT, eseguire il comando [az iot hub delete](/cli/azure/iot/hub#az-iot-hub-delete):

```azurecli-interactive
az iot hub delete --name my-sample-hub --resource-group my-sample-resource-group
```

Per eliminare un gruppo di risorse e tutte le relative risorse, usare il comando [az group delete](/cli/azure/group#az-group-delete):

```azurecli-interactive
az group delete --name my-sample-resource-group
```

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido sono stati distribuiti un hub IoT e un'istanza del servizio Device Provisioning e le due risorse sono state collegate. Per informazioni su come usare questa configurazione per eseguire il provisioning di un dispositivo simulato, proseguire con l'argomento di avvio rapido per la creazione di un dispositivo simulato.

> [!div class="nextstepaction"]
> [Avvio rapido per la creazione di un dispositivo simulato](./quick-create-simulated-device.md)
