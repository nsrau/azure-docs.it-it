<properties title="Scripting Elastic Scale with Scripts" pageTitle="Scripting per Scalabilità elastica tramite script" description="Attività di script in Elastic Scale con PowerShell e runbook del servizio di automazione di Azure." metaKeywords="Azure SQL Database, elastic scale, powershell scripts" services="sql-database" documentationCenter="" manager="jhubbard" authors="sidneyh@microsoft.com"/>

<tags ms.service="sql-database" ms.workload="sql-database" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/02/2014" ms.author="sidneyh" />

# Gestione della Scalabilità elastica tramite script


## Servizio automazione di Azure 

[Automazione di Azure](http://azure.microsoft.com/it-it/documentation/services/automation/) offre un servizio avanzato e molto richiesto per l'esecuzione del flusso di lavoro PowerShell nella piattaforma di Azure. È quindi possibile automatizzare le attività di manutenzione la cui esecuzione nel normale portale di Azure risulta complessa.  È sufficiente creare un flusso di lavoro PowerShell, definito **runbook** in Automazione di Azure, caricarlo nel cloud e pianificare l'esecuzione del runbook. Questo documento include informazioni sula configurazione end-to-end di Automazione di Azure per alcuni esempi di elasticità di partizionamento. Per altre informazioni, vedere l'[annuncio relativo all'anteprima](http://blogs.technet.com/b/in_the_cloud/archive/2014/04/15/announcing-the-microsoft-azure-automation-preview.aspx) oppure creare una [sottoscrizione](https://account.windowsazure.com/PreviewFeatures?fid=automation) di Azure.

Automazione di Azure viene usato in questo esempio come framework di pianificazione e di esecuzione del carico di lavoro. Automazione di Azure è simile a [SQL Agent nel cloud](http://azure.microsoft.com/blog/2014/06/26/azure-automation-your-sql-agent-in-the-cloud/). 

Oltre a questo documento sono disponibili le risorse seguenti:

* [Introduzione all'Automazione di Azure](http://azure.microsoft.com/it-it/documentation/articles/automation-create-runbook-from-samples/)
* [Procedura dettagliata: Introduzione alla NUOVA funzionalità di anteprima Automazione di Microsoft Azure](http://blogs.technet.com/b/keithmayer/archive/2014/04/04/step-by-step-getting-started-with-windows-azure-automation.aspx) 
* [Automazione di Microsoft Azure](http://blogs.technet.com/b/cbernier/archive/2014/04/08/microsoft-azure-automation.aspx) 
* Per domande specifiche su Automazione di Azure, vedere il [forum relativo ad Automazione](http://social.msdn.microsoft.com/Forums/windowsazure/en-US/home?forum=azureautomation&filter=alltypes&sort=lastpostdesc).  


## Prerequisiti

[Iscriversi](http://azure.microsoft.com/it-it/services/preview/) e [acquisire familiarità](http://azure.microsoft.com/it-it/documentation/articles/automation-create-runbook-from-samples/) con la versione di anteprima del servizio Automazione di Microsoft Azure. 


## File di PowerShell relativi all'elasticità di partizionamento

Il set di file di PowerShell seguenti include i comandi di base per ottenere scenari di ridimensionamento orizzontale e verticale tramite Automazione di Azure. 

Questi esempi illustrano come usare i moduli di esempio di PowerShell per eseguire attività di base relative all'elasticità di partizionamento. Insieme al servizio Automazione di Microsoft Azure e ai runbook di Automazione di Azure corrispondenti, è possibile creare processi automatizzati e pianificati per il provisioning di una nuova partizione e/o per la modifica del livello di prestazioni di partizioni specifiche in base a un set di regole. 

**SetupShardedEnvironment.ps1**: Questo runbook di PowerShell esegue una singola configurazione di un ambiente partizionato che include un gestore delle mappe partizioni e una mappa partizioni di tipo intervallo. 

**ProvisionByDate.ps1**: Esegue il provisioning di un nuovo database in modo anticipato rispetto al carico di lavoro del giorno successivo. Il database viene creato e denominato in base all'indicatore di data (AAAAMMGG) e viene registrato nel gestore delle mappe partizioni sotto forma di intervallo [AAAAMMGG, AAAAMMGG + 1G). 

**ProvisionBySize.ps1**: Esegue il provisioning di un nuovo database quando la capacità del database corrente è quasi esaurita. 

**ReduceServiceTier.ps1**: Scorre le partizioni disponibili in una mappa partizioni e determina se ogni singola partizione è idonea per la riduzione del livello di prestazioni. Per determinare l'idoneità di una partizione sono necessari due criteri: 1) Il livello di servizio corrente della partizione e 2) la durata del database.  

**ShardManagement.psm1**: Fornisce un set di metodi per l'interazione con il gestore delle mappe partizioni. 

**SqlDatabaseHelpers.psm1**: Fornisce un set di metodi per l'interazione con il database SQL di Azure. 

**ShardElasticity.psm1**: Fornisce un set di metodi per l'esecuzione di operazioni di ridimensionamento orizzontale e verticale. 

**ShardElasticityModule.psd1**: Fornisce un set di metodi per l'interazione con Scalabilità elastica e il database SQL di Azure. 

## Costi

Si noti che l'esecuzione degli script di esempio di PowerShell avrà come risultato la creazione di database che comporteranno costi effettivi per il proprietario della sottoscrizione. I database SQL di Azure SQL sottostanti verranno [addebitati a una tariffa](http://azure.microsoft.com/it-it/pricing/details/sql-database/) uguale a quella degli altri database SQL di Azure SQL.  A partire dal 1° novembre verranno addebitati i costi seguenti: 

* Il runbook SetupShardedEnvironment crea il gestore delle mappe partizioni in un database Basic ($0,0069 all'ora) ed esegue anche il provisioning della prima partizione in un database Basic ($0,0069 all'ora). 

* I runbook ProvisionBySize e ProvisionByDate eseguono il provisioning di un database SO Standard ($0,0208 all'ora).  Per fare fronte a questi costi, se è prevista l'esecuzione insieme al runbook ReduceServiceTier, il livello del servizio del database appena sottoposto a provisioning verrà ridotto da Standard S0 ($0,0208 all'ora) a Basic ($0,0069 all'ora) dopo una giornata. 

Nell'ambito degli esempi forniti, infine, l'uso di [Automazione di Azure](http://azure.microsoft.com/it-it/pricing/details/automation/) non comporta attualmente alcun addebito per il proprietario della sottoscrizione.  Per informazioni dettagliate, vedere la [pagina relativa ai prezzi di Automazione di Azure](http://azure.microsoft.com/it-it/pricing/details/automation/). 

## Per caricare i runbook 

1. Scaricare il file **ShardElasticity.zip** ed estrarne i contenuti.
2. [Aggiungere riferimenti ai file binari di Scalabilità elastica usando NuGet](./sql-database-elastic-scale-add-references-visual-studio.md)
3. Individuare il file binario del client di Scalabilità elastica (**Microsoft.Azure.SqlDatabase.ElasticScale.Client.dll**).
4. Posizionare il file DLL nella cartella ShardElasticityModule, quindi comprimere la cartella 
3. Nell'account di Automazione di Azure caricare il file ShardElasticityModule.zip come **asset**. 
4. In Automazione di Azure creare una **credenziale di asset** denominata *ElasticScaleCredential*, che include il nome utente e la password per il server di database SQL di Azure. 
5. Creare una **variabile di asset** denominata *SqlServerName* per il nome del server di database SQL di Azure. 
5. Caricare i file **SetupShardedEnvironment.ps1**, **ProvisionBySize.ps1**, **ProvisionByDate.ps1** e **ProvisionByDate.ps1** come runbook. 
6. Testare una sola volta il runbook **SetupShardedEnvironment.ps1** per configurare l'ambiente partizionato. 
7. Pubblicare uno o più runbook rimanenti e collegarli a una pianificazione. 
8. Esaminare l'output del runbook nella scheda **PROCESSI**. 

Se le istruzioni rapide per l'esempio non sono state utili, vedere le istruzioni dettagliate per l'esempio più avanti.  

##  Per usare i runbook

1. Creare un modulo PowerShell e inserirlo in un pacchetto 
2. Creare un account di Automazione di Microsoft Azure 
3. Caricare il modulo PowerShell in Automazione di Azure come asset 
4. Creare asset di tipo credenziale e variabile di Automazione di Azure 
5. Caricare runbook di PowerShell in Automazione di Azure 
6. Configurare un ambiente partizionato 
7. Testare i runbook di Automazione 
8. Pubblicare i runbook 
9. Pianificare i runbook 


## Creare un modulo PowerShell e inserirlo in un pacchetto 

È prima di tutto necessario creare un modulo PowerShell che fa riferimento agli assembly di Scalabilità elastica e inserire il modulo in un pacchetto, in modo che sia pronto per il caricamento nel servizio Automazione di Azure come un asset. 

1. Scaricare il file "ShardElasticity.zip".
2. Estrarre tutto il contenuto.

    ![Extract all][1]
3. Ottenere il file DLL del client di Scalabilità elastica, ad esempio Microsoft.Azure.SqlDatabase.ElasticScale.Client.dll, e copiare i file seguenti nella cartella "ShardElasticityModule" locale, scaricata nel passaggio 1.  A questo scopo, è possibile eseguire una delle due operazioni seguenti: 1) Scaricare il file DLL tramite il [collegamento] al pacchetto NuGet per Scalabilità elastica oppure 2) usare il [collegamento] per il progetto Starter Kit di Scalabilità elastica (da creare) e passare a \bin\Debug\ per ottenere il file DLL.

    ![Obtain Dll][2]

4. Comprimere la cartella ShardElasticityModule. 

    Nota: Per Automazione di Azure sono necessarie alcune condizioni di denominazione. Il nome del file con estensione zip deve corrispondere esattamente al nome del modulo ShardElasticityModule.psm1 specificato (ShardElasticityModule.zip). Il file con estensione zip include la cartella ShardElasticityModule (nome corrispondente al nome del modulo), che a sua volta include il file con estensione psm1. Se non si rispetta questa struttura, Automazione di Azure non riuscirà a decomprimere il modulo.

5.    Dopo avere verificato che i contenuti e la struttura della cartella compressa soddisfino i requisiti previsti, procedere con il passaggio successivo. L'aspetto dovrebbe essere analogo al seguente:

    ![dll][3]


## Iscriversi per l'anteprima di Automazione di Azure

1. Passare alla pagina delle [funzionalità di anteprima di Azure](http://azure.microsoft.com/it-it/services/preview/).
    
2. Fare clic su **Prova**.

    ![Click Try It][8]

2. Passare al [portale di Microsoft Azure](https://manage.windowsazure.com/microsoft.onmicrosoft.com).
3. Fare clic su **Automazione**.

    ![Automation][4]
4. Nella parte inferiore dello schermo fare clic su **Crea**. 
5. Nel prompt riportato di seguito immettere un nome di account valido e fare clic sul segno di spunta nell'angolo inferiore destro della finestra di dialogo.

    ![Prompt][5] 
5. Procedere con il passaggio successivo. Se l'operazione ha esito positivo, si otterrà un risultato analogo al seguente.

    ![success][6]

## Caricare il modulo PowerShell in Automazione di Azure come asset 

Caricare il modulo PowerShell precedente nell'account di Automazione di Azure. Ad esempio, il modulo include un insieme di funzioni di elasticità di partizionamento e file DLL di Scalabilità elastica, a cui è possibile fare riferimento dai runbook. 

1. Fare clic su **ASSET** sulla barra multifunzione nella parte superiore della schermata.
2. Fare clic su **IMPORTA MODULO** nella parte inferiore della pagina. 
3. Fare clic su **CERCA FILE** e individuare il file **ShardElasticityModule.zip** creato in precedenza. 
4. Dopo la selezione del file corretto, fare clic sul segno di spunta nell'angolo inferiore destro della finestra di dialogo per importarlo. Il servizio Automazione di Azure importerà il modulo. 
5. Procedere con il passaggio successivo. Se l'operazione ha esito positivo, si otterrà un risultato analogo al seguente. Se il modulo non è stato importato correttamente, verificare che il file con estensione zip rispetti le convenzioni indicate in precedenza.

    ![Assets][10] 
      
## Creare asset di tipo credenziale e variabile di Automazione di Azure 

Invece di impostare come hardcoded le credenziali e le variabili più usate nei runbook, Automazione di Azure permette di creare asset di tipo credenziale e variabile, rispettivamente, a cui molti runbook possono fare riferimento. È ad esempio possibile modificare una password in una sola posizione. 

1. Selezionare il nuovo account di Automazione di Azure appena creato.
2. Nell'account **ShardElasticityExamples** fare clic su **ASSET** sulla barra multifunzione.
3. Fare clic su **AGGIUNGI IMPOSTAZIONE** nella parte inferiore della schermata.  
4. Fare clic su **AGGIUNGI CREDENZIALI**. 

    ![Add credential][9]
4. Selezionare **Credenziali per Windows PowerShell** come **TIPO DI CREDENZIALI** ed **ElasticScaleCredential** come Nome. La descrizione è facoltativa.  
5. Fare clic sulla freccia nell'angolo inferiore destro della finestra di dialogo. 

    Nota: Per usare i runbook senza modifiche, usare i nomi di variabile esattamente come sono forniti nelle istruzioni. I runbook fanno riferimento ai nomi delle variabili. 
5. Inserire il nome utente e la password (due volte) per il server del database SQL di Azure SQL in cui devono essere eseguiti gli esempi dell'elasticità di partizionamento. 
 
6. Per creare un asset di tipo variabile, fare clic su **AGGIUNGI IMPOSTAZIONE**, quindi selezionare **AGGIUNGI VARIABILE**. 

    ![Add variable][7]
7. Per questa esercitazione creare una variabile per il nome completo del server del database SQL di Azure in cui risiederanno il gestore delle mappe partizioni e i database partizionati. Selezionare **Stringa** come **TIPO VARIABILE** e immettere **SqlServerName**. Fare clic sulla freccia per continuare. 
8. Immettere il nome completo del server del database SQL di Azure come VALORE, quindi fare clic sul segno di spunta. 
9. È stata completata la creazione di un asset di tipo credenziale e un asset di tipo variabile, che verranno usati nei runbook dell'elasticità di partizionamento.  Procedere con il passaggio successivo. Se l'operazione ha esito positivo, si otterrà un risultato analogo al seguente: 
    

## Caricare runbook di PowerShell in Automazione di Azure 

Caricare i quattro runbook di PowerShell di esempio disponibili. 

1. Per caricare un nuovo runbook di Automazione di Azure, fare clic su **RUNBOOK** sulla barra multifunzione. 
2. Fare clic su **IMPORTA** nella parte inferiore della schermata. 
3. Passare alla cartella che include il file, quindi selezionare **SetupShardedEnvironment.ps1** e fare clic sul segno di spunta. 
4. Ripetere i passaggi 2 e 3 per i tre runbook di PowerShell rimanenti (**ProvisionByDate.ps1**, **ProvisionBySize.ps1** e **ReduceServiceTier.ps1**). 
5. Procedere con il passaggio successivo. 

## Configurare un ambiente partizionato 

Il passaggio successivo consiste nell'eseguire il runbook **SetupShardedEnvironment**, che esegue il provisioning di un ambiente partizionato, che include un database di gestore delle mappe partizioni, una mappa partizioni di tipo intervallo e una partizione per la giornata corrente.   

1. Selezionare il runbook **SetupShardedEnvironment** facendo clic sul nome corrispondente. 
2. Fare clic su **AUTORE** sulla barra multifunzione. 
3. In questa schermata viene visualizzato il codice che costituisce il runbook e sarà possibile modificarlo, se necessario. Per configurare l'ambiente partizionato, fare clic su **TEST** nella parte inferiore della schermata. Confermare che si vuole salvare e testare il runbook. 
4. È possibile verificare lo stato del processo nel riquadro di output. Al termine, il server del database SQL di Azure specificato verrà popolato con un database di gestione delle mappe partizioni e un database partizioni. Il runbook **SetupShardedEnvironment** è stato progettato unicamente per il provisioning dell'ambiente partizionato e non deve essere eseguito su base ricorrente. Procedere con il passaggio successivo.  

## Testare i runbook di Automazione 

Prima di pubblicare e pianificare i runbook, verificare la corretta esecuzione di ogni runbook. 

1. Fare clic su **RUNBOOK** sulla barra multifunzione nella parte superiore della pagina. 
2. Fare clic sul runbook **ProvisionByDate**.
3. Fare clic su **AUTORE** sulla barra multifunzione nella parte superiore della pagina. 
4. Fare clic su **SALVA** e quindi su **TEST**.
5. Ripetere la procedura per **ReduceServiceTier**. 

    Poiché **ProvisionBySize** e **ProvisionByDate** eseguono il provisioning di nuove partizioni, usando algoritmi diversi, non è necessario eseguire **ProvisionByDate** in questo momento. 

## Pubblicare il runbook 
Il passaggio successivo consiste nel pubblicare il runbook, in modo che sia possibile pianificarne l'esecuzione su base periodica. 

1. Fare clic su **PUBBLICA** nella parte inferiore della pagina. 
2. Fare clic su **Pubblicato**. 
3. Procedere con il passaggio successivo.
 
## Pianificare il runbook 

Il passaggio finale consiste nel creare e collegare una pianificazione al runbook pubblicato in precedenza. 

1. Fare clic su **PIANIFICAZIONE** nella parte superiore della pagina. 
2. Fare clic su **COLLEGA A UNA NUOVA PIANIFICAZIONE**.
3. Assegnare un nome appropriato alla pianificazione, quindi fare clic sulla freccia a destra.
4. Configurare la pianificazione.
5. Al termine, fare clic sul segno di spunta nella parte inferiore della finestra di dialogo.
6. Dopo il completamento dell'esecuzione del processo in base alla pianificazione definita in precedenza, fare clic sull'opzione **PROCESSI** sulla barra multifunzione nella parte superiore della pagina, quindi selezionare il processo pianificato. 

## Conclusioni 

Un modulo PowerShell è stato creato, inserito in un pacchetto e caricato in Automazione di Azure come un asset ed è stato creato, testato, pubblicato e pianificato un runbook.  Per monitorare l'integrità e lo stato del runbook, usare le schede Dashboard e Processi. 

Gli esempi forniti presentano solo alcune delle possibilità offerte dall'uso combinato di Scalabilità elastica, database SQL di Azure e Automazione di Azure. Provare a usare questi esempi e approfondirli per abilitare il ridimensionamento orizzontale, verticale o entrambi per l'applicazione di Scalabilità elastica del database SQL di Azure. 

[AZURE.INCLUDE [elastic-scale-include](../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-scripting/zip.png
[2]: ./media/sql-database-elastic-scale-scripting/dll.png
[3]: ./media/sql-database-elastic-scale-scripting/lookslikethis.png
[4]: ./media/sql-database-elastic-scale-scripting/automation.png
[5]: ./media/sql-database-elastic-scale-scripting/prompt.png
[6]: ./media/sql-database-elastic-scale-scripting/success.png
[7]: ./media/sql-database-elastic-scale-scripting/add-variable.png
[8]: ./media/sql-database-elastic-scale-scripting/sign-up.png
[9]: ./media/sql-database-elastic-scale-scripting/add-credential.png
[10]: ./media/sql-database-elastic-scale-scripting/assets.png
