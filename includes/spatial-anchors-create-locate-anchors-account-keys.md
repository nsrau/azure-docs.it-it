---
author: msftradford
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 11/20/2020
ms.author: parkerra
ms.openlocfilehash: 7210b05566f5cd6f3c56792bce0904b3c9b645da
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95993040"
---
## <a name="set-up-authentication"></a>Configurare l'autenticazione

Per accedere al servizio, è necessario specificare una chiave dell'account, un token di accesso o un token di autenticazione di Azure Active Directory. Per altre informazioni, vedere la [pagina relativa al concetto di autenticazione](../articles/spatial-anchors/concepts/authentication.md).

### <a name="account-keys"></a>Chiavi dell'account

Le chiavi dell'account sono credenziali che consentono all'applicazione di eseguire l'autenticazione con il servizio degli ancoraggi nello spazio di Azure. Le chiavi dell'account consentono di essere subito operativi, soprattutto durante la fase di sviluppo dell'integrazione dell'applicazione con gli ancoraggi nello spazio di Azure. Di conseguenza, è possibile usare le chiavi dell'account incorporandole nelle applicazioni client durante lo sviluppo. Nelle fasi successive allo sviluppo è consigliabile passare a un meccanismo di autenticazione a livello di produzione, supportato da token di accesso o dall'autenticazione utente di Azure Active Directory. Per ottenere una chiave dell'account per lo sviluppo, visitare l'account ancoraggi nello spazio di Azure e passare alla scheda "Chiavi".