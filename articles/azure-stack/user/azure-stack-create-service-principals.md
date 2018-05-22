---
title: Creare un'entità servizio per lo Stack di Azure | Documenti Microsoft
description: Viene descritto come creare un'entità servizio che può essere utilizzata con il controllo di accesso basata su ruoli in Azure Resource Manager per gestire l'accesso alle risorse.
services: azure-resource-manager
documentationcenter: na
author: mattbriggs
manager: femila
ms.assetid: 7068617b-ac5e-47b3-a1de-a18c918297b6
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/27/2018
ms.author: mabrigg
ms.openlocfilehash: de5712fd7b48a759b366f5b9808bbbefc6e305cd
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/10/2018
---
# <a name="give-applications-access-to-azure-stack-resources-by-creating-service-principals"></a>Consentono alle applicazioni di accedere a risorse di Azure Stack creando le entità servizio

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

È possibile assegnare un'applicazione l'accesso alle risorse di Azure Stack mediante la creazione di un servizio principale che usa Azure Resource Manager. Un'entità servizio consente di delegare le autorizzazioni specifiche mediante [controllo di accesso basata su ruoli](azure-stack-manage-permissions.md).

Come procedura consigliata, è necessario utilizzare le entità servizio per le applicazioni. Le entità servizio sono preferibili per l'esecuzione di un'applicazione utilizzando le proprie credenziali per i motivi seguenti:

* È possibile assegnare autorizzazioni per l'entità che sono diverse dalle proprie autorizzazioni dell'account servizio. In genere, le autorizzazioni all'entità servizio sono limitate ai esattamente ciò che l'app deve eseguire.
* Non è necessario modificare le credenziali dell'applicazione se il ruolo o la responsabilità di modifica.
* È possibile utilizzare un certificato per automatizzare l'autenticazione quando si esegue uno script automatico.

## <a name="example-scenario"></a>Scenario di esempio

Si dispone di un'app di gestione di configurazione che deve eseguire l'inventario delle risorse di Azure con Azure Resource Manager. È possibile creare un servizio principale e assegnarlo al ruolo di lettore. Questo ruolo fornisce l'accesso di sola lettura di app alle risorse di Azure.

## <a name="getting-started"></a>Introduzione

Utilizzare i passaggi in questo articolo come guida per:

* Creare un'entità servizio per l'app.
* Registrare l'app e creare una chiave di autenticazione.
* Assegnare l'app a un ruolo.

La configurazione di Active Directory per lo Stack di Azure determina come creare un'entità servizio. Selezionare una delle opzioni seguenti:

* Creare un servizio principale per [Azure Active Directory (Azure AD)](azure-stack-create-service-principals.md#create-service-principal-for-azure-ad).
* Creare un servizio principale per [Active Directory Federation Services (ADFS)](azure-stack-create-service-principals.md#create-service-principal-for-ad-fs).

I passaggi per l'assegnazione di un'entità servizio a un ruolo lo stesso per Azure Active Directory e AD FS. Dopo aver creato l'entità servizio, è possibile [delegare le autorizzazioni](azure-stack-create-service-principals.md#assign-role-to-service-principal) tramite l'assegnazione a un ruolo.

## <a name="create-a-service-principal-for-azure-ad"></a>Creare un'entità servizio per Azure AD

Se lo Stack di Azure Usa Azure AD come archivio identità, è possibile creare un servizio principale usando la stessa procedura come in Azure, tramite il portale di Azure.

>[!NOTE]
Verificare di avere il [Azure AD le autorizzazioni necessarie](../../azure-resource-manager/resource-group-create-service-principal-portal.md#required-permissions) prima di iniziare a creare un'entità servizio.

### <a name="create-service-principal"></a>Creare un'entità servizio

Per creare un'entità servizio per l'applicazione:

1. Accedere all'account di Azure tramite il [portale di Azure](https://portal.azure.com).
2. Selezionare **Azure Active Directory** > **registrazioni di App** > **Aggiungi**.
3. Specificare un nome e un URL per l'applicazione. Selezionare **App Web/API** o **Nativa** come tipo di applicazione da creare. Dopo aver impostato i valori selezionare **Crea**.

### <a name="get-credentials"></a>Ottieni credenziali

Durante l'accesso a livello di codice, usare l'ID per l'applicazione e una chiave di autenticazione. Per ottenere questi valori:

1. Da **Registrazioni dell'app** in Active Directory selezionare l'applicazione.

2. Copiare l'**ID applicazione** e archiviarlo nel codice dell'applicazione. Le applicazioni nel [applicazioni di esempio](#sample-applications) utilizzare **id client** quando si fa riferimento per il **ID applicazione**.

     ![ID dell'applicazione per l'applicazione](./media/azure-stack-create-service-principal/image12.png)
3. Per generare una chiave di autenticazione selezionare **Chiavi**.

4. Specificare una descrizione e una durata per la chiave. Al termine scegliere **Salva**.

>[!IMPORTANT]
Dopo aver salvato la chiave, la chiave **valore** viene visualizzato. Annotare questo valore poiché non è possibile recuperare la chiave in un secondo momento. Salvare il valore della chiave in una posizione in cui l'applicazione possa recuperarlo.

![Avviso di valore della chiave per la chiave salvata.](./media/azure-stack-create-service-principal/image15.png)

Il passaggio finale consiste [assegnazione di un ruolo applicazione](azure-stack-create-service-principals.md#assign-role-to-service-principal).

## <a name="create-service-principal-for-ad-fs"></a>Creare l'entità servizio per ADFS

Se è stato distribuito Azure Stack utilizza AD FS come archivio di identità, è possibile utilizzare PowerShell per le attività seguenti:

* Creare un'entità servizio.
* Assegnare un'entità servizio a un ruolo.
* Accedere utilizzando l'identità dell'entità servizio.

### <a name="before-you-begin"></a>Prima di iniziare

[Scaricare gli strumenti di Azure Stack richiesti nel computer locale.](azure-stack-powershell-download.md)

### <a name="import-the-identity-powershell-module"></a>Importare il modulo PowerShell di identità

Passare alla cartella di download per gli strumenti di Azure Stack e importare il modulo PowerShell di identità usando il comando seguente:

```PowerShell
Import-Module .\Identity\AzureStack.Identity.psm1
```

Quando si importa il modulo di identità, è possibile che venga visualizzato questo messaggio di errore: "AzureStack.Connect.psm1 non è firmato digitalmente. Lo script non verrà eseguita nel sistema".

Per risolvere questo problema, è necessario configurare i criteri di esecuzione per consentire l'esecuzione dello script. Per impostare i criteri di esecuzione, eseguire il comando seguente in una sessione di PowerShell con privilegi elevata:

```PowerShell
Set-ExecutionPolicy Unrestricted
```

### <a name="create-the-service-principal"></a>Creare l'entità servizio

È possibile creare un'entità servizio eseguendo il comando seguente, assicurandosi di aggiornare il **DisplayName** parametro:

```powershell
$servicePrincipal = New-AzSADGraphServicePrincipal `
 -DisplayName "<YourServicePrincipalName>" `
 -AdminCredential $(Get-Credential) `
 -AdfsMachineName "AZS-ADFS01" `
 -Verbose

```

### <a name="assign-a-role"></a>Assegnare un ruolo

Dopo aver creato l'entità servizio, è necessario [assegnarlo a un ruolo](azure-stack-create-service-principals.md#assign-role-to-service-principal).

### <a name="sign-in-using-powershell"></a>Accedi con PowerShell

È possibile accedere allo Stack di Azure eseguendo il comando seguente, assicurandosi di aggiornare il **EnvironmentName** parametro con il nome dell'app:

```powershell
Add-AzureRmAccount -EnvironmentName "<AzureStackEnvironmentName>" `
 -ServicePrincipal `
 -CertificateThumbprint $servicePrincipal.Thumbprint `
 -ApplicationId $servicePrincipal.ApplicationId `
 -TenantId $directoryTenantId
```

## <a name="assign-the-service-principal-to-a-role"></a>Assegnare l'entità servizio a un ruolo

Per accedere alle risorse della propria sottoscrizione è necessario assegnare l'applicazione a un ruolo. Decidere quale ruolo rappresenti le autorizzazioni appropriate per l'applicazione. Per informazioni sui ruoli disponibili, vedere [Controllo degli accessi in base al ruolo: ruoli predefiniti](../../role-based-access-control/built-in-roles.md).

>[!NOTE]
È possibile impostare l'ambito del ruolo a livello di una sottoscrizione, un gruppo di risorse o una risorsa. Le autorizzazioni vengono ereditate a livelli inferiori dell'ambito. Ad esempio, un'app con il ruolo di lettore per un gruppo di risorse significa che l'app può leggere tutte le risorse nel gruppo di risorse.

Utilizzare la procedura seguente come guida per l'assegnazione di un ruolo a un'entità servizio.

1. Nel portale di Azure Stack, passare al livello di ambito che si desidera assegnare l'applicazione. Ad esempio, per assegnare un ruolo a un ambito della sottoscrizione, selezionare **Sottoscrizioni**.

2. Selezionare la sottoscrizione per assegnare l'applicazione. In questo esempio, la sottoscrizione è Visual Studio Enterprise.

     ![Selezionare la sottoscrizione di Visual Studio Enterprise per assegnazione](./media/azure-stack-create-service-principal/image16.png)

3. Selezionare **controllo di accesso (IAM)** per la sottoscrizione.

     ![Selezionare il controllo di accesso](./media/azure-stack-create-service-principal/image17.png)

4. Selezionare **Aggiungi**.

5. Selezionare il ruolo che si desidera assegnare all'applicazione.

6. Cercare l'applicazione e selezionarla.

7. Selezionare **OK** per completare l'assegnazione del ruolo. È possibile visualizzare l'applicazione nell'elenco di utenti assegnati a un ruolo per tale ambito.

Ora che hai creato un'entità servizio ed assegnato un ruolo, l'applicazione può accedere alle risorse di Azure Stack.

## <a name="next-steps"></a>Passaggi successivi

[Gestire le autorizzazioni utente](azure-stack-manage-permissions.md)
