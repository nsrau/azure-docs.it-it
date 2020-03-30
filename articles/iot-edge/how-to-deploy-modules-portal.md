---
title: Distribuire moduli dal portale di Azure - Azure IoT EdgeDeploy modules from Azure portal - Azure IoT Edge
description: Usare l'hub IoT nel portale di Azure per eseguire il push di un modulo IoT Edge dall'hub IoT al dispositivo IoT Edge, come configurato da un manifesto di distribuzione.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 12/30/2019
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 5c44561895bc1905328ec0eb357bee1c68a8eb55
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271447"
---
# <a name="deploy-azure-iot-edge-modules-from-the-azure-portal"></a>Distribuire i moduli di Azure IoT Edge dal portale di Azure

Dopo aver creato i moduli di IoT Edge in base alla propria logica di business, si distribuiscono i moduli nei dispositivi per consentirne l'uso a livello perimetrale. Se si hanno più moduli che interagiscono per raccogliere ed elaborare dati, è possibile distribuirli contemporaneamente e dichiarare le regole di routing che li connettono.

Questo articolo illustra come il portale di Azure consente di creare un manifesto della distribuzione ed eseguire il push della distribuzione a un dispositivo IoT Edge. Per informazioni sulla creazione di una distribuzione destinata a più dispositivi in base ai relativi tag condivisi, vedere [Distribuire e monitorare i moduli IoT Edge su larga scala.](how-to-deploy-monitor.md)

## <a name="prerequisites"></a>Prerequisiti

* Un hub IoT nella sottoscrizione di Azure.An [IoT hub](../iot-hub/iot-hub-create-through-portal.md) in your Azure subscription.
* Un [dispositivo IoT Edge](how-to-register-device.md#register-in-the-azure-portal) con il runtime di IoT Edge installato.

## <a name="configure-a-deployment-manifest"></a>Configurare un manifesto della distribuzione

Un manifesto della distribuzione è un documento JSON contenente la descrizione dei moduli da distribuire, dei flussi di dati esistenti tra i moduli e delle proprietà desiderate dei moduli gemelli. Per altre informazioni sul funzionamento e sulla modalità di creazione dei manifesti della distribuzione, vedere [Informazioni su come usare, configurare e riusare i moduli IoT Edge](module-composition.md).

Nel portale di Azure è disponibile una procedura guidata che consente di creare il manifesto dell'applicazione anziché creare il documento JSON manualmente. Sono previsti tre passaggi: **Add modules** (Aggiungere moduli), **Specify routes** (Specificare route) e **Review deployment** (Verificare la distribuzione).

### <a name="select-device-and-add-modules"></a>Selezionare il dispositivo e aggiungere moduli

1. Accedere al [portale di Azure](https://portal.azure.com) e passare all'hub IoT.
1. Nel riquadro sinistro selezionare **Edge IoT** dal menu.
1. Fare clic sull'ID del dispositivo di destinazione nell'elenco dei dispositivi.
1. Sulla barra superiore selezionare **Imposta moduli**.
1. Nella sezione **Impostazioni del Registro di** sistema contenitori della pagina specificare le credenziali per accedere a tutti i registri dei contenitori privati che contengono le immagini del modulo.
1. Nella sezione **Moduli perimetrali IoT** della pagina selezionare **Aggiungi**.
1. Esaminare i tipi di moduli dal menu a discesa:

   * **Modulo Edge IoT:** specificare il nome del modulo e l'URI dell'immagine del contenitore. Ad esempio, l'URI dell'immagine per `mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0`il modulo SimulatedTemperatureSensor di esempio è . Se l'immagine del modulo è archiviata in un registro contenitori privato, aggiungere le credenziali in questa pagina per accedere all'immagine.
   * **Modulo Marketplace** - Moduli ospitati in Azure Marketplace. Alcuni moduli del marketplace richiedono una configurazione aggiuntiva, pertanto esaminare i dettagli del modulo nell'elenco Moduli edge IoT di Azure Marketplace.Some marketplace modules require additional configuration, so review the modules details in the [Azure Marketplace IoT Edge Modules](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) list.
   * **Modulo di Analisi di flusso** di Azure: moduli generati da un carico di lavoro di Analisi di flusso di Azure.Azure Stream Analytics Module - Modules generated from an Azure Stream Analytics workload.

1. Dopo aver aggiunto un modulo, selezionare il nome del modulo dall'elenco per aprire le impostazioni del modulo. Specificare i campi facoltativi, se necessario. Per altre informazioni sulle opzioni di creazione dei contenitore, i criteri di riavvio e lo stato desiderato, vedere [Proprietà desiderate di EdgeAgent](module-edgeagent-edgehub.md#edgeagent-desired-properties). Per altre informazioni sul modulo gemello, vedere [Definire o aggiornare le proprietà desiderate](module-composition.md#define-or-update-desired-properties).
1. Se necessario, ripetere i passaggi da 5 a 8 per aggiungere altri moduli alla distribuzione.
1. Selezionare **Avanti: Percorsi** per continuare alla sezione percorsi.

### <a name="specify-routes"></a>Specificare le route

Nella scheda **Route** è possibile definire come vengono passati i messaggi tra i moduli e l'hub IoT. I messaggi vengono costruiti mediante coppie nome/valore. Per impostazione predefinita, una route viene definita **route** e definita come **FROM\* /messages/ INTO $upstream**, il che significa che tutti i messaggi restituiti da qualsiasi modulo vengono inviati all'hub IoT.  

Aggiungere o aggiornare le route con le informazioni di [Dichiara route](module-composition.md#declare-routes), quindi selezionare **Avanti: Revisione : crea** per continuare con il passaggio successivo della procedura guidata.

### <a name="review-deployment"></a>Verificare la distribuzione

La sezione relativa alla verifica mostra il manifesto della distribuzione JSON che è stato creato in base alle selezioni nelle due sezioni precedenti. Si noti che sono presenti due moduli dichiarati che non sono stati aggiunti: **$edgeAgent** e **$edgeHub**. Questi due moduli costituiscono il [runtime di IoT Edge](iot-edge-runtime.md) e sono impostazioni predefinite obbligatorie in ogni distribuzione.

Esaminare le informazioni di distribuzione, quindi selezionare **Crea**.

## <a name="view-modules-on-your-device"></a>Visualizzare i moduli nel dispositivo

Dopo aver distribuito i moduli nel dispositivo, puoi visualizzarli tutti nella pagina dei dettagli del dispositivo dell'hub IoT. Questa pagina visualizza il nome di ogni modulo distribuito e informazioni utili come lo stato della distribuzione e il codice di uscita.

## <a name="deploy-modules-from-azure-marketplace"></a>Distribuire i moduli da Azure Marketplace

[Azure Marketplace](https://azuremarketplace.microsoft.com/) è un marketplace di applicazioni e servizi online in cui è possibile esplorare un'ampia gamma di applicazioni e soluzioni aziendali certificate e ottimizzate per l'esecuzione in Azure, inclusi i [moduli IoT Edge.](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules)

È possibile distribuire un modulo IoT Edge da Azure Marketplace e dall'hub IoT.You can deploy an IoT Edge module from the Azure Marketplace and from your IoT Hub.

### <a name="deploy-from-azure-marketplace"></a>Distribuire da Azure Marketplace

È possibile utilizzare i moduli IoT Edge nel marketplace e, quando si trova quello desiderato, è possibile distribuirlo selezionando **Crea** o **Scarica ora**. Procedere con i passaggi della procedura guidata di distribuzione che possono variare a seconda del modulo IoT Edge selezionato:

1. Accettare le condizioni per l'utilizzo e l'informativa sulla privacy del provider selezionando **Continua**. Potrebbe essere necessario fornire le informazioni di contatto.
1. Scegliere la sottoscrizione e l'hub IoT a cui è collegato il dispositivo di destinazione.
1. Scegliere **Distribuisci in un dispositivo**.
1. Immettere il nome del dispositivo o selezionare **Trova dispositivo** per cercare tra i dispositivi registrati nell'hub.
1. Selezionare **Crea** per continuare il processo standard di configurazione di un manifesto della distribuzione, inclusa l'aggiunta di altri moduli, se lo si desidera. I dettagli per il nuovo modulo, ad esempio URI dell'immagine, opzioni di creazione e proprietà desiderate, sono predefiniti, ma possono essere modificati.

Verificare che il modulo sia distribuito nell'hub IoT nel portale di Azure.Verify that the module is deployed in your IoT Hub in the Azure portal. Selezionare il dispositivo, selezionare **Imposta moduli** e il modulo dovrebbe essere elencato nella sezione Moduli **Edge IoT.**

### <a name="deploy-from-azure-iot-hub"></a>Eseguire la distribuzione dall'hub IoT di AzureDeploy from Azure IoT Hub

È possibile distribuire rapidamente un modulo da Azure Marketplace nel dispositivo nell'hub IoT nel portale di Azure.You can quickly deploy a module from the Azure Marketplace to your device in your IoT Hub in the Azure portal.

1. Nel portale di Azure passare all'hub IoT.
1. Nel riquadro sinistro, in **Gestione automatica dispositivi**, selezionare **IoT Edge**.
1. Selezionare il dispositivo IoT Edge che deve ricevere la distribuzione.
1. Sulla barra superiore selezionare **Imposta moduli**.
1. Nella sezione **Moduli Edge IoT** fare clic su **Aggiungi**e selezionare **Modulo Marketplace** dal menu a discesa.

![Aggiungi modulo nell'hub IoT](./media/how-to-deploy-modules-portal/iothub-add-module.png)

Scegliere un modulo dalla pagina **Mercato modulo Edge IoT.** Il modulo selezionato viene configurato automaticamente per la sottoscrizione, il gruppo di risorse e il dispositivo. Viene quindi visualizzato nell'elenco dei moduli IoT Edge. Alcuni moduli potrebbero richiedere una configurazione aggiuntiva.

> [!TIP]
> Le informazioni sui moduli IoT Edge dall'hub IoT di Azure sono limitate. Per altre informazioni sui [moduli IoT Edge](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) in Azure Marketplace.

Selezionare **Avanti: Route** e continuare la distribuzione come descritto in [Specificare le route](#specify-routes) e [rivedere la distribuzione](#review-deployment) più indietro in questo articolo.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [distribuire e monitorare i moduli di IoT Edge su larga scala](how-to-deploy-monitor.md)
