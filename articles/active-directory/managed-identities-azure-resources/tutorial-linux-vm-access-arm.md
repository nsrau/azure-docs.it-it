---
title: Usare un'identità gestita assegnata dal sistema per una macchina virtuale Linux per accedere ad Azure Resource Manager
description: Guida introduttiva che illustra come usare un'identità gestita assegnata dal sistema per una macchina virtuale Linux per accedere ad Azure Resource Manager.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: bryanla
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/20/2017
ms.author: daveba
ms.openlocfilehash: 79aacd7160dd4e794681e9b182236d6946baf3b8
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2018
ms.locfileid: "47107235"
---
# <a name="use-a-linux-vm-system-assigned-managed-identity-to-access-azure-resource-manager"></a>Usare un'identità gestita assegnata dal sistema per una macchina virtuale Linux per accedere ad Azure Resource Manager

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Questa guida introduttiva illustra come usare un'identità assegnata dal sistema per una macchina virtuale Linux per accedere all'API di Azure Resource Manager. Le identità gestite per le risorse di Azure vengono gestite automaticamente da Azure e consentono di eseguire l'autenticazione ai servizi che supportano l'autenticazione di Azure AD senza la necessità di inserire le credenziali nel codice. Si apprenderà come:

> [!div class="checklist"]
> * Concedere alla macchina virtuale l'accesso a un gruppo di risorse in Azure Resource Manager 
> * Ottenere un token di accesso usando l'identità della macchina virtuale e usarlo per chiamare Azure Resource Manager 

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

- [Accedere al portale di Azure](https://portal.azure.com)

- [Creare una macchina virtuale Linux](/azure/virtual-machines/linux/quick-create-portal)

- [Abilitare l'identità gestita assegnata dal sistema nella macchina virtuale](/azure/active-directory/managed-service-identity/qs-configure-portal-windows-vm#enable-system-assigned-identity-on-an-existing-vm)

## <a name="grant-your-vm-access-to-a-resource-group-in-azure-resource-manager"></a>Concedere alla macchina virtuale l'accesso a un gruppo di risorse in Azure Resource Manager 

Usando le identità gestite per le risorse di Azure, il codice può ottenere i token di accesso per autenticarsi alle risorse che supportano l'autenticazione di Azure AD. L'API di Azure Resource Manager supporta l'autenticazione di Azure AD. In primo luogo, è necessario concedere all'identità della macchina virtuale l'accesso a una risorsa Azure Resource Manager, in questo caso al gruppo di risorse che contiene la macchina virtuale.  

1. Passare alla scheda **Gruppo di risorse**.
2. Selezionare il **Gruppo di risorse** specifico creato in precedenza.
3. Passare a **Controllo di accesso (IAM)** nel pannello a sinistra.
4. Fare clic su **Aggiungi** per aggiungere una nuova assegnazione di ruolo alla macchina virtuale. Scegliere **Ruolo** come **Lettore**.
5. Dall'elenco a discesa successivo **Assegna accesso a** selezionare la risorsa **Macchina virtuale**.
6. Verificare quindi che nell'elenco a discesa **Sottoscrizione** sia elencata la sottoscrizione corretta. In **Gruppo di risorse** selezionare **Tutti i gruppi di risorse**.
7. Scegliere infine la macchina virtuale Linux nell'elenco a discesa **Seleziona** e fare clic su **Salva**.

    ![Testo immagine alt](media/msi-tutorial-linux-vm-access-arm/msi-permission-linux.png)

## <a name="get-an-access-token-using-the-vms-system-assigned-managed-identity-and-use-it-to-call-resource-manager"></a>Ottenere un token di accesso usando l'identità gestita assegnata dal sistema della macchina virtuale e usarlo per chiamare Resource Manager 

Per completare questi passaggi, è necessario disporre di un client SSH. Se si usa Windows, è possibile usare il client SSH nel [sottosistema Windows per Linux](https://msdn.microsoft.com/commandline/wsl/about). Per richiedere assistenza nella configurazione delle chiavi del client SSH, vedere [Come usare le chiavi SSH con Windows in Azure](../../virtual-machines/linux/ssh-from-windows.md) o [Come creare e usare una coppia di chiavi SSH pubblica e privata per le macchine virtuali Linux in Azure](../../virtual-machines/linux/mac-create-ssh-keys.md).

1. Nel portale, passare a una macchina virtuale Linux e in **Panoramica** fare clic su **Connetti**.  
2. **Connettersi** alla macchina virtuale usando un client SSH di propria scelta. 
3. Nella finestra del terminale usare CURL per eseguire una richiesta all'endpoint locale delle identità gestite per le risorse di Azure per ottenere un token di accesso per Azure Resource Manager.  
 
    La richiesta CURL per il token di accesso è mostrata di seguito.  
    
    ```bash
    curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true   
    ```
    
    > [!NOTE]
    > Il valore del parametro "risorsa" deve corrispondere esattamente a quello previsto da Azure AD.  Quando si usa l'ID della risorsa di Azure Resource Manager, è necessario includere la barra finale nell'URI. 
    
    La risposta include il token di accesso necessario per accedere ad Azure Resource Manager. 
    
    Risposta:  

    ```bash
    {"access_token":"eyJ0eXAiOi...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1504130527",
    "not_before":"1504126627",
    "resource":"https://management.azure.com",
    "token_type":"Bearer"} 
    ```
    
    È possibile usare questo token di accesso per accedere a Azure Resource Manager, ad esempio per leggere i dettagli del gruppo di risorse a cui l'utente ha concesso in precedenza l'accesso alla macchina virtuale. Sostituire i valori dell'\<ID di sottoscrizione\>, del \<gruppo di risorse\> e del \<token di accesso\> con quelli creati in precedenza. 
    
    > [!NOTE]
    > L'URL fa distinzione tra maiuscole e minuscole. Accertarsi quindi di usare la stessa combinazione di maiuscole e minuscole usata in precedenza per il nome del gruppo di risorse, facendo attenzione alla maiuscola "G" in "resourceGroup".  
    
    ```bash 
    curl https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>?api-version=2016-09-01 -H "Authorization: Bearer <ACCESS TOKEN>" 
    ```
    
    La risposta restituisce le informazioni specifiche del gruppo di risorse: 
     
    ```bash
    {"id":"/subscriptions/98f51385-2edc-4b79-bed9-7718de4cb861/resourceGroups/DevTest","name":"DevTest","location":"westus","properties":{"provisioningState":"Succeeded"}} 
    ```     

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come usare un'identità gestita assegnata dal sistema per accedere all'API di Azure Resource Manager.  Per altre informazioni su Azure Resource Manager, vedere:

> [!div class="nextstepaction"]
>[Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview)