---
title: Installazione dei processi di database elastici | Documentazione Microsoft
description: Installazione dettagliata della funzionalità dei processi elastici.
services: sql-database
manager: craigg
author: ddove
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 10/24/2016
ms.author: sstein
ms.openlocfilehash: 7e5258e0ccb88c8e2f3fe3277f69444ae5424cd0
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
# <a name="installing-elastic-database-jobs-overview"></a>Installazione dei processi di database elastici (panoramica)
I [**processi di database elastico**](sql-database-elastic-jobs-overview.md) possono essere installati tramite PowerShell o tramite il portale di Azure. È possibile ottenere l'accesso per creare e gestire i processi tramite l'API di PowerShell solo se si installa il pacchetto di PowerShell. Inoltre, le API PowerShell forniscono molte più funzionalità rispetto al portale in questo momento.

Se sono già stati installati i **processi di database elastici** tramite il portale da un **pool elastico**, l'ultima anteprima di Powershell include gli script per aggiornare l'installazione esistente. È consigliabile aggiornare l'installazione alla versione più recente dei componenti dei **Processi di database elastici** per trarre vantaggio dalle nuove funzionalità esposte tramite le API di PowerShell.

## <a name="prerequisites"></a>prerequisiti
* Una sottoscrizione di Azure. Per una versione di valutazione gratuita, vedere [Versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/).
* Azure PowerShell. Installare la versione più recente tramite l’ [installazione guidata piattaforma Web](http://go.microsoft.com/fwlink/p/?linkid=320376). Per informazioni dettagliate, vedere [Come installare e configurare Azure PowerShell](/powershell/azure/overview).
* [Utilità della riga di comando NuGet](https://nuget.org/nuget.exe) viene utilizzata per installare il pacchetto dei processi di database elastici. Per altre informazioni, vedere http://docs.nuget.org/docs/start-here/installing-nuget.

## <a name="download-and-import-the-elastic-database-jobs-powershell-package"></a>Scaricare e importare il pacchetto di PowerShell dei processi di database elastici
1. Avviare la finestra di comando Microsoft Azure PowerShell e passare alla directory in cui è stato scaricata l’utilità della riga di comando NuGet (nuget.exe).
2. Scaricare e importare il pacchetto **Processi di database elastici** nella directory corrente con il comando seguente:
   
        PS C:\>.\nuget install Microsoft.Azure.SqlDatabase.Jobs -prerelease
   
    I file **Processi di database elastici**vengono inseriti in una directory locale in una cartella denominata **Microsoft.Azure.SqlDatabase.Processi.x.x.xxxx.x** dove *x.x.xxxx.x* rappresenta il numero di versione. I cmdlet di PowerShell (inclusi i client .dlls richiesti) si trovano nella sottodirectory **Strumenti\ProcessiDatabaseElastici** e gli script di PowerShell per installare, aggiornare e disinstallare si trovano anch’essi nella sottodirectory **Strumenti**.
3. Passare alla sottodirectory strumenti sotto la cartella Microsoft.Azure.SqlDatabase.Processi.x.x.xxx.x digitando strumenti cd, ad esempio:
   
        PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*>cd tools

4. Eseguire lo script .\InstallElasticDatabaseJobsCmdlets.ps1 per copiare la directory ProcessiDatabaseElastici in $home\Documenti\WindowsPowerShell\Moduli. Il modulo da utilizzare, verrà automaticamente importato, ad esempio:
   
        PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools>Unblock-File .\InstallElasticDatabaseJobsCmdlets.ps1
        PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools>.\InstallElasticDatabaseJobsCmdlets.ps1

## <a name="install-the-elastic-database-jobs-components-using-powershell"></a>Installare i componenti dei processi di database elastici utilizzando PowerShell
1. Avviare una finestra di comando di Microsoft Azure PowerShell e passare alla \sottodirectory strumenti sotto la cartella Microsoft.Azure.SqlDatabase.Processi.x.x.xxx.x: digitare cd\strumenti
   
        PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*>cd tools

2. Eseguire lo script PowerShell .\InstallElasticDatabaseJobs.ps1 e fornire valori per le variabili richieste. Questo script crea i componenti descritti in [Componenti e prezzi dei processi di database elastici](sql-database-elastic-jobs-overview.md#components-and-pricing) con la configurazione del servizio Cloud di Azure per utilizzare correttamente i componenti dipendenti.

        PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools>Unblock-File .\InstallElasticDatabaseJobs.ps1
        PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools>.\InstallElasticDatabaseJobs.ps1

Quando si esegue questo comando, viene visualizzata una finestra in cui vengono richiesti **Nome utente** e **Password**. Non si tratta delle credenziali di Azure. Immettere il nome utente e password che saranno le credenziali di amministratore che si desidera creare per il nuovo server.

I parametri forniti in questa chiamata di esempio possono essere modificati per le impostazioni desiderate. Di seguito vengono fornite ulteriori informazioni sul comportamento di ciascun parametro:

<table style="width:100%">
  <tr>
    <th>Parametro</th>
    <th>Description</th>
  </tr>

<tr>
    <td>ResourceGroupName</td>
    <td>Fornisce il nome del gruppo di risorse di Azure creato per contenere i componenti di Azure appena creati. Questo parametro viene impostato su "__ElasticDatabaseJob". È consigliabile non modificare questo valore.</td>
    </tr>

</tr>

    <tr>
    <td>ResourceGroupLocation</td>
    <td>Fornisce la posizione di Azure da utilizzare per i componenti di Azure appena creati. Questo parametro viene impostato per il percorso Stati Uniti centrali.</td>
</tr>

<tr>
    <td>ServiceWorkerCount</td>
    <td>Fornisce il numero di ruoli di lavoro del servizio da installare. Questo parametro viene impostato su 1. Per ridimensionare il servizio e per garantire un'elevata disponibilità, è possibile utilizzare un numero maggiore di ruoli di lavoro. È consigliabile utilizzare "2" per le distribuzioni che richiedono un'elevata disponibilità del servizio.</td>
    </tr>

</tr>
    <tr>
    <td>ServiceVmSize</td>
    <td>Fornisce le dimensioni della macchina virtuale per l'utilizzo all'interno del servizio Cloud. Questo parametro viene impostato su A0. Sono accettati valori di parametri di A0/A1/A2/A3 che fanno si che il ruolo di lavoro utilizzi una dimensione Extrapiccola/Piccola/Media/Grande, rispettivamente. Per altre informazioni sulle dimensioni dei ruoli di lavoro, vedere [Componenti e prezzi dei processi di database elastici](sql-database-elastic-jobs-overview.md#components-and-pricing).</td>
</tr>

</tr>
    <tr>
    <td>SqlServerDatabaseSlo</td>
    <td>Fornisce l'obiettivo del livello di servizio per un'edizione Standard. Questo parametro viene impostato su S0. Sono accettati i valori dei parametri S0/S1/S2/S3/S4/S6/S9/S12 che fanno sì che il Database SQL di Azure usi il rispettivo SLO. Per ulteriori informazioni sugli SLO del database SQL, vedere [Componenti e prezzi dei processi di database elastici](sql-database-elastic-jobs-overview.md#components-and-pricing).</td>
</tr>

</tr>
    <tr>
    <td>SqlServerAdministratorUserName</td>
    <td>Fornisce il nome utente dell’amministratore per il server del Database SQL di Azure appena creato. Se omesso, si aprirà una finestra di credenziali di PowerShell per la richiesta di credenziali.</td>
</tr>

</tr>
    <tr>
    <td>SqlServerAdministratorPassword</td>
    <td>Fornisce la password dell’amministratore per il server del Database SQL di Azure appena creato. Se omesso, si aprirà una finestra di credenziali di PowerShell per richiedere le credenziali.</td>
</tr>
</table>

Per i sistemi che mirano ad avere un numero elevato di processi in esecuzione in parallelo su un numero elevato di database, si consiglia di specificare  parametri come ad esempio: ServiceWorkerCount - 2 - ServiceVmSize A2 - SqlServerDatabaseSlo S2.

    PS C:\*Microsoft.Azure.SqlDatabase.Jobs.dll.x.x.xxx.x*\tools>Unblock-File .\InstallElasticDatabaseJobs.ps1
    PS C:\*Microsoft.Azure.SqlDatabase.Jobs.dll.x.x.xxx.x*\tools>.\InstallElasticDatabaseJobs.ps1 -ServiceWorkerCount 2 -ServiceVmSize A2 -SqlServerDatabaseSlo S2

## <a name="update-an-existing-elastic-database-jobs-components-installation-using-powershell"></a>Aggiornare un'installazione dei componenti dei processi di database elastici esistente tramite PowerShell
**Processi di database elastici** possono essere aggiornati all'interno di un'installazione esistente per la scalabilità e la disponibilità elevata. Questo processo consente aggiornamenti futuri del codice del servizio senza dover eliminare e ricreare il database di controllo. Questo processo può anche essere utilizzato all'interno della stessa versione per modificare la dimensione della macchina virtuale di servizio o il numero del ruolo di lavoro del server.

Per aggiornare la dimensione della macchina virtuale di un'installazione, eseguire lo script seguente con i parametri aggiornati ai valori di propria scelta.

    PS C:\*Microsoft.Azure.SqlDatabase.Jobs.dll.x.x.xxx.x*\tools>Unblock-File .\UpdateElasticDatabaseJobs.ps1
    PS C:\*Microsoft.Azure.SqlDatabase.Jobs.dll.x.x.xxx.x*\tools>.\UpdateElasticDatabaseJobs.ps1 -ServiceVmSize A1 -ServiceWorkerCount 2

<table style="width:100%">
  <tr>
  <th>Parametro</th>
  <th>Description</th>
</tr>

  <tr>
    <td>ResourceGroupName</td>
    <td>Identifica il nome del gruppo di risorse di Azure utilizzato quando i componenti dei processi di database elastici sono stati inizialmente installati. Questo parametro viene impostato su "__ElasticDatabaseJob". Poiché non è consigliabile modificare questo valore, non è necessario specificare questo parametro.</td>
    </tr>
</tr>

</tr>

  <tr>
    <td>ServiceWorkerCount</td>
    <td>Fornisce il numero di ruoli di lavoro del servizio da installare.  Questo parametro viene impostato su 1.  Per ridimensionare il servizio e per garantire un'elevata disponibilità, è possibile utilizzare un numero maggiore di ruoli di lavoro.  È consigliabile utilizzare "2" per le distribuzioni che richiedono un'elevata disponibilità del servizio.</td>
</tr>

</tr>

    <tr>
    <td>ServiceVmSize</td>
    <td>Fornisce le dimensioni della macchina virtuale per l'utilizzo all'interno del servizio Cloud. Questo parametro viene impostato su A0. Sono accettati valori di parametri di A0/A1/A2/A3 che fanno si che il ruolo di lavoro utilizzi una dimensione Extrapiccola/Piccola/Media/Grande, rispettivamente. Per altre informazioni sulle dimensioni dei ruoli di lavoro, vedere [Componenti e prezzi dei processi di database elastici](sql-database-elastic-jobs-overview.md#components-and-pricing).</td>
</tr>

</table>

## <a name="install-the-elastic-database-jobs-components-using-the-portal"></a>Installare i componenti dei processi di database elastici utilizzando il portale
Dopo aver creato un [pool elastico](sql-database-elastic-pool-manage-portal.md), è possibile installare componenti dei **processi di database elastici** per abilitare l'esecuzione di attività amministrative su ogni database nel pool elastico. A differenza di quando si utilizzano le API PowerShell dei **processi di database elastici** , l'interfaccia del portale è attualmente limitata solamente all’esecuzione su un pool esistente.

**Tempo previsto per il completamento:** 10 minuti

1. Nella vista del dashboard del pool elastico tramite il [portale di Azure](https://portal.azure.com/#) fare clic su **Crea processo**.
2. Se si sta creando un processo per la prima volta, è necessario installare **processi di database elastici** facendo clic su **ANTEPRIMA TERMINI**.
3. Accettare i termini selezionando la casella di controllo.
4. Nella vista "Installa servizi", fare clic su **CREDENZIALI PROCESSO**.
   
    ![Installazione dei servizi][1]
5. Digitare un nome utente e una password per un amministratore del database. Come parte dell'installazione, viene creato un nuovo server di database SQL di Azure. All'interno di questo nuovo server, viene creato un nuovo database, noto come database di controllo, che viene utilizzato per contenere i metadati per i processi di database elastici. Il nome utente e la password creati in questo caso vengono utilizzati per l'accesso al database di controllo. Una credenziale separata viene utilizzata per l'esecuzione di script sui database all'interno del pool.
   
    ![Creare nome utente e password][2]
6. Fare clic sul pulsante OK. I componenti vengono creati automaticamente in pochi minuti in un nuovo [gruppo di risorse](../azure-resource-manager/resource-group-overview.md). Il nuovo gruppo di risorse viene bloccato sulla schermata iniziale, come illustrato di seguito. Una volta creati, i processi di database elastici (Servizio cloud, database SQL, Bus di servizio e Archiviazione) vengono creati tutti nel gruppo.
   
    ![gruppo di risorse nella schermata iniziale][3]
7. Se si tenta di creare o gestire un processo mentre si installano i processi di database elastici, nel momento in cui vengono fornite le **credenziali** verrà visualizzato il messaggio seguente.
   
    ![Distribuzione ancora in corso][4]

Se è necessaria la disinstallazione, eliminare il gruppo di risorse. Vedere [Come disinstallare i componenti dei processi di database elastici](sql-database-elastic-jobs-uninstall.md).

## <a name="next-steps"></a>Passaggi successivi
Assicurarsi che una credenziale con i diritti appropriati per l'esecuzione di script venga creata in ogni database nel gruppo e vedere [Protezione del Database SQL](sql-database-manage-logins.md)per ulteriori informazioni.
Per un’introduzione, vedere [Creazione e gestione di processi di database elastici](sql-database-elastic-jobs-create-and-manage.md) .

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-service-installation/screen-1.png
[2]: ./media/sql-database-elastic-jobs-service-installation/credentials.png
[3]: ./media/sql-database-elastic-jobs-service-installation/start-board.png
[4]: ./media/sql-database-elastic-jobs-service-installation/not-done.png
