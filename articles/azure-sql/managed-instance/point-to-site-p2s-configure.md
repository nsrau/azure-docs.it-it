---
title: Configurare la connettività da punto a sito usando SSMS
titleSuffix: Azure SQL Managed Instance
description: Connettersi a Istanza gestita di SQL di Azure usando SQL Server Management Studio (SSMS) e una connessione da punto a sito da un computer client locale.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, jovanpop
ms.date: 03/13/2019
ms.openlocfilehash: 3baf2d7ed7c326895ae40948fc2d0a4cc03021f9
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92788367"
---
# <a name="quickstart-configure-a-point-to-site-connection-to-azure-sql-managed-instance-from-on-premises"></a>Avvio rapido: Configurare una connessione da punto a sito a Istanza gestita di SQL di Azure da un computer locale
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Questo argomento di avvio rapido illustra come connettersi a Istanza gestita di SQL di Azure con [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms) (SSMS) da un computer client locale tramite una connessione da punto a sito. Per informazioni sulle connessioni da punto a sito, vedere [Informazioni sulla VPN da punto a sito](../../vpn-gateway/point-to-site-about.md).

## <a name="prerequisites"></a>Prerequisiti

La guida introduttiva:

- Usa le risorse create in [Creare un'istanza gestita](instance-create-quickstart.md) come punto iniziale.
- Richiede PowerShell 5.1 e Azure PowerShell 1.4.0 o versione successiva nel computer client locale. Se necessario, vedere le istruzioni per l'[installazione del modulo Azure PowerShell](/powershell/azure/install-az-ps#install-the-azure-powershell-module).
- Richiede la versione più recente di [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms) nel computer client locale.

## <a name="attach-a-vpn-gateway-to-a-virtual-network"></a>Collegare un gateway VPN a una rete virtuale

1. Aprire PowerShell nel computer client locale.

2. Copiare questo script di PowerShell. Questo script collega un gateway VPN alla rete virtuale di Istanza gestita di SQL che è stata creata nell'argomento di avvio rapido [Creare un'istanza gestita](instance-create-quickstart.md). Questo script usa il modulo Az di Azure PowerShell ed effettua le operazioni seguenti per gli host basati su Windows o Linux:

   - Crea e installa i certificati in un computer client
   - Calcola l'intervallo IP della subnet del futuro gateway VPN
   - Crea la subnet del gateway
   - Distribuisce il modello di Azure Resource Manager che collega il gateway VPN alla subnet VPN

     ```powershell
     $scriptUrlBase = 'https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/manage/azure-sql-db-managed-instance/attach-vpn-gateway'

     $parameters = @{
       subscriptionId = '<subscriptionId>'
       resourceGroupName = '<resourceGroupName>'
       virtualNetworkName = '<virtualNetworkName>'
       certificateNamePrefix  = '<certificateNamePrefix>'
       }

     Invoke-Command -ScriptBlock ([Scriptblock]::Create((iwr ($scriptUrlBase+'/attachVPNGateway.ps1?t='+ [DateTime]::Now.Ticks)).Content)) -ArgumentList $parameters, $scriptUrlBase
     ```

3. Incollare lo script nella finestra di PowerShell e fornire i parametri obbligatori. I valori per `<subscriptionId>`, `<resourceGroup>` e `<virtualNetworkName>` devono corrispondere a quelli usati per l'argomento di avvio rapido [Creare un'istanza gestita](instance-create-quickstart.md). Il valore per `<certificateNamePrefix>` può essere una stringa di propria scelta.

4. Eseguire lo script di PowerShell.

> [!IMPORTANT]
> Non continuare fino al completamento dello script di PowerShell.

## <a name="create-a-vpn-connection"></a>Creare una connessione a VPN

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Aprire il gruppo di risorse in cui è stato creato il gateway di rete virtuale e quindi aprire la risorsa del gateway di rete virtuale.
3. Selezionare **Configurazione da punto a sito** e quindi selezionare **Scarica client VPN** .

    ![Scaricare il client VPN](./media/point-to-site-p2s-configure/download-vpn-client.png)  
4. Nel computer client locale, estrarre i file dal file ZIP e quindi aprire la cartella con i file estratti.
5. Aprire la cartella **WindowsAmd64** e aprire il file **VpnClientSetupAmd64.exe** .
6. Se viene visualizzato un messaggio per segnalare che **il PC è stato protetto da Windows** , fare clic su **Altre informazioni** e quindi su **Esegui comunque** .

    ![Installare il client VPN](./media/point-to-site-p2s-configure/vpn-client-defender.png)
7. Nella finestra di dialogo Controllo account utente fare clic su **Sì** per continuare.
8. Nella finestra di dialogo che fa riferimento alla rete virtuale, selezionare **Sì** per installare il client VPN per la rete virtuale.

## <a name="connect-to-the-vpn-connection"></a>Connettersi alla VPN

1. Passare a **VPN** in **Rete e Internet** nel computer client locale e selezionare la rete virtuale di Istanza gestita di SQL per stabilire una connessione a questa rete virtuale. Nell'immagine seguente, la rete virtuale è denominata **MyNewVNet** .

    ![Connessione VPN](./media/point-to-site-p2s-configure/vpn-connection.png)  
2. Selezionare **Connetti** .
3. Nella finestra di dialogo, selezionare **Connessione** .

    ![Screenshot con il pulsante Connetti evidenziato.](./media/point-to-site-p2s-configure/vpn-connection2.png)  
4. Quando viene segnalato che Gestione connessioni richiede privilegi elevati per aggiornare la tabella di route, scegliere **Continua** .
5. Selezionare **Sì** nella finestra di dialogo Controllo dell'account utente per continuare.

   È stata stabilita una connessione VPN alla rete virtuale di Istanza gestita di SQL.

    ![Screenshot con il messaggio Connesso evidenziato quando si stabilisce la nuova connessione.](./media/point-to-site-p2s-configure/vpn-connection-succeeded.png)  

## <a name="connect-with-ssms"></a>Connettersi a SSMS

1. Nel computer client locale aprire SQL Server Management Studio.
2. Nella finestra di dialogo **Connetti al server** immettere il **nome host** completo per l'istanza gestita nella casella **Nome server** .
3. Selezionare **Autenticazione di SQL Server** , specificare il nome utente e la password e quindi selezionare **Connetti** .

    ![Connessione a SSMS](./media/point-to-site-p2s-configure/ssms-connect.png)  

Dopo la connessione, è possibile visualizzare i database di sistema e utente nel nodo database. È inoltre possibile visualizzare diversi oggetti nei nodi di sicurezza, oggetti server, replica, gestione, SQL Server Agent e Profiler XEvent.

## <a name="next-steps"></a>Passaggi successivi

- Per una guida introduttiva che illustra come connettersi da una macchina virtuale di Azure, vedere [Configurare una connessione da punto a sito](point-to-site-p2s-configure.md).
- Per una panoramica delle opzioni di connessione per le applicazioni, vedere [Connettere le applicazioni a Istanza gestita di SQL](connect-application-instance.md).
- Per ripristinare un database SQL Server esistente dal sistema locale a un'istanza gestita, è possibile usare il [Servizio Migrazione del database di Azure per la migrazione](../../dms/tutorial-sql-server-to-managed-instance.md) o il [comando T-SQL RESTORE](restore-sample-database-quickstart.md) per il ripristino da un file di backup del database.