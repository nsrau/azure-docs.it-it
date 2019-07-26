---
title: Risolvere i problemi relativi alla gestione dei diritti Azure AD (anteprima)-Azure Active Directory
description: Informazioni su alcuni elementi da controllare per la risoluzione dei problemi Azure Active Directory gestione dei diritti (anteprima).
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 05/30/2019
ms.author: ajburnle
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 39ec27c75ff5ba9164b44b0524f90a4e28ab20f1
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/25/2019
ms.locfileid: "68488983"
---
# <a name="troubleshoot-azure-ad-entitlement-management-preview"></a>Risolvere i problemi di Azure AD gestione dei diritti (anteprima)

> [!IMPORTANT]
> Gestione entitlement di Azure Active Directory (Azure AD) è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Questo articolo descrive alcuni elementi da controllare per semplificare la risoluzione dei problemi di Azure Active Directory (Azure AD) la gestione dei diritti.

## <a name="checklist-for-entitlement-management-administration"></a>Elenco di controllo per l'amministrazione della gestione dei diritti

* Se si riceve un messaggio di accesso negato quando si configura la gestione dei diritti e si è un amministratore globale, assicurarsi che la directory disponga di una [licenza Azure ad Premium P2 (o EMS E5)](entitlement-management-overview.md#license-requirements).  
* Se si riceve un messaggio di accesso negato durante la creazione o la visualizzazione di pacchetti di accesso e si è un membro di un gruppo di autori del catalogo, è necessario creare un catalogo prima di creare il primo pacchetto di accesso.

## <a name="checklist-for-adding-a-resource"></a>Elenco di controllo per l'aggiunta di una risorsa

* Affinché un'applicazione sia una risorsa in un pacchetto di accesso, deve disporre di almeno un ruolo di risorsa che può essere assegnato. I ruoli vengono definiti dall'applicazione stessa e gestiti in Azure AD. Si noti che i portale di Azure possono anche visualizzare entità servizio per i servizi che non possono essere selezionati come applicazioni.  In particolare, **Exchange Online** e **SharePoint Online** sono servizi, non applicazioni con ruoli delle risorse nella directory, quindi non possono essere inclusi in un pacchetto di Access.  Usare invece le licenze basate sui gruppi per stabilire una licenza appropriata per un utente che deve accedere a tali servizi.

* Affinché un gruppo sia una risorsa in un pacchetto di accesso, deve essere in grado di essere modificabile in Azure AD.  I gruppi che hanno origine in un Active Directory locale non possono essere assegnati come risorse perché i relativi attributi proprietario o membro non possono essere modificati nel Azure AD.  

* Non è possibile aggiungere le raccolte documenti di SharePoint Online e i singoli documenti come risorse.  Al contrario, creare un gruppo di sicurezza Azure AD, includere il gruppo e un ruolo del sito nel pacchetto di accesso e in SharePoint Online utilizzare tale gruppo per controllare l'accesso alla raccolta documenti o al documento.

* Se sono presenti utenti che sono già stati assegnati a una risorsa che si vuole gestire con un pacchetto di accesso, assicurarsi che gli utenti siano assegnati al pacchetto di accesso con un criterio appropriato. Ad esempio, potrebbe essere necessario includere un gruppo in un pacchetto di accesso che dispone già di utenti nel gruppo. Se gli utenti del gruppo necessitano dell'accesso continuo, devono avere un criterio appropriato per i pacchetti di accesso in modo che non perdano l'accesso al gruppo. È possibile assegnare il pacchetto di accesso chiedendo agli utenti di richiedere il pacchetto di accesso contenente tale risorsa o di assegnarli direttamente al pacchetto di accesso. Per ulteriori informazioni, vedere [modifica e gestione di un pacchetto di accesso esistente](entitlement-management-access-package-edit.md).

## <a name="checklist-for-providing-external-users-access"></a>Elenco di controllo per fornire l'accesso a utenti esterni

* Se è presente un [elenco Consenti](../b2b/allow-deny-list.md)B2B, gli utenti le cui directory non sono consentite non saranno in grado di richiedere l'accesso.

* Assicurarsi che non siano presenti [criteri di accesso condizionale](../conditional-access/require-managed-devices.md) che impediscano a utenti esterni di richiedere l'accesso o di usare le applicazioni nei pacchetti di accesso.

## <a name="checklist-for-request-issues"></a>Elenco di controllo per problemi di richiesta

* Quando un utente desidera richiedere l'accesso a un pacchetto di accesso, assicurarsi che usi il **collegamento portale di accesso personale** per il pacchetto di accesso. Per altre informazioni, vedere [il collegamento copiare il portale di accesso](entitlement-management-access-package-edit.md#copy-my-access-portal-link).

* Quando un utente accede al portale di accesso personale per richiedere un pacchetto di accesso, assicurarsi di eseguire l'autenticazione usando il proprio account aziendale. L'account aziendale può essere un account nella directory delle risorse o in una directory inclusa in uno dei criteri del pacchetto di accesso. Se l'account dell'utente non è un account aziendale o la directory non è inclusa nel criterio, l'utente non visualizzerà il pacchetto di accesso. Per altre informazioni, vedere [richiedere l'accesso a un pacchetto di accesso](entitlement-management-request-access.md).

* Se un utente non accede alla directory delle risorse, non sarà in grado di richiedere l'accesso nel portale di accesso personale. Prima che l'utente possa richiedere l'accesso, è necessario rimuovere il blocco di accesso dal profilo dell'utente. Per rimuovere il blocco di accesso, nella portale di Azure fare clic su **Azure Active Directory**, fare clic su **utenti**, fare clic sull'utente e quindi su **profilo**. Modificare la sezione **Impostazioni** e modificare **Accedi** a **No**. Per altre informazioni, vedere [aggiungere o aggiornare le informazioni sul profilo di un utente usando Azure Active Directory](../fundamentals/active-directory-users-profile-azure-portal.md).  È anche possibile controllare se l'utente è stato bloccato a causa di un [criterio di Identity Protection](../identity-protection/howto-unblock-user.md).

* Se un utente è un richiedente e un responsabile approvazione, nel portale di accesso personale non visualizzerà la richiesta di un pacchetto di accesso nella pagina approvazioni. Questo comportamento è intenzionale, un utente non può approvare la propria richiesta. Verificare che il pacchetto di accesso richiesto disponga di responsabili approvazione aggiuntivi configurati nei criteri. Per altre informazioni, vedere [modificare un criterio esistente](entitlement-management-access-package-edit.md#edit-an-existing-policy).

* Se un nuovo utente esterno, che non ha precedentemente eseguito l'accesso alla directory, riceve un pacchetto di accesso che include un sito di SharePoint Online, il relativo pacchetto di accesso verrà visualizzato come non completamente recapitato fino a quando non viene eseguito il provisioning dell'account in SharePoint Online.

## <a name="next-steps"></a>Passaggi successivi

- [Visualizzare i report relativi al modo in cui gli utenti hanno accesso alla gestione dei diritti](entitlement-management-reports.md)
