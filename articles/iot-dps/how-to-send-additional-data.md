---
title: Come trasferire dati aggiuntivi tra il dispositivo e il servizio Device provisioning di Azure
description: Questo documento descrive come trasferire dati aggiuntivi tra il dispositivo e il servizio Device provisioning (DPS)
author: menchi
ms.author: menchi
ms.date: 10/29/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: e9482f7069616d61efb98f66590ce33cfe3cf350
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74974854"
---
# <a name="how-to-transfer-additional-data-between-device-and-dps"></a>Come trasferire dati aggiuntivi tra il dispositivo e il DPS
Talvolta il servizio Device Provisioning necessita di una maggiore quantità di dati dai dispositivi per effettuarne il provisioning nell'hub IoT corretto e tali dati devono essere forniti dal dispositivo. Viceversa, DPS può restituire i dati al dispositivo per semplificare le logiche lato client. 

## <a name="when-to-use-it"></a>Quando utilizzarli
Questa funzionalità può essere usata come miglioramento per l' [allocazione personalizzata](https://docs.microsoft.com/azure/iot-dps/how-to-use-custom-allocation-policies). Ad esempio, si desidera allocare i dispositivi in base al modello di dispositivo senza intervento umano. In questo caso, si utilizzerà l' [allocazione personalizzata](https://docs.microsoft.com/azure/iot-dps/how-to-use-custom-allocation-policies). È possibile configurare il dispositivo in modo che segnali le informazioni del modello come parte della [chiamata del dispositivo Register](https://docs.microsoft.com/rest/api/iot-dps/runtimeregistration/registerdevice). DPS passerà le informazioni del dispositivo al webhook di allocazione personalizzato. E la funzione può decidere a quale Hub di tutto il dispositivo andrà a ricevere informazioni sul modello di dispositivo. Allo stesso modo, se il webhook vuole restituire alcuni dati al dispositivo, li passerà di nuovo come stringa nella risposta del webhook.  

## <a name="device-sends-data-to-dps"></a>Il dispositivo invia dati a DPS
Quando il dispositivo invia una [chiamata al dispositivo di registrazione](https://docs.microsoft.com/rest/api/iot-dps/runtimeregistration/registerdevice) a DPS, la chiamata al registro può essere migliorata per prendere altri campi nel corpo. Il corpo ha un aspetto simile al seguente: 
   ```
   { 
       “registrationId”: “mydevice”, 
       “tpm”:                
       { 
           “endorsementKey”: “stuff”, 
           “storageRootKey”: “things” 
       }, 
       “interfaces”: “TODO: get how interfaces are reported by devices from PnP folks.”, 
       “data”: “your additional data goes here. It can be nested JSON.” 
    } 
   ```

## <a name="dps-returns-data-to-the-device"></a>DPS restituisce i dati al dispositivo
Se il webhook dei criteri di allocazione personalizzati vuole restituire alcuni dati al dispositivo, i dati vengono passati come stringa nella risposta del webhook. La modifica si trova nella sezione returnData riportata di seguito. 
   ```
   { 
       "iotHubHostName": "sample-iot-hub-1.azure-devices.net", 
       "initialTwin": { 
           "tags": { 
               "tag1": true 
               }, 
               "properties": { 
                   "desired": { 
                       "tag2": true 
                    } 
                } 
            }, 
        "returnData": "whatever is returned by the webhook" 
    } 
   ```

## <a name="sdk-support"></a>Supporto SDK
Questa funzionalità è disponibile negli SDK del C# [client](https://docs.microsoft.com/azure/iot-dps/)C,, Java e node. js.  

## <a name="next-steps"></a>Passaggi successivi
* Sviluppare usando [Azure IoT SDK]( https://github.com/Azure/azure-iot-sdks) per hub IoT di Azure e il servizio Device Provisioning in hub IoT di Azure
