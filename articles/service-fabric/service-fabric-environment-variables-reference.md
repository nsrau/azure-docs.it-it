---
title: Variabili di ambiente di Azure Service Fabric | Microsoft Docs
description: Documentazione di riferimento sulle variabili di ambiente di Service Fabric
documentationcenter: .net
author: mikkelhegn
manager: msfussell
editor: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/07/2017
ms.author: mikhegn
ms.openlocfilehash: a9faefb43b9d5da81dddef8f326a3867b32842f7
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
# <a name="service-fabric-environment-variables"></a>Variabili di ambiente di Service Fabric

Service Fabric dispone di variabili di ambiente predefinite, impostate per ciascuna istanza di servizio. La tabella seguente include l'elenco completo delle variabili di ambiente:

| Variabile di ambiente                         | DESCRIZIONE                                                            | Esempio                                                              |
|----------------------------------------------|------------------------------------------------------------------------|----------------------------------------------------------------------|
| Fabric_ApplicationName                       | Nome URI dell'applicazione nell'infrastruttura                                 | fabric:/MyApplication                                                |
| Fabric_CodePackageName                       | Nome del pacchetto di codice a cui appartiene il processo              | Codice                                                                 |
| Fabric_Endpoint\_IPOrFQDN\_*ServiceEndpointName*     | Indirizzo IP o nome di dominio completo dell'endpoint                                 | 10.0.0.1                                                     |
| Fabric\_Endpoint\_*ServiceEndpointName*              | Numero di porta per l'endpoint                                  | 8234                                                                 |
| Fabric_Folder_App_Log                        | Cartella dei log                                                             | C:\\\\Data\\\\_App\\\\_Node_0\\\\MyApplicationType_App12\\\\log      |
| Fabric_Folder_App_Temp                       | Cartella temporanea                                                            | C:\\\\Data\\\\_App\\\\_Node_0\\\\MyApplicationType_App12\\\\temp     |
| Fabric_Folder_App_Work                       | Cartella di lavoro                                                            | C:\\\\Data\\\\_App\\\\_Node_0\\\\MyApplicationType_App12\\\\work     |
| Fabric_Folder_Application                    | Home directory dell'applicazione                                           | C:\\\\Data\\\\_App\\\\_Node_0\\\\MyApplicationType_App12             |
| Fabric_IsContainerHost                       | Valore booleano che specifica se il processo è un contenitore                   | false                                                                |
| Fabric_NodeId                                | ID del nodo che esegue il processo                            | bf865279ba277deb864a976fbf4c200e                                     |
| Fabric_NodeIPOrFQDN                          | Indirizzo IP o nome di dominio completo del nodo, come specificato nel file manifesto del cluster | localhost o 10.0.0.1                                                |
| Fabric_NodeName                              | Nome del nodo che esegue il processo                          | _Node_0                                                              |
| Fabric_ServiceName                           | Nome del servizio, se ospitato in modalità ExclusiveProcess. Il valore di questa variabile è disponibile solo se si crea il servizio con ServicePackageActivationMode ExclusiveProcess.  | MyService                                               |
| Fabric_ServicePackageActivationId            | ID di attivazione del pacchetto del servizio                                         | GUID                                                               |
| Fabric_ServicePackageName                    | Nome del pacchetto del servizio di cui fa parte il processo                     | Web1Pkg                                                              |

Variabili di ambiente interne usate dal runtime di Service Fabric:

- Fabric_ApplicationHostId
- Fabric_ApplicationHostType
- Fabric_ApplicationId
- Fabric_CodePackageInstanceId
- Fabric_CodePackageInstanceSeqNum
- Fabric_RuntimeConnectionAddress
- Fabric_ServicePackageActivationGuid
- Fabric_ServicePackageInstanceId
- Fabric_ServicePackageInstanceSeqNum
- Fabric_ServicePackageVersionInstance
- FabricActivatorAddress
- FabricPackageFileName
- HostedServiceName