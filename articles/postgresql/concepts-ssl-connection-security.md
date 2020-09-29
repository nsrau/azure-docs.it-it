---
title: SSL/TLS-database di Azure per PostgreSQL-server singolo
description: 'Istruzioni e informazioni su come configurare la connettività TLS per database di Azure per PostgreSQL: singolo server.'
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 07/08/2020
ms.openlocfilehash: e54b8bc51817d2a56153dcc0c14f45e76b9ae88b
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/29/2020
ms.locfileid: "91444940"
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

:::image type="content" source="./media/concepts-ssl-connection-security/1-disable-ssl.png" alt-text="Sicurezza della connessione-disabilitare applica TLS/SSL":::

È possibile confermare l'impostazione aprendo la pagina **Panoramica** per visualizzare l'indicatore **Stato imposizione SSL**.

### <a name="using-azure-cli"></a>Utilizzare l'interfaccia della riga di comando di Azure

È possibile abilitare o disabilitare il parametro **ssl-enforcement** usando rispettivamente i valori `Enabled` o `Disabled` nell'interfaccia della riga di comando di Azure.

```azurecli
az postgres server update --resource-group myresourcegroup --name mydemoserver --ssl-enforcement Enabled
```

## <a name="ensure-your-application-or-framework-supports-tls-connections"></a>Assicurarsi che l'applicazione o il Framework supporti le connessioni TLS

Alcuni framework di applicazioni che usano PostgreSQL per i servizi di database non abilitano TLS per impostazione predefinita durante l'installazione. Se il server PostgreSQL impone le connessioni TLS ma l'applicazione non è configurata per TLS, l'applicazione potrebbe non riuscire a connettersi al server di database. Consultare la documentazione dell'applicazione per informazioni su come abilitare le connessioni TLS.

## <a name="applications-that-require-certificate-verification-for-tls-connectivity"></a>Applicazioni che richiedono la verifica del certificato per la connettività TLS

In alcuni casi, le applicazioni richiedono un file di certificato locale generato da un file di certificato dell'autorità di certificazione (CA) attendibile per la connessione sicura. Il certificato per la connessione a un database di Azure per il server PostgreSQL si trova in https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem . Scaricare il file del certificato e salvarlo nel percorso preferito. 

Vedere i collegamenti seguenti per i certificati per i server in cloud sovrani: [Azure per enti pubblici](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem), [Azure Cina](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem)e [Azure Germania](https://www.d-trust.net/cgi-bin/D-TRUST_Root_Class_3_CA_2_2009.crt).

### <a name="connect-using-psql"></a>Connettersi con PSQL

L'esempio seguente illustra come connettersi al server PostgreSQL tramite l'utilità da riga di comando psql. Usare l' `sslmode=verify-full` impostazione della stringa di connessione per applicare la verifica del certificato TLS/SSL. Passare il percorso del file del certificato locale al `sslrootcert` parametro.

Il comando seguente è un esempio della stringa di connessione PSQL:

```shell
psql "sslmode=verify-full sslrootcert=BaltimoreCyberTrustRoot.crt host=mydemoserver.postgres.database.azure.com dbname=postgres user=myusern@mydemoserver"
```

> [!TIP]
> Confermare che il valore passato a `sslrootcert` corrisponda al percorso del file del certificato salvato.

## <a name="tls-enforcement-in-azure-database-for-postgresql-single-server"></a>Imposizione TLS nel server singolo database di Azure per PostgreSQL

Database di Azure per PostgreSQL: un server singolo supporta la crittografia per i client che si connettono al server di database usando Transport Layer Security (TLS). TLS è un protocollo standard del settore che garantisce connessioni di rete sicure tra il server di database e le applicazioni client, consentendo di rispettare i requisiti di conformità.

### <a name="tls-settings"></a>Impostazioni di TLS

Il server singolo di database di Azure per PostgreSQL offre la possibilità di applicare la versione TLS per le connessioni client. Per applicare la versione TLS, usare l'impostazione dell'opzione **versione minima di TLS** . Per questa impostazione di opzione sono consentiti i valori seguenti:

|  Impostazione minima di TLS             | Versione TLS del client supportata                |
|:---------------------------------|-------------------------------------:|
| TLSEnforcementDisabled (impostazione predefinita) | Non sono necessari TLS                      |
| TLS1_0                           | TLS 1,0, TLS 1,1, TLS 1,2 e versioni successive |
| TLS1_1                           | TLS 1,1, TLS 1,2 e versioni successive          |
| TLS1_2                           | TLS versione 1,2 e successive           |


Ad esempio, se si imposta questa versione minima dell'impostazione TLS su TLS 1,0, il server consentirà le connessioni dai client usando TLS 1,0, 1,1 e 1.2 +. In alternativa, l'impostazione di questa opzione su 1,2 significa che è possibile consentire solo le connessioni dai client che usano TLS 1.2 + e tutte le connessioni con TLS 1,0 e TLS 1,1 verranno rifiutate.

> [!Note] 
> Per impostazione predefinita, database di Azure per PostgreSQL non impone una versione minima di TLS (impostazione `TLSEnforcementDisabled` ).
>
> Quando si applica una versione minima di TLS, non sarà più possibile disabilitare l'imposizione minima della versione.

Per informazioni su come impostare l'impostazione TLS per il server singolo del database di Azure per PostgreSQL, vedere [How to configure TLS setting](howto-tls-configurations.md).

## <a name="cipher-support-by-azure-database-for-postgresql-single-server"></a>Supporto crittografico da server singolo database di Azure per PostgreSQL

Come parte della comunicazione SSL/TLS, i pacchetti di crittografia sono convalidati e supportano solo i pacchetti di crittografia che possono comunicare con il database server. La convalida del pacchetto di crittografia viene controllata nel [livello gateway](concepts-connectivity-architecture.md#connectivity-architecture) e non in modo esplicito nel nodo stesso. Se i pacchetti di crittografia non corrispondono a uno dei gruppi elencati di seguito, le connessioni client in ingresso verranno rifiutate.

### <a name="cipher-suite-supported"></a>Pacchetto di crittografia supportato

*   TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
*   TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
*   TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
*   TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256

## <a name="next-steps"></a>Passaggi successivi

Esaminare varie opzioni di connettività delle applicazioni in [raccolte connessioni per database di Azure per PostgreSQL](concepts-connection-libraries.md).

- Informazioni su come [configurare TLS](howto-tls-configurations.md)