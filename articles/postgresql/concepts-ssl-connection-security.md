---
title: SSL-database di Azure per PostgreSQL-server singolo
description: Istruzioni e informazioni su come configurare la connettività SSL per database di Azure per PostgreSQL-singolo server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: 21b4dffa135e1311be8c738c634de22304665695
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74768147"
---
# <a name="configure-ssl-connectivity-in-azure-database-for-postgresql---single-server"></a>Configurare la connettività SSL nel database di Azure per PostgreSQL-server singolo
Il Database di Azure per PostgreSQL preferisce connettere le applicazioni client al servizio di PostgreSQL usando la connettività SSL (Secure Sockets Layer). L'applicazione delle connessioni SSL tra il server di database e le applicazioni client consente di proteggersi dagli attacchi "Man-in-the-Middle" crittografando il flusso di dati tra il server e l'applicazione.

Per impostazione predefinita, il servizio di database PostgreSQL è configurato per richiedere la connessione SSL. È possibile scegliere di disabilitare la richiesta di SSL se l'applicazione client non supporta la connettività SSL. 

## <a name="enforcing-ssl-connections"></a>Applicazione delle connessioni SSL
Per impostazione predefinita, l'opzione di applicazione delle connessioni SSL è attiva quando si esegue il provisioning di tutti i Database di Azure per i server PostgreSQL attraverso l'interfaccia della riga di comando e il portale di Azure. 

Analogamente, le stringhe di connessione predefinite nelle impostazioni "Stringhe di connessione" per il server nel portale di Azure includono i parametri obbligatori per le lingue comuni per effettuare la connessione al server di database tramite SSL. Il parametro SSL varia in base al connettore, ad esempio "ssl=true", "sslmode=require" oppure "sslmode=required" e altre varianti.

## <a name="configure-enforcement-of-ssl"></a>Configurare l'applicazione di SSL
Facoltativamente, è possibile disabilitare l'applicazione della connettività SSL. Microsoft Azure consiglia di abilitare sempre l'impostazione **Enforce SSL connection** (Applica connessione SSL) per una maggiore sicurezza.

### <a name="using-the-azure-portal"></a>Uso del portale di Azure
Visitare il Database di Azure per il server PostgreSQL e fare clic su **Sicurezza connessione**. Usare l'interruttore per abilitare o disabilitare l'impostazione **Applica connessione SSL**. Fare quindi clic su **Salva**. 

![Sicurezza connessione - Disabilitare l'applicazione di SSL](./media/concepts-ssl-connection-security/1-disable-ssl.png)

È possibile confermare l'impostazione aprendo la pagina **Panoramica** per visualizzare l'indicatore **Stato imposizione SSL**.

### <a name="using-azure-cli"></a>Utilizzare l'interfaccia della riga di comando di Azure
È possibile abilitare o disabilitare il parametro **ssl-enforcement** usando rispettivamente i valori `Enabled` o `Disabled` nell'interfaccia della riga di comando di Azure.

```azurecli
az postgres server update --resource-group myresourcegroup --name mydemoserver --ssl-enforcement Enabled
```

## <a name="ensure-your-application-or-framework-supports-ssl-connections"></a>Verificare che le connessioni SSL siano supportate dall'applicazione o dal framework
Per impostazione predefinita, alcuni framework applicazione che usano PostgreSQL per i servizi di database non abilitano SSL durante l'installazione. Se il server PostgreSQL impone le connessioni SSL, ma l'applicazione non è configurata per SSL, l'applicazione potrebbe non riuscire a connettersi al server di database. Consultare la documentazione dell'applicazione per le informazioni su come abilitare le connessioni SSL.


## <a name="applications-that-require-certificate-verification-for-ssl-connectivity"></a>Applicazioni che richiedono la verifica del certificato per la connettività SSL
In alcuni casi, le applicazioni richiedono un file del certificato locale generato da un file di certificato (.cer) dell'autorità di certificazione (CA) attendibile per connettersi in modo sicuro. Il certificato per la connessione a un database di Azure per il server PostgreSQL si trova in https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem. Scaricare il file del certificato e salvarlo nel percorso preferito. 

### <a name="connect-using-psql"></a>Connettersi con PSQL
L'esempio seguente illustra come connettersi al server PostgreSQL tramite l'utilità da riga di comando psql. Utilizzare l'impostazione della stringa di connessione `sslmode=verify-full` per applicare la verifica del certificato SSL. Passare il percorso del file del certificato locale al parametro `sslrootcert`.

Di seguito è riportato un esempio della stringa di connessione PSQL:
```
psql "sslmode=verify-full sslrootcert=BaltimoreCyberTrustRoot.crt host=mydemoserver.postgres.database.azure.com dbname=postgres user=myusern@mydemoserver"
```

> [!TIP]
> Verificare che il valore passato a `sslrootcert` corrisponda al percorso del file del certificato salvato.


## <a name="next-steps"></a>Passaggi successivi
Esaminare varie opzioni di connettività delle applicazioni in [raccolte connessioni per database di Azure per PostgreSQL](concepts-connection-libraries.md).
