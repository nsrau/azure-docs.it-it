---
title: TLS-iperscalabilità (CITUS)-database di Azure per PostgreSQL
description: Istruzioni e informazioni per configurare il database di Azure per PostgreSQL-iperscalabilità (CITUS) e le applicazioni associate per usare correttamente le connessioni TLS.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/30/2020
ms.openlocfilehash: 8a691b6d3f28800ab1cb38a72f124e950d485084
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80422329"
---
# <a name="configure-tls-in-azure-database-for-postgresql---hyperscale-citus"></a>Configurare TLS in database di Azure per PostgreSQL-iperscalabilità (CITUS)
Le connessioni dell'applicazione client al nodo coordinatore iperscale (CITUS) richiedono Transport Layer Security (TLS), precedentemente noto come Secure Sockets Layer (SSL). L'applicazione delle connessioni TLS tra il server di database e le applicazioni client consente di proteggersi dagli attacchi "Man-in-the-Middle" crittografando il flusso di dati tra il server e l'applicazione.

## <a name="enforcing-tls-connections"></a>Applicazione delle connessioni TLS
Per tutti i server di database di Azure per PostgreSQL sottoposti a provisioning tramite il portale di Azure, l'applicazione delle connessioni TLS è abilitata per impostazione predefinita. 

Analogamente, le stringhe di connessione predefinite nelle impostazioni "stringhe di connessione" del server nel portale di Azure includono i parametri obbligatori per le lingue comuni per la connessione al server di database tramite TLS. Il parametro TLS varia in base al connettore, ad esempio "SSL = true" o "sslmode = require" o "sslmode = required" e altre varianti.

## <a name="ensure-your-application-or-framework-supports-tls-connections"></a>Assicurarsi che l'applicazione o il Framework supporti le connessioni TLS
Alcuni framework di applicazioni che usano PostgreSQL per i servizi di database non abilitano TLS per impostazione predefinita durante l'installazione. Se il server PostgreSQL impone le connessioni TLS ma l'applicazione non è configurata per TLS, l'applicazione potrebbe non riuscire a connettersi al server di database. Consultare la documentazione dell'applicazione per informazioni su come abilitare le connessioni TLS.

## <a name="applications-that-require-certificate-verification-for-tls-connectivity"></a>Applicazioni che richiedono la verifica del certificato per la connettività TLS
In alcuni casi, le applicazioni richiedono un file del certificato locale generato da un file di certificato (.cer) dell'autorità di certificazione (CA) attendibile per connettersi in modo sicuro. Il certificato per la connessione a un database di Azure per PostgreSQL-iperscalabilità (CITUS) https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pemsi trova in. Scaricare il file del certificato e salvarlo nel percorso preferito.

### <a name="connect-using-psql"></a>Connettersi con PSQL
Nell'esempio seguente viene illustrato come connettersi al nodo coordinatore CITUS (iperscale) utilizzando l'utilità da riga di comando psql. Usare l' `sslmode=verify-full` impostazione della stringa di connessione per applicare la verifica del certificato TLS. Passare il percorso del file del certificato locale `sslrootcert` al parametro.

Di seguito è riportato un esempio della stringa di connessione PSQL:
```
psql "sslmode=verify-full sslrootcert=DigiCertGlobalRootCA.crt.pem host=mydemoserver.postgres.database.azure.com dbname=citus user=citus password=your_pass"
```
> [!TIP]
> Confermare che il valore passato a `sslrootcert` corrisponda al percorso del file del certificato salvato.

## <a name="next-steps"></a>Passaggi successivi
Migliorare ulteriormente la sicurezza con [le regole del firewall in database di Azure per PostgreSQL-iperscalabilità (CITUS)](concepts-hyperscale-firewall-rules.md).
