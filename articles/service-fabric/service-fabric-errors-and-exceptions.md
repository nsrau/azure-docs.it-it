---
title: Eccezioni FabricClient comuni | Documentazione Microsoft
description: Vengono descritti errori ed eccezioni comuni che possono essere generati dalle API FabricClient durante l'esecuzione di operazioni di gestione di applicazioni e cluster.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: bb821313-b221-479f-b08e-36cf07e60a07
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/30/2017
ms.author: ryanwi
ms.openlocfilehash: fec85bcf1ce46374815fddcd21ab2e8f73cbd1df
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="common-exceptions-and-errors-when-working-with-the-fabricclient-apis"></a>Errori ed eccezioni comuni quando si usano le API FabricClient
Le API [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient#System_Fabric_FabricClient) consentono agli amministratori di cluster e applicazioni di eseguire attività amministrative su applicazioni, servizi o cluster di Service Fabric. Ad esempio, la distribuzione, l'aggiornamento e la rimozione di applicazioni, il controllo dell'integrità di un cluster o il test di un servizio. Gli sviluppatori di applicazioni e gli amministratori di cluster possono usare le API FabricClient per sviluppare strumenti per la gestione del cluster e delle applicazioni Service Fabric.

Esistono molti tipi diversi di operazioni che possono essere eseguite tramite FabricClient.  Ogni metodo può generare eccezioni per errori dovuti a input non corretti, errori di runtime o problemi temporanei dell'infrastruttura.  Per individuare le eccezioni generate da un metodo specifico, vedere la documentazione di riferimento delle API. Esistono alcune eccezioni, tuttavia, che possono essere generate da diverse API [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient#System_Fabric_FabricClient). Nella tabella seguente vengono elencate le eccezioni comuni alle API FabricClient.

| Eccezione | Generata quando |
| --- |:--- |
| [System.Fabric.FabricObjectClosedException](https://docs.microsoft.com/dotnet/api/system.fabric.fabricobjectclosedexception#System_Fabric_FabricObjectClosedException) |L'oggetto [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient#System_Fabric_FabricClient) si trova in uno stato chiuso. Eliminare l'oggetto [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient#System_Fabric_FabricClient) in uso e creare un'istanza di un nuovo oggetto [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient#System_Fabric_FabricClient). |
| [System.TimeoutException](https://docs.microsoft.com/dotnet/core/api/system.timeoutexception#System_TimeoutException) |L'operazione ha raggiunto il timeout. [OperationTimedOut](https://docs.microsoft.com/dotnet/api/system.fabric.fabricerrorcode#System_Fabric_FabricErrorCode) viene restituito quando per il completamento dell'operazione occorre più tempo di quello stabilito in MaxOperationTimeout. |
| [System.UnauthorizedAccessException](https://docs.microsoft.com/dotnet/core/api/system.unauthorizedaccessexception#System_UnauthorizedAccessException) |Il controllo di accesso per l'operazione non è riuscito. Viene restituito E_ACCESSDENIED. |
| [System.Fabric.FabricException](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception#System_Fabric_FabricException) |Si è verificato un errore di runtime durante l'operazione. Tutti i metodi FabricClient possono potenzialmente generare un'eccezione [FabricException](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception#System_Fabric_FabricException). La proprietà [ErrorCode](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception#System_Fabric_FabricException_ErrorCode) indica la causa esatta dell'eccezione. I codici di errore sono definiti nell'enumerazione [FabricErrorCode](https://docs.microsoft.com/dotnet/api/system.fabric.fabricerrorcode#System_Fabric_FabricErrorCode). |
| [System.Fabric.FabricTransientException](https://docs.microsoft.com/dotnet/api/system.fabric.fabrictransientexception#System_Fabric_FabricTransientException) |L'operazione non è riuscita a causa di una condizione di errore temporanea. Ad esempio, un'operazione potrebbe non riuscire perché un quorum di repliche non è temporaneamente raggiungibile. Le eccezioni temporanee corrispondono alle operazioni non riuscite che è possibile ritentare. |

Alcuni errori [FabricErrorCode](https://docs.microsoft.com/dotnet/api/system.fabric.fabricerrorcode#System_Fabric_FabricErrorCode) comuni che possono essere restituiti in un'eccezione [FabricException](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception#System_Fabric_FabricException):

| Errore | Condizione |
| --- |:--- |
| CommunicationError |L'operazione non è riuscita a causa di un errore di comunicazione, ripetere l'operazione. |
| InvalidCredentialType |Il tipo di credenziale non è valido. |
| InvalidX509FindType |Il tipo X509FindType non è valido. |
| InvalidX509StoreLocation |Il percorso dell'archivio X509 non è valido. |
| InvalidX509StoreName |Il nome dell'archivio X509 non è valido. |
| InvalidX509Thumbprint |La stringa di identificazione personale del certificato X509 non è valida. |
| InvalidProtectionLevel |Il livello di protezione non è valido. |
| InvalidX509Store |Non è possibile aprire l'archivio certificati X509. |
| InvalidSubjectName |Il nome soggetto non è valido. |
| InvalidAllowedCommonNameList |Il formato della stringa dell'elenco nomi comuni non è valido. Deve essere un elenco delimitato da virgole. |

