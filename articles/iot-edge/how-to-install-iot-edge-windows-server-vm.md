---
title: Eseguire Azure IoT Edge in macchine virtuali Windows Server | Microsoft Docs
description: Istruzioni del programma di installazione di Azure IoT Edge in Windows Server Marketplace di macchine virtuali di
author: gregman-msft
manager: arjmands
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 03/29/2019
ms.author: gregman
ms.openlocfilehash: be7479d3f042d6e64428a07e0509907b78595200
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65159781"
---
# <a name="run-azure-iot-edge-on-windows-server-virtual-machines"></a>Eseguire Azure IoT Edge in macchine virtuali Windows Server
Il runtime di Azure IoT Edge è ciò che trasforma un dispositivo in un dispositivo IoT Edge. Il runtime può essere distribuito nei dispositivi di dimensioni pari a un server industriale o a un dispositivo Raspberry Pi. Dopo aver configurato un dispositivo con il runtime di IoT Edge, è possibile avviare la distribuzione della logica di business dal cloud.

Per altre informazioni sul funzionamento del runtime di IoT Edge e sui componenti inclusi, vedere [Informazioni sul runtime Azure IoT Edge e la relativa architettura](iot-edge-runtime.md).

Questo articolo elenca i passaggi per eseguire il runtime di Azure IoT Edge in una macchina virtuale di Windows Server 2019 usando il [Windows Server](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.WindowsServer?tab=Overview) offerta di Azure Marketplace. Seguire le istruzioni in [installare il runtime di Azure IoT Edge](how-to-install-iot-edge-windows.md) su Windows per l'uso con altre versioni.

> [!NOTE]
> Il runtime di IoT Edge in Windows Server è in [versione di anteprima pubblica](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="deploy-from-the-azure-marketplace"></a>Eseguire la distribuzione da Azure Marketplace
1.  Passare il [Windows Server](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.WindowsServer?tab=Overview) offerta di Azure Marketplace oppure cercando "Windows Server" [Azure Marketplace](https://azuremarketplace.microsoft.com/)
2.  Selezionare **ottenere adesso** 
3.  Nelle **piano Software**trovare "2019 Data Center Server Core con contenitori di Windows Server" e quindi selezionare **continua** nella finestra di dialogo successiva.
    * È anche possibile usare queste istruzioni per le altre versioni di Windows Server con i contenitori
4.  Una volta nel portale di Azure selezionare **Crea** e seguire la procedura guidata per distribuire la macchina virtuale. 
    *   Se è provato a utilizzare una macchina virtuale per la prima volta, è più semplice per usare una password e abilitare RDP e SSH nel menu della porta in ingresso pubblico. 
    *   Se si ha un carico di lavoro con utilizzo intensivo delle risorse, è consigliabile aggiornare le dimensioni della macchina virtuale aggiungendo più CPU e/o memoria.
5.  Dopo aver distribuito la macchina virtuale, configurarla per connettersi all'hub IoT:
    1.  Copiare la stringa di connessione del dispositivo dal dispositivo IoT Edge creato nell'hub IoT (è possibile seguire la guida alle procedure [Registrare un nuovo dispositivo Azure IoT Edge dal portale di Azure](how-to-register-device-portal.md) se non si ha familiarità con questo processo)
    1.  Selezionare la nuova risorsa macchina virtuale creata dal portale di Azure e aprire l'opzione **Comando Esegui**
    1.  Selezionare il **RunPowerShellScript** opzione
    1.  Copiare questo script nella finestra di comando con la stringa di connessione del dispositivo: 
        ```powershell
        . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
        Install-IoTEdge -Manual -DeviceConnectionString '<connection-string>'
        ```
    1.  Eseguire lo script per installare il runtime di Edge e impostare la stringa di connessione selezionando **eseguire**
    1.  Dopo un minuto o due, si verrà visualizzato un messaggio che il runtime di Edge è stato installato e configurato correttamente.


## <a name="deploy-from-the-azure-portal"></a>Distribuzione dal portale di Azure
1. Dal portale di Azure, cercare "Windows Server" e selezionare **Windows Server Datacenter 2019** per avviare il flusso di lavoro di creazione della macchina virtuale. 
2. Dal **selezionare un piano software** scegliere "2019 Data Center Server Core con contenitori di Windows Server", quindi selezionare **Create**
3. Completamento istruzioni di "distribuire da Azure Marketplace" passaggio 5 riportato sopra.

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
   1. Con l'ID è stato copiato, eseguire questo comando:
    
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
   * Questo comando richiederà una password, ma è possibile aggiungere l'opzione `--admin-password` impostarla più facilmente in uno script
   * L'immagine di Windows Server Core è presente comando supporto solo con desktop remoto, pertanto, se si desidera l'esperienza desktop completa, specificare `MicrosoftWindowsServer:WindowsServer:2019-Datacenter-with-Containers:latest` dell'immagine

1. Impostare la stringa di connessione del dispositivo (è possibile seguire la guida alle procedure [Registrare un nuovo dispositivo Azure IoT Edge con l'interfaccia della riga di comando di Azure](how-to-register-device-cli.md) se non si ha familiarità con questo processo):

   ```azurecli-interactive
   az vm run-command invoke -g IoTEdgeResources -n EdgeVM --command-id RunPowerShellScript --script ". {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `Install-IoTEdge -Manual -DeviceConnectionString '<connection-string>'"
   ```

## <a name="next-steps"></a>Passaggi successivi

Dopo aver eseguito il provisioning del dispositivo IoT Edge con il runtime installato, è possibile [distribuire moduli IoT Edge](how-to-deploy-modules-portal.md).

Se si verificano problemi con il runtime di Edge correttamente l'installazione, consultare il [risoluzione dei problemi](troubleshoot.md) pagina.

Per aggiornare un'installazione esistente alla versione più recente di IoT Edge, vedere [Aggiornare il daemon di sicurezza e il runtime di IoT Edge](how-to-update-iot-edge.md).

Altre informazioni sull'uso di macchine virtuali Windows il [documentazione delle macchine virtuali Windows](https://docs.microsoft.com/azure/virtual-machines/windows/).

Se vuole connettersi tramite SSH in questa macchina virtuale al termine dell'installazione, seguire le [installazione di OpenSSH per Windows Server](https://docs.microsoft.com/windows-server/administration/openssh/openssh_install_firstuse#installing-openssh-with-powershell) Guida usando powershell remota o desktop remoto.
