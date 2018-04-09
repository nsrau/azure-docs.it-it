---
title: Esercitazione - Ridimensionare automaticamente un set di scalabilità con l'interfaccia della riga di comando di Azure 2.0 | Microsoft Docs
description: Informazioni su come usare l'interfaccia della riga di comando di Azure 2.0 per ridimensionare automaticamente un set di scalabilità di macchine virtuali in base all'aumento o alla riduzione delle esigenze in termini di CPU
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 10e5b1a261f28391bed8cf3f111b1124b52d7816
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2018
---
# <a name="tutorial-automatically-scale-a-virtual-machine-scale-set-with-the-azure-cli-20"></a>Esercitazione: Ridimensionare automaticamente un set di scalabilità di macchine virtuali con l'interfaccia della riga di comando di Azure 2.0
Quando si crea un set di scalabilità, definire il numero di istanze di macchine virtuali da eseguire. È possibile aumentare o ridurre automaticamente il numero di istanze di macchine virtuali in base alle richieste dell'applicazione. La scalabilità automatica consente di adattarsi alle esigenze dei clienti o di rispondere alle prestazioni dell'applicazione durante il ciclo di vita dell'app. In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Usare la scalabilità automatica con un set di scalabilità
> * Creare e usare regole di scalabilità automatica
> * Sottoporre a test di stress le istanze di VM e attivare le regole di scalabilità automatica
> * Ridurre automaticamente il numero di istanze con la riduzione delle esigenze

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questa esercitazione è necessario eseguire l'interfaccia della riga di comando di Azure versione 2.0.29 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0]( /cli/azure/install-azure-cli). 


## <a name="create-a-scale-set"></a>Creare un set di scalabilità
Per semplificare la creazione delle regole di scalabilità automatica, definire alcuni parametri per ID sottoscrizione, gruppo di risorse, nome del set di scalabilità e località:

```azurecli-interactive
sub=$(az account show --query id -o tsv)
resourcegroup_name="myResourceGroup"
scaleset_name="myScaleSet"
location_name="eastus"
```

Creare un gruppo di risorse con [az group create](/cli/azure/group#create), come illustrato di seguito:

```azurecli-interactive
az group create --name $resourcegroup_name --location $location_name
```

Si può ora creare un set di scalabilità di macchine virtuali con il comando [az vmss create](/cli/azure/vmss#create). L'esempio seguente crea un set di scalabilità con numero di istanze pari a *2* e genera le chiavi SSH, se non sono presenti:

```azurecli-interactive
az vmss create \
  --resource-group $resourcegroup_name \
  --name $scaleset_name \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --instance-count 2 \
  --admin-username azureuser \
  --generate-ssh-keys
```


## <a name="define-an-autoscale-profile"></a>Definire un profilo di scalabilità automatica
Le regole di scalabilità automatica vengono distribuite in formato JSON (JavaScript Object Notation) con l'interfaccia della riga di comando di Azure 2.0. Verrà ora esaminata ogni parte di questo profilo di scalabilità automatica, quindi si creerà un esempio completo.

L'inizio del profilo di scalabilità automatica definisce la capacità predefinita, minima e massima del set di scalabilità. L'esempio seguente imposta la capacità predefinita e minima di *2* istanze di macchine virtuali e la capacità massima di *10*:

```json
{
  "name": "autoscale rules",
  "capacity": {
    "minimum": "2",
    "maximum": "10",
    "default": "2"
  }
}
```


## <a name="create-a-rule-to-autoscale-out"></a>Creare una regola per aumentare automaticamente il numero di istanze
Se aumenta la richiesta da parte dell'applicazione, aumenta il carico sulle istanze di macchine virtuali nel set di scalabilità. Se il carico aumenta in modo coerente e non momentaneamente, è possibile configurare regole di scalabilità automatica per aumentare il numero di istanze di macchine virtuali nel set di scalabilità. Quando sono state create le istanze di macchine virtuali e sono state distribuite le applicazioni, il set di scalabilità inizia a distribuire loro il traffico tramite il bilanciamento del carico. È possibile controllare le metriche da monitorare, ad esempio per la CPU o il disco, il tempo per cui il carico dell'applicazione deve soddisfare una determinata soglia e il numero di istanze di macchine virtuali da aggiungere al set di scalabilità.

Verrà ora creata una regola che aumenta il numero di istanze di VM in un set di scalabilità quando il carico medio della CPU è superiore al 70% per un periodo di 5 minuti. Quando la regola viene attivata, il numero di istanze di VM viene incrementato di tre.

Per questa regola vengono usati i parametri seguenti:

| Parametro         | Spiegazione                                                                                                         | Valore           |
|-------------------|---------------------------------------------------------------------------------------------------------------------|-----------------|
| *metricName*      | La metrica delle prestazioni da monitorare e a cui applicare azioni dei set di scalabilità.                                                   | CPU percentuale  |
| *timeGrain*       | La frequenza con cui le metriche vengono raccolte per l'analisi.                                                                   | 1 minuto        |
| *timeAggregation* | Definisce la modalità di aggregazione delle metriche raccolte per l'analisi.                                                | Media         |
| *timeWindow*      | Il tempo monitorato prima che vengano confrontati i valori delle metriche e delle soglie.                                   | 5 minuti       |
| *operator*        | Operatore usato per confrontare i dati della metrica rispetto alla soglia.                                                     | Maggiore di    |
| *threshold*       | Il valore che determina l'attivazione di un'azione da parte della regola di scalabilità automatica.                                                      | 70%             |
| *direction*       | Definisce se il numero di istanze nel set di scalabilità verrà aumentato o ridotto quando si applica la regola.                                              | Aumento        |
| *type*            | Indica che il numero di istanze di VM dovrà essere modificato di un valore specifico.                                    | ChangeCount    |
| *value*           | Numero di istanze di VM previsto come aumento o riduzione quando viene applicata la regola.                                             | 3               |
| *cooldown*        | Il tempo di attesa prima che la regola venga applicata nuovamente in modo che le azioni di scalabilità automatica diventino effettive. | 5 minuti       |

L'esempio seguente definisce la regola per aumentare il numero di istanze di macchine virtuali. *metricResourceUri* usa le variabili definite precedentemente per l'ID sottoscrizione, il nome del gruppo di risorse e il nome del set di scalabilità:

```json
{
  "metricTrigger": {
    "metricName": "Percentage CPU",
    "metricNamespace": "",
    "metricResourceUri": "/subscriptions/'$sub'/resourceGroups/'$resourcegroup_name'/providers/Microsoft.Compute/virtualMachineScaleSets/'$scaleset_name'",
    "metricResourceLocation": "'$location_name'",
    "timeGrain": "PT1M",
    "statistic": "Average",
    "timeWindow": "PT5M",
    "timeAggregation": "Average",
    "operator": "GreaterThan",
    "threshold": 70
  },
  "scaleAction": {
    "direction": "Increase",
    "type": "ChangeCount",
    "value": "3",
    "cooldown": "PT5M"
  }
}
```


## <a name="create-a-rule-to-autoscale-in"></a>Creare una regola per ridurre automaticamente il numero di istanze
Nelle ore serali o nel fine settimana è possibile che la richiesta delle applicazioni si riduca. Se il carico diminuisce in modo coerente nel tempo, è possibile configurare regole di scalabilità automatica per diminuire il numero di istanze di macchine virtuali nel set di scalabilità. Questa azione riduce i costi di esecuzione del set di scalabilità poiché si esegue solo il numero di istanze necessarie per soddisfare la richiesta corrente.

Creare un'altra regola che riduca il numero di istanze di VM in un set di scalabilità quando il carico medio della CPU scende quindi al di sotto del 30% per un periodo di 5 minuti. L'esempio seguente definisce la regola per ridurre il numero di istanze di VM di uno. *metricResourceUri* usa le variabili definite precedentemente per l'ID sottoscrizione, il nome del gruppo di risorse e il nome del set di scalabilità:

```json
{
  "metricTrigger": {
    "metricName": "Percentage CPU",
    "metricNamespace": "",
    "metricResourceUri": "/subscriptions/'$sub'/resourceGroups/'$resourcegroup_name'/providers/Microsoft.Compute/virtualMachineScaleSets/'$scaleset_name'",
    "metricResourceLocation": "'$location_name'",
    "timeGrain": "PT1M",
    "statistic": "Average",
    "timeWindow": "PT5M",
    "timeAggregation": "Average",
    "operator": "LessThan",
    "threshold": 30
  },
  "scaleAction": {
    "direction": "Decrease",
    "type": "ChangeCount",
    "value": "1",
    "cooldown": "PT5M"
  }
}
```


## <a name="apply-autoscale-rules-to-a-scale-set"></a>Applicare le regole di scalabilità automatica a un set di scalabilità
Il passaggio finale consiste nell'applicare il profilo e le regole di scalabilità automatica al set di scalabilità. Il numero di istanze nel set di scalabilità potrà quindi essere ridotto o aumentato automaticamente in base alle esigenze delle applicazioni. Applicare il profilo di scalabilità automatica con [az monitor autoscale-settings create](/cli/azure/monitor/autoscale-settings#az_monitor_autoscale_settings_create) come indicato di seguito. Il codice JSON completo seguente usa il profilo e le regole descritte nelle sezioni precedenti:

```azurecli-interactive
az monitor autoscale-settings create \
    --resource-group $resourcegroup_name \
    --name autoscale \
    --parameters '{"autoscale_setting_resource_name": "autoscale",
      "enabled": true,
      "location": "'$location_name'",
      "notifications": [],
      "profiles": [
        {
          "name": "autoscale by percentage based on CPU usage",
          "capacity": {
            "minimum": "2",
            "maximum": "10",
            "default": "2"
          },
          "rules": [
            {
              "metricTrigger": {
                "metricName": "Percentage CPU",
                "metricNamespace": "",
                "metricResourceUri": "/subscriptions/'$sub'/resourceGroups/'$resourcegroup_name'/providers/Microsoft.Compute/virtualMachineScaleSets/'$scaleset_name'",
                "metricResourceLocation": "'$location_name'",
                "timeGrain": "PT1M",
                "statistic": "Average",
                "timeWindow": "PT5M",
                "timeAggregation": "Average",
                "operator": "GreaterThan",
                "threshold": 70
              },
              "scaleAction": {
                "direction": "Increase",
                "type": "ChangeCount",
                "value": "3",
                "cooldown": "PT5M"
              }
            },
            {
              "metricTrigger": {
                "metricName": "Percentage CPU",
                "metricNamespace": "",
                "metricResourceUri": "/subscriptions/'$sub'/resourceGroups/'$resourcegroup_name'/providers/Microsoft.Compute/virtualMachineScaleSets/'$scaleset_name'",
                "metricResourceLocation": "'$location_name'",
                "timeGrain": "PT1M",
                "statistic": "Average",
                "timeWindow": "PT5M",
                "timeAggregation": "Average",
                "operator": "LessThan",
                "threshold": 30
              },
              "scaleAction": {
                "direction": "Decrease",
                "type": "ChangeCount",
                "value": "1",
                "cooldown": "PT5M"
              }
            }
          ]
        }
      ],
      "tags": {},
      "target_resource_uri": "/subscriptions/'$sub'/resourceGroups/'$resourcegroup_name'/providers/Microsoft.Compute/virtualMachineScaleSets/'$scaleset_name'"
    }'
```


## <a name="generate-cpu-load-on-scale-set"></a>Generare carico della CPU nel set di scalabilità
Per testare le regole di scalabilità automatica, generare una certa quantità di carico della CPU nelle istanze di VM del set di scalabilità. Questo carico della CPU simulato causa l'aumento del numero di istanze di VM in base alla scalabilità automatica. Quando il carico della CPU simulato viene successivamente ridotto, le regole di scalabilità automatica determinano la riduzione del numero di istanze di VM.

Per prima cosa, visualizzare l'elenco con l'indirizzo e le porte per la connessione alle istanze di VM di un set di scalabilità con [az vmss list-instance-connection-info](/cli/azure/vmss#az_vmss_list_instance_connection_info):

```azurecli-interactive
az vmss list-instance-connection-info \
  --resource-group $resourcegroup_name \
  --name $scaleset_name
```

L'output di esempio seguente mostra il nome dell'istanza, l'indirizzo IP pubblico del servizio di bilanciamento del carico e il numero di porta a cui le regole NAT (Network Address Translation) inoltrano il traffico:

```json
{
  "instance 1": "13.92.224.66:50001",
  "instance 3": "13.92.224.66:50003"
}
```

Connettersi tramite SSH alla prima istanza di VM. Specificare l'indirizzo IP pubblico e il numero di porta con il parametro `-p`, in base a quanto visualizzato dal comando precedente:

```azurecli-interactive
ssh azureuser@13.92.224.66 -p 50001
```

Dopo aver effettuato l'accesso, installare l'utilità **stress**. Avviare *10* ruoli di lavoro di **stress**che generano carico della CPU. Questi ruoli di lavoro vengono eseguiti per *420* secondi, che costituiscono un intervallo di tempo sufficiente affinché le regole di scalabilità automatica implementino l'azione desiderata.

```azurecli-interactive
sudo apt-get -y install stress
sudo stress --cpu 10 --timeout 420 &
```

Quando l'output visualizzato da **stress** è simile a *stress: info: [2688] dispatching hogs: 10 cpu, 0 io, 0 vm, 0 hdd*, premere *INVIO* per tornare al prompt.

Per verificare che **stress** generi carico della CPU, esaminare il carico di sistema attivo con l'utilità **top**:

```azuecli-interactive
top
```

Uscire da **top** e quindi chiudere la connessione all'istanza di VM. **stress** continuerà a essere eseguito nell'istanza di VM.

```azurecli-interactive
Ctrl-c
exit
```

Connettersi alla seconda istanza di VM con il numero di porta elencato dal precedente comando [az vmss list-instance-connection-info](/cli/azure/vmss#az_vmss_list_instance_connection_info):

```azurecli-interactive
ssh azureuser@13.92.224.66 -p 50003
```

Installare ed eseguire **stress** e quindi avviare dieci ruoli di lavoro in questa seconda istanza di VM.

```azurecli-interactive
sudo apt-get -y install stress
sudo stress --cpu 10 --timeout 420 &
```

Anche in questo caso, quando l'output visualizzato da **stress** è simile a *stress: info: [2713] dispatching hogs: 10 cpu, 0 io, 0 vm, 0 hdd*, premere *INVIO* per tornare al prompt.

Chiudere la connessione alla seconda istanza di VM. **stress** continuerà a essere eseguito nell'istanza di VM.

```azurecli-interactive
exit
```

## <a name="monitor-the-active-autoscale-rules"></a>Monitorare le regole di scalabilità automatica attive
Per monitorare il numero di istanze di VM nel set di scalabilità, usare **watch**. Le regole di scalabilità automatica impiegano 5 minuti ad avviare il processo di aumento del numero di istanze in risposta al carico della CPU generato da **stress** in ognuna delle istanze di VM:

```azurecli-interactive
watch az vmss list-instances \
  --resource-group $resourcegroup_name \
  --name $scaleset_name \
  --output table
```

Al raggiungimento della soglia relativa alla CPU, le regole di scalabilità automatica aumentano il numero di istanze di VM nel set di scalabilità. L'output seguente mostra tre VM create con l'aumento automatico del numero di istanze nel set di scalabilità:

```bash
Every 2.0s: az vmss list-instances --resource-group myResourceGroup --name myScaleSet --output table

  InstanceId  LatestModelApplied    Location    Name          ProvisioningState    ResourceGroup    VmId
------------  --------------------  ----------  ------------  -------------------  ---------------  ------------------------------------
           1  True                  eastus      myScaleSet_1  Succeeded            MYRESOURCEGROUP  4f92f350-2b68-464f-8a01-e5e590557955
           2  True                  eastus      myScaleSet_2  Succeeded            MYRESOURCEGROUP  d734cd3d-fb38-4302-817c-cfe35655d48e
           4  True                  eastus      myScaleSet_4  Creating             MYRESOURCEGROUP  061b4c90-0d73-49fc-a066-19eab0b3d95c
           5  True                  eastus      myScaleSet_5  Creating             MYRESOURCEGROUP  4beff8b9-4e65-40cb-9652-43899309da27
           6  True                  eastus      myScaleSet_6  Creating             MYRESOURCEGROUP  9e4133dd-2c57-490e-ae45-90513ce3b336
```

All'arresto di **stress** nelle istanze di VM iniziali, il carico medio della CPU torna alla normalità. Dopo altri 5 minuti, le regole di scalabilità automatica riducono quindi il numero di istanze di VM. Le azioni di riduzione del numero di istanze rimuovono per prime le istanze di VM con gli ID più elevati. L'output di esempio seguente mostra l'eliminazione di un'istanza di VM con la riduzione automatica del numero di istanze nel set di scalabilità:

```bash
           6  True                  eastus      myScaleSet_6  Deleting             MYRESOURCEGROUP  9e4133dd-2c57-490e-ae45-90513ce3b336
```

Uscire da *watch* con `Ctrl-c`. La riduzione del numero di istanze nel set di scalabilità continua ogni 5 minuti con la rimozione di un'istanza di VM fino al raggiungimento del numero minimo di due istanze.


## <a name="clean-up-resources"></a>Pulire le risorse
Per rimuovere il set di scalabilità e le risorse aggiuntive, eliminare il gruppo di risorse e tutte le relative risorse con [az group delete](/cli/azure/group#az_group_delete). Il parametro `--no-wait` restituisce il controllo al prompt senza attendere il completamento dell'operazione. Il parametro `--yes` conferma che si desidera eliminare le risorse senza un prompt aggiuntivo a tale scopo.

```azurecli-interactive
az group delete --name $resourcegroup_name --yes --no-wait
```


## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione si è appreso come ridurre o aumentare automaticamente il numero di istanze in un set di scalabilità con l'interfaccia della riga di comando di Azure 2.0:

> [!div class="checklist"]
> * Usare la scalabilità automatica con un set di scalabilità
> * Creare e usare regole di scalabilità automatica
> * Sottoporre a test di stress le istanze di VM e attivare le regole di scalabilità automatica
> * Ridurre automaticamente il numero di istanze con la riduzione delle esigenze

Per altri esempi del funzionamento dei set di scalabilità di macchine virtuali, vedere gli script di esempio dell'interfaccia della riga di comando di Azure 2.0 riportati di seguito:

> [!div class="nextstepaction"]
> [Esempi di script dell'interfaccia della riga di comando di Azure 2.0 per i set di scalabilità](cli-samples.md)