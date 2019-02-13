---
title: Aggiungere un nuovo account tenant di Azure Stack in Azure Active Directory | Microsoft Docs
description: Dopo aver distribuito Microsoft Azure Stack Development Kit, è necessario creare almeno un account utente tenant in modo da poter esplorare il portale tenant.
services: azure-stack
documentationcenter: ''
author: patricka
manager: femila
editor: ''
ms.assetid: a75d5c88-5b9e-4e9a-a6e3-48bbfa7069a7
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2019
ms.author: patricka
ms.reviewer: unknown
ms.lastreviewed: 09/17/2018
ms.openlocfilehash: 5c07288bbfbf70be62723f835192cf09d92166ab
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2019
ms.locfileid: "56163224"
---
# <a name="add-a-new-azure-stack-tenant-account-in-azure-active-directory"></a>Aggiungere un nuovo account tenant di Azure Stack in Azure Active Directory

Dopo aver [distribuzione di Azure Stack Development Kit](azure-stack-run-powershell-script.md), è necessario un account utente di tenant in modo da poter esplorare il portale tenant e testare i piani e offerte. È possibile creare un account tenant dal [usando il portale di Azure](#create-an-azure-stack-tenant-account-using-the-azure-portal) o tramite [usando PowerShell](#create-an-azure-stack-tenant-account-using-powershell).

## <a name="create-an-azure-stack-tenant-account-using-the-azure-portal"></a>Creare un account tenant di Azure Stack usando il portale di Azure

Per usare il portale di Azure, è necessario disporre di una sottoscrizione di Azure.

1. Accedere al [Azure](https://portal.azure.com).
2. Nella barra di spostamento a sinistra, selezionare **Active Directory** e passare alla directory in cui si vuole usare per Azure Stack o crearne uno nuovo.
3. Selezionare **Azure Active Directory** > **utenti** > **nuovo utente**.

    ![Pagina Utenti - Tutti gli utenti con il comando Nuovo utente evidenziato](media/azure-stack-add-new-user-aad/new-user-all-users.png)

4. Nella pagina **Utente** inserire le informazioni richieste.

    ![Aggiunta di un nuovo utente, pagina Utente con le informazioni sull'utente](media/azure-stack-add-new-user-aad/new-user-user.png)

    - **Nome (obbligatorio).** Nome e cognome del nuovo utente. Ad esempio, Mary Parker.
    - **Nome utente (obbligatorio).** Nome utente del nuovo utente. Ad esempio: mary@contoso.com.
        La parte relativa al dominio del nome utente deve usare il nome di dominio predefinito iniziale, <_nomedominio_>.onmicrosoft.com o un nome di dominio personalizzato, ad esempio contoso.com. Per altre informazioni su come creare un nome di dominio personalizzato, vedere [Come aggiungere un nome di dominio personalizzato ad Azure Active Directory](../active-directory/fundamentals/add-custom-domain.md).
    - **Profilo.** Facoltativamente, è possibile aggiungere altre informazioni sull'utente. È anche possibile aggiungere informazioni sull'utente in un secondo momento. Per altre informazioni sull'aggiunta di informazioni sull'utente, vedere [Come aggiungere o modificare informazioni di un profilo utente](../active-directory/fundamentals/active-directory-users-profile-azure-portal.md).
    - **Ruolo directory.**  Scegli **utente**.

5. Controllare **Show Password** e copiare la password generata automaticamente disponibili nel **Password** casella. Questa password sarà necessaria per il processo di accesso iniziale.

6. Selezionare **Create**.

    L'utente viene creato e aggiunto al tenant di Azure AD.

7. Accedere al portale di Microsoft Azure con il nuovo account. Modificare la password quando viene richiesto.
8. Accedi a `https://portal.local.azurestack.external` con il nuovo account per visualizzare il portale tenant.

## <a name="create-an-azure-stack-user-account-using-powershell"></a>Creare un account utente di Azure Stack usando PowerShell

Se non hai una sottoscrizione di Azure, non è possibile usare il portale di Azure per aggiungere un account utente di tenant. In questo caso, è possibile usare invece il modulo Azure Active Directory per Windows PowerShell.

> [!NOTE]
> Se si usa l'Account Microsoft (Live ID) per distribuire Azure Stack Development Kit, è possibile utilizzare AAD PowerShell per creare account tenant. 

1. Installare l' [assistente per l'accesso a Microsoft Online Services per professionisti IT RTW](https://www.microsoft.com/en-us/download/details.aspx?id=41950).
2. Installare il [modulo di Azure Active Directory per Windows PowerShell (versione a 64 bit)](https://go.microsoft.com/fwlink/p/?linkid=236297) e aprirlo.
3. Eseguire i cmdlet seguenti:

    ```powershell
    # Provide the AAD credential you use to deploy Azure Stack Development Kit

            $msolcred = get-credential

    # Add a tenant account "Tenant Admin <username>@<yourdomainname>" with the initial password "<password>".

            connect-msolservice -credential $msolcred
            $user = new-msoluser -DisplayName "Tenant Admin" -UserPrincipalName <username>@<yourdomainname> -Password <password>
            Add-MsolRoleMember -RoleName "Company Administrator" -RoleMemberType User -RoleMemberObjectId $user.ObjectId

    ```

1. Accedi a Microsoft Azure con il nuovo account. Modificare la password quando viene richiesto.
2. Accedi a `https://portal.local.azurestack.external` con il nuovo account per visualizzare il portale tenant.

## <a name="next-steps"></a>Passaggi successivi

[Aggiungere utenti di Azure Stack in AD FS](azure-stack-add-users-adfs.md)