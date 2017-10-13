---
title: "Configurare la connettività SSL nel Database di Azure per PostgreSQL | Microsoft Docs"
description: Indicazioni e informazioni per configurare il Database di Azure per PostgreSQL e delle applicazioni associate per usare correttamente le connessioni SSL.
services: postgresql
author: JasonMAnderson
ms.author: janders
editor: jasonwhowell
manager: jhubbard
ms.service: postgresql
ms.custom: 
ms.topic: article
ms.date: 05/15/2017
ms.openlocfilehash: 685aa4c2f75b7c3260ca737f7c786157480b2d90
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="configure-ssl-connectivity-in-azure-database-for-postgresql"></a>Configurare la connettività SSL nel Database di Azure per PostgreSQL
Il Database di Azure per PostgreSQL preferisce connettere le applicazioni client al servizio di PostgreSQL usando la connettività SSL (Secure Sockets Layer). L'applicazione delle connessioni SSL tra il server di database e le applicazioni client aiuta a proteggersi dagli attacchi "man in the middle" crittografando il flusso di dati tra il server e l'applicazione.

Per impostazione predefinita, il servizio di database PostgreSQL è configurato per richiedere la connessione SSL. Se l'applicazione client non supporta la connettività SSL, si è liberi di disabilitare l'opzione di richiesta SSL per la connessione al servizio di database. 

## <a name="enforcing-ssl-connections"></a>Applicazione delle connessioni SSL
Per impostazione predefinita, l'opzione di applicazione delle connessioni SSL è attiva quando si esegue il provisioning di tutti i Database di Azure per i server PostgreSQL attraverso l'interfaccia della riga di comando e il portale di Azure. 

Analogamente, le stringhe di connessione predefinite nelle impostazioni "Stringhe di connessione" per il server nel portale di Azure includono i parametri obbligatori per le lingue comuni per effettuare la connessione al server di database tramite SSL. Il parametro SSL varia in base al connettore, ad esempio "ssl=true", "sslmode=require" oppure "sslmode=required" e altre varianti.

## <a name="configure-enforcement-of-ssl"></a>Configurare l'applicazione di SSL
Facoltativamente, è possibile disabilitare l'applicazione della connettività SSL. Microsoft Azure consiglia di abilitare sempre l'impostazione **Enforce SSL connection** (Applica connessione SSL) per una maggiore sicurezza.

### <a name="using-the-azure-portal"></a>Uso del portale di Azure
Visitare il Database di Azure per il server PostgreSQL e fare clic su **Sicurezza connessione**. Usare l'interruttore per abilitare o disabilitare l'impostazione **Applica connessione SSL**. Fare quindi clic su **Salva**. 

![Sicurezza connessione - Disabilitare l'applicazione di SSL](./media/concepts-ssl-connection-security/1-disable-ssl.png)

È possibile confermare l'impostazione aprendo la pagina **Panoramica** per visualizzare l'indicatore **SSL enforce status** (Stato di applicazione di SSL).

### <a name="using-azure-cli"></a>Utilizzare l'interfaccia della riga di comando di Azure
È possibile abilitare o disabilitare il parametro **ssl-enforcement** usando rispettivamente i valori `Enabled` o `Disabled` nell'interfaccia della riga di comando di Azure.

```azurecli
az postgres server update --resource-group myresourcegroup --name mypgserver-20170401 --ssl-enforcement Enabled
```

## <a name="ensure-your-application-or-framework-supports-ssl-connections"></a>Verificare che le connessioni SSL siano supportate dall'applicazione o dal framework
Per impostazione predefinita, molti framework di applicazione comuni che usano i servizi PostgreSQL per database, quali Drupal e Django, non abilitano SSL durante l'installazione. L'abilitazione della connettività SSL deve essere eseguita dopo l'installazione o tramite i comandi dell'interfaccia della riga di comando specifici dell'applicazione. Se il server PostgreSQL applica le connessioni SSL e l'applicazione associata non è configurata correttamente, l'applicazione potrebbe non riuscire a connettersi al server di database. Consultare la documentazione dell'applicazione per le informazioni su come abilitare le connessioni SSL.


## <a name="applications-that-require-certificate-verification-for-ssl-connectivity"></a>Applicazioni che richiedono la verifica del certificato per la connettività SSL
In alcuni casi, le applicazioni richiedono un file del certificato locale generato da un file di certificato (.cer) dell'autorità di certificazione (CA) attendibile per connettersi in modo sicuro. Vedere la procedura seguente per ottenere il file .cer, decodificare il certificato e associarlo all'applicazione.

### <a name="download-the-certificate-file-from-the-certificate-authority-ca"></a>Scaricare il file del certificato dall'autorità di certificazione (CA) 
Il certificato richiesto per comunicare con il Database di Azure per il server PostgreSQL tramite SSL si trova [qui](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt). Scaricare il file del certificato e salvarlo in locale.

### <a name="download-and-install-openssl-on-your-machine"></a>Scaricare e installare OpenSSL sul computer 
Per decodificare il file del certificato richiesto dall'applicazione per connettersi al server di database in modo sicuro, è necessario installare OpenSSL sul computer locale.

#### <a name="for-linux-os-x-or-unix"></a>Per Linux, OS X o Unix
Le librerie OpenSSL vengono distribuite nel codice sorgente direttamente da [OpenSSL Software Foundation](http://www.openssl.org). Le istruzioni seguenti consentono di eseguire i passaggi necessari per installare OpenSSL nel computer Linux. In questo articolo vengono usati i comandi noti per lavorare su Ubuntu 12.04 (e versioni successive).

Aprire una sessione terminal e installare OpenSSL
```bash
wget http://www.openssl.org/source/openssl-1.1.0e.tar.gz
``` 
Estrarre i file dal pacchetto di download
```bash
tar -xvzf openssl-1.1.0e.tar.gz
```
Specificare la directory in cui sono stati estratti i file. Per impostazione predefinita, deve essere come segue.

```bash
cd openssl-1.1.0e
```
Configurare OpenSSL eseguendo il comando indicato di seguito. Se si desidera collocare i file in una cartella diversa da /usr/local/openssl, assicurarsi di modificare quanto segue in modo appropriato.

```bash
./config --prefix=/usr/local/openssl --openssldir=/usr/local/openssl
```
Ora che OpenSSL è stato configurato correttamente, è necessario compilarlo per convertire il certificato. A tale scopo, eseguire il comando seguente:

```bash
make
```
Al termine della compilazione è possibile installare OpenSSL come eseguibile con questo comando:
```bash
make install
```
Per confermare di aver installato correttamente OpenSSL nel sistema, eseguire il comando seguente e assicurarsi di ottenere lo stesso output.

```bash
/usr/local/openssl/bin/openssl version
```
In caso affermativo, si dovrebbe vedere il messaggio seguente.
```bash
OpenSSL 1.1.0e 7 Apr 2014
```

#### <a name="for-windows"></a>Per Windows
L'installazione di OpenSSL in un PC Windows può essere eseguita nei modi seguenti:
1. **(Scelta consigliata)** Usando la funzionalità Bash per Windows disponibile in Windows 10 e versioni successive, OpenSSL viene installato per impostazione predefinita. Le istruzioni su come abilitare la funzionalità Bash per Windows in Windows 10 sono disponibili [qui](https://msdn.microsoft.com/en-us/commandline/wsl/install_guide).
2. Tramite il download di un'applicazione Win32/64 bit fornita dalla community. Pur non offrendo o avallando eventuali programmi di installazione di Windows specifici, OpenSSL Software Foundation offre un elenco dei programmi di installazione disponibili [qui](https://wiki.openssl.org/index.php/Binaries)

### <a name="decode-your-certificate-file"></a>Decodificare il file del certificato
Il file CA radice scaricato è in formato crittografato. Usare OpenSSL per decodificare il file del certificato. Per farlo, eseguire questo comando OpenSSL:

```dos
OpenSSL>x509 -inform DER -in BaltimoreCyberTrustRoot.cer -text -out root.crt
```

### <a name="connecting-to-azure-database-for-postgresql-with-ssl-certificate-authentication"></a>Connessione al Database di Azure per PostgreSQL con autenticazione del certificato SSL
Ora che il certificato è stato decodificato correttamente, è possibile connettersi al server di database in modo protetto tramite SSL. Per consentire la verifica dei certificati server, il certificato deve essere posizionato nel file ~/.postgresql/root.crt nella directory home dell'utente. Su Microsoft Windows il file è denominato % APPDATA%\postgresql\root.crt. Di seguito vengono fornite le istruzioni per connettersi al Database di Azure per PostgreSQL.

> [!NOTE]
> Attualmente si verifica un problema noto se si usa "sslmode = verify-full" nella connessione al servizio, ovvero la connessione avrà esito negativo con un errore indicante che il _certificato del server per "&lt;area&gt;.control.database.windows.net" (e 7 altri nomi) non corrisponde al nome host "&lt;servername&gt;.postgres.database.azure.com"._
> Se è obbligatorio usare la modalità "sslmode=verify-full", usare la convenzione di denominazione server  **&lt;nomeserver&gt;.database.windows.net** come nome host della stringa di connessione. In futuro questa limitazione verrà rimossa. Le connessioni che usano altre [modalità SSL](https://www.postgresql.org/docs/9.6/static/libpq-ssl.html#LIBPQ-SSL-SSLMODE-STATEMENTS) devono continuare a usare la convenzione di denominazione host preferita  **&lt;nomeserver&gt;.postgres.database.azure.com**.

#### <a name="using-psql-command-line-utility"></a>Uso dell'utilità della riga di comando PSQL
L'esempio seguente illustra come connettersi al server PostgreSQL tramite l'utilità della riga di comando PSQL. Usare il file `root.crt` creato e `sslmode=verify-ca` o l'opzione `sslmode=verify-full`.

Usando l'interfaccia della riga di comando di PostgreSQL, eseguire il comando seguente:
```bash
psql "sslmode=verify-ca sslrootcert=root.crt host=mypgserver-20170401.postgres.database.azure.com dbname=postgres user=mylogin@mypgserver-20170401"
```
Se l'operazione va a buon fine, si dovrebbe ottenere l'output seguente:
```bash
Password for user mylogin@mypgserver-20170401:
psql (9.6.2)
WARNING: Console code page (437) differs from Windows code page (1252)
     8-bit characters might not work correctly. See psql reference
     page "Notes for Windows users" for details.
SSL connection (protocol: TLSv1.2, cipher: ECDHE-RSA-AES256-SHA384, bits: 256, compression: off)
Type "help" for help.

postgres=>
```

#### <a name="using-pgadmin-gui-tool"></a>Uso dello strumento GUI pgAdmin
La configurazione di pgAdmin 4 per connettersi in modo sicuro tramite SSL richiede l'impostazione di `SSL mode = Verify-CA` o `SSL mode = Verify-Full` nel modo indicato di seguito:

![Schermata di pgAdmin - connessione - modalità SSL richiesta](./media/concepts-ssl-connection-security/2-pgadmin-ssl.png)

## <a name="next-steps"></a>Passaggi successivi
Esaminare varie opzioni di connettività dell'applicazione secondo [Raccolte connessioni per il Database di Azure per PostgreSQL](concepts-connection-libraries.md)
