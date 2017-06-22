---
title: Creare e gestire le regole del firewall di Database di Azure per MySQL con il portale di Azure | Microsoft Docs
description: Creare e gestire le regole del firewall di Database di Azure per MySQL con il portale di Azure
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: mysql-database
ms.tgt_pltfrm: portal
ms.topic: article
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 744f3503d2abc618095351e1b018cab22adcf091
ms.contentlocale: it-it
ms.lasthandoff: 05/10/2017

---
# <a name="create-and-manage-azure-database-for-mysql-firewall-rules-using-the-azure-portal"></a>Creare e gestire le regole del firewall di Database di Azure per MySQL con il portale di Azure
Le regole del firewall a livello di server consentono agli amministratori di accedere a un server di Database di Azure per MySQL da un indirizzo IP specificato o da un intervallo di indirizzi IP. 

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Creare una regola del firewall a livello di server nel portale di Azure

1. Nel pannello del server MySQL fare clic su **Sicurezza connessione** sotto l'intestazione Impostazioni per aprire il pannello Sicurezza connessione per Database di Azure per MySQL.
![Portale di Azure: fare clic su Sicurezza connessione](./media/howto-manage-firewall-using-portal/1-connection-security.png)

2. Fare clic su **Aggiungi indirizzo IP corrente** sulla barra degli strumenti. Verrà creata automaticamente una regola con l'indirizzo IP del computer rilevato dal sistema Azure.
![Portale di Azure: fare clic su Aggiungi indirizzo IP corrente](./media/howto-manage-firewall-using-portal/2-add-my-ip.png)

3. Verificare l'indirizzo IP prima di salvare la configurazione. In alcuni casi, l'indirizzo IP individuato dal portale di Azure è diverso da quello usato per l'accesso a Internet e ai server Azure, quindi potrebbe essere necessario modificare l'indirizzo IP iniziale e l'indirizzo IP finale per fare in modo che la regola funzioni come previsto.

Usare un motore di ricerca o un altro strumento online per controllare il proprio indirizzo IP (ad esempio, cercare "what is my IP").
![Ricerca del proprio indirizzo IP in Bing](./media/howto-manage-firewall-using-portal/3-what-is-my-ip.png)

4. Fare clic su **Salva** sulla barra degli strumenti per salvare questa regola del firewall a livello di server. Attendere la conferma che l'aggiornamento delle regole del firewall abbia avuto esito positivo.
![Portale di Azure: fare clic su Salva](./media/howto-manage-firewall-using-portal/4-save-firewall-rule.png)

> [!NOTE]
> Nelle regole per il firewall di Database di Azure per MySQL è possibile specificare un singolo indirizzo IP o un intervallo di indirizzi. Per limitare la regola a un solo indirizzo IP, digitare lo stesso indirizzo nei campi Indirizzo IP iniziale e Indirizzo IP finale. L'apertura del firewall consente agli amministratori e agli utenti di accedere a qualsiasi database nel server MySQL per cui hanno credenziali valide.
![Portale di Azure: regole del firewall](./media/howto-manage-firewall-using-portal/5-specify-addresses.png)

## <a name="manage-existing-server-level-firewall-rules-through-the-azure-portal"></a>Gestione delle regole del firewall a livello di server esistenti tramite il portale di Azure
Ripetere i passaggi per gestire le regole del firewall.
* Per aggiungere il computer corrente, fare clic su **+ Aggiungi indirizzo IP corrente**.
* Per aggiungere altri indirizzi IP, digitare **NOME REGOLA**, **INDIRIZZO IP INIZIALE** e **INDIRIZZO IP FINALE**.
* Per modificare una regola esistente, fare clic su uno dei campi nella regola e modificare.
* Per eliminare una regola esistente, fare clic sui puntini di sospensione (…) e quindi su **Elimina**.
* È consigliabile fare clic su **Salva** per salvare le modifiche.

## <a name="next-steps"></a>Passaggi successivi
- Per informazioni sulla connessione a un server di Database di Azure per MySQL, vedere [Connection libraries for Azure Database for MySQL](./concepts-connection-libraries.md) (Raccolte di connessioni per Database di Azure per MySQL)
