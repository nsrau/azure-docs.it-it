---
title: Usare un'identità gestita assegnata dal sistema per una macchina virtuale Linux per accedere ad Azure Key Vault
description: Esercitazione che illustra come usare un'identità gestita assegnata dal sistema per una macchina virtuale Linux per accedere ad Azure Resource Manager.
services: active-directory
documentationcenter: ''
author: daveba
manager: daveba
editor: daveba
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/20/2017
ms.author: daveba
ms.openlocfilehash: 388c2de437546ac0a415a5e061ce8bceb0a507d3
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/22/2019
ms.locfileid: "54425102"
---
# <a name="tutorial-use-a-linux-vm-system-assigned-managed-identity-to-access-azure-key-vault"></a>Esercitazione: Usare un'identità gestita assegnata dal sistema per una macchina virtuale Linux per accedere ad Azure Key Vault 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Questa esercitazione mostra come usare un'identità gestita assegnata dal sistema per una macchina virtuale Linux per accedere ad Azure Key Vault. Fungendo da bootstrap, Key Vault consente quindi all'applicazione client di usare il segreto per accedere alle risorse non protette da Azure Active Directory (AD). Le identità gestite per le risorse di Azure vengono gestite automaticamente da Azure e consentono di eseguire l'autenticazione ai servizi che supportano l'autenticazione di Azure AD senza la necessità di inserire le credenziali nel codice. 

Si apprenderà come:

> [!div class="checklist"]
> * Concedere alla macchina virtuale l'accesso a un segreto archiviato in un insieme di credenziali delle chiavi 
> * Ottenere un token di accesso usando l'identità della VM e usarlo per recuperare il segreto da Key Vault 
 
## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="grant-your-vm-access-to-a-secret-stored-in-a-key-vault"></a>Concedere alla macchina virtuale l'accesso a un segreto archiviato nell'insieme di credenziali delle chiavi  

Usando le identità del servizio gestite per le risorse di Azure il codice può ottenere i token di accesso per autenticarsi alle risorse che supportano l'autenticazione di Azure Active Directory. Tuttavia, non tutti i servizi di Azure supportano l'autenticazione di Azure AD. Per usare le identità gestite per le risorse di Azure con questi servizi, archiviare le credenziali del servizio in Azure Key Vault e usare le identità gestite per le risorse di Azure per accedere a Key Vault e recuperare le credenziali. 

In primo luogo, è necessario creare un insieme di credenziali delle chiavi e concedere l'accesso a tale insieme all'identità gestita assegnata dal sistema della macchina virtuale.   

1. Nella parte superiore della barra di spostamento a sinistra selezionare **Crea una risorsa** > **Sicurezza e identità** > **Key Vault**.  
2. Specificare un **nome** per il nuovo insieme di credenziali delle chiavi. 
3. Individuare l'insieme di credenziali delle chiavi nella stessa sottoscrizione e nello stesso gruppo di risorse della macchina virtuale creata in precedenza. 
4. Selezionare **Criteri di accesso** condiviso e fare clic su **Aggiungi nuovo**. 
5. In Configura dal modello selezionare **Gestione dei segreti**. 
6. Scegliere **Selezionare un'entità** e nel campo di ricerca immettere il nome della macchina virtuale creata in precedenza.  Selezionare la macchina virtuale nell'elenco dei risultati e fare clic su **Seleziona**. 
7. Fare clic su **OK** per terminare l'aggiunta dei nuovi criteri di accesso e su **OK** per completare la selezione dei criteri di accesso. 
8. Fare clic su **Crea** per completare la creazione dell'insieme di credenziali delle chiavi. 

    ![Testo immagine alt](./media/msi-tutorial-windows-vm-access-nonaad/msi-blade.png)

Successivamente, aggiungere un segreto all'insieme di credenziali delle chiavi, in modo che in un secondo momento sia possibile recuperare il segreto mediante il codice in esecuzione nella macchina virtuale: 

1. Selezionare **Tutte le risorse**, quindi individuare e selezionare l'insieme di credenziali delle chiavi appena creato. 
2. Selezionare **Segreti** e fare clic su **Aggiungi**. 
3. Selezionare **Manuale** dalle **Opzioni di caricamento**. 
4. Specificare un nome e il valore del segreto.  È possibile specificare qualsiasi valore. 
5. Lasciare vuoti i campi della data di attivazione e della data di scadenza e lasciare **Abilitato** su **Sì**. 
6. Fare clic su **Crea** per creare il segreto. 
 
## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-retrieve-the-secret-from-the-key-vault"></a>Ottenere un token di accesso usando l'identità della VM e usarlo per recuperare il segreto da Key Vault  

Per completare questi passaggi, è necessario disporre di un client SSH.  Se si usa Windows, è possibile usare il client SSH nel [sottosistema Windows per Linux](https://msdn.microsoft.com/commandline/wsl/about). Per richiedere assistenza nella configurazione delle chiavi del client SSH, vedere [Come usare le chiavi SSH con Windows in Azure](../../virtual-machines/linux/ssh-from-windows.md) o [Come creare e usare una coppia di chiavi SSH pubblica e privata per le macchine virtuali Linux in Azure](../../virtual-machines/linux/mac-create-ssh-keys.md).
 
1. Nel portale, passare a una macchina virtuale Linux e in **Panoramica** fare clic su **Connetti**. 
2. **Connettersi** alla macchina virtuale usando un client SSH di propria scelta. 
3. Nella finestra del terminale usare CURL per eseguire una richiesta all'endpoint locale delle identità gestite per le risorse di Azure per ottenere un token di accesso per Azure Key Vault.  
 
    La richiesta CURL per il token di accesso è mostrata di seguito.  
    
    ```bash
    curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -H Metadata:true  
    ```
    La risposta include il token di accesso necessario per accedere a Gestione risorse. 
    
    Risposta:  
    
    ```bash
    {"access_token":"eyJ0eXAi...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1504130527",
    "not_before":"1504126627",
    "resource":"https://vault.azure.net",
    "token_type":"Bearer"} 
    ```
    
    È possibile usare questo token di accesso per l'autenticazione ad Azure Key Vault.  La successiva richiesta CURL mostra come leggere un segreto da Key Vault usando CURL e l'API REST di Key Vault.  È necessario l'URL dell'insieme di credenziali delle chiavi che si trova nella sezione **Informazioni di base** della pagina **Panoramica** dell'insieme di credenziali delle chiavi.  È necessario anche il token di accesso ottenuto alla chiamata precedente. 
        
    ```bash
    curl https://<YOUR-KEY-VAULT-URL>/secrets/<secret-name>?api-version=2016-10-01 -H "Authorization: Bearer <ACCESS TOKEN>" 
    ```
    
    La risposta avrà l'aspetto seguente: 
    
    ```bash
    {"value":"p@ssw0rd!","id":"https://mytestkeyvault.vault.azure.net/secrets/MyTestSecret/7c2204c6093c4d859bc5b9eff8f29050","attributes":{"enabled":true,"created":1505088747,"updated":1505088747,"recoveryLevel":"Purgeable"}} 
    ```
    
Dopo aver recuperato il segreto dall'insieme di credenziali delle chiavi, è possibile usarlo per eseguire l'autenticazione a un servizio che richiede un nome e una password.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come usare un'identità gestita assegnata dal sistema per una macchina virtuale Linux per accedere ad Azure Key Vault.  Per altre informazioni su Azure Key Vault, consultare:

> [!div class="nextstepaction"]
>[Insieme di credenziali chiave Azure](/azure/key-vault/key-vault-whatis)




