---
title: Aggiungere un nuovo account tenant di Azure Stack in Azure Active Directory | Documenti Microsoft
description: "Dopo la distribuzione di Microsoft Azure Stack Development Kit, è necessario creare account utente di almeno un tenant in modo è possibile esplorare il portale tenant."
services: azure-stack
documentationcenter: 
author: heathl17
manager: byronr
editor: 
ms.assetid: a75d5c88-5b9e-4e9a-a6e3-48bbfa7069a7
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: helaw
ms.openlocfilehash: b7fd3c36825746a009c01c97fb8664e04278159f
ms.sourcegitcommit: 094061b19b0a707eace42ae47f39d7a666364d58
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2017
---
*Si applica a: Azure Stack Development Kit*

# <a name="add-a-new-azure-stack-tenant-account-in-azure-active-directory"></a>Aggiungere un nuovo account tenant di Azure Stack in Azure Active Directory
Dopo aver [distribuzione il Kit di sviluppo di Azure Stack](azure-stack-run-powershell-script.md), è necessario un account utente di tenant, pertanto è possibile esplorare il portale tenant e le offerte e piani di test. È possibile creare un account tenant da [tramite il portale di Azure](#create-an-azure-stack-tenant-account-using-the-azure-portal) o [tramite PowerShell](#create-an-azure-stack-tenant-account-using-powershell).

## <a name="create-an-azure-stack-tenant-account-using-the-azure-portal"></a>Creare un account tenant di Azure Stack usando il portale di Azure
Per usare il portale di Azure, è necessario disporre di una sottoscrizione di Azure.

1. Accedere ad [Azure](https://portal.azure.com).
2. Nella barra di spostamento sinistra di Microsoft Azure fare clic su **Active Directory**.
3. Nell'elenco di directory fare clic sulla directory che si vuole usare per Azure Stack o crearne una nuova.
4. In questa pagina della directory fare clic su **Utenti**.
5. Fare clic su **Add User**.
6. Nel **Aggiungi utente** procedura guidata di **tipo di utente** scegliere **nuovo utente nell'organizzazione**.
7. Nella casella **Nome utente** digitare un nome per l'utente.
8. Nella procedura guidata per l' **@** scegliere la voce appropriata.
9. Fare clic sulla freccia avanti.
10. Nel **profilo utente** pagina della procedura guidata, digitare un **nome**, **cognome**, e **nome visualizzato**.
11. Nel **ruolo** scegliere **utente**.
12. Fare clic sulla freccia avanti.
13. Nel **Ottieni password temporanea** pagina, fare clic su **crea**.
14. Copiare la **nuova password**.
15. Accedere a Microsoft Azure con il nuovo account. Modificare la password quando viene richiesto.
16. Accedere a `https://portal.local.azurestack.external` con il nuovo account per visualizzare il portale tenant.

## <a name="create-an-azure-stack-tenant-account-using-powershell"></a>Creare un account tenant di Azure Stack usando PowerShell
Se non si dispone di una sottoscrizione di Azure, è possibile utilizzare il portale di Azure per aggiungere un account utente di tenant. In questo caso, è possibile utilizzare invece il modulo di Azure Active Directory per Windows PowerShell.

> [!NOTE]
> Se si utilizza l'Account Microsoft (Live ID) per distribuire il Kit di sviluppo dello Stack di Azure, è possibile utilizzare PowerShell per Azure ad per creare account tenant. 
> 
> 

1. Installare il [Microsoft Online Services Assistente per l'accesso per i professionisti IT RTW](https://www.microsoft.com/en-us/download/details.aspx?id=41950).
2. Installare il [modulo di Azure Active Directory per Windows PowerShell (versione a 64 bit)](http://go.microsoft.com/fwlink/p/?linkid=236297) e aprirlo.
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

