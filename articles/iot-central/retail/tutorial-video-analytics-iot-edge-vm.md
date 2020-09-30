---
title: Esercitazione - Creare un'istanza di IoT Edge per l'analisi video in Azure IoT Central (macchina virtuale Linux)
description: Questa esercitazione mostra come creare in una macchina virtuale Linux un'istanza di IoT Edge per l'analisi video da usare con il modello di applicazione Analisi video - rilevamento movimento e oggetti.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.author: nandab
author: KishorIoT
ms.date: 07/31/2020
ms.openlocfilehash: 0b39ec9c8cb70adac1474b2647ac1c9591b9d5cd
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/15/2020
ms.locfileid: "90526392"
---
# <a name="tutorial-create-an-iot-edge-instance-for-video-analytics-linux-vm"></a>Esercitazione: Creare un'istanza di IoT Edge per l'analisi video (macchina virtuale Linux)

Azure IoT Edge è un servizio completamente gestito che offre funzionalità di Cloud Intelligence a livello locale tramite la distribuzione e l'esecuzione di:

* Logica personalizzata
* Servizi di Azure
* Intelligenza artificiale

In IoT Edge questi servizi vengono eseguiti direttamente su dispositivi IoT multipiattaforma e ciò consente di eseguire una soluzione IoT in modo sicuro e su larga scala nel cloud o offline.

Questa esercitazione mostra come preparare un dispositivo IoT Edge in una macchina virtuale di Azure. L'istanza di IoT Edge esegue i moduli di Analisi video live usati dal modello di applicazione Analisi video - rilevamento movimento e oggetti di Azure IoT Central.

In questa esercitazione verranno illustrate le procedure per:
> [!div class="checklist"]
> * Creare una macchina virtuale di Azure con il runtime Azure IoT Edge installato
> * Preparare l'installazione di IoT Edge per ospitare il modulo di Analisi video live e connettersi a IoT Central

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, completare l'esercitazione precedente [Creare un'applicazione di analisi video in Azure IoT Central](./tutorial-video-analytics-create-app.md).

È anche necessaria una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, è possibile crearne una gratuitamente nella [pagina di iscrizione ad Azure](https://aka.ms/createazuresubscription).

## <a name="deploy-azure-iot-edge"></a>Eseguire la distribuzione di Azure IoT Edge

Per creare una macchina virtuale di Azure con la versione più recente del runtime IoT Edge e i moduli di Analisi video live installati, selezionare il pulsante seguente:

[![Pulsante Distribuisci in Azure per iotedge-vm-deploy](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Flive-video-analytics%2Fmaster%2Fref-apps%2Flva-edge-iot-central-gateway%2Fvm_deploy%2FedgeModuleVMDeploy.json)

Usare le informazioni nella tabella seguente per completare il modulo **Distribuzione personalizzata**:

| Campo | valore |
| ----- | ----- |
| Subscription | Selezionare la sottoscrizione di Azure. |
| Resource group | *lva-rg*, ovvero il gruppo di risorse creato nell'esercitazione precedente. |
| Area       | *Stati Uniti orientali* |
| Prefisso etichetta DNS | Scegliere un prefisso DNS univoco per la macchina virtuale. |
| Nome utente amministratore | *AzureUser* |
| Password amministratore | Immettere una password. Prendere nota della password nel file *scratchpad.txt* perché sarà necessaria in seguito. |
| ID ambito | Valore di **ID ambito** annotato nel file *scratchpad.txt* nell'esercitazione precedente quando è stato aggiunto il dispositivo gateway. |
| ID dispositivo | *lva-gateway-001*, ovvero il dispositivo gateway creato nell'esercitazione precedente. |
| Chiave del dispositivo | Chiave primaria del dispositivo annotata nel file *scratchpad.txt* nell'esercitazione precedente quando è stato aggiunto il dispositivo gateway. |
| Iot Central App Host (Host app IoT Central) | Valore di **URL applicazione** annotato nel file *scratchpad.txt* nell'esercitazione precedente. Ad esempio, *traders.azureiotcentral.com*. |
| Iot Central App Api Token (Token API app IoT Central) | Token dell'API dell'operatore annotato nell'esercitazione precedente. |
| Iot Central Device Provisioning Key (Chiave di provisioning di dispositivi IoT Central) | Token di firma di accesso condiviso del gruppo primario annotato nel file *scratchpad.txt* nell'esercitazione precedente. |
| Dimensioni macchina virtuale | *Standard_DS1_v2* |
| Ubuntu OS Version (Versione sistema operativo Ubuntu) | *18.04-LTS* |
| Location | *[resourceGroup().location]* |

Selezionare **Rivedi e crea**. Dopo aver completato la convalida, selezionare **Crea**. Il completamento della distribuzione richiede in genere circa tre minuti. Al termine della distribuzione, passare al gruppo di risorse **lva-rg** nel portale di Azure.

## <a name="ensure-the-iot-edge-runtime-loads-the-modules"></a>Verificare che il runtime IoT Edge carichi i moduli

Nel portale di Azure passare al gruppo di risorse **lva-rg** e selezionare la macchina virtuale. Nella sezione **Supporto e risoluzione dei problemi** selezionare **Console seriale**.

Premere **INVIO** per visualizzare il prompt `login:`. Usare *AzureUser* come nome utente e la password scelta al momento della creazione della macchina virtuale.

Eseguire il comando seguente per controllare la versione del runtime IoT Edge. Al momento della redazione di questo documento, la versione è 1.0.9:

```bash
sudo iotedge --version
```

Elencare i moduli IoT Edge usando il comando:

```bash
sudo iotedge list
```

La distribuzione ha configurato i cinque moduli IoT Edge seguenti per l'esecuzione:

* LvaEdgeGatewayModule
* edgeAgent
* edgeHub
* lvaEdge
* lvaYolov3

La distribuzione ha creato un ambiente IoT Edge personalizzato con i moduli necessari per Analisi video live. La distribuzione ha aggiornato il file **config.yaml** predefinito per garantire che il runtime IoT Edge usi il servizio Device provisioning in IoT per la connessione a IoT Central. La distribuzione ha anche creato un file denominato **state.json** nella cartella **/data/storage** per fornire dati di configurazione aggiuntivi ai moduli. Per altre informazioni, vedere l'esercitazione [Creare un'istanza di IoT Edge per l'analisi video (Intel NUC)](./tutorial-video-analytics-iot-edge-nuc.md).

Per risolvere i problemi relativi al dispositivo IoT Edge, vedere [Risolvere i problemi del dispositivo IoT Edge](https://docs.microsoft.com/azure/iot-edge/troubleshoot)

## <a name="use-the-rtsp-simulator"></a>Usare il simulatore RTSP

Se non si dispone di dispositivi telecamera reali da connettere al dispositivo IoT Edge, è possibile usare due dispositivi telecamera simulati nel modello di applicazione di analisi video. Questa sezione mostra come usare un flusso video simulato nel dispositivo IoT Edge.

Queste istruzioni usano [Live555 Media Server](http://www.live555.com/mediaServer/) come simulatore RTSP in un contenitore Docker.

> [!NOTE]
> I riferimenti a software di terze parti in questo repository sono solo a scopo informativo e per praticità. Microsoft non promuove software di terze parti né fornisce i diritti per tali software. Per altre informazioni, vedere [Live555 Media Server](http://www.live555.com/mediaServer/).

Usare il comando seguente per eseguire l'utilità **rtspvideo** in un contenitore Docker nella macchina virtuale IoT Edge. Il contenitore Docker crea un flusso RTSP in background:

```bash
sudo docker run -d --name live555 --rm -p 554:554 mcr.microsoft.com/lva-utilities/rtspsim-live555:1.2
```

Usare il comando seguente per elencare i contenitori Docker:

```bash
sudo docker ps
```

L'elenco include un contenitore denominato **live555**.

## <a name="next-steps"></a>Passaggi successivi

A questo punto sono stati distribuiti il runtime IoT Edge, i moduli di Analisi video live e il flusso di simulazione di Live555 in una macchina virtuale Linux in esecuzione in Azure.

Per gestire le telecamere, seguire l'esercitazione successiva

> [!div class="nextstepaction"]
> [Monitorare e gestire un'applicazione Analisi video - rilevamento movimento e oggetti](./tutorial-video-analytics-manage.md)
