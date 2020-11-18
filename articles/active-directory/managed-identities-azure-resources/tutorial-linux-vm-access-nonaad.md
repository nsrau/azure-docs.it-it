---
title: Esercitazione`:` Usare un'identità gestita per accedere ad Azure Key Vault - Linux - Azure AD
description: Esercitazione che illustra come usare un'identità gestita assegnata dal sistema per una macchina virtuale Linux per accedere ad Azure Resource Manager.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: daveba
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/03/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7cfcaec38a939291090da7d2229c4a95f984bf28
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2020
ms.locfileid: "93360441"
---
# <a name="tutorial-use-a-linux-vm-system-assigned-managed-identity-to-access-azure-key-vault"></a>Esercitazione: Usare un'identità gestita assegnata dal sistema per una macchina virtuale Linux per accedere ad Azure Key Vault 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Questa esercitazione mostra come usare un'identità gestita assegnata dal sistema per una macchina virtuale Linux per accedere ad [Azure Key Vault](../../key-vault/general/overview.md). Fungendo da bootstrap, Key Vault consente quindi all'applicazione client di usare un segreto per accedere alle risorse non protette da Azure Active Directory (AD). Le identità del servizio gestito vengono gestite automaticamente da Azure e consentono di eseguire l'autenticazione ai servizi che supportano l'autenticazione di Azure AD senza la necessità di includere le informazioni di autenticazione nel codice.

Si apprenderà come:

> [!div class="checklist"]
> * Concedere alla macchina virtuale l'accesso a un segreto archiviato in un insieme di credenziali delle chiavi 
> * Ottenere un token di accesso usando l'identità della VM e usarlo per recuperare il segreto da Key Vault 
 
## <a name="prerequisites"></a>Prerequisiti

- Conoscenza delle identità gestite. Se non si ha familiarità con la funzionalità delle identità gestite per le risorse di Azure, vedere questa [panoramica](overview.md). 
- Un account Azure. [Iscriversi per riceverne uno gratuito](https://azure.microsoft.com/free/).
- Autorizzazioni di "Proprietario" nell'ambito appropriato (sottoscrizione o gruppo di risorse) per eseguire le procedure di creazione delle risorse e gestione dei ruoli. Se è necessaria assistenza con l'assegnazione, vedere [Usare il controllo degli accessi in base al ruolo per gestire l'accesso alle risorse della sottoscrizione di Azure](../../role-based-access-control/role-assignments-portal.md).
- È anche necessaria una macchina virtuale Linux con identità gestite assegnate dal sistema abilitate.
  - Se è necessario creare una macchina virtuale per questa esercitazione, è possibile seguire l'articolo [Creare una macchina virtuale Linux con il portale di Azure](../../virtual-machines/linux/quick-create-portal.md#create-virtual-machine)


## <a name="create-a-key-vault"></a>Creare un insieme di credenziali delle chiavi  

Questa sezione illustra come concedere alla macchina virtuale l'accesso a un segreto archiviato in un insieme di credenziali delle chiavi. Usando le identità gestite per le risorse di Azure, il codice può ottenere i token di accesso per autenticarsi alle risorse che supportano l'autenticazione di Azure AD.Tuttavia, non tutti i servizi di Azure supportano l'autenticazione di Azure AD. Per usare le identità gestite per le risorse di Azure con questi servizi, archiviare le credenziali del servizio in Azure Key Vault e usare l'identità gestita della macchina virtuale per accedere a Key Vault e recuperare le credenziali.

In primo luogo, è necessario creare un insieme di credenziali delle chiavi e concedere l'accesso a tale insieme all'identità gestita assegnata dal sistema della macchina virtuale.

1. Aprire il [portale](https://portal.azure.com/) di Azure
1. Nella parte superiore della barra di spostamento a sinistra selezionare **Crea una risorsa**  
1. Nella casella **Cerca nel Marketplace** digitare **Key Vault** e quindi premere **INVIO**.  
1. Nell'elenco dei risultati selezionare **Key Vault**.
1. Selezionare **Crea**
1. Specificare un **nome** per il nuovo insieme di credenziali delle chiavi.

    ![Schermata Crea un Key Vault](./media/tutorial-linux-vm-access-nonaad/create-key-vault.png)

1. Compilare tutte le informazioni necessarie assicurandosi di scegliere la sottoscrizione e il gruppo di risorse in cui è stata creata la macchina virtuale in uso per questa esercitazione.
1. Selezionare **Rivedi e crea**
1. Selezionare **Crea**

## <a name="grant-access"></a>Concedere l'accesso

All'identità gestita usata dalla macchina virtuale deve essere concesso l'accesso in lettura per il segreto che verrà archiviato in Key Vault.

1. Passare all'istanza di Key Vault appena creata
1. Selezionare **Criteri di accesso** dal menu sul lato sinistro.
1. Selezionare **Aggiungi criterio di accesso**.

   ![schermata di creazione dei criteri di accesso di Key Vault](./media/tutorial-linux-vm-access-nonaad/key-vault-access-policy.png)

1. Nella sezione **Aggiungi criterio di accesso** in **Configura dal modello (facoltativo)** scegliere **Gestione dei segreti** dal menu a discesa.
1. Scegliere **Selezionare un'entità** e nel campo di ricerca immettere il nome della macchina virtuale creata in precedenza.  Selezionare la macchina virtuale nell'elenco dei risultati e scegliere **Seleziona**.
1. Selezionare **Aggiungi**
1. Selezionare **Salva**.

## <a name="create-a-secret"></a>Creare un segreto

Successivamente, aggiungere un segreto all'istanza di Key Vault, in modo che sia possibile recuperarlo in un secondo momento usando il codice in esecuzione nella macchina virtuale. In questa esercitazione viene usato PowerShell, ma gli stessi concetti si applicano a qualsiasi tipo di codice in esecuzione in questa macchina virtuale.

1. Passare all'istanza di Key Vault appena creata.
1. Selezionare **Segreti** e fare clic su **Aggiungi**.
1. Selezionare **Genera/Importa**
1. Nella schermata **Crea un segreto** lasciare selezionata l'opzione **Manuale** in **Opzioni di caricamento**.
1. Specificare un nome e il valore del segreto.  È possibile specificare qualsiasi valore. 
1. Lasciare vuoti i campi della data di attivazione e della data di scadenza e lasciare **Abilitato** su **Sì**. 
1. Fare clic su **Crea** per creare il segreto.

   ![Creare un segreto](./media/tutorial-linux-vm-access-nonaad/create-secret.png)
 
## <a name="access-data"></a>Accedere ai dati

Per completare questi passaggi, è necessario disporre di un client SSH.  Se si usa Windows, è possibile usare il client SSH nel [sottosistema Windows per Linux](/windows/wsl/about). Per richiedere assistenza nella configurazione delle chiavi del client SSH, vedere [Come usare le chiavi SSH con Windows in Azure](../../virtual-machines/linux/ssh-from-windows.md) o [Come creare e usare una coppia di chiavi SSH pubblica e privata per le macchine virtuali Linux in Azure](../../virtual-machines/linux/mac-create-ssh-keys.md).
 
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
    
    È possibile usare questo token di accesso per l'autenticazione ad Azure Key Vault.  La successiva richiesta CURL mostra come leggere un segreto da Key Vault usando CURL e l'API REST di Key Vault.  È necessario l'URL dell'insieme di credenziali delle chiavi che si trova nella sezione **Informazioni di base** della pagina **Panoramica** dell'insieme di credenziali delle chiavi.  È necessario anche il token di accesso ottenuto alla chiamata precedente. 
        
    ```bash
    curl https://<YOUR-KEY-VAULT-URL>/secrets/<secret-name>?api-version=2016-10-01 -H "Authorization: Bearer <ACCESS TOKEN>" 
    ```
    
    La risposta avrà l'aspetto seguente: 
    
    ```bash
    {"value":"p@ssw0rd!","id":"https://mytestkeyvault.vault.azure.net/secrets/MyTestSecret/7c2204c6093c4d859bc5b9eff8f29050","attributes":{"enabled":true,"created":1505088747,"updated":1505088747,"recoveryLevel":"Purgeable"}} 
    ```
    
Dopo aver recuperato il segreto dall'insieme di credenziali delle chiavi, è possibile usarlo per eseguire l'autenticazione a un servizio che richiede un nome e una password.

## <a name="clean-up-resources"></a>Pulire le risorse

Se si vogliono pulire le risorse, visitare il [portale di Azure](https://portal.azure.com), selezionare **Gruppi di risorse**, individuare e selezionare il gruppo di risorse creato in questa esercitazione, ad esempio `mi-test`, quindi scegliere il comando **Elimina gruppo di risorse**.

In alternativa, è possibile eseguire questa operazione anche tramite [PowerShell o l'interfaccia della riga di comando](../../azure-resource-manager/management/delete-resource-group.md)

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come usare un'identità gestita assegnata dal sistema per una macchina virtuale Linux per accedere ad Azure Key Vault.  Per altre informazioni su Azure Key Vault, consultare:

> [!div class="nextstepaction"]
>[Azure Key Vault](../../key-vault/general/overview.md)