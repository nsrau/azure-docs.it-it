---
title: Configurare i nodi delle transazioni del servizio blockchain di Azure
description: Come configurare i nodi delle transazioni del servizio blockchain di Azure
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 11/20/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: janders
manager: femila
ms.openlocfilehash: 2885e5c9376264875cba03865c45b6b1e5d4aaf2
ms.sourcegitcommit: e50a39eb97a0b52ce35fd7b1cf16c7a9091d5a2a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74286912"
---
# <a name="configure-azure-blockchain-service-transaction-nodes"></a>Configurare i nodi delle transazioni del servizio blockchain di Azure

I nodi di transazione vengono usati per inviare le transazioni blockchain al servizio Azure blockchain tramite un endpoint pubblico. Il nodo di transazione predefinito contiene la chiave privata dell'account Ethereum registrato in blockchain e, di conseguenza, non può essere eliminato.

Per visualizzare i dettagli del nodo di transazione predefinito:

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Passare al membro del servizio Azure Blockchain. Selezionare **nodi transazione**.

    ![Selezionare il nodo della transazione predefinito](./media/configure-transaction-nodes/nodes.png)

    I dettagli della panoramica includono gli indirizzi degli endpoint pubblici e la chiave pubblica.

## <a name="create-transaction-node"></a>Creare il nodo di transazioni

È possibile aggiungere fino a nove nodi di transazione aggiuntivi al membro blockchain, per un totale di 10 nodi di transazione. Aggiungendo nodi delle transazioni, è possibile aumentare la scalabilità o distribuire il carico. Ad esempio, è possibile disporre di un endpoint del nodo di transazione per diverse applicazioni client.

Per aggiungere un nodo di transazione:

1. Nella portale di Azure passare al membro del servizio Azure blockchain e selezionare **nodi transazione > Aggiungi**.
1. Completare le impostazioni per il nuovo nodo della transazione.

    ![Aggiungi nodo transazione](./media/configure-transaction-nodes/add-node.png)

    | Impostazione | DESCRIZIONE |
    |---------|-------------|
    | Nome | Nome del nodo di transazioni. Il nome viene usato per creare l'indirizzo DNS per l'endpoint del nodo di transazioni. Ad esempio `newnode-myblockchainmember.blockchain.azure.com`. Il nome del nodo non può essere modificato dopo la creazione. |
    | Password | Impostare una password complessa. Utilizzare la password per accedere all'endpoint del nodo della transazione con l'autenticazione di base.

1. Selezionare **Create**.

    Il provisioning del nuovo nodo di transazioni richiede circa 10 minuti. I nodi di transazione aggiuntivi comportano costi. Per altre informazioni sui costi, vedere [prezzi di Azure](https://aka.ms/ABSPricing).

## <a name="endpoints"></a>Endpoints

I nodi di transazione hanno un nome DNS univoco e endpoint pubblici.

Per visualizzare i dettagli dell'endpoint di un nodo di transazione:

1. Nella portale di Azure passare a uno dei nodi di transazione membro del servizio Azure blockchain e selezionare **Panoramica**.

    ![Endpoints](./media/configure-transaction-nodes/endpoints.png)

Gli endpoint del nodo di transazione sono protetti e richiedono l'autenticazione. È possibile connettersi a un endpoint di transazione usando l'autenticazione Azure AD, l'autenticazione di base HTTPS e usando una chiave di accesso su HTTPS o WebSocket tramite SSL.

### <a name="azure-active-directory-access-control"></a>Azure Active Directory Access Control

Gli endpoint del nodo di transazione del servizio blockchain di Azure supportano l'autenticazione Azure Active Directory (Azure AD). È possibile concedere a Azure AD utente, al gruppo e all'entità servizio l'accesso all'endpoint.

Per concedere Azure AD controllo di accesso all'endpoint:

1. Nella portale di Azure passare al membro del servizio Azure blockchain e selezionare **nodi transazione > controllo di accesso (IAM) > aggiungi > Aggiungi assegnazione ruolo**.
1. Creare una nuova assegnazione di ruolo per un utente, un gruppo o un'entità servizio (ruoli applicazione).

    ![Aggiungi ruolo IAM](./media/configure-transaction-nodes/add-role.png)

    | Impostazione | Azione |
    |---------|-------------|
    | Ruolo | Selezionare **proprietario**, **collaboratore**o **lettore**.
    | Assegna accesso a | Selezionare **Azure ad utente, gruppo o entità servizio**.
    | Selezionare | Cercare l'utente, il gruppo o l'entità servizio che si desidera aggiungere.

1. Selezionare **Save (Salva** ) per aggiungere l'assegnazione di ruolo.

Per altre informazioni sul controllo di accesso Azure AD, vedere [gestire l'accesso alle risorse di Azure con RBAC e il portale di Azure](../../role-based-access-control/role-assignments-portal.md)

Per informazioni dettagliate su come connettersi usando l'autenticazione Azure AD, vedere [connettersi al nodo usando l'autenticazione di AAD](configure-aad.md).

### <a name="basic-authentication"></a>Autenticazione di base

Per l'autenticazione HTTPS di base, le credenziali per nome utente e password vengono passate nell'intestazione HTTPS della richiesta all'endpoint.

È possibile visualizzare i dettagli dell'endpoint di autenticazione di base di un nodo di transazione nel portale di Azure. Passare a uno dei nodi di transazione membro del servizio Azure blockchain e selezionare **autenticazione di base** in impostazioni.

![Autenticazione di base](./media/configure-transaction-nodes/basic.png)

Il nome utente è il nome del nodo e non può essere modificato.

Per usare l'URL, sostituire \<password\> con la password impostata quando è stato eseguito il provisioning del nodo. È possibile aggiornare la password selezionando **Reimposta password**.

### <a name="access-keys"></a>Chiavi di accesso

Per l'autenticazione della chiave di accesso, la chiave di accesso è inclusa nell'URL dell'endpoint. Quando viene eseguito il provisioning del nodo della transazione, vengono generate due chiavi di accesso. Per l'autenticazione è possibile usare una chiave di accesso. Due chiavi consentono di modificare e ruotare le chiavi.

È possibile visualizzare i dettagli della chiave di accesso di un nodo di transazione e copiare gli indirizzi degli endpoint che includono le chiavi di accesso. Passare a uno dei nodi di transazione membro del servizio Azure blockchain e selezionare **chiavi di accesso** in impostazioni.

### <a name="firewall-rules"></a>Regole del firewall

Le regole del firewall consentono di limitare gli indirizzi IP che possono tentare di eseguire l'autenticazione nel nodo della transazione.  Se non è configurata alcuna regola del firewall per il nodo della transazione, non è possibile accedervi da nessuna parte.  

Per visualizzare le regole del firewall di un nodo di transazione, passare a uno dei nodi di transazione membro del servizio Azure blockchain e selezionare **regole firewall** in impostazioni.

È possibile aggiungere regole del firewall immettendo il nome di una regola, l'indirizzo IP iniziale e un indirizzo IP finale nella griglia **delle regole del firewall** .

![Regole del firewall](./media/configure-transaction-nodes/firewall-rules.png)

Per abilitare:

* **Singolo indirizzo IP:** Configurare lo stesso indirizzo IP per gli indirizzi IP iniziale e finale.
* **Intervallo di indirizzi IP:** Configurare l'intervallo di indirizzi IP iniziale e finale. Ad esempio, un intervallo che inizia a 10.221.34.0 e termina in 10.221.34.255 Abilita l'intera subnet 10.221.34.xxx.
* **Consenti tutti gli indirizzi IP:** Configurare l'indirizzo IP iniziale su 0.0.0.0 e l'indirizzo IP finale a 255.255.255.255.

## <a name="connection-strings"></a>Stringhe di connessione

La sintassi della stringa di connessione per il nodo della transazione è disponibile per l'autenticazione di base o per l'uso di chiavi di accesso Vengono fornite stringhe di connessione, incluse le chiavi di accesso su HTTPS e WebSocket.

È possibile visualizzare le stringhe di connessione di un nodo di transazione e copiare gli indirizzi degli endpoint. Passare a uno dei nodi di transazione membro del servizio blockchain di Azure e selezionare le **stringhe di connessione** nelle impostazioni.

![Stringhe di connessione](./media/configure-transaction-nodes/connection-strings.png)

## <a name="sample-code"></a>Codice di esempio

Il codice di esempio viene fornito per consentire rapidamente la connessione al nodo di transazione tramite Web3, Nethereum, Web3js e tartufo.

È possibile visualizzare il codice di connessione di esempio di un nodo di transazione e copiarlo per usarlo con gli strumenti di sviluppo più diffusi. Passare a uno dei nodi di transazione membro del servizio Azure blockchain e selezionare **codice di esempio** in impostazioni.

Scegliere la scheda Web3, Nethereum, tartufo o Web3j per visualizzare l'esempio di codice che si vuole usare.

![Codice di esempio](./media/configure-transaction-nodes/sample-code.png)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Configurare i nodi delle transazioni usando l'interfaccia della riga di comando](manage-cli.md)
