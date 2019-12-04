---
title: Connettività SSL-database di Azure per MariaDB
description: Informazioni per la configurazione di Database di Azure per MariaDB e delle applicazioni associate per usare correttamente le connessioni SSL
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: b7206db24c813c8f273dd57407c43974932ff110
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74772028"
---
# <a name="ssl-connectivity-in-azure-database-for-mariadb"></a>Connettività SSL di Database di Azure per MariaDB
Database di Azure per MariaDB supporta la connessione del server di database alle applicazioni client mediante Secure Sockets Layer (SSL). L'applicazione delle connessioni SSL tra il server di database e le applicazioni client aiuta a proteggersi dagli attacchi "man in the middle" crittografando il flusso di dati tra il server e l'applicazione.

## <a name="default-settings"></a>Impostazioni predefinite
Per impostazione predefinita, il servizio di database deve essere configurato per richiedere le connessioni SSL quando ci si connette a MariaDB.  È consigliabile evitare di disabilitare l'opzione SSL, quando possibile.

Quando si effettua il provisioning di un nuovo server Database di Azure per MariaDB attraverso il portale di Azure e l'interfaccia della riga di comando, vengono abilitate per impostazione predefinita le connessioni SSL.

Nel portale di Azure vengono visualizzate le stringhe di connessione per diversi linguaggi di programmazione. Tali stringhe di connessione includono i parametri SSL necessari per la connessione al database. Nel portale di Azure selezionare il server. Sotto l'intestazione **Impostazioni**, selezionare **Stringhe di connessione**. Il parametro SSL varia in base al connettore, ad esempio "ssl=true", "sslmode=require" oppure "sslmode=required" e altre varianti.

Per informazioni su come abilitare o disabilitare la connessione SSL durante lo sviluppo dell'applicazione, consultare [Come configurare SSL](howto-configure-ssl.md).

## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni sulle [regole del firewall del server](concepts-firewall-rules.md)
- Informazioni su come [configurare SSL](howto-configure-ssl.md).
