---
title: Aggiungere un dominio personalizzato - Azure Active Directory | Microsoft Docs
description: Istruzioni su come aggiungere un dominio personalizzato usando il portale di Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: ajburnle
ms.reviewer: elkuzmen
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: de91bd7e1e4c5f9909213f663dd3ede0f979d4de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262152"
---
# <a name="add-your-custom-domain-name-using-the-azure-active-directory-portal"></a>Aggiungere un nome di dominio personalizzato usando il portale di Azure Active Directory

Ogni nuovo tenant di Azure AD viene fornito con un nome di dominio iniziale, * \<nomedominio>.onmicrosoft.com*. Non è possibile modificare o eliminare il nome di dominio iniziale, ma è possibile aggiungere i nomi dell'organizzazione. L'aggiunta di nomi di dominio personalizzati consente di creare nomi utente familiari agli utenti, ad esempio *alain\@contoso.com*.

## <a name="before-you-begin"></a>Prima di iniziare

Prima di poter aggiungere un nome di dominio personalizzato, crea il tuo nome di dominio con un registrar. Per un registrar accreditato, vedere [ICANN-Accredited Registrars](https://www.icann.org/registrar-reports/accredited-list.html) (Registrar accreditati da ICANN).

## <a name="create-your-directory-in-azure-ad"></a>Creare la directory in Azure AD

Dopo avere ottenuto il nome di dominio, è possibile creare la prima directory di Azure AD. Accedere al portale di Azure per la directory usando un account con il ruolo **Proprietario** per la sottoscrizione.

Creare la nuova directory seguendo i passaggi illustrati in [Create a new tenant for your organization](active-directory-access-create-new-tenant.md#create-a-new-tenant-for-your-organization) (Creare un nuovo tenant per l'organizzazione).

>[!IMPORTANT]
>La persona che crea il tenant ne diventa automaticamente l'amministratore globale. L'amministratore globale può aggiungere altri amministratori al tenant.

Per altre informazioni sui ruoli di sottoscrizione, vedere Ruoli rbac di Azure.For more information about subscription roles, see [Azure RBAC roles.](../../role-based-access-control/rbac-and-directory-admin-roles.md#azure-rbac-roles)

>[!TIP]
> Se si prevede di federare Windows Server AD locale con Azure AD, è necessario selezionare Si prevede di **configurare questo dominio per l'accesso Single Sign-On con Active Directory locale** quando si esegue lo strumento Azure AD Connect per sincronizzare le directory.
>
> È anche necessario registrare lo stesso nome di dominio selezionato per la federazione con la directory locale nel passaggio **Dominio di Azure AD** della procedura guidata. Per verificare l'aspetto della configurazione, vedere Verificare il dominio di [Azure AD selezionato per la federazione.](../hybrid/how-to-connect-install-custom.md#verify-the-azure-ad-domain-selected-for-federation) Se non si dispone dello strumento Azure AD Connect, è possibile [scaricarlo qui.](https://go.microsoft.com/fwlink/?LinkId=615771)

## <a name="add-your-custom-domain-name-to-azure-ad"></a>Aggiungere un nome di dominio personalizzato ad Azure AD

Dopo aver creato la directory, è possibile aggiungere il nome di dominio personalizzato.

1. Accedere al [portale di Azure](https://portal.azure.com/) con un account amministratore globale per la directory.

1. Cercare e selezionare *Azure Active Directory* da qualsiasi pagina. Selezionare quindi **Nomi** > di dominio personalizzati**Aggiungere un dominio personalizzato**.

    ![Pagina Nomi di dominio personalizzati, con mostrato Aggiungi dominio personalizzato](media/add-custom-domain/add-custom-domain.png)

1. In **Nome dominio personalizzato**immettere il nuovo nome dell'organizzazione, in questo esempio *contoso.com*. Selezionare **Add domain** (Aggiungi dominio).

    ![Pagina Nomi di dominio personalizzati, con la pagina Aggiungi dominio personalizzato](media/add-custom-domain/add-custom-domain-blade.png)

    >[!IMPORTANT]
    >Per il corretto funzionamento, è necessario includere *.com,* *.net*o qualsiasi altra estensione di primo livello.

    Viene aggiunto il dominio non verificato. Viene visualizzata la pagina **contoso.com** che mostra le informazioni sul DNS. Salvare queste informazioni. È necessario che sia necessario in un secondo momento per creare un record TXT per configurare DNS.

    ![Pagina Contoso con le informazioni relative alla voce DNS](media/add-custom-domain/contoso-blade-with-dns-info.png)

## <a name="add-your-dns-information-to-the-domain-registrar"></a>Aggiungere le informazioni sul DNS al registrar

Dopo aver aggiunto il nome di dominio personalizzato ad Azure AD, è necessario tornare al registrar e aggiungere le informazioni sul DNS di Azure AD dal file TXT copiato. La creazione di questo record TXT per il dominio verifica la proprietà del nome di dominio.

Torna al registrar e crea un nuovo record TXT per il tuo dominio in base alle informazioni DNS copiate. Impostare il tempo di durata (TTL) su 3600 secondi (60 minuti) e quindi salvare il record.

>[!IMPORTANT]
>È possibile registrare tutti i nomi di dominio desiderati. Tuttavia, ogni dominio ottiene il proprio record TXT da Azure AD. Prestare attenzione quando si inseriscono le informazioni sul file TXT presso il registrar. Se inserisci le informazioni errate o duplicate per errore, dovrai attendere il timeout del TTL (60 minuti) prima di poter riprovare.

## <a name="verify-your-custom-domain-name"></a>Verificare il nome di dominio personalizzato

Dopo aver registrato il nome di dominio personalizzato, assicurarsi che sia valido in Azure AD. La propagazione dal registrar di dominio ad Azure AD può essere istantanea o può richiedere alcuni giorni, a seconda del registrar.

Per verificare il nome di dominio personalizzato, attenersi alla seguente procedura:

1. Accedere al [portale di Azure](https://portal.azure.com/) con un account amministratore globale per la directory.

1. Cercare e selezionare *Azure Active Directory* da qualsiasi pagina, quindi selezionare Nomi di dominio **personalizzati**.

1. In **Nomi di dominio personalizzati**selezionare il nome di dominio personalizzato. In questo esempio, selezionare **contoso.com**.

    ![Pagina Fabrikam - Nomi di dominio personalizzati, con contoso evidenziato](media/add-custom-domain/custom-blade-with-contoso-highlighted.png)

1. Nella pagina **contoso.com** **selezionare Verifica** per assicurarsi che il dominio personalizzato sia registrato correttamente e sia valido per Azure AD.

    ![Pagina Contoso con le informazioni relative alla voce DNS e pulsante Verifica](media/add-custom-domain/contoso-blade-with-dns-info-verify.png)

Dopo aver verificato il nome di dominio personalizzato, puoi eliminare il file TXT o MX di verifica.

## <a name="common-verification-issues"></a>Problemi comuni di verifica

Se Azure AD non riesce a verificare un nome di dominio personalizzato, provare a mettere in pratica i suggerimenti seguenti:

- **Attendere almeno un'ora e riprovare**. I record DNS devono essere propagati prima che Azure AD possa verificare il dominio. Questo processo può richiedere più di un'ora.

- **Verificare che il record DNS sia corretto.** Tornare al sito del registrar del nome di dominio. Assicurarsi che la voce sia presente e che corrisponda alle informazioni sulla voce DNS fornite da Azure AD.

  Se non è possibile aggiornare il record nel sito del registrar, condividere la voce con un utente che dispone delle autorizzazioni per aggiungere la voce e verificare che sia corretta.

- **Verificare che il nome di dominio non sia già usato in un'altra directory.** Un nome di dominio può essere verificato solo in una directory. Se il tuo nome di dominio è attualmente verificato in un'altra directory, non può essere verificato anche nella nuova directory. Per risolvere questo problema di duplicazione, è necessario eliminare il nome di dominio dalla directory precedente. Per altre informazioni sull'eliminazione dei nomi di dominio, vedere [Gestire i nomi di dominio personalizzati](../users-groups-roles/domains-manage.md).

- **Assicurarsi che non vi siano tenant di Power BI non gestiti.** Se gli utenti hanno attivato Power BI con l'iscrizione self-service e creato un tenant non gestito per l'organizzazione, è necessario assumere la gestione come amministratore all'interno o esterno, usando PowerShell. Per altre informazioni, vedere [Acquisire la proprietà di una directory non gestita come amministratore in Azure Active Directory](../users-groups-roles/domains-admin-takeover.md).

## <a name="next-steps"></a>Passaggi successivi

- Aggiungere un altro amministratore globale alla directory. Per altre informazioni, vedere [Come assegnare ruoli e amministratori](active-directory-users-assign-role-azure-portal.md).

- Aggiungere utenti al dominio. Per ulteriori informazioni, vedere [Come aggiungere o eliminare utenti](add-users-azure-active-directory.md).

- Gestire le informazioni sul nome di dominio in Azure AD. Per altre informazioni, vedere [Gestione dei nomi di dominio personalizzati](../users-groups-roles/domains-manage.md).

- Se sono disponibili versioni locali di Windows Server che si vuole usare con Azure Active Directory, vedere [Integrare le directory locali con Azure Active Directory](../connect/active-directory-aadconnect.md).
