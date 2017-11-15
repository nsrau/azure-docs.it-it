---
title: Creare e gestire regole del firewall per Database di Azure per PostgreSQL usando il portale di Azure | Microsoft Docs
description: Creare e gestire regole del firewall per Database di Azure per PostgreSQL usando il portale di Azure
services: postgresql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 11/03/2017
ms.openlocfilehash: 96e917d1ea147e3b53b00002675ed16facb69255
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2017
---
# <a name="create-and-manage-azure-database-for-postgresql-firewall-rules-using-the-azure-portal"></a>Creare e gestire regole del firewall per Database di Azure per PostgreSQL usando il portale di Azure
Le regole del firewall a livello di server consentono agli amministratori di accedere a un server di Database di Azure per PostgreSQL da un indirizzo IP specificato o da un intervallo di indirizzi IP. 

## <a name="prerequisites"></a>Prerequisiti
Per proseguire con questa guida, si richiedono:
- Un server [Creare un database di Azure per PostgreSQL](quickstart-create-server-database-portal.md)

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Creare una regola del firewall a livello di server nel portale di Azure
1. Nell'intestazione Impostazioni della pagina del server PostgreSQL fare clic su **Sicurezza connessione** per aprire la pagina Sicurezza connessione del Database di Azure per PostgreSQL.

  ![Portale di Azure: fare clic su Sicurezza connessione](./media/howto-manage-firewall-using-portal/1-connection-security.png)

2. Selezionare **Aggiungi indirizzo IP corrente** sulla barra degli strumenti. Questa azione crea automaticamente una regola firewall con l'indirizzo IP pubblico del computer rilevato dal sistema di Azure.

  ![Portale di Azure: fare clic su Aggiungi indirizzo IP corrente](./media/howto-manage-firewall-using-portal/2-add-my-ip.png)

3. Verificare l'indirizzo IP prima di salvare la configurazione. In alcuni casi, l'indirizzo IP individuato dal portale di Azure è diverso da quello usato per l'accesso a Internet e ai server Azure, quindi potrebbe essere necessario modificare l'indirizzo IP iniziale e l'indirizzo IP finale per fare in modo che la regola funzioni come previsto.
Usare un motore di ricerca o un altro strumento online per controllare l'indirizzo IP (ad esempio, la ricerca Bing di "what is my IP").

  ![Ricerca Bing di What is my IP](./media/howto-manage-firewall-using-portal/3-what-is-my-ip.png)

4. Aggiungere altri intervalli di indirizzi. Nelle regole firewall per Database di Azure per PostgreSQL è possibile specificare un solo indirizzo IP o un intervallo di indirizzi. Per limitare la regola a un solo indirizzo IP, digitare lo stesso indirizzo nei campi Indirizzo IP iniziale e Indirizzo IP finale. L'apertura del firewall consente agli amministratori, agli utenti e alle applicazioni di accedere a qualsiasi database nel server PostgreSQL per cui hanno credenziali valide.

  ![Portale di Azure: regole del firewall ](./media/howto-manage-firewall-using-portal/4-specify-addresses.png)

5. Fare clic su **Salva** sulla barra degli strumenti per salvare questa regola del firewall a livello di server. Attendere la conferma che l'aggiornamento delle regole del firewall abbia avuto esito positivo.

  ![Portale di Azure: fare clic su Salva](./media/howto-manage-firewall-using-portal/5-save-firewall-rule.png)


## <a name="manage-existing-server-level-firewall-rules-through-the-azure-portal"></a>Gestione delle regole del firewall a livello di server esistenti tramite il portale di Azure
Ripetere i passaggi per gestire le regole del firewall.
* Per aggiungere il computer corrente, fare clic sul pulsante + **Aggiungi indirizzo IP corrente**. È consigliabile fare clic su **Salva** per salvare le modifiche.
* Per aggiungere ulteriori indirizzi IP, digitare nome della regola, indirizzo IP iniziale e indirizzo IP finale. È consigliabile fare clic su **Salva** per salvare le modifiche.
* Per modificare una regola esistente, fare clic su uno dei campi nella regola e modificare. È consigliabile fare clic su **Salva** per salvare le modifiche.
* Per eliminare una regola esistente, fare clic sui puntini di sospensione […] e fare clic su **Elimina** per rimuovere la regola. È consigliabile fare clic su **Salva** per salvare le modifiche.

## <a name="next-steps"></a>Passaggi successivi
- Analogamente, è possibile generare uno script per [creare e gestire le regole firewall per Database di Azure per PostgreSQL usando l'interfaccia della riga di comando di Azure](howto-manage-firewall-using-cli.md).
- Per informazioni sulla connessione a un'istanza di Database di Azure per il server PostgreSQL, vedere [Connection libraries for Azure Database for PostgreSQL](concepts-connection-libraries.md) (Raccolte di connessioni per Database di Azure per PostgreSQL).
