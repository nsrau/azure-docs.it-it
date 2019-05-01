---
title: Automatizzare le attività di Azure Analysis Services con le entità servizio | Microsoft Docs
description: Informazioni su come creare entità servizio per l'automazione delle attività di Azure Analysis Services.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: a440494b183d18c1d888b5d39836eb4317190d02
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64708311"
---
# <a name="automation-with-service-principals"></a>Automazione con le entità servizio

Le entità servizio sono una risorsa dell'applicazione Azure Active Directory creata all'interno del tenant per l'esecuzione automatica di operazioni a livello di servizio e di risorsa. Sono un tipo univoco di *identità utente* con un ID applicazione e una password o un certificato. Un'entità servizio ha solo le autorizzazioni necessarie per eseguire le attività definite dai ruoli e le autorizzazioni per le quali viene assegnata. 

In Analysis Services le entità servizio vengono usate con Automazione di Azure, la modalità automatica di PowerShell, le applicazioni client personalizzate e le app Web per automatizzare le attività comuni. Ad esempio, il provisioning dei server, la distribuzione di modelli, l'aggiornamento dei dati, l'aumento/riduzione delle prestazioni e la sospensione/ripresa possono essere automatizzati usando le entità servizio. Le autorizzazioni vengono assegnate alle entità servizio tramite l'appartenenza a un ruolo, in modo analogo ai normali account UPN di Azure AD.

## <a name="create-service-principals"></a>Creare entità servizio
 
Le entità servizio possono essere create nel portale di Azure oppure con PowerShell. Per altre informazioni, vedere:

[Creare un'entità servizio - Portale di Azure](../active-directory/develop/howto-create-service-principal-portal.md)   
[Creare un'entità servizio - PowerShell](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

## <a name="store-credential-and-certificate-assets-in-azure-automation"></a>Archiviare gli asset di tipo certificato e credenziale in Automazione di Azure

I certificati e le credenziali delle entità servizio possono essere archiviati in modo sicuro in Automazione di Azure per le operazioni dei runbook. Per altre informazioni, vedere:

[Asset credenziali in Automazione di Azure](../automation/automation-credentials.md)   
[Asset di tipo certificato in Automazione di Azure](../automation/automation-certificates.md)

## <a name="add-service-principals-to-server-admin-role"></a>Aggiungere le entità servizio al ruolo di amministratore del server

Prima di poter usare un'entità servizio per le operazioni di gestione del server Analysis Services, è necessario aggiungerla al ruolo di amministratore del server. Per altre informazioni, vedere [Aggiungere un'entità servizio al ruolo di amministratore del server](analysis-services-addservprinc-admins.md).

## <a name="service-principals-in-connection-strings"></a>Entità servizio nelle stringhe di connessione

L'ID app e la password o il certificato dell'entità servizio possono essere usati nelle stringhe di connessione in modo molto simile a un nome dell'entità utente.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

#### <a name="a-nameazmodule-using-azanalysisservices-module"></a><a name="azmodule" />Using Az.AnalysisServices module

Quando si usa un'entità servizio per le operazioni di gestione risorse con il [Az.AnalysisServices](/powershell/module/az.analysisservices) modulo, usare `Connect-AzAccount` cmdlet. 

Nell'esempio seguente, appID e password usati per eseguire operazioni del piano di controllo per la sincronizzazione delle repliche di sola lettura e scalabilità verticale/orizzontale:

```powershell
Param (
        [Parameter(Mandatory=$true)] [String] $AppId,
        [Parameter(Mandatory=$true)] [String] $PlainPWord,
        [Parameter(Mandatory=$true)] [String] $TenantId
       )
$PWord = ConvertTo-SecureString -String $PlainPWord -AsPlainText -Force
$Credential = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList $AppId, $PWord

# Connect using Az module
Connect-AzAccount -Credential $Credential -SubscriptionId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx"

# Syncronize a database for query scale out
Sync-AzAnalysisServicesInstance -Instance "asazure://westus.asazure.windows.net/testsvr" -Database "testdb"

# Scale up the server to an S1, set 2 read-only replicas, and remove the primary from the query pool. The new replicas will hydrate from the synchronized data.
Set-AzAnalysisServicesServer -Name "testsvr" -ResourceGroupName "testRG" -Sku "S1" -ReadonlyReplicaCount 2 -DefaultConnectionMode Readonly
```

#### <a name="using-sqlserver-module"></a>Usando il modulo SQLServer

Nell'esempio seguente vengono usati l'ID app e una password per eseguire un'operazione di aggiornamento del database modello:

```powershell
Param (
        [Parameter(Mandatory=$true)] [String] $AppId,
        [Parameter(Mandatory=$true)] [String] $PlainPWord,
        [Parameter(Mandatory=$true)] [String] $TenantId
       )
$PWord = ConvertTo-SecureString -String $PlainPWord -AsPlainText -Force

$Credential = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList $AppId, $PWord

Invoke-ProcessTable -Server "asazure://westcentralus.asazure.windows.net/myserver" -TableName "MyTable" -Database "MyDb" -RefreshType "Full" -ServicePrincipal -ApplicationId $AppId -TenantId $TenantId -Credential $Credential
```

### <a name="amo-and-adomd"></a>AMO e ADOMD 

Quando ci si connette ad applicazioni client e app Web, i pacchetti installabili delle [librerie client AMO e ADOMD](analysis-services-data-providers.md) versione 15.0.2 e successive di NuGet supportano le entità servizio nelle stringhe di connessione con la sintassi seguente: `app:AppID` e password o `cert:thumbprint`. 

Nell'esempio seguente vengono usati `appID` e una `password` per eseguire un'operazione di aggiornamento del database modello:

```C#
string appId = "xxx";
string authKey = "yyy";
string connString = $"Provider=MSOLAP;Data Source=asazure://westus.asazure.windows.net/<servername>;User ID=app:{appId};Password={authKey};";
Server server = new Server();
server.Connect(connString);
Database db = server.Databases.FindByName("adventureworks");
Table tbl = db.Model.Tables.Find("DimDate");
tbl.RequestRefresh(RefreshType.Full);
db.Model.SaveChanges();
```

## <a name="next-steps"></a>Passaggi successivi
[Accedere con Azure PowerShell](https://docs.microsoft.com/powershell/azure/authenticate-azureps)   
[Aggiungere un'entità servizio al ruolo di amministratore del server](analysis-services-addservprinc-admins.md)   
