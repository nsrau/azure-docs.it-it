---
title: "Configurare la connettività SSL per la connessione sicura a Database di Azure per MySQL | Microsoft Docs"
description: Istruzioni per la configurazione di Database di Azure per MySQL e delle applicazioni associate per usare correttamente le connessioni SSL
services: mysql
author: seanli1988
ms.author: seanli
editor: jasonwhowell
manager: jhubbard
ms.service: mysql-database
ms.topic: article
ms.date: 09/15/2017
ms.openlocfilehash: 38e68712699b3e89a10c3d44d8ec313f531fcbdc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="configure-ssl-connectivity-in-your-application-to-securely-connect-to-azure-database-for-mysql"></a>Configurare la connettività SSL nell'applicazione per la connessione sicura a Database di Azure per MySQL
Database di Azure per il server MySQL supporta la connessione alle applicazioni client tramite Secure Sockets Layer (SSL). L'applicazione delle connessioni SSL tra il server di database e le applicazioni client aiuta a proteggersi dagli attacchi "man in the middle" crittografando il flusso di dati tra il server e l'applicazione.

## <a name="step-1-obtain-ssl-certificate"></a>Passaggio 1: ottenere un certificato SSL
Scaricare il certificato necessario per comunicare tramite SSL con Database di Azure per il server MySQL da [https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) e salvare il file del certificato nel disco locale (in questa esercitazione, abbiamo usato c:\ssl).
**Per Microsoft Internet Explorer e Microsoft Edge:** una volta completato il download, rinominare il certificato in BaltimoreCyberTrustRoot.crt.pem.

## <a name="step-2-bind-ssl"></a>Passaggio 2: eseguire il binding SSL
### <a name="connecting-to-server-using-the-mysql-workbench-over-ssl"></a>Connessione al server con MySQL Workbench tramite SSL
Configurare MySQL Workbench per connettersi in modo sicuro tramite SSL. Passare alla scheda **SSL** nella finestra di dialogo Setup New Connection (Configura nuova connessione) di MySQL Workbench. Immettere il percorso del file **BaltimoreCyberTrustRoot.crt.pem** nel campo **SSL CA File:** (File CA SSL:).
![Salvare un riquadro personalizzato](./media/howto-configure-ssl/mysql-workbench-ssl.png)

### <a name="connecting-to-server-using-the-mysql-cli-over-ssl"></a>Connessione al server con l'interfaccia della riga di comando di MySQL tramite SSL
Usando l'interfaccia della riga di comando di MySQL, eseguire questo comando:
```dos
mysql.exe -h mysqlserver4demo.mysql.database.azure.com -u Username@mysqlserver4demo -p --ssl-ca=c:\ssl\BaltimoreCyberTrustRoot.crt.pem
```

## <a name="step-3--enforcing-ssl-connections-in-azure"></a>Passaggio 3: applicazione delle connessioni SSL in Azure 
### <a name="using-the-azure-portal"></a>Uso del portale di Azure
Tramite il portale di Azure passare all'istanza di Database di Azure per il server MySQL e fare clic su **Sicurezza delle connessioni**. Usare l'interruttore per abilitare o disabilitare l'impostazione **Imponi connessione SSL**, quindi fare clic su **Salva**. È consigliabile abilitare sempre l'impostazione **Imponi connessione SSL** per ottenere un livello di sicurezza avanzato.
![enable-ssl](./media/howto-configure-ssl/enable-ssl.png)

### <a name="using-azure-cli"></a>Utilizzare l'interfaccia della riga di comando di Azure
È possibile abilitare o disabilitare il parametro **ssl-enforcement** usando rispettivamente i valori Enabled o Disabled nell'interfaccia della riga di comando di Azure.
```azurecli-interactive
az mysql server update --resource-group myresource --name mysqlserver4demo --ssl-enforcement Enabled
```

## <a name="step-4-verify-the-ssl-connection"></a>Passaggio 4: verificare la connessione SSL
Eseguire il comando mysql **status** per verificare di essere connessi al server MySQL tramite SSL:
```dos
mysql> status
```
Verificare che la connessione sia crittografata analizzando l'output, che dovrebbe essere il seguente: **SSL: Cipher in use is AES256-SHA** (SSL: la crittografia in uso è AES256-SHA) 

## <a name="sample-code"></a>Codice di esempio
### <a name="php"></a>PHP
```
$conn = mysqli_init();
mysqli_ssl_set($conn,NULL,NULL, "/var/www/html/BaltimoreCyberTrustRoot.crt.pem", NULL, NULL) ; 
mysqli_real_connect($conn, 'myserver4demo.mysql.database.azure.com', 'myadmin@myserver4demo', 'yourpassword', 'quickstartdb', 3306);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}
```
### <a name="python"></a>Python
```
try:
conn = mysql.connector.connect(user='myadmin@myserver4demo',password='yourpassword',database='quickstartdb',host='myserver4demo.mysql.database.azure.com',ssl_ca='/var/www/html/BaltimoreCyberTrustRoot.crt.pem')
except mysql.connector.Error as err:
 print(err)
```

## <a name="next-steps"></a>Passaggi successivi
Esaminare varie opzioni di connettività dell'applicazione in [Raccolte di connessioni per Database di Azure per MySQL](concepts-connection-libraries.md)
