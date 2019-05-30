---
title: Usare un'identità gestita assegnata dal sistema per una macchina virtuale Windows per accedere ad Azure Key Vault
description: Esercitazione che illustra come usare un'identità gestita assegnata dal sistema per una macchina virtuale Windows per accedere ad Azure Key Vault.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: daveba
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/20/2017
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 180e5544cfdc8fe7d5c3317347901f70667f1c8d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "66226802"
---
# <a name="tutorial-use-a-windows-vm-system-assigned-managed-identity-to-access-azure-key-vault"></a>Esercitazione: Usare un'identità gestita assegnata dal sistema per una macchina virtuale Windows per accedere ad Azure Key Vault 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Questa esercitazione illustra come usare un'identità gestita assegnata dal sistema per una macchina virtuale Windows per accedere ad Azure Key Vault. Fungendo da bootstrap, Key Vault consente quindi all'applicazione client di usare il segreto per accedere alle risorse non protette da Azure Active Directory (AD). Le Identità del servizio gestito vengono gestite automaticamente da Azure e consentono di eseguire l'autenticazione ai servizi che supportano l'autenticazione di Azure AD senza la necessità di inserire le credenziali nel codice. 

Si apprenderà come:


> [!div class="checklist"]
> * Concedere alla macchina virtuale l'accesso a un segreto archiviato in un insieme di credenziali delle chiavi 
> * Ottenere un token di accesso usando l'identità  della macchina virtuale e usarlo per recuperare il segreto dall'insieme di credenziali delle chiavi 

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="grant-your-vm-access-to-a-secret-stored-in-a-key-vault"></a>Concedere alla macchina virtuale l'accesso a un segreto archiviato nell'insieme di credenziali delle chiavi 
 
Usando le identità gestite per le risorse di Azure, il codice può ottenere i token di accesso per autenticarsi alle risorse che supportano l'autenticazione di Azure AD.  Tuttavia, non tutti i servizi di Azure supportano l'autenticazione di Azure AD. Per usare le identità gestite per le risorse di Azure con questi servizi, archiviare le credenziali del servizio in Azure Key Vault e usare l'identità gestita della macchina virtuale per accedere a Key Vault e recuperare le credenziali. 

In primo luogo, è necessario creare un insieme di credenziali delle chiavi e concedere l'accesso a tale insieme all'identità gestita assegnata dal sistema della macchina virtuale.   

1. Nella parte superiore della barra di spostamento a sinistra selezionare **Crea una risorsa** > **Sicurezza e identità** > **Key Vault**.  
2. Specificare un **nome** per il nuovo insieme di credenziali delle chiavi. 
3. Individuare l'insieme di credenziali delle chiavi nella stessa sottoscrizione e nello stesso gruppo di risorse della macchina virtuale creata in precedenza. 
4. Selezionare **Criteri di accesso** condiviso e fare clic su **Aggiungi nuovo**. 
5. In Configura dal modello selezionare **Gestione dei segreti**. 
6. Scegliere **Selezionare un'entità** e nel campo di ricerca immettere il nome della macchina virtuale creata in precedenza.  Selezionare la macchina virtuale nell'elenco dei risultati e fare clic su **Seleziona** . 
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
 
## <a name="get-an-access-token-using-the-vm-identity-and-use-it-to-retrieve-the-secret-from-the-key-vault"></a>Ottenere un token di accesso usando l'identità della macchina virtuale e usarlo per recuperare il segreto da Key Vault  

Se non si dispone di PowerShell 4.3.1 o versione successiva, è necessario [scaricare e installare la versione più recente](https://docs.microsoft.com/powershell/azure/overview).

In primo luogo, si userà l'identità gestita assegnata dal sistema della macchina virtuale per ottenere un token di accesso per l'autenticazione a Key Vault:
 
1. Nel portale passare a **Macchine virtuali**, selezionare la macchina virtuale Windows e in **Panoramica** fare clic su **Connetti**.
2. Inserire il **Nome utente** e la **Password** aggiunti al momento della creazione della **macchina virtuale Windows**.  
3. Ora che si è creata una **connessione Desktop remoto** con la macchina virtuale, aprire PowerShell nella sessione remota.  
4. In PowerShell, richiamare la richiesta Web nel tenant per ottenere il token per l'host locale in una porta specifica della macchina virtuale.  

    Richiesta di PowerShell:
    
    ```powershell
    $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -Method GET -Headers @{Metadata="true"} 
    ```
    
    Estrarre quindi la risposta completa, archiviata come stringa in formato JSON (JavaScript Object Notation) nell'oggetto $response.  
    
    ```powershell
    $content = $response.Content | ConvertFrom-Json 
    ```
    
    Estrarre poi il token di accesso dalla risposta.  
    
    ```powershell
    $KeyVaultToken = $content.access_token 
    ```
    
    Infine, usare il comando Invoke-WebRequest di PowerShell per recuperare il segreto creato in precedenza nell'insieme di credenziali delle chiavi, passando il token di accesso nell'intestazione dell'autorizzazione.  È necessario l'URL dell'insieme di credenziali delle chiavi che si trova nella sezione **Informazioni di base** della pagina **Panoramica** dell'insieme di credenziali delle chiavi.  
    
    ```powershell
    (Invoke-WebRequest -Uri https://<your-key-vault-URL>/secrets/<secret-name>?api-version=2016-10-01 -Method GET -Headers @{Authorization="Bearer $KeyVaultToken"}).content 
    ```
    
    La risposta avrà l'aspetto seguente: 
    
    ```powershell
    {"value":"p@ssw0rd!","id":"https://mytestkeyvault.vault.azure.net/secrets/MyTestSecret/7c2204c6093c4d859bc5b9eff8f29050","attributes":{"enabled":true,"created":1505088747,"updated":1505088747,"recoveryLevel":"Purgeable"}} 
    ```
    
Dopo aver recuperato il segreto dall'insieme di credenziali delle chiavi, è possibile usarlo per eseguire l'autenticazione a un servizio che richiede un nome e una password. 

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come usare un'identità gestita assegnata dal sistema per una macchina virtuale Windows per accedere ad Azure Key Vault.  Per altre informazioni su Azure Key Vault, consultare:

> [!div class="nextstepaction"]
>[Insieme di credenziali chiave Azure](/azure/key-vault/key-vault-whatis)
