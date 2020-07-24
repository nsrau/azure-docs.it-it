---
title: Transport Layer Security (TLS)-overscale (CITUS)-database di Azure per PostgreSQL
description: Istruzioni e informazioni per configurare il database di Azure per PostgreSQL-iperscalabilità (CITUS) e le applicazioni associate per usare correttamente le connessioni TLS.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 07/16/2020
ms.openlocfilehash: 659f86a107e4b08db4ec5195635ea32d2260d677
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87071454"
---
# <a name="configure-tls-in-azure-database-for-postgresql---hyperscale-citus"></a>Configurare TLS in database di Azure per PostgreSQL-iperscalabilità (CITUS)
Il nodo coordinatore iperscale (CITUS) richiede che le applicazioni client si connettano con Transport Layer Security (TLS). L'applicazione di TLS tra il server di database e le applicazioni client contribuisce a garantire la riservatezza dei dati in transito. Le impostazioni di verifica aggiuntive descritte di seguito proteggono anche dagli attacchi "Man-in-the-Middle".

## <a name="enforcing-tls-connections"></a>Applicazione delle connessioni TLS
Le applicazioni utilizzano una "stringa di connessione" per identificare il database di destinazione e le impostazioni per una connessione. Client diversi richiedono impostazioni diverse. Per visualizzare un elenco di stringhe di connessione utilizzate dai client comuni, consultare la sezione **stringhe di connessione** per il gruppo di server nel portale di Azure.

I parametri TLS `ssl` e `sslmode` variano in base alle funzionalità del connettore, ad esempio `ssl=true` o `sslmode=require` `sslmode=required` .

## <a name="ensure-your-application-or-framework-supports-tls-connections"></a>Assicurarsi che l'applicazione o il Framework supporti le connessioni TLS
Per impostazione predefinita, alcuni framework applicazione non abilitano TLS per le connessioni PostgreSQL. Tuttavia, senza una connessione protetta, un'applicazione non può connettersi a un nodo coordinatore iperscalare (CITUS). Consultare la documentazione dell'applicazione per informazioni su come abilitare le connessioni TLS.

## <a name="applications-that-require-certificate-verification-for-tls-connectivity"></a>Applicazioni che richiedono la verifica del certificato per la connettività TLS
In alcuni casi, le applicazioni richiedono un file del certificato locale generato da un file di certificato (.cer) dell'autorità di certificazione (CA) attendibile per connettersi in modo sicuro. Il certificato per la connessione a un database di Azure per PostgreSQL-iperscalabilità (CITUS) si trova in https://cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem . Scaricare il file del certificato e salvarlo nel percorso preferito.

> [!NOTE]
>
> Per verificare l'autenticità del certificato, è possibile verificare l'impronta digitale SHA-256 usando lo strumento da riga di comando OpenSSL:
>
> ```sh
> openssl x509 -in DigiCertGlobalRootCA.crt.pem -noout -sha256 -fingerprint
>
> # should output:
> # 43:48:A0:E9:44:4C:78:CB:26:5E:05:8D:5E:89:44:B4:D8:4F:96:62:BD:26:DB:25:7F:89:34:A4:43:C7:01:61
> ```

### <a name="connect-using-psql"></a>Connettersi con PSQL
Nell'esempio seguente viene illustrato come connettersi al nodo coordinatore CITUS (iperscale) utilizzando l'utilità da riga di comando psql. Usare l' `sslmode=verify-full` impostazione della stringa di connessione per applicare la verifica del certificato TLS. Passare il percorso del file del certificato locale al `sslrootcert` parametro.

Di seguito è riportato un esempio della stringa di connessione PSQL:
```
psql "sslmode=verify-full sslrootcert=DigiCertGlobalRootCA.crt.pem host=mydemoserver.postgres.database.azure.com dbname=citus user=citus password=your_pass"
```
> [!TIP]
> Confermare che il valore passato a `sslrootcert` corrisponda al percorso del file del certificato salvato.

## <a name="next-steps"></a>Passaggi successivi
Migliorare ulteriormente la sicurezza con [le regole del firewall in database di Azure per PostgreSQL-iperscalabilità (CITUS)](concepts-hyperscale-firewall-rules.md).
