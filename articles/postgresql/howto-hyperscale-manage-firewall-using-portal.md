---
title: Gestire le regole del firewall in database di Azure per PostgreSQL-iperscalabilità (CITUS)
description: Creare e gestire le regole del firewall per database di Azure per PostgreSQL-iperscalabilità (CITUS) con il portale di Azure
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 9/12/2019
ms.openlocfilehash: 1b132eb168f3d873c8150bc33b581aa0f0f8d124
ms.sourcegitcommit: a6718e2b0251b50f1228b1e13a42bb65e7bf7ee2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71273714"
---
# <a name="manage-firewall-rules-for-azure-database-for-postgresql---hyperscale-citus"></a>Gestire le regole del firewall per database di Azure per PostgreSQL-iperscalabilità (CITUS)
Le regole del firewall a livello di server possono essere usate per gestire l'accesso a un nodo coordinatore iperscalabile (CITUS) da un indirizzo IP specificato o da un intervallo di indirizzi IP.

## <a name="prerequisites"></a>Prerequisiti
Per proseguire con questa guida, si richiedono:
- Un gruppo di server [Crea un gruppo di server di database di Azure per PostgreSQL – iperscala (CITUS)](quickstart-create-hyperscale-portal.md).

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Creare una regola del firewall a livello di server nel portale di Azure

> [!NOTE]
> Queste impostazioni sono accessibili anche durante la creazione di un gruppo di server di database di Azure per PostgreSQL-overscale (CITUS). Nella scheda **rete** fare clic su **endpoint pubblico**.
> ![Portale di Azure-scheda rete](./media/howto-hyperscale-manage-firewall-using-portal/0-create-public-access.png)

1. Nella pagina gruppo di server PostgreSQL, sotto l'intestazione sicurezza, fare clic su **rete** per aprire le regole del firewall.

   ![portale di Azure fare clic su rete](./media/howto-hyperscale-manage-firewall-using-portal/1-connection-security.png)

2. Fare clic su **Aggiungi IP client**sulla barra degli strumenti (opzione A sotto) o nel collegamento (opzione B). In entrambi i casi crea automaticamente una regola del firewall con l'indirizzo IP pubblico del computer, come percepito dal sistema Azure.

   ![Portale di Azure-fare clic su Aggiungi IP client](./media/howto-hyperscale-manage-firewall-using-portal/2-add-my-ip.png)

In alternativa, facendo clic su **+ Aggiungi 0.0.0.0-255.255.255.255** (a destra dell'opzione B) non è possibile solo l'IP, ma l'intera Internet per accedere alla porta 5432 del nodo coordinatore. In questa situazione, i client devono ancora accedere con il nome utente e la password corretti per usare il cluster. Tuttavia, si consiglia di consentire l'accesso a livello globale solo per brevi periodi di tempo e solo per i database non di produzione.

3. Verificare l'indirizzo IP prima di salvare la configurazione. In alcuni casi, l'indirizzo IP individuato dal portale di Azure è diverso da quello usato per l'accesso a Internet e ai server Azure, quindi potrebbe essere necessario modificare l'indirizzo IP iniziale e l'indirizzo IP finale per fare in modo che la regola funzioni come previsto.
   Usare un motore di ricerca o un altro strumento online per controllare l'indirizzo IP. Ad esempio, cercare "qual è l'indirizzo IP".

   ![Ricerca Bing di What is my IP](./media/howto-hyperscale-manage-firewall-using-portal/3-what-is-my-ip.png)

4. Aggiungere altri intervalli di indirizzi. Nelle regole del firewall è possibile specificare un singolo indirizzo IP o un intervallo di indirizzi. Per limitare la regola a un solo indirizzo IP, digitare lo stesso indirizzo nei campi Indirizzo IP iniziale e Indirizzo IP finale. L'apertura del firewall consente agli amministratori, agli utenti e alle applicazioni di accedere al nodo coordinatore sulla porta 5432.

5. Fare clic su **Salva** sulla barra degli strumenti per salvare questa regola del firewall a livello di server. Attendere la conferma che l'aggiornamento delle regole del firewall abbia avuto esito positivo.

## <a name="connecting-from-azure"></a>Connessione da Azure

Esiste un modo semplice per concedere l'accesso al database con iperscalabilità alle applicazioni ospitate in Azure, ad esempio un'applicazione app Web di Azure o quelle in esecuzione in una macchina virtuale di Azure. È sufficiente impostare l'opzione **Consenti ai servizi e alle risorse di Azure di accedere a questo gruppo di server** su **Sì** nel portale dal riquadro **rete** e premere **Salva**.

> [!IMPORTANT]
> Questa opzione permette di configurare il firewall in maniera tale da consentire tutte le connessioni da Azure, incluse le connessioni dalle sottoscrizioni di altri clienti. Quando si seleziona questa opzione, assicurarsi che l'account di accesso e le autorizzazioni utente limitino l'accesso ai soli utenti autorizzati.

## <a name="manage-existing-server-level-firewall-rules-through-the-azure-portal"></a>Gestione delle regole del firewall a livello di server esistenti tramite il portale di Azure
Ripetere i passaggi per gestire le regole del firewall.
* Per aggiungere il computer corrente, fare clic sul pulsante per + **Aggiungi IP client**. È consigliabile fare clic su **Salva** per salvare le modifiche.
* Per aggiungere ulteriori indirizzi IP, digitare nome della regola, indirizzo IP iniziale e indirizzo IP finale. È consigliabile fare clic su **Salva** per salvare le modifiche.
* Per modificare una regola esistente, fare clic su uno dei campi nella regola e modificare. È consigliabile fare clic su **Salva** per salvare le modifiche.
* Per eliminare una regola esistente, fare clic sui puntini di sospensione […] e fare clic su **Elimina** per rimuovere la regola. È consigliabile fare clic su **Salva** per salvare le modifiche.

## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni sul [concetto di regole del firewall](concepts-hyperscale-firewall-rules.md), inclusa la risoluzione dei problemi di connessione.
