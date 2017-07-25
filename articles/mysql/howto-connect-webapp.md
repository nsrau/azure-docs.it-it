---
title: Connessione del servizio app di Azure esistente al Database di Azure per MySQL | Microsoft Docs
description: Istruzioni su come connettere correttamente un servizio app di Azure esistente al Database di Azure per MySQL
services: mysql
author: v-chenyh
ms.author: v-chenyh
editor: jasonwhowell
manager: jhubbard
ms.service: mysql-database
ms.topic: article
ms.date: 05/23/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: a30a90682948b657fb31dd14101172282988cbf0
ms.openlocfilehash: 2488a28353f3cfe76dc5aa9f9a9159a37ee9901b
ms.contentlocale: it-it
ms.lasthandoff: 05/25/2017

---

# <a name="connect-an-existing-azure-app-service-to-azure-database-for-mysql-server"></a>Connessione di un servizio app di Azure esistente al Database di Azure per MySQL
Questo documento descrive come connettere un servizio app di Azure esistente al Database di Azure per il server MySQL.

## <a name="before-you-begin"></a>Prima di iniziare
Accedere al [Portale di Azure](https://portal.azure.com). Creare un database di Azure per il server MySQL. Per informazioni dettagliate, vedere [Come creare il database di Azure per il server MySQL dal portale](quickstart-create-mysql-server-database-using-azure-portal.md) o [Come creare il database di Azure per il server MySQL con l'interfaccia della riga di comando](quickstart-create-mysql-server-database-using-azure-cli.md).

Attualmente sono disponibili due soluzioni per abilitare l'accesso dal servizio app di Azure a un database di Azure per MySQL. Entrambe le soluzioni implicano la configurazione di regole firewall a livello di server.

## <a name="solution-1---create-a-firewall-rule-to-allow-all-ips"></a>Soluzione 1: creare una regola del firewall per consentire tutti gli indirizzi IP
Il database di Azure per MySQL offre accesso alla sicurezza tramite un firewall per proteggere i dati. Durante la connessione da un servizio app di Azure al database di Azure per il server MySQL, tenere presente che gli indirizzi IP in uscita del servizio app hanno una natura dinamica. 

Per garantire la disponibilità del servizio app di Azure, è consigliabile usare questa soluzione per consentire tutti gli indirizzi IP.

> [!NOTE]
> Microsoft sta lavorando a una soluzione a lungo termine per evitare che tutti gli indirizzi IP consentano ai servizi di Azure di connettersi al database di Azure per MySQL.

1. Nel pannello del server MySQL fare clic su **Sicurezza connessione** sotto l'intestazione Impostazioni per aprire il pannello Sicurezza connessione per Database di Azure per MySQL.

   ![Portale di Azure: fare clic su Sicurezza connessione](./media/howto-manage-firewall-using-portal/1-connection-security.png)

2. Compilare i campi **NOME REGOLA**, **INDIRIZZO IP INIZIALE** e **INDIRIZZO IP FINALE**. Fare quindi clic su **Salva**.
   - Nome regola: Allow-All-IPs
   - Indirizzo IP iniziale: 0.0.0.0
   - Indirizzo IP finale: 255.255.255.255

   ![Portale di Azure - Aggiungere tutti gli indirizzi IP](./media/howto-connect-webapp/1_2-add-all-ips.png)

## <a name="solution-2---create-a-firewall-rule-to-explicitly-allow-outbound-ips"></a>Soluzione 2: creare una regola del firewall per consentire in modo esplicito gli indirizzi IP in uscita
È possibile aggiungere in modo esplicito tutti gli IP in uscita del servizio app di Azure.

1. Nel pannello Proprietà del servizio app, visualizzare l'**INDIRIZZO IP IN USCITA**.

   ![Portale di Azure - Visualizzare gli indirizzi IP in uscita](./media/howto-connect-webapp/2_1-outbound-ip-address.png)

2. Nel pannello Sicurezza connessione di MySQL, aggiungere gli indirizzi IP in uscita uno alla volta.

   ![Portale di Azure - Aggiungere indirizzi IP in modo esplicito](./media/howto-connect-webapp/2_2-add-explicit-ips.png)

3. Ricordarsi di **salvare** le regole del firewall.

Sebbene il servizio app di Azure tenti di mantenere gli indirizzi IP costanti nel tempo, vi sono casi in cui gli indirizzi IP possono cambiare. Ad esempio, quando l'app ricicla o si effettua un'operazione di ridimensionamento, oppure quando vengono aggiunti nuovi data center nell'area di Azure per aumentare la capacità. Quando gli indirizzi IP cambiano, l'app potrebbe subire tempi di inattività nel caso in cui non riesca a connettersi al server MySQL. Considerare questa possibilità quando si sceglie una delle soluzioni precedenti.

## <a name="ssl-configuration"></a>Configurazione SSL
Il database di Azure per MySQL ha abilitato SSL per impostazione predefinita. Se l'applicazione non usa SSL per la connessione al database, è necessario disabilitarlo sul server MySQL. Per informazioni dettagliate su come configurare SSL, vedere [Uso di SSL con il database di Azure per MySQL](howto-configure-ssl.md).

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulle stringhe di connessione, vedere l'argomento relativo alle [stringhe di connessione](howto-connection-string.md).

