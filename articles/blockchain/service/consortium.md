---
title: Azure blockchain Service Consortium
description: Informazioni sul modo in cui il servizio Azure blockchain usa un consorzio privato
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 10/14/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 04ea4a4ebecec958ba9d9a72711e101adb3690ab
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72329298"
---
# <a name="azure-blockchain-service-consortium"></a>Azure blockchain Service Consortium

Con il servizio blockchain di Azure è possibile creare reti blockchain del consorzio privato in cui ogni rete blockchain può essere limitata a specifici partecipanti nella rete. Solo i partecipanti alla rete blockchain del consorzio privato possono visualizzare e interagire con il blockchain. Le reti del Consorzio nel servizio Azure blockchain possono contenere due tipi di ruoli del partecipante del membro:

* Partecipanti con privilegi **amministrativi** che possono intraprendere azioni di gestione del Consorzio e possono partecipare alle transazioni blockchain.

* **Utenti** -partecipanti che non possono intraprendere alcuna azione di gestione del Consorzio, ma possono partecipare alle transazioni blockchain.

Le reti Consortium possono essere una combinazione di ruoli dei partecipanti e possono avere un numero arbitrario di ogni tipo di ruolo. Deve essere presente almeno un amministratore.

Il diagramma seguente mostra una rete Consortium con più partecipanti:

![Diagramma di rete del consorzio privato](./media/consortium/network-diagram.png)

Con la gestione del Consorzio nel servizio blockchain di Azure, è possibile gestire i partecipanti nella rete Consortium. La gestione del Consorzio è basata sul modello di consenso della rete. Nella versione di anteprima corrente, il servizio Azure blockchain fornisce un modello di consenso centralizzato per la gestione del Consorzio. Qualsiasi partecipante con privilegi con un ruolo di amministratore può eseguire azioni di gestione del Consorzio, ad esempio l'aggiunta o la rimozione di partecipanti da una rete.

## <a name="roles"></a>Ruoli

I partecipanti a un consorzio possono essere individui o organizzazioni e possono essere assegnati a un ruolo utente o a un ruolo di amministratore. Nella tabella seguente sono elencate le differenze di alto livello tra i due ruoli:

| Azione | Ruolo utente | Ruolo amministratore
|--------|:----:|:------------:|
| Crea nuovo membro | SÌ | SÌ |
| Invita nuovi membri | No | SÌ |
| Impostare o modificare il ruolo del partecipante del membro | No | SÌ |
| Modificare il nome visualizzato del membro | Solo per il membro personale | Solo per il membro personale |
| Rimuovere membri | Solo per il membro personale | SÌ |
| Partecipa alle transazioni blockchain | SÌ | SÌ |

### <a name="user-role"></a>Ruolo utente

Gli utenti sono partecipanti al Consorzio senza capacità di amministratore. Non possono partecipare alla gestione dei membri correlati al Consorzio. Gli utenti possono modificare il nome visualizzato del membro e possono rimuovere se stessi da un consorzio.

### <a name="administrator"></a>Amministratore

Un amministratore può gestire i membri all'interno del Consorzio. Un amministratore può invitare membri, rimuovere membri o aggiornare i ruoli dei membri all'interno del Consorzio.
In un consorzio deve essere sempre presente almeno un amministratore. L'ultimo amministratore deve specificare un altro partecipante come ruolo di amministratore prima di uscire da un consorzio.

## <a name="managing-members"></a>Gestione dei membri

Solo gli amministratori possono invitare altri partecipanti al Consorzio. Gli amministratori possono invitare i partecipanti a usare l'ID sottoscrizione di Azure.

Una volta invitati, i partecipanti possono partecipare al Consorzio blockchain distribuendo un nuovo membro nel servizio Azure blockchain. Per visualizzare e partecipare al Consorzio invitato, è necessario specificare lo stesso ID sottoscrizione di Azure usato nell'invito da parte dell'amministratore di rete.

Gli amministratori possono rimuovere qualsiasi partecipante dal Consorzio, inclusi altri amministratori. I membri possono solo rimuovere se stessi da un consorzio.

## <a name="consortium-management-smart-contract"></a>Contratto intelligente di gestione del Consorzio

La gestione del Consorzio nel servizio blockchain di Azure viene eseguita tramite contratti intelligenti di gestione del Consorzio. I contratti intelligenti vengono distribuiti automaticamente nei nodi quando si distribuisce un nuovo membro di blockchain.

L'indirizzo dello Smart Contract di gestione del Consorzio radice può essere visualizzato nella portale di Azure. L' **Indirizzo RootContract** si trova nella sezione Panoramica del membro blockchain.

![Indirizzo RootContract](./media/consortium/rootcontract-address.png)

È possibile interagire con lo Smart Contract di gestione del Consorzio usando il [modulo di PowerShell](manage-consortium-powershell.md)di gestione del Consorzio, portale di Azure o direttamente tramite il contratto intelligente usando il servizio Azure blockchain generato dall'account Ethereum.

## <a name="ethereum-account"></a>Account Ethereum

Quando viene creato un membro, viene creata una chiave dell'account Ethereum. Il servizio Azure blockchain usa la chiave per creare transazioni correlate alla gestione del Consorzio. La chiave dell'account Ethereum viene gestita automaticamente dal servizio blockchain di Azure.

L'account del membro può essere visualizzato nella portale di Azure. L'account membro si trova nella sezione Panoramica del membro blockchain.

![Account membro](./media/consortium/member-account.png)

È possibile reimpostare l'account Ethereum facendo clic sull'account del membro e immettendo una nuova password. Sia l'indirizzo dell'account Ethereum che la password verranno reimpostati.  

## <a name="next-steps"></a>Passaggi successivi

[Come gestire i membri nel servizio Azure blockchain usando PowerShell](manage-consortium-powershell.md)
