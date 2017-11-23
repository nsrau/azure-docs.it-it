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
ms.date: 11/02/2017
ms.author: dobett
ms.openlocfilehash: 17d9e496aedfdf98423e821e7f379dc481ffa0a5
ms.sourcegitcommit: f67f0bda9a7bb0b67e9706c0eb78c71ed745ed1d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2017
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
* L'amministratore globale può eseguire il provisioning di una soluzione preconfigurata e gli viene assegnato un ruolo **Admin** per l'applicazione all'interno di tenant di AAD.
* Se un altro utente nello stesso tenant di AAD crea un'applicazione, il ruolo predefinito concesso all'amministratore globale è **ReadOnly**.
* Gli amministratori globali possono assegnare agli utenti dei ruoli per le applicazioni tramite il [portale di Azure][lnk-portal].

### <a name="domain-user"></a>Utente di dominio

Per ogni tenant di AAD possono essere presenti molti utenti del dominio:

* Un utente di dominio può effettuare il provisioning di una soluzione preconfigurata tramite il sito [azureiotsuite.com][lnk-azureiotsuite]. Per impostazione predefinita, all'utente di dominio viene concesso il ruolo di **Amministratore** nell'applicazione in cui è stato eseguito il provisioning.
* Un utente di dominio può creare un'applicazione usando lo script build.cmd nel repository [azure-iot-remote-monitoring][lnk-rm-github-repo], [azure-iot-predictive-maintenance][lnk-pm-github-repo] oppure [azure-iot-connected-factory][lnk-cf-github-repo]. Tuttavia, il ruolo predefinito concesso all'utente di dominio è **ReadOnly**, perché un utente di dominio non dispone dell'autorizzazione per assegnare i ruoli.
* Se un altro utente nel tenant di AAD crea un'applicazione, all'utente di dominio viene assegnato il ruolo **ReadOnly** per impostazione predefinita per l'applicazione.
* Un utente di dominio non può assegnare ruoli per le applicazioni, quindi non può aggiungere utenti o ruoli per gli utenti di un'applicazione anche se ne è stato effettuato il provisioning.

### <a name="guest-user"></a>Utente guest

Per ogni tenant AAD possono essere presenti molti utenti guest. Gli utenti guest hanno un set di diritti limitato nel tenant di AAD. Di conseguenza, gli utenti guest non possono effettuare il provisioning di una soluzione preconfigurata nel tenant di AAD.

Per altre informazioni sugli utenti e i ruoli in AAD, vedere le risorse seguenti:

* [Creare utenti in Azure AD][lnk-create-edit-users]
* [Assegnare utenti alle app][lnk-assign-app-roles]

## <a name="azure-subscription-administrator-roles"></a>Ruoli di amministratore della sottoscrizione di Azure

I ruoli di amministrazione di Azure controllano la possibilità di eseguire il mapping di una sottoscrizione di Azure a un tenant di AD.

È possibile trovare altre informazioni sui ruoli di amministratore nell'articolo [Come aggiungere o modificare i ruoli Co-amministratore, Amministratore del servizio e Amministratore account di Azure][lnk-admin-roles].

## <a name="application-roles"></a>Ruoli applicazione

I ruoli applicazione controllano l'accesso ai dispositivi nella soluzione preconfigurato.

In un'applicazione su cui è stato eseguito il provisioning sono stati definiti due ruoli e uno implicito:

* **Admin:** dispone di autorizzazioni complete per aggiungere, gestire, rimuovere i dispositivi e modificare le impostazioni.
* **ReadOnly:** è possibile visualizzare i dispositivi, le regole, le azioni, i processi e i dati di telemetria.

È possibile trovare le autorizzazioni assegnate a ogni ruolo nel file di origine [RolePermissions.cs][lnk-resource-cs].

### <a name="changing-application-roles-for-a-user"></a>Modifica dei ruoli applicazione di un utente

È possibile usare la procedura seguente per configurare un utente di Active Directory come amministratore della soluzione preconfigurata.

Per modificare i ruoli per un utente, è necessario essere un amministratore globale di AAD:

1. Accedere al [portale di Azure][lnk-portal].
2. Selezionare **Azure Active Directory**.
3. Verificare che si sta usando la directory selezionata in azureiotsuite.com durante il provisioning della soluzione. Se si dispone di più directory associate alla sottoscrizione, è possibile passare da una all'altra facendo clic sul nome dell'account in alto a destra del portale.
4. Fare clic su **Applicazioni aziendali**, quindi su **Tutte le applicazioni**.
4. Vengono visualizzate **Tutte le applicazioni** con stato **Qualsiasi**. Eseguire quindi una ricerca per un'applicazione con il nome della soluzione preconfigurata.
5. Fare clic sul nome dell'applicazione che coincide con il nome della soluzione preconfigurata.
6. Fare clic su **Utenti e gruppi**.
7. Selezionare l'utente per cui si vogliono scambiare i ruoli.
8. Fare clic su **Assegna** e selezionare il ruolo, ad esempio **Amministratore**, da assegnare all'utente, fare clic sul segno di spunta.

## <a name="faq"></a>Domande frequenti

### <a name="im-a-service-administrator-and-id-like-to-change-the-directory-mapping-between-my-subscription-and-a-specific-aad-tenant-how-do-i-complete-this-task"></a>Un amministratore del servizio vuole modificare il mapping della directory tra la sottoscrizione e un tenant di AAD specifico. Come completare questa attività

Vedere [Come aggiungere una sottoscrizione esistente alla directory di Azure AD](../active-directory/active-directory-how-subscriptions-associated-directory.md#to-add-an-existing-subscription-to-your-azure-ad-directory)

### <a name="im-a-domain-usermember-on-the-aad-tenant-and-ive-created-a-preconfigured-solution-how-do-i-get-assigned-a-role-for-my-application"></a>Un utente o membro di dominio del tenant di AAD ha creato una soluzione preconfigurata. Come gli viene assegnato un ruolo per l'applicazione?

Contattare l'amministratore globale per diventare amministratore globale del tenant di AAD e assegnare ruoli agli utenti. In alternativa, contattare l'amministratore globale per assegnare direttamente un ruolo. Se si vuole modificare il tenant di AAD in cui è stata distribuita la soluzione preconfigurata, vedere la domanda successiva.

### <a name="how-do-i-switch-the-aad-tenant-my-remote-monitoring-preconfigured-solution-and-application-are-assigned-to"></a>Come si cambia il tenant AAD a cui sono assegnate l'applicazione e soluzione preconfigurata di monitoraggio remoto?

È possibile eseguire una distribuzione cloud da <https://github.com/Azure/azure-iot-remote-monitoring> ed eseguire una ridistribuizione con un tenant AAD appena creato. Poiché per impostazione predefinita quando si crea un nuovo tenant di AAD si diventa un amministratore globale, si dispone anche dell'autorizzazione per aggiungere utenti e assegnare loro dei ruoli.

1. Creare una directory di AAD nel [Portale di Azure][lnk-portal].
2. Passare a <https://github.com/Azure/azure-iot-remote-monitoring>.
3. Eseguire `build.cmd cloud [debug | release] {name of previously deployed remote monitoring solution}`. Ad esempio `build.cmd cloud debug myRMSolution`.
4. Quando richiesto, impostare il **tenantid** come tenant appena creato al posto del tenant precedente.

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

[img-flowchart]: media/iot-suite-v1-permissions/flowchart.png

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
[lnk-customize]: iot-suite-v1-guidance-on-customizing-preconfigured-solutions.md
