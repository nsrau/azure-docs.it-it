---
title: SSL - Hyperscale (Citus) - Database di Azure per PostgreSQL
description: Istruzioni e informazioni per configurare il database di Azure per PostgreSQL - Hyperscale (Citus) e le applicazioni associate per usare correttamente le connessioni SSL.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/17/2019
ms.openlocfilehash: 3e4ef5d2d6db3a3d4f8923f47079f2484639a751
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74973986"
---
# <a name="configure-ssl-in-azure-database-for-postgresql---hyperscale-citus"></a>Configurare SSL nel database di Azure per PostgreSQL - Hyperscale (Citus)Configure SSL in Azure Database for PostgreSQL - Hyperscale (Citus)
Le connessioni dell'applicazione client al nodo del coordinatore Hyperscale (Citus) richiedono SSL (Secure Sockets Layer). L'applicazione delle connessioni SSL tra il server di database e le applicazioni client consente di proteggersi da attacchi "man-in-the-middle" crittografando il flusso di dati tra il server e l'applicazione.

## <a name="enforcing-ssl-connections"></a>Applicazione delle connessioni SSL
Per tutti i database di Azure per i server PostgreSQL di cui è stato eseguito il provisioning tramite il portale di Azure, l'imposizione delle connessioni SSL è abilitata per impostazione predefinita. 

Analogamente, le stringhe di connessione predefinite nelle impostazioni "Stringhe di connessione" per il server nel portale di Azure includono i parametri obbligatori per le lingue comuni per effettuare la connessione al server di database tramite SSL. Il parametro SSL varia in base al connettore, ad esempio "ssl=true", "sslmode=require" oppure "sslmode=required" e altre varianti.

## <a name="ensure-your-application-or-framework-supports-ssl-connections"></a>Verificare che le connessioni SSL siano supportate dall'applicazione o dal framework
Alcuni framework dell'applicazione che utilizzano PostgreSQL per i servizi di database non abilitano SSL per impostazione predefinita durante l'installazione. Se il server PostgreSQL applica connessioni SSL ma l'applicazione non è configurata per SSL, l'applicazione potrebbe non riuscire a connettersi al server di database. Consultare la documentazione dell'applicazione per le informazioni su come abilitare le connessioni SSL.

## <a name="applications-that-require-certificate-verification-for-ssl-connectivity"></a>Applicazioni che richiedono la verifica del certificato per la connettività SSL
In alcuni casi, le applicazioni richiedono un file del certificato locale generato da un file di certificato (.cer) dell'autorità di certificazione (CA) attendibile per connettersi in modo sicuro. Il certificato per la connessione a un database di Azure per PostgreSQL - Hyperscale (Citus) si trova in https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem. Scaricare il file del certificato e salvarlo nella posizione preferita.

### <a name="connect-using-psql"></a>Connettersi tramite psqlConnect using psql
Nell'esempio seguente viene illustrato come connettersi al nodo coordinatore Hyperscale (Citus) utilizzando l'utilità della riga di comando psql. Utilizzare `sslmode=verify-full` l'impostazione della stringa di connessione per applicare la verifica del certificato SSL. Passare il percorso del `sslrootcert` file del certificato locale al parametro.

Di seguito è riportato un esempio della stringa di connessione psql:Below is an example of the psql connection string:
```
psql "sslmode=verify-full sslrootcert=DigiCertGlobalRootCA.crt.pem host=mydemoserver.postgres.database.azure.com dbname=citus user=citus password=your_pass"
```
> [!TIP]
> Verificare che il `sslrootcert` valore passato a corrisponda al percorso del file per il certificato salvato.

## <a name="next-steps"></a>Passaggi successivi
Aumentare ulteriormente la sicurezza con le regole del firewall nel database di [Azure per PostgreSQL - Hyperscale (Citus)](concepts-hyperscale-firewall-rules.md).
