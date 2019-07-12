---
title: Eseguire Azure IoT Edge in macchine virtuali Ubuntu | Microsoft Docs
description: Istruzioni di installazione di Azure IoT Edge in macchine virtuali Ubuntu 16.04 di Azure Marketplace
author: gregman-msft
manager: arjmands
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: gregman
ms.openlocfilehash: 8275bceca1a18f49eb7eeece66a3866d77c47635
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67796169"
---
# <a name="run-azure-iot-edge-on-ubuntu-virtual-machines"></a>Eseguire Azure IoT Edge in macchine virtuali Ubuntu

Il runtime di Azure IoT Edge è ciò che trasforma un dispositivo in un dispositivo IoT Edge. Il runtime può essere distribuito nei dispositivi di dimensioni pari a un server industriale o a un dispositivo Raspberry Pi. Dopo aver configurato un dispositivo con il runtime di IoT Edge, è possibile avviare la distribuzione della logica di business dal cloud.

Per altre informazioni sul funzionamento del runtime di IoT Edge e sui componenti inclusi, vedere [Informazioni sul runtime Azure IoT Edge e la relativa architettura](iot-edge-runtime.md).

Questo articolo elenca i passaggi per eseguire il runtime Azure IoT Edge in una macchina virtuale Ubuntu 16.04 usando l'[offerta Azure IoT Edge on Ubuntu di Azure Marketplace](https://aka.ms/azure-iot-edge-ubuntuvm) preconfigurata. 

Al primo avvio, la macchina virtuale con Azure IoT Edge on Ubuntu preinstalla la versione più recente del runtime Azure IoT Edge. Include anche uno script per impostare la stringa di connessione e quindi riavviare il runtime, che può essere attivato in remoto tramite il portale delle macchine virtuali di Azure o la riga di comando di Azure, per semplificare la configurazione e la connessione al dispositivo IoT Edge senza la necessità di avviare una sessione Desktop remoto o SSH. Lo script aspetta a impostare la stringa di connessione fino a quando il client IoT Edge non è completamente installato, in modo che non sia necessario integrare tale aspetto nell'automazione.

## <a name="deploy-from-the-azure-marketplace"></a>Eseguire la distribuzione da Azure Marketplace
1.  Passare all'offerta [Azure IoT Edge on Ubuntu](https://aka.ms/azure-iot-edge-ubuntuvm) di Marketplace o cercare "Azure IoT Edge on Ubuntu" in [Azure Marketplace](https://azuremarketplace.microsoft.com/)
2.  Selezionare **SCARICA ADESSO** e quindi **Continua** nella finestra di dialogo successiva.
3.  Una volta nel portale di Azure selezionare **Crea** e seguire la procedura guidata per distribuire la macchina virtuale. 
    *   Se è la prima volta che si usa una macchina virtuale, è più semplice usare una password e abilitare SSH nel menu della porta in ingresso pubblica. 
    *   Se si ha un carico di lavoro con utilizzo intensivo delle risorse, è consigliabile aggiornare le dimensioni della macchina virtuale aggiungendo più CPU e/o memoria.
4.  Dopo aver distribuito la macchina virtuale, configurarla per connettersi all'hub IoT:
    1.  Copiare la stringa di connessione del dispositivo dal dispositivo IoT Edge creato nell'hub IoT (è possibile seguire la guida alle procedure [Registrare un nuovo dispositivo Azure IoT Edge dal portale di Azure](how-to-register-device-portal.md) se non si ha familiarità con questo processo)
    1.  Selezionare la nuova risorsa macchina virtuale creata dal portale di Azure e aprire l'opzione **Comando Esegui**
    1.  Selezionare l'opzione **RunShellScript**
    1.  Eseguire lo script seguente tramite la finestra di comando con la stringa di connessione del dispositivo: `/etc/iotedge/configedge.sh “{device_connection_string}”`
    1.  Selezionare **Esegui**
    1.  Attendere qualche minuto e dovrebbe venire visualizzato un messaggio di conferma che indica che la stringa di connessione è stata impostata correttamente.


## <a name="deploy-from-the-azure-portal"></a>Distribuzione dal portale di Azure
Dal portale di Azure, cercare "Azure IoT Edge" e selezionare **Ubuntu Server 16.04 LTS + Azure IoT Edge runtime** (Server Ubuntu 16.04 LTS + runtime Azure IoT Edge) per avviare il flusso di lavoro di creazione della macchina virtuale. Completare quindi i passaggi 3 e 4 nella sezione "Eseguire la distribuzione da Azure Marketplace" precedente.

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
    
   1. Copiare il campo ID sottoscrizione per la sottoscrizione da usare.

   1. Impostare la sottoscrizione di lavoro con l'ID appena copiata:
    
      ```azurecli-interactive 
      az account set -s {SubscriptionId}
      ```
    
1. Creare un nuovo gruppo di risorse o specificarne uno esistente nei passaggi successivi:

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

1. Accettare le condizioni d'uso per la macchina virtuale. Se si desidera esaminare le condizioni prima di tutto, seguire i passaggi descritti in [Distribuisci da Azure Marketplace](#deploy-from-the-azure-marketplace).

   ```azurecli-interactive
   az vm image accept-terms --urn microsoft_iot_edge:iot_edge_vm_ubuntu:ubuntu_1604_edgeruntimeonly:latest
   ```

1. Creare una nuova macchina virtuale:

   ```azurecli-interactive
   az vm create --resource-group IoTEdgeResources --name EdgeVM --image microsoft_iot_edge:iot_edge_vm_ubuntu:ubuntu_1604_edgeruntimeonly:latest --admin-username azureuser --generate-ssh-keys
   ```

1. Impostare la stringa di connessione del dispositivo (è possibile seguire la guida alle procedure [Registrare un nuovo dispositivo Azure IoT Edge con l'interfaccia della riga di comando di Azure](how-to-register-device-cli.md) se non si ha familiarità con questo processo):

   ```azurecli-interactive
   az vm run-command invoke -g IoTEdgeResources -n EdgeVM --command-id RunShellScript --script "/etc/iotedge/configedge.sh '{device_connection_string}'"
   ```

Se si desidera connettersi tramite SSH a questa macchina virtuale al termine dell'installazione, usare l'indirizzo IP pubblico con il comando: `ssh azureuser@{publicIpAddress}`


## <a name="next-steps"></a>Passaggi successivi

Dopo aver eseguito il provisioning del dispositivo IoT Edge con il runtime installato, è possibile [distribuire moduli IoT Edge](how-to-deploy-modules-portal.md).

Se si sono verificati problemi con il runtime di IoT Edge correttamente l'installazione, consultare il [risoluzione dei problemi](troubleshoot.md) pagina.

Per aggiornare un'installazione esistente alla versione più recente di IoT Edge, vedere [Aggiornare il daemon di sicurezza e il runtime di IoT Edge](how-to-update-iot-edge.md).
