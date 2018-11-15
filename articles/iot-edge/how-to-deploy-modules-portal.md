---
title: Distribuire i moduli di Azure IoT Edge (portale) | Microsoft Docs
description: Usare il portale di Azure per distribuire i moduli in un dispositivo IoT Edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/06/2018
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: b135832c1f0cb8af23a513d4914d7e32b398be7e
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2018
ms.locfileid: "51564936"
---
# <a name="deploy-azure-iot-edge-modules-from-the-azure-portal"></a>Distribuire i moduli di Azure IoT Edge dal portale di Azure

Dopo aver creato i moduli di IoT Edge in base alla propria logica di business, si distribuiscono i moduli nei dispositivi per consentirne il funzionamento nella rete perimetrale. Se si hanno più moduli che interagiscono per raccogliere ed elaborare dati, è possibile distribuirli contemporaneamente e dichiarare le regole di routing che li connettono. 

Questo articolo illustra come il portale di Azure consente di creare un manifesto della distribuzione ed eseguire il push della distribuzione a un dispositivo IoT Edge. Per informazioni sulla creazione di una distribuzione da assegnare a più dispositivi in base ai relativi tag condivisi, vedere [Distribuire e monitorare i moduli di IoT Edge su larga scala](how-to-deploy-monitor.md).

## <a name="prerequisites"></a>Prerequisiti

* Un [hub IoT](../iot-hub/iot-hub-create-through-portal.md) nella sottoscrizione di Azure. 
* Un [dispositivo IoT Edge](how-to-register-device-portal.md) con il runtime di IoT Edge installato. 

## <a name="select-your-device"></a>Selezionare il dispositivo

1. Accedere al [portale di Azure](https://portal.azure.com) e passare all'hub IoT.
2. Selezionare **IoT Edge** dal menu.
3. Fare clic sull'ID del dispositivo di destinazione nell'elenco dei dispositivi. 
4. Selezionare **Set Modules** (Configura i moduli).

## <a name="configure-a-deployment-manifest"></a>Configurare un manifesto della distribuzione

Un manifesto della distribuzione è un documento JSON contenente la descrizione dei moduli da distribuire, dei flussi di dati esistenti tra i moduli e delle proprietà desiderate dei moduli gemelli. Per altre informazioni sul funzionamento e sulla modalità di creazione dei manifesti della distribuzione, vedere [Informazioni su come usare, configurare e riusare i moduli di IoT Edge](module-composition.md).

Nel portale di Azure è disponibile una procedura guidata che consente di creare il manifesto dell'applicazione anziché creare il documento JSON manualmente. Sono previsti tre passaggi: **Add modules** (Aggiungere moduli), **Specify routes** (Specificare route) e **Review deployment** (Verificare la distribuzione). 

### <a name="add-modules"></a>Aggiungere moduli

1. Nella sezione **Impostazioni registro** della pagina specificare le credenziali per accedere ai registri contenitori privati che contengono le immagini di modulo. 
2. Nella sezione dei **moduli di distribuzione** della pagina fare clic su **Aggiungi**. 
3. Scorrere i tipi di modulo dall'elenco a discesa: 
   * **Modulo IoT Edge**: opzione predefinita.
   * **Azure Stream Analytics Module** (Modulo di Analisi di flusso di Azure): solo i moduli generati da un carico di lavoro di Analisi di flusso di Azure. 
4. Selezionare il **Modulo IoT Edge**.
5. Specificare un nome per il modulo e quindi l'immagine del contenitore. Ad esempio:  
   * **Nome**: tempSensor
   * **URI immagine**: mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0
6. Specificare i campi facoltativi, se necessario. Per altre informazioni sulle opzioni di creazione dei contenitore, i criteri di riavvio e lo stato desiderato, vedere [Proprietà desiderate di EdgeAgent](module-edgeagent-edgehub.md#edgeagent-desired-properties). Per altre informazioni sul modulo gemello, vedere [Definire o aggiornare le proprietà desiderate](module-composition.md#define-or-update-desired-properties).
7. Selezionare **Salva**.
8. Ripetere i passaggi da 2 a 6 per aggiungere altri moduli alla distribuzione. 
9. Scegliere **Avanti** per proseguire con la sezione relativa alle route.

### <a name="specify-routes"></a>Specificare le route

Per impostazione predefinita, la procedura guidata presenta una route denominata **route** e definita come **FROM /\* INTO $upstream**, per indicare che i messaggi generati dai moduli vengono inviati all'hub IoT.  

Aggiungere o aggiornare le route con le informazioni riportate in [Dichiarare le route](module-composition.md#declare-routes) e quindi scegliere **Avanti** per proseguire con la sezione di verifica.

### <a name="review-deployment"></a>Verificare la distribuzione

La sezione relativa alla verifica mostra il manifesto della distribuzione JSON che è stato creato in base alle selezioni nelle due sezioni precedenti. Si noti che sono presenti due moduli dichiarati che non sono stati aggiunti: **$edgeAgent** e **$edgeHub**. Questi due moduli costituiscono il [runtime di IoT Edge](iot-edge-runtime.md) e sono impostazioni predefinite obbligatorie in ogni distribuzione. 

Controllare le informazioni sulla distribuzione e quindi selezionare **Submit** (Invia). 

## <a name="view-modules-on-your-device"></a>Visualizzare i moduli nel dispositivo

Dopo aver distribuito i moduli nel dispositivo, è possibile visualizzarli tutti nella pagina **Dettagli dispositivo** del portale. Questa pagina visualizza il nome di ogni modulo distribuito e informazioni utili come lo stato della distribuzione e il codice di uscita. 

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [distribuire e monitorare i moduli di IoT Edge su larga scala](how-to-deploy-monitor.md)
