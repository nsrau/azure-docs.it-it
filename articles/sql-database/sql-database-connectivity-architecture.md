---
title: "Architettura della connettività del database SQL di Azure | Microsoft Docs"
description: "Questo documento illustra l'architettura della connettività del database SQL di Azure dall'interno o dall'esterno di Azure."
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: monicar
ms.assetid: 
ms.service: sql-database
ms.custom: DBs & servers
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: On Demand
ms.date: 01/24/2018
ms.author: carlrab
ms.openlocfilehash: 81f96c223fb5ad2c37bd0679743f14980a5885b0
ms.sourcegitcommit: 28178ca0364e498318e2630f51ba6158e4a09a89
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/24/2018
---
# <a name="azure-sql-database-connectivity-architecture"></a>Architettura della connettività del database SQL di Azure 

Questo articolo illustra l'architettura della connettività del database SQL di Azure e spiega il funzionamento dei diversi componenti per indirizzare il traffico a un'istanza del database SQL di Azure. La funzione dei componenti di connettività del database SQL di Azure è indirizzare il traffico di rete verso il database di Azure con client che si connettono dall'interno di Azure e client che si connettono dall'esterno di Azure. Questo articolo include anche alcuni esempi di script per modificare la modalità di connessione e propone alcune considerazioni sulla modifica delle impostazioni di connettività predefinite. 

## <a name="connectivity-architecture"></a>Architettura della connettività

Il diagramma seguente offre una panoramica generale dell'architettura della connettività del database SQL di Azure.

![panoramica dell'architettura](./media/sql-database-connectivity-architecture/architecture-overview.png)


I passaggi seguenti descrivono come viene stabilita una connessione a un database SQL di Azure tramite il servizio di bilanciamento del carico software del database SQL di Azure e il gateway del database SQL di Azure.

- I client all'interno o all'esterno di Azure si connettono al servizio di bilanciamento del carico software, che ha un indirizzo IP pubblico ed è in ascolto sulla porta 1433.
- Il servizio di bilanciamento del carico software indirizza il traffico al gateway del database SQL di Azure.
- Il gateway reindirizza il traffico al middleware proxy corretto.
- Il middleware proxy reindirizza il traffico al database SQL di Azure appropriato.

> [!IMPORTANT]
> Ognuno di questi componenti incorpora la protezione DDoS (Distributed Denial of Service) a livello di rete e di app.
>

## <a name="connectivity-from-within-azure"></a>Connettività dall'interno di Azure

Se ci si connette dall'interno di Azure, il criterio di connessione predefinito per le connessioni è **reindirizzamento**. Un criterio di **reindirizzamento** significa che, dopo aver stabilito la sessione TCP al database SQL di Azure, la sessione client viene reindirizzata al middleware proxy sostituendo l'indirizzo IP virtuale di destinazione del gateway del database SQL di Azure con quello del middleware proxy. Tutti i pacchetti successivi passano poi direttamente attraverso il middleware proxy, ignorando il gateway del database SQL di Azure. Il diagramma seguente illustra il flusso del traffico.

![panoramica dell'architettura](./media/sql-database-connectivity-architecture/connectivity-from-within-azure.png)

## <a name="connectivity-from-outside-of-azure"></a>Connettività dall'esterno di Azure

Se ci si connette dall'esterno di Azure, le connessioni usano un criterio di connessione **proxy** per impostazione predefinita. Il criterio **proxy** significa che la sessione TCP viene stabilita tramite il gateway del database SQL di Azure e che tutti i pacchetti successivi passano attraverso il gateway. Il diagramma seguente illustra il flusso del traffico.

![panoramica dell'architettura](./media/sql-database-connectivity-architecture/connectivity-from-outside-azure.png)

## <a name="azure-sql-database-gateway-ip-addresses"></a>Indirizzi IP del gateway del database SQL di Azure

Per connettersi a un database SQL di Azure da risorse locali, è necessario consentire il traffico di rete in uscita verso il gateway del database SQL di Azure per la propria area di Azure. Le connessioni passano solo attraverso il gateway quando ci si connette in modalità proxy, ovvero l'impostazione predefinita per la connessione da risorse locali.

La tabella seguente elenca gli indirizzi IP primario e secondario del gateway del database SQL di Azure per tutte le aree dati. Per alcune aree sono disponibili due indirizzi IP. In queste aree, l'indirizzo IP primario è l'indirizzo IP corrente del gateway e il secondo indirizzo IP è un indirizzo IP di failover. L'indirizzo di failover è l'indirizzo verso cui potrebbe essere spostato il server per mantenere l'alta disponibilità del servizio. Per queste aree, è consigliabile consentire il traffico in uscita verso entrambi gli indirizzi IP. Il secondo indirizzo IP è di proprietà di Microsoft e non è in ascolto su alcun servizio fino a quando non viene attivato dal database SQL di Azure per accettare connessioni.

| Nome area | Indirizzo IP primario | Indirizzo IP secondario |
| --- | --- |--- |
| Australia orientale | 191.238.66.109 | 13.75.149.87 |
| Australia sud-orientale | 191.239.192.109 | 13.73.109.251 |
| Brasile meridionale | 104.41.11.5 | |
| Canada centrale | 40.85.224.249 | |
| Canada orientale | 40.86.226.166 | |
| Stati Uniti centrali | 23.99.160.139 | 13.67.215.62 |
| Asia orientale | 191.234.2.139 | 52.175.33.150 |
| Stati Uniti orientali 1 | 191.238.6.43 | 40.121.158.30 |
| Stati Uniti orientali 2 | 191.239.224.107 | 40.79.84.180 * |
| India centrale | 104.211.96.159  | |
| India meridionale | 104.211.224.146  | |
| India occidentale | 104.211.160.80 | |
| Giappone orientale | 191.237.240.43 | 13.78.61.196 |
| Giappone occidentale | 191.238.68.11 | 104.214.148.156 |
| Corea centrale | 52.231.32.42 | |
| Corea meridionale | 52.231.200.86 |  |
| Stati Uniti centro-settentrionali | 23.98.55.75 | 23.96.178.199 |
| Europa settentrionale | 191.235.193.75 | 40.113.93.91 |
| Stati Uniti centro-meridionali | 23.98.162.75 | 13.66.62.124 |
| Asia sudorientale | 23.100.117.95 | 104.43.15.0 |
| Regno Unito settentrionale | 13.87.97.210 | |
| Regno Unito meridionale 1 | 51.140.184.11 | |
| Regno Unito meridionale 2 | 13.87.34.7 | |
| Regno Unito occidentale | 51.141.8.11  | |
| Stati Uniti centro-occidentali | 13.78.145.25 | |
| Europa occidentale | 191.237.232.75 | 40.68.37.158 |
| Stati Uniti occidentali 1 | 23.99.34.75 | 104.42.238.205 |
| Stati Uniti occidentali 2 | 13.66.226.202  | |
||||

\* **NOTA:** *Stati Uniti orientali 2* ha anche l'indirizzo IP terziario `52.167.104.0`.

## <a name="change-azure-sql-database-connection-policy"></a>Modificare il criterio di connessione del database SQL di Azure

Per modificare il criterio di connessione del database SQL di Azure per un server di database SQL di Azure, usare l'[API REST](https://msdn.microsoft.com/library/azure/mt604439.aspx).

- Se il criterio di connessione è impostato su **proxy**, tutti i pacchetti di rete passano attraverso il gateway del database SQL di Azure. Per questa impostazione, è necessario consentire il traffico in uscita solo per l'indirizzo IP del gateway del database SQL di Azure. L'uso dell'impostazione **proxy** ha una latenza maggiore rispetto all'impostazione **reindirizzamento**.
- Se il criterio di connessione è impostato su **reindirizzamento**, tutti i pacchetti di rete passano direttamente al proxy del middleware. Per questa impostazione, è necessario consentire il traffico in uscita verso più IP.

## <a name="script-to-change-connection-settings-via-powershell"></a>Script per modificare le impostazioni di connessione tramite PowerShell

> [!IMPORTANT]
> Per questo script è necessario il [modulo Azure PowerShell](/powershell/azure/install-azurerm-ps).
>

Lo script di PowerShell seguente mostra come modificare il criterio di connessione.

```powershell
Add-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName <Subscription Name>

# Azure Active Directory ID
$tenantId = "<Azure Active Directory GUID>"
$authUrl = "https://login.microsoftonline.com/$tenantId"

# Subscription ID
$subscriptionId = "<Subscription GUID>"

# Create an App Registration in Azure Active Directory.  Ensure the application type is set to NATIVE
# Under Required Permissions, add the API:  Windows Azure Service Management API

# Specify the redirect URL for the app registration
$uri = "<NATIVE APP - REDIRECT URI>"

# Specify the application id for the app registration
$clientId = "<NATIVE APP - APPLICATION ID>"

# Logical SQL Server Name
$serverName = "<LOGICAL DATABASE SERVER - NAME>"

# Resource Group where the SQL Server is located
$resourceGroupName= "<LOGICAL DATABASE SERVER - RESOURCE GROUP NAME>"


# Login and acquire a bearer token
$AuthContext = [Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext]$authUrl
$result = $AuthContext.AcquireToken(
"https://management.core.windows.net/",
$clientId,
[Uri]$uri,
[Microsoft.IdentityModel.Clients.ActiveDirectory.PromptBehavior]::Auto
)

$authHeader = @{
'Content-Type'='application\json; '
'Authorization'=$result.CreateAuthorizationHeader()
}

#Get current connection Policy
Invoke-RestMethod -Uri "https://management.azure.com/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Sql/servers/$serverName/connectionPolicies/Default?api-version=2014-04-01-preview" -Method GET -Headers $authHeader

#Set connection policy to Proxy
$connectionType="Proxy" <#Redirect / Default are other options#>
$body = @{properties=@{connectionType=$connectionType}} | ConvertTo-Json

# Apply Changes
Invoke-RestMethod -Uri "https://management.azure.com/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Sql/servers/$serverName/connectionPolicies/Default?api-version=2014-04-01-preview" -Method PUT -Headers $authHeader -Body $body -ContentType "application/json"
```

## <a name="script-to-change-connection-settings-via-azure-cli-20"></a>Script per modificare le impostazioni di connessione tramite l'interfaccia della riga di comando di Azure 2.0

> [!IMPORTANT]
> Per questo script è necessaria l'[interfaccia della riga di comando di Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
>

Lo script dell'interfaccia della riga di comando seguente mostra come modificare il criterio di connessione.

<pre>
# Get SQL Server ID
sqlserverid=$(az sql server show -n <b>sql-server-name</b> -g <b>sql-server-group</b> --query 'id' -o tsv)

# Set URI
id="$sqlserverid/connectionPolicies/Default"

# Get current connection policy 
az resource show --ids $id

# Update connection policy 
az resource update --ids $id --set properties.connectionType=Proxy

</pre>

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su come modificare i criteri di connessione del database SQL di Azure per un server di database SQL di Azure, vedere [Create or Update Server Connection Policy using the REST API](https://msdn.microsoft.com/library/azure/mt604439.aspx) (Creare o aggiornare i criteri di connessione server tramite l'API REST).
- Per informazioni sul comportamento della connessione al database SQL di Azure per i client che usano ADO.NET 4.5 o versione successiva, vedere [Porte successive alla 1433 per ADO.NET 4.5](sql-database-develop-direct-route-ports-adonet-v12.md).
- Per una panoramica generale sullo sviluppo di applicazioni, vedere [Panoramica dello sviluppo di applicazioni del database SQL](sql-database-develop-overview.md).
