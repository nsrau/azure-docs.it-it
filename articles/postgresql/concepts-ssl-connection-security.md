---
title: TLS-database di Azure per PostgreSQL-server singolo
description: 'Istruzioni e informazioni su come configurare la connettività TLS per database di Azure per PostgreSQL: singolo server.'
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: d0482e5205b97b5c57c41e0ba98fb9ca819e5d5f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82141739"
---
# <a name="configure-tls-connectivity-in-azure-database-for-postgresql---single-server"></a>Configurare la connettività TLS nel database di Azure per PostgreSQL-server singolo

Database di Azure per PostgreSQL preferisce connettere le applicazioni client al servizio PostgreSQL usando Transport Layer Security (TLS), precedentemente noto come Secure Sockets Layer (SSL). L'applicazione delle connessioni TLS tra il server di database e le applicazioni client consente di proteggersi dagli attacchi "Man-in-the-Middle" crittografando il flusso di dati tra il server e l'applicazione.

Per impostazione predefinita, il servizio database PostgreSQL è configurato per richiedere la connessione TLS. È possibile scegliere di disabilitare la richiesta di TLS se l'applicazione client non supporta la connettività TLS.

## <a name="enforcing-tls-connections"></a>Applicazione delle connessioni TLS

Per tutti i server di database di Azure per PostgreSQL con provisioning tramite il portale di Azure e l'interfaccia della riga di comando, l'applicazione delle connessioni TLS è abilitata per impostazione predefinita. 

Analogamente, le stringhe di connessione predefinite nelle impostazioni "stringhe di connessione" del server nel portale di Azure includono i parametri obbligatori per le lingue comuni per la connessione al server di database tramite TLS. Il parametro TLS varia in base al connettore, ad esempio "SSL = true" o "sslmode = require" o "sslmode = required" e altre varianti.

## <a name="configure-enforcement-of-tls"></a>Configurare l'applicazione di TLS

Facoltativamente, è possibile disabilitare l'applicazione della connettività TLS. Microsoft Azure consiglia di abilitare sempre l'impostazione **Imponi connessione SSL** per la sicurezza avanzata.

### <a name="using-the-azure-portal"></a>Uso del portale di Azure

Visitare il Database di Azure per il server PostgreSQL e fare clic su **Sicurezza connessione**. Usare l'interruttore per abilitare o disabilitare l'impostazione **Imponi connessione SSL**. Fare quindi clic su **Salva**.

![Sicurezza della connessione-disabilitare applica TLS/SSL](./media/concepts-ssl-connection-security/1-disable-ssl.png)

È possibile confermare l'impostazione aprendo la pagina **Panoramica** per visualizzare l'indicatore **Stato imposizione SSL**.

### <a name="using-azure-cli"></a>Utilizzare l'interfaccia della riga di comando di Azure

È possibile abilitare o disabilitare il parametro **ssl-enforcement** usando rispettivamente i valori `Enabled` o `Disabled` nell'interfaccia della riga di comando di Azure.

```azurecli
az postgres server update --resource-group myresourcegroup --name mydemoserver --ssl-enforcement Enabled
```

## <a name="ensure-your-application-or-framework-supports-tls-connections"></a>Assicurarsi che l'applicazione o il Framework supporti le connessioni TLS

Alcuni framework di applicazioni che usano PostgreSQL per i servizi di database non abilitano TLS per impostazione predefinita durante l'installazione. Se il server PostgreSQL impone le connessioni TLS ma l'applicazione non è configurata per TLS, l'applicazione potrebbe non riuscire a connettersi al server di database. Consultare la documentazione dell'applicazione per informazioni su come abilitare le connessioni TLS.

## <a name="applications-that-require-certificate-verification-for-tls-connectivity"></a>Applicazioni che richiedono la verifica del certificato per la connettività TLS

In alcuni casi, le applicazioni richiedono un file del certificato locale generato da un file di certificato (.cer) dell'autorità di certificazione (CA) attendibile per connettersi in modo sicuro. Il certificato per la connessione a un database di Azure per il server PostgreSQL https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pemsi trova in. Scaricare il file del certificato e salvarlo nel percorso preferito.

### <a name="connect-using-psql"></a>Connettersi con PSQL

L'esempio seguente illustra come connettersi al server PostgreSQL tramite l'utilità da riga di comando psql. Usare l' `sslmode=verify-full` impostazione della stringa di connessione per applicare la verifica del certificato TLS/SSL. Passare il percorso del file del certificato locale `sslrootcert` al parametro.

Il comando seguente è un esempio della stringa di connessione PSQL:

```shell
psql "sslmode=verify-full sslrootcert=BaltimoreCyberTrustRoot.crt host=mydemoserver.postgres.database.azure.com dbname=postgres user=myusern@mydemoserver"
```

> [!TIP]
> Confermare che il valore passato a `sslrootcert` corrisponda al percorso del file del certificato salvato.

## <a name="next-steps"></a>Passaggi successivi

Esaminare varie opzioni di connettività delle applicazioni in [raccolte connessioni per database di Azure per PostgreSQL](concepts-connection-libraries.md).
