---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 02/21/2019
ms.author: rgarcia
ms.openlocfilehash: 9bd213b63b69a25fb2530cd8f6659abf5357616a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "76694334"
---
## <a name="set-up-authentication"></a>Configurare l'autenticazione

Per accedere al servizio, è necessario specificare una chiave dell'account, un token di accesso o un token di autenticazione di Azure Active Directory. Per altre informazioni, vedere la [pagina relativa al concetto di autenticazione](/azure/spatial-anchors/concepts/authentication).

### <a name="account-keys"></a>Chiavi dell'account

Le chiavi dell'account sono credenziali che consentono all'applicazione di eseguire l'autenticazione con il servizio degli ancoraggi nello spazio di Azure. Le chiavi dell'account consentono di essere subito operativi, soprattutto durante la fase di sviluppo dell'integrazione dell'applicazione con gli ancoraggi nello spazio di Azure. Di conseguenza, è possibile usare le chiavi dell'account incorporandole nelle applicazioni client durante lo sviluppo. Nelle fasi successive allo sviluppo è consigliabile passare a un meccanismo di autenticazione a livello di produzione, supportato da token di accesso o dall'autenticazione utente di Azure Active Directory. Per ottenere una chiave dell'account per lo sviluppo, visitare l'account ancoraggi nello spazio di Azure e passare alla scheda "Chiavi".
