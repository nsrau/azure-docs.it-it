---
title: Aggiungere o eliminare utenti in Azure Active Directory | Microsoft Docs
description: Viene illustrato come aggiungere nuovi utenti o eliminare utenti esistenti in Azure Active Directory
services: active-directory
documentationcenter: 
author: curtand
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2017
ms.author: curtand
ms.reviewer: jeffsta
ms.custom: it-pro
ms.openlocfilehash: 3df0324cae65418f35743b6543753c405dee01d0
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2017
---
# <a name="quickstart-add-new-users-to-azure-active-directory"></a>Guida introduttiva: Aggiungere nuovi utenti ad anteprima di Azure Active Directory
In questo articolo viene illustrato come aggiungere nuovi utenti dell'organizzazione in Azure Active Directory (Azure AD) uno per volta, utilizzando il portale di Azure o sincronizzando i dati dell'account utente Windows Server Active Directory locale. 

## <a name="add-cloud-based-users"></a>Aggiungere gli utenti basati su cloud
1. Accedere all'[interfaccia di amministrazione di Azure Active Directory](https://aad.portal.azure.com) con un account di amministratore globale per la directory del tenant.
2. Selezionare **Azure Active Directory** e quindi **Utenti e gruppi**.
3. In **Utenti e gruppi** selezionare **Tutti gli utenti** e quindi **Nuovo utente**.
   ![Selezione del comando Aggiungi](./media/add-users-azure-active-directory/add-user.png)
4. Immettere dettagli per gli utenti, ad esempio **nome** e **nome utente**. La parte del nome di dominio del nome utente deve essere il nome di dominio "[nome dominio]" del nome di dominio predefinito iniziale o un nome di dominio verificato, non federato, [personalizzato, ](add-custom-domain.md)ad esempio "contoso.com".
5. Copiare o comunque annotare la password generata in modo da poterla fornire all'utente al termine del processo.
6. Facoltativamente, è possibile aprire e immettere le informazioni in **Profilo**, **Gruppi** o **Ruolo della directory** per l'utente. Per altre informazioni sui ruoli utente e di amministratore, vedere [Assegnazione dei ruoli di amministratore in Azure Active Directory](active-directory-assign-admin-roles.md).
7. In **Utente** selezionare **Crea**.
8. Distribuire in modo sicuro la password generata al nuovo utente per consentirgli l'accesso.

> [!TIP]
> È inoltre possibile sincronizzare i dati dell'account utente locale Windows Server Active Directory. Le soluzioni di gestione delle identità di Microsoft abbracciano funzionalità locali e basate sul cloud, creando una singola identità utente per l’autenticazione e l’autorizzazione per tutte le risorse, indipendentemente dalla loro ubicazione. Tutto questo viene chiamato Identità ibrida. È possibile utilizzare [Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect)per integrare le directory locali con Azure Active Directory per gli scenari con identità ibrida. Consente quindi di fornire agli utenti un'identità comune per le applicazioni di Office 365, Azure e SaaS integrate con Azure AD. 

## <a name="delete-users-from-azure-ad"></a>Eliminare gli utenti da Azure Active Directory
1. Accedere all'[interfaccia di amministrazione di Azure Active Directory](https://aad.portal.azure.com) con un account di amministratore globale per la directory del tenant.
2. Selezionare **Utenti e gruppi**.
3. Nel pannello **Utenti e gruppi**, selezionare l'utente da eliminare dall'elenco. 
4. Nel pannello dell'utente selezionare **Panoramica**, quindi nella barra dei comandi selezionare **Elimina**.
   ![Selezione del comando Aggiungi](./media/add-users-azure-active-directory/delete-user.png)


### <a name="learn-more"></a>Altre informazioni 
* [Aggiungere utenti guest da un'altra directory](active-directory-b2b-what-is-azure-ad-b2b.md) 

* [Assegnare un utente a un ruolo in Azure AD](active-directory-users-assign-role-azure-portal.md)

## <a name="next-steps"></a>Passaggi successivi
In questa guida introduttiva si è appreso come aggiungere nuovi utenti ad Azure AD Premium. 

È possibile usare il collegamento seguente per creare un nuovo utente in Azure AD dal portale di Azure.

> [!div class="nextstepaction"]
> [Aggiungere utenti ad Azure AD](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/All users) 