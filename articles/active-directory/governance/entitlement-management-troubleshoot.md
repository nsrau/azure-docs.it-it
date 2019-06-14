---
title: Risolvere i problemi di gestione dei diritti di Azure AD (anteprima) - Azure Active Directory
description: Informazioni su alcuni elementi che sarà necessario verificare che consentono di risolvere i problemi di gestione dei diritti di Azure Active Directory (anteprima).
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 05/30/2019
ms.author: rolyon
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: c2526ef10c3080dae1b32881a109a9436a0fd390
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66473832"
---
# <a name="troubleshoot-azure-ad-entitlement-management-preview"></a>Risolvere i problemi di gestione dei diritti di Azure AD (anteprima)

> [!IMPORTANT]
> Gestione entitlement di Azure Active Directory (Azure AD) è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Questo articolo descrive alcuni elementi sarà necessario verificare che consentono di risolvere i problemi di gestione dei diritti di Azure Active Directory (Azure AD).

## <a name="checklist-for-entitlement-management-administration"></a>Elenco di controllo per l'amministrazione di gestione dei diritti

* Se si riceve un messaggio di accesso negato durante la configurazione di gestione dei diritti, pur si è un amministratore globale, assicurarsi che la directory include un' [licenza di Azure AD Premium P2 (o EMS E5)](entitlement-management-overview.md#license-requirements).  
* Se si otterrà un accesso negato messaggio durante la creazione o la visualizzazione di pacchetti di accesso e sei un membro di un gruppo creatore di catalogo, è necessario creare un catalogo prima di creare il primo pacchetto di accesso.

## <a name="checklist-for-adding-a-resource"></a>Elenco di controllo per l'aggiunta di una risorsa

* Per un'applicazione può essere una risorsa in un pacchetto di accesso, deve avere almeno un ruolo per le risorse che può essere assegnato. I ruoli vengono definiti dall'applicazione stessa e vengono gestiti in Azure AD. Si noti che il portale di Azure può anche visualizzare le entità servizio per i servizi che non possono essere selezionati come le applicazioni.  In particolare **Exchange Online** e **SharePoint Online** sono servizi, non le applicazioni con i ruoli delle risorse nella directory, in modo che non può essere incluso in un pacchetto di accesso.  In alternativa, usare licenze basate su gruppo per stabilire una licenza appropriata per un utente che deve accedere a tali servizi.

* Per un gruppo come risorsa in un pacchetto di accesso, deve essere in grado di essere modificabile in Azure AD.  I gruppi che hanno origine in un server di Active Directory locale non possono essere assegnati come risorse perché il proprietario o attributi del membro non possono essere modificati in Azure AD.  

* Le raccolte documenti di SharePoint Online e singoli documenti non possono essere aggiunti come risorse.  In alternativa, creare un gruppo di sicurezza di Azure AD, includere tale gruppo e un ruolo del sito nel pacchetto di accesso e in SharePoint Online, usare tale gruppo per controllare l'accesso alla raccolta documenti o al documento.

* Se sono presenti utenti che sono già stati assegnati a una risorsa che si desidera gestire con un pacchetto di accesso, assicurarsi che gli utenti vengono assegnati al pacchetto di accesso con un criterio appropriato. Potrebbe ad esempio, si desidera includere un gruppo in un pacchetto di accesso che dispone già di utenti nel gruppo. Se gli utenti richiedono il gruppo di continuare ad accedere, devono avere un criterio appropriato per i pacchetti di accesso in modo da non perdono il loro accesso al gruppo. È possibile assegnare il pacchetto di accesso entrambi chiedendo agli utenti di richiedere il pacchetto di accesso contenente la risorsa o assegnandole direttamente al pacchetto di accesso. Per altre informazioni, vedere [modificare e gestire un pacchetto di accesso esistente](entitlement-management-access-package-edit.md).

## <a name="checklist-for-providing-external-users-access"></a>Elenco di controllo per fornire l'accesso agli utenti esterni

* Se è presente un B2B [elenco Consenti](../b2b/allow-deny-list.md), gli utenti non sono consentite con le directory non sarà in grado di richiedere l'accesso.

* Verificare che siano presenti alcun [criteri di accesso condizionale](../conditional-access/require-managed-devices.md) che potrebbe impedire agli utenti esterni che richiedono l'accesso o la possibilità di utilizzare le applicazioni nei pacchetti di accesso.

## <a name="checklist-for-request-issues"></a>Elenco di controllo per i problemi di richiesta

* Quando un utente vuole richiedere l'accesso a un pacchetto di accesso, assicurarsi che utilizzano le **collegamento del portale di accesso personale** per il pacchetto di accesso. Per altre informazioni, vedere [collegamento del portale di accesso personali copia](entitlement-management-access-package-edit.md#copy-my-access-portal-link).

* Quando un utente accede al portale di accesso personale per richiedere un pacchetto di accesso, assicurarsi che l'autenticazione usando il proprio account dell'organizzazione. L'account aziendale può essere un account nella directory delle risorse o in una directory in cui è incluso in uno dei criteri del pacchetto di accesso. Se l'account dell'utente non è un account aziendale o la directory non è incluso nei criteri, l'utente non visualizzerà il pacchetto di accesso. Per altre informazioni, vedere [richiedere l'accesso a un pacchetto di accesso](entitlement-management-request-access.md).

* Se un utente viene impedito l'accesso alla directory delle risorse, non saranno in grado di richiedere l'accesso nel portale di accesso personale. Prima che l'utente possa richiedere l'accesso, è necessario rimuovere il blocco di accesso dal profilo dell'utente. Per rimuovere il blocco di accesso, nel portale di Azure, fare clic su **Azure Active Directory**, fare clic su **Users**, fare clic sull'utente e quindi fare clic su **profilo**. Modificare il **le impostazioni** sezione e modificare **blocca l'accesso** al **No**. Per altre informazioni, vedere [Aggiungi o aggiorna le informazioni sul profilo dell'utente tramite Azure Active Directory](../fundamentals/active-directory-users-profile-azure-portal.md).  È anche possibile verificare se l'utente è stata bloccata a causa dell'errore un' [dei criteri di Identity Protection](../identity-protection/howto-unblock-user.md).

* Nel portale di accesso personale, se un utente è un richiedente sia un responsabile approvazione, non visualizzeranno la richiesta per un pacchetto di accesso sul **approvazioni** pagina. Questo comportamento è intenzionale: un utente non può approvare le proprie richieste. Verificare che il pacchetto di accesso che richiedono ha responsabili approvazione aggiuntivi configurati nei criteri. Per altre informazioni, vedere [modifica un criterio esistente](entitlement-management-access-package-edit.md#edit-an-existing-policy).

* Se un nuovo utente esterno, che non firmato in precedenza nella directory, riceve un pacchetto di accesso, incluso un sito di SharePoint Online, il pacchetto di accesso mostrerà come non completamente recapitato fino a quando non viene eseguito il provisioning di account in SharePoint Online.

## <a name="next-steps"></a>Passaggi successivi

- [Visualizzare i report del modo in cui gli utenti hanno ottenuto accesso nella gestione dei diritti](entitlement-management-reports.md)
