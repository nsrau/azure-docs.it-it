---
title: Gestire le regole del firewall - Hyperscale (Citus) - Database di Azure per PostgreSQL
description: Creare e gestire le regole del firewall per Database di Azure per PostgreSQL - Hyperscale (Citus) usando il portale di Azure
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 9/11/2020
ms.openlocfilehash: d369614357bd62dc13073f650fbe5ce358d6dc6e
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90884329"
---
# <a name="manage-firewall-rules-for-azure-database-for-postgresql---hyperscale-citus"></a>Gestire le regole del firewall per Database di Azure per PostgreSQL - Hyperscale (Citus)
Le regole del firewall a livello di server possono essere usate per gestire l'accesso a un nodo coordinatore Hyperscale (Citus) da un indirizzo IP specificato o da un intervallo di indirizzi IP.

## <a name="prerequisites"></a>Prerequisiti
Per proseguire con questa guida, si richiedono:
- Un gruppo di server [Creare un Database di Azure per PostgreSQL - Hyperscale (Citus)](quickstart-create-hyperscale-portal.md).

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Creare una regola del firewall a livello di server nel portale di Azure

> [!NOTE]
> Queste impostazioni sono accessibili anche durante la creazione di un gruppo di server di Database di Azure per PostgreSQL - Hyperscale (Citus). Nella scheda **Rete** fare clic su **Endpoint pubblico**.

> :::image type="content" source="./media/howto-hyperscale-manage-firewall-using-portal/0-create-public-access.png" alt-text="Portale di Azure - Scheda Rete":::

1. Nella pagina del gruppo di server PostgreSQL, nell'intestazione Sicurezza, fare clic su **Rete** per aprire le regole del firewall.

   :::image type="content" source="./media/howto-hyperscale-manage-firewall-using-portal/1-connection-security.png" alt-text="Portale di Azure - Selezione di Rete":::

2. Fare clic su **Aggiungi indirizzo IP client corrente** per creare una regola del firewall con l'indirizzo IP pubblico del computer, come percepito dal sistema Azure.

   :::image type="content" source="./media/howto-hyperscale-manage-firewall-using-portal/2-add-my-ip.png" alt-text="Portale di Azure - Selezione di Aggiungi IP client":::

In alternativa, facendo clic su **+ Aggiungi 0.0.0.0 - 255.255.255.255** (a destra dell'opzione B) si consente non solo all'IP, ma all'intera rete Internet di accedere alla porta 5432 del nodo coordinatore. In questa situazione, i client devono ancora accedere con il nome utente e la password corretti per usare il cluster. Tuttavia, si consiglia di consentire l'accesso a livello globale solo per brevi periodi di tempo e solo per i database non di produzione.

3. Verificare l'indirizzo IP prima di salvare la configurazione. In alcuni casi, l'indirizzo IP individuato dal portale di Azure è diverso da quello usato per l'accesso a Internet e ai server Azure, quindi potrebbe essere necessario modificare l'indirizzo IP iniziale e l'indirizzo IP finale per fare in modo che la regola funzioni come previsto.
   Usare un motore di ricerca o un altro strumento online per controllare l'indirizzo IP. Ad esempio, cercare "qual è l'indirizzo IP".

   :::image type="content" source="./media/howto-hyperscale-manage-firewall-using-portal/3-what-is-my-ip.png" alt-text="Ricerca Bing di What is my IP":::

4. Aggiungere altri intervalli di indirizzi. Nelle regole del firewall è possibile specificare un singolo indirizzo IP o un intervallo di indirizzi. Per limitare la regola a un solo indirizzo IP, digitare lo stesso indirizzo nei campi Indirizzo IP iniziale e Indirizzo IP finale. L'apertura del firewall consente agli amministratori, agli utenti e alle applicazioni di accedere al nodo coordinatore sulla porta 5432.

5. Fare clic su **Salva** sulla barra degli strumenti per salvare questa regola del firewall a livello di server. Attendere la conferma che l'aggiornamento delle regole del firewall abbia avuto esito positivo.

## <a name="connecting-from-azure"></a>Connessione da Azure

Esiste un modo semplice per concedere al database Hyperscale l'accesso alle applicazioni ospitate in Azure, ad esempio un'applicazione app Web di Azure o quelle in esecuzione in una macchina virtuale di Azure. È sufficiente impostare l'opzione **Consenti alle risorse e ai servizi di Azure di accedere a questo gruppo di server** su **Sì** nel portale dal riquadro **Rete** e premere **Salva**.

> [!IMPORTANT]
> Questa opzione permette di configurare il firewall in maniera tale da consentire tutte le connessioni da Azure, incluse le connessioni dalle sottoscrizioni di altri clienti. Quando si seleziona questa opzione, assicurarsi che l'account di accesso e le autorizzazioni utente limitino l'accesso ai soli utenti autorizzati.

## <a name="manage-existing-server-level-firewall-rules-through-the-azure-portal"></a>Gestione delle regole del firewall a livello di server esistenti tramite il portale di Azure
Ripetere i passaggi per gestire le regole del firewall.
* Per aggiungere il computer corrente, fare clic sul pulsante per **aggiungere l'indirizzo IP del client corrente**. È consigliabile fare clic su **Salva** per salvare le modifiche.
* Per aggiungere ulteriori indirizzi IP, digitare nome della regola, indirizzo IP iniziale e indirizzo IP finale. È consigliabile fare clic su **Salva** per salvare le modifiche.
* Per modificare una regola esistente, fare clic su uno dei campi nella regola e modificare. È consigliabile fare clic su **Salva** per salvare le modifiche.
* Per eliminare una regola esistente, fare clic sui puntini di sospensione […] e fare clic su **Elimina** per rimuovere la regola. È consigliabile fare clic su **Salva** per salvare le modifiche.

## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni su [Concetto di regole del firewall](concepts-hyperscale-firewall-rules.md), inclusa la risoluzione dei problemi di connessione.
