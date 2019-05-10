---
title: Connettività SSL per Database di Azure per MariaDB
description: Informazioni per la configurazione di Database di Azure per MariaDB e delle applicazioni associate per usare correttamente le connessioni SSL
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 15bb28846b3409dd31bcdf8d42990facc94fd06d
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "60332687"
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
