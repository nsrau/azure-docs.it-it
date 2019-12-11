---
title: SSL-iperscalabilità (CITUS)-database di Azure per PostgreSQL
description: Istruzioni e informazioni per configurare il database di Azure per PostgreSQL-iperscalabilità (CITUS) e le applicazioni associate per usare correttamente le connessioni SSL.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/17/2019
ms.openlocfilehash: 3e4ef5d2d6db3a3d4f8923f47079f2484639a751
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74973986"
---
# <a name="configure-ssl-in-azure-database-for-postgresql---hyperscale-citus"></a>Configurare SSL in database di Azure per PostgreSQL-iperscalabilità (CITUS)
Le connessioni dell'applicazione client al nodo coordinatore iperscale (CITUS) richiedono Secure Sockets Layer (SSL). L'applicazione delle connessioni SSL tra il server di database e le applicazioni client consente di proteggersi dagli attacchi "Man-in-the-Middle" crittografando il flusso di dati tra il server e l'applicazione.

## <a name="enforcing-ssl-connections"></a>Applicazione delle connessioni SSL
Per tutti i server di database di Azure per PostgreSQL sottoposti a provisioning tramite il portale di Azure, l'applicazione delle connessioni SSL è abilitata per impostazione predefinita. 

Analogamente, le stringhe di connessione predefinite nelle impostazioni "Stringhe di connessione" per il server nel portale di Azure includono i parametri obbligatori per le lingue comuni per effettuare la connessione al server di database tramite SSL. Il parametro SSL varia in base al connettore, ad esempio "ssl=true", "sslmode=require" oppure "sslmode=required" e altre varianti.

## <a name="ensure-your-application-or-framework-supports-ssl-connections"></a>Verificare che le connessioni SSL siano supportate dall'applicazione o dal framework
Per impostazione predefinita, alcuni framework applicazione che usano PostgreSQL per i servizi di database non abilitano SSL durante l'installazione. Se il server PostgreSQL impone le connessioni SSL, ma l'applicazione non è configurata per SSL, l'applicazione potrebbe non riuscire a connettersi al server di database. Consultare la documentazione dell'applicazione per le informazioni su come abilitare le connessioni SSL.

## <a name="applications-that-require-certificate-verification-for-ssl-connectivity"></a>Applicazioni che richiedono la verifica del certificato per la connettività SSL
In alcuni casi, le applicazioni richiedono un file del certificato locale generato da un file di certificato (.cer) dell'autorità di certificazione (CA) attendibile per connettersi in modo sicuro. Il certificato per la connessione a un database di Azure per PostgreSQL-iperscalabilità (CITUS) si trova in https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem. Scaricare il file del certificato e salvarlo nel percorso preferito.

### <a name="connect-using-psql"></a>Connettersi con PSQL
Nell'esempio seguente viene illustrato come connettersi al nodo coordinatore CITUS (iperscale) utilizzando l'utilità da riga di comando psql. Utilizzare l'impostazione della stringa di connessione `sslmode=verify-full` per applicare la verifica del certificato SSL. Passare il percorso del file del certificato locale al parametro `sslrootcert`.

Di seguito è riportato un esempio della stringa di connessione PSQL:
```
psql "sslmode=verify-full sslrootcert=DigiCertGlobalRootCA.crt.pem host=mydemoserver.postgres.database.azure.com dbname=citus user=citus password=your_pass"
```
> [!TIP]
> Verificare che il valore passato a `sslrootcert` corrisponda al percorso del file del certificato salvato.

## <a name="next-steps"></a>Passaggi successivi
Migliorare ulteriormente la sicurezza con [le regole del firewall in database di Azure per PostgreSQL-iperscalabilità (CITUS)](concepts-hyperscale-firewall-rules.md).
