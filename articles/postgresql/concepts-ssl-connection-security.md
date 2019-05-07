---
title: Configurare la connettività SSL nel Database di Azure per PostgreSQL - Server singolo
description: Istruzioni e informazioni per configurare i Database di Azure per PostgreSQL - singolo Server e delle applicazioni associate per usare correttamente le connessioni SSL.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 56611267872ca79d7d2fe3a08c9b9f49a9b1840b
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65067417"
---
# <a name="configure-ssl-connectivity-in-azure-database-for-postgresql---single-server"></a>Configurare la connettività SSL nel Database di Azure per PostgreSQL - Server singolo
Il Database di Azure per PostgreSQL preferisce connettere le applicazioni client al servizio di PostgreSQL usando la connettività SSL (Secure Sockets Layer). L'applicazione delle connessioni SSL tra il server di database e le applicazioni client aiuta a proteggersi dagli attacchi "man in the middle" crittografando il flusso di dati tra il server e l'applicazione.

Per impostazione predefinita, il servizio di database PostgreSQL è configurato per richiedere la connessione SSL. Se l'applicazione client non supporta la connettività SSL, si è liberi di disabilitare l'opzione di richiesta SSL per la connessione al servizio di database. 

## <a name="enforcing-ssl-connections"></a>Applicazione delle connessioni SSL
Per impostazione predefinita, l'opzione di applicazione delle connessioni SSL è attiva quando si esegue il provisioning di tutti i Database di Azure per i server PostgreSQL attraverso l'interfaccia della riga di comando e il portale di Azure. 

Analogamente, le stringhe di connessione predefinite nelle impostazioni "Stringhe di connessione" per il server nel portale di Azure includono i parametri obbligatori per le lingue comuni per effettuare la connessione al server di database tramite SSL. Il parametro SSL varia in base al connettore, ad esempio "ssl=true", "sslmode=require" oppure "sslmode=required" e altre varianti.

## <a name="configure-enforcement-of-ssl"></a>Configurare l'applicazione di SSL
Facoltativamente, è possibile disabilitare l'applicazione della connettività SSL. Microsoft Azure consiglia di abilitare sempre l'impostazione **Enforce SSL connection** (Applica connessione SSL) per una maggiore sicurezza.

### <a name="using-the-azure-portal"></a>Uso del portale di Azure
Visitare il Database di Azure per il server PostgreSQL e fare clic su **Sicurezza connessione**. Usare l'interruttore per abilitare o disabilitare l'impostazione **Imponi connessione SSL**. Fare quindi clic su **Salva**. 

![Sicurezza connessione - Disabilitare l'applicazione di SSL](./media/concepts-ssl-connection-security/1-disable-ssl.png)

È possibile confermare l'impostazione aprendo la pagina **Panoramica** per visualizzare l'indicatore **SSL enforce status** (Stato di applicazione di SSL).

### <a name="using-azure-cli"></a>Utilizzare l'interfaccia della riga di comando di Azure
È possibile abilitare o disabilitare il parametro **ssl-enforcement** usando rispettivamente i valori `Enabled` o `Disabled` nell'interfaccia della riga di comando di Azure.

```azurecli
az postgres server update --resource-group myresourcegroup --name mydemoserver --ssl-enforcement Enabled
```

## <a name="ensure-your-application-or-framework-supports-ssl-connections"></a>Verificare che le connessioni SSL siano supportate dall'applicazione o dal framework
Per impostazione predefinita, molti framework di applicazione comuni che usano i servizi PostgreSQL per database, quali Drupal e Django, non abilitano SSL durante l'installazione. L'abilitazione della connettività SSL deve essere eseguita dopo l'installazione o tramite i comandi dell'interfaccia della riga di comando specifici dell'applicazione. Se il server PostgreSQL applica le connessioni SSL e l'applicazione associata non è configurata correttamente, l'applicazione potrebbe non riuscire a connettersi al server di database. Consultare la documentazione dell'applicazione per le informazioni su come abilitare le connessioni SSL.


## <a name="applications-that-require-certificate-verification-for-ssl-connectivity"></a>Applicazioni che richiedono la verifica del certificato per la connettività SSL
In alcuni casi, le applicazioni richiedono un file del certificato locale generato da un file di certificato (.cer) dell'autorità di certificazione (CA) attendibile per connettersi in modo sicuro. Vedere la procedura seguente per ottenere il file .cer, decodificare il certificato e associarlo all'applicazione.

### <a name="download-the-certificate-file-from-the-certificate-authority-ca"></a>Scaricare il file del certificato dall'autorità di certificazione (CA) 
Il certificato richiesto per comunicare con il Database di Azure per il server PostgreSQL tramite SSL si trova [qui](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt). Scaricare il file del certificato e salvarlo in locale.

### <a name="install-a-cert-decoder-on-your-machine"></a>Installare un decodificatore di certificato nel computer 
È possibile usare [OpenSSL](https://github.com/openssl/openssl) per decodificare il file del certificato richiesto dall'applicazione per connettersi in modo sicuro al server di database. Per informazioni su come installare OpenSSL, vedere la [istruzioni di installazione di OpenSSL](https://github.com/openssl/openssl/blob/master/INSTALL). 


### <a name="decode-your-certificate-file"></a>Decodificare il file del certificato
Il file CA radice scaricato è in formato crittografato. Usare OpenSSL per decodificare il file del certificato. Per farlo, eseguire questo comando OpenSSL:

```
openssl x509 -inform DER -in BaltimoreCyberTrustRoot.crt -text -out root.crt
```

### <a name="connecting-to-azure-database-for-postgresql-with-ssl-certificate-authentication"></a>Connessione al Database di Azure per PostgreSQL con autenticazione del certificato SSL
Ora che il certificato è stato decodificato correttamente, è possibile connettersi al server di database in modo protetto tramite SSL. Per consentire la verifica dei certificati server, il certificato deve essere posizionato nel file ~/.postgresql/root.crt nella directory home dell'utente. Su Microsoft Windows il file è denominato % APPDATA%\postgresql\root.crt. 

#### <a name="connect-using-psql"></a>Connettersi tramite psql
L'esempio seguente illustra come connettersi al server PostgreSQL tramite l'utilità della riga di comando PSQL. Usare il file `root.crt` creato e `sslmode=verify-ca` o l'opzione `sslmode=verify-full`.

Usando l'interfaccia della riga di comando di PostgreSQL, eseguire il comando seguente:
```bash
psql "sslmode=verify-ca sslrootcert=root.crt host=mydemoserver.postgres.database.azure.com dbname=postgres user=mylogin@mydemoserver"
```
Se l'operazione va a buon fine, si dovrebbe ottenere l'output seguente:
```bash
Password for user mylogin@mydemoserver:
psql (9.6.2)
WARNING: Console code page (437) differs from Windows code page (1252)
     8-bit characters might not work correctly. See psql reference
     page "Notes for Windows users" for details.
SSL connection (protocol: TLSv1.2, cipher: ECDHE-RSA-AES256-SHA384, bits: 256, compression: off)
Type "help" for help.

postgres=>
```

## <a name="next-steps"></a>Passaggi successivi
Esaminare varie opzioni di connettività dell'applicazione come descritto nell'articolo [Raccolte connessioni per Database di Azure per PostgreSQL](concepts-connection-libraries.md).
