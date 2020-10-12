---
title: Eccezioni FabricClient comuni generate
description: Vengono descritti errori ed eccezioni comuni che possono essere generati dalle API FabricClient durante l'esecuzione di operazioni di gestione di applicazioni e cluster.
author: georgewallace
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: gwallace
ms.openlocfilehash: 7f3c3e072a3a2e4f7723f84b2c70ba0d0ddb9d03
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86258838"
---
# <a name="common-exceptions-and-errors-when-working-with-the-fabricclient-apis"></a>Errori ed eccezioni comuni quando si usano le API FabricClient
Le API [FabricClient](/dotnet/api/system.fabric.fabricclient) consentono agli amministratori di cluster e applicazioni di eseguire attività amministrative su applicazioni, servizi o cluster di Service Fabric. Ad esempio, la distribuzione, l'aggiornamento e la rimozione di applicazioni, il controllo dell'integrità di un cluster o il test di un servizio. Gli sviluppatori di applicazioni e gli amministratori di cluster possono usare le API FabricClient per sviluppare strumenti per la gestione del cluster e delle applicazioni Service Fabric.

Esistono molti tipi diversi di operazioni che possono essere eseguite tramite FabricClient.  Ogni metodo può generare eccezioni per errori dovuti a input non corretti, errori di runtime o problemi temporanei dell'infrastruttura.  Per individuare le eccezioni generate da un metodo specifico, vedere la documentazione di riferimento delle API. Esistono alcune eccezioni, tuttavia, che possono essere generate da diverse API [FabricClient](/dotnet/api/system.fabric.fabricclient). Nella tabella seguente vengono elencate le eccezioni comuni alle API FabricClient.

| Eccezione | Generata quando |
| --- |:--- |
| [System.Fabric.FabricObjectClosedException](/dotnet/api/system.fabric.fabricobjectclosedexception) |L'oggetto [FabricClient](/dotnet/api/system.fabric.fabricclient) si trova in uno stato chiuso. Eliminare l'oggetto [FabricClient](/dotnet/api/system.fabric.fabricclient) in uso e creare un'istanza di un nuovo oggetto [FabricClient](/dotnet/api/system.fabric.fabricclient). |
| [System.TimeoutException](/dotnet/core/api/system.timeoutexception) |Timeout dell'operazione. [OperationTimedOut](/dotnet/api/system.fabric.fabricerrorcode) viene restituito quando l'operazione richiede più di MaxOperationTimeout per il completamento. |
| [System.UnauthorizedAccessException](/dotnet/core/api/system.unauthorizedaccessexception) |Il controllo di accesso per l'operazione non è riuscito. Viene restituito E_ACCESSDENIED. |
| [System.Fabric.FabricException](/dotnet/api/system.fabric.fabricexception) |Si è verificato un errore di runtime durante l'operazione. Tutti i metodi FabricClient possono potenzialmente generare un'eccezione [FabricException](/dotnet/api/system.fabric.fabricexception). La proprietà [ErrorCode](/dotnet/api/system.fabric.fabricexception.errorcode) indica la causa esatta dell'eccezione. I codici di errore sono definiti nell'enumerazione [FabricErrorCode](/dotnet/api/system.fabric.fabricerrorcode). |
| [System.Fabric.FabricTransientException](/dotnet/api/system.fabric.fabrictransientexception) |L'operazione non è riuscita a causa di una condizione di errore temporanea. Ad esempio, un'operazione potrebbe non riuscire perché un quorum di repliche non è temporaneamente raggiungibile. Le eccezioni temporanee corrispondono alle operazioni non riuscite che è possibile ritentare. |

Alcuni errori [FabricErrorCode](/dotnet/api/system.fabric.fabricerrorcode) comuni che possono essere restituiti in un'eccezione [FabricException](/dotnet/api/system.fabric.fabricexception):

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
