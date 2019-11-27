---
title: Gestire i membri del Consorzio di servizi blockchain di Azure-PowerShell
description: Informazioni su come gestire i membri del Consorzio di servizi blockchain di Azure usando Azure PowerShell.
ms.date: 10/14/2019
ms.topic: article
ms.reviewer: zeyadr
ms.openlocfilehash: 4eb35838394d3324f460b5e83cfbcb47b8114221
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74455524"
---
# <a name="manage-consortium-members-in-azure-blockchain-service-using-powershell"></a>Gestire i membri del Consorzio nel servizio Azure blockchain usando PowerShell

È possibile usare PowerShell per gestire i membri del Consorzio blockchain per il servizio blockchain di Azure. I membri che dispongono di privilegi di amministratore possono invitare, aggiungere, rimuovere e modificare i ruoli per tutti i partecipanti al Consorzio blockchain. I membri con privilegi utente possono visualizzare tutti i partecipanti al Consorzio blockchain e modificare il nome visualizzato del membro.

## <a name="prerequisites"></a>prerequisiti

* Creare un membro blockchain usando il [portale di Azure](create-member.md).
* Per altre informazioni su consorzi, membri e nodi, vedere il [Consorzio di servizi di Azure blockchain](consortium.md).

## <a name="open-azure-cloud-shell"></a>Aprire Azure Cloud Shell

Azure Cloud Shell è una shell interattiva gratuita che può essere usata per eseguire la procedura di questo articolo. Include strumenti comuni di Azure preinstallati e configurati per l'uso con l'account.

È anche possibile aprire Cloud Shell in una scheda del browser separata passando a [Shell.Azure.com/PowerShell](https://shell.azure.com/powershell). Selezionare **copia** per copiare i blocchi di codice, incollarlo in cloud Shell e premere **invio** per eseguirlo.

## <a name="install-the-powershell-module"></a>Installare il modulo PowerShell

Installare il pacchetto Microsoft.AzureBlockchainService.ConsortiumManagement.PS dalla PowerShell Gallery.

```powershell-interactive
Install-Module -Name Microsoft.AzureBlockchainService.ConsortiumManagement.PS -Scope CurrentUser
Import-Module Microsoft.AzureBlockchainService.ConsortiumManagement.PS
```

## <a name="set-the-information-preference"></a>Impostazione preferenza informazioni

È possibile ottenere altre informazioni quando si eseguono i cmdlet impostando la variabile di preferenza Information. Per impostazione predefinita, *$InformationPreference* è impostata su *SilentlyContinue*.

Per informazioni più dettagliate dai cmdlet, impostare la preferenza in PowerShell nel modo seguente:

```powershell-interactive
$InformationPreference = 'Continue'
```

## <a name="establish-a-web3-connection"></a>Stabilire una connessione WEB3

Per gestire i membri del Consorzio, stabilire una connessione Web3 all'endpoint membro del servizio blockchain. È possibile utilizzare questo script per impostare le variabili globali per chiamare i cmdlet di gestione Consortium.

```powershell-interactive
$Connection = New-Web3Connection -RemoteRPCEndpoint '<Endpoint address>'
$MemberAccount = Import-Web3Account -ManagedAccountAddress '<Member account address>' -ManagedAccountPassword '<Member account password>'
$ContractConnection = Import-ConsortiumManagementContracts -RootContractAddress '<RootContract address>' -Web3Client $Connection
```

Sostituire *\<password dell'account membro\>* con la password dell'account membro usato al momento della creazione del membro.

Trovare gli altri valori nel portale di Azure:

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Passare alla pagina di **Panoramica** del membro del servizio blockchain predefinito.

    ![Panoramica sui membri](./media/manage-consortium-powershell/member-overview.png)

    Sostituire *\<account membro\>* e *\<indirizzo RootContract\>* con i valori del portale.

1. Per indirizzo endpoint selezionare **nodi transazione**, quindi selezionare il **nodo transazione predefinito**. Il nome del nodo predefinito è uguale a quello del membro blockchain.
1. Selezionare **Connection strings** (Stringhe di connessione).

    ![Stringhe di connessione](./media/manage-consortium-powershell/connection-strings.png)

    Sostituire *\<indirizzo Endpoint\>* con il valore da **https (chiave di accesso 1)** o **https (chiave di accesso 2)** .

## <a name="manage-the-network-and-smart-contracts"></a>Gestire la rete e i contratti intelligenti

Usare i cmdlet di rete e di Smart Contract per stabilire una connessione agli Smart contract dell'endpoint blockchain responsabile della gestione del Consorzio.

### <a name="import-consortiummanagementcontracts"></a>Import-ConsortiumManagementContracts

Utilizzare questo cmdlet per connettersi agli Smart contract della gestione del Consorzio. Questi contratti vengono usati per gestire e applicare i membri all'interno del Consorzio.

`Import-ConsortiumManagementContracts -RootContractAddress <String> -Web3Client <IClient>`

| . | DESCRIZIONE | obbligatori |
|-----------|-------------|:--------:|
| RootContractAddress | Indirizzo del contratto radice degli Smart Contract di gestione del Consorzio | Sì |
| Web3Client | Oggetto Web3Client ottenuto da New-Web3Connection | Sì |

#### <a name="example"></a>Esempio

```powershell-interactive
Import-ConsortiumManagementContracts -RootContractAddress '<RootContract address>'  -Web3Client $Connection
```

### <a name="import-web3account"></a>Import-Web3Account

Utilizzare questo cmdlet per creare un oggetto che contenga le informazioni per l'account di gestione di un nodo remoto.

`Import-Web3Account -ManagedAccountAddress <String> -ManagedAccountPassword <String>`

| . | DESCRIZIONE | obbligatori |
|-----------|-------------|:--------:|
| ManagedAccountAddress | Indirizzo dell'account membro blockchain | Sì |
| ManagedAccountPassword | Password indirizzo account | Sì |

#### <a name="example"></a>Esempio

```powershell-interactive
Import-Web3Account -ManagedAccountAddress '<Member account address>'  -ManagedAccountPassword '<Member account password>'
```

### <a name="new-web3connection"></a>New-Web3Connection

Utilizzare questo cmdlet per stabilire una connessione all'endpoint RPC di un nodo di transazione.

`New-Web3Connection [-RemoteRPCEndpoint <String>]`

| . | DESCRIZIONE | obbligatori |
|-----------|-------------|:--------:|
| RemoteRPCEndpoint | Indirizzo endpoint membro blockchain | Sì |

#### <a name="example"></a>Esempio

```powershell-interactive
New-Web3Connection -RemoteRPCEndpoint '<Endpoint address>'
```

## <a name="manage-the-consortium-members"></a>Gestire i membri del Consorzio

Usare i cmdlet di gestione dei membri del Consorzio per gestire i membri all'interno del Consorzio. Le azioni disponibili dipendono dal ruolo del Consorzio.

### <a name="get-blockchainmember"></a>Get-BlockchainMember

Utilizzare questo cmdlet per ottenere i dettagli del membro o elencare i membri del Consorzio.

`Get-BlockchainMember [[-Name] <String>] -Members <IContract> -Web3Client <IClient>`

| . | DESCRIZIONE | obbligatori |
|-----------|-------------|:--------:|
| Nome | Nome del membro del servizio blockchain per il quale si desidera recuperare i dettagli. Quando viene immesso un nome, vengono restituiti i dettagli del membro. Quando viene omesso un nome, viene restituito un elenco di tutti i membri del Consorzio. | No |
| Membri | Oggetto members ottenuto da Import-ConsortiumManagementContracts | Sì |
| Web3Client | Oggetto Web3Client ottenuto da New-Web3Connection | Sì |

#### <a name="example"></a>Esempio

[Stabilire una connessione Web3](#establish-a-web3-connection) per impostare la variabile $ContractConnection.

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

Utilizzare questo cmdlet per rimuovere un membro blockchain.

`Remove-BlockchainMember -Name <String> -Members <IContract> -Web3Account <IAccount> -Web3Client <IClient>`

| . | DESCRIZIONE | obbligatori |
|-----------|-------------|:--------:|
| Nome | Nome del membro da rimuovere | Sì |
| Membri | Oggetto members ottenuto da Import-ConsortiumManagementContracts | Sì |
| Web3Account | Oggetto Web3Account ottenuto da Import-Web3Account | Sì |
| Web3Client | Oggetto Web3Client ottenuto da New-Web3Connection | Sì |

#### <a name="example"></a>Esempio

[Stabilire una connessione Web3](#establish-a-web3-connection) per impostare le variabili $ContractConnection e $MemberAccount.

```powershell-interactive
$ContractConnection | Remove-BlockchainMember -Name <Member Name> -Web3Account $MemberAccount
```

### <a name="set-blockchainmember"></a>Set-BlockchainMember

Utilizzare questo cmdlet per impostare gli attributi del membro blockchain, inclusi il nome visualizzato e il ruolo Consortium.

Gli amministratori del Consorzio possono impostare **DisplayName** e **Role** per tutti i membri. Un membro del consorzio con il ruolo utente può modificare solo il nome visualizzato di un membro.

```
Set-BlockchainMember -Name <String> [-DisplayName <String>] [-AccountAddress <String>] [-Role <String>]
 -Members <IContract> -Web3Account <IAccount> -Web3Client <IClient>
```

| . | DESCRIZIONE | obbligatori |
|-----------|-------------|:--------:|
| Nome | Nome del membro blockchain | Sì |
| displayName | Nuovo nome visualizzato | No |
| AccountAddress | Indirizzo account | No |
| Membri | Oggetto members ottenuto da Import-ConsortiumManagementContracts | Sì |
| Web3Account | Oggetto Web3Account ottenuto da Import-Web3Account | Sì |
| Web3Client |  Oggetto Web3Client ottenuto da New-Web3Connection| Sì |

#### <a name="example"></a>Esempio

[Stabilire una connessione Web3](#establish-a-web3-connection) per impostare le variabili $ContractConnection e $MemberAccount.

```powershell-interactive
$ContractConnection | Set-BlockchainMember -Name <Member Name> -DisplayName <Display name> -Web3Account $MemberAccount
```

## <a name="manage-the-consortium-members-invitations"></a>Gestire gli inviti dei membri del Consorzio

Usare i cmdlet di gestione degli inviti dei membri del Consorzio per gestire gli inviti dei membri del Consorzio. Le azioni disponibili dipendono dal ruolo del Consorzio.

### <a name="new-blockchainmemberinvitation"></a>New-BlockchainMemberInvitation

Utilizzare questo cmdlet per invitare nuovi membri al Consortium.

```
New-BlockchainMemberInvitation -SubscriptionId <String> -Role <String> -Members <IContract>
 -Web3Account <IAccount> -Web3Client <IClient>
```

| . | DESCRIZIONE | obbligatori |
|-----------|-------------|:--------:|
| SubscriptionId | ID sottoscrizione di Azure del membro da invitare | Sì |
| Ruolo | Ruolo del Consorzio. I valori possono essere amministratore o utente. ADMIN è il ruolo di amministratore del Consorzio. L'utente è il ruolo del membro Consortium. | Sì |
| Membri | Oggetto members ottenuto da Import-ConsortiumManagementContracts | Sì |
| Web3Account | Oggetto Web3Account ottenuto da Import-Web3Account | Sì |
| Web3Client | Oggetto Web3Client ottenuto da New-Web3Connection | Sì |

#### <a name="example"></a>Esempio

[Stabilire una connessione Web3](#establish-a-web3-connection) per impostare le variabili $ContractConnection e $MemberAccount.

```powershell-interactive
$ContractConnection | New-BlockchainMemberInvitation -SubscriptionId <Azure Subscription ID> -Role USER -Web3Account $MemberAccount
```

### <a name="get-blockchainmemberinvitation"></a>Get-BlockchainMemberInvitation

Utilizzare questo cmdlet per recuperare o elencare lo stato di invito di un membro del Consorzio.

`Get-BlockchainMemberInvitation [[-SubscriptionId] <String>] -Members <IContract> -Web3Client <IClient>`

| . | DESCRIZIONE | obbligatori |
|-----------|-------------|:--------:|
| SubscriptionId | ID sottoscrizione di Azure del membro da invitare. Se viene specificato l'ID sottoscrizione, vengono restituiti i dettagli dell'invito dell'ID sottoscrizione. Se l'ID sottoscrizione viene omesso, viene restituito un elenco di tutti gli inviti dei membri. | No |
| Membri | Oggetto members ottenuto da Import-ConsortiumManagementContracts | Sì |
| Web3Client | Oggetto Web3Client ottenuto da New-Web3Connection | Sì |

#### <a name="example"></a>Esempio

[Stabilire una connessione Web3](#establish-a-web3-connection) per impostare la variabile $ContractConnection.

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

Utilizzare questo cmdlet per revocare l'invito di un membro del Consorzio.

```
Remove-BlockchainMemberInvitation -SubscriptionId <String> -Members <IContract> -Web3Account <IAccount>
 -Web3Client <IClient>
```

| . | DESCRIZIONE | obbligatori |
|-----------|-------------|:--------:|
| SubscriptionId | ID sottoscrizione di Azure del membro da revocare | Sì |
| Membri | Oggetto members ottenuto da Import-ConsortiumManagementContracts | Sì |
| Web3Account | Oggetto Web3Account ottenuto da Import-Web3Account | Sì |
| Web3Client | Oggetto Web3Client ottenuto da New-Web3Connection | Sì |

#### <a name="example"></a>Esempio

[Stabilire una connessione Web3](#establish-a-web3-connection) per impostare le variabili $ContractConnection e $MemberAccount.

```powershell-interactive
$ContractConnection | Remove-BlockchainMemberInvitation -SubscriptionId <Subscription ID> -Web3Account $MemberAccount
```

### <a name="set-blockchainmemberinvitation"></a>Set-BlockchainMemberInvitation

Utilizzare questo cmdlet per impostare il **ruolo** per un invito esistente. Solo gli amministratori del Consorzio possono modificare gli inviti.

```
Set-BlockchainMemberInvitation -SubscriptionId <String> -Role <String> -Members <IContract>
 -Web3Account <IAccount> -Web3Client <IClient>
```

| . | DESCRIZIONE | obbligatori |
|-----------|-------------|:--------:|
| SubscriptionId | ID sottoscrizione di Azure del membro da invitare | Sì |
| Ruolo | Nuovo ruolo del Consorzio per l'invito. I valori possono essere **utente** o **amministratore**. | Sì |
| Membri |  Oggetto members ottenuto da Import-ConsortiumManagementContracts | Sì |
| Web3Account | Oggetto Web3Account ottenuto da Import-Web3Account | Sì |
| Web3Client | Oggetto Web3Client ottenuto da New-Web3Connection | Sì |

#### <a name="example"></a>Esempio

[Stabilire una connessione Web3](#establish-a-web3-connection) per impostare le variabili $ContractConnection e $MemberAccount.

```powershell-interactive
$ContractConnection | Set-BlockchainMemberInvitation -SubscriptionId <Azure subscription ID> -Role USER -Web3Account $MemberAccount
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su consorzi, membri e nodi, vedere il [Consorzio di servizi di Azure blockchain](consortium.md)
