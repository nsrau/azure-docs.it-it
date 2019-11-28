---
title: 'Avvio rapido: Creare un profilo per la disponibilità elevata delle applicazioni - Interfaccia della riga di comando di Azure - Gestione traffico di Azure'
description: Questo articolo di avvio rapido descrive come creare un profilo di Gestione traffico per creare un'applicazione Web a disponibilità elevata.
services: traffic-manager
author: asudbring
mnager: twooley
Customer intent: As an IT admin, I want to direct user traffic to ensure high availability of web applications.
ms.service: traffic-manager
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/29/2019
ms.author: allensu
ms.openlocfilehash: b724a3e469c5dd8f7b4c4f30adef00c58c5c47c5
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2019
ms.locfileid: "74483911"
---
# <a name="quickstart-create-a-traffic-manager-profile-for-a-highly-available-web-application-using-azure-cli"></a>Guida introduttiva: Creare un profilo di Gestione traffico per un'applicazione Web a disponibilità elevata usando l'interfaccia della riga di comando di Azure

Questo argomento di avvio rapido descrive come creare un profilo di Gestione traffico che fornisce disponibilità elevata per l'applicazione Web.

In questo argomento di avvio rapido verranno create due istanze di un'applicazione Web. Ognuna di esse è in esecuzione in un'area di Azure diversa. Si creerà un profilo di Gestione traffico basato sulla [priorità degli endpoint](traffic-manager-routing-methods.md#priority-traffic-routing-method). Il profilo indirizza il traffico utente al sito primario che esegue l'applicazione Web. Gestione traffico monitora continuamente l'applicazione Web. Se il sito primario non è disponibile, fornisce il failover automatico al sito di backup.

Se non si ha una sottoscrizione di Azure, creare ora un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questa esercitazione è necessario eseguire l'interfaccia della riga di comando di Azure versione 2.0.28 o successiva. Per trovare la versione, eseguire `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse
Come prima cosa creare un gruppo di risorse con [az group create](https://docs.microsoft.com/cli/azure/group). Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite.

L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nella posizione *eastus*:

```azurecli-interactive

  az group create \
    --name myResourceGroup \
    --location eastus

```

## <a name="create-a-traffic-manager-profile"></a>Creare un profilo di Gestione traffico

Creare un profilo di Gestione traffico che indirizza il traffico utente in base alla priorità degli endpoint usando [az network traffic-manager profile create](https://docs.microsoft.com/cli/azure/network/traffic-manager/profile?view=azure-cli-latest#az-network-traffic-manager-profile-create).

Nell'esempio seguente sostituire **<profile_name>** con un nome univoco del profilo di Gestione traffico.

```azurecli-interactive

az network traffic-manager profile create \
    --name <profile_name> \
    --resource-group myResourceGroup \
    --routing-method Priority \
    --path "/" \
    --protocol HTTP \
    --unique-dns-name <profile_name> \
    --ttl 30 \
    --port 80

```

## <a name="create-web-apps"></a>Creare app Web

Per questo avvio rapido, saranno necessarie due istanze di un'applicazione Web distribuita in due aree di Azure diverse (*Stati Uniti orientali* ed *Europa occidentale*). Ognuna verrà usata come endpoint primario e di failover per Gestione traffico.

### <a name="create-web-app-service-plans"></a>Creare piani di servizio app Web
Creare piani di servizio app Web per le due istanze dell'applicazione Web che verrà distribuita in due diverse aree di Azure usando [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create).

Nell'esempio seguente sostituire **<appspname_eastus>** e **<appspname_westeurope>** con un nome univoco di piano di servizio app

```azurecli-interactive

az appservice plan create \
    --name <appspname_eastus> \
    --resource-group myResourceGroup \
    --location eastus \
    --sku S1

az appservice plan create \
    --name <appspname_westeurope> \
    --resource-group myResourceGroup \
    --location westeurope \
    --sku S1

```
### <a name="create-a-web-app-in-the-app-service-plan"></a>Creare un'app Web nel piano di servizio app
Creare due istanze dell'applicazione Web nei piani di servizio app nelle aree di Azure *Stati Uniti orientali* ed *Europa occidentale* usando [az webapp create](https://docs.microsoft.com/cli/azure/webapp?view=azure-cli-latest#az-webapp-create).

Nell'esempio seguente sostituire **<app1name_eastus>** e **<app2name_westeurope>** con un nome univoco dell'app e sostituire **<appspname_eastus>** e **<appspname_westeurope>** con il nome usato per creare i piani di servizio app nella sezione precedente.

```azurecli-interactive

az webapp create \
    --name <app1name_eastus> \
    --plan <appspname_eastus> \
    --resource-group myResourceGroup

az webapp create \
    --name <app2name_westeurope> \
    --plan <appspname_westeurope> \
    --resource-group myResourceGroup

```

## <a name="add-traffic-manager-endpoints"></a>Aggiungere endpoint di Gestione traffico
Aggiungere le due app Web come endpoint di Gestione traffico al profilo di Gestione traffico usando [az network traffic-manager endpoint create](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint?view=azure-cli-latest#az-network-traffic-manager-endpoint-create), come indicato di seguito:

- Determinare l'ID dell'app Web e aggiungere l'app Web che si trova nell'area di Azure *Stati Uniti orientali* come endpoint primario verso cui instradare tutto il traffico utente. 
- Determinare l'ID dell'app Web e aggiungere l'app Web che si trova nell'area di Azure *Europa occidentale* come endpoint di failover. 

Quando l'endpoint primario non è disponibile, il traffico viene instradato automaticamente all'endpoint di failover.

Nell'esempio seguente sostituire **<app1name_eastus>** e **<app2name_westeurope>** con il nome dell'app creata per ogni area nella sezione precedente, sostituire **<appspname_eastus>** e **<appspname_westeurope>** con il nome usato per creare i piani di servizio app nella sezione precedente e sostituire **<profile_name>** con il nome del profilo usato nella sezione precedente. 

**Endpoint Stati Uniti orientali**

```azurecli-interactive

az webapp show \
    --name <app1name_eastus> \
    --resource-group myResourceGroup \
    --query id

```
Prendere nota dell'ID visualizzato nell'output e usare il comando seguente per aggiungere l'endpoint:

```azurecli-interactive

az network traffic-manager endpoint create \
    --name <app1name_eastus> \
    --resource-group myResourceGroup \
    --profile-name <profile_name> \
    --type azureEndpoints \
    --target-resource-id <ID from az webapp show> \
    --priority 1 \
    --endpoint-status Enabled
```

**Endpoint Europa occidentale**

```azurecli-interactive

az webapp show \
    --name <app2name_westeurope> \
    --resource-group myResourceGroup \
    --query id

```
Prendere nota dell'ID visualizzato nell'output e usare il comando seguente per aggiungere l'endpoint:

```azurecli-interactive

az network traffic-manager endpoint create \
    --name <app1name_westeurope> \
    --resource-group myResourceGroup \
    --profile-name <profile_name> \
    --type azureEndpoints \
    --target-resource-id <ID from az webapp show> \
    --priority 2 \
    --endpoint-status Enabled

```

## <a name="test-your-traffic-manager-profile"></a>Testare il profilo di Gestione traffico

In questa sezione, si controllerà il nome di dominio del profilo di Gestione traffico. Si configurerà anche l'endpoint primario come non disponibile. Si osserverà infine che l'app Web è ancora disponibile. Gestione traffico infatti invia il traffico all'endpoint di failover.

Nell'esempio seguente sostituire **<app1name_eastus>** e **<app2name_westeurope>** con il nome dell'app creata per ogni area nella sezione precedente, sostituire **<appspname_eastus>** e **<appspname_westeurope>** con il nome usato per creare i piani di servizio app nella sezione precedente e sostituire **<profile_name>** con il nome del profilo usato nella sezione precedente.

### <a name="determine-the-dns-name"></a>Determinare il nome DNS

Determinare il nome DNS del profilo di Gestione traffico usando [az network traffic-manager profile show](https://docs.microsoft.com/cli/azure/network/traffic-manager/profile?view=azure-cli-latest#az-network-traffic-manager-profile-show).

```azurecli-interactive

az network traffic-manager profile show \
    --name <profile_name> \
    --resource-group myResourceGroup \
    --query dnsConfig.fqdn

```

Copiare il valore **RelativeDnsName**. Il nome DNS del profilo di Gestione traffico è *http://<* relativednsname *>.trafficmanager.net*. 

### <a name="view-traffic-manager-in-action"></a>Visualizzare Gestione traffico in azione
1. In un Web browser immettere il nome DNS del profilo di Gestione traffico (*http://<* relativednsname *>.trafficmanager.net*) per visualizzare il sito Web predefinito dell'app Web.

    > [!NOTE]
    > Nello scenario di questo avvio rapido tutte le richieste vengono instradate all'endpoint primario, che è impostato su **Priorità 1**.
2. Per visualizzare il failover di Gestione traffico in azione, disabilitare il sito primario usando [az network traffic-manager endpoint update](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint?view=azure-cli-latest#az-network-traffic-manager-endpoint-update).

   ```azurecli-interactive

    az network traffic-manager endpoint update \
        --name <app1name_eastus> \
        --resource-group myResourceGroup \
        --profile-name <profile_name> \
        --type azureEndpoints \
        --endpoint-status Disabled
    
   ```

3. Copiare il nome DNS del profilo di Gestione traffico (*http://<* relativednsname *>.trafficmanager.net*) per visualizzare il sito Web in una nuova sessione del Web browser.
4. Verificare che l'app Web sia ancora disponibile.

## <a name="clean-up-resources"></a>Pulire le risorse

Al termine, eliminare i gruppi di risorse, le applicazioni Web e tutte le risorse correlate usando [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete).

```azurepowershell-interactive

az group delete \
    --resource-group myResourceGroup

```

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido è stato creato un profilo di Gestione traffico che fornisce disponibilità elevata per l'applicazione Web. Per altre informazioni su come instradare il traffico, passare alle esercitazioni di Gestione traffico.

> [!div class="nextstepaction"]
> [Esercitazioni di Gestione traffico](tutorial-traffic-manager-improve-website-response.md)
