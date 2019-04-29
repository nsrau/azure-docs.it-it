---
title: Connettere un Servizio app di Azure esistente a Database di Azure per MySQL
description: Istruzioni su come connettere correttamente un servizio app di Azure esistente al Database di Azure per MySQL
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 09/26/2018
ms.openlocfilehash: eb2fee7c76bcf29aee2dcd70d7975d7631bb23f6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61459204"
---
# <a name="connect-an-existing-azure-app-service-to-azure-database-for-mysql-server"></a>Connessione di un servizio app di Azure esistente al Database di Azure per MySQL
Questo argomento descrive come connettere un Servizio app di Azure esistente al database di Azure per il server MySQL.

## <a name="before-you-begin"></a>Prima di iniziare
Accedere al [Portale di Azure](https://portal.azure.com). Creare un database di Azure per il server MySQL. Per informazioni dettagliate, vedere [Come creare il database di Azure per il server MySQL dal portale](quickstart-create-mysql-server-database-using-azure-portal.md) o [Come creare il database di Azure per il server MySQL con l'interfaccia della riga di comando](quickstart-create-mysql-server-database-using-azure-cli.md).

Attualmente sono disponibili due soluzioni per abilitare l'accesso dal servizio app di Azure a un database di Azure per MySQL. Entrambe le soluzioni implicano la configurazione di regole firewall a livello di server.

## <a name="solution-1---allow-azure-services"></a>Soluzione 1: Consenti servizi di Azure
Il database di Azure per MySQL offre accesso alla sicurezza tramite un firewall per proteggere i dati. Durante la connessione da un servizio app di Azure al database di Azure per il server MySQL, tenere presente che gli indirizzi IP in uscita del servizio app hanno una natura dinamica. Scelta dell'opzione "Consenti l'accesso a Servizi di Azure" consentirà al servizio app di connettersi al server MySQL.

1. Nel pannello del server MySQL fare clic su **Sicurezza connessione** sotto l'intestazione Impostazioni per aprire il pannello Sicurezza connessione per il database di Azure per MySQL.

   ![Portale di Azure: fare clic su Sicurezza connessione](./media/howto-connect-webapp/1-connection-security.png)

2. Selezionare **ON** in **Consenti l'accesso a Servizi di Azure**, quindi **Salva**.
   ![Portale di Azure: Consenti accesso ad Azure](./media/howto-connect-webapp/allow-azure.png)

## <a name="solution-2---create-a-firewall-rule-to-explicitly-allow-outbound-ips"></a>Soluzione 2: creare una regola del firewall per consentire in modo esplicito gli indirizzi IP in uscita
È possibile aggiungere in modo esplicito tutti gli IP in uscita del servizio app di Azure.

1. Nel pannello Proprietà del servizio app, visualizzare l'**INDIRIZZO IP IN USCITA**.

   ![Portale di Azure - Visualizzare gli indirizzi IP in uscita](./media/howto-connect-webapp/2_1-outbound-ip-address.png)

2. Nel pannello Sicurezza connessione di MySQL, aggiungere gli indirizzi IP in uscita uno alla volta.

   ![Portale di Azure - Aggiungere indirizzi IP in modo esplicito](./media/howto-connect-webapp/2_2-add-explicit-ips.png)

3. Ricordarsi di **salvare** le regole del firewall.

Sebbene il servizio app di Azure tenti di mantenere gli indirizzi IP costanti nel tempo, vi sono casi in cui gli indirizzi IP possono cambiare. Ciò può avvenire ad esempio quando l'app esegue un riciclo o si effettua un'operazione di ridimensionamento oppure quando vengono aggiunti nuovi computer ai data center nell'area di Azure per aumentare la capacità. Quando gli indirizzi IP cambiano, l'app potrebbe subire tempi di inattività nel caso in cui non riesca a connettersi al server MySQL. È bene considerare questo fatto quando si sceglie una delle soluzioni precedenti.

## <a name="ssl-configuration"></a>Configurazione SSL
Il database di Azure per MySQL dispone dell'autenticazione SSL abilitata per impostazione predefinita. Se l'applicazione non usa l'autenticazione SSL per connettersi al database, è necessario disabilitare SSL nel server MySQL. Per informazioni dettagliate su come configurare l'autenticazione SSL, vedere [Uso di SSL con il database di Azure per MySQL](howto-configure-ssl.md).

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulle stringhe di connessione, vedere l'argomento relativo alle [stringhe di connessione](howto-connection-string.md).
