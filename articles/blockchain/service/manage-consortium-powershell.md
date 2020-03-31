---
title: Gestire i membri del consorzio del servizio Blockchain di Azure - PowerShellManage Azure Blockchain Service consortium members - PowerShell
description: Informazioni su come gestire i membri del consorzio del servizio Blockchain di Azure usando Azure PowerShell.Learn how to manage Azure Blockchain Service consortium members by using Azure PowerShell.
ms.date: 10/14/2019
ms.topic: article
ms.reviewer: zeyadr
ms.openlocfilehash: e819dd39481b58d446384a5e2253c548ce0c267c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77505975"
---
# <a name="manage-consortium-members-in-azure-blockchain-service-using-powershell"></a>Gestire i membri del consorzio nel servizio Blockchain di Azure tramite PowerShellManage consortium members in Azure Blockchain Service using PowerShell

È possibile usare PowerShell per gestire i membri del consorzio blockchain per il servizio Blockchain di Azure.You can use PowerShell to manage blockchain consortium members for your Azure Blockchain Service. I membri che dispongono di privilegi di amministratore possono invitare, aggiungere, rimuovere e modificare i ruoli per tutti i partecipanti al consorzio blockchain. I membri che dispongono di privilegi utente possono visualizzare tutti i partecipanti al consorzio blockchain e modificare il nome visualizzato del membro.

## <a name="prerequisites"></a>Prerequisiti

* Creare un membro blockchain tramite il portale di [Azure.](create-member.md)
* Per altre informazioni su consorzi, membri e nodi, vedere [Consorzio di servizi Blockchain](consortium.md)di Azure.For more information about consortia, members, and nodes, see Azure Blockchain Service consortium .

## <a name="open-azure-cloud-shell"></a>Aprire Azure Cloud Shell

Azure Cloud Shell è una shell interattiva gratuita che può essere usata per eseguire la procedura di questo articolo. Include strumenti comuni di Azure preinstallati e configurati per l'uso con l'account.

È anche possibile aprire Cloud Shell in una scheda del browser separata accedendo a [shell.azure.com/powershell](https://shell.azure.com/powershell). Selezionare **Copia** per copiare i blocchi di codice, incollarlo in Cloud Shell e selezionare **Invio** per eseguirlo.

## <a name="install-the-powershell-module"></a>Installa il modulo PowerShell

Installare il pacchetto Microsoft.AzureBlockchainService.ConsortiumManagement.PS da PowerShell Gallery.Install the Microsoft.AzureBlockchainService.ConsortiumManagement.PS package from the PowerShell Gallery.

```powershell-interactive
Install-Module -Name Microsoft.AzureBlockchainService.ConsortiumManagement.PS -Scope CurrentUser
Import-Module Microsoft.AzureBlockchainService.ConsortiumManagement.PS
```

## <a name="set-the-information-preference"></a>Impostare le preferenze relative alle informazioni

È possibile ottenere ulteriori informazioni durante l'esecuzione dei cmdlet impostando la variabile di preferenza informazioni. Per impostazione predefinita, *$InformationPreference* è impostato su *SilentlyContinue*.

Per ulteriori informazioni dettagliate dai cmdlet, impostare la preferenza in PowerShell come segue:

```powershell-interactive
$InformationPreference = 'Continue'
```

## <a name="establish-a-web3-connection"></a>Stabilire una connessione Web3

Per gestire i membri del consorzio, stabilire una connessione Web3 all'endpoint membro del servizio Blockchain. È possibile utilizzare questo script per impostare le variabili globali per chiamare i cmdlet di gestione del consorzio.

```powershell-interactive
$Connection = New-Web3Connection -RemoteRPCEndpoint '<Endpoint address>'
$MemberAccount = Import-Web3Account -ManagedAccountAddress '<Member account address>' -ManagedAccountPassword '<Member account password>'
$ContractConnection = Import-ConsortiumManagementContracts -RootContractAddress '<RootContract address>' -Web3Client $Connection
```

Sostituire * \<La\> password dell'account membro* con la password dell'account membro utilizzata al momento della creazione del membro.

Trovare gli altri valori nel portale di Azure:Find the other values in the Azure portal:

1. Accedere al [portale](https://portal.azure.com)di Azure .
1. Vai alla pagina **Panoramica dei** membri del servizio Blockchain predefinito.

    ![Panoramica dei membri](./media/manage-consortium-powershell/member-overview.png)

    Sostituire * \<Account\> membro* e * \<\> indirizzo RootContract* con i valori del portale.

1. Per l'indirizzo endpoint selezionare **Nodi di transazione**, quindi selezionare il **nodo di transazione predefinito.** Il nodo predefinito ha lo stesso nome del membro blockchain.
1. Selezionare **Stringhe di connessione**.

    ![Stringhe di connessione](./media/manage-consortium-powershell/connection-strings.png)

    Sostituire * \<Endpoint\> address* con il value from **HTTPS (Access key 1)** or **HTTPS (Access key 2)**.

## <a name="manage-the-network-and-smart-contracts"></a>Gestire la rete e i contratti intelligenti

Utilizzare i cmdlet di rete e contratto intelligente per stabilire una connessione ai contratti intelligenti dell'endpoint blockchain responsabili della gestione del consorzio.

### <a name="import-consortiummanagementcontracts"></a>Import-ConsortiumManagementContracts

Utilizzare questo cmdlet per connettersi ai contratti intelligenti della gestione del consorzio. Questi contratti vengono utilizzati per gestire e far rispettare i membri all'interno del consorzio.

`Import-ConsortiumManagementContracts -RootContractAddress <String> -Web3Client <IClient>`

| Parametro | Descrizione | Obbligatoria |
|-----------|-------------|:--------:|
| RootContractAddress | Indirizzo del contratto radice dei contratti intelligenti per la gestione del consorzio | Sì |
| Web3Client (informazioni in lingua inlingua in stato | Oggetto Web3Client ottenuto da New-Web3Connection | Sì |

#### <a name="example"></a>Esempio

```powershell-interactive
Import-ConsortiumManagementContracts -RootContractAddress '<RootContract address>'  -Web3Client $Connection
```

### <a name="import-web3account"></a>Importa-Web3Account

Utilizzare questo cmdlet per creare un oggetto per contenere le informazioni per l'account di gestione di un nodo remoto.

`Import-Web3Account -ManagedAccountAddress <String> -ManagedAccountPassword <String>`

| Parametro | Descrizione | Obbligatoria |
|-----------|-------------|:--------:|
| ManagedAccountAddress (Indirizzo ManagedAccountAddress) | Indirizzo account membro blockchain | Sì |
| ManagedAccountPassword | Password dell'indirizzo dell'account | Sì |

#### <a name="example"></a>Esempio

```powershell-interactive
Import-Web3Account -ManagedAccountAddress '<Member account address>'  -ManagedAccountPassword '<Member account password>'
```

### <a name="new-web3connection"></a>Nuova connessione Web3

Utilizzare questo cmdlet per stabilire una connessione all'endpoint RPC di un nodo di transazione.

`New-Web3Connection [-RemoteRPCEndpoint <String>]`

| Parametro | Descrizione | Obbligatoria |
|-----------|-------------|:--------:|
| RemoteRPCEndpoint (Endpoint RPC remoto) | Indirizzo endpoint membro Blockchain | Sì |

#### <a name="example"></a>Esempio

```powershell-interactive
New-Web3Connection -RemoteRPCEndpoint '<Endpoint address>'
```

## <a name="manage-the-consortium-members"></a>Gestire i membri del consorzio

Utilizzare i cmdlet di gestione dei membri del consorzio per gestire i membri all'interno del consorzio. Le azioni disponibili dipendono dal ruolo del consorzio.

### <a name="get-blockchainmember"></a>Get-BlockchainMember

Utilizzare questo cmdlet per ottenere i dettagli dei membri o i membri del consorzio.

`Get-BlockchainMember [[-Name] <String>] -Members <IContract> -Web3Client <IClient>`

| Parametro | Descrizione | Obbligatoria |
|-----------|-------------|:--------:|
| Nome | Il nome del membro del servizio Blockchain su cui si desidera recuperare i dettagli. Quando viene immesso un nome, restituisce i dettagli del membro. Quando un nome viene omesso, restituisce un elenco di tutti i membri del consorzio. | No |
| Membri | Oggetto Members ottenuto da Import-ConsortiumManagementContracts | Sì |
| Web3Client (informazioni in lingua inlingua in stato | Oggetto Web3Client ottenuto da New-Web3Connection | Sì |

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

| Parametro | Descrizione | Obbligatoria |
|-----------|-------------|:--------:|
| Nome | Nome del membro da rimuovere | Sì |
| Membri | Oggetto Members ottenuto da Import-ConsortiumManagementContracts | Sì |
| Web3Account | Oggetto Web3Account ottenuto da Import-Web3Account | Sì |
| Web3Client (informazioni in lingua inlingua in stato | Oggetto Web3Client ottenuto da New-Web3Connection | Sì |

#### <a name="example"></a>Esempio

[Stabilire una connessione Web3](#establish-a-web3-connection) per impostare le variabili $ContractConnection e $MemberAccount.

```powershell-interactive
$ContractConnection | Remove-BlockchainMember -Name <Member Name> -Web3Account $MemberAccount
```

### <a name="set-blockchainmember"></a>Set-BlockchainMember

Utilizzare questo cmdlet per impostare gli attributi dei membri blockchain, inclusi il nome visualizzato e il ruolo del consorzio.

Gli amministratori del consorzio possono impostare **DisplayName** e **Role** per tutti i membri. Un membro del consorzio con il ruolo utente può modificare solo il nome visualizzato del proprio membro.

```
Set-BlockchainMember -Name <String> [-DisplayName <String>] [-AccountAddress <String>] [-Role <String>]
 -Members <IContract> -Web3Account <IAccount> -Web3Client <IClient>
```

| Parametro | Descrizione | Obbligatoria |
|-----------|-------------|:--------:|
| Nome | Nome del membro blockchain | Sì |
| DisplayName | Nuovo nome visualizzato | No |
| Indirizzo Account | Account address | No |
| Membri | Oggetto Members ottenuto da Import-ConsortiumManagementContracts | Sì |
| Web3Account | Oggetto Web3Account ottenuto da Import-Web3Account | Sì |
| Web3Client (informazioni in lingua inlingua in stato |  Oggetto Web3Client ottenuto da New-Web3Connection| Sì |

#### <a name="example"></a>Esempio

[Stabilire una connessione Web3](#establish-a-web3-connection) per impostare le variabili $ContractConnection e $MemberAccount.

```powershell-interactive
$ContractConnection | Set-BlockchainMember -Name <Member Name> -DisplayName <Display name> -Web3Account $MemberAccount
```

## <a name="manage-the-consortium-members-invitations"></a>Gestire gli inviti dei membri del consorzio

Utilizzare i cmdlet di gestione degli inviti membri del consorzio per gestire gli inviti dei membri del consorzio. Le azioni disponibili dipendono dal ruolo del consorzio.

### <a name="new-blockchainmemberinvitation"></a>New-BlockchainMemberInvito

Utilizzare questo cmdlet per invitare nuovi membri al consorzio.

```
New-BlockchainMemberInvitation -SubscriptionId <String> -Role <String> -Members <IContract>
 -Web3Account <IAccount> -Web3Client <IClient>
```

| Parametro | Descrizione | Obbligatoria |
|-----------|-------------|:--------:|
| SubscriptionId | ID sottoscrizione di Azure del membro da invitareAzure subscription ID of the member to invite | Sì |
| Ruolo | Il ruolo del consorzio. I valori possono essere ADMIN o USER. ADMIN è il ruolo di amministratore del consorzio. USER è il ruolo di membro del consorzio. | Sì |
| Membri | Oggetto Members ottenuto da Import-ConsortiumManagementContracts | Sì |
| Web3Account | Oggetto Web3Account ottenuto da Import-Web3Account | Sì |
| Web3Client (informazioni in lingua inlingua in stato | Oggetto Web3Client ottenuto da New-Web3Connection | Sì |

#### <a name="example"></a>Esempio

[Stabilire una connessione Web3](#establish-a-web3-connection) per impostare le variabili $ContractConnection e $MemberAccount.

```powershell-interactive
$ContractConnection | New-BlockchainMemberInvitation -SubscriptionId <Azure Subscription ID> -Role USER -Web3Account $MemberAccount
```

### <a name="get-blockchainmemberinvitation"></a>Get-BlockchainMemberInvitation

Utilizzare questo cmdlet per recuperare o elencare lo stato di invito di un membro del consorzio.

`Get-BlockchainMemberInvitation [[-SubscriptionId] <String>] -Members <IContract> -Web3Client <IClient>`

| Parametro | Descrizione | Obbligatoria |
|-----------|-------------|:--------:|
| SubscriptionId | ID sottoscrizione di Azure del membro da invitare. Se viene fornito l'ID sottoscrizione, vengono restituiti i dettagli dell'invito dell'ID sottoscrizione. Se l'ID sottoscrizione viene omesso, viene restituito un elenco di tutti gli inviti ai membri. | No |
| Membri | Oggetto Members ottenuto da Import-ConsortiumManagementContracts | Sì |
| Web3Client (informazioni in lingua inlingua in stato | Oggetto Web3Client ottenuto da New-Web3Connection | Sì |

#### <a name="example"></a>Esempio

[Stabilire una connessione Web3](#establish-a-web3-connection) per impostare la variabile $ContractConnection.

```powershell-interactive
$ContractConnection | Get-BlockchainMemberInvitation –SubscriptionId <Azure subscription ID>
```

#### <a name="example-output"></a>Output di esempio

```
SubscriptionId                       Role CorrelationId
--------------                       ---- -------------
<Azure subscription ID>              USER             2
```

### <a name="remove-blockchainmemberinvitation"></a>Remove-BlockchainMemberInvitation

Utilizzare questo cmdlet per revocare l'invito di un membro del consorzio.

```
Remove-BlockchainMemberInvitation -SubscriptionId <String> -Members <IContract> -Web3Account <IAccount>
 -Web3Client <IClient>
```

| Parametro | Descrizione | Obbligatoria |
|-----------|-------------|:--------:|
| SubscriptionId | ID sottoscrizione di Azure del membro da revocare | Sì |
| Membri | Oggetto Members ottenuto da Import-ConsortiumManagementContracts | Sì |
| Web3Account | Oggetto Web3Account ottenuto da Import-Web3Account | Sì |
| Web3Client (informazioni in lingua inlingua in stato | Oggetto Web3Client ottenuto da New-Web3Connection | Sì |

#### <a name="example"></a>Esempio

[Stabilire una connessione Web3](#establish-a-web3-connection) per impostare le variabili $ContractConnection e $MemberAccount.

```powershell-interactive
$ContractConnection | Remove-BlockchainMemberInvitation -SubscriptionId <Subscription ID> -Web3Account $MemberAccount
```

### <a name="set-blockchainmemberinvitation"></a>Set-BlockchainMemberInvitation

Utilizzare questo cmdlet per impostare il **ruolo** per un invito esistente. Solo gli amministratori del consorzio possono modificare gli inviti.

```
Set-BlockchainMemberInvitation -SubscriptionId <String> -Role <String> -Members <IContract>
 -Web3Account <IAccount> -Web3Client <IClient>
```

| Parametro | Descrizione | Obbligatoria |
|-----------|-------------|:--------:|
| SubscriptionId | ID sottoscrizione di Azure del membro da invitareAzure subscription ID of the member to invite | Sì |
| Ruolo | Nuovo ruolo del consorzio per l'invito. I valori possono essere **USER** o **ADMIN**. | Sì |
| Membri |  Oggetto Members ottenuto da Import-ConsortiumManagementContracts | Sì |
| Web3Account | Oggetto Web3Account ottenuto da Import-Web3Account | Sì |
| Web3Client (informazioni in lingua inlingua in stato | Oggetto Web3Client ottenuto da New-Web3Connection | Sì |

#### <a name="example"></a>Esempio

[Stabilire una connessione Web3](#establish-a-web3-connection) per impostare le variabili $ContractConnection e $MemberAccount.

```powershell-interactive
$ContractConnection | Set-BlockchainMemberInvitation -SubscriptionId <Azure subscription ID> -Role USER -Web3Account $MemberAccount
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su consorzi, membri e nodi, vedere Consorzio del servizio Blockchain di [AzureFor](consortium.md) more information about consortia, members, and nodes, see Azure Blockchain Service consortium
