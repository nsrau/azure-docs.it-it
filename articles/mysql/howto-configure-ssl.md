---
title: "Configurare la connettività SSL nell&quot;applicazione per la connessione sicura a Database di Azure per MySQL | Microsoft Docs"
description: Istruzioni per la configurazione di Database di Azure per MySQL e delle applicazioni associate per usare correttamente le connessioni SSL
services: mysql
author: JasonMAnderson
ms.author: janders
editor: jasonh
manager: jhubbard
ms.assetid: 
ms.service: mysql-database
ms.tgt_pltfrm: portal
ms.devlang: na
ms.topic: article
ms.date: 05/18/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 44eac1ae8676912bc0eb461e7e38569432ad3393
ms.openlocfilehash: 801806056b745be5663c0a10241795947d1dd036
ms.contentlocale: it-it
ms.lasthandoff: 05/17/2017

---

# <a name="configure-ssl-connectivity-in-your-application-to-securely-connect-to-azure-database-for-mysql"></a>Configurare la connettività SSL nell'applicazione per la connessione sicura a Database di Azure per MySQL
Database di Azure per il server MySQL supporta la connessione alle applicazioni client tramite Secure Sockets Layer (SSL). L'applicazione delle connessioni SSL tra il server di database e le applicazioni client aiuta a proteggersi dagli attacchi "man in the middle" crittografando il flusso di dati tra il server e l'applicazione.

Per impostazione predefinita, il servizio di database deve essere configurato per richiedere le connessioni SSL quando ci si connette a Database di Azure per il server MySQL.  È consigliabile evitare di disabilitare l'opzione SSL, quando possibile. 

## <a name="enforcing-ssl-connections"></a>Applicazione delle connessioni SSL
Per impostazione predefinita, l'applicazione delle connessioni SSL è abilitata quando si esegue il provisioning di una nuova un'istanza di Database di Azure per il server MySQL attraverso l'interfaccia della riga di comando e il portale di Azure. 

Analogamente, le stringhe di connessione predefinite nelle impostazioni "Stringhe di connessione" per il server nel portale di Azure includono i parametri obbligatori per i linguaggi comuni per la connessione al server di database tramite SSL. Il parametro SSL varia in base al connettore, ad esempio "ssl=true", "sslmode=require" oppure "sslmode=required" e altre varianti.

## <a name="configure-enforcement-of-ssl"></a>Configurare l'applicazione di SSL
È possibile disabilitare o abilitare l'applicazione di SSL. Microsoft Azure consiglia di abilitare sempre l'impostazione Imponi connessione SSL per maggiore sicurezza.

### <a name="using-azure-portal"></a>Uso del portale di Azure
Usando il portale di Azure, passare all'istanza di Database di Azure per il server MySQL e fare clic su **Sicurezza delle connessioni**. Usare l'interruttore per abilitare o disabilitare l'impostazione **Imponi connessione SSL**. Fare quindi clic su **Salva**. Microsoft consiglia di abilitare sempre l'impostazione **Imponi connessione SSL** per maggiore sicurezza. 
![enable-ssl](./media/howto-configure-ssl/enable-ssl.png)

È possibile confermare l'impostazione aprendo la pagina **Panoramica** per visualizzare l'indicatore **Stato imposizione SSL**.

### <a name="using-azure-cli"></a>Utilizzare l'interfaccia della riga di comando di Azure
È possibile abilitare o disabilitare il parametro **ssl-enforcement** usando rispettivamente i valori Enabled o Disabled nell'interfaccia della riga di comando di Azure.
```azurecli
az mysql server update --resource-group myresource --name mysqlserver4demo --ssl-enforcement Enabled
```
## <a name="ensure-your-application-or-framework-supports-ssl-connections"></a>Verificare che le connessioni SSL siano supportate dall'applicazione o dal framework
Per impostazione predefinita, molte applicazioni comuni che usano i servizi di database MySQL, quali Wordpress, Drupal e Magento, non abilitano SSL durante l'installazione.  L'abilitazione della connettività SSL deve essere eseguita dopo l'installazione o tramite i comandi dell'interfaccia della riga di comando specifici dell'applicazione.  Se il server MySQL applica le connessioni SSL e l'applicazione associata non è configurata correttamente, l'applicazione potrebbe non riuscire a connettersi al server di database.  Consultare la documentazione dell'applicazione per informazioni su come abilitare le connessioni SSL.

## <a name="applications-that-require-a-local-certificate-for-ssl-connectivity"></a>Applicazioni che richiedono un certificato locale per la connettività SSL
In alcuni casi, le applicazioni richiedono un file di certificato locale (file PEM) generato da un file di certificato (file CER) dell'autorità di certificazione (CA) per connettersi in modo sicuro.  Vedere la procedura seguente per ottenere il file CER, generare il file PEM locale e associarlo all'applicazione.

### <a name="download-the-certificate-file-from-the-certificate-authority-ca"></a>Scaricare il file del certificato dall'autorità di certificazione (CA) 
Il certificato richiesto per comunicare con Database di Azure per il server MySQL tramite SSL è disponibile [qui](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt).  Scaricare il file del certificato nel disco locale. In questa esercitazione si userà **c:\ssl**.

### <a name="download-and-install-openssl-on-your-pc"></a>Scaricare e installare OpenSSL nel computer 
Per generare il file **PEM** locale richiesto dall'applicazione per connettersi al server di database in modo sicuro, è necessario installare OpenSSL nel computer locale.  

Le sezioni seguenti descrivono l'approccio che è possibile usare, da un PC Linux o Windows a seconda del sistema operativo preferito, ed è necessario seguire un solo approccio.

### <a name="linux-users---download-and-install-openssl-using-a-linux-pc"></a>Utenti Linux: scaricare e installare OpenSSL usando un computer Linux
Le librerie OpenSSL vengono distribuite nel codice sorgente direttamente da [OpenSSL Software Foundation](http://www.openssl.org).  Le istruzioni seguenti consentono di eseguire i passaggi necessari per installare OpenSSL nel computer Linux.  In questa guida verranno illustrati i comandi per Ubuntu 12.04 e versioni successive.

Aprire una sessione terminal e installare OpenSSL
```bash
wget http://www.openssl.org/source/openssl-1.1.0e.tar.gz
```  
Estrarre i file dal pacchetto di download
```bash
tar -xvzf openssl-1.1.0e.tar.gz
```
Specificare la directory in cui sono stati estratti i file.  Per impostazione predefinita sarà la directory seguente.

```bash
cd openssl-1.1.0e
```
Configurare OpenSSL eseguendo questo comando. Per salvare i file in una cartella diversa da /usr/local/openssl, modificare quanto segue secondo le esigenze.

```bash
./config --prefix=/usr/local/openssl --openssldir=/usr/local/openssl
```
Ora che OpenSSL è stato configurato correttamente, è necessario compilarlo per convertire il certificato.  A tale scopo, eseguire questo comando:

```bash
make
```
Al termine della compilazione è possibile installare OpenSSL come eseguibile con questo comando:
```bash
make install
```
Per verificare di aver installato correttamente OpenSSL nel sistema, eseguire questo comando e assicurarsi di ottenere lo stesso output.

```bash
/usr/local/openssl/bin/openssl version
```
In caso affermativo, si dovrebbe vedere il messaggio seguente:
```bash
OpenSSL 1.1.0e 7 Apr 2014
```

### <a name="windows-pc-users---download-and-install-openssl-using-a-windows-pc"></a>Utenti Windows: scaricare e installare OpenSSL usando un computer Windows
Per generare il file **PEM** locale richiesto dall'applicazione per connettersi al server di database in modo sicuro, è necessario installare OpenSSL nel computer locale.

Le librerie OpenSSL vengono distribuite nel codice sorgente direttamente da [OpenSSL Software Foundation](http://www.openssl.org). Le istruzioni seguenti consentono di eseguire i passaggi necessari per installare OpenSSL nel computer Linux.

L'installazione di OpenSSL in un PC Windows può essere eseguita nei modi seguenti:

1. **(Scelta consigliata)** Usando la funzionalità Bash per Windows disponibile in Windows 10 e versioni successive, OpenSSL viene installato per impostazione predefinita.  Le istruzioni su come abilitare la funzionalità Bash per Windows in Windows 10 sono disponibili [qui](https://msdn.microsoft.com/commandline/wsl/install_guide).

2. Tramite il download di un'applicazione Win32/64 bit fornita dalla community. Pur non fornendo o avallando eventuali programmi di installazione di Windows specifici, OpenSSL Software Foundation offre un elenco dei programmi di installazione disponibili [qui](https://wiki.openssl.org/index.php/Binaries)

### <a name="convert-your-cer-certificate-to-a-local-pem"></a>Convertire il certificato CER in un certificato locale PEM
Il file CA radice scaricato è in formato **CER**. È necessario usare OpenSSL per convertire il file di certificato in un file **PEM**.  A tale scopo, con lo strumento da riga di comando openssl.exe eseguire questo comando: 

```dos
OpenSSL>x509 -inform DER -in BaltimoreCyberTrustRoot.cer -out MyServerCACert.pem
```
Ora che è stato creato il file di certificato MyServerCACert.pem, è possibile connettersi al server di database in modo sicuro tramite SSL. 

Gli esempi seguenti illustrano come connettersi al server MySQL tramite l'interfaccia della riga di comando di MySQL e MySQL Workbench usando il file **MyServerCACert.pem**.

### <a name="connecting-to-server-using-the-mysql-cli-over-ssl"></a>Connessione al server con l'interfaccia della riga di comando di MySQL tramite SSL
Usando l'interfaccia della riga di comando di MySQL, eseguire questo comando:

```dos
mysql.exe -h mysqlserver4demo.mysql.database.azure.com -uUsername@mysqlserver4demo -pYourPassword --ssl-ca=c:\ssl\MyServerCACert.pem
```
Eseguire il comando mysql **status** per verificare di essere connessi al server MySQL tramite SSL:

```dos
mysql> status
--------------
mysql.exe  Ver 14.14 Distrib 5.7.18, for Win64 (x86_64)

Connection id:          65535
Current database:
Current user:           jason@66.235.55.141
SSL:                    Cipher in use is AES256-SHA
Using delimiter:        ;
Server version:         5.6.26.0 MySQL Community Server (GPL)
Protocol version:       10
Connection:             janderserver.mysql.sqltest-eg1.mscds.com via TCP/IP
Server characterset:    latin1
Db     characterset:    latin1
Client characterset:    cp850
Conn.  characterset:    cp850
TCP port:               3306
Uptime:                 1 day 19 hours 9 min 43 sec

Threads: 4  Questions: 26082  Slow queries: 0  Opens: 112  Flush tables: 1  Open tables: 105  Queries per second avg: 0.167
--------------
```

> [!NOTE]
> Attualmente esiste un problema noto: se si usa l'opzione "--ssl-mode=VERIFY_IDENTITY" nella connessione di mysql.exe al servizio, la connessione non riesce e mostra l'errore seguente: _ERROR 2026 (HY000): Errore di connessione SSL: Errore nella convalida del certificato SSL_ Eseguire il downgrade a "--ssl-mode=VERIFY_CA" o a [modalità SSL](https://dev.mysql.com/doc/refman/5.7/en/secure-connection-options.html#option_general_ssl-mode) inferiori. Se è necessario usare "--ssl-mode=VERIFY_IDENTITY", è possibile eseguire un ping al nome del server per risolvere il nome del server di area, ad esempio westeurope1-a.control.database.windows.net, e usare il nome del server di area nella connessione fino a quando il problema viene risolto. In futuro questa limitazione verrà rimossa. 

### <a name="connecting-to-server-using-the-mysql-workbench-over-ssl"></a>Connessione al server con MySQL Workbench tramite SSL
Per la configurazione di MySQL Workbench per connettersi in modo sicuro tramite SSL è necessario passare alla scheda **SSL** nella finestra di dialogo Setup New Connection (Configura nuova connessione) di MySQL Workbench, quindi immettere il percorso del file **MyServerCACert.pem** nel campo **SSL CA File** (File CA SSL).
![Salvare un riquadro personalizzato](./media/concepts-ssl-connection-security/mysql-workbench-ssl.png)

## <a name="next-steps"></a>Passaggi successivi
- Esaminare varie opzioni di connettività dell'applicazione in [Raccolte di connessioni per Database di Azure per MySQL](concepts-connection-libraries.md)

