---
title: Il servizio Azure Blockchain Consortium
description: ''
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: e745a4ee4789ef46a61b5cb0bbf806c41ef631ec
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65027916"
---
# <a name="azure-blockchain-service-consortium"></a>Il servizio Azure Blockchain Consortium

Usa il servizio di Azure Blockchain, è possibile creare consortium privata blockchain reti in cui ogni rete blockchain può essere limitato ai partecipanti specifici nella rete. Solo i partecipanti nella rete privata consortium blockchain possono visualizzare e interagire con le blockchain. Le reti Consortium nel servizio di Azure Blockchain possono contenere due tipi di membro partecipante ruoli:

* **Amministratore** -con privilegi ai partecipanti che consente di eseguire azioni di gestione consortium e possono partecipare alle transazioni di blockchain.

* **Utente** -partecipanti che non possono intraprendere alcuna azione di gestione consortium ma possono partecipare alle transazioni di blockchain.

Reti Consortium possono essere una combinazione di ruoli di partecipanti e possono avere un numero arbitrario di ogni tipo di ruolo. Deve essere presente almeno un amministratore.

Il diagramma seguente mostra una rete di consorzio con più partecipanti:

![Diagramma della rete privata consortium](./media/consortium/network-diagram.png)

Con gestione consortium nel servizio di Azure Blockchain, è possibile gestire partecipanti nella rete consortium. Gestione del consorzio si basa sul modello di consenso della rete. Nella versione di anteprima corrente, Azure Blockchain servizio offre un modello di consenso centralizzato per la gestione consortium. Qualsiasi partecipante con un ruolo di amministratore con privilegi può eseguire azioni di gestione consortium, ad esempio aggiungendo o rimuovendo i partecipanti di una rete.

## <a name="roles"></a>Ruoli

I partecipanti in un consorzio possono essere singoli individui o organizzazioni e possono essere assegnati un ruolo utente o un ruolo di amministratore. Nella tabella seguente sono elencate le differenze generali tra i due ruoli:

| Azione | Ruolo utente | Ruolo amministratore
|--------|:----:|:------------:|
| Crea nuovo membro | Yes | Yes |
| Invita nuovi membri | No | Yes |
| Impostazione o modifica membro partecipante ruolo | No | Yes |
| Modifica nome visualizzato di membro | Solo per un membro | Solo per un membro |
| Rimuovere membri | Solo per un membro | Yes |
| Partecipare alle transazioni di blockchain | Yes | Yes |

### <a name="user-role"></a>Ruolo utente

Gli utenti sono i partecipanti di consorzio con alcuna funzionalità di amministratore. Essi non può essere incluso nella gestione di membri correlati al consorzio. Gli utenti possono modificare il nome del membro e possono rimuovere se stessi un Consortium.

### <a name="administrator"></a>Amministratore

Gli amministratori possono gestire i membri all'interno del consortium. Un amministratore può invitare membri, rimuovere i membri o aggiornare i ruoli di membri all'interno del consorzio.
Deve sempre essere presente almeno un amministratore all'interno di un consortium. L'ultimo amministratore deve specificare un altro partecipante come un ruolo di amministratore prima di lasciare un consorzio.

## <a name="managing-members"></a>Gestione dei membri

Solo gli amministratori possono invitare altri partecipanti al consorzio. Gli amministratori di invitare i partecipanti con relativi ID sottoscrizione di Azure.

Dopo aver visualizzato un messaggio, i partecipanti possono unirsi al consorzio blockchain distribuendo un nuovo membro nel servizio di Azure Blockchain. Per visualizzare e aggiungere il consorzio invitato, è necessario specificare lo stesso ID di sottoscrizione di Azure usato nell'invito dall'amministratore di rete.

Gli amministratori possono rimuovere qualsiasi partecipante Consortium, inclusi altri amministratori. I membri solo possono rimuovere se stessi dal consorzio.

## <a name="consortium-management-smart-contract"></a>Contratto di Consortium gestione intelligente

Gestione Consortium nel servizio di Azure Blockchain avviene tramite smart Contract gestione consortium. La smart Contract vengono distribuiti automaticamente ai nodi quando si distribuisce un nuovo membro di blockchain.

L'indirizzo del contratto radice consortium gestione intelligente può essere visualizzato nel portale di Azure. Il **RootContract indirizzo** si trova nella sezione Panoramica del membro di blockchain.

![Indirizzo RootContract](./media/consortium/rootcontract-address.png)

È possibile interagire con il contratto di smart gestione consortium utilizzando la gestione consortium [modulo di PowerShell](manage-consortium-powershell.md), Azure portale o direttamente tramite il contratto intelligente usando il servizio di Azure Blockchain generato Ethereum account.

## <a name="ethereum-account"></a>Account Ethereum

Quando viene creato un membro, viene creata una chiave dell'account Ethereum. Azure Blockchain Service Usa la chiave per creare transazioni correlate alla gestione consortium. La chiave dell'account Ethereum viene gestita automaticamente dal servizio di Azure Blockchain.

L'account di membro può essere visualizzato nel portale di Azure. L'account del membro è nella sezione Panoramica del membro di blockchain.

![Account di membro](./media/consortium/member-account.png)

È possibile reimpostare l'account Ethereum facendo clic sull'account di membro e immettendo una nuova password. Sia l'indirizzo dell'account Ethereum e la password verrà reimpostate.  

## <a name="next-steps"></a>Passaggi successivi

[Come gestire i membri nel servizio di Blockchain di Azure con PowerShell](manage-consortium-powershell.md)
