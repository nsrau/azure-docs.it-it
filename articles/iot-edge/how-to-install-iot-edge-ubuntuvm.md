---
title: Eseguire Azure IoT Edge in macchine virtuali Ubuntu | Microsoft Docs
description: Azure IoT Edge istruzioni di installazione per le macchine virtuali Ubuntu 18,04 LTS
author: toolboc
manager: veyalla
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: pdecarlo
ms.openlocfilehash: 050631731a04e4c2ea89d8c7792ec093d6ab316e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85800563"
---
# <a name="run-azure-iot-edge-on-ubuntu-virtual-machines"></a>Eseguire Azure IoT Edge in macchine virtuali Ubuntu

Il runtime di Azure IoT Edge è ciò che trasforma un dispositivo in un dispositivo IoT Edge. Il runtime può essere distribuito nei dispositivi di dimensioni pari a un server industriale o a un dispositivo Raspberry Pi. Dopo aver configurato un dispositivo con il runtime di IoT Edge, è possibile avviare la distribuzione della logica di business dal cloud.

Per altre informazioni sul funzionamento del runtime di IoT Edge e sui componenti inclusi, vedere [Informazioni sul runtime Azure IoT Edge e la relativa architettura](iot-edge-runtime.md).

Questo articolo elenca i passaggi per distribuire una macchina virtuale Ubuntu 18,04 LTS con il runtime di Azure IoT Edge installato e configurato usando una stringa di connessione del dispositivo prefornita. La distribuzione viene eseguita usando un [modello di Azure Resource Manager](../azure-resource-manager/templates/overview.md) basato su [cloud-init](../virtual-machines/linux/using-cloud-init.md
) gestito nel repository del progetto [iotedge-VM-deploy](https://github.com/Azure/iotedge-vm-deploy) .

Al primo avvio, la macchina virtuale Ubuntu 18,04 LTS [installa la versione più recente del runtime di Azure IOT Edge tramite cloud-init](https://github.com/Azure/iotedge-vm-deploy/blob/master/cloud-init.txt). Imposta anche una stringa di connessione fornita prima dell'avvio del runtime, consentendo di configurare e connettere facilmente il dispositivo IoT Edge senza dover avviare una sessione SSH o desktop remoto. 

## <a name="deploy-using-deploy-to-azure-button"></a>Pulsante Distribuisci con Distribuisci in Azure

Il [pulsante Distribuisci in Azure](../azure-resource-manager/templates/deploy-to-azure-button.md) consente la distribuzione semplificata dei [modelli di Azure Resource Manager](../azure-resource-manager/templates/overview.md) gestiti in GitHub.  Questa sezione illustra l'uso del pulsante Distribuisci in Azure contenuto nel repository del progetto [iotedge-VM-deploy](https://github.com/Azure/iotedge-vm-deploy) .  


1. Si distribuirà una VM Linux abilitata per Azure IoT Edge usando il modello di Azure Resource Manager iotedge-VM-deploy.  Per iniziare, fare clic sul pulsante seguente:

    [![Pulsante Distribuisci in Azure per iotedge-vm-deploy](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fiotedge-vm-deploy%2Fmaster%2FedgeDeploy.json)

1. Nella finestra appena avviata compilare i campi del modulo disponibili:

    > [!div class="mx-imgBorder"]
    > [![Screenshot che mostra il modello iotedge-VM-deploy](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-deploy.png)](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-deploy.png)

    **Sottoscrizione**: sottoscrizione di Azure attiva in cui distribuire la macchina virtuale.

    **Gruppo di risorse**: gruppo di risorse esistente o appena creato per contenere la macchina virtuale e le risorse associate.

    **Prefisso dell'etichetta DNS**: un valore obbligatorio a scelta usato per anteporre il nome host della macchina virtuale.

    **Nome utente amministratore**: un nome utente, a cui verranno forniti i privilegi radice per la distribuzione.

    **Stringa di connessione del dispositivo**: una [stringa di connessione del dispositivo](how-to-register-device.md) per un dispositivo che è stato creato all'interno dell' [Hub](../iot-hub/about-iot-hub.md).

    **VM size (dimensioni VM**): [dimensioni](../cloud-services/cloud-services-sizes-specs.md) della macchina virtuale da distribuire

    **Versione del sistema operativo Ubuntu**: versione del sistema operativo Ubuntu da installare nella macchina virtuale di base.

    **Località**: l' [area geografica](https://azure.microsoft.com/global-infrastructure/locations/) in cui distribuire la macchina virtuale. per impostazione predefinita, questo valore viene impostato sul percorso del gruppo di risorse selezionato.

    **Tipo di autenticazione**: scegliere **sshPublicKey** o **password** in base alle proprie preferenze.

    **Password amministratore o chiave**: il valore della chiave pubblica SSH o il valore della password a seconda della scelta del tipo di autenticazione.

    Dopo aver compilato tutti i campi, selezionare la casella di controllo nella parte inferiore della pagina per accettare i termini e selezionare **Acquista** per iniziare la distribuzione.

1. Verificare che la distribuzione sia stata completata correttamente.  Una risorsa della macchina virtuale dovrebbe essere stata distribuita nel gruppo di risorse selezionato.  Prendere nota del nome del computer, che deve essere nel formato `vm-0000000000000` . Prendere nota anche del **nome DNS**associato, che deve essere nel formato `<dnsLabelPrefix>` . `<location>` cloudapp.azure.com.

    Il **nome DNS** può essere ottenuto dalla sezione **Panoramica** della macchina virtuale appena distribuita all'interno del portale di Azure.

    > [!div class="mx-imgBorder"]
    > [![Screenshot che mostra il nome DNS della macchina virtuale iotedge](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)

1. Se si vuole eseguire SSH in questa macchina virtuale dopo l'installazione, usare il **nome DNS** associato con il comando:`ssh <adminUsername>@<DNS_Name>`

## <a name="deploy-from-azure-cli"></a>Eseguire la distribuzione dall'interfaccia della riga di comando di Azure

1. Assicurarsi di aver installato l'estensione dell'interfaccia della riga di comando di Azure con:
    ```azurecli-interactive
    az extension add --name azure-iot
    ```

1. Successivamente, se si usa l'interfaccia della riga di comando di Azure sul desktop, iniziare eseguendo l'accesso:

   ```azurecli-interactive
   az login
   ```

1. Se sono disponibili più sottoscrizioni, selezionare la sottoscrizione che si vuole usare:
   1. Elencare le sottoscrizioni:

      ```azurecli-interactive
      az account list --output table
      ```

   1. Copiare il campo SubscriptionID per la sottoscrizione che si vuole usare.

   1. Impostare la sottoscrizione funzionante con l'ID copiato:

      ```azurecli-interactive
      az account set -s <SubscriptionId>
      ```

1. Creare un nuovo gruppo di risorse o specificarne uno esistente nei passaggi successivi:

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

1. Creare una nuova macchina virtuale:

    Per usare un **AuthenticationType** di `password` , vedere l'esempio seguente:

   ```azurecli-interactive
   az deployment group create \
   --resource-group IoTEdgeResources \
   --template-uri "https://aka.ms/iotedge-vm-deploy" \
   --parameters dnsLabelPrefix='my-edge-vm1' \
   --parameters adminUsername='<REPLACE_WITH_USERNAME>' \
   --parameters deviceConnectionString=$(az iot hub device-identity show-connection-string --device-id <REPLACE_WITH_DEVICE-NAME> --hub-name <REPLACE-WITH-HUB-NAME> -o tsv) \
   --parameters authenticationType='password' \
   --parameters adminPasswordOrKey="<REPLACE_WITH_SECRET_PASSWORD>"
   ```

    Per eseguire l'autenticazione con una chiave SSH, è possibile eseguire questa operazione specificando un **AuthenticationType** di `sshPublicKey` , quindi specificare il valore della chiave SSH nel parametro **adminPasswordOrKey** .  Di seguito è illustrato un esempio.

    ```azurecli-interactive
    #Generate the SSH Key
    ssh-keygen -m PEM -t rsa -b 4096 -q -f ~/.ssh/iotedge-vm-key -N ""  

    #Create a VM using the iotedge-vm-deploy script
    az deployment group create \
    --resource-group IoTEdgeResources \
    --template-uri "https://aka.ms/iotedge-vm-deploy" \
    --parameters dnsLabelPrefix='my-edge-vm1' \
    --parameters adminUsername='<REPLACE_WITH_USERNAME>' \
    --parameters deviceConnectionString=$(az iot hub device-identity show-connection-string --device-id <REPLACE_WITH_DEVICE-NAME> --hub-name <REPLACE-WITH-HUB-NAME> -o tsv) \
    --parameters authenticationType='sshPublicKey' \
    --parameters adminPasswordOrKey="$(< ~/.ssh/iotedge-vm-key.pub)"
    ```

1. Verificare che la distribuzione sia stata completata correttamente.  Una risorsa della macchina virtuale dovrebbe essere stata distribuita nel gruppo di risorse selezionato.  Prendere nota del nome del computer, che deve essere nel formato `vm-0000000000000` . Prendere nota anche del **nome DNS**associato, che deve essere nel formato `<dnsLabelPrefix>` . `<location>` cloudapp.azure.com.

    Il **nome DNS** può essere ottenuto dall'output in formato JSON del passaggio precedente, all'interno della sezione **Outputs** come parte della voce **SSH pubblica** .  Il valore di questa voce può essere usato per SSH nel computer appena distribuito.

    ```bash
    "outputs": {
      "public SSH": {
        "type": "String",
        "value": "ssh <adminUsername>@<DNS_Name>"
      }
    }
    ```

    Il **nome DNS** può essere ottenuto anche dalla sezione **Panoramica** della macchina virtuale appena distribuita all'interno del portale di Azure.

    > [!div class="mx-imgBorder"]
    > [![Screenshot che mostra il nome DNS della macchina virtuale iotedge](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)

1. Se si vuole eseguire SSH in questa macchina virtuale dopo l'installazione, usare il **nome DNS** associato con il comando:`ssh <adminUsername>@<DNS_Name>`

## <a name="next-steps"></a>Passaggi successivi

Dopo aver eseguito il provisioning del dispositivo IoT Edge con il runtime installato, è possibile [distribuire moduli IoT Edge](how-to-deploy-modules-portal.md).

Se si verificano problemi con il runtime di IoT Edge installato correttamente, consultare la pagina [risoluzione dei problemi](troubleshoot.md) .

Per aggiornare un'installazione esistente alla versione più recente di IoT Edge, vedere [Aggiornare il daemon di sicurezza e il runtime di IoT Edge](how-to-update-iot-edge.md).

Per aprire le porte per accedere alla macchina virtuale tramite SSH o altre connessioni in ingresso, vedere la documentazione relativa alle macchine virtuali di Azure per l' [apertura di porte ed endpoint a una VM Linux](../virtual-machines/linux/nsg-quickstart.md)
