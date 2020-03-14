---
title: Connettività SSL-database di Azure per MariaDB
description: Informazioni per la configurazione di Database di Azure per MariaDB e delle applicazioni associate per usare correttamente le connessioni SSL
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: c03a56176a6e2cc995e74017b60747541fc843bb
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/14/2020
ms.locfileid: "79371631"
---
# <a name="ssl-connectivity-in-azure-database-for-mariadb"></a>Connettività SSL di Database di Azure per MariaDB
Database di Azure per MariaDB supporta la connessione del server di database alle applicazioni client mediante Secure Sockets Layer (SSL). L'applicazione delle connessioni SSL tra il server di database e le applicazioni client aiuta a proteggersi dagli attacchi "man in the middle" crittografando il flusso di dati tra il server e l'applicazione.

## <a name="default-settings"></a>Impostazioni predefinite
Per impostazione predefinita, il servizio di database deve essere configurato per richiedere le connessioni SSL quando ci si connette a MariaDB.  È consigliabile evitare di disabilitare l'opzione SSL, quando possibile.

Quando si effettua il provisioning di un nuovo server Database di Azure per MariaDB attraverso il portale di Azure e l'interfaccia della riga di comando, vengono abilitate per impostazione predefinita le connessioni SSL.

Nel portale di Azure vengono visualizzate le stringhe di connessione per diversi linguaggi di programmazione. Tali stringhe di connessione includono i parametri SSL necessari per la connessione al database. Nel portale di Azure selezionare il server. Sotto l'intestazione **Impostazioni**, selezionare **Stringhe di connessione**. Il parametro SSL varia in base al connettore, ad esempio "ssl=true", "sslmode=require" oppure "sslmode=required" e altre varianti.

Per informazioni su come abilitare o disabilitare la connessione SSL durante lo sviluppo dell'applicazione, consultare [Come configurare SSL](howto-configure-ssl.md).

## <a name="tls-connectivity-in-azure-database-for-mariadb"></a>Connettività TLS nel database di Azure per MariaDB

Il database di Azure per MariaDB supporta la crittografia per i client che si connettono al server di database usando Transport Layer Security (TLS). TLS è un protocollo standard del settore che garantisce connessioni di rete sicure tra il server di database e le applicazioni client, consentendo di rispettare i requisiti di conformità.

### <a name="tls-settings"></a>Impostazioni di TLS

Il database di Azure per MariaDB offre la possibilità di applicare la versione TLS per le connessioni client. Per usare l'opzione TLS, usare l'impostazione **minima della versione TLS** . Per questa impostazione di opzione sono consentiti i valori seguenti:

|  Impostazione minima di TLS             | Versione di TLS supportata                |
|:---------------------------------|-------------------------------------:|
| TLSEnforcementDisabled (impostazione predefinita) | Non sono necessari TLS                      |
| TLS1_0                           | TLS 1,0, TLS 1,1, TLS 1,2 e versioni successive |
| TLS1_1                           | TLS 1,1, TLS 1,2 e versioni successive          |
| TLS1_2                           | TLS versione 1,2 e successive           |


Ad esempio, se si imposta questa versione minima dell'impostazione TLS su TLS 1,0, il server consentirà le connessioni dai client usando TLS 1,0, 1,1 e 1.2 +. In alternativa, se si imposta questa opzione su 1,2, è possibile consentire solo le connessioni dai client che usano TLS 1,2 e tutte le connessioni con TLS 1,0 e TLS 1,1 verranno rifiutate.

> [!Note] 
> Il valore predefinito di TLS per database di Azure per MariaDB è disabilitato per tutti i nuovi server.
>
> Attualmente le versioni di TLS supportate dal database byAzure per MariaDB sono TLS 1,0, 1,1 e 1,2.

Per informazioni su come impostare l'impostazione TLS per il database di Azure per MariaDB, vedere [How to configure TLS setting](howto-tls-configurations.md).

## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni sulle [regole del firewall del server](concepts-firewall-rules.md)
- Informazioni su come [configurare SSL](howto-configure-ssl.md).
- Informazioni su come [configurare TLS](howto-tls-configurations.md).