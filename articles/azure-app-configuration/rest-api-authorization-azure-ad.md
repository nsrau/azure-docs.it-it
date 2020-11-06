---
title: API REST di configurazione app Azure-Azure Active Directory autorizzazione
description: Usare Azure Active Directory per l'autorizzazione per la configurazione app Azure con l'API REST
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: bebab7c06062726f7b5c7868f984cadda3b4c98e
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424183"
---
# <a name="azure-active-directory-authorization---rest-api-reference"></a>Azure Active Directory autorizzazione-informazioni di riferimento sull'API REST

Quando si usa l'autenticazione Azure Active Directory (Azure AD), l'autorizzazione viene gestita dal controllo degli accessi in base al ruolo di Azure (RBAC). Per consentire l'accesso alle risorse, il controllo degli accessi in base al ruolo di Azure richiede l'assegnazione di utenti ai ruoli. Ogni ruolo contiene un set di azioni che gli utenti assegnati al ruolo saranno in grado di eseguire.

## <a name="roles"></a>Ruoli

Per impostazione predefinita, i ruoli seguenti sono ruoli predefiniti disponibili nelle sottoscrizioni di Azure:

- **Proprietario dei dati di configurazione app Azure** : questo ruolo fornisce l'accesso completo a tutte le operazioni.
- **Lettore dati di configurazione app Azure** : questo ruolo Abilita le operazioni di lettura.

## <a name="actions"></a>Azioni

I ruoli contengono un elenco di azioni che possono essere eseguite dagli utenti assegnati a tale ruolo. App Azure configurazione supporta le azioni seguenti:

- `Microsoft.AppConfiguration/configurationStores/keyValues/read`: Questa azione consente l'accesso in lettura alle risorse chiave-valore di configurazione dell'app, ad esempio/kV e/labels.
- `Microsoft.AppConfiguration/configurationStores/keyValues/write`: Questa azione consente l'accesso in scrittura alle risorse chiave-valore di configurazione dell'app.
- `Microsoft.AppConfiguration/configurationStores/keyValues/delete`: Questa azione consente di eliminare le risorse chiave-valore di configurazione dell'app. Si noti che l'eliminazione di una risorsa restituisce il valore chiave che è stato eliminato.

## <a name="errors"></a>Errors

```http
HTTP/1.1 403 Forbidden
```

**Motivo:** L'entità che effettua la richiesta non dispone delle autorizzazioni necessarie per eseguire l'operazione richiesta.
**Soluzione:** Assegnare il ruolo necessario per eseguire l'operazione richiesta al principale che effettua la richiesta.

## <a name="managing-role-assignments"></a>Gestione delle assegnazioni di ruolo

Per gestire le assegnazioni di ruolo, è possibile usare le procedure [RBAC di Azure](https://docs.microsoft.com/azure/role-based-access-control/overview) standard in tutti i servizi di Azure. È possibile eseguire questa operazione tramite l'interfaccia della riga di comando di Azure, PowerShell, il portale di Azure e altro ancora. La documentazione ufficiale su come effettuare le assegnazioni di ruolo è disponibile [qui](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal).
