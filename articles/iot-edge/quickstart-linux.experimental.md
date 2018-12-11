---
title: Guida introduttiva per Azure IoT Edge e Linux | Microsoft Docs
description: Questa guida introduttiva descrive come distribuire codice precompilato in remoto in un dispositivo IoT Edge.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/14/2018
ms.topic: quickstart
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 2d988d70285e64414277bc2337a564aa87e45201
ms.sourcegitcommit: 2bb46e5b3bcadc0a21f39072b981a3d357559191
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2018
ms.locfileid: "52888138"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-to-a-linux-x64-device"></a>Guida introduttiva: Distribuire il primo modulo IoT Edge in un dispositivo Linux x64

Azure IoT Edge sposta la potenza del cloud sui dispositivi di Internet delle cose. In questa guida introduttiva si apprende come usare l'interfaccia cloud per distribuire in modalità remota codice predefinito in un dispositivo IoT Edge.

In questa guida introduttiva si apprende come:

1. Creare un hub IoT.
2. Registrare un dispositivo IoT Edge nell'hub IoT.
3. Installare e avviare il runtime IoT Edge nel dispositivo.
4. Distribuire in remoto un modulo in un dispositivo IoT Edge.

![Guida introduttiva sull'architettura](./media/quickstart-linux/install-edge-full.png)

Questa guida introduttiva trasforma il computer o la macchina virtuale Linux in un dispositivo IoT Edge. È quindi possibile distribuire un modulo dal portale di Azure nel dispositivo. Il modulo distribuito in questa guida introduttiva è un sensore simulato che genera dati di temperatura, umidità e pressione. Le altre esercitazioni su Azure IoT Edge si basano sulle operazioni eseguite qui tramite la distribuzione di moduli che analizzano dati simulati per ottenere informazioni aziendali accurate.

Se non si ha una sottoscrizione di Azure attiva, creare un [account gratuito](https://azure.microsoft.com/free) prima di iniziare.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Usare l'interfaccia della riga di comando di Azure per completare molti dei passaggi di questa guida introduttiva. Azure IoT include un'estensione per abilitare funzionalità aggiuntive. 

Aggiungere l'estensione Azure IoT all'istanza di Cloud Shell.

   ```azurecli-interactive
   az extension add --name azure-cli-iot-ext
   ```

## <a name="prerequisites"></a>Prerequisiti

Risorse cloud: 

* Un gruppo di risorse per la gestione di tutte le risorse usate in questa guida introduttiva. 

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

Dispositivo IoT Edge:

* Un dispositivo o macchina virtuale Linux da usare come dispositivo IoT Edge. È consigliabile usare [Azure IoT Edge on Ubuntu](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft_iot_edge.iot_edge_vm_ubuntu) fornito da Microsoft, che preinstallerà il runtime IoT Edge. Creare la macchina virtuale usando il comando seguente:

   ```azurecli-interactive
   az vm create --resource-group IoTEdgeResources --name EdgeVM --image microsoft_iot_edge:iot_edge_vm_ubuntu:ubuntu_1604_edgeruntimeonly:latest --admin-username azureuser --generate-ssh-keys --size Standard_DS1_v2
   ```

   Quando si crea una nuova macchina virtuale, prendere nota del valore di **publicIpAddress**, che viene fornito come parte dell'output del comando di creazione. Questo indirizzo IP pubblico verrà usato per connettersi alla macchina virtuale più avanti in questa guida introduttiva.

* Se si preferisce eseguire il runtime Azure IoT Edge nel sistema locale, seguire le istruzioni illustrate in [Installare il runtime di Azure IoT Edge in Linux (x64)](how-to-install-iot-edge-linux.md).

* Per usare un dispositivo basato su ARM32, ad esempio Raspberry Pi, seguire le istruzioni illustrate in [Installare il runtime di Azure IoT Edge in Linux (ARM32v7/armhf)](how-to-install-iot-edge-linux-arm.md).

## <a name="create-an-iot-hub"></a>Creare un hub IoT

Iniziare la guida introduttiva creando l'hub IoT con l'interfaccia della riga di comando di Azure.

![Creare un hub IoT](./media/quickstart-linux/create-iot-hub.png)

Per questa guida introduttiva è possibile usare il livello gratuito dell'hub IoT. Se l'hub IoT è già stato usato in passato ed già stato creato un hub gratuito, è possibile usarlo qui. Ogni sottoscrizione può avere un solo hub IoT gratuito. 

Il codice seguente crea un hub **F1** gratuito nel gruppo di risorse **IoTEdgeResources**. Sostituire *{hub_name}* con un nome univoco per l'hub IoT.

   ```azurecli-interactive
   az iot hub create --resource-group IoTEdgeResources --name {hub_name} --sku F1 
   ```

   Se si verifica un errore perché è già presente un hub gratuito nella sottoscrizione, modificare lo SKU in **S1**. Se si verifica un errore che indica che il nome dell'hub IoT non è disponibile, significa che qualcuno ha già un hub con lo stesso nome. Provare con un nuovo nome. 

## <a name="register-an-iot-edge-device"></a>Registrare un dispositivo IoT Edge

Registrare un dispositivo IoT Edge con l'hub IoT appena creato.
![Registrare un dispositivo](./media/quickstart-linux/register-device.png)

Creare un'identità del dispositivo per il dispositivo simulato in modo che possa comunicare con l'hub IoT. L'identità del dispositivo si trova nel cloud e si usa una stringa di connessione del dispositivo univoca per associare un dispositivo fisico a un'identità del dispositivo. 

Poiché i dispositivi IoT Edge si comportano e possono essere gestiti in modo diverso rispetto ai dispositivi IoT tipici, indicare con un flag `--edge-enabled` che l'identità si riferisce a un dispositivo IoT Edge. 

1. In Azure Cloud Shell immettere il comando seguente per creare un dispositivo denominato **myEdgeDevice** nell'hub.

   ```azurecli-interactive
   az iot hub device-identity create --hub-name {hub_name} --device-id myEdgeDevice --edge-enabled
   ```

   Se viene visualizzato un errore relativo alle chiavi dei criteri iothubowner, assicurarsi che Cloud Shell esegua la versione più recente dell'estensione azure-cli-iot-ext. 

2. Recuperare la stringa di connessione per il dispositivo, che collega il dispositivo fisico alla sua identità nell'hub IoT. 

   ```azurecli-interactive
   az iot hub device-identity show-connection-string --device-id myEdgeDevice --hub-name {hub_name}
   ```

3. Copiare la stringa di connessione e salvarla. Usare questo valore per configurare il runtime IoT Edge nella sezione successiva. 

## <a name="connect-the-iot-edge-device-to-iot-hub"></a>Connettere il dispositivo IoT Edge all'hub IoT

Installare e avviare il runtime Azure IoT Edge nel dispositivo IoT Edge. 
![Registrare un dispositivo](./media/quickstart-linux/start-runtime.png)

Il runtime di IoT Edge viene distribuito in tutti i dispositivi IoT Edge. È costituito da tre componenti. Il **daemon di sicurezza IoT Edge** si avvia a ogni avvio di un dispositivo Edge ed esegue l' avvio del dispositivo avviando l'agente IoT Edge. L'**agente IoT Edge** semplifica la distribuzione e il monitoraggio di moduli nel dispositivo IoT Edge, tra cui l'hub di IoT Edge. L'**hub IoT Edge** gestisce le comunicazioni tra moduli nel dispositivo IoT Edge e tra il dispositivo e l'hub IoT. 

Durante la configurazione del runtime, si immette una stringa di connessione del dispositivo. Usare la stringa recuperata tramite l'interfaccia della riga di comando di Azure. La stringa associa il dispositivo fisico all'identità del dispositivo IoT Edge in Azure. 

### <a name="set-the-connection-string-on-the-iot-edge-device"></a>Impostare la stringa di connessione sul dispositivo IoT Edge

* Se si usa Azure IoT Edge nella macchina virtuale Ubuntu, usare la stringa di connessione del dispositivo copiata in precedenza per configurare in remoto il dispositivo IoT Edge:

   ```azurecli-interactive
   az vm run-command invoke -g IoTEdgeResources -n EdgeVM --command-id RunShellScript --script '/etc/iotedge/configedge.sh "{device_connection_string}"'
   ```

   Per i passaggi rimanenti, recuperare l'indirizzo IP pubblico generato dal comando di creazione. È anche possibile trovare l'indirizzo IP pubblico nella pagina di panoramica della macchina virtuale nel portale di Azure. Usare il comando seguente per connettersi alla macchina virtuale. Sostituire **{publicIpAddress}** con l'indirizzo del computer. 

   ```azurecli-interactive
   ssh azureuser@{publicIpAddress}
   ```

* Se si esegue IoT Edge nel computer locale o in un dispositivo ARM32, aprire il file di configurazione che si trova in /etc/iotedge/config.yaml e aggiornare la variabile **device_connection_string** con il valore copiato in precedenza, quindi riavviare il daemon di sicurezza di IoT Edge per applicare le modifiche:

   ```bash
   sudo systemctl restart iotedge
   ```

>[!TIP]
>Per eseguire comandi `iotedge` sono necessari privilegi elevati. Quando ci si disconnette dal computer e si accede di nuovo per la prima volta dopo l'installazione del runtime IoT Edge, le autorizzazioni vengono aggiornate automaticamente. Fino ad allora, usare **sudo** davanti ai comandi. 

### <a name="view-the-iot-edge-runtime-status"></a>Visualizzare lo stato del runtime IoT Edge

Verificare che il runtime sia stato installato e configurato correttamente.

1. Verificare che il daemon di sicurezza IoT Edge sia in esecuzione come servizio di sistema.

   ```bash
   sudo systemctl status iotedge
   ```

   ![Osservare il daemon di sicurezza IoT Edge come servizio di sistema](./media/quickstart-linux/iotedged-running.png)

2. Se è necessario risolvere problemi del servizio, recuperare i log di servizio. 

   ```bash
   journalctl -u iotedge
   ```

3. Visualizzare i moduli in esecuzione nel dispositivo. 

   ```bash
   sudo iotedge list
   ```

   ![Visualizzare un modulo nel dispositivo](./media/quickstart-linux/iotedge-list-1.png)

Il dispositivo IoT Edge è ora configurato. È pronto per eseguire i moduli distribuiti nel cloud. 

## <a name="deploy-a-module"></a>Distribuire un modulo

Gestire il dispositivo Azure IoT Edge dal cloud per distribuire un modulo che invierà dati di telemetria all'hub IoT.
![Registrare un dispositivo](./media/quickstart-linux/deploy-module.png)

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>Visualizzare i dati generati

In questa guida introduttiva è stato creato un nuovo dispositivo IoT Edge, nel quale è stato installato il runtime di IoT Edge. È stato quindi usato il portale di Azure per eseguire il push di un modulo di IoT Edge da eseguire nel dispositivo senza dovere apportare modifiche al dispositivo stesso. In questo caso il modulo di cui è stato eseguito il push crea dati ambientali che è possibile usare per le esercitazioni.

Aprire di nuovo il prompt dei comandi nel dispositivo IoT Edge o usare la connessione SSH dall'interfaccia della riga di comando di Azure. Verificare che il modulo distribuito dal cloud sia in esecuzione nel dispositivo IoT Edge:

   ```bash
   sudo iotedge list
   ```

   ![Visualizzare tre moduli nel dispositivo](./media/quickstart-linux/iotedge-list-2.png)

Visualizzare i messaggi inviati dal modulo tempSensor:

   ```bash
   sudo iotedge logs tempSensor -f
   ```

![Visualizzare i dati dal modulo](./media/quickstart-linux/iotedge-logs.png)

Il modulo del sensore temperatura può attendere di connettersi all'hub di IoT Edge se l'ultima riga visualizzata nel log è `Using transport Mqtt_Tcp_Only`. Provare a terminare il modulo e permettere all'agente IoT Edge di riavviarlo. È possibile terminare il modulo con il comando `sudo docker stop tempSensor`.

È anche possibile visualizzare i messaggi ricevuti dall'hub IoT usando l'[estensione Azure IoT Toolkit per Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit). 

## <a name="clean-up-resources"></a>Pulire le risorse

Se si vuole continuare con le esercitazioni su IoT Edge, è possibile usare il dispositivo registrato e configurato in questa guida introduttiva. In caso contrario, è possibile eliminare le risorse di Azure create e rimuovere il runtime IoT Edge dal dispositivo.

### <a name="delete-azure-resources"></a>Eliminare le risorse di Azure

Se la macchina virtuale e l'hub IoT sono stati creati in un nuovo gruppo di risorse, è possibile eliminare il gruppo e tutte le risorse associate. Verificare il contenuto del gruppo di risorse per assicurarsi che non vi siano dati da conservare. Se non si vuole eliminare l'intero gruppo, è possibile eliminare le singole risorse.

Rimuovere il gruppo **IoTEdgeResources**.

   ```azurecli-interactive
   az group delete --name IoTEdgeResources 
   ```

### <a name="remove-the-iot-edge-runtime"></a>Rimuovere il runtime IoT Edge

Se si vuole rimuovere le installazioni dal dispositivo, usare i comandi seguenti.  

Rimuovere il runtime IoT Edge.

   ```bash
   sudo apt-get remove --purge iotedge
   ```

Quando il runtime IoT Edge viene rimosso, i contenitori creati vengono arrestati, ma rimangono sul dispositivo. Visualizzare tutti i contenitori.

   ```bash
   sudo docker ps -a
   ```

Eliminare i contenitori creati nel dispositivo dal runtime IoT Edge. Modificare il nome del contenitore tempSensor se gli è stato assegnato un nome diverso. 

   ```bash
   sudo docker rm -f tempSensor
   sudo docker rm -f edgeHub
   sudo docker rm -f edgeAgent
   ```

Rimuovere il runtime del contenitore.

   ```bash
   sudo apt-get remove --purge moby-cli
   sudo apt-get remove --purge moby-engine
   ```

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione costituisce un prerequisito per tutte le esercitazioni su IoT Edge. È possibile continuare con una delle altre esercitazioni per ottenere informazioni sui modi in cui Azure IoT Edge può contribuire alla trasformazione dei dati in informazioni aziendali dettagliate nei dispositivi perimetrali.

> [!div class="nextstepaction"]
> [Filtrare dati dei sensori tramite una funzione di Azure](tutorial-deploy-function.md)
