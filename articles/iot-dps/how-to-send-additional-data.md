---
title: Come trasferire un payload tra il dispositivo e il servizio di provisioning dei dispositivi di AzureHow to transfer a payload between device and Azure Device Provisioning Service
description: Questo documento descrive come trasferire un payload tra dispositivo e Device Provisioning Service (DPS)This document describes how to transfer a payload between device and Device Provisioning Service (DPS)
author: menchi
ms.author: menchi
ms.date: 02/11/2020
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: de6bb3fe0879a69467283e93a04a355876a02cba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246688"
---
# <a name="how-to-transfer-a-payload-between-device-and-dps"></a>Come trasferire un payload tra dispositivo e DPS
A volte DPS richiede più dati dai dispositivi per eseguirne correttamente il provisioning nell'hub IoT corretto e tali dati devono essere forniti dal dispositivo. Viceversa, DPS può restituire dati al dispositivo per facilitare le logiche lato client. 

## <a name="when-to-use-it"></a>Quando utilizzarli
Questa funzionalità può essere utilizzata come miglioramento per [l'allocazione personalizzata.](https://docs.microsoft.com/azure/iot-dps/how-to-use-custom-allocation-policies) Ad esempio, si desidera allocare i dispositivi in base al modello di dispositivo senza l'intervento umano. In questo caso, si utilizzerà [l'allocazione personalizzata](https://docs.microsoft.com/azure/iot-dps/how-to-use-custom-allocation-policies). È possibile configurare il dispositivo per segnalare le informazioni sul modello come parte della chiamata al dispositivo di [registrazione.](https://docs.microsoft.com/rest/api/iot-dps/runtimeregistration/registerdevice) DPS passerà il payload del dispositivo al webhook di allocazione personalizzata. E la funzione può decidere a quale hub IoT andrà a cui verrà inviato questo dispositivo quando riceve le informazioni sul modello di dispositivo. Analogamente, se il webhook desidera restituire alcuni dati al dispositivo, passerà nuovamente i dati come stringa nella risposta webhook.  

## <a name="device-sends-data-payload-to-dps"></a>Il dispositivo invia il payload dei dati a DPS
Quando il dispositivo invia una chiamata al dispositivo di [registrazione](https://docs.microsoft.com/rest/api/iot-dps/runtimeregistration/registerdevice) a DPS, la chiamata di registro può essere migliorata per prendere altri campi nel corpo. Il corpo è simile al seguente: 
   ```
   { 
       “registrationId”: “mydevice”, 
       “tpm”:                
       { 
           “endorsementKey”: “stuff”, 
           “storageRootKey”: “things” 
       }, 
       “payload”: “your additional data goes here. It can be nested JSON.” 
    } 
   ```

## <a name="dps-returns-data-to-the-device"></a>DPS restituisce i dati al dispositivo
Se il webhook dei criteri di allocazione personalizzati desidera restituire alcuni dati al dispositivo, passerà nuovamente i dati come stringa nella risposta webhook. La modifica è nella sezione payload riportata di seguito. 
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
        "payload": "whatever is returned by the webhook" 
    } 
   ```

## <a name="sdk-support"></a>Supporto SDK
Questa funzionalità è disponibile negli SDK dei [client](https://docs.microsoft.com/azure/iot-dps/)C, C, JAVA e Node.js .  

## <a name="next-steps"></a>Passaggi successivi
* Sviluppare usando [Azure IoT SDK]( https://github.com/Azure/azure-iot-sdks) per hub IoT di Azure e il servizio Device Provisioning in hub IoT di Azure
