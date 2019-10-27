---
title: Eseguire Azure IoT Edge in macchine virtuali Windows Server | Microsoft Docs
description: Azure IoT Edge istruzioni di installazione nelle macchine virtuali di Windows Server Marketplace
author: gregman-msft
manager: arjmands
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: gregman
ms.openlocfilehash: b32bbfa5e849c1a0490bba5d09d1838268033b26
ms.sourcegitcommit: c4700ac4ddbb0ecc2f10a6119a4631b13c6f946a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2019
ms.locfileid: "72964670"
---
# <a name="run-azure-iot-edge-on-windows-server-virtual-machines"></a>Eseguire Azure IoT Edge in macchine virtuali Windows Server

Il runtime di Azure IoT Edge è ciò che trasforma un dispositivo in un dispositivo IoT Edge. Il runtime può essere distribuito nei dispositivi di dimensioni pari a un server industriale o a un dispositivo Raspberry Pi. Dopo aver configurato un dispositivo con il runtime di IoT Edge, è possibile avviare la distribuzione della logica di business dal cloud.

Per altre informazioni sul funzionamento di runtime di IoT Edge e sui componenti inclusi, vedere [Informazioni sul runtime di Azure IoT Edge e la relativa architettura](iot-edge-runtime.md).

Questo articolo elenca i passaggi per eseguire il runtime di Azure IoT Edge in una macchina virtuale Windows Server 2019 usando l'offerta di [Windows Server](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview) Azure Marketplace. Seguire le istruzioni in [installare il runtime di Azure IOT Edge](how-to-install-iot-edge-windows.md) in Windows per l'uso con altre versioni.

## <a name="deploy-from-the-azure-marketplace"></a>Eseguire la distribuzione da Azure Marketplace

1.  Passare all'offerta di [Windows Server](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview) Azure Marketplace o cercare "Windows Server" in [Azure Marketplace](https://azuremarketplace.microsoft.com/)
2.  Selezionare **Get it now** 
3.  Nel **piano software**individuare "Windows Server 2019 Datacenter Server Core with containers", quindi selezionare **continua** nella finestra di dialogo successiva.
    * È anche possibile usare queste istruzioni per altre versioni di Windows Server con i contenitori
4.  Una volta nel portale di Azure selezionare **Crea** e seguire la procedura guidata per distribuire la macchina virtuale. 
    *   Se è la prima volta che si prova una macchina virtuale, è più semplice usare una password e abilitare RDP e SSH nel menu della porta pubblica in ingresso. 
    *   Se si ha un carico di lavoro con utilizzo intensivo delle risorse, è consigliabile aggiornare le dimensioni della macchina virtuale aggiungendo più CPU e/o memoria.
5.  Dopo aver distribuito la macchina virtuale, configurarla per connettersi all'hub IoT:
    1.  Copiare la stringa di connessione del dispositivo dal dispositivo IoT Edge creato nell'hub Internet. Vedere la procedura [recuperare la stringa di connessione nel portale di Azure](how-to-register-device.md#retrieve-the-connection-string-in-the-azure-portal).
    1.  Selezionare la nuova risorsa macchina virtuale creata dal portale di Azure e aprire l'opzione **Comando Esegui**
    1.  Selezionare l'opzione **RunPowerShellScript**
    1.  Copiare questo script nella finestra di comando con la stringa di connessione del dispositivo: 
        ```powershell
        . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
        Install-IoTEdge -Manual -DeviceConnectionString '<connection-string>'
        ```
    1.  Eseguire lo script per installare il runtime di IoT Edge e impostare la stringa di connessione selezionando **Esegui** .
    1.  Dopo un paio di minuti, viene visualizzato un messaggio che mostra che il runtime di Edge è stato installato e ne è stato effettuato il provisioning.

## <a name="deploy-from-the-azure-portal"></a>Eseguire la distribuzione dalla portale di Azure

1. Dal portale di Azure cercare "Windows Server" e selezionare **Windows server 2019 datacenter** per avviare il flusso di lavoro di creazione della macchina virtuale. 
2. In **selezionare un piano software** scegliere "Windows Server 2019 Datacenter Server Core with containers", quindi selezionare **Crea** .
3. Completare il passaggio 5 nelle istruzioni "Deploy from the Azure Marketplace" illustrate in precedenza.

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

   * Questo comando richiederà una password, ma è possibile aggiungere l'opzione `--admin-password` per impostarla più facilmente in uno script
   * L'immagine di Windows Server Core dispone del supporto della riga di comando solo con desktop remoto, pertanto se si desidera l'esperienza desktop completa, specificare `MicrosoftWindowsServer:WindowsServer:2019-Datacenter-with-Containers:latest` come immagine

1. Impostare la stringa di connessione del dispositivo. se non si ha familiarità con questo processo, è possibile seguire la procedura [per recuperare la stringa di connessione con l'interfaccia della riga di comando di Azure](how-to-register-device.md#retrieve-the-connection-string-with-the-azure-cli) :

   ```azurecli-interactive
   az vm run-command invoke -g IoTEdgeResources -n EdgeVM --command-id RunPowerShellScript --script ". {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `Install-IoTEdge -Manual -DeviceConnectionString '<connection-string>'"
   ```

## <a name="next-steps"></a>Passaggi successivi

Dopo aver eseguito il provisioning del dispositivo IoT Edge con il runtime installato, è possibile [distribuire moduli IoT Edge](how-to-deploy-modules-portal.md).

Se si verificano problemi con il runtime di Edge installato correttamente, consultare la pagina [risoluzione dei problemi](troubleshoot.md) .

Per aggiornare un'installazione esistente alla versione più recente di IoT Edge, vedere [Aggiornare il daemon di sicurezza e il runtime di IoT Edge](how-to-update-iot-edge.md).

Per ulteriori informazioni sull'utilizzo delle macchine virtuali Windows, vedere la [documentazione macchine virtuali Windows](https://docs.microsoft.com/azure/virtual-machines/windows/).

Se si vuole usare SSH in questa macchina virtuale dopo l'installazione, seguire le istruzioni [di installazione di OpenSSH per Windows Server](https://docs.microsoft.com/windows-server/administration/openssh/openssh_install_firstuse#installing-openssh-with-powershell) con desktop remoto o PowerShell remoto.
