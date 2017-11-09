---
title: "Usare l'Identità del servizio gestito di una macchina virtuale Windows per accedere ad Azure Resource Manager"
description: "Esercitazione che illustra come usare un'Identità del servizio gestito di una macchina virtuale Windows per accedere ad Azure Resource Manager."
services: active-directory
documentationcenter: 
author: bryanla
manager: mbaldwin
editor: bryanla
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/24/2017
ms.author: elkuzmen
ms.openlocfilehash: 17c2e72603715b5704fa56e18f68d531a1e44a53
ms.sourcegitcommit: 76a3cbac40337ce88f41f9c21a388e21bbd9c13f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2017
---
# <a name="use-a-windows-vm-managed-service-identity-msi-to-access-resource-manager"></a>Usare un'identità del servizio gestita per una macchina virtuale Windows per accedere a Gestione risorse

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

Questa esercitazione illustra come abilitare l'Identità del servizio gestito (MSI, Managed Service Identity) per una macchina virtuale Windows. È quindi possibile usare tale identità per accedere all'API di Azure Resource Manager. Le identità del servizio gestito vengono gestite automaticamente da Azure e consentono di eseguire l'autenticazione ai servizi che supportano l'autenticazione di Azure AD senza la necessità di inserire le credenziali nel codice. Si apprenderà come:

> [!div class="checklist"]
> * Abilitare Identità del servizio gestito in una macchina virtuale Windows 
> * Concedere alla macchina virtuale l'accesso a un gruppo di risorse in Azure Resource Manager 
> * Ottenere un token di accesso usando l'identità della macchina virtuale e usarlo per chiamare Azure Resource Manager


Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="sign-in-to-azure"></a>Accedere ad Azure
Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-windows-virtual-machine-in-a-new-resource-group"></a>Creare una macchina virtuale Windows in un nuovo gruppo di risorse

Per questa esercitazione si creerà una nuova macchina virtuale Windows,  ma è anche possibile abilitare l'identità del servizio gestito in una macchina virtuale esistente.

1.  Fare clic sul pulsante **Nuovo** nell'angolo superiore sinistro del portale di Azure.
2.  Selezionare **Calcolo** e quindi **Windows Server 2016 Datacenter**. 
3.  Immettere le informazioni relative alla macchina virtuale. I valori di **Nome utente** e **Password** creati in questa schermata sono le credenziali usate per accedere alla macchina virtuale.
4.  Dall'elenco a discesa **Sottoscrizione** selezionare la sottoscrizione corretta per la macchina virtuale.
5.  Per selezionare un nuovo **Gruppo di risorse** in cui creare la macchina virtuale, scegliere **Crea nuovo**. Al termine fare clic su **OK**.
6.  Selezionare la dimensione della macchina virtuale. Per visualizzare altre dimensioni, selezionare **Visualizza tutto** o modificare il filtro **Supported disk type** (Tipo di disco supportato). Nella pagina delle impostazioni mantenere le impostazioni predefinite e fare clic su **OK**.

    ![Testo immagine alt](media/msi-tutorial-windows-vm-access-arm/msi-windows-vm.png)

## <a name="enable-msi-on-your-vm"></a>Abilitare identità del servizio gestito nella macchina virtuale 

Un'Identità del servizio gestito per una macchina virtuale consente di ottenere i token di accesso da Azure AD senza dover inserire le credenziali nel codice. L'abilitazione dell'Identità del servizio gestito indica ad Azure di creare un'identità gestita per la macchina virtuale. A livello sottostante, quando si abilita Identità del servizio gestito, si eseguono due operazioni, ovvero si installa l'estensione della macchina virtuale per l'Identità del servizio gestito nella macchina virtuale e si abilita l'Identità del servizio gestito in Azure Resource Manager.

1.  Selezionare la **macchina virtuale** in cui si vuole abilitare identità del servizio gestito.  
2.  Nella barra di spostamento a sinistra fare clic su **Configurazione**. 
3.  Viene visualizzato **Managed Service Identity** (identità del servizio gestito). Per registrare e abilitare identità del servizio gestita, scegliere **Sì**. Se si vuole disabilitare questa funzionalità, scegliere No. 
4.  Assicurarsi di fare clic su **Salva** per salvare la configurazione.  
    ![Testo immagine alt](media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. Per verificare le estensioni installate nella macchina virtuale, fare clic su **Estensioni**. Se Identità del servizio gestita è abilitata, nell'elenco sarà inclusa la voce **ManagedIdentityExtensionforWindows**.

    ![Testo immagine alt](media/msi-tutorial-windows-vm-access-arm/msi-windows-extension.png)

## <a name="grant-your-vm-access-to-a-resource-group-in-resource-manager"></a>Concedere alla macchina virtuale l'accesso a un gruppo di risorse in Gestione risorse
Usando Identità del servizio gestito, il codice può ottenere i token di accesso per autenticarsi nelle risorse che supportano l'autenticazione di Azure AD.  Azure Resource Manager supporta l'autenticazione di Azure AD.  In primo luogo, è necessario concedere all'identità della macchina virtuale l'accesso a una risorsa di Gestione risorse, in questo caso al gruppo di risorse che contiene la macchina virtuale.  

1.  Passare alla scheda **Gruppo di risorse**. 
2.  Selezionare il **Gruppo di risorse** specifico creato per la **macchina virtuale Windows**. 
3.  Passare a **Controllo di accesso (IAM)** nel pannello a sinistra. 
4.  Fare quindi clic su **Aggiungi** per aggiungere una nuova assegnazione di ruolo alla **macchina virtuale Windows**.  Scegliere **Ruolo** come **Lettore**. 
5.  Dall'elenco a discesa successivo **Assegna accesso a** selezionare la risorsa **Macchina virtuale**. 
6.  Verificare quindi che nell'elenco a discesa **Sottoscrizione** sia elencata la sottoscrizione corretta. In **Gruppo di risorse** selezionare **Tutti i gruppi di risorse**. 
7.  Scegliere infine la macchina virtuale Windows nell'elenco a discesa **Seleziona** e fare clic su **Salva**.

    ![Testo immagine alt](media/msi-tutorial-windows-vm-access-arm/msi-windows-permissions.png)

## <a name="get-an-access-token-using-the-vm-identity-and-use-it-to-call-azure-resource-manager"></a>Ottenere un token di accesso usando l'identità della macchina virtuale e usarlo per chiamare Azure Resource Manager 

In questa sezione è necessario usare **PowerShell**.  Se non è installato, scaricarlo da [qui](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-4.3.1). 

1.  Nel portale passare a **Macchine virtuali**, selezionare la macchina virtuale Windows e in **Panoramica** fare clic su **Connetti**. 
2.  In **Nome utente** e **Password** immettere i valori specificati al momento della creazione della macchina virtuale Windows. 
3.  Ora che si è creata una **connessione Desktop remoto** con la macchina virtuale, aprire **PowerShell** nella sessione remota. 
4.  Usando Invoke-WebRequest di PowerShell, eseguire una richiesta all'endpoint locale di Identità del servizio gestito per ottenere un token di accesso per Azure Resource Manager.

    ```powershell
       $response = Invoke-WebRequest -Uri http://localhost:50342/oauth2/token -Method GET -Body @{resource="https://management.azure.com/"} -Headers @{Metadata="true"}
    ```
    
    > [!NOTE]
    > Il valore del parametro "risorsa" deve corrispondere esattamente a quello previsto da Azure AD. Quando si usa l'ID risorsa di Azure Resource Manager, è necessario includere la barra finale nell'URI.
    
    Estrarre la risposta completa, archiviata come stringa in formato JSON (JavaScript Object Notation) nell'oggetto $response. 
    
    ```powershell
    $content = $response.Content | ConvertFrom-Json
    ```
    Estrarre quindi il token di accesso dalla risposta.
    
    ```powershell
    $ArmToken = $content.access_token
    ```
    
    Chiamare infine Azure Resource Manager usando il token di accesso. In questo esempio Invoke-WebRequest di PowerShell viene usato anche per eseguire la chiamata ad Azure Resource Manager e includere il token di accesso nell'intestazione dell'autorizzazione.
    
    ```powershell
    (Invoke-WebRequest -Uri https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>?api-version=2016-06-01 -Method GET -ContentType "application/json" -Headers @{ Authorization ="Bearer $ArmToken"}).content
    ```
    > [!NOTE] 
    > L'URL fa distinzione tra maiuscole e minuscole. Accertarsi quindi di usare la stessa combinazione di maiuscole e minuscole usata in precedenza per il nome del gruppo di risorse, facendo attenzione alla maiuscola "G" in "resourceGroup".
        
    Il comando seguente restituisce le informazioni del gruppo di risorse:

    ```powershell
    {"id":"/subscriptions/98f51385-2edc-4b79-bed9-7718de4cb861/resourceGroups/DevTest","name":"DevTest","location":"westus","properties":{"provisioningState":"Succeeded"}}
    ```

## <a name="related-content"></a>Contenuti correlati

- Per una panoramica dell'identità di servizio gestito, vedere [Panoramica dell'identità di servizio gestito](../active-directory/msi-overview.md).

Usare la sezione dei commenti seguente per fornire commenti e suggerimenti utili per migliorare e organizzare i contenuti disponibili.

