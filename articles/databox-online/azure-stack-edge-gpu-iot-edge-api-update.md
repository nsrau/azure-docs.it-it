---
title: Impatto dell'aggiornamento di gennaio 2021 di Azure Stack Edge | Microsoft Docs
description: Descrive l'impatto della gestione dei ruoli di IoT Edge sui dispositivi Azure Stack Edge dopo l'installazione dell'aggiornamento di gennaio 2021.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 10/26/2020
ms.author: alkohli
ms.openlocfilehash: 4b54f75b7d90e4b3a0a11d2ecdc676bb48eeee99
ms.sourcegitcommit: 2a8a53e5438596f99537f7279619258e9ecb357a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2020
ms.locfileid: "94335683"
---
# <a name="iot-edge-role-management-changes-for-your-azure-stack-edge"></a>Modifiche alla gestione dei ruoli di IoT Edge per Azure Stack Edge

Per la gestione dei ruoli di IoT Edge per il dispositivo Azure Stack Edge, usare la versione più recente di API, SDK e Azure PowerShell il cui rilascio è previsto nel gennaio 2021. Questo articolo descrive nel dettaglio le modifiche necessarie quando si usa la versione più recente dell'API, dell'SDK e dei cmdlet di PowerShell per la gestione dei ruoli di IoT Edge in Azure Stack Edge.

L'aggiornamento di gennaio 2021 è disponibile solo per i dispositivi Azure Stack Edge Pro GPU, Azure Stack Edge Pro R e Azure Stack Edge Mini R e le informazioni fornite in questo articolo sono valide solo per questi dispositivi. 

## <a name="iot-edge-role-management-changes"></a>Modifiche alla gestione dei ruoli di IoT Edge

Dopo l'installazione della versione facoltativa del software del dispositivo di gennaio 2021 nel dispositivo Azure Stack Edge, viene richiesto di usare la versione più recente dell'API, dell'SDK e dei cmdlet di PowerShell per la gestione dei ruoli di IoT Edge.

- Se si usa l'API di gestione dei ruoli con la versione 2019-08-01, eseguire l'aggiornamento alla versione dell'API che verrà rilasciata a gennaio 2021. 
- Se si usa la gestione dei ruoli tramite SDK versione 1.0.0, eseguire l'aggiornamento alla versione che verrà rilasciata a gennaio 2021.
- Se si usa la gestione dei ruoli con i cmdlet di Azure PowerShell (anteprima), ad esempio `Get-AzStackEdgeRole`, `New-AzStackEdgeRole`, `Set-AzStackEdgeRole` o `Remove-AzStackEdgeRole`, attendere i nuovi cmdlet che saranno rilasciati a febbraio 2021.

Le modifiche indicate sopra sono necessarie solo se si applica l'aggiornamento di gennaio 2021. Se si mantiene la versione del software del dispositivo esistente, non c'è alcun impatto sulla gestione dei ruoli di IoT Edge. Si consiglia però di aggiornare il dispositivo per usufruire delle nuove funzionalità e dei miglioramenti della sicurezza. 


## <a name="api-usage"></a>Utilizzo delle API

Se la gestione dei ruoli di IoT Edge veniva eseguita tramite l'API, è necessario usare la nuova versione dell'API (2020-12-01) che verrà pubblicata prossimamente. Se si usa l'API Ruolo corrente e si è installata la nuova versione del software del dispositivo, è necessario passare al ruolo di Kubernetes PUT, GET, DELETE seguito dall'API PUT del componente aggiuntivo IoT.


### <a name="for-put-method"></a>Per il metodo PUT

#### <a name="current-http-request"></a>Richiesta HTTP corrente 

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

#### <a name="upcoming-http-request"></a>Richiesta HTTP futura 

- Le chiamate API per il ruolo Kubernetes vengono effettuate all'URI seguente: 'https://management.azure.com/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/KubernetesRole1?api-version=2020-12-01 '

    Il corpo della richiesta ha un aspetto simile al seguente:

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

- Le chiamate API per il componente aggiuntivo IoT Edge vengono effettuate all'URI seguente: 'https://management.azure.com/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/KubernetesRole1/addons/iotaddon?api-version=2020-12-01 '


    Il corpo della richiesta ha un aspetto simile al seguente:

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


### <a name="for-get-method"></a>Per il metodo GET

#### <a name="current-http-response"></a>Risposta HTTP corrente

- Le chiamate API vengono effettuate all'URI seguente: 'https://management.azure.com/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/IoTRole1?api-version=2019-08-01 '


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

#### <a name="upcoming-http-response"></a>Risposta HTTP futura

- Le chiamate API vengono effettuate all'URI seguente: 'https://management.azure.com/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/KubernetesRole1/addons/iotaddon?api-version=2020-12-01 '
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

### <a name="for-delete-method"></a>Per il metodo DELETE

### <a name="current"></a>Corrente

Le chiamate API vengono effettuate all'URI seguente: 'https://management.azure.com/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/IoTRole1?api-version=2019-08-01 '

### <a name="upcoming"></a>In programma

Le chiamate API vengono effettuate all'URI seguente: 'https://management.azure.com/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/KubernetesRole1/addons/iotaddon?api-version=2020-12-01 '


## <a name="sdk-usage"></a>Uso dell'SDK

Se si usa l'SDK e si è installato l'aggiornamento del dispositivo di gennaio 2021, è necessario cambiare la modalità di configurazione del ruolo di IoT Edge come illustrato di seguito. Occorre quindi scaricare e installare il nuovo pacchetto NuGet per passare al nuovo SDK come illustrato nell'esempio seguente.

### <a name="current-sdk-sample"></a>Esempio dell'SDK corrente

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


### <a name="new-sdk-sample"></a>Esempio del nuovo SDK

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

Se si usano i cmdlet `Get-AzStackEdgeRole`, `New-AzStackEdgeRole`, `Set-AzStackEdgeRole` o `Remove-AzStackEdgeRole`, è necessario attendere la nuova versione il cui rilascio è previsto per febbraio 2021.

## <a name="frequently-asked-questions-faq"></a>Domande frequenti

#### <a name="i-am-using-azure-stack-edge-pro--fpga-does-the-january-2021-update-affect-the-fpga-model"></a>Si usa Azure Stack Edge Pro FPGA. L'aggiornamento di gennaio 2021 interessa il modello FPGA?

No. L'aggiornamento di gennaio 2021 si applica solo ai dispositivi Azure Stack Edge GPU, Azure Stack Edge Pro R e Azure Stack Edge Mini R. Il modello Azure Stack Edge Pro FPGA non è interessato da questo aggiornamento e non richiede alcuna modifica alla gestione dei ruoli di IoT Edge.

#### <a name="after-i-update-my-azure-stack-edge-pro---gpu-to-the-new-device-software-in-january-2021-are-any-of-the-existing-services-affected"></a>L'aggiornamento del dispositivo Azure Stack Edge Pro GPU alla nuova versione del software del dispositivo di gennaio 2021 ha un impatto sui servizi esistenti?

No. I servizi configurati non saranno in alcun modo interessati dall'installazione dell'aggiornamento del dispositivo di gennaio 2021. <!--check w/ Anoob, what existing services you are talking about in this question-->

#### <a name="what-are-the-high-level-changes-to-iot-edge-management-api-sdk-or-cmdlet"></a>Quali sono le modifiche generali apportate all'API di gestione, all'SDK o al cmdlet di IoT Edge?

IoT Edge è un componente aggiuntivo nel ruolo Kubernetes. Questo implica che occorre prima assicurarsi che Kubernetes sia configurato e quindi eseguire la configurazione di IoT Edge.


## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [applicare gli aggiornamenti](azure-stack-edge-gpu-install-update.md).

