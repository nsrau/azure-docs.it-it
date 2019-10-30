---
title: file di inclusione
description: file di inclusione
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 10/10/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 8656bbb070e2b05a06ea22dd1634a40182b440cb
ms.sourcegitcommit: b45ee7acf4f26ef2c09300ff2dba2eaa90e09bc7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73098687"
---
## <a name="deploy-event-grid-iot-edge-module"></a>Distribuisci modulo di IoT Edge griglia di eventi

Esistono diversi modi per distribuire i moduli in un dispositivo IoT Edge e tutti funzionano per griglia di eventi di Azure su IoT Edge. Questo articolo descrive i passaggi per la distribuzione di griglia di eventi in IoT Edge dalla portale di Azure.

>[!NOTE]
> In questa esercitazione verrà distribuito il modulo di griglia di eventi senza persistenza. Ciò significa che tutti gli argomenti e le sottoscrizioni create in questa esercitazione verranno eliminati quando si ridistribuisce il modulo. Per altre informazioni su come configurare la persistenza, vedere gli articoli seguenti: [persistenza dello stato in Linux](../articles/event-grid/edge/persist-state-linux.md) o [persistenza in Windows](../articles/event-grid/edge/persist-state-windows.md). Per i carichi di lavoro di produzione, è consigliabile installare il modulo di griglia di eventi con la persistenza.

>[!IMPORTANT]
> In questa esercitazione il modulo di griglia di eventi verrà distribuito con l'autenticazione client disattivata e consentirà sottoscrittori HTTP. Per i carichi di lavoro di produzione, è consigliabile abilitare solo le richieste HTTPS e i sottoscrittori con autenticazione client abilitata. Per altre informazioni su come configurare il modulo di griglia di eventi in modo sicuro, vedere [sicurezza e autenticazione](../articles/event-grid/edge/security-authentication.md).
 
### <a name="select-your-iot-edge-device"></a>Selezionare il dispositivo IoT Edge

1. Accedere al [portale di Azure](https://portal.azure.com)
1. Passare all'hub IoT.
1. Selezionare **IOT Edge** dal menu nella sezione **gestione automatica dei dispositivi** . 
1. Fare clic sull'ID del dispositivo di destinazione dall'elenco di dispositivi
1. Selezionare **Set Modules** (Configura i moduli). Mantieni aperta la pagina. Si procederà con la procedura descritta nella sezione successiva.

### <a name="configure-a-deployment-manifest"></a>Configurare un manifesto della distribuzione

Un manifesto della distribuzione è un documento JSON contenente la descrizione dei moduli da distribuire, dei flussi di dati esistenti tra i moduli e delle proprietà desiderate dei moduli gemelli. Il portale di Azure dispone di una procedura guidata che illustra la creazione di un manifesto di distribuzione, anziché compilare manualmente il documento JSON.  Sono previsti tre passaggi: **Add modules** (Aggiungere moduli), **Specify routes** (Specificare route) e **Review deployment** (Verificare la distribuzione).

### <a name="add-modules"></a>Aggiungere moduli

1. Nella sezione **moduli di distribuzione** selezionare **Aggiungi**
1. Dai tipi di moduli nell'elenco a discesa selezionare **IOT Edge modulo**
1. Specificare il nome, l'immagine e le opzioni di creazione del contenitore del contenitore:

   * **Nome**: eventgridmodule
   * **URI immagine**: `mcr.microsoft.com/azure-event-grid/iotedge:latest`
   * **Opzioni di creazione del contenitore**:

    ```json
        {
          "Env": [
            "inbound:clientAuth:clientCert:enabled=false",
            "outbound:webhook:httpsOnly=false"
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

 1. Fare clic su **Salva**.
 1. Fare clic su **Avanti** per passare alla sezione Route

    > [!NOTE]
    > Se si usa una macchina virtuale di Azure come dispositivo perimetrale, aggiungere una regola per la porta in ingresso per consentire il traffico in ingresso sulla porta 4438. Per istruzioni sull'aggiunta della regola, vedere [How to open ports to a VM](../articles/virtual-machines/windows/nsg-quickstart-portal.md).


### <a name="setup-routes"></a>Route di installazione

 Mantenere le route predefinite e fare clic su **Avanti** per passare alla sezione Revisione

### <a name="review-deployment"></a>Verificare la distribuzione

1. La sezione relativa alla verifica mostra il manifesto della distribuzione JSON che è stato creato in base alle selezioni nelle due sezioni precedenti. Verificare che nell'elenco siano visualizzati i due moduli: **$edgeAgent** e **$edgeHub**. Questi due moduli costituiscono il runtime IoT Edge e sono necessari i valori predefiniti in ogni distribuzione.
1. Controllare le informazioni sulla distribuzione e quindi selezionare **Submit** (Invia).

### <a name="verify-your-deployment"></a>Verificare la distribuzione

1. Dopo aver inviato la distribuzione, tornare alla pagina IoT Edge dell'hub Internet.
1. Selezionare il **dispositivo IOT Edge** di destinazione della distribuzione per aprirne i dettagli.
1. Nei dettagli del dispositivo, verificare che il modulo griglia di eventi sia elencato come **specificato nella distribuzione** e **segnalato dal dispositivo**.

Per l'avvio del modulo e per il rilevamento da parte dell'hub IoT può essere richiesto un po' di tempo. Aggiornare la pagina per visualizzare lo stato aggiornato.