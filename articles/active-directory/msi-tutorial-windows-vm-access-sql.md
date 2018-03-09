---
title: "Usare un'identità del servizio gestita per una macchina virtuale Windows per accedere a SQL di Azure"
description: "Esercitazione che illustra come usare un'identità del servizio gestita per una macchina virtuale Windows per accedere a SQL di Azure."
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
editor: bryanla
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/20/2017
ms.author: skwan
ms.openlocfilehash: 8240b2d986e18de24ee25fcaa17be357e4b4b5bc
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/07/2018
---
# <a name="use-a-windows-vm-managed-service-identity-msi-to-access-azure-sql"></a>Usare un'identità del servizio gestita per una macchina virtuale Windows per accedere a SQL di Azure

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

Questa esercitazione illustra come usare un'identità del servizio gestito per una macchina virtuale Windows per accedere a un server SQL di Azure. Le Identità del servizio gestito vengono gestite automaticamente da Azure e consentono di eseguire l'autenticazione ai servizi che supportano l'autenticazione di Azure AD senza la necessità di inserire le credenziali nel codice. Si apprenderà come:

> [!div class="checklist"]
> * Abilitare Identità del servizio gestito in una macchina virtuale Windows 
> * Concedere l'accesso della macchina virtuale a un server SQL Azure
> * Ottenere un token di accesso usando l'identità della macchina virtuale e usarlo per eseguire query su un server SQL di Azure

## <a name="prerequisites"></a>prerequisiti

[!INCLUDE [msi-qs-configure-prereqs](../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-windows-virtual-machine-in-a-new-resource-group"></a>Creare una macchina virtuale Windows in un nuovo gruppo di risorse

Per questa esercitazione si creerà una nuova macchina virtuale Windows,  ma è anche possibile abilitare l'identità del servizio gestito in una macchina virtuale esistente.

1.  Fare clic sul pulsante **Crea una risorsa** visualizzato nell'angolo in alto a sinistra nel portale di Azure.
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

5. Per verificare le estensioni installate nella macchina virtuale, fare clic su **Estensioni**. Se identità del servizio gestito è abilitata, nell'elenco sarà inclusa la voce **ManagedIdentityExtensionforWindows**.

    ![Testo immagine alt](media/msi-tutorial-windows-vm-access-arm/msi-windows-extension.png)

## <a name="grant-your-vm-access-to-a-database-in-an-azure-sql-server"></a>Concedere l'accesso della macchina virtuale a un database in un server SQL di Azure

Ora è possibile concedere alla macchina virtuale l'accesso a un database in un server SQL di Azure.  Per questo passaggio, è possibile usare un server SQL esistente o crearne uno nuovo.  Per creare un nuovo server e un database tramite il portale di Azure, seguire questa [Guida rapida di SQL di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal). Nella [documentazione di SQL di Azure](https://docs.microsoft.com/azure/sql-database/) sono disponibili anche guide rapide che usano l'interfaccia della riga di comando di Azure e Azure PowerShell.

Per concedere alla macchina virtuale l'accesso a un database sono necessari tre passaggi:
1.  Creare un gruppo in Azure AD e fare in modo che l'identità del servizio gestito della macchina virtuale diventi membro del gruppo.
2.  Abilitare l'autenticazione di Azure AD per il server SQL.
3.  Creare un **utente indipendente** nel database che rappresenta il gruppo di Azure AD.

> [!NOTE]
> In genere, si crea un utente indipendente che esegue il mapping diretto dell'identità del servizio gestito della macchina virtuale.  Attualmente, SQL di Azure non consente all'entità servizio di Azure AD che rappresenta l'identità del servizio gestito della macchina virtuale di essere mappata per un utente indipendente.  Un'alternativa supportata consiste nel fare in modo che l'identità del servizio gestito della macchina virtuale entri a far parte di un gruppo di Azure AD, quindi creare un utente indipendente nel database che rappresenta il gruppo.


### <a name="create-a-group-in-azure-ad-and-make-the-vm-msi-a-member-of-the-group"></a>Creare un gruppo in Azure AD e fare in modo che l'identità del servizio gestito della macchina virtuale diventi membro del gruppo

È possibile usare un gruppo di Azure AD esistente o crearne uno nuovo con PowerShell di Azure AD.  

Installare innanzitutto il modulo [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2). Accedere quindi usando `Connect-AzureAD` ed eseguire il comando seguente per creare il gruppo, quindi salvarlo in una variabile:

```powershell
$Group = New-AzureADGroup -DisplayName "VM MSI access to SQL" -MailEnabled $false -SecurityEnabled $true -MailNickName "NotSet"
```

Il risultato sarà analogo al seguente ed esamina anche il valore della variabile:

```powershell
$Group = New-AzureADGroup -DisplayName "VM MSI access to SQL" -MailEnabled $false -SecurityEnabled $true -MailNickName "NotSet"
$Group
ObjectId                             DisplayName          Description
--------                             -----------          -----------
6de75f3c-8b2f-4bf4-b9f8-78cc60a18050 VM MSI access to SQL
```

Successivamente, aggiungere l'identità del servizio gestito della macchina virtuale al gruppo.  È necessario l'**ObjectId** dell'identità del servizio gestito, che è possibile recuperare usando Azure PowerShell.  Scaricare innanzitutto [Azure Powershell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps). Accedere quindi usando `Login-AzureRmAccount` ed eseguire i comandi seguenti per:
- Verificare che il contesto della sessione sia impostato sulla sottoscrizione di Azure desiderata, se si dispone di più sessioni.
- Elencare le risorse disponibili nella sottoscrizione di Azure, verificare i nomi del gruppo di risorse corretto e della macchina virtuale.
- Ottenere le proprietà dell'identità del servizio gestito usando i valori appropriati per `<RESOURCE-GROUP>` e `<VM-NAME>`.

```powershell
Set-AzureRMContext -subscription "bdc79274-6bb9-48a8-bfd8-00c140fxxxx"
Get-AzureRmResource
$VM = Get-AzureRmVm -ResourceGroup <RESOURCE-GROUP> -Name <VM-NAME>
```

Il risultato sarà analogo al seguente, che esamina anche il valore dell'ID oggetto per l'entità servizio dell'identità del servizio gestito della macchina virtuale:
```powershell
$VM = Get-AzureRmVm -ResourceGroup DevTestGroup -Name DevTestWinVM
$VM.Identity.PrincipalId
b83305de-f496-49ca-9427-e77512f6cc64
```

Aggiungere ora l'identità del servizio gestito della macchina virtuale al gruppo.  È possibile aggiungere una sola entità servizio a un gruppo usando Azure AD PowerShell.  Eseguire questo comando:
```powershell
Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId $VM.Identity.PrincipalId
```

Se in un secondo momento si esamina anche l'appartenenza al gruppo, l'output è simile al seguente:

```powershell
Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId $VM.Identity.PrincipalId
Get-AzureAdGroupMember -ObjectId $Group.ObjectId

ObjectId                             AppId                                DisplayName
--------                             -----                                -----------
b83305de-f496-49ca-9427-e77512f6cc64 0b67a6d6-6090-4ab4-b423-d6edda8e5d9f DevTestWinVM
```

### <a name="enable-azure-ad-authentication-for-the-sql-server"></a>Abilitare l'autenticazione di Azure AD per il server SQL

Dopo aver creato il gruppo e aggiunto l'identità del servizio gestito della macchina virtuale all'appartenenza, è possibile [configurare l'autenticazione di Azure AD per il server SQL](/azure/sql-database/sql-database-aad-authentication-configure#provision-an-azure-active-directory-administrator-for-your-azure-sql-server) usando la procedura seguente:

1.  Nel portale di Azure selezionare **i server SQL** dal menu a sinistra.
2.  Fare clic sul server SQL da abilitare per l'autenticazione di Azure AD.
3.  Nella sezione **Impostazioni** del pannello fare clic su **Amministratore di Active Directory**.
4.  Nella barra dei comandi fare clic su **Imposta amministratore**.
5.  Selezionare un account utente di Azure AD da rendere amministratore del server e fare clic su **Seleziona**.
6.  Nella barra dei comandi fare clic su **Salva**.

### <a name="create-a-contained-user-in-the-database-that-represents-the-azure-ad-group"></a>Creare un utente indipendente nel database che rappresenta il gruppo di Azure AD

Per il passaggio successivo, sarà necessario [Microsoft SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms). Prima di iniziare, potrebbe essere utile consultare anche gli articoli seguenti per avere informazioni sull'integrazione di Azure AD:

- [Autenticazione universale con database SQL e SQL Data Warehouse (supporto SSMS per MFA)](/azure/sql-database/sql-database-ssms-mfa-authentication.md)
- [Configurare e gestire l'autenticazione di Azure Active Directory con il database SQL oppure con SQL Data Warehouse](/azure/sql-database/sql-database-aad-authentication-configure.md)

1.  Avviare SQL Server Management Studio.
2.  Nella finestra di dialogo **Connetti al server** immettere il nome del server SQL nel campo **Nome server**.
3.  Nel campo **Autenticazione** selezionare **Active Directory - Universale con supporto MFA**.
4.  Nel campo **Nome utente** immettere il nome dell'account di Azure AD impostato come amministratore del server, ad esempio helen@woodgroveonline.com
5.  Fare clic su **Opzioni**.
6.  Nel campo **Connetti a database** immettere il nome del database esterno al sistema che si desidera configurare.
7.  Fare clic su **Connetti**.  Completare il processo di accesso.
8.  In **Esplora oggetti** espandere la cartella **Database**.
9.  Fare clic con il pulsante destro del mouse su un database utente e scegliere **Nuova query**.
10.  Nella finestra della query immettere la riga seguente e fare clic su **Esegui** nella barra degli strumenti:
    
     ```
     CREATE USER [VM MSI access to SQL] FROM EXTERNAL PROVIDER
     ```
    
     Il comando dovrebbe completare l'operazione correttamente, creando l'utente indipendente per il gruppo.
11.  Cancellare la finestra di query, immettere la riga seguente e fare clic su **Esegui** nella barra degli strumenti:
     
     ```
     ALTER ROLE db_datareader ADD MEMBER [VM MSI access to SQL]
     ```

     Il comando dovrebbe completare l'operazione correttamente, concedendo all'utente indipendente la possibilità di leggere tutto il database.

Il codice in esecuzione nella macchina virtuale può ora ottenere un token dall'identità del servizio gestito e usarlo per autenticare il server SQL.

## <a name="get-an-access-token-using-the-vm-identity-and-use-it-to-call-azure-sql"></a>Ottenere un token di accesso usando l'identità della macchina virtuale e usarlo per chiamare Azure SQL 

SQL Azure supporta in modo nativo l'autenticazione di Azure AD, in modo da poter accettare direttamente i token di accesso ottenuti usando l'identità del servizio gestito.  Usare il metodo di creazione di una connessione a SQL del **token di accesso**.  Questo fa parte dell'integrazione di SQL di Azure con Azure AD e non prevede l'inserimento di credenziali nella stringa di connessione.

Di seguito è riportato un esempio di codice .Net di apertura di una connessione a SQL tramite un token di accesso.  Questo codice deve essere eseguito nella macchina virtuale per poter accedere all'endpoint dell'identità del servizio gestito per la macchina virtuale.  Per usare il metodo del token di accesso è necessario **.Net Framework 4.6** o una versione successiva.  Sostituire i valori di AZURE-SQL-SERVERNAME e DATABASE di conseguenza.  Si noti che l'ID della risorsa per SQL di Azure è "https://database.windows.net/".

```csharp
using System.Net;
using System.IO;
using System.Data.SqlClient;
using System.Web.Script.Serialization;

//
// Get an access token for SQL.
//
HttpWebRequest request = (HttpWebRequest)WebRequest.Create("http://localhost:50342/oauth2/token?resource=https://database.windows.net/");
request.Headers["Metadata"] = "true";
request.Method = "GET";
string accessToken = null;

try
{
    // Call MSI endpoint.
    HttpWebResponse response = (HttpWebResponse)request.GetResponse();

    // Pipe response Stream to a StreamReader and extract access token.
    StreamReader streamResponse = new StreamReader(response.GetResponseStream()); 
    string stringResponse = streamResponse.ReadToEnd();
    JavaScriptSerializer j = new JavaScriptSerializer();
    Dictionary<string, string> list = (Dictionary<string, string>) j.Deserialize(stringResponse, typeof(Dictionary<string, string>));
    accessToken = list["access_token"];
}
catch (Exception e)
{
    string errorText = String.Format("{0} \n\n{1}", e.Message, e.InnerException != null ? e.InnerException.Message : "Acquire token failed");
}

//
// Open a connection to the SQL server using the access token.
//
if (accessToken != null) {
    string connectionString = "Data Source=<AZURE-SQL-SERVERNAME>; Initial Catalog=<DATABASE>;";
    SqlConnection conn = new SqlConnection(connectionString);
    conn.AccessToken = accessToken;
    conn.Open();
}
```

Un altro modo rapido per verificare le impostazioni end-to-end senza dover scrivere e distribuire un'app nella macchina virtuale consiste nell'usare PowerShell.

1.  Nel portale passare a **Macchine virtuali**, selezionare la macchina virtuale Windows e in **Panoramica** fare clic su **Connetti**. 
2.  In **Nome utente** e **Password** immettere i valori specificati al momento della creazione della macchina virtuale Windows. 
3.  Ora che si è creata una **connessione Desktop remoto** con la macchina virtuale, aprire **PowerShell** nella sessione remota. 
4.  Con `Invoke-WebRequest` di PowerShell, eseguire una richiesta all'endpoint locale di identità del servizio gestito per ottenere un token di accesso per Azure SQL.

    ```powershell
       $response = Invoke-WebRequest -Uri http://localhost:50342/oauth2/token -Method GET -Body @{resource="https://database.windows.net/"} -Headers @{Metadata="true"}
    ```
    
    Convertire la risposta da un oggetto JSON a un oggetto di PowerShell. 
    
    ```powershell
    $content = $response.Content | ConvertFrom-Json
    ```

    Estrarre il token di accesso dalla risposta.
    
    ```powershell
    $AccessToken = $content.access_token
    ```

5.  Aprire una connessione nel server SQL. Ricordarsi di sostituire i valori di AZURE-SQL-SERVERNAME e DATABASE.
    
    ```powershell
    $SqlConnection = New-Object System.Data.SqlClient.SqlConnection
    $SqlConnection.ConnectionString = "Data Source = <AZURE-SQL-SERVERNAME>; Initial Catalog = <DATABASE>"
    $SqlConnection.AccessToken = $AccessToken
    $SqlConnection.Open()
    ```

    Successivamente, creare e inviare una query al server.  Ricordarsi di sostituire il valore per TABELLA.

    ```powershell
    $SqlCmd = New-Object System.Data.SqlClient.SqlCommand
    $SqlCmd.CommandText = "SELECT * from <TABLE>;"
    $SqlCmd.Connection = $SqlConnection
    $SqlAdapter = New-Object System.Data.SqlClient.SqlDataAdapter
    $SqlAdapter.SelectCommand = $SqlCmd
    $DataSet = New-Object System.Data.DataSet
    $SqlAdapter.Fill($DataSet)
    ```

Esaminare il valore di `$DataSet.Tables[0]` per visualizzare i risultati della query.  Complimenti, è stata eseguita una query sul database usando un'identità del servizio gestito della macchina virtuale senza dover inserire le credenziali.

## <a name="related-content"></a>Contenuti correlati

- Per una panoramica dell'identità di servizio gestito, vedere [Panoramica dell'identità di servizio gestito](../active-directory/msi-overview.md).
- Altre informazioni sul [supporto di SQL di Azure per l'autenticazione di Azure AD](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication).
- Altre informazioni sulla [supporto alla configurazione di SQL di Azure per l'autenticazione di Azure AD](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure).
- Altre informazioni sull'[autenticazione e l'accesso al server SQL](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/getting-started-with-database-engine-permissions).

Usare la sezione dei commenti seguente per fornire commenti e suggerimenti utili per migliorare e organizzare i contenuti disponibili.
