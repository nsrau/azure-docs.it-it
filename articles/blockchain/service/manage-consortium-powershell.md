---
title: Gestire i membri in un consorzio di servizio di Azure Blockchain con Azure PowerShell
description: Informazioni su come gestire i membri di un servizio di Azure Blockchain consorzio con Azure PowerShell.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/10/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 4bb72bc3fe8b85a8d2aed88e02f5f3150abb6899
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66493643"
---
# <a name="manage-consortium-members-in-azure-blockchain-service-by-using-powershell"></a>Gestire i membri consortium nel servizio di Blockchain di Azure con PowerShell

È possibile usare PowerShell per gestire i membri consortium blockchain per il servizio di Blockchain di Azure. I membri che dispongono dei privilegi di amministratore possono invitare, aggiungere, rimuovere e modificare i ruoli per tutti i partecipanti al consorzio di blockchain. I membri che hanno i privilegi utente possono visualizzare tutti i partecipanti al consorzio blockchain e modifica il nome visualizzato del membro.

## <a name="prerequisites"></a>Prerequisiti

* Creare un membro di blockchain con i [portale di Azure](create-member.md).
* Per altre informazioni su consorzi, membri e i nodi, vedere [consortium Azure Blockchain servizio](consortium.md).

## <a name="open-azure-cloud-shell"></a>Aprire Azure Cloud Shell

Azure Cloud Shell è una shell interattiva gratuita che può essere usata per eseguire la procedura di questo articolo. Include strumenti comuni di Azure preinstallati e configurati per l'uso con l'account.

È anche possibile aprire Cloud Shell in una scheda separata del browser visitando [shell.azure.com/powershell](https://shell.azure.com/powershell). Selezionare **copia** per copiare i blocchi di codice, incollarlo in Cloud Shell e selezionare **invio** per eseguirlo.

## <a name="install-the-powershell-module"></a>Installare il modulo PowerShell

Installare il pacchetto Microsoft.AzureBlockchainService.ConsortiumManagement.PS da PowerShell Gallery.

```powershell-interactive
Install-Module -Name Microsoft.AzureBlockchainService.ConsortiumManagement.PS -Scope CurrentUser
Import-Module Microsoft.AzureBlockchainService.ConsortiumManagement.PS
```

## <a name="set-the-information-preference"></a>Impostare la preferenza di informazioni

È possibile ottenere altre informazioni quando si esegue i cmdlet, impostando la variabile di preferenza di informazioni. Per impostazione predefinita *$InformationPreference* è impostata su *SilentlyContinue*.

Per informazioni più dettagliate dai cmdlet, impostare la preferenza in PowerShell, come indicato di seguito:

```powershell-interactive
$InformationPreference = 'Continue'
```

## <a name="establish-a-web3-connection"></a>Stabilire una connessione Web3

Per gestire i membri consortium, stabilire una connessione Web3 all'endpoint di membro della Blockchain Service. È possibile usare questo script per impostare le variabili globali per chiamare i cmdlet di gestione consortium.

```powershell-interactive
$Connection = New-Web3Connection -RemoteRPCEndpoint '<Endpoint address>'
$MemberAccount = Import-Web3Account -ManagedAccountAddress '<Member account address>' -ManagedAccountPassword '<Member account password>'
$ContractConnection = Import-ConsortiumManagementContracts -RootContractAddress '<RootContract address>' -Web3Client $Connection
```

Sostituire *\<password dell'account del membro\>* con la password dell'account membro che è stato utilizzato durante la creazione del membro.

Trovare gli altri valori nel portale di Azure:

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Passare al membro predefinito del servizio di Blockchain **Panoramica** pagina.

    ![Panoramica dei membri](./media/manage-consortium-powershell/member-overview.png)

    Sostituire *\<account membro\>* e *\<indirizzo RootContract\>* con i valori dal portale.

1. Per l'indirizzo dell'endpoint, selezionare **i nodi delle transazioni**e quindi selezionare la **nodo della transazione predefinito**. Il nodo predefinito ha lo stesso nome del membro della blockchain.
1. Selezionare **Connection strings** (Stringhe di connessione).

    ![Stringhe di connessione](./media/manage-consortium-powershell/connection-strings.png)

    Sostituire *\<indirizzo dell'Endpoint\>* con il valore di **HTTPS (chiave di accesso 1)** oppure **HTTPS (chiave di accesso 2)** .

## <a name="manage-the-network-and-smart-contracts"></a>Gestire la rete e smart Contract

Usare la rete e i cmdlet di contratto intelligente per stabilire una connessione smart Contract dell'endpoint blockchain responsabile della gestione consortium.

### <a name="import-consortiummanagementcontracts"></a>Import-ConsortiumManagementContracts

Utilizzare questo cmdlet per connettersi a smart Contract della dirigenza consortium. Questi contratti consentono di gestire e applicare i membri all'interno del consortium.

`Import-ConsortiumManagementContracts -RootContractAddress <String> -Web3Client <IClient>`

| Parametro | DESCRIZIONE | Obbligatorio |
|-----------|-------------|:--------:|
| RootContractAddress | Indirizzo radice del contratto dei contratti consortium gestione intelligente | Yes |
| Web3Client | Oggetto Web3Client ottenuto da New-Web3Connection | Yes |

#### <a name="example"></a>Esempio

```powershell-interactive
Import-ConsortiumManagementContracts -RootContractAddress '<RootContract address>'  -Web3Client $Connection
```

### <a name="import-web3account"></a>Import-Web3Account

Utilizzare questo cmdlet per creare un oggetto per contenere le informazioni per l'account di gestione del nodo remoto.

`Import-Web3Account -ManagedAccountAddress <String> -ManagedAccountPassword <String>`

| Parametro | DESCRIZIONE | Obbligatorio |
|-----------|-------------|:--------:|
| ManagedAccountAddress | Indirizzo dell'account membro Blockchain | Yes |
| ManagedAccountPassword | Password dell'account indirizzo | Yes |

#### <a name="example"></a>Esempio

```powershell-interactive
Import-Web3Account -ManagedAccountAddress '<Member account address>'  -ManagedAccountPassword '<Member account password>'
```

### <a name="new-web3connection"></a>Nuovo Web3Connection

Utilizzare questo cmdlet per stabilire una connessione all'endpoint RPC del nodo di una transazione.

`New-Web3Connection [-RemoteRPCEndpoint <String>]`

| Parametro | DESCRIZIONE | Obbligatorio |
|-----------|-------------|:--------:|
| RemoteRPCEndpoint | Indirizzo dell'endpoint membro Blockchain | Yes |

#### <a name="example"></a>Esempio

```powershell-interactive
New-Web3Connection -RemoteRPCEndpoint '<Endpoint address>'
```

## <a name="manage-the-consortium-members"></a>Gestire i membri consortium

Per gestire i membri all'interno del consorzio, usare i cmdlet di gestione membro consortium. Le azioni disponibili dipendono dal proprio ruolo consortium.

### <a name="get-blockchainmember"></a>Get-BlockchainMember

Utilizzare questo cmdlet per ottenere i dettagli relativi ai membri o elencare i membri del consortium.

`Get-BlockchainMember [[-Name] <String>] -Members <IContract> -Web3Client <IClient>`

| Parametro | DESCRIZIONE | Obbligatorio |
|-----------|-------------|:--------:|
| Name | Il nome del membro che si desidera recuperare i dettagli sulle Blockchain Service. Quando viene immesso un nome, restituisce i dettagli del membro. Quando un nome viene omesso, restituisce un elenco di tutti i membri consortium. | No |
| Membri | Oggetto membri ottenuto dall'importazione ConsortiumManagementContracts | Yes |
| Web3Client | Oggetto Web3Client ottenuto da New-Web3Connection | Yes |

#### <a name="example"></a>Esempio

```powershell-interactive
$ContractConnection | Get-BlockchainMember -Name <Member Name>
```

#### <a name="example-output"></a>Output di esempio

```
Name           : myblockchainmember
CorrelationId  : 0
DisplayName    : myCompany
SubscriptionId : <Azure subscription ID>
AccountAddress : 0x85b911c9e103d6405573151258d668479e9ebeef
Role           : ADMIN
```

### <a name="remove-blockchainmember"></a>Remove-BlockchainMember

Utilizzare questo cmdlet per rimuovere un membro di blockchain.

`Remove-BlockchainMember -Name <String> -Members <IContract> -Web3Account <IAccount> -Web3Client <IClient>`

| Parametro | DESCRIZIONE | Obbligatorio |
|-----------|-------------|:--------:|
| Name | Nome del membro da rimuovere | Yes |
| Membri | Oggetto membri ottenuto dall'importazione ConsortiumManagementContracts | Yes |
| Web3Account | Oggetto Web3Account ottenuto dall'importazione Web3Account | Yes |
| Web3Client | Oggetto Web3Client ottenuto da New-Web3Connection | Yes |

#### <a name="example"></a>Esempio

```powershell-interactive
$ContractConnection | Remove-BlockchainMember -Name <Member Name> -Web3Account $MemberAccount
```

### <a name="set-blockchainmember"></a>Set-BlockchainMember

Utilizzare questo cmdlet per impostare gli attributi di membro, inclusi il nome visualizzato e il ruolo consortium blockchain.

Consortium gli amministratori possono impostare **DisplayName** e **ruolo** per tutti i membri. Un membro di consorzio con il ruolo utente può modificare solo il nome di visualizzazione del proprio membro.

`Set-BlockchainMember -Name <String> [-DisplayName <String>] [-AccountAddress <String>] [-Role <String>]
 -Members <IContract> -Web3Account <IAccount> -Web3Client <IClient>`

| Parametro | DESCRIZIONE | Obbligatorio |
|-----------|-------------|:--------:|
| Name | Nome del membro blockchain | Yes |
| DisplayName | Nuovo nome visualizzato | No |
| AccountAddress | Indirizzo dell'account | No |
| Membri | Oggetto membri ottenuto dall'importazione ConsortiumManagementContracts | Yes |
| Web3Account | Oggetto Web3Account ottenuto dall'importazione Web3Account | Yes |
| Web3Client |  Oggetto Web3Client ottenuto da New-Web3Connection| Yes |

#### <a name="example"></a>Esempio

```powershell-interactive
$ContractConnection | Set-BlockchainMember -Name <Member Name> -DisplayName <Display name> -Web3Account $MemberAccount
```

## <a name="manage-the-consortium-members-invitations"></a>Gestire gli inviti dei membri consortium

Usare i cmdlet di gestione inviti membro consortium per gestire gli inviti i membri consortium. Le azioni disponibili dipendono dal proprio ruolo consortium.

### <a name="new-blockchainmemberinvitation"></a>New-BlockchainMemberInvitation

Usare questo cmdlet per invitare nuovi membri per il consorzio.

`New-BlockchainMemberInvitation -SubscriptionId <String> -Role <String> -Members <IContract>
 -Web3Account <IAccount> -Web3Client <IClient>`

| Parametro | DESCRIZIONE | Obbligatorio |
|-----------|-------------|:--------:|
| SubscriptionId | ID sottoscrizione di Azure del membro da invitare | Yes |
| Ruolo | Il ruolo consortium. Valori possono essere amministratore o utente. AMMINISTRATORE è il ruolo di amministratore consortium. UTENTE è il ruolo di membro consortium. | Yes |
| Membri | Oggetto membri ottenuto dall'importazione ConsortiumManagementContracts | Yes |
| Web3Account | Oggetto Web3Account ottenuto dall'importazione Web3Account | Yes |
| Web3Client | Oggetto Web3Client ottenuto da New-Web3Connection | Yes |

#### <a name="example"></a>Esempio

```powershell-interactive
$ContractConnection | New-BlockchainMemberInvitation -SubscriptionId <Azure Subscription ID> -Role USER -Web3Account $MemberAccount
```

### <a name="get-blockchainmemberinvitation"></a>Get-BlockchainMemberInvitation

Utilizzare questo cmdlet per recuperare o visualizzare lo stato dell'invito di un membro consortium.

`Get-BlockchainMemberInvitation [[-SubscriptionId] <String>] -Members <IContract> -Web3Client <IClient>`

| Parametro | DESCRIZIONE | Obbligatorio |
|-----------|-------------|:--------:|
| SubscriptionId | L'ID sottoscrizione di Azure del membro da invitare. Se l'ID della sottoscrizione è fornito, restituisce la sottoscrizione dell'ID dettagli dell'invito. Se l'ID sottoscrizione viene omesso, restituisce un elenco di tutti gli inviti di membro. | No |
| Membri | Oggetto membri ottenuto dall'importazione ConsortiumManagementContracts | Yes |
| Web3Client | Oggetto Web3Client ottenuto da New-Web3Connection | Yes |

#### <a name="example"></a>Esempio

```powershell-interactive
$ContractConnection | Get-BlockchainMemberInvitation – SubscriptionId <Azure subscription ID>
```

#### <a name="example-output"></a>Output di esempio

```
SubscriptionId                       Role CorrelationId
--------------                       ---- -------------
<Azure subscription ID>              USER             2
```

### <a name="remove-blockchainmemberinvitation"></a>Remove-BlockchainMemberInvitation

Utilizzare questo cmdlet per revocare l'invito di un membro consortium.

`Remove-BlockchainMemberInvitation -SubscriptionId <String> -Members <IContract> -Web3Account <IAccount>
 -Web3Client <IClient>`

| Parametro | DESCRIZIONE | Obbligatorio |
|-----------|-------------|:--------:|
| SubscriptionId | ID sottoscrizione di Azure del membro da revocare | Yes |
| Membri | Oggetto membri ottenuto dall'importazione ConsortiumManagementContracts | Yes |
| Web3Account | Oggetto Web3Account ottenuto dall'importazione Web3Account | Yes |
| Web3Client | Oggetto Web3Client ottenuto da New-Web3Connection | Yes |

#### <a name="example"></a>Esempio

```powershell-interactive
$ContractConnection | Remove-BlockchainMemberInvitation -SubscriptionId <Subscription ID> -Web3Account $MemberAccount
```

### <a name="set-blockchainmemberinvitation"></a>Set-BlockchainMemberInvitation

Utilizzare questo cmdlet per impostare il **ruolo** per un invito esistente. Solo gli amministratori consortium possono modificare gli inviti.

`Set-BlockchainMemberInvitation -SubscriptionId <String> -Role <String> -Members <IContract>
 -Web3Account <IAccount> -Web3Client <IClient>`

| Parametro | DESCRIZIONE | Obbligatorio |
|-----------|-------------|:--------:|
| SubscriptionId | ID sottoscrizione di Azure del membro da invitare | Yes |
| Ruolo | Nuovo ruolo consortium per invito. I possibili valori sono **utente** oppure **ADMIN**. | Yes |
| Membri |  Oggetto membri ottenuto dall'importazione ConsortiumManagementContracts | Yes |
| Web3Account | Oggetto Web3Account ottenuto dall'importazione Web3Account | Yes |
| Web3Client | Oggetto Web3Client ottenuto da New-Web3Connection | Yes |

#### <a name="example"></a>Esempio

```powershell-interactive
$ContractConnection | Set-BlockchainMemberInvitation -SubscriptionId <Azure subscription ID> -Role USER -Web3Account $MemberAccount
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su consorzi, membri e i nodi, vedere:

> [!div class="nextstepaction"]
> [Azure consortium Blockchain Service](consortium.md)
