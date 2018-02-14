---
title: "Creare un'entità servizio per lo Stack di Azure | Documenti Microsoft"
description: "Viene descritto come creare una nuova entità servizio che può essere utilizzata con il controllo di accesso basato sui ruoli in Gestione risorse di Azure per gestire l'accesso alle risorse."
services: azure-resource-manager
documentationcenter: na
author: heathl17
manager: byronr
ms.assetid: 7068617b-ac5e-47b3-a1de-a18c918297b6
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/25/2017
ms.author: helaw
ms.openlocfilehash: 058b01a37e2858801895fd22cf73dd6bd342ca04
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="provide-applications-access-to-azure-stack"></a>Fornire alle applicazioni di accedere allo Stack di Azure

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

Quando un'applicazione richiede l'accesso per distribuire o configurare le risorse tramite Gestione risorse di Azure nello Stack di Azure, si crea un servizio principale, ovvero una credenziale per l'applicazione.  È quindi possibile delegare solo le autorizzazioni necessarie a tale entità servizio.  

Ad esempio, potrebbe essere uno strumento di configurazione di gestione che usa Azure Resource Manager per creare un inventario delle risorse di Azure.  In questo scenario, è possibile creare un'entità servizio, concedere il ruolo di lettore a tale entità servizio e limitare lo strumento di Gestione configurazione per l'accesso in sola lettura. 

Le entità servizio sono preferibili per l'esecuzione dell'applicazione con le proprie credenziali perché:

* È possibile assegnare autorizzazioni per l'entità che sono diverse dalle proprie autorizzazioni dell'account servizio. Tali autorizzazioni sono in genere limitate alle specifiche operazioni che devono essere eseguite dall'app.
* Non è necessario modificare le credenziali dell'app in caso di cambiamento delle responsabilità dell'utente.
* È possibile usare un certificato per automatizzare l'autenticazione in caso di esecuzione di uno script automatico.  

## <a name="getting-started"></a>Introduzione

A seconda di come sono stati distribuiti dello Stack di Azure, è innanzitutto necessario creare un servizio principale.  Questo documento viene descritta la creazione di un'entità servizio per entrambi [Azure Active Directory (Azure AD)](azure-stack-create-service-principals.md#create-service-principal-for-azure-ad) e [Active Directory Federation Services(AD FS)](azure-stack-create-service-principals.md#create-service-principal-for-ad-fs).  Dopo aver creato l'entità servizio, un set di passi comuni sia ADFS e Azure Active Directory vengono utilizzati per [delegare le autorizzazioni](azure-stack-create-service-principals.md#assign-role-to-service-principal) al ruolo.     

## <a name="create-service-principal-for-azure-ad"></a>Creare l'entità servizio per Azure AD

Se è già distribuito Azure Stack mediante Azure AD come archivio di identità, è possibile creare le entità servizio, come se si trattasse di Azure.  In questa sezione viene illustrato come eseguire la procedura tramite il portale.  Verificare di disporre di [necessarie autorizzazioni di Azure AD](../../azure-resource-manager/resource-group-create-service-principal-portal.md#required-permissions) prima di iniziare.

### <a name="create-service-principal"></a>Creare un'entità servizio
In questa sezione si crea un'applicazione (entità servizio) in Azure AD che rappresenterà l'applicazione.

1. Accedere all'account di Azure tramite il [portale di Azure](https://portal.azure.com).
2. Selezionare **Azure Active Directory** > **registrazioni di App** > **Aggiungi**   
3. Specificare un nome e un URL per l'applicazione. Selezionare **App Web/API** o **Nativa** come tipo di applicazione da creare. Dopo aver impostato i valori selezionare **Crea**.

È stato creato un'entità servizio per l'applicazione.

### <a name="get-credentials"></a>Ottenere le credenziali
Durante l'accesso a livello di codice, utilizzare l'ID per l'applicazione e una chiave di autenticazione. Per ottenere questi valori eseguire la procedura seguente:

1. Da **Registrazioni dell'app** in Active Directory selezionare l'applicazione.

2. Copiare l'**ID applicazione** e archiviarlo nel codice dell'applicazione. Le applicazioni nella sezione delle [applicazioni di esempio](#sample-applications) indicano questo valore come ID client.

     ![ID CLIENT](./media/azure-stack-create-service-principal/image12.png)
3. Per generare una chiave di autenticazione selezionare **Chiavi**.

4. Specificare una descrizione e una durata per la chiave. Al termine scegliere **Salva**.

Dopo aver salvato la chiave viene visualizzato il valore della chiave. Copiare il valore in quanto non sarà possibile recuperare la chiave in seguito. Fornire il valore di chiave con l'ID applicazione di accedere come l'applicazione. Salvare il valore della chiave in una posizione in cui l'applicazione possa recuperarlo.

![chiave salvata](./media/azure-stack-create-service-principal/image15.png)


Al termine del processo, passare alla [l'assegnazione di un ruolo applicazione](azure-stack-create-service-principals.md#assign-role-to-service-principal).

## <a name="create-service-principal-for-ad-fs"></a>Creare l'entità servizio per ADFS
Se è stato distribuito Azure Stack con AD FS, è possibile utilizzare PowerShell per creare un'entità servizio, assegnare un ruolo per l'accesso e accesso da PowerShell mediante tale identità.

### <a name="before-you-begin"></a>Prima di iniziare

[Scaricare gli strumenti necessari per l'utilizzo con lo Stack di Azure nel computer locale.](azure-stack-powershell-download.md)

### <a name="import-the-identity-powershell-module"></a>Importare il modulo PowerShell di identità
Dopo aver scaricato gli strumenti, passare alla cartella di download e importare il modulo PowerShell di identità tramite il comando seguente:

```PowerShell
Import-Module .\Identity\AzureStack.Identity.psm1
```

Quando si importa il modulo, si potrebbe ricevere un messaggio di errore "AzureStack.Connect.psm1 non è firmato digitalmente. Lo script non verrà eseguita nel sistema". Per risolvere questo problema, è possibile impostare criteri di esecuzione per consentire l'esecuzione dello script con il comando seguente in una sessione di PowerShell con privilegi elevata:

```PowerShell
Set-ExecutionPolicy Unrestricted
```

### <a name="create-the-service-principal"></a>Creare l'entità servizio
È possibile creare un'entità servizio eseguendo il comando seguente, assicurandosi di aggiornare il *DisplayName* parametro:
```powershell
$servicePrincipal = New-AzSADGraphServicePrincipal `
 -DisplayName "<YourServicePrincipalName>" `
 -AdminCredential $(Get-Credential) `
 -AdfsMachineName "AZS-ADFS01" `
 -Verbose
```
### <a name="assign-a-role"></a>Assegnare un ruolo
Una volta creata l'entità servizio, è necessario [assegnarlo a un ruolo](azure-stack-create-service-principals.md#assign-role-to-service-principal)

### <a name="sign-in-through-powershell"></a>Accedi tramite PowerShell
Una volta assegnato un ruolo, è possibile accedere allo Stack di Azure usando l'entità servizio con il comando seguente:

```powershell
Add-AzureRmAccount -EnvironmentName "<AzureStackEnvironmentName>" `
 -ServicePrincipal `
 -CertificateThumbprint $servicePrincipal.Thumbprint `
 -ApplicationId $servicePrincipal.ApplicationId ` 
 -TenantId $directoryTenantId
```

## <a name="assign-role-to-service-principal"></a>Assegnazione di ruolo a un'entità servizio
Per accedere alle risorse della propria sottoscrizione è necessario assegnare l'applicazione a un ruolo. Decidere quale ruolo rappresenti le autorizzazioni appropriate per l'applicazione. Per informazioni sui ruoli disponibili, vedere [Controllo degli accessi in base al ruolo: ruoli predefiniti](../../active-directory/role-based-access-built-in-roles.md).

È possibile impostare l'ambito al livello della sottoscrizione, del gruppo di risorse o della risorsa. Le autorizzazioni vengono ereditate a livelli inferiori dell'ambito. Se ad esempio si aggiunge un'applicazione al ruolo Lettore per un gruppo di risorse, l'applicazione può leggere il gruppo di risorse e le risorse in esso contenute.

1. Nel portale di Azure Stack, passare al livello di ambito che si desidera assegnare l'applicazione. Ad esempio, per assegnare un ruolo a un ambito della sottoscrizione, selezionare **Sottoscrizioni**. In alternativa è possibile selezionare una risorsa o un gruppo di risorse.

2. Selezionare la sottoscrizione specifica (risorsa o un gruppo di risorse) a cui assegnare l'applicazione.

     ![selezionare la sottoscrizione per l'assegnazione](./media/azure-stack-create-service-principal/image16.png)

3. Selezionare **Controllo di accesso (IAM)**.

     ![selezionare accesso](./media/azure-stack-create-service-principal/image17.png)

4. Selezionare **Aggiungi**.

5. Selezionare il ruolo che si desidera assegnare all'applicazione.

6. Cercare l'applicazione e selezionarla.

7. Selezionare **OK** per completare l'assegnazione del ruolo. L'applicazione ora compare nell'elenco degli utenti assegnati a un ruolo per quell'ambito.

Ora che hai creato un'entità servizio ed assegnato un ruolo, è possibile iniziare usando questa all'interno dell'applicazione di accedere alle risorse di Azure Stack.  

## <a name="next-steps"></a>Passaggi successivi

[Gestire le autorizzazioni utente](azure-stack-manage-permissions.md)