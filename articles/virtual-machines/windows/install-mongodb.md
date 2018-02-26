---
title: Installare MongoDB in una VM Windows in Azure | Documentazione Microsoft
description: Informazioni su come installare MongoDB in una VM di Azure creata con il modello di distribuzione Resource Manager che esegue Windows Server 2012 R2.
services: virtual-machines-windows
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
ms.assetid: 53faf630-8da5-4955-8d0b-6e829bf30cba
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: iainfou
ms.openlocfilehash: f3fe9751467a1fc34f4e9d02855c4aff307424a3
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/16/2017
---
# <a name="install-and-configure-mongodb-on-a-windows-vm-in-azure"></a>Installare e configurare MongoDB in una VM Windows in Azure
[MongoDB](http://www.mongodb.org) è un diffuso database NoSQL open source a prestazioni elevate. Questo articolo illustra la procedura di installazione e configurazione di MongoDB in una macchina virtuale Windows Server 2016 in Azure. È anche possibile [installare MongoDB in una VM Linux in Azure](../linux/install-mongodb.md).

## <a name="prerequisites"></a>prerequisiti
Prima di installare e configurare MongoDB, è necessario creare una VM e, idealmente, aggiungere un disco dati. Vedere gli articoli seguenti per creare una VM e aggiungere un disco dati:

* Creare una macchina virtuale Windows Server usando [il portale di Azure](quick-create-portal.md) o [Azure PowerShell](quick-create-powershell.md).
* Collegare un disco dati a una macchina virtuale Windows Server usando [il portale di Azure](attach-managed-disk-portal.md) o [Azure PowerShell](attach-disk-ps.md).

Per iniziare a installare e configurare MongoDB, [accedere a una VM Windows Server](connect-logon.md) tramite Desktop remoto.

## <a name="install-mongodb"></a>Installare MongoDB
> [!IMPORTANT]
> Le funzionalità di sicurezza MongoDB, ad esempio l'autenticazione e l'associazione di indirizzi IP, non sono abilitate per impostazione predefinita. Dovranno essere abilitate prima di distribuire MongoDB in un ambiente di produzione. Per altre informazioni, vedere [MongoDB Security and Authentication](http://www.mongodb.org/display/DOCS/Security+and+Authentication) (Sicurezza e autenticazione di MongoDB).


1. Dopo avere eseguito la connessione alla macchina virtuale tramite Desktop remoto, aprire Internet Explorer dalla barra delle applicazioni.
2. All'apertura di Internet Explorer, selezionare **Usa impostazioni di sicurezza, privacy e compatibilità consigliate** e fare clic su **OK**.
3. La configurazione di protezione avanzata di Internet Explorer è abilitata per impostazione predefinita. Aggiungere il sito Web di MongoDB all'elenco dei siti consentiti:
   
   * Nell'angolo superiore destro fare clic sull'icona **Strumenti**.
   * In **Opzioni Internet** selezionare la scheda **Sicurezza**, quindi l'icona **Siti attendibili**.
   * Fare clic sul pulsante **Siti**. Aggiungere *https://\*.mongodb.com* all'elenco dei siti attendibili, quindi chiudere la finestra di dialogo.
     
     ![Configurare le impostazioni di sicurezza di Internet Explorer](./media/install-mongodb/configure-internet-explorer-security.png)
4. Accedere alla pagina [MongoDB - Download](http://www.mongodb.com/downloads) (http://www.mongodb.com/downloads).
5. Se necessario, selezionare l'edizione **Community Server** e quindi l'ultima versione stabile corrente per *Windows Server 2008 R2 a 64 bit e versioni successive*. Per scaricare il programma di installazione, fare clic su **DOWNLOAD (msi)**.
   
    ![Scaricare il programma di installazione di MongoDB](./media/install-mongodb/download-mongodb.png)
   
    Eseguire il programma di installazione al termine del download.
6. Leggere e accettare il contratto di licenza. Quando richiesto, selezionare **Completa** per l'installazione.
7. Se lo si desidera, è possibile scegliere anche di installare Compass, un'interfaccia grafica per MongoDB.
8. Nella schermata finale fare clic su **Installa**.

## <a name="configure-the-vm-and-mongodb"></a>Configurare la VM e MongoDB
1. Le variabili di percorso non vengono aggiornate dal programma di installazione di MongoDB. Senza il percorso `bin` di MongoDB nella variabile di percorso, è necessario specificare il percorso completo ogni volta che si usa un file eseguibile di MongoDB. Per aggiungere il percorso alla variabile:
   
   * Fare clic con il pulsante destro del mouse sul menu **Start** e selezionare **Sistema**.
   * Fare clic sulla scheda **Impostazioni di sistema avanzate**, quindi su **Variabili d'ambiente**.
   * In **Variabili di sistema** selezionare **Percorso**, quindi fare clic su **Modifica**.
     
     ![Configurare le variabili di PERCORSO](./media/install-mongodb/configure-path-variables.png)
     
     Aggiungere il percorso alla cartella `bin` di MongoDB. MongoDB viene in genere installato in *C:\Programmi\MongoDB*. Verificare il percorso di installazione nella VM. Nell'esempio seguente viene aggiunto il percorso di installazione predefinito di MongoDB alla variabile `PATH`:
     
     ```
     ;C:\Program Files\MongoDB\Server\3.6\bin
     ```
     
     > [!NOTE]
     > Assicurarsi di aggiungere il punto e virgola iniziale (`;`) per indicare che si sta aggiungendo un percorso alla variabile `PATH`.

2. Creare le directory di log e dati di MongoDB nel disco dati. Nel menu **Start** selezionare **Prompt dei comandi**. Nell'esempio seguente vengono create le directory nell'unità F:
   
    ```
    mkdir F:\MongoData
    mkdir F:\MongoLogs
    ```
3. Avviare un'istanza di MongoDB con il comando seguente, modificando di conseguenza il percorso alle directory di log e dati:
   
    ```
    mongod --dbpath F:\MongoData\ --logpath F:\MongoLogs\mongolog.log
    ```
   
    Possono essere necessari diversi minuti per l'allocazione dei file journal di MongoDB e l'inizio dell'attesa delle connessioni. Tutti i messaggi di log vengono indirizzati al file *F:\MongoLogs\mongolog.log* non appena il server `mongod.exe` viene avviato e vengono allocati i file journal.
   
   > [!NOTE]
   > Il prompt dei comandi continua con questa attività durante l'esecuzione dell'istanza di MongoDB. Lasciare aperta la finestra del prompt dei comandi per continuare l'esecuzione di MongoDB. In alternativa, installare MongoDB come servizio, come descritto nei dettagli nel passaggio successivo.

4. Per un'esperienza più affidabile con MongoDB, installare `mongod.exe` come servizio. Cerare un servizio significa che non è necessario lasciare in esecuzione un prompt dei comandi ogni volta che si desidera usare MongoDB. Creare il servizio come indicato di seguito, modificando di conseguenza il percorso alle directory di log e dati:
   
    ```
    mongod --dbpath F:\MongoData\ --logpath F:\MongoLogs\mongolog.log --logappend  --install
    ```
   
    Il comando precedente crea un servizio chiamato MongoDB con la descrizione "Mongo DB". Vengono specificati anche i parametri seguenti:
   
   * L'opzione `--dbpath` specifica il percorso della directory dei dati.
   * Per specificare un file di log è necessario usare l'opzione `--logpath`, perché il servizio in esecuzione non ha una finestra di comando in cui visualizzare l'output.
   * L'opzione `--logappend` specifica che a seguito di un riavvio del servizio l'output viene aggiunto al file di log esistente.
   
   Per avviare il servizio MongoDB, eseguire il comando seguente:
   
    ```
    net start MongoDB
    ```
   
    Per altre informazioni sulla creazione del servizio di MongoDB, vedere [Configure a Windows Service for MongoDB](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/#mongodb-as-a-windows-service) (Configurare un servizio Windows per MongoDB).

## <a name="test-the-mongodb-instance"></a>Testare l'istanza di MongoDB
Con MongoDB in esecuzione come istanza singola o installato come servizio, è possibile avviare la creazione e l'uso dei database. Per avviare la shell di amministrazione di MongoDB, aprire un'altra finestra del prompt dei comandi dal menu **Start** e immettere il comando seguente:

```
mongo  
```

È possibile elencare i database con il comando `db`. Inserire alcuni dati come mostrato di seguito:

```
db.foo.insert( { a : 1 } )
```

Cercare i dati come mostrato di seguito:

```
db.foo.find()
```

L'output è simile all'esempio seguente:

```
{ "_id" : "ObjectId("57f6a86cee873a6232d74842"), "a" : 1 }
```

Uscire dalla console `mongo` come mostrato di seguito:

```
exit
```

## <a name="configure-firewall-and-network-security-group-rules"></a>Configurare le regole del firewall e del gruppo di sicurezza di rete
Ora che MongoDB è stato installato ed è in esecuzione, aprire una porta in Windows Firewall per poter eseguire la connessione remota a MongoDB. Per creare una nuova regola in ingresso per consentire la porta TCP 27017, aprire un prompt amministrativo di PowerShell e immettere il comando seguente:

```powerahell
New-NetFirewallRule `
    -DisplayName "Allow MongoDB" `
    -Direction Inbound `
    -Protocol TCP `
    -LocalPort 27017 `
    -Action Allow
```

È anche possibile creare la regola usando lo strumento grafico di gestione **Windows Firewall con sicurezza avanzata**. Creare una nuova regola in ingresso per consentire la porta TCP 27017.

Se necessario, creare una regola del gruppo di sicurezza di rete per consentire l'accesso a MongoDB all'esterno della subnet di rete virtuale di Azure esistente. È possibile creare le regole del gruppo di sicurezza di rete tramite il [portale di Azure](nsg-quickstart-portal.md) o [Azure PowerShell](nsg-quickstart-powershell.md). Come con le regole di Windows Firewall, consentire la porta TCP 27017 per l'interfaccia di rete virtuale della VM di MongoDB.

> [!NOTE]
> La porta TPC 27017 è la porta predefinita usata da MongoDB. È possibile modificare la porta usando il parametro `--port` quando `mongod.exe` viene avviato manualmente o da un servizio. Se si modifica la porta, assicurarsi di aggiornare le regole di Windows Firewall e del gruppo di sicurezza di rete nei passaggi precedenti.


## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione è stato illustrato come installare e configurare MongoDB nella VM Windows. È ora possibile accedere a MongoDB nella VM basata su Windows, seguendo gli argomenti avanzati illustrati nella [documentazione di MongoDB](https://docs.mongodb.com/manual/).

