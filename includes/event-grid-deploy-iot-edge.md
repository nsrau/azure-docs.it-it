---
title: File di inclusione
description: File di inclusione
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 10/10/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: b453a04a170764a037eed7415eaf71e5a4d37526
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76844590"
---
## <a name="deploy-event-grid-iot-edge-module"></a>Distribuire il modulo IoT Edge griglia eventiDeploy Event Grid IoT Edge module

Esistono diversi modi per distribuire i moduli in un dispositivo IoT Edge e tutti funzionano per Griglia di eventi di Azure in IoT Edge.There are several ways to deploy modules to an IoT Edge device and all of them work for Azure Event Grid on IoT Edge. Questo articolo descrive i passaggi per distribuire Griglia di eventi in Edge Edge dal portale di Azure.This article describes the steps to deploy Event Grid on IoT Edge from the Azure portal.

>[!NOTE]
> In questa esercitazione verrà distribuito il modulo Griglia di eventi senza persistenza. Ciò significa che tutti gli argomenti e le sottoscrizioni creati in questa esercitazione verranno eliminati quando si ridistribuisce il modulo. Per ulteriori informazioni su come configurare la persistenza, vedere i seguenti articoli: [Persist state in Linux](../articles/event-grid/edge/persist-state-linux.md) o Persist state in [Windows](../articles/event-grid/edge/persist-state-windows.md). Per i carichi di lavoro di produzione, è consigliabile installare il modulo Griglia di eventi con persistenza.

>[!IMPORTANT]
> In questa esercitazione il modulo Griglia di eventi verrà distribuito con l'autenticazione client disattivata e consentirà ai sottoscrittori HTTP. Per i carichi di lavoro di produzione, è consigliabile abilitare solo le richieste HTTPS e i sottoscrittori con l'autenticazione client abilitata. Per ulteriori informazioni su come configurare il modulo Griglia di eventi in modo sicuro, vedere [Sicurezza e autenticazione](../articles/event-grid/edge/security-authentication.md).
 
### <a name="select-your-iot-edge-device"></a>Selezionare il dispositivo IoT Edge

1. Accedere al portale di [AzureSign](https://portal.azure.com) in to the Azure portal
1. Passare all'hub IoT.
1. Selezionare **IoT Edge** dal menu nella sezione **Gestione automatica dispositivi.** 
1. Fare clic sull'ID del dispositivo di destinazione dall'elenco dei dispositivi
1. Selezionare **Set Modules** (Configura i moduli). Tenere aperta la pagina. I passaggi verranno continuati nella sezione successiva.

### <a name="configure-a-deployment-manifest"></a>Configurare un manifesto della distribuzione

Un manifesto della distribuzione è un documento JSON contenente la descrizione dei moduli da distribuire, dei flussi di dati esistenti tra i moduli e delle proprietà desiderate dei moduli gemelli. Il portale di Azure include una procedura guidata che illustra come creare un manifesto di distribuzione, anziché compilare manualmente il documento JSON.  Sono previsti tre passaggi: **Add modules** (Aggiungere moduli), **Specify routes** (Specificare route) e **Review deployment** (Verificare la distribuzione).

### <a name="add-modules"></a>Aggiungere moduli

1. Nella sezione **Moduli di distribuzione** selezionare **Aggiungi**
1. Dai tipi di moduli nell'elenco a discesa, selezionare **Modulo edge IoT**
1. Specificare il nome, l'immagine e le opzioni di creazione del contenitore:

[!INCLUDE [event-grid-edge-module-version-update](event-grid-edge-module-version-update.md)]

   * **Nome**: eventgridmodule
   * **URI immagine**:`mcr.microsoft.com/azure-event-grid/iotedge:latest`
   * **Opzioni di creazione del contenitore**:

    ```json
        {
          "Env": [
            "inbound__clientAuth:clientCert__enabled=false",
            "outbound__webhook__httpsOnly=false"
          ],
          "HostConfig": {
            "PortBindings": {
              "4438/tcp": [
                {
                  "HostPort": "4438"
                }
              ]
            }
          }
        }
    ```

 1. Fare clic su **Save**.
 1. Fare clic su **Avanti** per passare alla sezione percorsi

    > [!NOTE]
    > Se si usa una macchina virtuale di Azure come dispositivo perimetrale, aggiungere una regola di porta in ingresso per consentire il traffico in ingresso sulla porta 4438.If you are using an Azure VM as an edge device, add an inbound port rule to allow inbound traffic on the port 4438. Per istruzioni sull'aggiunta della regola, vedere [Come aprire le porte a una macchina virtuale.](../articles/virtual-machines/windows/nsg-quickstart-portal.md)


### <a name="setup-routes"></a>Impostare i percorsi

 Mantenere i percorsi predefiniti e selezionare **Avanti** per continuare con la sezione di revisione

### <a name="review-deployment"></a>Verificare la distribuzione

1. La sezione relativa alla verifica mostra il manifesto della distribuzione JSON che è stato creato in base alle selezioni nelle due sezioni precedenti. Verificare che nell'elenco sono visualizzati i due moduli: **$edgeAgent** e **$edgeHub**. Questi due moduli costituiscono il runtime di IoT Edge e sono impostazioni predefinite obbligatorie in ogni distribuzione.
1. Controllare le informazioni sulla distribuzione e quindi selezionare **Submit** (Invia).

### <a name="verify-your-deployment"></a>Verificare la distribuzione

1. Dopo l'invio della distribuzione, si torna alla pagina IoT Edge dell'hub IoT.
1. Selezionare il **dispositivo IoT Edge** di destinazione della distribuzione per aprirne i dettagli.
1. Nei dettagli del dispositivo verificare che il modulo Griglia di eventi sia elencato come **specificato nella distribuzione** e Segnalato dal **dispositivo**.

Per l'avvio del modulo e per il rilevamento da parte dell'hub IoT può essere richiesto un po' di tempo. Aggiornare la pagina per visualizzare lo stato aggiornato.