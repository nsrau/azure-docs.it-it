---
title: Eseguire Azure IoT Edge in macchine virtuali Ubuntu | Microsoft Docs
description: Azure IoT Edge setup instructions for Ubuntu 18.04 LTS Virtual Machines
author: toolboc
manager: veyalla
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: pdecarlo
ms.openlocfilehash: 64e2787aa282e75893fa34e6de1373e6afed09fe
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80349623"
---
# <a name="run-azure-iot-edge-on-ubuntu-virtual-machines"></a>Eseguire Azure IoT Edge in macchine virtuali Ubuntu

Il runtime di Azure IoT Edge è ciò che trasforma un dispositivo in un dispositivo IoT Edge. Il runtime può essere distribuito nei dispositivi di dimensioni pari a un server industriale o a un dispositivo Raspberry Pi. Dopo aver configurato un dispositivo con il runtime di IoT Edge, è possibile avviare la distribuzione della logica di business dal cloud.

Per altre informazioni sul funzionamento del runtime di IoT Edge e sui componenti inclusi, vedere [Informazioni sul runtime Azure IoT Edge e la relativa architettura](iot-edge-runtime.md).

Questo articolo elenca i passaggi per distribuire una macchina virtuale Ubuntu 18.04 LTS con il runtime di Azure IoT Edge installato e configurato usando una stringa di connessione del dispositivo prefornita. La distribuzione viene eseguita usando un modello di Azure Resource Manager basato su cloud-init gestito nel repository di progetto iotedge-vm-deploy.The deployment is accomplished using a [cloud-init](../virtual-machines/linux/using-cloud-init.md
) based [Azure Resource Manager template](../azure-resource-manager/templates/overview.md) maintained in the [iotedge-vm-deploy](https://github.com/Azure/iotedge-vm-deploy) project repository.

Al primo avvio, la macchina virtuale Ubuntu 18.04 LTS [installa la versione più recente del runtime di Azure IoT Edge tramite cloud-init](https://github.com/Azure/iotedge-vm-deploy/blob/master/cloud-init.txt). Imposta inoltre una stringa di connessione fornita prima dell'avvio del runtime, consentendo di configurare e connettere facilmente il dispositivo IoT Edge senza la necessità di avviare una sessione SSH o desktop remoto. 

## <a name="deploy-using-deploy-to-azure-button"></a>Deploy using Deploy to Azure Button

Il [pulsante Distribuisci in Azure](../azure-resource-manager/templates/deploy-to-azure-button.md) consente la distribuzione semplificata dei modelli di Azure Resource Manager gestiti in GitHub.The Deploy to Azure Button allows for streamlined deployment of Azure Resource Manager [templates](../azure-resource-manager/templates/overview.md) maintained on GitHub.  Questa sezione illustra l'utilizzo del pulsante Distribuisci in Azure contenuto nel repository di progetto [iotedge-vm-deploy.This](https://github.com/Azure/iotedge-vm-deploy) section will demonstrate usage of the Deploy to Azure Button contained in the iotedge-vm-deploy project repository.  


1. Deployremo una macchina virtuale Linux abilitata per Azure IoT Edge usando il modello iotedge-vm-deploy di Azure Resource Manager.  Per iniziare, fare clic sul pulsante sottostante:

    [![Pulsante Distribuisci in Azure per iotedge-vm-deploy](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fiotedge-vm-deploy%2Fmaster%2FedgeDeploy.json)

1. Nella finestra appena avviata, compilare i campi modulo disponibili:

    > [!div class="mx-imgBorder"]
    > [![Screenshot del modello iotedge-vm-deploy](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-deploy.png)](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-deploy.png)

    **Sottoscrizione:** sottoscrizione di Azure attiva in cui distribuire la macchina virtuale.

    **Gruppo**di risorse: un gruppo di risorse esistente o appena creato che contiene la macchina virtuale e le risorse associate.

    **Prefisso etichetta DNS**: Un valore obbligatorio della scelta utilizzato per anteporre il nome host della macchina virtuale.

    **Nome utente amministratore**: Un nome utente a cui verranno forniti privilegi di root per la distribuzione.

    **Stringa di connessione dispositivo**: una [stringa di connessione dispositivo](how-to-register-device.md) per un dispositivo creato all'interno dell'hub [IoT](../iot-hub/about-iot-hub.md)previsto.

    **Dimensione macchina virtuale:** [la dimensione](../cloud-services/cloud-services-sizes-specs.md) della macchina virtuale da distribuire

    **Ubuntu OS Version (Versione del sistema operativo Ubuntu):** la versione del sistema operativo Ubuntu da installare nella macchina virtuale di base.

    **Percorso:** [l'area geografica](https://azure.microsoft.com/global-infrastructure/locations/) in cui distribuire la macchina virtuale, per impostazione predefinita, viene impostato sul percorso del gruppo di risorse selezionato.

    **Tipo di autenticazione**: Scegliere **sshPublicKey** o **password** a seconda delle preferenze.

    **Password o chiave amministratore**: il valore della chiave pubblica SSH o il valore della password a seconda del tipo di autenticazione scelto.

    Una volta compilati tutti i campi, selezionare la casella di controllo nella parte inferiore della pagina per accettare i termini e selezionare **Acquista** per avviare la distribuzione.

1. Verificare che la distribuzione sia stata completata correttamente.  Una risorsa di macchina virtuale deve essere stata distribuita nel gruppo di risorse selezionato.  Prendere nota del nome del computer, `vm-0000000000000`questo dovrebbe essere nel formato . Prendere inoltre nota del **nome DNS**associato , `<dnsLabelPrefix>`che dovrebbe essere nel formato . `<location>`File con estensione cloudapp.azure.com.

    Il nome DNS può essere ottenuto dalla sezione **Panoramica** della macchina virtuale appena distribuita nel portale di Azure.The **DNS Name** can be obtained from the Overview section of the newly deployed virtual machine within the Azure portal.

    > [!div class="mx-imgBorder"]
    > [![Screenshot che mostra il nome dns della macchina virtuale iotedge](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)

1. Se si vuole SSH in questa macchina virtuale dopo l'installazione, usare il **nome DNS** associato con il comando:`ssh <adminUsername>@<DNS_Name>`

## <a name="deploy-from-azure-cli"></a>Eseguire la distribuzione dall'interfaccia della riga di comando di Azure

1. Assicurarsi di aver installato l'estensione iot dell'interfaccia della riga di comando di Azure con:Ensure that you have installed the Azure CLI iot extension with:
    ```azurecli-interactive
    az extension add --name azure-iot
    ```

1. Successivamente, se si usa l'interfaccia della riga di comando di Azure sul desktop, iniziare eseguendo l'accesso:Next, if you're using Azure CLI on your desktop, start by logging in:

   ```azurecli-interactive
   az login
   ```

1. Se hai più abbonamenti, seleziona l'abbonamento che vuoi usare:
   1. Elencare le sottoscrizioni:

      ```azurecli-interactive
      az account list --output table
      ```

   1. Copiare il campo SubscriptionID per la sottoscrizione che si vuole usare.

   1. Impostare l'abbonamento di lavoro con l'ID copiato:

      ```azurecli-interactive
      az account set -s <SubscriptionId>
      ```

1. Creare un nuovo gruppo di risorse o specificarne uno esistente nei passaggi successivi:

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

1. Creare una nuova macchina virtuale:

    Per utilizzare un `password` **authenticationType** di , vedere l'esempio seguente:

   ```azurecli-interactive
   az group deployment create \
   --name edgeVm \
   --resource-group IoTEdgeResources \
   --template-uri "https://aka.ms/iotedge-vm-deploy" \
   --parameters dnsLabelPrefix='my-edge-vm1' \
   --parameters adminUsername='<REPLACE_WITH_USERNAME>' \
   --parameters deviceConnectionString=$(az iot hub device-identity show-connection-string --device-id <REPLACE_WITH_DEVICE-NAME> --hub-name <REPLACE-WITH-HUB-NAME> -o tsv) \
   --parameters authenticationType='password' \
   --parameters adminPasswordOrKey="<REPLACE_WITH_SECRET_PASSWORD>"
   ```

    Per eseguire l'autenticazione con una chiave SSH, `sshPublicKey`è possibile farlo specificando un **authenticationType** di , quindi fornire il valore della chiave SSH nel parametro **adminPasswordOrKey** .  Di seguito è illustrato un esempio.

    ```azurecli-interactive
    #Generate the SSH Key
    ssh-keygen -m PEM -t rsa -b 4096 -q -f ~/.ssh/iotedge-vm-key -N ""  

    #Create a VM using the iotedge-vm-deploy script
    az group deployment create \
    --name edgeVm \
    --resource-group IoTEdgeResources \
    --template-uri "https://aka.ms/iotedge-vm-deploy" \
    --parameters dnsLabelPrefix='my-edge-vm1' \
    --parameters adminUsername='<REPLACE_WITH_USERNAME>' \
    --parameters deviceConnectionString=$(az iot hub device-identity show-connection-string --device-id <REPLACE_WITH_DEVICE-NAME> --hub-name <REPLACE-WITH-HUB-NAME> -o tsv) \
    --parameters authenticationType='sshPublicKey' \
    --parameters adminPasswordOrKey="$(< ~/.ssh/iotedge-vm-key.pub)"
     
    ```

1. Verificare che la distribuzione sia stata completata correttamente.  Una risorsa di macchina virtuale deve essere stata distribuita nel gruppo di risorse selezionato.  Prendere nota del nome del computer, `vm-0000000000000`questo dovrebbe essere nel formato . Prendere inoltre nota del **nome DNS**associato , `<dnsLabelPrefix>`che dovrebbe essere nel formato . `<location>`File con estensione cloudapp.azure.com.

    Il **nome DNS** può essere ottenuto dall'output in formato JSON del passaggio precedente, all'interno della sezione output **come** parte della voce **SSH pubblica.**  Il valore di questa voce può essere utilizzato per SSH nella macchina appena distribuita.

    ```bash
    "outputs": {
      "public SSH": {
        "type": "String",
        "value": "ssh <adminUsername>@<DNS_Name>"
      }
    }
    ```

    Il nome DNS può essere ottenuto anche dalla sezione **Panoramica** della macchina virtuale appena distribuita nel portale di Azure.The **DNS Name** can also be obtained from the Overview section of the newly deployed virtual machine within the Azure portal.

    > [!div class="mx-imgBorder"]
    > [![Screenshot che mostra il nome dns della macchina virtuale iotedge](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)

1. Se si vuole SSH in questa macchina virtuale dopo l'installazione, usare il **nome DNS** associato con il comando:`ssh <adminUsername>@<DNS_Name>`

## <a name="next-steps"></a>Passaggi successivi

Dopo aver eseguito il provisioning del dispositivo IoT Edge con il runtime installato, è possibile [distribuire moduli IoT Edge](how-to-deploy-modules-portal.md).

Se si verificano problemi con l'installazione corretta del runtime di IoT Edge, vedere la pagina di [risoluzione dei problemi.](troubleshoot.md)

Per aggiornare un'installazione esistente alla versione più recente di IoT Edge, vedere [Aggiornare il daemon di sicurezza e il runtime di IoT Edge](how-to-update-iot-edge.md).

Se si desidera aprire le porte per accedere alla macchina virtuale tramite SSH o altre connessioni in ingresso, fare riferimento alla documentazione di Macchine virtuali di Azure all'apertura di porte ed endpoint in una macchina virtuale LinuxIf you'd like to open up ports to access the VM through SSH or other inbound connections, refer to the Azure Virtual Machines documentation on [opening up ports and endpoints to a Linux VM](../virtual-machines/linux/nsg-quickstart.md)
