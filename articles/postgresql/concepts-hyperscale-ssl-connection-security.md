---
title: TLS - Hyperscale (Citus) - Database di Azure per PostgreSQL
description: Istruzioni e informazioni per configurare il database di Azure per PostgreSQL - Hyperscale (Citus) e le applicazioni associate per usare correttamente le connessioni TLS.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/30/2020
ms.openlocfilehash: 8a691b6d3f28800ab1cb38a72f124e950d485084
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422329"
---
# <a name="configure-tls-in-azure-database-for-postgresql---hyperscale-citus"></a>Configurare TLS nel database di Azure per PostgreSQL - Hyperscale (Citus)Configure TLS in Azure Database for PostgreSQL - Hyperscale (Citus)
Le connessioni dell'applicazione client al nodo del coordinatore Hyperscale (Citus) richiedono Transport Layer Security (TLS), precedentemente noto come Secure Sockets Layer (SSL). L'applicazione delle connessioni TLS tra il server di database e le applicazioni client consente di proteggersi da attacchi "man-in-the-middle" crittografando il flusso di dati tra il server e l'applicazione.

## <a name="enforcing-tls-connections"></a>Applicazione delle connessioni TLS
Per tutti i database di Azure per i server PostgreSQL di cui è stato eseguito il provisioning tramite il portale di Azure, l'imposizione delle connessioni TLS è abilitata per impostazione predefinita. 

Analogamente, le stringhe di connessione predefinite nelle impostazioni "Stringhe di connessione" nel server nel portale di Azure includono i parametri necessari per le lingue comuni per connettersi al server di database usando TLS. Il parametro TLS varia in base al connettore, ad esempio "ssl- true" o "sslmode-require" o "sslmode-required" e altre varianti.

## <a name="ensure-your-application-or-framework-supports-tls-connections"></a>Verificare che l'applicazione o il framework supporti le connessioni TLSEnsure your application or framework supports TLS connections
Alcuni framework dell'applicazione che utilizzano PostgreSQL per i servizi di database non abilitano TLS per impostazione predefinita durante l'installazione. Se il server PostgreSQL applica connessioni TLS ma l'applicazione non è configurata per TLS, l'applicazione potrebbe non riuscire a connettersi al server di database. Consultare la documentazione dell'applicazione per informazioni su come abilitare le connessioni TLS.

## <a name="applications-that-require-certificate-verification-for-tls-connectivity"></a>Applicazioni che richiedono la verifica del certificato per la connettività TLSApplications that require certificate verification for TLS connectivity
In alcuni casi, le applicazioni richiedono un file del certificato locale generato da un file di certificato (.cer) dell'autorità di certificazione (CA) attendibile per connettersi in modo sicuro. Il certificato per la connessione a un database di Azure per PostgreSQL - Hyperscale (Citus) si trova in https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem. Scaricare il file del certificato e salvarlo nella posizione preferita.

### <a name="connect-using-psql"></a>Connettersi tramite psqlConnect using psql
Nell'esempio seguente viene illustrato come connettersi al nodo coordinatore Hyperscale (Citus) utilizzando l'utilità della riga di comando psql. Usare `sslmode=verify-full` l'impostazione della stringa di connessione per applicare la verifica del certificato TLS. Passare il percorso del `sslrootcert` file del certificato locale al parametro.

Di seguito è riportato un esempio della stringa di connessione psql:Below is an example of the psql connection string:
```
psql "sslmode=verify-full sslrootcert=DigiCertGlobalRootCA.crt.pem host=mydemoserver.postgres.database.azure.com dbname=citus user=citus password=your_pass"
```
> [!TIP]
> Verificare che il `sslrootcert` valore passato a corrisponda al percorso del file per il certificato salvato.

## <a name="next-steps"></a>Passaggi successivi
Aumentare ulteriormente la sicurezza con le regole del firewall nel database di [Azure per PostgreSQL - Hyperscale (Citus)](concepts-hyperscale-firewall-rules.md).
