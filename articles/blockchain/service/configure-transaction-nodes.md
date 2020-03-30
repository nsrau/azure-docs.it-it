---
title: Configurare i nodi delle transazioni del servizio Blockchain di AzureConfigure Azure Blockchain Service transaction nodes
description: Come configurare i nodi di transazione del servizio Blockchain di AzureHow to configure Azure Blockchain Service transaction nodes
ms.date: 11/20/2019
ms.topic: article
ms.reviewer: janders
ms.openlocfilehash: 4a9a4f660dd171e65b600ec4cd66714ca476b091
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252259"
---
# <a name="configure-azure-blockchain-service-transaction-nodes"></a>Configurare i nodi delle transazioni del servizio Blockchain di AzureConfigure Azure Blockchain Service transaction nodes

I nodi di transazione vengono usati per inviare transazioni blockchain al servizio Blockchain di Azure tramite un endpoint pubblico. Il nodo della transazione predefinita contiene la chiave privata dell'account Ethereum registrato nella blockchain e come tale non può essere eliminato.

Per visualizzare i dettagli del nodo di transazione predefinito:

1. Accedere al [portale](https://portal.azure.com)di Azure .
1. Passare al membro del servizio Azure Blockchain. Selezionare **Nodi transazione**.

    ![Selezionare il nodo della transazione predefinito](./media/configure-transaction-nodes/nodes.png)

    I dettagli della panoramica includono gli indirizzi degli endpoint pubblici e la chiave pubblica.

## <a name="create-transaction-node"></a>Creare il nodo di transazioni

È possibile aggiungere fino a nove nodi di transazione aggiuntivi al membro blockchain, per un totale di 10 nodi di transazione. Aggiungendo nodi di transazione, è possibile aumentare la scalabilità o distribuire il carico. Ad esempio, è possibile avere un endpoint del nodo di transazione per applicazioni client diverse.

Per aggiungere un nodo di transazione:

1. Nel portale di Azure passare al membro del servizio Blockchain di Azure e selezionare **Nodi di transazione > Aggiungi**.
1. Completare le impostazioni per il nuovo nodo di transazione.

    ![Aggiungi nodo transazione](./media/configure-transaction-nodes/add-node.png)

    | Impostazione | Descrizione |
    |---------|-------------|
    | Nome | Nome del nodo di transazioni. Il nome viene usato per creare l'indirizzo DNS per l'endpoint del nodo di transazioni. Ad esempio: `newnode-myblockchainmember.blockchain.azure.com`. Il nome del nodo non può essere modificato una volta creato. |
    | Password | Impostare una password complessa. Usare la password per accedere all'endpoint del nodo di transazione con l'autenticazione di base.

1. Selezionare **Crea**.

    Il provisioning del nuovo nodo di transazioni richiede circa 10 minuti. I nodi di transazione aggiuntivi comportano costi. Per altre informazioni sui costi, vedere Prezzi di Azure.For more information on costs, see [Azure pricing](https://aka.ms/ABSPricing).

## <a name="endpoints"></a>Endpoint

I nodi di transazione hanno un nome DNS univoco e endpoint pubblici.

Per visualizzare i dettagli dell'endpoint di un nodo di transazione:To view a transaction node's endpoint details:

1. Nel portale di Azure passare a uno dei nodi di transazione membri del servizio Blockchain di Azure e selezionare **Panoramica.**

    ![Endpoint](./media/configure-transaction-nodes/endpoints.png)

Gli endpoint dei nodi delle transazioni sono sicuri e richiedono l'autenticazione. È possibile connettersi a un endpoint di transazione usando l'autenticazione di Azure AD, l'autenticazione di base HTTPS e una chiave di accesso tramite HTTPS o Websocket tramite SSL.

### <a name="azure-active-directory-access-control"></a>Controllo degli accessi di Azure Active DirectoryAzure Active Directory access control

Gli endpoint del nodo delle transazioni del servizio Blockchain di Azure supportano l'autenticazione di Azure Active Directory (Azure AD). È possibile concedere l'accesso di utente, gruppo ed entità servizio di Azure AD all'endpoint.

Per concedere il controllo di accesso di Azure AD all'endpoint:To grant Azure AD access control to your endpoint:

1. Nel portale di Azure passare al membro del servizio Blockchain di Azure e selezionare **Nodi di transazione > controllo di accesso (IAM) > Aggiungi >'assegnazione**di ruolo .
1. Creare una nuova assegnazione di ruolo per un utente, un gruppo o un'entità servizio (ruoli applicazione).

    ![Aggiungi ruolo IAM](./media/configure-transaction-nodes/add-role.png)

    | Impostazione | Azione |
    |---------|-------------|
    | Ruolo | Selezionare **Proprietario**, **Collaboratore**o **Lettore**.
    | Assegna accesso a | Selezionare **Utente, gruppo o entità servizio**di Azure AD.
    | Select | Cercare l'utente, il gruppo o l'entità servizio che si desidera aggiungere.

1. Selezionare **Salva** per aggiungere l'assegnazione di ruolo.

Per altre informazioni sul controllo di accesso di Azure AD, vedere [Gestire l'accesso alle risorse di Azure usando il controllo degli accessi in base al ruolo e il portale](../../role-based-access-control/role-assignments-portal.md) di AzureFor more information on Azure AD access control, see Manage access to Azure resources using RBAC and the Azure portal

Per informazioni dettagliate su come connettersi usando l'autenticazione di Azure AD, vedere [Connettersi al nodo usando l'autenticazione AAD.](configure-aad.md)

### <a name="basic-authentication"></a>Autenticazione di base

Per l'autenticazione di base HTTPS, le credenziali di nome utente e password vengono passate nell'intestazione HTTPS della richiesta all'endpoint.

È possibile visualizzare i dettagli dell'endpoint di autenticazione di base di un nodo di transazione nel portale di Azure.You can view a transaction node's basic authentication endpoint details in the Azure portal. Passare a uno dei nodi di transazione membri del servizio Blockchain di Azure e selezionare **Autenticazione di base** nelle impostazioni.

![Autenticazione di base](./media/configure-transaction-nodes/basic.png)

Il nome utente è il nome del nodo e non può essere modificato.

Per utilizzare l'URL, sostituire \<password\> con la password impostata al momento del provisioning del nodo. È possibile aggiornare la password selezionando **Reimposta password**.

### <a name="access-keys"></a>Chiavi di accesso

Per l'autenticazione con chiave di accesso, la chiave di accesso è inclusa nell'URL dell'endpoint. Quando viene eseguito il provisioning del nodo della transazione, vengono generate due chiavi di accesso. Per l'autenticazione è possibile utilizzare entrambe le chiavi di accesso. Due tasti consentono di modificare e ruotare i tasti.

È possibile visualizzare i dettagli delle chiavi di accesso di un nodo di transazione e copiare gli indirizzi degli endpoint che includono le chiavi di accesso. Passare a uno dei nodi di transazione membri del servizio Blockchain di Azure e selezionare **Chiavi di accesso** nelle impostazioni.

### <a name="firewall-rules"></a>Regole del firewall

Le regole del firewall consentono di limitare gli indirizzi IP che possono tentare di eseguire l'autenticazione nel nodo della transazione.  Se non sono configurate regole del firewall per il nodo della transazione, non è possibile accedervi da alcuna parte.  

Per visualizzare le regole del firewall di un nodo di transazione, passare a uno dei nodi delle transazioni membro del servizio Blockchain di Azure e selezionare **Regole firewall** nelle impostazioni.

È possibile aggiungere regole firewall immettendo un nome di regola, un indirizzo IP iniziale e un indirizzo IP finale nella griglia **Regole firewall.**

![Regole del firewall](./media/configure-transaction-nodes/firewall-rules.png)

Per abilitare:

* **Indirizzo IP singolo:** Configurare lo stesso indirizzo IP per gli indirizzi IP iniziale e finale.
* **Intervallo di indirizzi IP:** Configurare l'intervallo di indirizzi IP iniziale e finale. Ad esempio, un intervallo a partire da 10.221.34.0 e che termina a 10.221.34.255 abiliterebbe l'intera subnet 10.221.34.xxx.
* **Consenti tutti gli indirizzi IP:** Configurare l'indirizzo IP iniziale su 0.0.0.0 e l'indirizzo IP finale su 255.255.255.255.

## <a name="connection-strings"></a>Stringhe di connessione

La sintassi della stringa di connessione per il nodo della transazione viene fornita per l'autenticazione di base o tramite chiavi di scelta. Vengono fornite stringhe di connessione che includono i tasti di scelta tramite HTTPS e WebSockets.Connection strings including access keys over HTTPS and WebSockets are provided.

È possibile visualizzare le stringhe di connessione di un nodo di transazione e copiare gli indirizzi degli endpoint. Passare a uno dei nodi di transazione membri del servizio Blockchain di Azure e selezionare **Stringhe di connessione** nelle impostazioni.

![Stringhe di connessione](./media/configure-transaction-nodes/connection-strings.png)

## <a name="sample-code"></a>Codice di esempio

Viene fornito codice di esempio per abilitare rapidamente la connessione al nodo della transazione tramite Web3, Nethereum, Web3js e Truffle.

È possibile visualizzare il codice di connessione di esempio di un nodo di transazione e copiarlo per utilizzarlo con gli strumenti di sviluppo più diffusi. Passare a uno dei nodi di transazione membro del servizio Blockchain di Azure e selezionare **Codice di esempio** nelle impostazioni.

Scegliere la scheda Web3, Nethereum, Tartufo o Web3j per visualizzare l'esempio di codice che si desidera utilizzare.

![Codice di esempio](./media/configure-transaction-nodes/sample-code.png)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Configurare i nodi delle transazioni usando l'interfaccia della riga di comando di AzureConfigure transaction](manage-cli.md)
