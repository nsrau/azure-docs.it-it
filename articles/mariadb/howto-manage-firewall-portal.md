---
title: Gestire le regole del firewall-portale di Azure-database di Azure per MariaDB
description: Creare e gestire le regole del firewall di Database di Azure per MariaDB con il portale di Azure
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 70e6611cc3006424bcd1bc3a0f0838ad40c09634
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74766022"
---
# <a name="create-and-manage-azure-database-for-mariadb-firewall-rules-by-using-the-azure-portal"></a>Creare e gestire le regole del firewall di Database di Azure per MariaDB con il portale di Azure
Le regole del firewall a livello di server possono essere usate per gestire l'accesso a un database di Azure per il server MariaDB da un indirizzo IP specificato o da un intervallo di indirizzi IP.

Le regole della rete virtuale (VNet) possono essere usate anche per proteggere l'accesso al server. Altre informazioni sulla [creazione e la gestione di endpoint e regole del servizio rete virtuale usando il portale di Azure](howto-manage-vnet-portal.md).

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Creare una regola del firewall a livello di server nel portale di Azure

1. Nella pagina del server MariaDB, in Impostazioni, fare clic su **Sicurezza connessione** per aprire la pagina Sicurezza connessione per il Database di Azure per MariaDB.

   ![Portale di Azure: fare clic su Sicurezza connessione](./media/howto-manage-firewall-portal/1-connection-security.png)

2. Fare clic su **Aggiungi indirizzo IP corrente** sulla barra degli strumenti. Viene creata automaticamente una regola del firewall con l'indirizzo IP pubblico del computer rilevato dal sistema Azure.

   ![Portale di Azure: fare clic su Aggiungi indirizzo IP corrente](./media/howto-manage-firewall-portal/2-add-my-ip.png)

3. Verificare l'indirizzo IP prima di salvare la configurazione. In alcuni casi, l'indirizzo IP individuato dal portale di Azure è diverso da quello usato per l'accesso a Internet e ai server Azure, quindi potrebbe essere necessario modificare l'indirizzo IP iniziale e l'indirizzo IP finale per fare in modo che la regola funzioni come previsto.

   Usare un motore di ricerca o un altro strumento online per controllare l'indirizzo IP. Ad esempio, cercare qual è l'indirizzo IP in uso.

4. Aggiungere altri intervalli di indirizzi. Nelle regole del firewall per il Database di Azure per MariaDB è possibile specificare un singolo indirizzo IP o un intervallo di indirizzi. Per limitare la regola a un solo indirizzo IP, digitare lo stesso indirizzo nei campi Indirizzo IP iniziale e Indirizzo IP finale. L'apertura del firewall consente agli amministratori, agli utenti e all'applicazione di accedere a qualsiasi database nel server MariaDB per cui dispongono di credenziali valide.

   ![Portale di Azure: regole del firewall](./media/howto-manage-firewall-portal/4-specify-addresses.png)

5. Fare clic su **Salva** sulla barra degli strumenti per salvare questa regola del firewall a livello di server. Attendere la conferma che l'aggiornamento delle regole del firewall abbia avuto esito positivo.

   ![Portale di Azure: fare clic su Salva](./media/howto-manage-firewall-portal/5-save-firewall-rule.png)

## <a name="connecting-from-azure"></a>Connessione da Azure
Per consentire alle applicazioni da Azure di stabilire la connessione al server Database di Azure per MariaDB, è necessario abilitare le connessioni da Azure. Ad esempio, per ospitare un'applicazione delle app Web di Azure o un'applicazione che viene eseguita in una macchina virtuale di Azure oppure per connettersi da un gateway di gestione dati di Azure Data Factory. Le risorse non devono essere necessariamente nella stessa rete virtuale (VNet) o nello stesso gruppo di risorse perché la regola del firewall consenta tali connessioni. Quando si tenta di connettersi al server di database da un'applicazione di Azure, il firewall verifica che le connessioni da Azure siano consentite. Sono disponibili alcuni metodi per consentire questi tipi di connessioni. Un'impostazione del firewall con indirizzo iniziale e finale uguale a 0.0.0.0 indica che queste connessioni sono consentite. In alternativa, è possibile impostare l'opzione **Consenti l'accesso a Servizi di Azure** su **Sì** nel portale dal riquadro **Sicurezza connessione** e fare clic su **Salva**. Se il tentativo di connessione non è consentito, la richiesta non raggiunge il server Database di Azure per MariaDB.

> [!IMPORTANT]
> Questa opzione permette di configurare il firewall in maniera tale da consentire tutte le connessioni da Azure, incluse le connessioni dalle sottoscrizioni di altri clienti. Quando si seleziona questa opzione, assicurarsi che l'account di accesso e le autorizzazioni utente limitino l'accesso ai soli utenti autorizzati.
> 

## <a name="manage-existing-firewall-rules-in-the-azure-portal"></a>Gestire regole del firewall esistenti nel portale di Azure
Ripetere i passaggi per gestire le regole del firewall.
* Per aggiungere il computer corrente, fare clic su **+ Aggiungi indirizzo IP corrente**. È consigliabile fare clic su **Salva** per salvare le modifiche.
* Per aggiungere altri indirizzi IP, digitare **NOME REGOLA**, **INDIRIZZO IP INIZIALE** e **INDIRIZZO IP FINALE**. È consigliabile fare clic su **Salva** per salvare le modifiche.
* Per modificare una regola esistente, fare clic su uno dei campi nella regola e inserire la modifica. È consigliabile fare clic su **Salva** per salvare le modifiche.
* Per eliminare una regola esistente, fare clic sui puntini di sospensione (…) e quindi su **Elimina**. È consigliabile fare clic su **Salva** per salvare le modifiche.

## <a name="next-steps"></a>Passaggi successivi
 - Analogamente, è possibile creare uno script per [creare e gestire regole del firewall di database di Azure per MariaDB usando l'interfaccia della riga](howto-manage-firewall-cli.md)di comando
 - Proteggere ulteriormente l'accesso al server [creando e gestendo gli endpoint e le regole del servizio rete virtuale usando il portale di Azure](howto-manage-vnet-portal.md).