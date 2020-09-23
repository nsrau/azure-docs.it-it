---
title: Connettività crittografata con TLS/SSL nel database di Azure per PostgreSQL-server flessibile
description: Istruzioni e informazioni su come connettersi usando TLS/SSL nel database di Azure per PostgreSQL-server flessibile.
author: ambhatna
ms.author: ambhatna
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 0ba04ff14e62cd411515a765dc95ef7a3e72b709
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90938922"
---
# <a name="encrypted-connectivity-using-transport-layer-security-in-azure-database-for-postgresql---flexible-server"></a>Connettività crittografata con Transport Layer Security nel database di Azure per PostgreSQL-server flessibile

> [!IMPORTANT]
> Il server flessibile di Database di Azure per PostgreSQL è disponibile in anteprima

Database di Azure per PostgreSQL: il server flessibile supporta la connessione delle applicazioni client al servizio PostgreSQL usando Transport Layer Security (TLS), precedentemente noto come Secure Sockets Layer (SSL). TLS è un protocollo standard del settore che garantisce connessioni di rete crittografate tra il server di database e le applicazioni client, consentendo di rispettare i requisiti di conformità.

Database di Azure per PostgreSQL: il server flessibile supporta le connessioni crittografate con Transport Layer Security (TLS 1.2 +) e tutte le connessioni in ingresso con TLS 1,0 e TLS 1,1 verranno negate. Per tutti i server flessibili, l'applicazione delle connessioni TLS è abilitata e non è possibile disabilitare TLS/SSL per la connessione al server flessibile.

## <a name="applications-that-require-certificate-verification-for-tlsssl-connectivity"></a>Applicazioni che richiedono la verifica del certificato per la connettività TLS/SSL
In alcuni casi, le applicazioni richiedono un file di certificato locale generato da un file di certificato dell'autorità di certificazione (CA) attendibile per la connessione sicura. Database di Azure per PostgreSQL: il server flessibile usa la *CA radice globale DigiCert*. Scaricare questo certificato necessario per comunicare tramite SSL dalla [CA radice globale DigiCert](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem) e salvare il file del certificato nel percorso preferito. Ad esempio, questa esercitazione usa `c:\ssl`.


### <a name="connect-using-psql"></a>Connettersi con PSQL
Se il server flessibile è stato creato con l'opzione per l'*accesso privato (integrazione rete virtuale)* , sarà necessario connettersi al server da una risorsa all'interno della stessa rete virtuale usata dal server. È possibile creare una macchina virtuale e aggiungerla alla rete virtuale creata con il server flessibile.

Se il server flessibile è stato creato con l'opzione per l'*accesso pubblico (indirizzi IP consentiti)* , è possibile aggiungere l'indirizzo IP locale all'elenco di regole del firewall nel server.

Nell'esempio seguente viene illustrato come connettersi al server utilizzando l'interfaccia della riga di comando psql. Usare l' `sslmode=verify-full` impostazione della stringa di connessione per applicare la verifica del certificato TLS/SSL. Passare il percorso del file del certificato locale al `sslrootcert` parametro.

```bash
 psql "sslmode=verify-full sslrootcert=c:\ssl\DigiCertGlobalRootCA.crt.pem host=mydemoserver.postgres.database.azure.com dbname=postgres user=myadmin"
```
> [!Note]
> Confermare che il valore passato a `sslrootcert` corrisponda al percorso del file del certificato salvato.

## <a name="ensure-your-application-or-framework-supports-tls-connections"></a>Assicurarsi che l'applicazione o il Framework supporti le connessioni TLS

Alcuni framework di applicazioni che usano PostgreSQL per i servizi di database non abilitano TLS per impostazione predefinita durante l'installazione. Il server PostgreSQL impone le connessioni TLS, ma se l'applicazione non è configurata per TLS, l'applicazione potrebbe non riuscire a connettersi al server di database. Consultare la documentazione dell'applicazione per informazioni su come abilitare le connessioni TLS.

## <a name="next-steps"></a>Passaggi successivi
- [Creare e gestire database di Azure per PostgreSQL: rete virtuale server flessibile usando l'interfaccia della riga di comando di Azure](./how-to-manage-virtual-network-cli.md).
- Altre informazioni sulla [rete in database di Azure per PostgreSQL-server flessibile](./concepts-networking.md)
- Altre informazioni su [database di Azure per PostgreSQL-regole flessibili del firewall del server](./concepts-networking.md#public-access-allowed-ip-addresses)
