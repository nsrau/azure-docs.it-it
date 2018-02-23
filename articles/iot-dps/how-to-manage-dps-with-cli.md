---
title: Come usare l'interfaccia della riga di comando di Azure 2.0 e l'estensione IoT per gestire i servizi Device Provisioning | Microsoft Docs
description: Informazioni su come usare l'interfaccia della riga di comando di Azure 2.0 e l'estensione IoT per gestire i servizi Device Provisioning
services: iot-dps
keywords: 
author: chrissie926
ms.author: menchi
ms.date: 01/17/2018
ms.topic: tutorial
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 674245f1e284e7308474fed0f6c53b350ec1c819
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/19/2018
---
# <a name="how-to-use-azure-cli-20-and-the-iot-extension-to-manage-device-provisioning-services"></a>Come usare l'interfaccia della riga di comando di Azure 2.0 e l'estensione IoT per gestire i servizi Device Provisioning

L'[interfaccia della riga di comando di Azure 2.0](https://docs.microsoft.com/en-us/cli/azure/overview?view=azure-cli-latest) è uno strumento da riga di comando multipiattaforma e open source per la gestione di risorse di Azure come IoT Edge. L'interfaccia della riga di comando di Azure 2.0 è disponibile su Windows, Linux e MacOS. L'interfaccia della riga di comando di Azure 2.0 consente di gestire le risorse dell'hub IoT di Azure, le istanze del servizio Device Provisioning e gli hub collegati predefiniti.

L'estensione IoT arricchisce l'interfaccia della riga di comando di Azure 2.0 con funzionalità quali la gestione dei dispositivi e le funzionalità complete di IoT Edge.

In questa esercitazione viene completata prima di tutto la procedura per la configurazione dell'interfaccia della riga di comando di Azure 2.0 e dell'estensione IoT. Viene quindi illustrato come eseguire i comandi dell'interfaccia della riga di comando per eseguire operazioni di base del servizio Device Provisioning. 

## <a name="installation"></a>Installazione 

### <a name="step-1---install-python"></a>Passaggio 1 - Installare Python

È necessario [Python 2.7x o Python 3.x](https://www.python.org/downloads/).

### <a name="step-2---install-azure-cli-20"></a>Passaggio 2 - Installare l'interfaccia della riga di comando di Azure 2.0

Seguire le [istruzioni di installazione](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest) per configurare l'interfaccia della riga di comando di Azure 2.0 nell'ambiente. La versione l'interfaccia della riga di comando di Azure 2.0 deve essere almeno 2.0.24 o versione successiva. Usare `az –version` per la convalida. Questa versione supporta i comandi dell'estensione az e introduce il framework dei comandi Knack. Un approccio semplice per l'installazione su Windows consiste nel scaricare e installare [MSI](https://aka.ms/InstallAzureCliWindows).

### <a name="step-3---install-iot-extension"></a>Passaggio 3 - Installare l'estensione IoT

Il [file Leggimi dell'estensione IoT](https://github.com/Azure/azure-iot-cli-extension) illustra molti modi per installare l'estensione. L'approccio più semplice consiste nell'eseguire `az extension add --name azure-cli-iot-ext`. Dopo l'installazione, è possibile usare `az extension list` per convalidare le estensioni attualmente installate o `az extension show --name azure-cli-iot-ext` per visualizzare informazioni dettagliate sull'estensione IoT. Per rimuovere l'estensione, è possibile usare `az extension remove --name azure-cli-iot-ext`.


## <a name="basic-device-provisioning-service-operations"></a>Operazioni di base del servizio Device Provisioning
Questo esempio illustra come accedere all'account Azure, creare un gruppo di risorse di Azure (un contenitore che include risorse correlate per una soluzione di Azure), creare un hub IoT, creare un servizio Device Provisioning, elencare i servizi Device Provisioning esistenti e creare un hub IoT collegato con i comandi dell'interfaccia della riga di comando. 

Prima di iniziare, completare la procedura di installazione illustrata in precedenza. Se non si ha un account Azure, è possibile [creare un account gratuito](https://azure.microsoft.com/free/?v=17.39a) subito. 


### <a name="1-log-in-to-the-azure-account"></a>1. Accedere all'account Azure
  
    az login

![Accesso][1]

### <a name="2-create-a-resource-group-iothubblogdemo-in-eastus"></a>2. Creare un gruppo di risorse IoTHubBlogDemo in eastus

    az group create -l eastus -n IoTHubBlogDemo

![Creare un gruppo di risorse][2]


### <a name="3-create-two-device-provisioning-services"></a>3. Creare due servizi Device Provisioning

    az iot dps create --resource-group IoTHubBlogDemo --name demodps

![Creare un servizio Device Provisioning][3]

    az iot dps create --resource-group IoTHubBlogDemo --name demodps2

### <a name="4-list-all-the-existing-device-provisioning-services-under-this-resource-group"></a>4. Elencare tutti i servizi Device Provisioning inclusi in questo gruppo di risorse

    az iot dps list --resource-group IoTHubBlogDemo

![Elencare i servizi Device Provisioning][4]


### <a name="5-create-an-iot-hub-blogdemohub-under-the-newly-created-resource-group"></a>5. Creare un hub IoT denominato blogDemoHub nel gruppo di risorse appena creato

    az iot hub create --name blogDemoHub --resource-group IoTHubBlogDemo

![Creare un hub IoT][5]

### <a name="6-link-one-existing-iot-hub-to-a-device-provisioning-service"></a>6. Collegare un hub IoT esistente a un servizio Device Provisioning

    az iot dps linked-hub create --resource-group IoTHubBlogDemo --dps-name demodps --connection-string <connection string> -l westus

![Collegare l'hub][5]

<!-- Images -->
[1]: ./media/how-to-manage-dps-with-cli/login.jpg
[2]: ./media/how-to-manage-dps-with-cli/create-resource-group.jpg
[3]: ./media/how-to-manage-dps-with-cli/create-dps.jpg
[4]: ./media/how-to-manage-dps-with-cli/list-dps.jpg
[5]: ./media/how-to-manage-dps-with-cli/create-hub.jpg
[6]: ./media/how-to-manage-dps-with-cli/link-hub.jpg


## <a name="next-steps"></a>Passaggi successivi
Questa esercitazione illustra come:

> [!div class="checklist"]
> * Registrare il dispositivo
> * Avviare il dispositivo
> * Verificare che il dispositivo sia registrato

Passare all'esercitazione successiva per informazioni su come eseguire il provisioning di più dispositivi tra hub con bilanciamento del carico. 

> [!div class="nextstepaction"]
> [Eseguire il provisioning dei dispositivi in più hub IoT con bilanciamento del carico](./tutorial-provision-multiple-hubs.md)
