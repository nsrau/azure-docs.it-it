---
title: Creare una VM Linux di SQL Server 2017 in Azure | Microsoft Docs
description: Questa esercitazione illustra come creare una macchina virtuale Linux di SQL Server 2017 nel portale di Azure.
services: virtual-machines-linux
author: rothja
ms.author: jroth
manager: jhubbard
ms.date: 10/25/2017
ms.topic: hero-article
tags: azure-service-management
ms.devlang: na
ms.service: virtual-machines-sql
ms.workload: iaas-sql-server
ms.technology: database-engine
ms.openlocfilehash: 8d714718e4fc27f0abbfede8fe6d0e2693698ec1
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/18/2017
---
# <a name="provision-a-linux-sql-server-virtual-machine-in-the-azure-portal"></a>Effettuare il provisioning di una macchina virtuale Linux di SQL Server nel portale di Azure

> [!div class="op_single_selector"]
> * [Linux](provision-sql-server-linux-virtual-machine.md)
> * [Windows](../../windows/sql/virtual-machines-windows-portal-sql-server-provision.md)

In questa esercitazione introduttiva si usa il portale di Azure per creare una macchina virtuale Linux con SQL Server 2017 installato.

In questa esercitazione si apprenderà come:

* [Creare una VM Linux di SQL dalla raccolta](#create)
* [Connettersi alla nuova VM con ssh](#connect)
* [Modificare la password dell'amministratore di sistema](#password)
* [Configurare connessioni remote](#remote)

## <a name="prerequisites"></a>Prerequisiti

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free) prima di iniziare.

## <a id="create"></a> Creare una VM Linux con SQL Server installato

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. Nel riquadro sinistro fare clic su **Nuovo**.

1. Nel riquadro **Nuovo** fare clic su **Calcolo**.

1. Fare clic su **Visualizza tutto** accanto all'intestazione **In primo piano**.

   ![Visualizzare tutte le immagini di VM](./media/provision-sql-server-linux-virtual-machine/azure-compute-blade.png)

1. Nella casella di ricerca digitare **SQL Server 2017** e premere **INVIO** per avviare la ricerca.

1. Fare clic sull'icona **Filtro**, limitare la ricerca alle immagini **Basato su Linux** e **Microsoft** e quindi fare clic su **Fatto**.

    ![Filtro di ricerca per le immagini di VM di SQL Server 2017](./media/provision-sql-server-linux-virtual-machine/searchfilter.png)

1. Selezionare un'immagine Linux di SQL Server 2017 nei risultati della ricerca. Questa esercitazione usa **Free SQL Server License: SQL Server 2017 Developer on Red Hat Enterprise Linux 7.4** (Licenza gratuita di SQL Server: SQL Server 2017 Developer in Red Hat Enterprise Linux 7.4).

   > [!TIP]
   > L'edizione Developer consente di testare o sviluppare con le funzionalità dell'edizione Enterprise ma senza costi di licenza per SQL Server. Si paga solo il costo dell'esecuzione della VM Linux.

1. Fare clic su **Crea**.

1. Nella finestra **Informazioni di base** immettere i dettagli per la VM Linux. 

    ![Finestra Informazioni di base](./media/provision-sql-server-linux-virtual-machine/basics.png)

    > [!Note]
    > Per l'autenticazione, si può scegliere di usare una chiave pubblica SSH o una password. L'opzione più sicura è SSH. Per istruzioni su come generare una chiave SSH, vedere l'articolo su come [creare chiavi SSH in Linux e Mac per le VM Linux in Azure](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-mac-create-ssh-keys).

1. Fare clic su **OK**.

1. Nella finestra relativa alle **dimensioni** scegliere una dimensione di macchina virtuale. Per visualizzare altre dimensioni, selezionare **Visualizza tutto**. Per altre informazioni sulle dimensioni di VM, vedere [Dimensioni delle macchine virtuali Linux](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-sizes).

    ![Scegliere le dimensioni per la macchina virtuale](./media/provision-sql-server-linux-virtual-machine/vmsizes.png)

   > [!TIP]
   > Per lo sviluppo e i test funzionali, è consigliabile usare **DS2** o una dimensione di VM superiore. Per test delle prestazioni, usare **DS13** o una dimensione superiore.

1. Fare clic su **Seleziona**.

1. Nella finestra **Impostazioni** è possibile apportare modifiche o mantenere le impostazioni predefinite.

1. Fare clic su **OK**.

1. Nella pagina **Riepilogo** fare clic su **Acquista** per creare la VM.

## <a id="connect"></a> Connettersi alla VM Linux

Se si usa già una shell BASH, connettersi alla VM di Azure con il comando **ssh**. Nel comando seguente, sostituire il nome utente e l'indirizzo IP della VM per connettersi alla propria VM Linux.

```bash
ssh azureadmin@40.55.55.555
```

L'indirizzo IP della VM è riportato nel portale di Azure.

![Indirizzo IP nel portale di Azure](./media/provision-sql-server-linux-virtual-machine/vmproperties.png)

Se non si ha una shell BASH, in caso di esecuzione in Windows, è possibile installare un client SSH come PuTTY.

1. [Scaricare e installare PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

1. Eseguire PuTTY.

1. Nella schermata di configurazione di PuTTY immettere l'indirizzo IP pubblico della VM.

1. Fare clic su Open (Apri) e immettere nome utente e password quando richiesto.

Per altre informazioni sulla connessione alle VM Linux, vedere l'articolo su come [creare una VM Linux in Azure con il portale](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-quick-create-portal#ssh-to-the-vm).

## <a id="password"></a> Modificare la password dell'amministratore di sistema

La nuova macchina virtuale installa SQL Server con una password dell'amministratore di sistema casuale. Per potersi connettere a SQL Server con l'account di accesso SA, è prima necessario reimpostare questa password.

1. Dopo la connessione alla VM Linux, aprire un nuovo terminale di comando.

1. Modificare la password dell'amministratore di sistema con i comandi seguenti:

   ```bash
   sudo systemctl stop mssql-server
   sudo /opt/mssql/bin/mssql-conf set-sa-password
   ```

   Immettere una nuova password dell'amministratore di sistema e la conferma della password quando richiesto.

1. Riavviare il servizio SQL Server.

   ```bash
   sudo systemctl start mssql-server
   ```

## <a name="add-the-tools-to-your-path-optional"></a>Aggiungere gli strumenti al percorso (facoltativo)

Per impostazione predefinita vengono installati diversi [pacchetti](sql-server-linux-virtual-machines-overview.md#packages) di SQL Server, incluso il pacchetto degli strumenti da riga di comando di SQL Server. Questo pacchetto contiene gli strumenti **sqlcmd** e **bcp**. Per praticità, si può facoltativamente aggiungere il percorso degli strumenti, `/opt/mssql-tools/bin/`, alla variabile di ambiente **PATH**.

1. Per modificare **PATH** sia per le sessioni di accesso che per le sessioni interattive/senza accesso, eseguire questi comandi:

   ```bash
   echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bash_profile
   echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bashrc
   source ~/.bashrc
   ```

## <a id="remote"></a> Configurare connessioni remote

Se è necessario connettersi in remoto a SQL Server nella VM di Azure, è necessario configurare una regola in ingresso nel gruppo di sicurezza di rete. La regola consentirà il traffico sulla porta su cui è in ascolto SQL Server (per impostazione predefinita, la porta 1433). La procedura seguente illustra come usare il portale di Azure per questo passaggio. 

1. Nel portale selezionare **Macchine virtuali**e quindi la propria macchina virtuale di SQL Server.

1. Nell'elenco delle proprietà selezionare **Rete**.

1. Nella finestra **Rete** fare clic su **Aggiungi** in **Regole porta in ingresso**.

   ![Regole porta in ingresso](./media/provision-sql-server-linux-virtual-machine/networking.png)

1. Nell'elenco **Servizio** selezionare **MS SQL**.

    ![Regola del gruppo di sicurezza per MS SQL](./media/provision-sql-server-linux-virtual-machine/sqlnsgrule.png)

1. Fare clic su **OK** per salvare la regola creata per la macchina virtuale.

### <a name="open-the-firewall-on-rhel"></a>Aprire il firewall in RHEL

Questa esercitazione ha descritto come creare una VM Red Hat Enterprise Linux (RHEL). Per connettersi in remoto alle VM RHEL, è necessario anche aprire la porta 1433 nel firewall Linux.

1. [Connettersi](#connect) alla VM RHEL.

1. Nella shell BASH eseguire questi comandi:

   ```bash
   sudo firewall-cmd --zone=public --add-port=1433/tcp --permanent
   sudo firewall-cmd --reload
   ```

## <a name="next-steps"></a>Passaggi successivi

Ora che è disponibile una macchina virtuale di SQL Server 2017 in Azure, è possibile connettersi in locale con **sqlcmd** per eseguire query Transact-SQL.

Se la VM di Azure è stata configurata per le connessioni remote a SQL Server, sarà anche possibile connettersi in remoto. Per un esempio di come connettersi in remoto a SQL Server in Linux da Windows, vedere l'articolo su come [usare SSMS in Windows per connettersi a SQL Server in Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-develop-use-ssms). Per connettersi con Visual Studio Code, vedere [Usare Visual Studio Code per creare ed eseguire script Transact-SQL per SQL Server](https://docs.microsoft.com/sql/linux/sql-server-linux-develop-use-vscode).

Per altre informazioni generali su SQL Server in Linux, vedere la [panoramica di SQL Server 2017 in Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-overview). Per altre informazioni sull'uso di macchine virtuali Linux di SQL Server 2017, vedere la [panoramica delle macchine virtuali di SQL Server 2017 in Azure](sql-server-linux-virtual-machines-overview.md).
