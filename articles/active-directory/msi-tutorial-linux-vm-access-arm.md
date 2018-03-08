---
title: "Usare l'Identità del servizio gestito di una macchina virtuale Linux per accedere ad Azure Resource Manager"
description: "Esercitazione che illustra come usare un'Identità del servizio gestito di una macchina virtuale Linux per accedere ad Azure Resource Manager."
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
editor: bryanla
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/20/2017
ms.author: daveba
ms.openlocfilehash: 198087545e43b2e2a96d9bdec7c288f0699ca0e5
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/07/2018
---
# <a name="use-a-linux-vm-managed-service-identity-msi-to-access-azure-resource-manager"></a>Usare un'identità del servizio gestito per una macchina virtuale Linux per accedere ad Azure Resource Manager

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

Questa esercitazione illustra come abilitare l'Identità del servizio gestito (MSI, Managed Service Identity) per una macchina virtuale Linux e quindi usarla per accedere all'API di Azure Resource Manager. Le identità del servizio gestito vengono gestite automaticamente da Azure e consentono di eseguire l'autenticazione ai servizi che supportano l'autenticazione di Azure AD senza la necessità di inserire le credenziali nel codice. Si apprenderà come:

> [!div class="checklist"]
> * Abilitare Identità del servizio gestito in una macchina virtuale Linux 
> * Concedere alla macchina virtuale l'accesso a un gruppo di risorse in Azure Resource Manager 
> * Ottenere un token di accesso usando l'identità della macchina virtuale e usarlo per chiamare Azure Resource Manager 

## <a name="prerequisites"></a>prerequisiti

[!INCLUDE [msi-qs-configure-prereqs](../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>Creare una macchina virtuale Linux in un nuovo gruppo di risorse

Per questa esercitazione si creerà una nuova macchina virtuale Linux, ma è anche possibile abilitare l'identità del servizio gestito in una macchina virtuale esistente.

1. Fare clic sul pulsante **Crea una risorsa** visualizzato nell'angolo in alto a sinistra nel portale di Azure.
2. Selezionare **Calcolo** e quindi **Ubuntu Server 16.04 LTS**.
3. Immettere le informazioni relative alla macchina virtuale. In **Tipo di autenticazione** selezionare **Chiave pubblica SSH** o **Password**. Le credenziali create consentono di eseguire l'accesso alla macchina virtuale.

    ![Testo immagine alt](media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

4. Dall'elenco a discesa **Sottoscrizione** selezionare una sottoscrizione per la macchina virtuale.
5. Per selezionare un nuovo **Gruppo di risorse** in cui creare la macchina virtuale, scegliere **Crea nuovo**. Al termine fare clic su **OK**.
6. Selezionare la dimensione della macchina virtuale. Per visualizzare altre dimensioni, selezionare **View all** (Visualizza tutto) o modificare il filtro Tipo di disco supportato. Nel pannello delle impostazioni mantenere le impostazioni predefinite e fare clic su **OK**.

## <a name="enable-msi-on-your-vm"></a>Abilitare identità del servizio gestito nella macchina virtuale

Un'Identità del servizio gestito per una macchina virtuale consente di ottenere i token di accesso da Azure AD senza dover inserire le credenziali nel codice. A livello sottostante, quando si abilita Identità del servizio gestito, si eseguono due operazioni, ovvero si installa l'estensione di Identità del servizio gestito nella macchina virtuale e si abilita la funzionalità per tale macchina.  

1. Selezionare la **macchina virtuale** in cui si vuole abilitare identità del servizio gestito.
2. Nella barra di spostamento a sinistra fare clic su **Configurazione**.
3. Viene visualizzato **Managed Service Identity** (identità del servizio gestito). Per registrare e abilitare identità del servizio gestita, scegliere **Sì**. Se si vuole disabilitare questa funzionalità, scegliere No.
4. Assicurarsi di fare clic su **Salva** per salvare la configurazione.

    ![Testo immagine alt](media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. Per verificare le estensioni installate nella **macchina virtuale Linux**, fare clic su **Estensioni**. Se la funzione Identità del servizio gestito è abilitata, nell'elenco è inclusa la voce **ManagedIdentityExtensionforLinux**.

    ![Testo immagine alt](media/msi-tutorial-linux-vm-access-arm/msi-extension-value.png)

## <a name="grant-your-vm-access-to-a-resource-group-in-azure-resource-manager"></a>Concedere alla macchina virtuale l'accesso a un gruppo di risorse in Azure Resource Manager 

Usando Identità del servizio gestito, il codice può ottenere i token di accesso per autenticarsi alle risorse che supportano l'autenticazione di Azure AD. L'API di Azure Resource Manager supporta l'autenticazione di Azure AD. In primo luogo, è necessario concedere all'identità della macchina virtuale l'accesso a una risorsa Azure Resource Manager, in questo caso al gruppo di risorse che contiene la macchina virtuale.  

1. Passare alla scheda **Gruppo di risorse**.
2. Selezionare il **Gruppo di risorse** specifico creato in precedenza.
3. Passare a **Controllo di accesso (IAM)** nel pannello a sinistra.
4. Fare clic su **Aggiungi** per aggiungere una nuova assegnazione di ruolo alla macchina virtuale. Scegliere **Ruolo** come **Lettore**.
5. Dall'elenco a discesa successivo **Assegna accesso a** selezionare la risorsa **Macchina virtuale**.
6. Verificare quindi che nell'elenco a discesa **Sottoscrizione** sia elencata la sottoscrizione corretta. In **Gruppo di risorse** selezionare **Tutti i gruppi di risorse**.
7. Scegliere infine la macchina virtuale Linux nell'elenco a discesa **Seleziona** e fare clic su **Salva**.

    ![Testo immagine alt](media/msi-tutorial-linux-vm-access-arm/msi-permission-linux.png)

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-resource-manager"></a>Ottenere un token di accesso usando l'identità della macchina virtuale e usarlo per chiamare Azure Resource Manager 

Per completare questi passaggi, è necessario disporre di un client SSH. Se si usa Windows, è possibile usare il client SSH nel [sottosistema Windows per Linux](https://msdn.microsoft.com/commandline/wsl/about). Per richiedere assistenza nella configurazione delle chiavi del client SSH, vedere [Come usare le chiavi SSH con Windows in Azure](../virtual-machines/linux/ssh-from-windows.md) o [Come creare e usare una coppia di chiavi SSH pubblica e privata per le macchine virtuali Linux in Azure](../virtual-machines/linux/mac-create-ssh-keys.md).

1. Nel portale, passare a una macchina virtuale Linux e in **Panoramica** fare clic su **Connetti**.  
2. **Connettersi** alla macchina virtuale usando un client SSH di propria scelta. 
3. Nella finestra terminale usare CURL per fare una richiesta all'endpoint locale di Identità del servizio gestito per ottenere un token di accesso per Azure Resource Manager.  
 
    La richiesta CURL per il token di accesso è mostrata di seguito.  
    
    ```bash
    curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true   
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
     
## <a name="related-content"></a>Contenuti correlati

- Per una panoramica dell'identità di servizio gestito, vedere [Panoramica dell'identità di servizio gestito](../active-directory/msi-overview.md).

Usare la sezione dei commenti seguente per fornire commenti e suggerimenti utili per migliorare e organizzare i contenuti disponibili.

