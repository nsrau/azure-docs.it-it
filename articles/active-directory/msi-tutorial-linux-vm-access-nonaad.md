---
title: "Usare un'Identità del servizio gestito di una macchina virtuale Linux per accedere ad Azure Key Vault"
description: "Esercitazione che illustra come usare un'Identità del servizio gestito di una macchina virtuale Linux per accedere ad Azure Resource Manager."
services: active-directory
documentationcenter: 
author: elkuzmen
manager: mbaldwin
editor: bryanla
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/19/2017
ms.author: elkuzmen
ms.openlocfilehash: dd2dfe20f86b3fac28871b27a1c2b66c2b4a4cd6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="use-managed-service-identity-msi-with-a-linux-vm-to-access-azure-key-vault"></a>Usare un'Identità del servizio gestito con una macchina virtuale Linux per accedere ad Azure Key Vault 

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

Questa esercitazione illustra come abilitare l'Identità del servizio gestito (MSI, Managed Service Identity) per una macchina virtuale Windows e quindi usarla per accedere ad Azure Key Vault. Le Identità del servizio gestito vengono gestite automaticamente da Azure e consentono di eseguire l'autenticazione ai servizi che supportano l'autenticazione di Azure AD senza la necessità di inserire le credenziali nel codice. Si apprenderà come:

> [!div class="checklist"]
> * Abilitare Identità del servizio gestito in una macchina virtuale Linux 
> * Concedere alla macchina virtuale l'accesso a un segreto archiviato in un insieme di credenziali delle chiavi 
> * Ottenere un token di accesso usando l'identità della macchina virtuale e usarlo per recuperare il segreto da Key Vault 
 


Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.


## <a name="sign-in-to-azure"></a>Accedere ad Azure
Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>Creare una macchina virtuale Linux in un nuovo gruppo di risorse

Per questa esercitazione si creerà una nuova macchina virtuale Linux, ma è anche possibile abilitare l'Identità del servizio gestito in una macchina virtuale esistente.

1. Fare clic sul pulsante **Nuovo** nell'angolo superiore sinistro del portale di Azure.
2. Selezionare **Calcolo** e quindi **Ubuntu Server 16.04 LTS**.
3. Immettere le informazioni relative alla macchina virtuale. In **Tipo di autenticazione** selezionare **Chiave pubblica SSH** o **Password**. Le credenziali create consentiranno di effettuare l'accesso alla macchina virtuale.

    ![Testo immagine alt](media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

4. Dall'elenco a discesa **Sottoscrizione** selezionare una sottoscrizione per la macchina virtuale.
5. Per selezionare un nuovo **Gruppo di risorse** in cui creare la macchina virtuale, scegliere **Crea nuovo**. Al termine fare clic su **OK**.
6. Selezionare la dimensione della macchina virtuale. Per visualizzare altre dimensioni, selezionare **View all** (Visualizza tutto) o modificare il filtro Tipo di disco supportato. Nella pagina delle impostazioni mantenere le impostazioni predefinite e fare clic su **OK**.

## <a name="enable-msi-on-your-vm"></a>Abilitare Identità del servizio gestito nella macchina virtuale

Un'Identità del servizio gestito per una macchina virtuale consente di ottenere i token di accesso da Azure AD senza dover inserire le credenziali nel codice. A livello sottostante, quando si abilita Identità del servizio gestito, si eseguono due operazioni, ovvero si installa l'estensione di Identità del servizio gestito nella macchina virtuale e si abilita la funzionalità per tale macchina.  

1. Selezionare la **macchina virtuale** in cui si vuole abilitare Identità del servizio gestito.
2. Nella barra di spostamento a sinistra fare clic su **Configurazione**.
3. Verrà visualizzata la schermata **Managed Service Identity** (Identità del servizio gestito). Per registrare e abilitare l'Identità del servizio gestito, scegliere **Sì**. Se si vuole disabilitare questa funzionalità, scegliere No.
4. Assicurarsi di fare clic su **Salva** per salvare la configurazione.

    ![Testo immagine alt](media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. Per verificare le estensioni installate nella **macchina virtuale Linux**, fare clic su **Estensioni**. Se Identità del servizio gestito è abilitata, nell'elenco sarà inclusa la voce **ManagedIdentityExtensionforLinux**.

    ![Testo immagine alt](media/msi-tutorial-linux-vm-access-arm/msi-extension-value.png)


## <a name="grant-your-vm-access-to-a-secret-stored-in-a-key-vault"></a>Concedere alla macchina virtuale l'accesso a un segreto archiviato in un insieme di credenziali delle chiavi  

Usando Identità del servizio gestito, il codice può ottenere i token di accesso per autenticarsi alle risorse che supportano l'autenticazione di Azure Active Directory. Tuttavia, non tutti i servizi di Azure supportano l'autenticazione di Azure AD. Per usare Identità del servizio gestito con i servizi che non la supportano, è possibile archiviare le credenziali necessarie per tali servizi in Azure Key Vault e usare Identità del servizio gestito per l'autenticazione in Key Vault al fine di recuperare le credenziali. 

In primo luogo, è necessario creare un insieme di credenziali delle chiavi e concedere all'Identità della macchina virtuale l'accesso a Key Vault.   

1. Nella parte superiore della barra di spostamento a sinistra selezionare **+ Nuovo**, quindi **Sicurezza e identità** quindi **Key Vault**.  
2. Specificare un **nome** per il nuovo insieme di credenziali delle chiavi. 
3. Individuare l'insieme di credenziali delle chiavi nella stessa sottoscrizione e nello stesso gruppo di risorse della macchina virtuale creata in precedenza. 
4. Selezionare **Criteri di accesso** condiviso e fare clic su **Aggiungi nuovo**. 
5. In Configura dal modello selezionare **Gestione dei segreti**. 
6. Scegliere **Selezionare un'entità** e nel campo di ricerca immettere il nome della macchina virtuale creata in precedenza.  Selezionare la macchina virtuale nell'elenco dei risultati e fare clic su **Seleziona**. 
7. Fare clic su **OK** per terminare l'aggiunta dei nuovi criteri di accesso e su **OK** per completare la selezione dei criteri di accesso. 
8. Fare clic su **Crea** per completare la creazione dell'insieme di credenziali delle chiavi. 

    ![Testo immagine alt](media/msi-tutorial-windows-vm-access-nonaad/msi-blade.png)

Successivamente, aggiungere un segreto a Key Vault, in modo che in un secondo momento sia possibile recuperare il segreto mediante il codice in esecuzione nella macchina virtuale: 

1. Selezionare **Tutte le risorse**, quindi individuare e selezionare l'insieme di credenziali delle chiavi appena creato. 
2. Selezionare **Segreti** e fare clic su **Aggiungi**. 
3. Selezionare **Manuale** dalle **Opzioni di caricamento**. 
4. Specificare un nome e il valore del segreto.  Il valore può essere qualsiasi. 
5. Lasciare vuoti i campi della data di attivazione e della data di scadenza e lasciare **Abilitato** su **Sì**. 
6. Fare clic su **Crea** per creare il segreto. 
 
## <a name="get-an-access-token-using-the-vm-identity-and-use-it-retrieve-the-secret-from-the-key-vault"></a>Ottenere un token di accesso usando l'identità della macchina virtuale e usarlo per recuperare il segreto da Key Vault  

Per completare questi passaggi, è necessario disporre di un client SSH.  Se si usa Windows, è possibile usare il client SSH nel [sottosistema Windows per Linux](https://msdn.microsoft.com/commandline/wsl/about).   
 
1. Nel portale, passare a una macchina virtuale Linux e in **Panoramica** fare clic su **Connetti**. 
2. **Connettersi** alla macchina virtuale usando un client SSH di propria scelta. 
3. Nella finestra terminale usare CURL per fare una richiesta all'endpoint locale di Identità del servizio gestito per ottenere un token di accesso per Azure Key Vault.  
 
    La richiesta CURL per il token di accesso è mostrata di seguito.  
    
    ```bash
    curl http://localhost:50342/oauth2/token --data "resource=https://vault.azure.net" -H Metadata:true  
    ```
    La risposta include il token di accesso necessario per accedere a Gestione risorse. 
    
    Risposta:  
    
    ```bash
    {"access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IkhIQnlLVS0wRHFBcU1aaDZaRlBkMlZXYU90ZyIsImtpZCI6IkhIQnlLVS0wRHFBcU1aaDZaRlBkMlZXYU90ZyJ9.eyJhdWQiOiJodHRwczovL21hbmFnZW1lbnQuYXp1cmUuY29tIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3LyIsImlhdCI6MTUwNDEyNjYyNywibmJmIjoxNTA0MTI2NjI3LCJleHAiOjE1MDQxMzA1MjcsImFpbyI6IlkyRmdZTGg2dENWSzRkSDlGWGtuZzgyQ21ZNVdBZ0E9IiwiYXBwaWQiOiI2ZjJmNmU2OS04MGExLTQ3NmEtOGRjZi1mOTgzZDZkMjUxYjgiLCJhcHBpZGFjciI6IjIiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwib2lkIjoiMTEyODJiZDgtMDNlMi00NGVhLTlmYjctZTQ1YjVmM2JmNzJlIiwic3ViIjoiMTEyODJiZDgtMDNlMi00NGVhLTlmYjctZTQ1YjVmM2JmNzJlIiwidGlkIjoiNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3IiwidXRpIjoib0U5T3JVZFJMMHVKSEw4UFdvOEJBQSIsInZlciI6IjEuMCJ9.J6KS7b9kFgDkegJ-Vfff19LMnu3Cfps4dL2uNGucb5M76rgDM5f73VO-19wZSRhQPxWmZLETzN3SljnIMQMkYWncp79MVdBud_xqXYyLdQpGkNinpKVJhTo1j1dY27U_Cjl4yvvpBTrtH3OX9gG0GtQs7PBFTTLznqcH3JR9f-bTSEN4wUhalaIPHPciVDtJI9I24_vvMfVqxkXOo6gkL0mEPfpXZRLwrBNd607AzX0KVmLFrwA1vYJnCV-sSV8bwTh2t6CVEj240t0iyeVWVc2usJ0NY2rxPzKd_UckQ_zzrECG3kS4vuYePKz6GqNJFVzm2w2c61lX0-O1CwvQ9w","refresh_token":"","expires_in":"3599","expires_on":"1504130527","not_before":"1504126627","resource":"https://vault.azure.net","token_type":"Bearer"} 
    ```
    
    È possibile usare questo token di accesso per l'autenticazione ad Azure Key Vault.  La successiva richiesta CURL mostra come leggere un segreto da Key Vault usando CURL e l'API REST di Key Vault.  È necessario l'URL dell'insieme di credenziali delle chiavi che si trova nella sezione **Informazioni di base** della pagina **Panoramica** dell'insieme di credenziali delle chiavi.  È necessario anche il token di accesso ottenuto alla chiamata precedente. 
        
    ```bash
    curl https://<YOUR-KEY-VAULT-URL>/secrets/<secret-name>?api-version=2016-10-01 -H "Authorization: Bearer <ACCESS TOKEN>" 
    ```
    
    La risposta avrà l'aspetto seguente: 
    
    ```bash
    {"value":"p@ssw0rd!","id":"https://mytestkeyvault.vault.azure.net/secrets/MyTestSecret/7c2204c6093c4d859bc5b9eff8f29050","attributes":{"enabled":true,"created":1505088747,"updated":1505088747,"recoveryLevel":"Purgeable"}} 
    ```
    
Dopo aver recuperato il segreto dall'insieme di credenziali delle chiavi, è possibile usarlo per eseguire l'autenticazione a un servizio che richiede un nome e una password.


## <a name="related-content"></a>Contenuti correlati

- Per una panoramica dell'Identità di servizio gestito, vedere [Panoramica dell'Identità di servizio gestito](../active-directory/msi-overview.md).

Usare la sezione dei commenti seguente per fornire commenti e suggerimenti utili per migliorare e organizzare i contenuti disponibili.




