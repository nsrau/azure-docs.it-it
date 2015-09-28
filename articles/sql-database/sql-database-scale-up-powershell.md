<properties 
    pageTitle="Modificare il livello di servizio e il livello delle prestazioni di un database SQL di Azure utilizzando PowerShell" 
    description="La modifica del livello del servizio e del livello delle prestazioni di un database SQL di Azure mostra come scalare il database SQL verso l’alto o verso il basso con PowerShell. Modificare il livello di prezzi di un database SQL di Azure con PowerShell." 
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jeffreyg"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="09/10/2015"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Modificare il livello di servizio e il livello di prestazioni (livello di prezzo) di un database SQL con PowerShell

**Database singolo**

> [AZURE.SELECTOR]
- [Azure Preview Portal](sql-database-scale-up.md)
- [PowerShell](sql-database-scale-up-powershell.md)


In questo articolo viene illustrato come modificare il livello del servizio e il livello delle prestazioni del database SQL con PowerShell.

Utilizzare le informazioni contenute negli argomenti [Aggiornamento delle edizioni Web e Business del database SQL ai nuovi livelli di servizio](sql-database-upgrade-new-service-tiers.md) e [Livelli di servizio e livelli di prestazioni del database SQL di Azure](https://msdn.microsoft.com/library/azure/dn741336.aspx) per determinare il livello di prestazioni e il livello di servizio appropriati per il database SQL di Azure.

> [AZURE.IMPORTANT]La modifica del livello di servizio e del livello delle prestazioni di un database SQL è un'operazione in linea. Ciò significa che il database rimane online e disponibile durante l'intera operazione senza tempi di inattività.

- Per effettuare il downgrade di un database, la dimensione di quest'ultimo deve essere inferiore alla dimensione massima consentita per il livello del servizio di destinazione. 
- Quando si aggiorna un database con la [replica geografica standard](https://msdn.microsoft.com/library/azure/dn758204.aspx) o la [replica geografica attiva](https://msdn.microsoft.com/library/azure/dn741339.aspx) abilitata, è necessario aggiornare i database secondari al livello di prestazioni desiderato prima di aggiornare il database primario.
- Quando si effettua il downgrade da un livello di servizio Premium, è necessario prima terminare tutte le relazioni di replica geografica. È possibile attenersi alla procedura descritta nell'argomento [Terminare una relazione di copia continua](https://msdn.microsoft.com/library/azure/dn741323.aspx) per arrestare il processo di replica tra il database primario e i database secondari attivi.
- Le offerte per il ripristino del servizio sono diverse per i vari livelli di servizio. Se si effettua il downgrade è possibile che la capacità di eseguire un ripristino temporizzato o di disporre di un periodo di mantenimento del backup inferiore vengano perse. Per ulteriori informazioni, vedere [Backup e ripristino del database SQL di Azure](https://msdn.microsoft.com/library/azure/jj650016.aspx).
- È possibile apportare fino a quattro modifiche di singoli database (livello di servizio o di prestazioni) nell'arco di un periodo di 24 ore.
- Le nuove proprietà del database non vengono applicate finché non sono state completate le modifiche.



**Per completare l'esercitazione di questo articolo, sono necessari gli elementi seguenti:**

- Una sottoscrizione di Azure. Se è necessaria una sottoscrizione ad Azure, fare semplicemente clic su **VERSIONE DI PROVA GRATUITA** nella parte superiore della pagina, quindi tornare e proseguire fino alla fine di questo articolo.
- un database SQL di Azure. Se non si dispone di un database SQL, crearne uno seguendo i passaggi indicati in questo articolo: [Creare il primo database SQL di Azure](sql-database-get-started.md).
- Azure PowerShell. Per scaricare e installare i moduli Azure PowerShell, eseguire l'[Installazione guidata piattaforma Web Microsoft](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409). Per informazioni dettagliate, vedere [Come installare e configurare Azure PowerShell](powershell-install-configure.md).

I cmdlet per modifica del livello di servizio dei database SQL di Azure si trovano nel modulo di Gestione risorse di Azure. Quando si avvia Azure PowerShell, i cmdlet nel modulo di Azure vengono importati per impostazione predefinita. Per passare al modulo AzureResourceManager, usare il cmdlet Switch-AzureMode.

	Switch-AzureMode -Name AzureResourceManager

Se si esegue il cmdlet **Switch-AzureMode** e si visualizza l'avviso Il *cmdlet Switch-AzureMode è deprecato e verrà rimosso in una versione successiva*, non è un problema. È sufficiente andare al passaggio successivo per configurare le credenziali.

Per informazioni dettagliate, vedere [Utilizzo di Windows PowerShell con Gestione risorse](powershell-azure-resource-manager.md).

## Configurare le credenziali e selezionare la sottoscrizione

È innanzitutto necessario stabilire l'accesso all'account Azure, poi avviare PowerShell ed eseguire il cmdlet seguente. Nella schermata di accesso utilizzare lo stesso indirizzo email e password utilizzati per accedere al portale di Azure.

	Add-AzureAccount

Dopo aver effettuato l'accesso, sullo schermo verranno visualizzate informazioni tra cui l'ID usato per l'accesso con le sottoscrizioni di Azure per le quali si dispone dell'accesso.


### Selezionare la sottoscrizione ad Azure

Per selezionare la sottoscrizione, è necessario l’ID sottoscrizione o il nome della sottoscrizione (**-SubscriptionName**) che può essere copiato dal passaggio precedente o, se si dispone di più sottoscrizioni e si necessita di maggiori informazioni, è possibile eseguire il cmdlet **Get-AzureSubscription** e copiare le informazioni di sottoscrizione desiderate dal set di risultati. Una volta acquisita la sottoscrizione, eseguire il cmdlet seguente:

	$SubscriptionId = "4cac86b0-1e56-bbbb-aaaa-000000000000"
    Select-AzureSubscription -SubscriptionId $SubscriptionId

Dopo aver eseguito correttamente il cmdlet **Select-AzureSubscription**, verrà nuovamente visualizzato il prompt dei comandi di PowerShell. Se si dispone di più di una sottoscrizione, è possibile eseguire il cmdlet **Get-AzureSubscription** e verificare che nella sottoscrizione che si desidera usare sia visualizzato **IsCurrent: True**.


 


## Modificare il livello di servizio e il livello delle prestazioni del database SQL

Eseguire il cmdlet **Set-AzureSqlDatabase** e impostare **-RequestedServiceObjectiveName** sul livello di prestazioni del livello di prezzi desiderato, ad esempio *S0*, *S1*, *S2*, *S3*, *P1*, *P2*, ...

    $ResourceGroupName = "resourceGroupName"
    
    $ServerName = "serverName"
    $DatabaseName = "databaseName"

    $NewEdition = "Standard"
    $NewPricingTier = "S2"

    $ScaleRequest = Set-AzureSqlDatabase -DatabaseName $DatabaseName -ServerName $ServerName -ResourceGroupName $ResourceGroupName -Edition $NewEdition -RequestedServiceObjectiveName $NewPricingTier


  

   


## Script di esempio di PowerShell per modificare il livello di servizio e il livello delle prestazioni del database SQL

    
	Switch-AzureMode -Name AzureResourceManager
    
    $SubscriptionId = "4cac86b0-1e56-bbbb-aaaa-000000000000"
    
    $ResourceGroupName = "resourceGroupName"
    $Location = "Japan West"
    
    $ServerName = "serverName"
    $DatabaseName = "databaseName"
    
    $NewEdition = "Standard"
    $NewPricingTier = "S2"
    
    Add-AzureAccount
    Select-AzureSubscription -SubscriptionId $SubscriptionId
    
    $ScaleRequest = Set-AzureSqlDatabase -DatabaseName $DatabaseName -ServerName $ServerName -ResourceGroupName $ResourceGroupName -Edition $NewEdition -RequestedServiceObjectiveName $NewPricingTier
    
    $ScaleRequest
    
        


## Passaggi successivi

- [Scalare orizzontalmente e verticalmente](sql-database-elastic-scale-get-started.md)
- [Connettersi al database SQL con SSMS ed eseguire query](sql-database-connect-query-ssms.md)
- [Esportare un database SQL di Azure](sql-database-export-powershell.md)

## Risorse aggiuntive

- [Panoramica sulla continuità aziendale](sql-database-business-continuity.md)
- [Documentazione relativa al database SQL](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=Sept15_HO3-->