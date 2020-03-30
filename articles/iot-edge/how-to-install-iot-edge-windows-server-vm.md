---
title: Eseguire Azure IoT Edge nelle macchine virtuali di Windows Server Documenti Microsoft
description: Azure IoT Edge setup instructions on Windows Server Marketplace Virtual Machines
author: gregman-msft
manager: arjmands
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: philmea
ms.openlocfilehash: 5f88a21efd04c9dd24fe31e925a3b911b5ec9df2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77045914"
---
# <a name="run-azure-iot-edge-on-windows-server-virtual-machines"></a>Eseguire Azure IoT Edge in macchine virtuali Windows Server

Il runtime di Azure IoT Edge è ciò che trasforma un dispositivo in un dispositivo IoT Edge. Il runtime può essere distribuito nei dispositivi di dimensioni pari a un server industriale o a un dispositivo Raspberry Pi. Dopo aver configurato un dispositivo con il runtime di IoT Edge, è possibile avviare la distribuzione della logica di business dal cloud.

Per altre informazioni sul funzionamento del runtime di IoT Edge e sui componenti inclusi, vedere [Informazioni sul runtime Azure IoT Edge e la relativa architettura](iot-edge-runtime.md).

Questo articolo elenca i passaggi per eseguire il runtime di Azure IoT Edge in una macchina virtuale Windows Server 2019 usando l'offerta di Windows Server Azure Marketplace.This article lists the steps to run the Azure IoT Edge runtime on a Windows Server 2019 virtual machine using the [Windows Server](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview) Azure Marketplace offer. Seguire le istruzioni in Installare il runtime di [Azure IoT Edge](how-to-install-iot-edge-windows.md) in Windows per usarlo con altre versioni.

## <a name="deploy-from-the-azure-marketplace"></a>Eseguire la distribuzione da Azure Marketplace

1. Passare all'offerta di [Windows Server](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview) Azure Marketplace o eseguendo una ricerca in "Windows Server" in [Azure Marketplace](https://azuremarketplace.microsoft.com/)
2. Seleziona **GET IT NOW**
3. In **Piano software**, trovare "Windows Server 2019 Datacenter Server Core with Containers" e quindi selezionare **Continua** nella finestra di dialogo successiva.
    * È inoltre possibile utilizzare queste istruzioni per altre versioni di Windows Server con contenitori
4. Una volta nel portale di Azure selezionare **Crea** e seguire la procedura guidata per distribuire la macchina virtuale.
    * Se è la prima volta che si prova una macchina virtuale, è più semplice usare una password e abilitare RDP e SSH nel menu della porta in ingresso pubblica.
    * Se si ha un carico di lavoro con utilizzo intensivo delle risorse, è consigliabile aggiornare le dimensioni della macchina virtuale aggiungendo più CPU e/o memoria.
5. Dopo aver distribuito la macchina virtuale, configurarla per connettersi all'hub IoT:
    1. Copiare la stringa di connessione del dispositivo dal dispositivo IoT Edge creato nell'hub IoT.Copy your device connection string from your IoT Edge device created in your IoT Hub. Vedere la procedura Recuperare la stringa di connessione nel portale di Azure.See the [procedure Retrieve the connection string in the Azure portal](how-to-register-device.md#retrieve-the-connection-string-in-the-azure-portal).
    1. Selezionare la nuova risorsa macchina virtuale creata dal portale di Azure e aprire l'opzione **Comando Esegui**
    1. Selezionare l'opzione **EseguiPowerShellScript**
    1. Copiare questo script nella finestra di comando con la stringa di connessione del dispositivo:Copy this script into the command window with your device connection string:

        ```powershell
        . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
        Install-IoTEdge -Manual -DeviceConnectionString '<connection-string>'
        ```

    1. Eseguire lo script per installare il runtime IoT Edge e impostare la stringa di connessione selezionando **Esegui**
    1. Dopo un minuto o due, verrà visualizzato un messaggio che indica che il runtime Edge è stato installato e sottoposto a provisioning.

## <a name="deploy-from-the-azure-portal"></a>Distribuire dal portale di AzureDeploy from the Azure portal

1. Nel portale di Azure cercare "Windows Server" e selezionare **Windows Server 2019 Datacenter** per avviare il flusso di lavoro per la creazione di macchine virtuali.
2. Da **Selezionare un piano software** scegliere "Windows Server 2019 Datacenter Server Core with Containers", quindi selezionare **Create**
3. Completare il passaggio 5 nelle istruzioni "Distribuire da Azure Marketplace" sopra riportato.

## <a name="deploy-from-azure-cli"></a>Eseguire la distribuzione dall'interfaccia della riga di comando di Azure

1. Se si usa l'interfaccia della riga di comando di Azure sul desktop, iniziare effettuando l'accesso:

   ```azurecli-interactive
   az login
   ```

1. Se si hanno più sottoscrizioni, selezionare la sottoscrizione da usare:
   1. Elencare le sottoscrizioni:

      ```azurecli-interactive
      az account list --output table
      ```

   1. Copiare il campo SubscriptionID per la sottoscrizione da usare
   1. Eseguire questo comando con l'ID copiato:

      ```azurecli-interactive
      az account set -s {SubscriptionId}
      ```

1. Creare un nuovo gruppo di risorse o specificarne uno esistente nei passaggi successivi:

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

1. Creare una nuova macchina virtuale:

   ```azurecli-interactive
   az vm create -g IoTEdgeResources -n EdgeVM --image MicrosoftWindowsServer:WindowsServer:2019-Datacenter-Core-with-Containers:latest  --admin-username azureuser --generate-ssh-keys --size Standard_DS1_v2
   ```

   * Questo comando ti chiederà una password, ma `--admin-password` puoi aggiungere l'opzione per impostarla più facilmente in uno script
   * L'immagine Windows Server Core supporta il supporto della riga di comando solo `MicrosoftWindowsServer:WindowsServer:2019-Datacenter-with-Containers:latest` con il desktop remoto, quindi se si desidera l'esperienza desktop completa, specificare come immagine

1. Impostare la stringa di connessione del dispositivo (è possibile seguire la procedura Recupera la stringa di connessione con l'interfaccia della riga di comando di Azure se non si ha familiarità con questo processo):Set the device connection string (You can follow [the Retrieve the connection string with Azure CLI](how-to-register-device.md#retrieve-the-connection-string-with-the-azure-cli) procedure if you're familiar with this process):

   ```azurecli-interactive
   az vm run-command invoke -g IoTEdgeResources -n EdgeVM --command-id RunPowerShellScript --script ". {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `Install-IoTEdge -Manual -DeviceConnectionString '<connection-string>'"
   ```

## <a name="next-steps"></a>Passaggi successivi

Dopo aver eseguito il provisioning del dispositivo IoT Edge con il runtime installato, è possibile [distribuire moduli IoT Edge](how-to-deploy-modules-portal.md).

Se si verificano problemi con l'installazione corretta del runtime di Edge, vedere la pagina di [risoluzione dei problemi.](troubleshoot.md)

Per aggiornare un'installazione esistente alla versione più recente di IoT Edge, vedere [Aggiornare il daemon di sicurezza e il runtime di IoT Edge](how-to-update-iot-edge.md).

Per ulteriori informazioni sull'utilizzo delle macchine virtuali Windows, vedere la [documentazione](https://docs.microsoft.com/azure/virtual-machines/windows/)relativa alle macchine virtuali Windows .

Se si vuole SSH in questa macchina virtuale dopo l'installazione, seguire la guida [Installazione di OpenSSH per Windows Server](https://docs.microsoft.com/windows-server/administration/openssh/openssh_install_firstuse#installing-openssh-with-powershell) usando desktop remoto o PowerShell remoto.
