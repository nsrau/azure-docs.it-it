---
title: Configurazione dell'inoltro DNS per File di Azure | Microsoft Docs
description: Panoramica delle opzioni di rete per File di Azure.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 3/19/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 6404115e64ba0ac1f65ba1cfc8d26604f1ce9cfa
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85509966"
---
# <a name="configuring-dns-forwarding-for-azure-files"></a>Configurazione dell'inoltro DNS per File di Azure
File di Azure consente di creare endpoint privati per gli account di archiviazione contenenti condivisioni file. Oltre che per molte applicazioni diverse, gli endpoint privati sono particolarmente utili per connettersi alle condivisioni file di Azure dalla rete locale tramite una connessione VPN o ExpressRoute con peering privato. 

Per consentire alle connessioni all'account di archiviazione di passare attraverso il tunnel di rete, il nome di dominio completo (FQDN) dell'account di archiviazione deve essere risolto nell'indirizzo IP privato dell'endpoint privato. A tale scopo, è necessario inoltrare il suffisso dell'endpoint di archiviazione (`core.windows.net` per le aree del cloud pubblico) al servizio DNS privato di Azure accessibile dall'interno della rete virtuale. Questa guida illustra come impostare e configurare l'inoltro DNS per la corretta risoluzione nell'indirizzo IP dell'endpoint privato dell'account di archiviazione.

Prima di completare la procedura di questo articolo, è consigliabile leggere [Pianificazione della distribuzione di File di Azure](storage-files-planning.md) e [Pianificazione della distribuzione di Sincronizzazione file di Azure](storage-files-networking-overview.md).

## <a name="overview"></a>Panoramica
File di Azure prevede due tipi principali di endpoint per l'accesso alle condivisioni file di Azure: 
- Endpoint pubblici, che hanno un indirizzo IP pubblico e sono accessibili da qualsiasi parte del mondo.
- Endpoint privati, presenti all'interno di una rete virtuale, che hanno un indirizzo IP privato all'interno dello spazio di indirizzi della rete virtuale.

Gli endpoint pubblici e privati sono disponibili nell'account di archiviazione di Azure. Un account di archiviazione è un costrutto di gestione che rappresenta un pool di archiviazione condiviso in cui è possibile distribuire più condivisioni file oltre ad altre risorse di archiviazione, ad esempio contenitori BLOB o code.

Ogni account di archiviazione è associato a un nome di dominio completo (FQDN). Per le aree del cloud pubblico, questo nome FQDN segue il modello `storageaccount.file.core.windows.net`, dove `storageaccount` è il nome dell'account di archiviazione. Quando si effettuano richieste per questo nome, ad esempio il montaggio della condivisione nella workstation tramite SMB, il sistema operativo esegue una ricerca DNS per risolvere il nome di dominio completo in un indirizzo IP utilizzabile per inviare le richieste SMB.

Per impostazione predefinita, `storageaccount.file.core.windows.net` viene risolto nell'indirizzo IP dell'endpoint pubblico. L'endpoint pubblico per un account di archiviazione è ospitato in un cluster di archiviazione di Azure che ne ospita molti altri. Quando si crea un endpoint privato, una zona DNS privato viene collegata alla rete virtuale a cui è stata aggiunta, con il mapping di un record CNAME `storageaccount.file.core.windows.net` a una voce di record A per l'indirizzo IP privato dell'endpoint privato dell'account di archiviazione. In questo modo è possibile usare il nome FQDN `storageaccount.file.core.windows.net` nella rete virtuale e risolverlo nell'indirizzo IP dell'endpoint privato.

Poiché l'obiettivo finale è l'accesso alle condivisioni file di Azure ospitate nell'account di archiviazione dall'ambiente locale tramite un tunnel di rete, ad esempio una connessione VPN o ExpressRoute, è necessario configurare i server DNS locali per l'inoltro al servizio DNS privato di Azure delle richieste effettuate al servizio File di Azure. A tale scopo, è necessario configurare l'*inoltro condizionale* di `*.core.windows.net` (o del suffisso dell'endpoint di archiviazione appropriato per i cloud nazionali del governo degli Stati Uniti, della Germania o della Cina) a un server DNS ospitato nella rete virtuale di Azure. Questo server DNS inoltrerà quindi in modo ricorsivo la richiesta al servizio DNS privato di Azure che risolverà il nome di dominio completo dell'account di archiviazione nell'indirizzo IP privato appropriato.

Per la configurazione dell'inoltro DNS per File di Azure sarà necessario eseguire una macchina virtuale in cui ospitare un server DNS per l'inoltro delle richieste. Tuttavia, si tratta di un passaggio da completare una sola volta per tutte le condivisioni file di Azure ospitate nella rete virtuale. Inoltre, questo non è un requisito esclusivo per File di Azure. Tutti i servizi di Azure che supportano gli endpoint privati a cui si vuole accedere dall'ambiente locale possono usare l'inoltro DNS che verrà configurato in questa guida: archiviazione BLOB di Azure, SQL Azure, Cosmos DB e così via. 

Questa guida illustra i passaggi per configurare l'inoltro DNS per l'endpoint di archiviazione di Azure, quindi, oltre che a File di Azure, le richieste di risoluzione dei nomi DNS per tutti gli altri servizi di archiviazione di Azure (archiviazione BLOB, tabelle, code e così via di Azure) verranno inoltrate al servizio DNS privato di Azure. Facoltativamente, è anche possibile aggiungere altri endpoint per altri servizi di Azure. Verrà configurato anche l'inoltro DNS ai server DNS locali, consentendo di risolvere i nomi dei computer locali per le risorse cloud all'interno della rete virtuale, ad esempio i server DFS-N. 

## <a name="prerequisites"></a>Prerequisiti
Prima di configurare l'inoltro DNS a File di Azure, è necessario aver completato i passaggi seguenti:

- Un account di archiviazione contenente una condivisione file di Azure da montare. Per informazioni su come creare un account di archiviazione e una condivisione file di Azure, vedere [Creare una condivisione file di Azure](storage-how-to-create-file-share.md).
- Un endpoint privato per l'account di archiviazione. Per informazioni su come creare un endpoint privato per File di Azure, vedere [Creare un endpoint privato](storage-files-networking-endpoints.md#create-a-private-endpoint).
- L'[ultima versione](https://docs.microsoft.com/powershell/azure/install-az-ps) del modulo Azure PowerShell.

> [!Important]  
> Questa guida presuppone che si usi il server DNS all'interno di Windows Server nell'ambiente locale. Tutti i passaggi descritti in questa guida possono essere eseguiti con qualsiasi server DNS, non solo con il server DNS di Windows.

## <a name="manually-configuring-dns-forwarding"></a>Configurazione manuale dell'inoltro DNS
Se nella rete virtuale di Azure sono già presenti server DNS oppure se si preferisce semplicemente distribuire le proprie macchine virtuali come server DNS con qualsiasi metodologia in uso nell'organizzazione, è possibile configurare DNS manualmente con i cmdlet predefiniti di PowerShell per server DNS.

Nei server DNS locali creare un server d'inoltro condizionale usando `Add-DnsServerConditionalForwarderZone`. Questo server d'inoltro condizionale deve essere distribuito in tutti i server DNS locali per l'inoltro efficace e corretto del traffico in Azure. Assicurarsi di sostituire `<azure-dns-server-ip>` con gli indirizzi IP appropriati per l'ambiente.

```powershell
$vnetDnsServers = "<azure-dns-server-ip>", "<azure-dns-server-ip>"

$storageAccountEndpoint = Get-AzContext | `
    Select-Object -ExpandProperty Environment | `
    Select-Object -ExpandProperty StorageEndpointSuffix

Add-DnsServerConditionalForwarderZone `
        -Name $storageAccountEndpoint `
        -MasterServers $vnetDnsServers
```

Nei server DNS all'interno della rete virtuale di Azure, è anche necessario inserire un server d'inoltro in modo che le richieste per la zona DNS dell'account di archiviazione vengano indirizzate al servizio DNS privato di Azure, che è associato all'indirizzo IP riservato `168.63.129.16`. Assicurarsi di popolare `$storageAccountEndpoint` se si eseguono i comandi all'interno di una sessione di PowerShell diversa.

```powershell
Add-DnsServerConditionalForwarderZone `
        -Name $storageAccountEndpoint `
        -MasterServers "168.63.129.16"
```

## <a name="using-the-azure-files-hybrid-module-to-configure-dns-forwarding"></a>Uso del modulo ibrido File di Azure per configurare l'inoltro DNS
Per semplificare il più possibile la configurazione dell'inoltro DNS, è possibile usare l'automazione del modulo ibrido di File di Azure. I cmdlet forniti per la manipolazione di DNS in questo modulo consentiranno di distribuire i server DNS nella rete virtuale di Azure e di aggiornare i server DNS locali per l'inoltro. 

Se non è mai stato usato il modulo ibrido di File di Azure, è necessario prima installarlo nella workstation. Scaricare l'[ultima versione](https://github.com/Azure-Samples/azure-files-samples/releases) del modulo PowerShell ibrido di File di Azure:

```PowerShell
# Unzip the downloaded file
Expand-Archive -Path AzFilesHybrid.zip

# Change the execution policy to unblock importing AzFilesHybrid.psm1 module
Set-ExecutionPolicy -ExecutionPolicy Unrestricted

# Navigate to where AzFilesHybrid is unzipped and stored and run to copy the files into your path
.\AzFilesHybrid\CopyToPSPath.ps1 

# Import AzFilesHybrid module
Import-Module -Name AzFilesHybrid
```

La distribuzione della soluzione di inoltro DNS prevede due passaggi, ovvero la creazione di un set di regole di inoltro DNS, che definisce i servizi di Azure a cui inoltrare le richieste, e la distribuzione effettiva dei server d'inoltro DNS. 

L'esempio seguente inoltra le richieste all'account di archiviazione, incluse le richieste a File di Azure, archiviazione BLOB di Azure, archiviazione tabelle di Azure e archiviazione code di Azure. Se si vuole, è possibile aggiungere alla regola l'inoltro per un servizio aggiuntivo di Azure tramite il parametro `-AzureEndpoints` del cmdlet `New-AzDnsForwardingRuleSet`. Ricordarsi di sostituire `<virtual-network-resource-group>`, `<virtual-network-name>` e `<subnet-name>` con i valori appropriati per l'ambiente.

```PowerShell
# Create a rule set, which defines the forwarding rules
$ruleSet = New-AzDnsForwardingRuleSet -AzureEndpoints StorageAccountEndpoint

# Deploy and configure DNS forwarders
New-AzDnsForwarder `
        -DnsForwardingRuleSet $ruleSet `
        -VirtualNetworkResourceGroupName "<virtual-network-resource-group>" `
        -VirtualNetworkName "<virtual-network-name>" `
        -VirtualNetworkSubnetName "<subnet-name>"
```

Potrebbe inoltre risultare utile o necessario fornire diversi parametri aggiuntivi:

| Nome parametro | Type | Descrizione |
|----------------|------|-------------|
| `DnsServerResourceGroupName` | `string` | Per impostazione predefinita, i server DNS verranno distribuiti nello stesso gruppo di risorse della rete virtuale. Se questo non è il risultato desiderato, questo parametro consente di scegliere un gruppo di risorse alternativo in cui eseguire la distribuzione. |
| `DnsForwarderRootName` | `string` | Per impostazione predefinita, i nomi dei server DNS distribuiti in Azure sono `DnsFwder-*`, dove l'asterisco viene popolato da un iteratore. Questo parametro cambia la radice del nome, ovvero `DnsFwder`. |
| `VmTemporaryPassword` | `SecureString` | Per impostazione predefinita, viene scelta una password casuale per l'account predefinito temporaneo di una macchina virtuale prima dell'aggiunta a un dominio. Dopo l'aggiunta a un dominio, l'account predefinito viene disabilitato. |
| `DomainToJoin` | `string` | Il dominio a cui aggiungere le VM DNS. Per impostazione predefinita, questo dominio viene scelto in base al dominio del computer in cui vengono eseguiti i cmdlet. |
| `DnsForwarderRedundancyCount` | `int` | Il numero di VM DNS da distribuire per la rete virtuale. Per impostazione predefinita, `New-AzDnsForwarder` distribuisce due server DNS nella rete virtuale di Azure, in un set di disponibilità, per garantire la ridondanza. Questo numero può essere modificato come si preferisce. |
| `OnPremDnsHostNames` | `HashSet<string>` | Un elenco specificato manualmente di nomi di host DNS locali in cui creare i server d'inoltro. Questo parametro è utile se non si vogliono applicare i server d'inoltro in tutti i server DNS locali, ad esempio quando è presente una gamma di client con nomi DNS specificati manualmente. |
| `Credential` | `PSCredential` | Una credenziale da usare per l'aggiornamento dei server DNS. Questa credenziale è utile quando l'account utente con cui è stato eseguito l'accesso non ha le autorizzazioni per modificare le impostazioni DNS. |
| `SkipParentDomain` | `SwitchParameter` | Per impostazione predefinita, i server d'inoltro DNS vengono applicati al dominio di livello più alto esistente nell'ambiente. Se ad esempio `northamerica.corp.contoso.com` è un dominio figlio di `corp.contoso.com`, il server d'inoltro verrà creato per i server DNS associati a `corp.contoso.com`. Questo parametro genera la creazione di server d'inoltro in `northamerica.corp.contoso.com`. |

## <a name="confirm-dns-forwarders"></a>Confermare i server d'inoltro DNS
Prima di verificare se i server d'inoltro DNS sono stati applicati correttamente, è consigliabile cancellare la cache DNS nella workstation locale usando `Clear-DnsClientCache`. Per verificare se è possibile risolvere correttamente il nome di dominio completo dell'account di archiviazione, usare `Resolve-DnsName` o `nslookup`.

```powershell
# Replace storageaccount.file.core.windows.net with the appropriate FQDN for your storage account.
# Note the proper suffix (core.windows.net) depends on the cloud your deployed in.
Resolve-DnsName -Name storageaccount.file.core.windows.net
```

Se la risoluzione dei nomi riesce, l'indirizzo IP risolto dovrà corrispondere all'indirizzo IP dell'account di archiviazione.

```Output
Name                              Type   TTL   Section    NameHost
----                              ----   ---   -------    --------
storageaccount.file.core.windows. CNAME  29    Answer     csostoracct.privatelink.file.core.windows.net
net

Name       : storageaccount.privatelink.file.core.windows.net
QueryType  : A
TTL        : 1769
Section    : Answer
IP4Address : 192.168.0.4
```

Se è già stata configurata una connessione VPN o ExpressRoute, è anche possibile usare `Test-NetConnection` per verificare che sia possibile eseguire correttamente una connessione TCP all'account di archiviazione.

```PowerShell
Test-NetConnection -ComputerName storageaccount.file.core.windows.net -CommonTCPPort SMB
```

## <a name="see-also"></a>Vedere anche
- [Pianificazione per la distribuzione di File di Azure](storage-files-planning.md)
- [Considerazioni sulla rete per File di Azure](storage-files-networking-overview.md)
- [Configurazione degli endpoint di rete di File di Azure](storage-files-networking-endpoints.md)