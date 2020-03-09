---
title: Esercitazione`:` usare l'identità gestita per accedere Azure Resource Manager-Windows-Azure AD
description: Un'esercitazione che illustra il processo di utilizzo di un'identità gestita assegnata dal sistema di macchine virtuali Windows per accedere Azure Resource Manager.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: daveba
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/20/2017
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4431031e5e96c71c6488b57cc570271d763bb764
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78372454"
---
# <a name="use-a-windows-vm-system-assigned-managed-identity-to-access-resource-manager"></a>Usare un'identità gestita assegnata dal sistema VM Windows per accedere a Gestione risorse

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Questa Guida introduttiva illustra come accedere all'API Azure Resource Manager usando una macchina virtuale Windows con identità gestita assegnata dal sistema abilitata. Le identità gestite per le risorse di Azure vengono gestite automaticamente da Azure e consentono di eseguire l'autenticazione ai servizi che supportano l'autenticazione Azure AD senza la necessità di inserire le credenziali nel codice. Si apprenderà come:

> [!div class="checklist"] 
> * Concedere alla macchina virtuale l'accesso a un gruppo di risorse in Azure Resource Manager 
> * Ottenere un token di accesso usando l'identità della VM e usarlo per chiamare Azure Resource Manager

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="grant-your-vm-access-to-a-resource-group-in-resource-manager"></a>Concedere alla macchina virtuale l'accesso a un gruppo di risorse in Gestione risorse
Usando le identità gestite per le risorse di Azure, il codice può ottenere i token di accesso per l'autenticazione a risorse che supportano l'autenticazione Azure AD.  Il Azure Resource Manager supporta l'autenticazione Azure AD.  In primo luogo, è necessario concedere all'identità gestita assegnata dal sistema di questa macchina virtuale l'accesso a una risorsa in Gestione risorse, in questo caso il gruppo di risorse in cui è contenuta la macchina virtuale.  

1.  Passare alla scheda relativa ai **gruppi di risorse**. 
2.  Selezionare il **gruppo di risorse** specifico creato per la **macchina virtuale Windows**. 
3.  Passare a **controllo di accesso (IAM)** nel pannello di sinistra. 
4.  Aggiungere quindi un' **assegnazione di ruolo** a una nuova assegnazione di ruolo per la **macchina virtuale Windows**.  Scegliere **Role** As **Reader**. 
5.  Nell'elenco a discesa successivo assegnare l' **accesso alla** **macchina virtuale**delle risorse. 
6.  Assicurarsi quindi che la sottoscrizione appropriata sia elencata nell'elenco a discesa **sottoscrizione** . Per **gruppo di risorse**selezionare **tutti i gruppi di risorse**. 
7.  Infine, in **selezionare** scegliere la macchina virtuale Windows nell'elenco a discesa e fare clic su **Salva**.

    ![Testo immagine ALT](media/msi-tutorial-windows-vm-access-arm/msi-windows-permissions.png)

## <a name="get-an-access-token-using-the-vms-system-assigned-managed-identity-and-use-it-to-call-azure-resource-manager"></a>Ottenere un token di accesso usando l'identità gestita assegnata dal sistema della macchina virtuale e usarlo per chiamare Azure Resource Manager 

In questa parte sarà necessario usare **PowerShell** .  Se **PowerShell** non è installato, scaricarlo [qui](https://docs.microsoft.com/powershell/azure/overview). 

1.  Nel portale passare a **macchine virtuali** e passare alla macchina virtuale Windows e in **Panoramica**fare clic su **Connetti**. 
2.  Immettere il **nome utente** e la **password** per i quali è stato aggiunto al momento della creazione della macchina virtuale Windows. 
3.  Ora che è stata creata una **Connessione desktop remoto** con la macchina virtuale, aprire **PowerShell** nella sessione remota. 
4.  Usando il cmdlet Invoke-WebRequest, effettuare una richiesta all'identità gestita locale per l'endpoint delle risorse di Azure per ottenere un token di accesso per Azure Resource Manager.

    ```powershell
       $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://management.azure.com/' -Method GET -Headers @{Metadata="true"}
    ```
    
    > [!NOTE]
    > Il valore del parametro "Resource" deve corrispondere esattamente a quello previsto dal Azure AD. Quando si usa l'ID risorsa Azure Resource Manager, è necessario includere la barra finale nell'URI.
    
    Estrarre quindi la risposta completa, archiviata come stringa in formato JavaScript Object Notation (JSON) nell'oggetto $response. 
    
    ```powershell
    $content = $response.Content | ConvertFrom-Json
    ```
    Estrarre quindi il token di accesso dalla risposta.
    
    ```powershell
    $ArmToken = $content.access_token
    ```
    
    Infine, chiamare Azure Resource Manager usando il token di accesso. In questo esempio viene usato anche il cmdlet Invoke-WebRequest per effettuare la chiamata a Azure Resource Manager e includere il token di accesso nell'intestazione dell'autorizzazione.
    
    ```powershell
    (Invoke-WebRequest -Uri https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>?api-version=2016-06-01 -Method GET -ContentType "application/json" -Headers @{ Authorization ="Bearer $ArmToken"}).content
    ```
    > [!NOTE] 
    > L'URL fa distinzione tra maiuscole e minuscole. Assicurarsi quindi di usare esattamente lo stesso caso usato in precedenza quando è stato denominato il gruppo di risorse e la "G" maiuscola in "resourceGroups".
        
    Il comando seguente restituisce i dettagli del gruppo di risorse:

    ```powershell
    {"id":"/subscriptions/98f51385-2edc-4b79-bed9-7718de4cb861/resourceGroups/DevTest","name":"DevTest","location":"westus","properties":{"provisioningState":"Succeeded"}}
    ```

## <a name="next-steps"></a>Passaggi successivi

In questa Guida introduttiva si è appreso come usare un'identità gestita assegnata dal sistema per accedere all'API Azure Resource Manager.  Per ulteriori informazioni su Azure Resource Manager vedere:

> [!div class="nextstepaction"]
>[Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview)

