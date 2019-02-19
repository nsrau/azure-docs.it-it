---
title: Autenticazione con Mappe di Azure | Microsoft Docs
description: Autenticazione per l'uso dei servizi di Mappe di Azure.
author: walsehgal
ms.author: v-musehg
ms.date: 02/12/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: b97c303433eb8fadcda51257d37447f052ce4a3b
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/12/2019
ms.locfileid: "56118813"
---
# <a name="authentication-with-azure-maps"></a>Autenticazione con Mappe di Azure

Mappe di Azure supporta due modalità di autenticazione delle richieste. La chiave condivisa o Azure Active Directory (Azure AD) offrono metodi distinti per autorizzare ogni richiesta inviata a Mappe di Azure. Lo scopo di questo articolo è spiegare entrambi i metodi di autenticazione per illustrare l'implementazione dell'autenticazione.

## <a name="shared-key-authentication"></a>Autenticazione con chiave condivisa

L'autenticazione con chiave condivisa si basa sul passaggio delle chiavi generate dall'account di Mappe di Azure con ogni richiesta a Mappe di Azure.  Vengono generate due chiavi quando viene creato l'account di Mappe di Azure.  Ogni richiesta ai servizi di Mappe di Azure richiede l'aggiunta della chiave di sottoscrizione come parametro all'URL.

> [!Tip]
> È consigliabile rigenerare le chiavi regolarmente. Vengono fornite due chiavi per mantenere attive le connessioni con una chiave durante la rigenerazione dell'altra. Quando si rigenerano le chiavi è necessario aggiornare tutte le applicazioni che accedono a questo account per usare le nuove chiavi.

Per visualizzare le chiavi, vedere [Dettagli di autenticazione](https://aka.ms/amauthdetails).

## <a name="authentication-with-azure-active-directory-preview"></a>Autenticazione con Azure Active Directory (anteprima)

Mappe di Azure offre ora l'integrazione di [Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) per l'autenticazione delle richieste per i servizi di Mappe di Azure.  Azure AD fornisce un'autenticazione basata sull'identificazione che include il [controllo degli accessi in base al ruolo](https://docs.microsoft.com/azure/role-based-access-control/overview) per consentire l'accesso alle risorse di Mappe di Azure a livello di utente o di applicazione. Lo scopo di questo articolo è illustrare i concetti e i componenti dell'integrazione di Azure AD in Mappe di Azure.

## <a name="authentication-with-oauth-access-tokens"></a>Autenticazione con token di accesso OAuth

Mappe di Azure accetta token di accesso **OAuth 2.0** per i tenant di Azure AD associati alla sottoscrizione di Azure contenente un account di Mappe di Azure.  Mappe di Azure accetta token per:

* Utenti di Azure AD 
* Applicazioni di terze parti che usano autorizzazioni delegate dagli utenti
* Identità gestite per le risorse di Azure

Mappe di Azure genera un `unique identifier (client ID)` per ogni account di Mappe di Azure.  Quando l'ID client è combinato con parametri aggiuntivi, i token possono essere richiesti da Azure AD specificando il valore seguente:

```
https://login.microsoftonline.com
```
Per altre informazioni su come configurare Azure AD e richiedere i token per Mappe di Azure, vedere [Come gestire l'autenticazione](https://review.docs.microsoft.com/azure/azure-maps/how-to-manage-authentication).

Per informazioni generali sulla richiesta di token da Azure AD, vedere [Informazioni di base sull'autenticazione in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios).

## <a name="requesting-azure-map-resources-with-oauth-tokens"></a>Richiesta di risorse di Mappe di Azure con token OAuth

Una volta acquisito un token da Azure AD, è possibile inviare una richiesta a Mappe di Azure impostando le due seguenti intestazioni di richiesta necessarie:

| Intestazione di richiesta    |    Valore    |
|:------------------|:------------|
| x-ms-client-id    | 30d7cc….9f55|
| Authorization     | Bearer eyJ0e….HNIVN |

> [!Note]
> `x-ms-client-id` è il GUID basato sull'account di Mappe di Azure visualizzato nella pagina di autenticazione di Mappe di Azure

Di seguito è riportato l'esempio di instradamento delle richieste di Mappe di Azure con il token OAuth:

```
GET /route/directions/json?api-version=1.0&query=52.50931,13.42936:52.50274,13.43872 
Host: atlas.microsoft.com 
x-ms-client-id: 30d7cc….9f55 
Authorization: Bearer eyJ0e….HNIVN 
```

Per visualizzare l'ID client, vedere [Dettagli di autenticazione](https://aka.ms/amauthdetails).

## <a name="control-access-with-role-based-access-control-rbac"></a>Controllare l'accesso con il controllo degli accessi in base al ruolo

Una caratteristica chiave di Azure AD è il controllo dell'accesso alle risorse protette tramite il controllo degli accessi in base al ruolo. Dopo aver creato l'account di Mappe di Azure e aver registrato l'applicazione Azure AD di Mappe di Azure nel tenant di Azure AD, è ora possibile configurare il controllo degli accessi in base al ruolo per un utente, un'applicazione o una risorsa di Azure all'interno della pagina del portale dell'account di Mappe di Azure. 

Mappe di Azure supporta attualmente il controllo dell'accesso in lettura per singoli utenti di Azure AD, applicazioni o servizi di Azure tramite le identità gestite per le risorse di Azure.

![concetto](./media/azure-maps-authentication/concept.png)

Per visualizzare le impostazioni del controllo degli accessi in base al ruolo, vedere [Come configurare il controllo degli accessi in base al ruolo per Mappe di Azure](https://aka.ms/amrbac).

## <a name="managed-identities-for-azure-resources-and-azure-maps"></a>Identità gestite per risorse di Azure e Mappe di Azure

Le [identità gestite per le risorse di Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) forniscono ai servizi di Azure (servizio app di Azure, Funzioni di Azure, Macchine virtuali e così via) un'identità gestita automaticamente che può essere autorizzata per l'accesso ai servizi di Mappe di Azure.  

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sull'autenticazione di un'applicazione con Azure AD e Mappe di Azure, vedere [Come gestire l'autenticazione](https://review.docs.microsoft.com/azure/azure-maps/how-to-manage-authentication).

* Per altre informazioni sull'autenticazione di Mappe di Azure, controllo mappa e Azure AD, vedere [Azure AD e controllo mappa di Mappe di Azure](https://aka.ms/amaadmc).