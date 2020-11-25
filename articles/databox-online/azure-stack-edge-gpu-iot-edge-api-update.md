---
title: Impatto dell'aggiornamento di gennaio 2021 di Azure Stack Edge | Microsoft Docs
description: Questo articolo descrive l'impatto della gestione dei ruoli di IoT Edge sui dispositivi Azure Stack Edge dopo l'installazione dell'aggiornamento di gennaio 2021.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 10/26/2020
ms.author: alkohli
ms.openlocfilehash: f16f33e9aadcc01427602a1bd81f81cb0710e4dd
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2020
ms.locfileid: "94578740"
---
# <a name="iot-edge-role-management-changes-for-your-azure-stack-edge-device"></a>Modifiche alla gestione dei ruoli di IoT Edge per i dispositivi Azure Stack Edge

Per la gestione dei ruoli di IoT Edge per il dispositivo Azure Stack Edge, usare la versione aggiornata di API, SDK e Azure PowerShell il cui rilascio è previsto nel gennaio 2021. 

Questo articolo descrive in dettaglio le modifiche che è necessario effettuare per l'uso di questa nuova versione.

L'aggiornamento di gennaio 2021 si applica solo ai dispositivi Azure Stack Edge Pro - GPU, Azure Stack Edge Pro R e Azure Stack Edge Mini R. Le informazioni contenute in questo articolo si applicano solo a questi dispositivi.

> [!NOTE]
> Non è obbligatorio eseguire l'aggiornamento alla versione di gennaio 2021. Se si sceglie di continuare a usare la versione corrente, non si verifica alcun effetto sulla gestione dei ruoli di IoT Edge. Tuttavia, per sfruttare le nuove funzionalità e ridurre i rischi per la sicurezza, è consigliabile installare la versione più recente. 

## <a name="iot-edge-role-management-changes"></a>Modifiche alla gestione dei ruoli di IoT Edge

Dopo l'installazione dell'aggiornamento facoltativo di gennaio 2021 nel dispositivo Azure Stack Edge, sarà necessario usare la versione più recente dell'API, dell'SDK e dei cmdlet di PowerShell per la gestione dei ruoli di IoT Edge.

Le modifiche seguenti sono necessarie solo se si applica l'aggiornamento di gennaio 2021:

- Se attualmente si usa la versione&nbsp;2019-08-01 dell'API di gestione dei ruoli, eseguire l'aggiornamento alla versione che verrà rilasciata a gennaio 2021. 
- Se attualmente si usa la versione&nbsp;1.0.0 dell'SDK, eseguire l'aggiornamento alla versione che verrà rilasciata a gennaio 2021.
- Se si usa la gestione dei ruoli con i cmdlet di Azure PowerShell (anteprima), ad esempio `Get-AzStackEdgeRole`, `New-AzStackEdgeRole`, `Set-AzStackEdgeRole` o `Remove-AzStackEdgeRole`, attendere i nuovi cmdlet che saranno rilasciati a febbraio 2021.

## <a name="api-usage"></a>Utilizzo delle API

Se attualmente la gestione dei ruoli di IoT Edge viene eseguita tramite l'API, è necessario usare la nuova versione 2020-12-01 che verrà pubblicata più avanti. Se si usa l'API Ruolo corrente, dopo aver installato la prossima versione del software del dispositivo, è necessario passare al ruolo di Kubernetes PUT, GET, DELETE seguito dall'API PUT del componente aggiuntivo IoT.

### <a name="for-the-put-method"></a>Per il metodo PUT

#### <a name="the-current-http-request"></a>Richiesta HTTP corrente 

- Le chiamate API vengono effettuate a questo URI: 'https://management.azure.com/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/IoTRole1?api-version=2019-08-01 '

- Il corpo della richiesta ha un aspetto simile al seguente:

    ```json
    {
        "kind": "IOT",
        "properties": {
            "hostPlatform": "Linux",
            "ioTDeviceDetails": {
                "deviceId": "iotdevice",
                "ioTHostHub": "iothub.azure-devices.net",
                "ioTHostHubId": "/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/Microsoft.Devices/IotHubs/testrxiothub",
                "authentication": {
                    "symmetricKey": {
                        "connectionString": {
                            "value": "Encrypted<<HostName=iothub.azure-devices.net;DeviceId=iotDevice;SharedAccessKey=2C750FscEas3JmQ8Bnui5yQWZPyml0/UiRt1bQwd8=>>",
                            "encryptionCertThumbprint": "348586569999244",
                            "encryptionAlgorithm": "AES256"
                        }
                    }
                }
            },
            "ioTEdgeDeviceDetails": {
                "deviceId": "iotEdge",
                "ioTHostHub": "iothub.azure-devices.net",
                "ioTHostHubId": "/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/Microsoft.Devices/IotHubs/testrxiothub",
                "authentication": {
                    "symmetricKey": {
                        "connectionString": {
                            "value": "Encrypted<<HostName=iothub.azure-devices.net;DeviceId=iotEdge;SharedAccessKey=2C750FscEas3JmQ8Bnui5yQWZPyml0/UiRt1bQwd8=>>",
                            "encryptionCertThumbprint": "1245475856069999244",
                            "encryptionAlgorithm": "AES256"
                        }
                    }
                }
            },
            "shareMappings": [],
            "roleStatus": "Enabled"
        }
    }
    ```

#### <a name="the-upcoming-http-request"></a>Richiesta HTTP futura 

- Le chiamate API per il ruolo Kubernetes vengono effettuate all'URI seguente: 

  'https://management.azure.com/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/KubernetesRole1?api-version=2020-12-01'

    Il corpo della richiesta sarà simile al seguente:

    ```json
    {
        "kind": "Kubernetes",
        "properties": {
            "hostPlatform": "Linux",
            "kubernetesClusterInfo": {
                "version": "v1.17.3"
            },
            "kubernetesRoleResources": {
                "storage": {
                    "endpoints": []
                },
                "compute": {
                    "vmProfile": "DS1_v2"
                }
            }
        }
    }
    ```

- Le chiamate API per il componente aggiuntivo IoT Edge vengono effettuate all'URI seguente: 

   'https://management.azure.com/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/KubernetesRole1/addons/iotaddon?api-version=2020-12-01'

    Il corpo della richiesta sarà simile al seguente:

    ```json
    {
        "kind": "IoT",
        "properties": {
            "ioTDeviceDetails": {
                "deviceId": "iotdevice",
                "ioTHostHub": "iothub.azure-devices.net",
                "ioTHostHubId": "/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/Microsoft.Devices/IotHubs/testrxiothub",
                "authentication": {
                    "symmetricKey": {
                        "connectionString": {
                            "value": "Encrypted<<HostName=iothub.azure-devices.net;DeviceId=iotDevice;SharedAccessKey=2C750FscEas3JmQ8Bnui5yQWZPyml0/UiRt1bQwd8=>>",
                            "encryptionCertThumbprint": "348586569999244",
                            "encryptionAlgorithm": "AES256"
                        }
                    }
                }
            },
            "ioTEdgeDeviceDetails": {
                "deviceId": "iotEdge",
                "ioTHostHub": "iothub.azure-devices.net",
                "ioTHostHubId": "/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/Microsoft.Devices/IotHubs/testrxiothub",
                "authentication": {
                    "symmetricKey": {
                        "connectionString": {
                            "value": "Encrypted<<HostName=iothub.azure-devices.net;DeviceId=iotEdge;SharedAccessKey=2C750FscEas3JmQ8Bnui5yQWZPyml0/UiRt1bQwd8=>>",
                            "encryptionCertThumbprint": "1245475856069999244",
                            "encryptionAlgorithm": "AES256"
                        }
                    }
                }
            }
        }
    }
    ```


### <a name="for-the-get-method"></a>Per il metodo GET

#### <a name="the-current-http-response"></a>Risposta HTTP corrente

- Le chiamate API vengono effettuate all'URI seguente: 

   'https://management.azure.com/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/IoTRole1?api-version=2019-08-01'

- Il corpo della risposta ha un aspetto simile al seguente:

    ```json
        "kind": "IOT",
        "properties": {
            "hostPlatform": "Linux",
            "ioTDeviceDetails": {
                "deviceId": "iotdevice",
                "ioTHostHub": "iothub.azure-devices.net",
                "ioTHostHubId": "/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/Microsoft.Devices/IotHubs/testrxiothub",
                "authentication": {
                    "symmetricKey": {}
                }
            },
            "ioTEdgeDeviceDetails": {
                "deviceId": "iotEdge",
                "ioTHostHub": "iothub.azure-devices.net",
                "ioTHostHubId": "/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/Microsoft.Devices/IotHubs/testrxiothub",
                "authentication": {
                    "symmetricKey": {}
                }
            },
            "shareMappings": [],
            "roleStatus": "Enabled"
        },
        "id": "/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/IoTRole1",
        "name": "IoTRole1",
        "type": "dataBoxEdgeDevices/roles"
    }    
    ```

#### <a name="the-upcoming-http-response"></a>Risposta HTTP futura

- Le chiamate API vengono effettuate all'URI seguente:  

   'https://management.azure.com/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/KubernetesRole1/addons/iotaddon?api-version=2020-12-01'

- Il corpo della risposta ha un aspetto simile al seguente: 

    ```json
    {
        "kind": "IoT",
        "properties": {
            "provisioningState": "Creating",
            "ioTDeviceDetails": {
                "deviceId": "iotdevice",
                "ioTHostHub": "iothub.azure-devices.net",
                "ioTHostHubId": "/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/Microsoft.Devices/IotHubs/testrxiothub",
                "authentication": {
                    "symmetricKey": {}
                }
            },
            "ioTEdgeDeviceDetails": {
                "deviceId": "iotEdge",
                "ioTHostHub": "iothub.azure-devices.net",
                "ioTHostHubId": "/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/Microsoft.Devices/IotHubs/testrxiothub",
                "authentication": {
                    "symmetricKey": {}
                }
            },
            "version": "0.1.0-beta10"
        },
        "id": "/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/res1/roles/kubernetesRole/addons/iotName",
        "name": " iotName",
        "type": "Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/addon",
    }
    ```

### <a name="for-the-delete-method"></a>Per il metodo DELETE

### <a name="the-current-api-calls"></a>Chiamate API correnti

Le chiamate API vengono effettuate all'URI seguente:  

'https://management.azure.com/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/IoTRole1?api-version=2019-08-01'

### <a name="the-upcoming-api-calls"></a>Chiamate API future

Le chiamate API vengono effettuate all'URI seguente:  

'https://management.azure.com/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/KubernetesRole1/addons/iotaddon?api-version=2020-12-01'

## <a name="sdk-usage"></a>Uso dell'SDK

Se si usa l'SDK ed è stato installato l'aggiornamento di gennaio 2021, è necessario cambiare la modalità di configurazione del ruolo di IoT Edge, come illustrato nell'esempio seguente. Occorre quindi scaricare e installare il nuovo pacchetto NuGet per passare al nuovo SDK, come illustrato qui.

### <a name="the-current-sdk-sample"></a>Esempio dell'SDK corrente

```csharp
var iotRoleStatus = "Enabled";
var iotHostPlatform = "Linux";
var id = $@"/subscriptions/546ec571-2d7f-426f-9cd8-0d695fa7edba/resourceGroups/resourceGroup/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/deviceName/roles/iotrole"; 
var name = "iotrole";
var type = "Microsoft.DataBoxEdge/dataBoxEdgeDevices/role";
var iotRoleName = "iotrole";
var ioTDeviceDetails = new IoTDeviceInfo(...);
var ioTEdgeDeviceDetails = new IoTDeviceInfo(...);
var ioTEdgeAgentInfo = new IoTEdgeAgentInfo(...);
var shareMappings = new List<MountPointMap>(...);

var role = new IoTRole(roleStatus, 
    hostPlatform, 
    shareMappings, 
    ioTDeviceDetails, 
    ioTEdgeDeviceDetails, 
    ioTEdgeAgentInfo, 
    id, 
    name, 
    type);

DataBoxEdgeManagementClient.Roles.CreateOrUpdate(deviceName, iotRoleName, role, resourceGroup);
```


### <a name="the-new-sdk-sample"></a>Esempio del nuovo SDK

```csharp
var k8sRoleStatus = "Enabled";
var k8sHostPlatform = "Linux";
var k8sId = $@"/subscriptions/546ec571-2d7f-426f-9cd8-0d695fa7edba/resourceGroups/resourceGroup/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/deviceName/roles/KubernetesRole"; 
var k8sRoleName = "KubernetesRole";
var k8sClusterVersion = "v1.17.3"; //Final values will be updated here around January 2021
var k8sVmProfile = "DS1_v2"; //Final values will be updated here around January 2021
var type = "Microsoft.DataBoxEdge/dataBoxEdgeDevices/role";
var k8sRole = new KubernetesRole(
    roleStatus,
    hostPlatform,
    shareMappings,
    k8sClusterVersion,
    k8sVmProfile,
    k8sId,
    k8sRoleName,
    type
);
DataBoxEdgeManagementClient.Roles.CreateOrUpdate(deviceName, k8sRoleName, k8sRole, resourceGroup); //Final usage will be updated here around January 2021

var ioTId = $@"/subscriptions/546ec571-2d7f-426f-9cd8-0d695fa7edba/resourceGroups/resourceGroup/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/deviceName/roles/KubernetesRole/addons/iotaddon";
var ioTAddonName = "iotaddon";
var ioTAddonType = "Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/addons";
var addon = new IoTAddon(
    ioTDeviceDetails, 
    ioTEdgeDeviceDetails, 
    ioTEdgeAgentInfo, 
    ioTId, 
    ioTAddonName, 
    ioTAddonType);
DataBoxEdgeManagementClient.AddOns.CreateOrUpdate(deviceName, k8sRoleName, addonName, addon, resourceGroup); //Final usage will be updated here around January 2021
```

## <a name="cmdlet-usage"></a>Utilizzo dei cmdlet

Se attualmente si usa il cmdlet `Get-AzStackEdgeRole`, `New-AzStackEdgeRole`, `Set-AzStackEdgeRole` o `Remove-AzStackEdgeRole`, sarà necessario aspettare la nuova versione, pianificata per il rilascio a febbraio 2021.

## <a name="frequently-asked-questions"></a>Domande frequenti

**Si usa Azure Stack Edge Pro - FPGA. L'aggiornamento di gennaio 2021 influisce sul modello FPGA?**

No. L'aggiornamento di gennaio 2021 si applica solo ai dispositivi Azure Stack Edge Pro - GPU, Azure Stack Edge Pro R e Azure Stack Edge Mini R. Il modello Azure Stack Edge Pro - FPGA non è interessato da questo aggiornamento e non richiede alcuna modifica alla gestione dei ruoli di IoT Edge.

**L'aggiornamento del dispositivo Azure Stack Edge Pro - GPU alla nuova versione del software del dispositivo di gennaio 2021 ha effetto sui servizi esistenti?**

No. I servizi configurati non saranno interessati dall'installazione dell'aggiornamento del dispositivo di gennaio 2021.

**Quali sono le modifiche generali apportate all'API, all'SDK o al cmdlet di gestione di IoT Edge?**

IoT Edge è un componente aggiuntivo nel ruolo Kubernetes, il che implica che sarà prima di tutto necessario assicurarsi che Kubernetes sia configurato e quindi procedere alla configurazione di IoT Edge.


## <a name="next-steps"></a>Passaggi successivi

- [Informazioni su come applicare gli aggiornamenti](azure-stack-edge-gpu-install-update.md)

