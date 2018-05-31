---
title: File di inclusione
description: File di inclusione
services: iot-suite
author: dominicbetts
ms.service: iot-suite
ms.topic: include
ms.date: 04/24/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 500e335d0b2eddc56cdfb9828236bc4676d9b6aa
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2018
ms.locfileid: "34371168"
---
> [!div class="op_single_selector"]
> * [C su Windows](../articles/iot-accelerators/iot-accelerators-connecting-devices.md)
> * [C su Linux](../articles/iot-accelerators/iot-accelerators-connecting-devices-linux.md)
> * [Node.js (generico)](../articles/iot-accelerators/iot-accelerators-connecting-devices-node.md)
> * [Node.js su Raspberry Pi](../articles/iot-accelerators/iot-accelerators-connecting-pi-node.md)
> * [C su Raspberry Pi](../articles/iot-accelerators/iot-accelerators-connecting-pi-c.md)

In questa esercitazione viene implementato un dispositivo **Chiller** che invia i dati di telemetria seguenti all'acceleratore di soluzioni di [monitoraggio remoto](../articles/iot-accelerators/iot-accelerators-what-are-solution-accelerators.md):

* Temperatura
* Pressione
* Umidità

Per semplicità, il codice genera valori di telemetria di esempio per il **chiller**. È possibile estendere l'esempio connettendo sensori reali al dispositivo e inviando dati di telemetria reali.

Il dispositivo di esempio inoltre:

* Invia i metadati alla soluzione per descrivere le proprie funzionalità.
* Risponde alle azioni attivate dalla pagina **Dispositivi** della soluzione.
* Risponde alle modifiche di configurazione inviate dalla pagina **Dispositivi** della soluzione.

Per completare l'esercitazione, è necessario un account Azure attivo. Se non si dispone di un account Azure, è possibile creare un account di valutazione gratuito in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](http://azure.microsoft.com/pricing/free-trial/).

## <a name="before-you-start"></a>Prima di iniziare

Prima di scrivere il codice per il dispositivo, distribuire l'acceleratore di soluzioni di monitoraggio remoto e aggiungere un nuovo dispositivo fisico alla soluzione.

### <a name="deploy-your-remote-monitoring-solution-accelerator"></a>Distribuire l'acceleratore di soluzioni di monitoraggio remoto

Il dispositivo **Chiller** creato in questa esercitazione invia dati a un'istanza dell'acceleratore di soluzioni di [monitoraggio remoto](../articles/iot-suite/iot-suite-remote-monitoring-explore.md). Se nel proprio account Azure non è già stato effettuato il provisioning dell'acceleratore di soluzioni di monitoraggio remoto, vedere [Distribuire l'acceleratore di soluzioni di monitoraggio remoto](../articles/iot-accelerators/iot-accelerators-remote-monitoring-deploy.md)

Al termine del processo di distribuzione della soluzione di monitoraggio remoto, fare clic su **Avvia** per aprire il dashboard della soluzione nel browser.

![Dashboard della soluzione](media/iot-suite-selector-connecting/dashboard.png)

### <a name="add-your-device-to-the-remote-monitoring-solution"></a>Aggiungere il dispositivo alla soluzione di monitoraggio remoto

> [!NOTE]
> Se è già stato aggiunto un dispositivo nella soluzione, è possibile saltare questo passaggio. Per il passaggio successivo sarà però necessaria la stringa di connessione del dispositivo. È possibile recuperare la stringa di connessione di un dispositivo dal [portale di Azure](https://portal.azure.com) oppure usando lo strumento [az iot](https://docs.microsoft.com/cli/azure/iot?view=azure-cli-latest) dell'interfaccia della riga di comando.

Per connettere un dispositivo all'acceleratore di soluzioni, è necessario che identifichi se stesso nell'hub IoT mediante delle credenziali valide. Quando si aggiunge il dispositivo alla soluzione, si ha la possibilità di salvare la stringa di connessione del dispositivo che contiene queste credenziali. Le istruzioni per includere la stringa di connessione del dispositivo nell'applicazione client sono illustrate più avanti in questa esercitazione.

Per aggiungere un dispositivo alla soluzione per il monitoraggio remoto, completare i passaggi seguenti nella pagina **Dispositivi** della soluzione:

1. Scegliere **Nuovo dispositivo** e quindi come **Tipo di dispositivo** scegliere **Fisico**:

    ![Aggiungere un dispositivo fisico](media/iot-suite-selector-connecting/devicesprovision.png)

1. Immettere **Fisico-chiller** come ID del dispositivo. Scegliere le opzioni **Chiave simmetrica** e **Genera chiavi automaticamente**:

    ![Scegliere le opzioni per il dispositivo](media/iot-suite-selector-connecting/devicesoptions.png)

1. Scegliere **Applica**. Prendere quindi nota dei valori di **ID dispositivo**, **Chiave primaria**, e **Connection string primary key** (Chiave primaria della stringa di connessione):

    ![Recuperare le credenziali](media/iot-suite-selector-connecting/credentials.png)

A questo punto è stato aggiunto un dispositivo fisico all'acceleratore di soluzioni di monitoraggio remoto e ne è stata annotata la stringa di connessione. Nelle sezioni seguenti si implementerà l'applicazione client che usa la stringa di connessione del dispositivo per connettersi alla soluzione.

L'applicazione client implementa il modello di dispositivo **Chiller** predefinito. Un modello di dispositivo per l'acceleratore di soluzioni specifica le informazioni seguenti per il dispositivo:

* Le proprietà che il dispositivo segnala alla soluzione. Ad esempio, un dispositivo **Chiller** segnala informazioni su firmware e posizione.
* Tipi di dati di telemetria che il dispositivo invia alla soluzione. Ad esempio, un dispositivo **Chiller** invia valori di temperatura, umidità e pressione.
* I metodi che possono essere pianificati nella soluzione per l'esecuzione nel dispositivo. Un dispositivo **Chiller**, ad esempio, deve implementare i metodi **Reboot**, **FirmwareUpdate**, **EmergencyValveRelease** e **IncreasePressure**.