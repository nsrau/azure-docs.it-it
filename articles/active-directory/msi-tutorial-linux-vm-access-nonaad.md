---
title: "Usare un'Identità del servizio gestito di una macchina virtuale Linux per accedere ad Azure Key Vault"
description: "Esercitazione che illustra come usare un'Identità del servizio gestito di una macchina virtuale Linux per accedere ad Azure Resource Manager."
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
editor: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/20/2017
ms.author: daveba
ms.openlocfilehash: b14d1729592b0b97d2616189c6e0ae4727ebaf91
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/07/2018
---
# <a name="use-a-linux-vm-managed-service-identity-msi-to-access-azure-key-vault"></a>Usare un'Identità del servizio gestito con una macchina virtuale Linux per accedere ad Azure Key Vault 

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

Questa esercitazione illustra come abilitare l'identità del servizio gestito per una macchina virtuale Linux e quindi usarla per accedere ad Azure Key Vault. Fungendo da bootstrap, Key Vault consente quindi all'applicazione client di usare il segreto per accedere alle risorse non protette da Azure Active Directory (AD). Le Identità del servizio gestito vengono gestite automaticamente da Azure e consentono di eseguire l'autenticazione ai servizi che supportano l'autenticazione di Azure AD senza la necessità di inserire le credenziali nel codice. 

Si apprenderà come:

> [!div class="checklist"]
> * Abilitare Identità del servizio gestito in una macchina virtuale Linux 
> * Concedere alla macchina virtuale l'accesso a un segreto archiviato in un insieme di credenziali delle chiavi 
> * Ottenere un token di accesso usando l'identità della macchina virtuale e usarlo per recuperare il segreto da Key Vault 
 
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
6. Selezionare la dimensione della macchina virtuale. Per visualizzare altre dimensioni, selezionare **View all** (Visualizza tutto) o modificare il filtro Tipo di disco supportato. Nella pagina delle impostazioni mantenere le impostazioni predefinite e fare clic su **OK**.

## <a name="enable-msi-on-your-vm"></a>Abilitare identità del servizio gestito nella macchina virtuale

Un'Identità del servizio gestito per una macchina virtuale consente di ottenere i token di accesso da Azure AD senza dover inserire le credenziali nel codice. A livello sottostante, quando si abilita Identità del servizio gestito, si eseguono due operazioni, ovvero si installa l'estensione di Identità del servizio gestito nella macchina virtuale e si abilita la funzionalità per tale macchina.  

1. Selezionare la **macchina virtuale** in cui si vuole abilitare identità del servizio gestito.
2. Nella barra di spostamento a sinistra fare clic su **Configurazione**.
3. Viene visualizzato **Managed Service Identity** (identità del servizio gestito). Per registrare e abilitare identità del servizio gestita, scegliere **Sì**. Se si vuole disabilitare questa funzionalità, scegliere No.
4. Assicurarsi di fare clic su **Salva** per salvare la configurazione.

    ![Testo immagine alt](media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. Per verificare le estensioni installate nella **macchina virtuale Linux**, fare clic su **Estensioni**. Se la funzione Identità del servizio gestito è abilitata, nell'elenco è inclusa la voce **ManagedIdentityExtensionforLinux**.

    ![Testo immagine alt](media/msi-tutorial-linux-vm-access-arm/msi-extension-value.png)


## <a name="grant-your-vm-access-to-a-secret-stored-in-a-key-vault"></a>Concedere alla macchina virtuale l'accesso a un segreto archiviato nell'insieme di credenziali delle chiavi  

Usando Identità del servizio gestito, il codice può ottenere i token di accesso per autenticarsi alle risorse che supportano l'autenticazione di Azure Active Directory. Tuttavia, non tutti i servizi di Azure supportano l'autenticazione di Azure AD. Per usare l'Identità del servizio gestito con tali servizi, archiviare le credenziali del servizio in Azure Key Vault e usare l'Identità del servizio gestito per accedere all'insieme di credenziali delle chiavi e recuperare le credenziali. 

In primo luogo, è necessario creare un insieme di credenziali delle chiavi e concedere all'identità della macchina virtuale l'accesso all'insieme di credenziali delle chiavi.   

1. Nella parte superiore della barra di spostamento a sinistra selezionare **Crea una risorsa** > **Sicurezza e identità** > **Key Vault**.  
2. Specificare un **nome** per il nuovo insieme di credenziali delle chiavi. 
3. Individuare l'insieme di credenziali delle chiavi nella stessa sottoscrizione e nello stesso gruppo di risorse della macchina virtuale creata in precedenza. 
4. Selezionare **Criteri di accesso** condiviso e fare clic su **Aggiungi nuovo**. 
5. In Configura dal modello selezionare **Gestione dei segreti**. 
6. Scegliere **Selezionare un'entità** e nel campo di ricerca immettere il nome della macchina virtuale creata in precedenza.  Selezionare la macchina virtuale nell'elenco dei risultati e fare clic su **Seleziona**. 
7. Fare clic su **OK** per terminare l'aggiunta dei nuovi criteri di accesso e su **OK** per completare la selezione dei criteri di accesso. 
8. Fare clic su **Crea** per completare la creazione dell'insieme di credenziali delle chiavi. 

    ![Testo immagine alt](media/msi-tutorial-windows-vm-access-nonaad/msi-blade.png)

Successivamente, aggiungere un segreto all'insieme di credenziali delle chiavi, in modo che in un secondo momento sia possibile recuperare il segreto mediante il codice in esecuzione nella macchina virtuale: 

1. Selezionare **Tutte le risorse**, quindi individuare e selezionare l'insieme di credenziali delle chiavi appena creato. 
2. Selezionare **Segreti** e fare clic su **Aggiungi**. 
3. Selezionare **Manuale** dalle **Opzioni di caricamento**. 
4. Specificare un nome e il valore del segreto.  È possibile specificare qualsiasi valore. 
5. Lasciare vuoti i campi della data di attivazione e della data di scadenza e lasciare **Abilitato** su **Sì**. 
6. Fare clic su **Crea** per creare il segreto. 
 
## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-retrieve-the-secret-from-the-key-vault"></a>Ottenere un token di accesso usando l'identità della VM e usarlo per recuperare il segreto da Key Vault  

Per completare questi passaggi, è necessario disporre di un client SSH.  Se si usa Windows, è possibile usare il client SSH nel [sottosistema Windows per Linux](https://msdn.microsoft.com/commandline/wsl/about). Per richiedere assistenza nella configurazione delle chiavi del client SSH, vedere [Come usare le chiavi SSH con Windows in Azure](../virtual-machines/linux/ssh-from-windows.md) o [Come creare e usare una coppia di chiavi SSH pubblica e privata per le macchine virtuali Linux in Azure](../virtual-machines/linux/mac-create-ssh-keys.md).
 
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
    {"access_token":"eyJ0eXAi...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1504130527",
    "not_before":"1504126627",
    "resource":"https://vault.azure.net",
    "token_type":"Bearer"} 
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

- Per una panoramica dell'identità di servizio gestito, vedere [Panoramica dell'identità di servizio gestito](../active-directory/msi-overview.md).

Usare la sezione dei commenti seguente per fornire commenti e suggerimenti utili per migliorare e organizzare i contenuti disponibili.




