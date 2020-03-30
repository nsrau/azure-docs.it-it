---
title: Consorzio Azure Blockchain Service
description: Panoramica di come il servizio Blockchain di Azure implementa le reti blockchain del consorzio.
ms.date: 11/21/2019
ms.topic: conceptual
ms.reviewer: zeyadr
ms.openlocfilehash: 7b8885ba08d35db20d1eb7e75141cb173913b386
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247618"
---
# <a name="azure-blockchain-service-consortium"></a>Consorzio Azure Blockchain Service

Usando il servizio Blockchain di Azure, è possibile creare reti blockchain consorziprivate in cui ogni rete blockchain può essere limitata a partecipanti specifici nella rete. Solo i partecipanti alla rete blockchain del consorzio privato possono visualizzare e interagire con la blockchain. Le reti del consorzio nel servizio Blockchain di Azure possono contenere due tipi di ruoli dei partecipanti membri:Consortium networks in Azure Blockchain Service can contain two types of member participant roles:

* **Amministratore:** partecipanti con privilegi che possono intraprendere azioni di gestione del consorzio e partecipare alle transazioni blockchain.

* **Utente:** partecipanti che non possono intraprendere alcuna azione di gestione del consorzio, ma possono partecipare alle transazioni blockchain.

Le reti del consorzio possono essere un mix di ruoli dei partecipanti e possono avere un numero arbitrario di ogni tipo di ruolo. Deve essere presente almeno un amministratore.

Il diagramma seguente mostra una rete di consorzi con più partecipanti:

![Diagramma della rete privata del consorzio](./media/consortium/network-diagram.png)

Con la gestione del consorzio in Azure Blockchain Service, è possibile gestire i partecipanti nella rete del consorzio. La gestione del consorzio si basa sul modello di consenso della rete. Nella versione di anteprima corrente, il servizio Blockchain di Azure fornisce un modello di consenso centralizzato per la gestione del consorzio. Qualsiasi partecipante privilegiato con un ruolo di amministrazione può intraprendere azioni di gestione del consorzio, ad esempio l'aggiunta o la rimozione di partecipanti da una rete.

## <a name="roles"></a>Ruoli

I partecipanti a un consorzio possono essere individui o organizzazioni e possono essere assegnati a un ruolo utente o a un ruolo di amministratore. Nella tabella seguente sono elencate le differenze di alto livello tra i due ruoli:

| Azione | Ruolo utente | Ruolo amministratore
|--------|:----:|:------------:|
| Crea nuovo membro | Sì | Sì |
| Invitare nuovi membri | No | Sì |
| Impostare o modificare il ruolo del partecipante | No | Sì |
| Modificare il nome visualizzato del membro | Solo per il proprio membro | Solo per il proprio membro |
| Rimuovere membri | Solo per il proprio membro | Sì |
| Partecipare alle transazioni blockchain | Sì | Sì |

### <a name="user-role"></a>Ruolo utente

Gli utenti sono partecipanti del consorzio senza funzionalità di amministratore. Non possono partecipare alla gestione dei membri relativi al consorzio. Gli utenti possono modificare il nome visualizzato del membro e possono rimuovere se stessi da un consorzio.

### <a name="administrator"></a>Amministratore

Un amministratore può gestire i membri all'interno del consorzio. Un amministratore può invitare membri, rimuovere membri o aggiornare i ruoli dei membri all'interno del consorzio.
Deve essere sempre presente almeno un amministratore all'interno di un consorzio. L'ultimo amministratore deve specificare un altro partecipante come ruolo di amministratore prima di uscire da un consorzio.

## <a name="managing-members"></a>Gestione dei membri

Solo gli amministratori possono invitare altri partecipanti al consorzio. Gli amministratori invitano i partecipanti usando il proprio ID sottoscrizione di Azure.Administrators invite participants using their Azure subscription ID.

Una volta invitati, i partecipanti possono unirsi al consorzio blockchain implementando un nuovo membro nel servizio Blockchain di Azure.Once invited, participants can join the blockchain consortium by deploying a new member in Azure Blockchain Service. Per visualizzare e partecipare al consorzio invitato, è necessario specificare lo stesso ID sottoscrizione di Azure usato nell'invito dall'amministratore di rete.

Gli amministratori possono rimuovere qualsiasi partecipante dal consorzio, inclusi altri amministratori. I membri possono solo rimuovere se stessi da un consorzio.

## <a name="consortium-management-smart-contract"></a>Contratto intelligente per la gestione del consorzio

La gestione del consorzio nel servizio Blockchain di Azure viene eseguita tramite contratti intelligenti per la gestione del consorzio. I contratti intelligenti vengono distribuiti automaticamente ai nodi quando si distribuisce un nuovo membro blockchain.

L'indirizzo del contratto intelligente di gestione del consorzio radice può essere visualizzato nel portale di Azure.The address of the root consortium management smart contract can be viewed in the Azure portal. **L'indirizzo RootContract** è nella sezione Panoramica del membro blockchain.

![RootContract indirizzo](./media/consortium/rootcontract-address.png)

È possibile interagire con il contratto intelligente di gestione del consorzio usando il modulo PowerShell per la gestione del consorzio, il portale di Azure o direttamente tramite il contratto intelligente usando l'account Ethereum generato dal servizio Blockchain di Azure.You can interact with the consortium management smart contract using the consortium management [PowerShell module](manage-consortium-powershell.md), Azure portal, or directly through the smart contract using the Azure Blockchain Service generated Ethereum account.

## <a name="ethereum-account"></a>Conto Ethereum

Quando viene creato un membro, viene creata una chiave account Ethereum. Il servizio Blockchain di Azure usa la chiave per creare transazioni correlate alla gestione del consorzio. La chiave dell'account Ethereum viene gestita automaticamente dal servizio Blockchain di Azure.The Ethereum account key is managed by Azure Blockchain Service automatically.

L'account membro può essere visualizzato nel portale di Azure.The member account can be viewed in the Azure portal. L'account membro si trova nella sezione panoramica del membro blockchain.

![Account membro](./media/consortium/member-account.png)

Puoi reimpostare il tuo account Ethereum cliccando sul tuo account membro e inserendo una nuova password. Sia l'indirizzo dell'account Ethereum che la password verranno reimpostati.  

## <a name="next-steps"></a>Passaggi successivi

È possibile accedere alle azioni di gestione del consorzio tramite PowerShell.Consortium management actions can be accessed through PowerShell. Per altre informazioni, vedere Gestire i membri del consorzio nel servizio Blockchain di Azure usando PowerShell.For more information, see [Manage consortium members in Azure Blockchain Service using PowerShell](manage-consortium-powershell.md).
