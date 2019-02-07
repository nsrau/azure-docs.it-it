---
title: Gestire un'entità servizio per Azure Stack | Microsoft Docs
description: Viene descritto come gestire una nuova entità servizio che può essere utilizzata con il controllo di accesso basato sui ruoli in Azure Resource Manager per gestire l'accesso alle risorse.
services: azure-resource-manager
documentationcenter: na
author: sethmanheim
manager: femila
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/18/2018
ms.author: sethm
ms.lastreviewed: 12/18/2018
ms.openlocfilehash: 3c36bca12a16a796a964c4447b47265eecd756be
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/07/2019
ms.locfileid: "55809249"
---
# <a name="provide-applications-access-to-azure-stack"></a>Fornisci alle applicazioni l'accesso ad Azure Stack

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

Quando un'applicazione deve accedere a distribuire o configurare le risorse tramite Azure Resource Manager in Azure Stack, si crea un'entità servizio, ovvero una credenziale per l'applicazione. È quindi possibile delegare solo le autorizzazioni necessarie per tale entità servizio.  

Ad esempio, potrebbe essere uno strumento di Gestione configurazione che usa Azure Resource Manager per creare un inventario delle risorse di Azure. In questo scenario, è possibile creare un'entità servizio, concedere al ruolo lettore a tale entità servizio e limitare lo strumento di Gestione configurazione per l'accesso di sola lettura. 

Le entità servizio sono preferibili per l'esecuzione dell'app con le proprie credenziali perché:

 - È possibile assegnare autorizzazioni all'entità che sono diverse dalle proprie autorizzazioni dell'account servizio. Tali autorizzazioni sono in genere limitate alle specifiche operazioni che devono essere eseguite dall'app.
 - Non è necessario modificare le credenziali dell'app in caso di cambiamento delle responsabilità dell'utente.
 - È possibile usare un certificato per automatizzare l'autenticazione in caso di esecuzione di uno script automatico.  

## <a name="getting-started"></a>Introduzione

A seconda del modo in cui è stato distribuito Azure Stack, è necessario creare un'entità servizio. Questo documento descrive la creazione di un servizio principale per:

- Azure Active Directory (Azure AD). Azure AD è una directory di multi-tenant, basato sul cloud e il servizio di gestione di identità. È possibile usare Azure AD con un connesse di Azure Stack.
- Active Directory Federation Services (ADFS). ADFS fornisce federazione delle identità semplificate e protette e funzionalità di single sign-on (SSO) Web. È possibile usare AD FS con istanze di Azure Stack connesse e disconnesse.

Dopo aver creato l'entità servizio, una serie di passaggi comuni per AD FS sia Azure Active Directory consentono di delegare le autorizzazioni al ruolo.

## <a name="manage-service-principal-for-azure-ad"></a>Gestire entità servizio per Azure AD

Se Azure Stack con Azure Active Directory (Azure AD) è stata distribuita come servizio di gestione di identità, è possibile creare le entità servizio, proprio come per Azure. Questa sezione illustra come eseguire la procedura tramite il portale. Verificare di disporre il [autorizzazioni di Azure AD necessarie](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions) prima di iniziare.

### <a name="create-service-principal"></a>Creare un'entità servizio

In questa sezione si crea un'applicazione (entità servizio) in Azure AD che rappresenta l'applicazione.

1. Accedere all'account di Azure tramite il [portale di Azure](https://portal.azure.com).
2. Selezionare **Azure Active Directory** > **registrazioni per l'App** > **registrazione nuova applicazione**
3. Specificare un nome e un URL per l'applicazione. Selezionare **App Web/API** o **Nativa** come tipo di applicazione da creare. Dopo aver impostato i valori selezionare **Crea**.

È stato creato un'entità servizio per l'applicazione.

### <a name="get-credentials"></a>Ottenere le credenziali

Durante l'accesso a livello di codice, si usa l'ID per l'applicazione e per un'app Web / API, una chiave di autenticazione. Per ottenere questi valori eseguire la procedura seguente:

1. Da **Registrazioni dell'app** in Active Directory selezionare l'applicazione.

2. Copiare l'**ID applicazione** e archiviarlo nel codice dell'applicazione. Le applicazioni nella sezione di applicazioni di esempio fare riferimento a questo valore come ID client.

     ![Client Id](./media/azure-stack-create-service-principal/image12.png)
3. Per generare una chiave di autenticazione per un'app Web / API, selezionare **le impostazioni** > **chiavi**. 

4. Specificare una descrizione e una durata per la chiave. Al termine scegliere **Salva**.

Dopo aver salvato la chiave viene visualizzato il valore della chiave. Copiare questo valore in blocco note o in un'altra posizione temporanea, perché non è possibile recuperare la chiave in un secondo momento. È fornire il valore della chiave con l'ID applicazione per accedere come l'applicazione. Store il valore della chiave in un luogo in cui l'applicazione possa recuperarlo.

![Chiave salvata](./media/azure-stack-create-service-principal/image15.png)

Al termine dell'operazione, è possibile assegnare un ruolo applicazione.

## <a name="manage-service-principal-for-ad-fs"></a>Gestire entità servizio per ADFS

Se è stato distribuito Azure Stack con Active Directory Federation Services (ADFS) come il servizio di gestione di identità, è possibile usare PowerShell per creare un'entità servizio, assegnare un ruolo per l'accesso e accedere con tale identità.

È possibile utilizzare uno dei due metodi per creare un'entità servizio con AD FS. È possibile:
 - [Creare un'entità servizio usando un certificato](azure-stack-create-service-principals.md#create-a-service-principal-using-a-certificate)
 - [Creare un'entità servizio usando un segreto client](azure-stack-create-service-principals.md#create-a-service-principal-using-a-client-secret)

Attività per la gestione di AD FS entità servizio.

| Type | Azione |
| --- | --- |
| Certificato di AD FS | [Creare](azure-stack-create-service-principals.md#create-a-service-principal-using-a-certificate) |
| Certificato di AD FS | [Aggiornamento](azure-stack-create-service-principals.md#update-certificate-for-service-principal-for-AD-FS) |
| Certificato di AD FS | [Rimuovi](azure-stack-create-service-principals.md#remove-a-service-principal-for-AD-FS) |
| Segreto Client di ADFS di Active Directory | [Creare](azure-stack-create-service-principals.md#create-a-service-principal-using-a-client-secret) |
| Segreto Client di ADFS di Active Directory | [Aggiornamento](azure-stack-create-service-principals.md#create-a-service-principal-using-a-client-secret) |
| Segreto Client di ADFS di Active Directory | [Rimuovi](azure-stack-create-service-principals.md##remove-a-service-principal-for-AD-FS) |

### <a name="create-a-service-principal-using-a-certificate"></a>Creare un'entità servizio usando un certificato

Quando si crea un'entità servizio quando si usa AD FS per l'identità, è possibile usare un certificato.

#### <a name="certificate"></a>Certificate

È necessario un certificato.

**Requisiti dei certificati**

 - Il Provider di servizio di crittografia (CSP) deve essere legacy provider della chiave.
 - Il formato del certificato deve essere nel file PFX, perché entrambe le chiavi pubbliche e private sono necessarie. Windows Server usano file con estensione pfx che contengono file di chiave pubblica (file del certificato SSL) e il file di chiave privato associato.
 - Per la produzione, il certificato deve essere emesso da un'autorità di certificazione interna o da un'autorità di certificazione pubblica. Se si usa un'autorità di certificazione pubblica, è necessario incluso l'autorità nell'immagine del sistema operativo base durante il programma Microsoft Root autorità attendibili. È possibile trovare l'elenco completo in [programma Microsoft Trusted Root Certificate: I partecipanti](https://gallery.technet.microsoft.com/Trusted-Root-Certificate-123665ca).
 - L'infrastruttura di Azure Stack deve avere accesso di rete in percorso di elenco di revoche di certificati (CRL) dell'autorità di certificazione pubblicato nel certificato. Il CRL deve essere un endpoint HTTP.

#### <a name="parameters"></a>Parametri

Le informazioni seguenti sono necessarie come input per i parametri di automazione:

|Parametro|DESCRIZIONE|Esempio|
|---------|---------|---------|
|NOME|Nome per l'account del nome SPN|MyAPP|
|ClientCertificates|Matrice di oggetti certificato|X509 certificato|
|ClientRedirectUris<br>Facoltativa|URI di reindirizzamento dell'applicazione|-|

#### <a name="use-powershell-to-create-a-service-principal"></a>Usare PowerShell per creare un'entità servizio

1. Aprire una sessione di Windows PowerShell con privilegi elevata ed eseguire i cmdlet seguenti:

   ```PowerShell  
    # Credential for accessing the ERCS PrivilegedEndpoint, typically domain\cloudadmin
    $Creds = Get-Credential

    # Creating a PSSession to the ERCS PrivilegedEndpoint
    $Session = New-PSSession -ComputerName <ERCS IP> -ConfigurationName PrivilegedEndpoint -Credential $Creds

    # If you have a managed certificate use the Get-Item command to retrieve your certificate from your certificate location.
    # If you don't want to use a managed certificate, you can produce a self signed cert for testing purposes: 
    # $Cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=<YourAppName>" -KeySpec KeyExchange
    $Cert = Get-Item "<YourCertificateLocation>"
    
    $ServicePrincipal = Invoke-Command -Session $Session -ScriptBlock {New-GraphApplication -Name '<YourAppName>' -ClientCertificates $using:cert}
    $AzureStackInfo = Invoke-Command -Session $Session -ScriptBlock {Get-AzureStackStampInformation}
    $Session | Remove-PSSession

    # For Azure Stack development kit, this value is set to https://management.local.azurestack.external. This is read from the AzureStackStampInformation output of the ERCS VM.
    $ArmEndpoint = $AzureStackInfo.TenantExternalEndpoints.TenantResourceManager

    # For Azure Stack development kit, this value is set to https://graph.local.azurestack.external/. This is read from the AzureStackStampInformation output of the ERCS VM.
    $GraphAudience = "https://graph." + $AzureStackInfo.ExternalDomainFQDN + "/"

    # TenantID for the stamp. This is read from the AzureStackStampInformation output of the ERCS VM.
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

    Add-AzureRmAccount -EnvironmentName "AzureStackUser" `
    -ServicePrincipal `
    -CertificateThumbprint $ServicePrincipal.Thumbprint `
    -ApplicationId $ServicePrincipal.ClientId `
    -TenantId $TenantID

    # Output the SPN details
    $ServicePrincipal

   ```
   > [!Note]  
   > Esclusivamente a scopo di convalida un certificato autofirmato può essere creato usando l'esempio seguente:

   ```PowerShell  
   $Cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=<yourappname>" -KeySpec KeyExchange
   ```


2. Al termine del processo di automazione, vengono visualizzati i dettagli necessari per usare il nome SPN. È consigliabile archiviare l'output per un uso successivo.

   Ad esempio: 

   ```shell
   ApplicationIdentifier : S-1-5-21-1512385356-3796245103-1243299919-1356
   ClientId              : 3c87e710-9f91-420b-b009-31fa9e430145
   Thumbprint            : 30202C11BE6864437B64CE36C8D988442082A0F1
   ApplicationName       : Azurestack-MyApp-c30febe7-1311-4fd8-9077-3d869db28342
   PSComputerName        : azs-ercs01
   RunspaceId            : a78c76bb-8cae-4db4-a45a-c1420613e01b
   ```

### <a name="update-certificate-for-service-principal-for-ad-fs"></a>Aggiornare il certificato per l'entità servizio per AD FS

Se è stato distribuito Azure Stack con AD FS, è possibile usare PowerShell per aggiornare il segreto per un'entità servizio.

Lo script viene eseguito dall'endpoint con privilegi in una macchina virtuale ERCS.

#### <a name="parameters"></a>Parametri

Le informazioni seguenti sono necessarie come input per i parametri di automazione:

|Parametro|DESCRIZIONE|Esempio|
|---------|---------|---------|
|NOME|Nome per l'account del nome SPN|MyAPP|
|ApplicationIdentifier|Identificatore univoco|S-1-5-21-1634563105-1224503876-2692824315-2119|
|ClientCertificate|Matrice di oggetti certificato|X509 certificato|

#### <a name="example-of-updating-service-principal-for-ad-fs"></a>Esempio di aggiornamento dell'entità servizio per AD FS

L'esempio crea un certificato autofirmato. Quando si eseguono i cmdlet in una distribuzione di produzione, usare [Get-Item](https://docs.microsoft.com/powershell/module/Microsoft.PowerShell.Management/Get-Item) per recuperare l'oggetto certificato per il certificato da usare.

1. Aprire una sessione di Windows PowerShell con privilegi elevata ed eseguire i cmdlet seguenti:

     ```powershell
          # Creating a PSSession to the ERCS PrivilegedEndpoint
          $Session = New-PSSession -ComputerName <ERCS IP> -ConfigurationName PrivilegedEndpoint -Credential $Creds

          # This produces a self signed cert for testing purposes. It is preferred to use a managed certificate for this.
          $NewCert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=<YourAppName>" -KeySpec KeyExchange

          $RemoveServicePrincipal = Invoke-Command -Session $Session -ScriptBlock {Set-GraphApplication -ApplicationIdentifier  S-1-5-21-1634563105-1224503876-2692824315-2120 -ClientCertificates $NewCert}

          $Session | Remove-PSSession
     ```

2. Al termine del processo di automazione, viene visualizzato il valore di identificazione personale aggiornati necessario per l'autenticazione del nome SPN.

     ```Shell  
          ClientId              : 
          Thumbprint            : AF22EE716909041055A01FE6C6F5C5CDE78948E9
          ApplicationName       : Azurestack-ThomasAPP-3e5dc4d2-d286-481c-89ba-57aa290a4818
          ClientSecret          : 
          RunspaceId            : a580f894-8f9b-40ee-aa10-77d4d142b4e5
     ```

### <a name="create-a-service-principal-using-a-client-secret"></a>Creare un'entità servizio usando un segreto client

Quando si crea un'entità servizio quando si usa AD FS per l'identità, è possibile usare un certificato. Si userà l'endpoint con privilegi per eseguire i cmdlet.

Questi script vengono eseguiti dall'endpoint con privilegi in una macchina virtuale ERCS. Per altre informazioni sul punto di fine con privilegi, vedere [usando l'endpoint con privilegi in Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-privileged-endpoint).

#### <a name="parameters"></a>Parametri

Le informazioni seguenti sono necessarie come input per i parametri di automazione:

| Parametro | DESCRIZIONE | Esempio |
|----------------------|--------------------------|---------|
| NOME | Nome per l'account del nome SPN | MyAPP |
| GenerateClientSecret | Creare un segreto |  |

#### <a name="use-the-ercs-privilegedendpoint-to-create-the-service-principal"></a>Usare il PrivilegedEndpoint ERCS per creare l'entità servizio

1. Aprire una sessione di Windows PowerShell con privilegi elevata ed eseguire i cmdlet seguenti:

     ```PowerShell  
      # Credential for accessing the ERCS PrivilegedEndpoint, typically domain\cloudadmin
     $Creds = Get-Credential

     # Creating a PSSession to the ERCS PrivilegedEndpoint
     $Session = New-PSSession -ComputerName <ERCS IP> -ConfigurationName PrivilegedEndpoint -Credential $Creds

     # Creating a SPN with a secre
     $ServicePrincipal = Invoke-Command -Session $Session -ScriptBlock {New-GraphApplication -Name '<YourAppName>' -GenerateClientSecret}
     $AzureStackInfo = Invoke-Command -Session $Session -ScriptBlock {Get-AzureStackStampInformation}
     $Session | Remove-PSSession

     # Output the SPN details
     $ServicePrincipal
     ```

2. Dopo l'esecuzione di cmdlet, la shell Visualizza i dettagli necessari per usare il nome SPN. Assicurarsi che archiviare il segreto client.

     ```PowerShell  
     ApplicationIdentifier : S-1-5-21-1634563105-1224503876-2692824315-2623
     ClientId              : 8e0ffd12-26c8-4178-a74b-f26bd28db601
     Thumbprint            : 
     ApplicationName       : Azurestack-YourApp-6967581b-497e-4f5a-87b5-0c8d01a9f146
     ClientSecret          : 6RUZLRoBw3EebMDgaWGiowCkoko5_j_ujIPjA8dS
     PSComputerName        : 192.168.200.224
     RunspaceId            : 286daaa1-c9a6-4176-a1a8-03f543f90998
     ```

#### <a name="update-client-secret-for-a-service-principal-for-ad-fs"></a>Aggiornare il segreto client per un'entità servizio per AD FS

Un nuovo segreto client viene automaticamente generato dal cmdlet di PowerShell.

Lo script viene eseguito dall'endpoint con privilegi in una macchina virtuale ERCS.

##### <a name="parameters"></a>Parametri

Le informazioni seguenti sono necessarie come input per i parametri di automazione:

| Parametro | DESCRIZIONE | Esempio |
|-----------------------|-----------------------------------------------------------------------------------------------------------|------------------------------------------------|
| ApplicationIdentifier | Identificatore univoco. | S-1-5-21-1634563105-1224503876-2692824315-2119 |
| ChangeClientSecret | Modifica il segreto client con un periodo di attivazione impostato su 2880 minuti in cui il vecchio master secret sia ancora valido. |  |
| ResetClientSecret | Modificare immediatamente il segreto client |  |

##### <a name="example-of-updating-a-client-secret-for-ad-fs"></a>Esempio di aggiornamento di un segreto client per AD FS

L'esempio Usa la **ResetClientSecret** parametro, che diventa immediatamente il segreto client.

1. Aprire una sessione di Windows PowerShell con privilegi elevata ed eseguire i cmdlet seguenti:

     ```PowerShell  
          # Creating a PSSession to the ERCS PrivilegedEndpoint
          $Session = New-PSSession -ComputerName <ERCS IP> -ConfigurationName PrivilegedEndpoint -Credential $Creds

          # This produces a self signed cert for testing purposes. It is preferred to use a managed certificate for this.
          $NewCert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=<YourAppName>" -KeySpec KeyExchange

          $UpdateServicePrincipal = Invoke-Command -Session $Session -ScriptBlock {Set-GraphApplication -ApplicationIdentifier  S-1-5-21-1634563105-1224503876-2692824315-2120 -ResetClientSecret}

          $Session | Remove-PSSession
     ```

2. Al termine del processo di automazione, viene visualizzato il segreto appena generato richiesto per l'autenticazione del nome SPN. Assicurarsi che si archivierà il nuovo segreto client.

     ```PowerShell  
          ApplicationIdentifier : S-1-5-21-1634563105-1224503876-2692824315-2120
          ClientId              :  
          Thumbprint            : 
          ApplicationName       : Azurestack-Yourapp-6967581b-497e-4f5a-87b5-0c8d01a9f146
          ClientSecret          : MKUNzeL6PwmlhWdHB59c25WDDZlJ1A6IWzwgv_Kn
          RunspaceId            : 6ed9f903-f1be-44e3-9fef-e7e0e3f48564
     ```

### <a name="remove-a-service-principal-for-ad-fs"></a>Rimuovere un'entità servizio per AD FS

Se è stato distribuito Azure Stack con AD FS, è possibile usare PowerShell per eliminare un'entità servizio.

Lo script viene eseguito dall'endpoint con privilegi in una macchina virtuale ERCS.

#### <a name="parameters"></a>Parametri

Le informazioni seguenti sono necessarie come input per i parametri di automazione:

|Parametro|DESCRIZIONE|Esempio|
|---------|---------|---------|
| Parametro | DESCRIZIONE | Esempio |
| ApplicationIdentifier | Identificatore univoco | S-1-5-21-1634563105-1224503876-2692824315-2119 |

> [!Note]  
> Per visualizzare un elenco di tutte le entità servizio esistente e l'identificatore dell'applicazione, il comando get-graphapplication può essere usato.

#### <a name="example-of-removing-the-service-principal-for-ad-fs"></a>Esempio di rimozione dell'entità servizio per ADFS

```powershell  
     Credential for accessing the ERCS PrivilegedEndpoint, typically domain\cloudadmin
     $Creds = Get-Credential

     # Creating a PSSession to the ERCS PrivilegedEndpoint
     $Session = New-PSSession -ComputerName <ERCS IP> -ConfigurationName PrivilegedEndpoint -Credential $Creds

     $UpdateServicePrincipal = Invoke-Command -Session $Session -ScriptBlock {Remove-GraphApplication -ApplicationIdentifier S-1-5-21-1634563105-1224503876-2692824315-2119}

     $Session | Remove-PSSession
```

## <a name="assign-a-role"></a>Assegnare un ruolo

Per accedere alle risorse della propria sottoscrizione è necessario assegnare l'applicazione a un ruolo. Decidere quale ruolo rappresenti le autorizzazioni appropriate per l'applicazione. Per informazioni sui ruoli disponibili, vedere [RBAC: ruoli predefiniti](../role-based-access-control/built-in-roles.md).

È possibile impostare l'ambito al livello della sottoscrizione, del gruppo di risorse o della risorsa. Le autorizzazioni vengono ereditate a livelli inferiori dell'ambito. Se ad esempio si aggiunge un'applicazione al ruolo Lettore per un gruppo di risorse, l'applicazione può leggere il gruppo di risorse e le risorse in esso contenute.

1. Nel portale di Azure Stack, passare al livello dell'ambito che si desidera assegnare l'applicazione. Ad esempio, per assegnare un ruolo a un ambito della sottoscrizione, selezionare **Sottoscrizioni**. In alternativa è possibile selezionare una risorsa o un gruppo di risorse.

2. Selezionare la sottoscrizione specifica (risorsa o un gruppo di risorse) a cui assegnare l'applicazione.

     ![Selezionare la sottoscrizione per l'assegnazione](./media/azure-stack-create-service-principal/image16.png)

3. Selezionare **Controllo di accesso (IAM)**.

     ![Selezionare l'accesso](./media/azure-stack-create-service-principal/image17.png)

4. Selezionare **Aggiungi assegnazione ruolo**.

5. Selezionare il ruolo che si desidera assegnare all'applicazione.

6. Cercare l'applicazione e selezionarla.

7. Selezionare **OK** per completare l'assegnazione del ruolo. L'applicazione ora compare nell'elenco degli utenti assegnati a un ruolo per quell'ambito.

Ora che è stato creato un'entità servizio e assegnato un ruolo, è possibile iniziare a usare questo all'interno dell'applicazione per accedere alle risorse di Azure Stack.  

## <a name="next-steps"></a>Passaggi successivi

[Aggiungere utenti ad AD FS](azure-stack-add-users-adfs.md)  
[Gestire le autorizzazioni utente](azure-stack-manage-permissions.md)  
[Documentazione di Azure Active Directory](https://docs.microsoft.com/azure/active-directory)  
[Active Directory Federation Services](https://docs.microsoft.com/windows-server/identity/active-directory-federation-services)
