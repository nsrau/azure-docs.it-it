---
title: Connettività SSL per Database di Azure per MariaDB
description: Informazioni per la configurazione di Database di Azure per MariaDB e delle applicazioni associate per usare correttamente le connessioni SSL
author: ajlam
ms.author: andrela
editor: jasonwhowell
services: mariadb
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: dda5df58a83ddd3ce42fa887c3c32a3e23954920
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46946647"
---
# <a name="ssl-connectivity-in-azure-database-for-mariadb"></a>Connettività SSL di Database di Azure per MariaDB
Database di Azure per MariaDB supporta la connessione del server di database alle applicazioni client mediante Secure Sockets Layer (SSL). L'applicazione delle connessioni SSL tra il server di database e le applicazioni client aiuta a proteggersi dagli attacchi "man in the middle" crittografando il flusso di dati tra il server e l'applicazione.

## <a name="default-settings"></a>Impostazioni predefinite
Per impostazione predefinita, il servizio di database deve essere configurato per richiedere le connessioni SSL quando ci si connette a MariaDB.  È consigliabile evitare di disabilitare l'opzione SSL, quando possibile.

Quando si effettua il provisioning di un nuovo server Database di Azure per MariaDB attraverso il portale di Azure e l'interfaccia della riga di comando, vengono abilitate per impostazione predefinita le connessioni SSL.

Nel portale di Azure vengono visualizzate le stringhe di connessione per diversi linguaggi di programmazione. Tali stringhe di connessione includono i parametri SSL necessari per la connessione al database. Nel portale di Azure selezionare il server. Sotto l'intestazione **Impostazioni**, selezionare **Stringhe di connessione**. Il parametro SSL varia in base al connettore, ad esempio "ssl=true", "sslmode=require" oppure "sslmode=required" e altre varianti.

<!-- To learn how to enable or disable SSL connection when developing application, refer to [How to configure SSL](howto-configure-ssl.md).-->

## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni sulle [regole del firewall del server](concepts-firewall-rules.md)
- Informazioni su come [configurare SSL](howto-configure-ssl.md).
