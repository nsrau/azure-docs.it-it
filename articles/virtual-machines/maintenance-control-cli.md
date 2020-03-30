---
title: Controllo della manutenzione
description: Informazioni su come controllare quando viene applicata la manutenzione alle macchine virtuali di Azure usando il controllo di manutenzione.
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.workload: infrastructure-services
ms.date: 11/21/2019
ms.author: cynthn
ms.openlocfilehash: 58c0964d170f49066802b955f09dab01eaf998a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250179"
---
# <a name="preview-control-updates-with-maintenance-control-and-the-azure-cli"></a>Anteprima: Controllare gli aggiornamenti con il controllo di manutenzione e l'interfaccia della riga di comando di AzurePreview: Control updates with Maintenance Control and the Azure CLI

Gestire gli aggiornamenti della piattaforma, che non richiedono un riavvio, utilizzando il controllo di manutenzione. Azure aggiorna spesso la propria infrastruttura per migliorare l'affidabilità, le prestazioni, la sicurezza o lanciare nuove funzionalità. La maggior parte degli aggiornamenti è trasparente per gli utenti. Alcuni carichi di lavoro sensibili, come giochi, streaming multimediale e transazioni finanziarie, non possono tollerare nemmeno pochi secondi di blocco o disconnessione di una macchina virtuale per la manutenzione. Il controllo della manutenzione offre la possibilità di attendere gli aggiornamenti della piattaforma e applicarli all'interno di una finestra continua di 35 giorni. 

Il controllo di manutenzione consente di decidere quando applicare gli aggiornamenti alle macchine virtuali isolate e agli host dedicati di Azure.Maintenance control lets you decide when to apply updates to your isolated VMs and Azure Dedicated Hosts.

Con il controllo della manutenzione, è possibile:
- Aggiornamenti batch in un unico pacchetto di aggiornamento.
- Attendi fino a 35 giorni per applicare gli aggiornamenti. 
- Automatizzare gli aggiornamenti della piattaforma per la finestra di manutenzione usando Funzioni di Azure.Automate platform updates for your maintenance window using Azure Functions.
- Le configurazioni di manutenzione funzionano tra sottoscrizioni e gruppi di risorse. 

> [!IMPORTANT]
> Il controllo di manutenzione è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="limitations"></a>Limitazioni

- Le macchine virtuali devono trovarsi in un [host dedicato](./linux/dedicated-hosts.md)o essere create utilizzando una dimensione di macchina [virtuale isolata.](./linux/isolation.md)
- Dopo 35 giorni, verrà applicato automaticamente un aggiornamento.
- L'utente deve disporre **dell'accesso Collaboratore risorse.**


## <a name="install-the-maintenance-extension"></a>Installare l'estensione di manutenzione

Se si sceglie di installare [l'interfaccia della riga](https://docs.microsoft.com/cli/azure/install-azure-cli) di comando di Azure in locale, è necessaria la versione 2.0.76 o successiva.

Installare `maintenance` l'estensione dell'interfaccia della riga di comando di anteprima in locale o in Cloud Shell. 

```azurecli-interactive
az extension add -n maintenance
```


## <a name="create-a-maintenance-configuration"></a>Creare una configurazione di manutenzioneCreate a maintenance configuration

Consente `az maintenance configuration create` di creare una configurazione di manutenzione. In questo esempio viene creata una configurazione di manutenzione denominata *myConfig* con ambito all'host. 

```azurecli-interactive
az group create \
   --location eastus \
   --name myMaintenanceRG
az maintenance configuration create \
   -g myMaintenanceRG \
   --name myConfig \
   --maintenanceScope host\
   --location  eastus
```

Copiare l'ID di configurazione dall'output per utilizzarlo in un secondo momento.

L'utilizzo `--maintenanceScope host` garantisce che la configurazione di manutenzione venga utilizzata per controllare gli aggiornamenti all'host.

Se si tenta di creare una configurazione con lo stesso nome, ma in una posizione diversa, verrà visualizzato un errore. I nomi di configurazione devono essere univoci per la sottoscrizione.

È possibile eseguire una `az maintenance configuration list`query per le configurazioni di manutenzione disponibili utilizzando .

```azurecli-interactive
az maintenance configuration list --query "[].{Name:name, ID:id}" -o table 
```

## <a name="assign-the-configuration"></a>Assegnare la configurazione

Usare `az maintenance assignment create` per assegnare la configurazione alla macchina virtuale isolata o all'host dedicato di Azure.Use to assign the configuration to your isolated VM or Azure Dedicated Host.

### <a name="isolated-vm"></a>VM isolata

Applicare la configurazione a una macchina virtuale usando l'ID della configurazione. Specificare `--resource-type virtualMachines` e fornire il `--resource-name`nome della macchina virtuale per `--resource-group`, e il gruppo `--location`di risorse per la macchina virtuale in e il percorso della macchina virtuale per . 

```azurecli-interactive
az maintenance assignment create \
   --resource-group myMaintenanceRG \
   --location eastus \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute \
   --configuration-assignment-name myConfig \
   --maintenance-configuration-id "/subscriptions/1111abcd-1a11-1a2b-1a12-123456789abc/resourcegroups/myMaintenanceRG/providers/Microsoft.Maintenance/maintenanceConfigurations/myConfig"
```

### <a name="dedicated-host"></a>Host dedicato

Per applicare una configurazione a un host `--resource-type hosts`dedicato, è necessario includere `--resource-parent-name` , `--resource-parent-type hostGroups`con il nome del gruppo host e . 

Il `--resource-id` parametro è l'ID dell'host. È possibile usare [az vm host get-instance-view](/cli/azure/vm/host#az-vm-host-get-instance-view) per ottenere l'ID dell'host dedicato.

```azurecli-interactive
az maintenance assignment create \
   -g myDHResourceGroup \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --configuration-assignment-name myConfig \
   --maintenance-configuration-id "/subscriptions/1111abcd-1a11-1a2b-1a12-123456789abc/resourcegroups/myDhResourceGroup/providers/Microsoft.Maintenance/maintenanceConfigurations/myConfig" \
   -l eastus \
   --resource-parent-name myHostGroup \
   --resource-parent-type hostGroups 
```

## <a name="check-configuration"></a>Controllare la configurazione

È possibile verificare che la configurazione sia stata applicata `az maintenance assignment list`correttamente oppure verificare quale configurazione è attualmente applicata utilizzando .

### <a name="isolated-vm"></a>VM isolata

```azurecli-interactive
az maintenance assignment list \
   --provider-name Microsoft.Compute \
   --resource-group myMaintenanceRG \
   --resource-name myVM \
   --resource-type virtualMachines \
   --query "[].{resource:resourceGroup, configName:name}" \
   --output table
```

### <a name="dedicated-host"></a>Host dedicato 

```azurecli-interactive
az maintenance assignment list \
   --resource-group myDHResourceGroup \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --resource-parent-name myHostGroup \
   --resource-parent-type hostGroups 
   --query "[].{ResourceGroup:resourceGroup,configName:name}" \
   -o table
```


## <a name="check-for-pending-updates"></a>Verificare la disponibilità di aggiornamenti in sospeso

Utilizzare `az maintenance update list` per verificare se sono presenti aggiornamenti in sospeso. Aggiornare --subscription come ID per la sottoscrizione che contiene la macchina virtuale.

Se non sono presenti aggiornamenti, il comando restituirà un `Resource not found...StatusCode: 404`messaggio di errore che conterrà il testo: .

Se sono presenti aggiornamenti, ne verrà restituito solo uno, anche se sono presenti più aggiornamenti in sospeso. I dati per questo aggiornamento verranno restituiti in un oggetto:The data for this update will be returned in an object:

```text
[
  {
    "impactDurationInSec": 9,
    "impactType": "Freeze",
    "maintenanceScope": "Host",
    "notBefore": "2020-03-03T07:23:04.905538+00:00",
    "resourceId": "/subscriptions/9120c5ff-e78e-4bd0-b29f-75c19cadd078/resourcegroups/DemoRG/providers/Microsoft.Compute/hostGroups/demoHostGroup/hosts/myHost",
    "status": "Pending"
  }
]
  ```

### <a name="isolated-vm"></a>VM isolata

Verificare la disponibilità di aggiornamenti in sospeso per una macchina virtuale isolata. In questo esempio, l'output viene formattato come tabella per la leggibilità.

```azurecli-interactive
az maintenance update list \
   -g myMaintenanceRg \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute \
   -o table
```

### <a name="dedicated-host"></a>Host dedicato

Per verificare la disponibilità di aggiornamenti in sospeso per un host dedicato. In questo esempio, l'output viene formattato come tabella per la leggibilità. Sostituire i valori per le risorse con i propri.

```azurecli-interactive
az maintenance update list \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   -g myHostResourceGroup \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --resource-parentname myHostGroup \
   --resource-parent-type hostGroups \
   -o table
```

## <a name="apply-updates"></a>Applicare gli aggiornamenti

Consente `az maintenance apply update` di applicare gli aggiornamenti in sospeso. In caso di esito positivo, questo comando restituirà JSON contenente i dettagli dell'aggiornamento.

### <a name="isolated-vm"></a>VM isolata

Creare una richiesta per applicare gli aggiornamenti a una macchina virtuale isolata.

```azurecli-interactive
az maintenance applyupdate create \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   --resource-group myMaintenanceRG \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute
```


### <a name="dedicated-host"></a>Host dedicato

Applicare gli aggiornamenti a un host dedicato.

```azurecli-interactive
az maintenance applyupdate create \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   --resource-group myHostResourceGroup \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --resource-parent-name myHostGroup \
   --resource-parent-type hostGroups
```

## <a name="check-the-status-of-applying-updates"></a>Controllare lo stato dell'applicazione degli aggiornamenti 

È possibile controllare lo stato `az maintenance applyupdate get`di avanzamento degli aggiornamenti utilizzando . 

È possibile `default` utilizzare come nome dell'aggiornamento per visualizzare `myUpdateName` i risultati dell'ultimo aggiornamento oppure `az maintenance applyupdate create`sostituirli con il nome dell'aggiornamento restituito durante l'esecuzione di .

```text
Status         : Completed
ResourceId     : /subscriptions/12ae7457-4a34-465c-94c1-17c058c2bd25/resourcegroups/TestShantS/providers/Microsoft.Comp
ute/virtualMachines/DXT-test-04-iso
LastUpdateTime : 1/1/2020 12:00:00 AM
Id             : /subscriptions/12ae7457-4a34-465c-94c1-17c058c2bd25/resourcegroups/TestShantS/providers/Microsoft.Comp
ute/virtualMachines/DXT-test-04-iso/providers/Microsoft.Maintenance/applyUpdates/default
Name           : default
Type           : Microsoft.Maintenance/applyUpdates
```
LastUpdateTime sarà l'ora in cui l'aggiornamento è stato completato, avviato dall'utente o dalla piattaforma nel caso in cui non sia stata utilizzata la finestra di auto-manutenzione. Se non è mai stato applicato un aggiornamento tramite il controllo di manutenzione, verrà visualizzato il valore predefinito.

### <a name="isolated-vm"></a>VM isolata

```azurecli-interactive
az maintenance applyupdate get \
   --resource-group myMaintenanceRG \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute \
   --apply-update-name default 
```

### <a name="dedicated-host"></a>Host dedicato

```azurecli-interactive
az maintenance applyupdate get \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \ 
   --resource-group myMaintenanceRG \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --resource-parent-name myHostGroup \ 
   --resource-parent-type hostGroups \
   --apply-update-name myUpdateName \
   --query "{LastUpdate:lastUpdateTime, Name:name, ResourceGroup:resourceGroup, Status:status}" \
   --output table
```


## <a name="delete-a-maintenance-configuration"></a>Eliminare una configurazione di manutenzioneDelete a maintenance configuration

Consente `az maintenance configuration delete` di eliminare una configurazione di manutenzione. L'eliminazione della configurazione rimuove il controllo di manutenzione dalle risorse associate.

```azurecli-interactive
az maintenance configuration delete \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   -g myResourceGroup \
   --name myConfig
```

## <a name="next-steps"></a>Passaggi successivi
Per ulteriori informazioni, vedere [Manutenzione e aggiornamenti](maintenance-and-updates.md).
