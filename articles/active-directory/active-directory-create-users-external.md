---
title: "Aggiungere utenti da altre directory o società partner in Azure Active Directory | Documentazione Microsoft"
description: Illustra come aggiungere utenti o modificare le informazioni sugli utenti in Azure Active Directory, inclusi gli utenti esterni e guest.
services: active-directory
documentationcenter: 
author: LizCasey
manager: femila
editor: 
ms.assetid: 564a04ec-53c1-470b-9ab9-f3db57da0a89
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/10/2017
ms.author: curtand
ms.custom: oldportal;it-pro;
robots: NOINDEX
ms.openlocfilehash: c2e748d5b7ce958600d4da549bc4f2b085df83d8
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/31/2017
---
# <a name="add-users-from-other-directories-or-partner-companies-in-azure-active-directory"></a>Aggiungere utenti da altre directory o società partner in Azure Active Directory

Questo articolo illustra come aggiungere utenti da altre directory in Azure Active Directory o aggiungere utenti da società partner. Per informazioni sull'aggiunta di nuovi utenti nell'organizzazione e di utenti che possiedono account Microsoft, vedere [Aggiungere nuovi utenti o utenti con account Microsoft in Azure Active Directory](active-directory-create-users.md). 

> [!IMPORTANT]
> Microsoft consiglia di gestire Azure AD usando l'[interfaccia di amministrazione di Azure AD](https://aad.portal.azure.com) nel portale di Azure invece di usare il portale di Azure classico citato nel presente articolo. Per informazioni su come aggiungere utenti guest di Collaborazione B2B nell'interfaccia di amministrazione di Azure AD, vedere [Informazioni su Collaborazione B2B di Azure AD](active-directory-b2b-what-is-azure-ad-b2b.md).

Gli utenti aggiunti non hanno autorizzazioni di amministratore per impostazione predefinita, ma è possibile assegnare loro dei ruoli in qualsiasi momento.

## <a name="add-a-user"></a>Aggiungere un utente
1. Accedere al [portale di Azure classico](https://manage.windowsazure.com) con un account di amministratore globale per la directory.
2. Selezionare **Active Directory**e quindi aprire la directory.
3. Selezionare la scheda **Utenti** e quindi **Aggiungi utente** nella barra dei comandi.
4. Nella pagina **Informazioni sull'utente** selezionare in **Tipo di utente** una delle opzioni seguenti.

   * **Utente in un'altra directory di Microsoft Azure AD** : consente di aggiungere alla directory un account utente originato da un'altra directory di Azure AD. È possibile selezionare un utente in un'altra directory solo se si è membri di tale directory.
   * **Utenti nelle società partner**: consente di invitare e autorizzare utenti di società partner nella propria directory. Vedere [Collaborazione B2B di Azure Active Directory](active-directory-b2b-what-is-azure-ad-b2b.md). È necessario [caricare un file CSV che specifica gli indirizzi di posta elettronica](active-directory-b2b-references-csv-file-format.md).
5. Nella pagina **Profilo** dell'utente specificare nome e cognome, un nome descrittivo e un ruolo utente nell'elenco **Ruoli**. Per altre informazioni sui ruoli utente e di amministratore, vedere [Assegnazione dei ruoli di amministratore in Azure Active Directory](active-directory-assign-admin-roles.md). Specificare eventualmente **Abilita Multi-Factor Authentication** per l'utente.
6. Nella pagina **Ottieni password temporanea** selezionare **Crea**.

> [!IMPORTANT]
> Se l'organizzazione usa più di un dominio, è opportuno essere a conoscenza dei problemi seguenti quando si aggiunge un account utente:
>
> * Per aggiungere account utente con lo stesso nome dell'entità utente (UPN) in tutti i domini, aggiungere **prima** geoffgrisso@contoso.onmicrosoft.com, ad esempio, **seguito da** geoffgrisso@contoso.com.
> * **Non** aggiungere geoffgrisso@contoso.com prima di aggiungere geoffgrisso@contoso.onmicrosoft.com.
>

Se si modificano le informazioni per un utente la cui identità è sincronizzata con il servizio di Active Directory locale, non sarà possibile modificare le informazioni sull'utente nel portale di Azure classico. Per modificare le informazioni sull'utente, usare gli strumenti di gestione del servizio Active Directory locale.

## <a name="add-external-users"></a>Aggiungere utenti esterni
È anche possibile aggiungere utenti da un'altra directory di Azure AD di appartenenza o dalle società partner caricando un file CSV. Per aggiungere un utente esterno, in **Tipo di utente** specificare **Utente in un'altra directory di Microsoft Azure AD** o **Utenti nelle società partner**.

Gli utenti di questi due tipi sono originati da un'altra directory e vengono aggiunti come **utenti esterni**. Gli utenti esterni possono collaborare con altri utenti in una directory senza la necessità di aggiungere nuovi account e credenziali. Al momento dell'accesso, gli utenti esterni si autenticano con la propria home directory e l'autenticazione è valida per tutte le altre directory a cui sono stati aggiunti.

## <a name="external-user-management-and-limitations"></a>Gestione e limiti dell'utente esterno
Quando si aggiunge un utente da un'altra directory alla propria directory, tale utente viene considerato un utente esterno nella propria directory. Il nome visualizzato e il nome utente vengono copiati dalla relativa home directory e usati per l'utente esterno nella propria directory. Da questo momento, le proprietà dell'account utente esterno sono completamente indipendenti. Le eventuali modifiche apportate alle proprietà dell'utente nella relativa home directory non vengono propagate all'account utente esterno nella propria directory.

L'unico collegamento tra i due account consiste nel fatto che l'utente esegue sempre l'autenticazione nella propria home directory o con il proprio account Microsoft. Per questo motivo, per gli utenti esterni non sono disponibili opzioni per reimpostare o per abilitare l'autenticazione a più fattori. Attualmente, i criteri di autenticazione della home directory o dell'account Microsoft sono gli unici criteri che vengono valutati quando l'utente esegue l'accesso.

> [!NOTE]
> È comunque possibile disabilitare l'utente esterno nella directory, con il conseguente blocco dell'accesso alla propria directory.
>
>

Se un utente viene eliminato nella home directory o se viene annullato il relativo account Microsoft, l'utente esterno continua a esistere nella propria directory, ma non può accedere alle risorse nella directory, perché non è più autorizzato a effettuare l'autenticazione con la home directory o l'account Microsoft.

### <a name="services-that-currently-support-access-by-azure-ad-external-users"></a>Servizi che attualmente supportano l'accesso da parte di utenti esterni di Azure AD
* **Portale di Azure classico**: consente a un utente esterno con il ruolo di amministratore di più directory di gestire ognuna di esse.
* **SharePoint Online**: se la condivisione esterna è abilitata, consente a un utente esterno di accedere alle risorse autorizzate di SharePoint Online.
* **Dynamics CRM**: se all'utente è stata concessa una licenza tramite PowerShell, consente a un utente esterno di accedere alle risorse autorizzate di Dynamics CRM.
* **Dynamics AX**: se all'utente è stata concessa una licenza tramite PowerShell, consente a un utente esterno di accedere alle risorse autorizzate di Dynamics AX. Le limitazioni per gli [utenti esterni di Azure AD](#known-limitations-of-azure-ad-external-users) si applicano anche agli utenti esterni di Dynamics AX.

## <a name="next-steps"></a>Passaggi successivi
* [Aggiungere o modificare utenti in Azure Active Directory](active-directory-create-users.md)
* [Amministrazione di Azure AD](active-directory-administer.md)
* [Gestire password in Azure AD](active-directory-manage-passwords.md)
* [Gestire gruppi in Azure AD](active-directory-manage-groups.md)
