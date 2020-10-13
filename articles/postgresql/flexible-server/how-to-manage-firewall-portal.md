---
title: Gestire le regole del firewall-portale di Azure-database di Azure per PostgreSQL-server flessibile
description: Creare e gestire le regole del firewall per database di Azure per PostgreSQL-server flessibile usando il portale di Azure
author: ambhatna
ms.author: ambhatna
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 8c107b88f964d8e657d6833fc3a2e8425d9053ce
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90936919"
---
# <a name="create-and-manage-firewall-rules-for-azure-database-for-postgresql---flexible-server-using-the-azure-portal"></a>Creare e gestire le regole del firewall per database di Azure per PostgreSQL-server flessibile usando il portale di Azure

> [!IMPORTANT]
> Database di Azure per PostgreSQL: il server flessibile è attualmente disponibile in anteprima pubblica.

Database di Azure per PostgreSQL: il server flessibile supporta due tipi di metodi di connettività di rete che si escludono a vicenda per la connessione al server flessibile. Le due opzioni sono:

* Accesso pubblico (indirizzi IP consentiti)
* Accesso privato (integrazione rete virtuale)

In questo articolo verrà illustrata la creazione di un server PostgreSQL con **accesso pubblico (indirizzi IP consentiti)** utilizzando portale di Azure e verrà fornita una panoramica della gestione delle regole del firewall dopo la creazione di un server flessibile. Con l' *accesso pubblico (indirizzi IP consentiti)*, le connessioni al server PostgreSQL sono limitate solo agli indirizzi IP consentiti. Gli indirizzi IP client devono essere consentiti nelle regole del firewall. Per altre informazioni, vedere [accesso pubblico (indirizzi IP consentiti)](./concepts-networking.md#public-access-allowed-ip-addresses). Le regole del firewall possono essere definite al momento della creazione del server (scelta consigliata), ma possono essere aggiunte anche in seguito. In questo articolo verrà fornita una panoramica su come creare e gestire le regole del firewall tramite l'accesso pubblico (indirizzi IP consentiti).

## <a name="create-a-firewall-rule-when-creating-a-server"></a>Creare una regola del firewall quando si crea un server

1. Nell'angolo in alto a sinistra del portale selezionare **Crea una risorsa** (+).
2. Selezionare **Database** > **Database di Azure per PostgreSQL**. È anche possibile immettere **PostgreSQL** nella casella di ricerca per trovare il servizio.
3. Selezionare **Flexible server** (Server flessibile) come opzione di distribuzione.
4. Compilare il modulo **nozioni di base** .
5. Passare alla scheda **rete** per configurare il modo in cui si desidera connettersi al server.
6. Nel **metodo di connettività**selezionare *accesso pubblico (indirizzi IP consentiti)*. Per creare le **regole del firewall**, specificare il nome della regola del firewall e un singolo indirizzo IP o un intervallo di indirizzi. Se si vuole limitare la regola a un singolo indirizzo IP, digitare lo stesso indirizzo nel campo per indirizzo IP iniziale e indirizzo IP finale. L'apertura del firewall consente agli amministratori, agli utenti e alle applicazioni di accedere a qualsiasi database nel server PostgreSQL per cui hanno credenziali valide.
   > [!Note]
   > Database di Azure per PostgreSQL: il server flessibile crea un firewall a livello di server. che impedisce alle applicazioni e agli strumenti esterni di connettersi al server e ai database nel server, a meno che non si crei una regola per aprire il firewall per indirizzi IP specifici.
7. Selezionare **Rivedi e crea** per rivedere la configurazione del server flessibile.
8.  Selezionare **Crea** per effettuare il provisioning del server. Il provisioning può richiedere alcuni minuti.

## <a name="create-a-firewall-rule-after-server-is-created"></a>Creare una regola del firewall dopo la creazione del server

1. Nella [portale di Azure](https://portal.azure.com/)selezionare il database di Azure per PostgreSQL-server flessibile in cui si desidera aggiungere le regole del firewall.
2. Nella pagina server flessibile, in **Impostazioni** intestazione, fare clic su **rete** per aprire la pagina rete per server flessibile.

   <!--![Azure portal - click Connection Security](./media/howto-manage-firewall-portal/1-connection-security.png)-->

3. Fare clic su **Aggiungi indirizzo IP client corrente** nelle regole del firewall. Viene creata automaticamente una regola del firewall con l'indirizzo IP pubblico del computer rilevato dal sistema Azure.

   <!--![Azure portal - click Add My IP](./media/howto-manage-firewall-portal/2-add-my-ip.png)-->

4. Verificare l'indirizzo IP prima di salvare la configurazione. In alcuni casi, l'indirizzo IP individuato dal portale di Azure è diverso da quello usato per l'accesso a Internet e ai server Azure, Potrebbe pertanto essere necessario modificare l'indirizzo IP iniziale e l'indirizzo IP finale per fare in modo che la regola funzioni come previsto.

   È possibile usare un motore di ricerca o un altro strumento online per controllare il proprio indirizzo IP. Ad esempio, cercare "qual è l'indirizzo IP".

   <!--![Bing search for What is my IP](./media/howto-manage-firewall-portal/3-what-is-my-ip.png)-->

5. Aggiungere altri intervalli di indirizzi. Nelle regole del firewall per database di Azure per PostgreSQL-server flessibile è possibile specificare un singolo indirizzo IP o un intervallo di indirizzi. Se si vuole limitare la regola a un singolo indirizzo IP, digitare lo stesso indirizzo nel campo per indirizzo IP iniziale e indirizzo IP finale. L'apertura del firewall consente agli amministratori, agli utenti e alle applicazioni di accedere a qualsiasi database nel server PostgreSQL per cui hanno credenziali valide.

   <!--![Azure portal - firewall rules](./media/howto-manage-firewall-portal/4-specify-addresses.png)-->

6. Fare clic su **Salva** sulla barra degli strumenti per salvare questa regola del firewall. Attendere la conferma che l'aggiornamento delle regole del firewall abbia avuto esito positivo.

   <!--![Azure portal - click Save](./media/howto-manage-firewall-portal/5-save-firewall-rule.png)-->

## <a name="connecting-from-azure"></a>Connessione da Azure

Potrebbe essere necessario abilitare le risorse o le applicazioni distribuite in Azure per la connessione al server flessibile. Sono incluse le applicazioni Web ospitate nel servizio app Azure, in esecuzione in una macchina virtuale di Azure, un gateway di gestione dati Azure Data Factory e molto altro. 

Quando un'applicazione in Azure tenta di connettersi al server, il firewall verifica che le connessioni di Azure siano consentite. È possibile abilitare questa impostazione selezionando l'opzione **Consenti l'accesso pubblico da servizi di Azure e risorse all'interno di Azure al server** nel portale dalla scheda **rete** e quindi fare clic su **Salva**.

Non è necessario che le risorse si trovino nella stessa rete virtuale (VNet) o nel gruppo di risorse per la regola del firewall per abilitare tali connessioni. Se il tentativo di connessione non è consentito, la richiesta non raggiunge il database di Azure per PostgreSQL-server flessibile.

> [!IMPORTANT]
> Questa opzione configura il firewall in modo da consentire tutte le connessioni da Azure, incluse le connessioni dalle sottoscrizioni di altri clienti. Quando si seleziona questa opzione, verificare che l'account di accesso e le autorizzazioni utente limitino l'accesso ai soli utenti autorizzati.
>
> Si consiglia di scegliere l' **accesso privato (VNet Integration)** per accedere in modo sicuro a server flessibili.
>
## <a name="manage-existing-firewall-rules-through-the-azure-portal"></a>Gestire le regole del firewall esistenti tramite il portale di Azure

Ripetere i passaggi seguenti per gestire le regole del firewall.

- Per aggiungere il computer corrente, fare clic su + **Aggiungi indirizzo IP client corrente** nelle regole del firewall. È consigliabile fare clic su **Salva** per salvare le modifiche.
- Per aggiungere ulteriori indirizzi IP, digitare nome della regola, indirizzo IP iniziale e indirizzo IP finale. È consigliabile fare clic su **Salva** per salvare le modifiche.
- Per modificare una regola esistente, fare clic su uno dei campi nella regola e modificare. È consigliabile fare clic su **Salva** per salvare le modifiche.
- Per eliminare una regola esistente, fare clic sui puntini di sospensione […] e fare clic su **Elimina** per rimuovere la regola. È consigliabile fare clic su **Salva** per salvare le modifiche.

## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni sulla [rete in database di Azure per PostgreSQL-server flessibile](./concepts-networking.md)
- Altre informazioni su [database di Azure per PostgreSQL-regole flessibili del firewall del server](./concepts-networking.md#public-access-allowed-ip-addresses)
- [Creare e gestire regole del firewall di database di Azure per PostgreSQL usando l'interfaccia della riga di comando di Azure](./how-to-manage-firewall-cli.md)