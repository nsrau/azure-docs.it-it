---
title: Configurare i nodi delle transazioni di servizio di Azure Blockchain
description: Come configurare i nodi delle transazioni di servizio di Azure Blockchain
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: seal
manager: femila
ms.openlocfilehash: dffeb81ae1eb244c38639a1241c0581e6fcdf94a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65027961"
---
# <a name="configure-azure-blockchain-service-transaction-nodes"></a>Configurare i nodi delle transazioni di servizio di Azure Blockchain

Per interagire con il servizio di Azure Blockchain, questa operazione tramite la connessione a uno o più nodi di transazione nel membro di blockchain.  Per interagire con i nodi delle transazioni, è necessario configurare i nodi per l'accesso.

## <a name="prerequisites"></a>Prerequisiti

* [Creare un membro di Azure Blockchain](create-member.md)

## <a name="transaction-node-overview"></a>Cenni preliminari sul nodo delle transazioni

Nodi di transazione vengono usati per inviare le transazioni di blockchain Blockchain nel servizio di Azure tramite un endpoint pubblico. Il nodo della transazione predefinito contiene la chiave privata dell'account Ethereum registrato nella blockchain e di conseguenza non può essere eliminato.

Per visualizzare i dettagli del nodo predefinito delle transazioni:

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Passare al membro del servizio Azure Blockchain. Selezionare **nodi transazione**.

    ![Selezionare il nodo della transazione predefinito](./media/configure-transaction-nodes/nodes.png)

    Dettagli della panoramica includono gli indirizzi degli endpoint pubblici e la chiave pubblica.

## <a name="create-transaction-node"></a>Creare il nodo di transazioni

È possibile aggiungere fino a nove nodi delle transazioni aggiuntivi al membro blockchain, per un totale di dieci nodi delle transazioni. Quando si aggiungono nodi di transazione, è possibile aumentare la scalabilità o distribuire il carico. Ad esempio, hai un endpoint del nodo delle transazioni per le applicazioni client diverse.

Per aggiungere un nodo transazione:

1. Nel portale di Azure, passare al membro servizio Blockchain di Azure e selezionare **nodi transazione > Aggiungi**.
1. Completare le impostazioni per il nuovo nodo di transazione.

    ![Aggiungere il nodo delle transazioni](./media/configure-transaction-nodes/add-node.png)

    | Impostazione | DESCRIZIONE |
    |---------|-------------|
    | Name | Nome del nodo di transazioni. Il nome viene usato per creare l'indirizzo DNS per l'endpoint del nodo di transazioni. Ad esempio: `newnode-myblockchainmember.blockchain.azure.com`. Il nome del nodo non può essere modificato dopo averla creata. |
    | Password | Impostare una password complessa. Usare la password per accedere all'endpoint del nodo di transazione con l'autenticazione di base.

1. Selezionare **Create**.

    Il provisioning del nuovo nodo di transazioni richiede circa 10 minuti. I nodi delle transazioni aggiuntivi addebitati dei costi. Per altre informazioni sui costi, vedere [prezzi di Azure](https://aka.ms/ABSPricing).

## <a name="endpoints"></a>Endpoint

I nodi delle transazioni hanno un nome DNS univoco ed endpoint pubblici.

Per visualizzare i dettagli dell'endpoint del nodo di una transazione:

1. Nel portale di Azure, passare a uno dei nodi del servizio di Azure Blockchain membro delle transazioni e selezionare **Panoramica**.

    ![Endpoint](./media/configure-transaction-nodes/endpoints.png)

Gli endpoint del nodo transazione sono sicure e richiedono l'autenticazione. È possibile connettersi a un endpoint di transazione utilizzando l'autenticazione di Azure AD, l'autenticazione di base HTTPS e l'utilizzo di una chiave di accesso tramite HTTPS o Websocket su SSL.

### <a name="azure-active-directory-access-control"></a>Azure Active Directory Access Control

Gli endpoint di Azure Blockchain Service transazione nodo supportano l'autenticazione di Azure Active Directory (Azure AD). È possibile concedere a utente, gruppo e l'accesso dell'entità servizio Azure AD all'endpoint.

Per concedere Azure di controllo di accesso AD all'endpoint:

1. Nel portale di Azure, passare al membro servizio Blockchain di Azure e selezionare **nodi transazione > controllo di accesso (IAM) > Aggiungi > Aggiungi l'assegnazione di ruolo**.
1. Creare una nuova assegnazione di ruolo per un utente, gruppo o entità servizio (i ruoli applicazione).

    ![Aggiungere il ruolo IAM](./media/configure-transaction-nodes/add-role.png)

    | Impostazione | Azione |
    |---------|-------------|
    | Ruolo | Selezionare **Owner**, **collaboratore**, o **lettore**.
    | Assegna accesso a | Selezionare **utente, gruppo o entità servizio Azure AD**.
    | Select | Cercare l'utente, gruppo o entità servizio da aggiungere.

1. Selezionare **salvare** per aggiungere l'assegnazione di ruolo.

Per altre informazioni sul controllo di accesso di Azure AD, vedere [gestire l'accesso alle risorse di Azure tramite RBAC e il portale di Azure](../../role-based-access-control/role-assignments-portal.md)

Per informazioni dettagliate su come connettersi usando l'autenticazione di Azure AD, vedere [connettersi al nodo usando l'autenticazione di AAD](configure-aad.md).

### <a name="basic-authentication"></a>Autenticazione di base

Per l'autenticazione di base HTTPS, credenziali nome utente e password vengono passate nell'intestazione della richiesta HTTPS all'endpoint.

È possibile visualizzare i dettagli dell'endpoint dell'autenticazione di base del nodo di una transazione nel portale di Azure. Passare a uno dei nodi delle transazioni membro servizio Blockchain di Azure e selezionare **l'autenticazione di base** nelle impostazioni.

![Autenticazione di base](./media/configure-transaction-nodes/basic.png)

Il nome utente è il nome del nodo e non può essere modificato.

Per usare l'URL, sostituire \<password\> con la password impostata quando il nodo è stato eseguito il provisioning. È possibile aggiornare la password selezionando **la reimpostazione della password**.

### <a name="access-keys"></a>Chiavi di accesso

Per l'autenticazione chiave di accesso, la chiave di accesso è incluso nell'URL dell'endpoint. Quando il nodo delle transazioni viene eseguito il provisioning, vengono generate due chiavi di accesso. Una chiave di accesso utilizzabile per l'autenticazione. Due chiavi consentono di ruotare le chiavi e modifica.

È possibile visualizzare i dettagli chiave di accesso del nodo di una transazione e copiare gli indirizzi degli endpoint che includono le chiavi di accesso. Passare a uno dei nodi delle transazioni membro servizio Blockchain di Azure e selezionare **chiavi di accesso** nelle impostazioni.

### <a name="firewall-rules"></a>Regole del firewall

Le regole del firewall consentono di limitare gli indirizzi IP che è possono tentare di eseguire l'autenticazione per il nodo della transazione.  Se è configurata alcuna regola di firewall per il nodo della transazione, non è accessibile da qualsiasi entità.  

Per visualizzare le regole del firewall del nodo di una transazione, passare a uno dei nodi del servizio di Azure Blockchain membro delle transazioni e selezionare **regole del Firewall** nelle impostazioni.

È possibile aggiungere regole del firewall tramite l'immissione di nome di una regola, IP iniziale, indirizzo e un indirizzo IP finale indirizzi nel **regole del Firewall** griglia.

![Regole del firewall](./media/configure-transaction-nodes/firewall-rules.png)

Per abilitare:

* **Singolo indirizzo IP:** Configurare lo stesso indirizzo IP per il valore iniziale e finale degli indirizzi IP.
* **Intervallo di indirizzi IP:** Configurare l'inizio e fine intervallo di indirizzi IP. Ad esempio, un intervallo di partire 10.221.34.0 e terminando 10.221.34.255 consentirebbe 10.221.34.xxx intera subnet.
* **Consenti tutti gli indirizzi IP:** Configurare l'indirizzo IP iniziale per 0.0.0.0 e 255.255.255.255 l'indirizzo IP finale.

## <a name="connection-strings"></a>Stringhe di connessione

Sintassi della stringa di connessione per il nodo della transazione viene fornita per basic tramite chiavi di accesso o autenticazione. Vengono fornite le stringhe di connessione, incluse le chiavi di accesso su HTTPS e WebSocket.

È possibile visualizzare le stringhe di connessione del nodo di una transazione e copiare gli indirizzi degli endpoint. Passare a uno dei nodi delle transazioni membro servizio Blockchain di Azure e selezionare **stringhe di connessione** nelle impostazioni.

![Stringhe di connessione](./media/configure-transaction-nodes/connection-strings.png)

## <a name="sample-code"></a>Codice di esempio

Per attivare rapidamente la connessione al nodo delle transazioni tramite Web3, Nethereum, Web3js e Truffle viene fornito codice di esempio.

È possibile visualizzare codice di connessione di esempio del nodo di una transazione e copiarla per poterla usare con gli strumenti di sviluppo più diffusi. Passare a uno dei nodi delle transazioni membro servizio Blockchain di Azure e selezionare **esempio di codice** nelle impostazioni.

Scegliere la scheda Web3 o Nethereum per visualizzare il codice di esempio che si desidera utilizzare.

![Codice di esempio](./media/configure-transaction-nodes/sample-code.png)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Configurare i nodi delle transazioni tramite la CLI di Azure](manage-cli.md)
