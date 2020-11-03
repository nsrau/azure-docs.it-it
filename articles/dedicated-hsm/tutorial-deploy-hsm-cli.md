---
title: "Esercitazione: Distribuzione in una rete virtuale esistente con l'interfaccia della riga di comando di Azure - Modulo di protezione hardware dedicato di Azure | Microsoft Docs"
description: Esercitazione che illustra come distribuire un modulo di protezione hardware dedicato in una rete virtuale esistente con l'interfaccia della riga di comando.
services: dedicated-hsm
documentationcenter: na
author: msmbaldwin
manager: rkarlin
editor: ''
ms.service: key-vault
ms.topic: tutorial
ms.custom: mvc, seodec18, devx-track-azurecli
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/20/2020
ms.author: mbaldwin
ms.openlocfilehash: d175ac75ce76836d012cdd04d4dbd7d81ffda584
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92460700"
---
# <a name="tutorial-deploying-hsms-into-an-existing-virtual-network-using-the-azure-cli"></a>Esercitazione: Distribuzione di moduli di protezione hardware in una rete virtuale esistente usando l'interfaccia della riga di comando di Azure

HSM dedicato di Azure offre un dispositivo fisico ad uso esclusivo del cliente, con controllo amministrativo completo e piena responsabilità di gestione. Con l'uso di dispositivi fisici, per Microsoft diventa necessario controllarne l'allocazione per garantire una gestione efficace della capacità. Di conseguenza, il servizio HSM dedicato non sarà normalmente visibile per il provisioning delle risorse all'interno di una sottoscrizione di Azure. Per accedere al servizio HSM dedicato, i clienti di Azure dovranno prima contattare il rispettivo account executive Microsoft per richiedere la registrazione per tale servizio. Solo al termine di tale processo sarà possibile effettuare il provisioning. 

Questa esercitazione illustra un processo di provisioning tipico in cui:

- Un cliente ha già una rete virtuale.
- Il cliente ha una macchina virtuale.
- Il cliente deve aggiungere risorse HSM (Hardware Security Module, modulo di protezione hardware) all'ambiente esistente.

L'architettura di una tipica distribuzione in più aree a disponibilità elevata potrebbe essere simile alla seguente:

![Distribuzione in più aree](media/tutorial-deploy-hsm-cli/high-availability-architecture.png)

L'esercitazione è incentrata sull'integrazione di una coppia di moduli di protezione hardware e del necessario gateway ExpressRoute (subnet 1 nella figura sopra) in una rete virtuale esistente (rete virtuale 1 nella figura sopra).  Tutte le altre risorse sono risorse standard di Azure. Lo stesso processo di integrazione può essere usato per i moduli di protezione hardware nella subnet 4 della rete virtuale 3 della figura sopra.

## <a name="prerequisites"></a>Prerequisiti

HSM dedicato di Azure non è attualmente disponibile nel portale di Azure. L'interazione con il servizio avverrà interamente tramite riga di comando o con PowerShell. In questa esercitazione verrà usata l'interfaccia della riga di comando in Azure Cloud Shell. Se non si ha familiarità con l'interfaccia della riga di comando di Azure, seguire le istruzioni introduttive disponibili nell'[introduzione all'interfaccia della riga di comando di Azure 2.0](/cli/azure/get-started-with-azure-cli?view=azure-cli-latest&preserve-view=true).

Presupposti:

- È stato completato il processo di registrazione di HSM dedicato di Azure.
- Si è ottenuta l'approvazione per l'uso del servizio. In caso contrario, contattare il rappresentante Microsoft per informazioni dettagliate.
- È stato creato un gruppo di risorse per le risorse di seguito, a cui verranno aggiunte le nuove risorse distribuite nel corso dell'esercitazione.
- Sono già state create la rete virtuale, la subnet e le macchine virtuali necessarie, come illustrato nel diagramma sopra, e si intende ora integrare 2 moduli di protezione hardware in tale distribuzione.

Tutte le istruzioni riportate di seguito presuppongono che si sia già passati al portale di Azure e si sia già aperto Cloud Shell, selezionando "\>\_" in alto a destra nel portale.

## <a name="provisioning-a-dedicated-hsm"></a>Provisioning di un modulo di protezione hardware dedicato

Il provisioning dei moduli di protezione hardware e la relativa integrazione in una rete virtuale esistente tramite gateway ExpressRoute verranno convalidati con ssh. Questa convalida consente di verificare la raggiungibilità e la disponibilità di base del dispositivo HSM per qualsiasi ulteriore attività di configurazione.

### <a name="validating-feature-registration"></a>Convalida della registrazione della funzionalità

Come indicato sopra, per qualsiasi attività di provisioning è necessaria la registrazione del servizio HSM dedicato per la sottoscrizione. Per convalidare la registrazione, eseguire questi comandi in Cloud Shell nel portale di Azure.

```azurecli
az feature show \
   --namespace Microsoft.HardwareSecurityModules \
   --name AzureDedicatedHSM
```

I comandi devono restituire lo stato "Registered", come illustrato di seguito. Se i comandi non restituiscono "Registered", è necessario registrarsi per il servizio contattando il rappresentante Microsoft.

![Stato della sottoscrizione](media/tutorial-deploy-hsm-cli/subscription-status.png)

### <a name="creating-hsm-resources"></a>Creazione delle risorse HSM

Prima di creare risorse HSM, è necessario soddisfare alcuni prerequisiti di risorse. È necessario avere una rete virtuale con intervalli di subnet per il calcolo, i moduli di protezione hardware e il gateway. I comandi seguenti offrono un esempio di come creare una rete virtuale di questo tipo.

```azurecli
az network vnet create \
  --name myHSM-vnet \
  --resource-group myRG \
  --address-prefix 10.2.0.0/16 \
  --subnet-name compute \
  --subnet-prefix 10.2.0.0/24
```

```azurecli
az network vnet subnet create \
  --vnet-name myHSM-vnet \
  --resource-group myRG \
  --name hsmsubnet \
  --address-prefixes 10.2.1.0/24 \
  --delegations Microsoft.HardwareSecurityModules/dedicatedHSMs
```

```azurecli
az network vnet subnet create \
  --vnet-name myHSM-vnet \
  --resource-group myRG \
  --name GatewaySubnet \
  --address-prefixes 10.2.255.0/26
```

>[!NOTE]
>La configurazione più importante da tenere presente per la rete virtuale è l'impostazione delle deleghe della subnet per il dispositivo modulo di protezione hardware su "Microsoft.HardwareSecurityModules/dedicatedHSMs".  Se questa opzione non è impostata, il provisioning dei moduli di protezione hardware non funzionerà.

Dopo aver configurato la rete, usare questi comandi dell'interfaccia della riga di comando di Azure per effettuare il provisioning dei moduli di protezione hardware.

1. Usare il comando [az dedicated-hsm create](/cli/azure/ext/hardware-security-modules/dedicated-hsm#ext_hardware_security_modules_az_dedicated_hsm_create) per effettuare il provisioning del primo modulo di protezione hardware. Il modulo di protezione hardware è denominato hsm1. Sostituire la sottoscrizione:

   ```azurecli
   az dedicated-hsm create --location westus --name hsm1 --resource-group myRG --network-profile-network-interfaces \
        /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Network/virtualNetworks/MyHSM-vnet/subnets/MyHSM-vnet
   ```

   Il completamento di questa distribuzione richiederà circa 25-30 minuti, impiegati prevalentemente per i dispositivi HSM.

1. Per visualizzare un modulo di protezione hardware corrente, eseguire il comando [az dedicated-hsm show](/cli/azure/ext/hardware-security-modules/dedicated-hsm#ext_hardware_security_modules_az_dedicated_hsm_show):

   ```azurecli
   az dedicated-hsm show --resource group myRG --name hsm1
   ```

1. Effettuare il provisioning del secondo modulo di protezione hardware usando questo comando:

   ```azurecli
   az dedicated-hsm create --location westus --name hsm2 --resource-group myRG --network-profile-network-interfaces \
        /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Network/virtualNetworks/MyHSM-vnet/subnets/MyHSM-vnet
   ```

1. Eseguire il comando [az dedicated-hsm list](/cli/azure/ext/hardware-security-modules/dedicated-hsm#ext_hardware_security_modules_az_dedicated_hsm_list) per visualizzare i dettagli dei moduli di protezione hardware correnti:

   ```azurecli
   az dedicated-hsm list --resource-group myRG
   ```

Sono disponibili altri comandi che potrebbero risultare utili. Usare il comando [az dedicated-hsm update](/cli/azure/ext/hardware-security-modules/dedicated-hsm#ext_hardware_security_modules_az_dedicated_hsm_update) per aggiornare un modulo di protezione hardware:

```azurecli
az dedicated-hsm update --resource-group myRG –name hsm1
```

Per eliminare un modulo di protezione hardware, usare il comando [az dedicated-hsm delete](/cli/azure/ext/hardware-security-modules/dedicated-hsm#ext_hardware_security_modules_az_dedicated_hsm_delete):

```azurecli
az dedicated-hsm delete --resource-group myRG –name hsm1
```

## <a name="verifying-the-deployment"></a>Verifica della distribuzione

Per verificare che i dispositivi siano stati sottoposti a provisioning e visualizzarne gli attributi, eseguire il set di comandi riportato di seguito. Assicurarsi che il gruppo di risorse sia impostato correttamente e che il nome delle risorse sia esattamente quello riportato nel file di parametri.

```azurecli
subid=$(az account show --query id --output tsv)
az resource show \
   --ids /subscriptions/$subid/resourceGroups/myRG/providers/Microsoft.HardwareSecurityModules/dedicatedHSMs/HSM1
az resource show \
   --ids /subscriptions/$subid/resourceGroups/myRG/providers/Microsoft.HardwareSecurityModules/dedicatedHSMs/HSM2
```

L'output sarà simile al seguente:

```json
{
    "id": n/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/HSM-RG/providers/Microsoft.HardwareSecurityModules/dedicatedHSMs/HSMl",
    "identity": null,
    "kind": null,
    "location": "westus",
    "managedBy": null,
    "name": "HSM1",
    "plan": null,
    "properties": {
        "networkProfile": {
            "networkInterfaces": [
            {
            "id": n/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/HSM-RG/providers/Microsoft.Network/networkInterfaces/HSMl_HSMnic", "privatelpAddress": "10.0.2.5",
            "resourceGroup": "HSM-RG"
            }
            L
            "subnet": {
                "id": n/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/HSM-RG/providers/Microsoft.Network/virtualNetworks/demo-vnet/subnets/hsmsubnet", "resourceGroup": "HSM-RG"
            }
        },
        "provisioningState": "Succeeded",
        "stampld": "stampl",
        "statusMessage": "The Dedicated HSM device is provisioned successfully and ready to use."
    },
    "resourceGroup": "HSM-RG",
    "sku": {
        "capacity": null,
        "family": null,
        "model": null,
        "name": "SafeNet Luna Network HSM A790",
        "size": null,
        "tier": null
    },
    "tags": {
        "Environment": "prod",
        "resourceType": "Hsm"
    },
    "type": "Microsoft.HardwareSecurityModules/dedicatedHSMs"
}
```

Sarà ora possibile anche visualizzare le risorse con [Azure Resource Explorer](https://resources.azure.com/).   In Resource Explorer espandere "subscriptions" (sottoscrizioni) a sinistra, la sottoscrizione specifica per HSM dedicato, "resource groups" (gruppi di risorse) e quindi il gruppo di risorse usato e infine selezionare l'elemento "resources" (risorse).

## <a name="testing-the-deployment"></a>Test della distribuzione

Il test della distribuzione prevede la connessione a una macchina virtuale che può accedere a uno o più moduli di protezione hardware e quindi la connessione diretta al dispositivo HSM. Queste azioni confermeranno la raggiungibilità del modulo di protezione hardware.
Per la connessione alla macchina virtuale viene usato lo strumento ssh. Il comando sarà simile al seguente, ma con il nome amministratore e il nome DNS specificati nel parametro.

`ssh adminuser@hsmlinuxvm.westus.cloudapp.azure.com`

Nel comando precedente è anche possibile usare l'indirizzo IP della VM al posto del nome DNS. Se il comando ha esito positivo, verrà richiesta una password che dovrà essere immessa. Dopo aver eseguito l'accesso alla macchina virtuale, è possibile accedere al modulo di protezione hardware usando l'indirizzo IP privato riportato nel portale per la risorsa interfaccia di rete associata al modulo di protezione hardware.

![Elenco dei componenti](media/tutorial-deploy-hsm-cli/resources.png)

>[!NOTE]
>Si noti la casella di controllo "Mostra tipi nascosti", la cui selezione consente di visualizzare le risorse HSM.

Nello screenshot precedente facendo clic su "HSM1_HSMnic" o "HSM2_HSMnic" verrà visualizzato l'indirizzo IP privato appropriato. In alternativa, il comando `az resource show` usato in precedenza consente di identificare l'indirizzo IP corretto. 

Quando si ha l'indirizzo IP corretto, eseguire questo comando sostituendo l'indirizzo:

`ssh tenantadmin@10.0.2.4`

Se il comando ha esito positivo, verrà richiesta una password. La password predefinita è PASSWORD e il modulo di protezione hardware chiederà per prima cosa di modificarla. Impostare quindi una password complessa e usare il meccanismo preferito dalla propria organizzazione per archiviare la password ed evitarne la perdita.

>[!IMPORTANT]
>In caso di perdita di questa password, sarà necessario reimpostare il modulo di protezione hardware perdendo così le chiavi.

Dopo aver stabilito la connessione al modulo di protezione hardware con ssh, eseguire questo comando per verificare che il modulo di protezione hardware sia operativo.

`hsm show`

L'output sarà simile a quello riportato nell'immagine seguente:

![Screenshot che mostra l'output nella finestra di PowerShell.](media/tutorial-deploy-hsm-cli/hsm-show-output.png)

A questo punto, sono state allocate tutte le risorse per una distribuzione a disponibilità elevata con due moduli di protezione hardware e sono stati convalidati l'accesso e lo stato operativo. Qualsiasi ulteriore configurazione o test prevede operazioni aggiuntive con il dispositivo HSM stesso. A tale scopo, seguire le istruzioni riportate nel capitolo 7 della guida all'amministrazione di Gemalto Luna Network HSM 7 per inizializzare il modulo di protezione hardware e creare partizioni. Tutta la documentazione e il software sono scaricabili direttamente da Gemalto dopo aver effettuato la registrazione nel portale di supporto tecnico di Gemalto e aver acquisito un ID cliente. Per ottenere tutti i componenti necessari, scaricare la versione 7.2 del software client.

## <a name="delete-or-clean-up-resources"></a>Eliminazione o pulizia delle risorse

Se il dispositivo HSM non è più necessario, può essere eliminato come risorsa e tornare a far parte del pool libero. In questo caso, la fonte di preoccupazione è ovviamente costituita dagli eventuali dati sensibili dei clienti presenti nel dispositivo. Il modo migliore per azzerare un dispositivo consiste nell'usare tre volte una password errata per l'amministratore del modulo di protezione hardware. Si noti che non si tratta dell'amministratore dell'appliance ma dell'amministratore effettivo del modulo di protezione hardware. Come misura di sicurezza per proteggere il materiale della chiave, non è possibile eliminare il dispositivo come una risorsa di Azure finché si trova nello stato azzerato.

> [!NOTE]
> In caso di problemi con la configurazione del dispositivo Gemalto, contattare il [supporto tecnico di Gemalto](https://safenet.gemalto.com/technical-support/).

Se tutte le risorse di questo gruppo di risorse non sono più necessarie, è possibile rimuoverle tutte con il comando seguente:

```azurecli
az group delete \
   --resource-group myRG \
   --name HSMdeploy \
   --verbose
```

## <a name="next-steps"></a>Passaggi successivi

Al termine dei passaggi di questa esercitazione, le risorse di HSM dedicato sono state sottoposte a provisioning e si ha una rete virtuale con i moduli di protezione hardware e gli ulteriori componenti di rete necessari per consentire la comunicazione con il modulo di protezione hardware.  Si è ora in condizione di completare la distribuzione con le altre risorse necessarie per l'architettura di distribuzione preferita. Per altre informazioni utili per la pianificazione della distribuzione, vedere i documenti della sezione relativa ai concetti.
È consigliabile una progettazione con due moduli di protezione hardware in un'area primaria per la disponibilità a livello di rack e due moduli di protezione hardware in un'area secondaria per la disponibilità a livello di area. 

* [Disponibilità elevata](high-availability.md)
* [Sicurezza fisica](physical-security.md)
* [Rete](networking.md)
* [Supporto](supportability.md)
* [Monitoring](monitoring.md)
