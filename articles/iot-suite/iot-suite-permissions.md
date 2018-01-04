---
title: Azure IoT Suite e Azure Active Directory | Documentazione Microsoft
description: Descrive in che modo Azure IoT Suite usa Azure Active Directory per gestire le autorizzazioni.
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 246228ba-954a-4d96-b6d6-e53e4590cb4f
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/10/2017
ms.author: dobett
ms.openlocfilehash: e5804cda921e9d598d0ed02c4fafccdb40fbe7a5
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/18/2017
---
# <a name="permissions-on-the-azureiotsuitecom-site"></a>Autorizzazioni per il sito azureiotsuite.com

## <a name="what-happens-when-you-sign-in"></a>Cosa accade quando si accede

Quando si accede per la prima volta ad [azureiotsuite.com][lnk-azureiotsuite], il sito determina i livelli di autorizzazione in base al tenant di Azure Active Directory (AAD) attualmente selezionato e alla sottoscrizione di Azure.

1. Per prima cosa, il sito cerca prima in Azure a quale tenant di AAD appartiene l'utente per popolare l'elenco di tenant visualizzato accanto al nome utente connesso. In questo momento, il sito può ricevere solo i token utente per un tenant alla volta. Di conseguenza, quando si passa a un tenant diverso usando l'elenco a discesa nell'angolo superiore destro, il sito registra di nuovo l'utente nel tenant per ottenere il token di tale tenant.

2. Successivamente, il sito cerca in Azure quali sottoscrizioni sono state associate dall'utente al tenant selezionato. Le sottoscrizioni disponibili vengono visualizzate quando si crea una nuova soluzione preconfigurata.

3. Infine, il sito recupera tutte le risorse nelle sottoscrizioni e nei gruppi di risorse contrassegnati come soluzioni preconfigurate e popola i riquadri nella home page.

Le sezioni seguenti descrivono i ruoli che controllano l'accesso alle soluzioni preconfigurate.

## <a name="aad-roles"></a>Ruoli AAD

I ruoli AAD controllano la capacità di provisioning delle soluzioni preconfigurate e gestiscono gli utenti in una soluzione preconfigurata.

Per altre informazioni sui ruoli di amministratore in AAD, vedere [Assegnazione dei ruoli di amministratore in Azure Active Directory][lnk-aad-admin]. L'articolo corrente è incentrato sui ruoli della directory di **Amministratore globale** e **Utente** usati dalle soluzioni preconfigurate.

### <a name="global-administrator"></a>Amministratore globale

Per ogni tenant di AAD possono essere presenti più amministratori globali:

* Quando si crea un tenant di AAD, si è per impostazione predefinita l'amministratore globale del tenant.
* L'amministratore globale può effettuare il provisioning di soluzioni preconfigurate di base e standard.

### <a name="domain-user"></a>Utente di dominio

Per ogni tenant di AAD possono essere presenti molti utenti del dominio:

* Un utente di dominio può effettuare il provisioning di una soluzione preconfigurata di base tramite il sito [azureiotsuite.com][lnk-azureiotsuite].
* Un utente di dominio può creare una soluzione preconfigurata di base tramite l'interfaccia della riga di comando.

### <a name="guest-user"></a>Utente guest

Per ogni tenant AAD possono essere presenti molti utenti guest. Gli utenti guest hanno un set di diritti limitato nel tenant di AAD. Di conseguenza, gli utenti guest non possono effettuare il provisioning di una soluzione preconfigurata nel tenant di AAD.

Per altre informazioni sugli utenti e i ruoli in AAD, vedere le risorse seguenti:

* [Creare utenti in Azure AD][lnk-create-edit-users]
* [Assegnare utenti alle app][lnk-assign-app-roles]

## <a name="azure-subscription-administrator-roles"></a>Ruoli di amministratore della sottoscrizione di Azure

I ruoli di amministrazione di Azure controllano la possibilità di eseguire il mapping di una sottoscrizione di Azure a un tenant di AD.

È possibile trovare altre informazioni sui ruoli di amministratore nell'articolo [Come aggiungere o modificare i ruoli Co-amministratore, Amministratore del servizio e Amministratore account di Azure][lnk-admin-roles].

## <a name="faq"></a>Domande frequenti

### <a name="im-a-service-administrator-and-id-like-to-change-the-directory-mapping-between-my-subscription-and-a-specific-aad-tenant-how-do-i-complete-this-task"></a>Un amministratore del servizio vuole modificare il mapping della directory tra la sottoscrizione e un tenant di AAD specifico. Come completare questa attività

Vedere [Come aggiungere una sottoscrizione esistente alla directory di Azure AD](../active-directory/active-directory-how-subscriptions-associated-directory.md#to-associate-an-existing-subscription-to-your-azure-ad-directory)

### <a name="i-want-to-change-a-service-administrator-or-co-administrator-when-logged-in-with-an-organisational-account"></a>Si vuole modificare un amministratore del servizio o coamministratore quando si accede con un account dell'organizzazione

Vedere l'articolo di supporto [Modifica dell'amministratore del servizio e del coamministratore quando si effettua l'accesso con un account dell'organizzazione][lnk-service-admins].

### <a name="why-am-i-seeing-this-error-your-account-does-not-have-the-proper-permissions-to-create-a-solution-please-check-with-your-account-administrator-or-try-with-a-different-account"></a>Perché viene visualizzato un errore simile al seguente? "L'account non ha le autorizzazioni appropriate per creare una soluzione. Rivolgersi all'amministratore account o provare con un account diverso".

Per indicazioni vedere il diagramma seguente:

![][img-flowchart]

> [!NOTE]
> Se si continua a visualizzare l'errore dopo aver confermato di essere un amministratore globale del tenant AAD e un coamministratore della sottoscrizione, l'amministratore dell'account dovrà rimuovere l'utente e assegnare nuovamente le autorizzazioni necessarie in questo ordine. Innanzitutto, dovrà aggiungere l'utente come amministratore globale e quindi aggiungere l'utente come coamministratore della sottoscrizione di Azure. Se i problemi persistono, contattare il tema [Guida e supporto][lnk-help-support].

### <a name="why-am-i-seeing-this-error-when-i-have-an-azure-subscription-an-azure-subscription-is-required-to-create-pre-configured-solutions-you-can-create-a-free-trial-account-in-just-a-couple-of-minutes"></a>Perché viene visualizzato questo errore quando si dispone di una sottoscrizione di Azure? "Per creare soluzioni preconfigurate è necessaria una sottoscrizione di Azure. È possibile creare un account di valutazione gratuito in pochi minuti."

Se si è certi di avere una sottoscrizione di Azure, convalidare il mapping del tenant per la sottoscrizione e verificare che sia selezionato il tenant corretto nell'elenco a discesa. Se si è verificato che il tenant desiderato è corretto, seguire il diagramma precedente e verificare il mapping della sottoscrizione e il tenant di AAD.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni su IoT Suite, vedere come [personalizzare una soluzione preconfigurata][lnk-customize].

[img-flowchart]: media/iot-suite-permissions/flowchart.png

[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-rm-github-repo]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-pm-github-repo]: https://github.com/Azure/azure-iot-predictive-maintenance
[lnk-cf-github-repo]: https://github.com/Azure/azure-iot-connected-factory
[lnk-aad-admin]: ../active-directory/active-directory-assign-admin-roles.md
[lnk-portal]: https://portal.azure.com/
[lnk-create-edit-users]: ../active-directory/active-directory-create-users.md
[lnk-assign-app-roles]: ../active-directory/active-directory-coreapps-assign-user-azure-portal.md
[lnk-service-admins]: https://azure.microsoft.com/support/changing-service-admin-and-co-admin/
[lnk-admin-roles]: ../billing/billing-add-change-azure-subscription-administrator.md
[lnk-resource-cs]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/DeviceAdministration/Web/Security/RolePermissions.cs
[lnk-help-support]: https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[lnk-customize]: iot-suite-guidance-on-customizing-preconfigured-solutions.md
