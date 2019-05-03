---
title: Gestione di Azure Blockchain Service consortium usando PowerShell
description: Come gestire i membri consortium servizio Blockchain di Azure con PowerShell
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: bef0c5d776e8ab6424b8604a49782088c45b0538
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028231"
---
# <a name="manage-consortium-members-in-azure-blockchain-service-using-powershell"></a>Gestire i membri consortium nel servizio di Blockchain di Azure con PowerShell

È possibile usare PowerShell per gestire i membri consortium blockchain per il servizio di Blockchain di Azure. I membri con un privilegio di amministratore possono invitare, aggiungere, rimuovere e cambia i ruoli per tutti i partecipanti al consorzio di blockchain. I membri con privilegi di utente possono visualizzare tutti i partecipanti al consorzio blockchain e possono modificare il nome del membro.

## <a name="prerequisites"></a>Prerequisiti

* [Creare un membro di blockchain usando il portale di Azure](create-member.md)
* Per altre informazioni su consorzi, membri e i nodi, vedere [consortium servizio Blockchain di Azure](consortium.md)

## <a name="launch-azure-cloud-shell"></a>Avviare Azure Cloud Shell

Azure Cloud Shell è una shell interattiva gratuita che può essere usata per eseguire la procedura di questo articolo. Include strumenti comuni di Azure preinstallati e configurati per l'uso con l'account. 

È anche possibile avviare Cloud Shell in una scheda separata del browser visitando [https://shell.azure.com/powershell](https://shell.azure.com/powershell). Selezionare **Copia** per copiare i blocchi di codice, incollarli in Cloud Shell e premere INVIO per eseguirli.

## <a name="install-powershell-module"></a>Installare il modulo PowerShell

Installare il pacchetto Microsoft.AzureBlockchainService.ConsortiumManagement.PS da PowerShell Gallery.

```powershell
Install-Module -Name Microsoft.AzureBlockchainService.ConsortiumManagement.PS -Scope CurrentUser
Import-Module Microsoft.AzureBlockchainService.ConsortiumManagement.PS
```

## <a name="establish-a-web3-connection"></a>Stabilire una connessione Web3

Per gestire i membri consortium, è necessario stabilire una connessione Web3 all'endpoint di servizio di Azure Blockchain membro. È possibile usare questo script per impostare le variabili globali che possono essere utilizzate quando si chiama il cmdlet di gestione consortium.

```powershell
$Connection = New-Web3Connection -RemoteRPCEndpoint '<Endpoint address>'
$MemberAccount = Import-Web3Account -ManagedAccountAddress '<Member account address>' -ManagedAccountPassword '<Member account password>'
$ContractConnection = Import-ConsortiumManagementContracts -RootContractAddress '<RootContract address>' -Web3Client $Connection
```

Sostituire \<password dell'account del membro\> con la password dell'account membri usata durante la creazione del membro.

Trovare gli altri valori nel portale di Azure:

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Passare al membro predefinito del servizio di Azure Blockchain **Panoramica** pagina.

    ![Panoramica dei membri](./media/manage-consortium-powershell/member-overview.png)

    Sostituire \<account di membro\>, e \<RootContract indirizzo\> con i valori dal portale.

1. Per l'indirizzo dell'endpoint, selezionare **nodi transazione** e selezionare un nodo di transazione.
1. Selezionare **stringhe di connessione**.

    ![Stringhe di connessione](./media/manage-consortium-powershell/connection-strings.png)

    Sostituire \<indirizzo dell'Endpoint\> con il valore dal **HTTPS (chiave di accesso 1)** oppure **HTTPS (chiave di accesso 2)**.

## <a name="network-and-smart-contract-management"></a>Rete e gestione dei contratti intelligenti

Usare la rete e i cmdlet di contratto intelligente per stabilire una connessione con le blockchain endpoint smart Contract responsabile della gestione consortium.

### <a name="import-consortiummanagementcontracts"></a>Import-ConsortiumManagementContracts

Si connette a consortium gestione intelligente contratti, che vengono usati per gestire e applicare i membri all'interno del consortium.

```powershell
Import-ConsortiumManagementContracts -RootContractAddress <String> -Web3Client <IClient>
```

| Parametro | DESCRIZIONE | Obbligatoria |
|-----------|-------------|:--------:|
| RootContractAddress | L'indirizzo radice del contratto dei contratti consortium gestione intelligente | Sì |
| Web3Client | Oggetto Web3Client ottenuto da New-Web3Connection | Sì |

**Esempio**

```powershell
Import-ConsortiumManagementContracts -RootContractAddress '<RootContract address>'  -Web3Client $Connection
```

### <a name="import-web3account"></a>Import-Web3Account

Usare questo cmdlet per creare un oggetto per mantenere l'account di gestione di informazioni nodo remoto.

```powershell
Import-Web3Account -ManagedAccountAddress <String> -ManagedAccountPassword <String>
```

| Parametro | DESCRIZIONE | Obbligatoria |
|-----------|-------------|:--------:|
| ManagedAccountAddress | Indirizzo dell'account membro Blockchain | Sì |
| ManagedAccountPassword | Password dell'account indirizzo | Sì |

**Esempio**

```powershell
Import-Web3Account -ManagedAccountAddress '<Member account address>'  -ManagedAccountPassword '<Member account password>'
```

### <a name="new-web3connection"></a>Nuovo Web3Connection

Stabilisce una connessione all'endpoint RPC del nodo di una transazione.

```powershell
New-Web3Connection [-RemoteRPCEndpoint <String>]
```

| Parametro | DESCRIZIONE | Obbligatorio |
|-----------|-------------|:--------:|
| RemoteRPCEndpoint | Indirizzo dell'endpoint membro Blockchain | Sì |


**Esempio**

```powershell
New-Web3Connection -RemoteRPCEndpoint '<Endpoint address>'
```

## <a name="consortium-member-management"></a>Gestione dei membri Consortium

Per gestire i membri all'interno del consorzio, usare i cmdlet di gestione membro consortium. Azioni disponibili dipendono dal proprio ruolo consortium.

### <a name="get-blockchainmember"></a>Get-BlockchainMember

Ottiene i dettagli relativi ai membri o elenca i membri di consorzio.

```powershell
Get-BlockchainMember [[-Name] <String>] -Members <IContract> -Web3Client <IClient>
```

| Parametro | DESCRIZIONE | Obbligatoria |
|-----------|-------------|:--------:|
| NOME | Il nome del membro del servizio di Blockchain di Azure a cui si desidera recuperare informazioni dettagliate su. Se si specifica un nome di membro, vengono restituiti i dettagli del membro. Se viene omesso, viene restituito un elenco di tutti i membri consortium. | No  |
| Membri | Oggetto membri ottenuto dall'importazione ConsortiumManagementContracts | Sì |
| Web3Client | Oggetto Web3Client ottenuto da New-Web3Connection | Sì |

**Esempio**

```powershell
$ContractConnection | Get-BlockchainMember -Name <Member Name>
```

**Output di esempio**

```
Name           : myblockchainmember
CorrelationId  : 0
DisplayName    : myCompany
SubscriptionId : <Azure subscription ID>
AccountAddress : 0x85b911c9e103d6405573151258d668479e9ebeef
Role           : ADMIN
```

### <a name="remove-blockchainmember"></a>Remove-BlockchainMember

Rimuove un membro di blockchain.

```powershell
Remove-BlockchainMember -Name <String> -Members <IContract> -Web3Account <IAccount> -Web3Client <IClient>
```

| Parametro | DESCRIZIONE | Obbligatoria |
|-----------|-------------|:--------:|
| NOME | Nome del membro da rimuovere | Sì |
| Membri | Oggetto membri ottenuto dall'importazione ConsortiumManagementContracts | Sì |
| Web3Account | Oggetto Web3Account ottenuto dall'importazione Web3Account | Sì |
| Web3Client | Oggetto Web3Client ottenuto da New-Web3Connection | Sì |

**Esempio**

```powershell
$ContractConnection | Remove-BlockchainMember -Name <Member Name> -Web3Account $MemberAccount
```

### <a name="set-blockchainmember"></a>Set-BlockchainMember

Imposta gli attributi dei membri inclusi display name e consortium ruolo blockchain.

Consortium gli amministratori possono impostare **DisplayName** e **ruolo** per tutti i membri. Membro di consorzio con il ruolo utente può modificare solo nome visualizzato del proprio membro.

```powershell
Set-BlockchainMember -Name <String> [-DisplayName <String>] [-AccountAddress <String>] [-Role <String>]
 -Members <IContract> -Web3Account <IAccount> -Web3Client <IClient>
```

| Parametro | DESCRIZIONE | Obbligatoria |
|-----------|-------------|:--------:|
| NOME | Nome del membro blockchain | Sì |
| DisplayName | Nuovo nome visualizzato | No  |
| AccountAddress | Indirizzo dell'account | No  |
| Membri | Oggetto membri ottenuto dall'importazione ConsortiumManagementContracts | Sì |
| Web3Account | Oggetto Web3Account ottenuto dall'importazione Web3Account | Sì |
| Web3Client |  Oggetto Web3Client ottenuto da New-Web3Connection| Sì |

**Esempio**

```powershell
$ContractConnection | Set-BlockchainMember -Name <Member Name> -DisplayName <Display name> -Web3Account $MemberAccount
```

## <a name="consortium-member-invitation-management"></a>Gestione di invito di membri Consortium

Usare i cmdlet di gestione di consortium membro invito per gestire gli inviti membro consortium. Azioni disponibili dipendono dal proprio ruolo consortium.

### <a name="new-blockchainmemberinvitation"></a>New-BlockchainMemberInvitation

Invita nuovi membri per il consorzio.

```powershell
New-BlockchainMemberInvitation -SubscriptionId <String> -Role <String> -Members <IContract>
 -Web3Account <IAccount> -Web3Client <IClient>
```

| Parametro | DESCRIZIONE | Obbligatorio |
|-----------|-------------|:--------:|
| SubscriptionId | ID sottoscrizione di Azure di membri invitati | Sì |
| Ruolo | Ruolo Consortium. Valori possono essere amministratore o utente. AMMINISTRATORE è il ruolo di amministratore consortium. UTENTE è il ruolo di membro consortium. | Sì |
| Membri | Oggetto membri ottenuto dall'importazione ConsortiumManagementContracts | Sì |
| Web3Account | Oggetto Web3Account ottenuto dall'importazione Web3Account | Sì |
| Web3Client | Oggetto Web3Client ottenuto da New-Web3Connection | Sì |

**Esempio**

```powershell
$ContractConnection | New-BlockchainMemberInvitation -SubscriptionId <Azure Subscription ID> -Role USER -Web3Account $MemberAccount
```

### <a name="get-blockchainmemberinvitation"></a>Get-BlockchainMemberInvitation

Recupera o elenca lo stato dell'invito di membri consortium.

```powershell
Get-BlockchainMemberInvitation [[-SubscriptionId] <String>] -Members <IContract> -Web3Client <IClient>
```

| Parametro | DESCRIZIONE | Obbligatorio |
|-----------|-------------|:--------:|
| SubscriptionId | ID sottoscrizione di Azure di membri invitati. Se viene specificato l'ID sottoscrizione, vengono restituiti i dettagli di invito dell'ID sottoscrizione. Se si omette l'ID sottoscrizione, viene restituito un elenco di tutti gli inviti di membro. | No  |
| Membri | Oggetto membri ottenuto dall'importazione ConsortiumManagementContracts | Sì |
| Web3Client | Oggetto Web3Client ottenuto da New-Web3Connection | Sì |

**Esempio**

```powershell
$ContractConnection | Get-BlockchainMemberInvitation – SubscriptionId <Azure subscription ID>
```

**Output di esempio**

```
SubscriptionId                       Role CorrelationId
--------------                       ---- -------------
<Azure subscription ID>              USER             2
```

### <a name="remove-blockchainmemberinvitation"></a>Remove-BlockchainMemberInvitation

Revoca un invito di membri consortium.

```powershell
Remove-BlockchainMemberInvitation -SubscriptionId <String> -Members <IContract> -Web3Account <IAccount>
 -Web3Client <IClient>
```

| Parametro | DESCRIZIONE | Obbligatorio |
|-----------|-------------|:--------:|
| SubscriptionId | ID sottoscrizione di Azure di membro a cui revocare | Sì |
| Membri | Oggetto membri ottenuto dall'importazione ConsortiumManagementContracts | Sì |
| Web3Account | Oggetto Web3Account ottenuto dall'importazione Web3Account | Sì |
| Web3Client | Oggetto Web3Client ottenuto da New-Web3Connection | Sì |

**Esempio**

```powershell
$ContractConnection | Remove-BlockchainMemberInvitation -SubscriptionId <Subscription ID> -Web3Account $MemberAccount
```

### <a name="set-blockchainmemberinvitation"></a>Set-BlockchainMemberInvitation

Imposta il **ruolo** per un invito esistente. Solo gli amministratori consortium possono modificare gli inviti.

```powershell
Set-BlockchainMemberInvitation -SubscriptionId <String> -Role <String> -Members <IContract>
 -Web3Account <IAccount> -Web3Client <IClient>
```

| Parametro | DESCRIZIONE | Obbligatoria |
|-----------|-------------|:--------:|
| SubscriptionId | ID sottoscrizione di Azure di membri invitati | Sì |
| Ruolo | Nuovo ruolo consortium per invito. I possibili valori sono **utente** o **ADMIN** | Sì |
| Membri |  Oggetto membri ottenuto dall'importazione ConsortiumManagementContracts | Sì |
| Web3Account | Oggetto Web3Account ottenuto dall'importazione Web3Account | Sì |
| Web3Client | Oggetto Web3Client ottenuto da New-Web3Connection | Sì |

**Esempio**

```powershell
$ContractConnection | Set-BlockchainMemberInvitation -SubscriptionId <Azure subscription ID> -Role USER -Web3Account $MemberAccount
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su consorzi, membri e i nodi, vedere:

> [!div class="nextstepaction"]
> [Azure consortium Blockchain Service](consortium.md)