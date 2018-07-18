---
title: Creare un'entità servizio per Azure Stack | Microsoft Docs
description: Viene descritto come creare una nuova entità servizio che può essere utilizzata con il controllo di accesso basato sui ruoli in Azure Resource Manager per gestire l'accesso alle risorse.
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
ms.date: 06/21/2018
ms.author: mabrigg
ms.openlocfilehash: 0db3f19c99b786d7f32f126ad7bd70efc999a751
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/03/2018
ms.locfileid: "37444273"
---
# <a name="provide-applications-access-to-azure-stack"></a>Fornisci alle applicazioni l'accesso ad Azure Stack

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

Quando un'applicazione deve accedere a distribuire o configurare le risorse tramite Azure Resource Manager in Azure Stack, si crea un'entità servizio, ovvero una credenziale per l'applicazione.  È quindi possibile delegare solo le autorizzazioni necessarie per tale entità servizio.  

Ad esempio, potrebbe essere uno strumento di Gestione configurazione che usa Azure Resource Manager per creare un inventario delle risorse di Azure.  In questo scenario, è possibile creare un'entità servizio, concedere al ruolo lettore a tale entità servizio e limitare lo strumento di Gestione configurazione per l'accesso di sola lettura. 

Le entità servizio sono preferibili per l'esecuzione dell'app con le proprie credenziali perché:

* È possibile assegnare autorizzazioni all'entità che sono diverse dalle proprie autorizzazioni dell'account servizio. Tali autorizzazioni sono in genere limitate alle specifiche operazioni che devono essere eseguite dall'app.
* Non è necessario modificare le credenziali dell'app in caso di cambiamento delle responsabilità dell'utente.
* È possibile usare un certificato per automatizzare l'autenticazione in caso di esecuzione di uno script automatico.  

## <a name="getting-started"></a>Introduzione

A seconda del modo in cui è stato distribuito Azure Stack, è necessario creare un'entità servizio.  Questo documento illustra come creare un'entità servizio per entrambi [Azure Active Directory (Azure AD)](azure-stack-create-service-principals.md#create-service-principal-for-azure-ad) e [Active Directory Federation Services(AD FS)](azure-stack-create-service-principals.md#create-service-principal-for-ad-fs).  Dopo aver creato l'entità servizio, vengono usati per una serie di passaggi comuni per AD FS sia Azure Active Directory [delegare le autorizzazioni](azure-stack-create-service-principals.md#assign-role-to-service-principal) al ruolo.     

## <a name="create-service-principal-for-azure-ad"></a>Creare un'entità servizio per Azure AD

Se è stato distribuito Azure Stack tramite Azure AD come archivio identità, è possibile creare le entità servizio, proprio come per Azure.  Questa sezione illustra come eseguire la procedura tramite il portale.  Verificare di disporre il [autorizzazioni di Azure AD necessarie](../azure-resource-manager/resource-group-create-service-principal-portal.md#required-permissions) prima di iniziare.

### <a name="create-service-principal"></a>Creare un'entità servizio
In questa sezione si crea un'applicazione (entità servizio) in Azure AD che rappresenta l'applicazione.

1. Accedi all'Account di Azure tramite il [portale di Azure](https://portal.azure.com).
2. Selezionare **Azure Active Directory** > **registrazioni per l'App** > **Add**   
3. Specificare un nome e un URL per l'applicazione. Selezionare **App Web/API** o **Nativa** come tipo di applicazione da creare. Dopo aver impostato i valori selezionare **Crea**.

È stato creato un'entità servizio per l'applicazione.

### <a name="get-credentials"></a>Ottieni credenziali
Durante l'accesso a livello di codice, si usa l'ID per l'applicazione e per un'app Web / API, una chiave di autenticazione. Per ottenere questi valori eseguire la procedura seguente:

1. Da **Registrazioni dell'app** in Active Directory selezionare l'applicazione.

2. Copiare l'**ID applicazione** e archiviarlo nel codice dell'applicazione. Le applicazioni nella sezione delle [applicazioni di esempio](#sample-applications) indicano questo valore come ID client.

     ![ID CLIENT](./media/azure-stack-create-service-principal/image12.png)
3. Per generare una chiave di autenticazione per un'app Web / API, selezionare **le impostazioni** > **chiavi**. 

4. Specificare una descrizione e una durata per la chiave. Al termine scegliere **Salva**.

Dopo aver salvato la chiave viene visualizzato il valore della chiave. Copiare il valore in quanto non sarà possibile recuperare la chiave in seguito. È fornire il valore della chiave con l'ID applicazione per accedere come l'applicazione. Salvare il valore della chiave in una posizione in cui l'applicazione possa recuperarlo.

![chiave salvata](./media/azure-stack-create-service-principal/image15.png)


Al termine dell'operazione, passare alla [assegnazione di un ruolo applicazione](azure-stack-create-service-principals.md#assign-role-to-service-principal).

## <a name="create-service-principal-for-ad-fs"></a>Creare un'entità servizio per AD FS
Se è stato distribuito Azure Stack con AD FS, è possibile usare PowerShell per creare un'entità servizio, assegnare un ruolo per l'accesso e accesso da PowerShell usando tale identità.

Lo script viene eseguito dall'endpoint con privilegi in una macchina virtuale ERCS.


Requirements:
- È necessario un certificato.

**Parameters**

Le informazioni seguenti sono necessarie come input per i parametri di automazione:


|Parametro|DESCRIZIONE|Esempio|
|---------|---------|---------|
|NOME|Nome per l'account del nome SPN|MyAPP|
|ClientCertificates|Matrice di oggetti certificato|X509 certificato|
|ClientRedirectUris<br>Facoltativa|URI di reindirizzamento dell'applicazione|         |

**Esempio**

1. Aprire una sessione di Windows PowerShell con privilegi elevata ed eseguire i comandi seguenti:

   > [!NOTE]
   > Questo esempio crea un certificato autofirmato. Quando si eseguono questi comandi in una distribuzione di produzione, usare Get-Certificate per recuperare l'oggetto certificato per il certificato da usare.

   ```PowerShell  
    # Credential for accessing the ERCS PrivilegedEndpoint typically domain\cloudadmin
    $creds = Get-Credential

    # Creating a PSSession to the ERCS PrivilegedEndpoint
    $session = New-PSSession -ComputerName <ERCS IP> -ConfigurationName PrivilegedEndpoint -Credential $creds

    # This produces a self signed cert for testing purposes.  It is prefered to use a managed certificate for this.
    $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=<yourappname>" -KeySpec KeyExchange

    $ServicePrincipal = Invoke-Command -Session $session -ScriptBlock { New-GraphApplication -Name '<yourappname>' -ClientCertificates $using:cert}
    $AzureStackInfo = Invoke-Command -Session $session -ScriptBlock { get-azurestackstampinformation }
    $session|remove-pssession

    # For Azure Stack development kit, this value is set to https://management.local.azurestack.external. We will read this from the AzureStackStampInformation output of the ERCS VM.
    $ArmEndpoint = $AzureStackInfo.TenantExternalEndpoints.TenantResourceManager

    # For Azure Stack development kit, this value is set to https://graph.local.azurestack.external/. We will read this from the AzureStackStampInformation output of the ERCS VM.
    $GraphAudience = "https://graph." + $AzureStackInfo.ExternalDomainFQDN + "/"

    # TenantID for the stamp. We will read this from the AzureStackStampInformation output of the ERCS VM.
    $TenantID = $AzureStackInfo.AADTenantID

    # Register an AzureRM environment that targets your Azure Stack instance
    Add-AzureRMEnvironment `
    -Name "AzureStackUser" `
    -ArmEndpoint $ArmEndpoint

    # Set the GraphEndpointResourceId value
    Set-AzureRmEnvironment `
    -Name "AzureStackUser" `
    -GraphAudience $GraphAudience `
    -EnableAdfsAuthentication:$true

    Add-AzureRmAccount -EnvironmentName "azurestackuser" `
    -ServicePrincipal `
    -CertificateThumbprint $ServicePrincipal.Thumbprint `
    -ApplicationId $ServicePrincipal.ClientId `
    -TenantId $TenantID
   ```

2. Al termine del processo di automazione, vengono visualizzati i dettagli necessari per usare il nome SPN. 

   Ad esempio: 

   ```
   ApplicationIdentifier : S-1-5-21-1512385356-3796245103-1243299919-1356
   ClientId              : 3c87e710-9f91-420b-b009-31fa9e430145
   Thumbprint            : 30202C11BE6864437B64CE36C8D988442082A0F1
   ApplicationName       : Azurestack-MyApp-c30febe7-1311-4fd8-9077-3d869db28342
   PSComputerName        : azs-ercs01
   RunspaceId            : a78c76bb-8cae-4db4-a45a-c1420613e01b
   ```

### <a name="assign-a-role"></a>Assegnare un ruolo
Dopo aver creato l'entità servizio, è necessario [assegnarlo a un ruolo](azure-stack-create-service-principals.md#assign-role-to-service-principal)

### <a name="sign-in-through-powershell"></a>Eseguire l'accesso tramite PowerShell
Dopo aver assegnato un ruolo, è possibile accedere ad Azure Stack usando l'entità servizio con il comando seguente:

```powershell
Add-AzureRmAccount -EnvironmentName "<AzureStackEnvironmentName>" `
 -ServicePrincipal `
 -CertificateThumbprint $servicePrincipal.Thumbprint `
 -ApplicationId $servicePrincipal.ClientId ` 
 -TenantId $directoryTenantId
```

## <a name="assign-role-to-service-principal"></a>Assegnazione di ruolo all'entità servizio
Per accedere alle risorse della propria sottoscrizione è necessario assegnare l'applicazione a un ruolo. Decidere quale ruolo rappresenti le autorizzazioni appropriate per l'applicazione. Per informazioni sui ruoli disponibili, vedere [Controllo degli accessi in base al ruolo: ruoli predefiniti](../role-based-access-control/built-in-roles.md).

È possibile impostare l'ambito al livello della sottoscrizione, del gruppo di risorse o della risorsa. Le autorizzazioni vengono ereditate a livelli inferiori dell'ambito. Se ad esempio si aggiunge un'applicazione al ruolo Lettore per un gruppo di risorse, l'applicazione può leggere il gruppo di risorse e le risorse in esso contenute.

1. Nel portale di Azure Stack, passare al livello dell'ambito che si desidera assegnare l'applicazione. Ad esempio, per assegnare un ruolo a un ambito della sottoscrizione, selezionare **Sottoscrizioni**. In alternativa è possibile selezionare una risorsa o un gruppo di risorse.

2. Selezionare la sottoscrizione specifica (risorsa o un gruppo di risorse) a cui assegnare l'applicazione.

     ![selezionare la sottoscrizione per l'assegnazione](./media/azure-stack-create-service-principal/image16.png)

3. Selezionare **Controllo di accesso (IAM)**.

     ![selezionare accesso](./media/azure-stack-create-service-principal/image17.png)

4. Selezionare **Aggiungi**.

5. Selezionare il ruolo che si desidera assegnare all'applicazione.

6. Cercare l'applicazione e selezionarla.

7. Selezionare **OK** per completare l'assegnazione del ruolo. L'applicazione ora compare nell'elenco degli utenti assegnati a un ruolo per quell'ambito.

Ora che è stato creato un'entità servizio e assegnato un ruolo, è possibile iniziare a usare questo all'interno dell'applicazione per accedere alle risorse di Azure Stack.  

## <a name="next-steps"></a>Passaggi successivi

[Aggiungere utenti ad ad FS](azure-stack-add-users-adfs.md)
[gestire le autorizzazioni utente](azure-stack-manage-permissions.md)
