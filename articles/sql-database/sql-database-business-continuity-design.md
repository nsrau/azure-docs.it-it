<properties 
   pageTitle="Progettazione del database SQL per la continuità aziendale" 
   description="Linee guida per la scelta delle funzionalità di ripristino di emergenza per la continuità aziendale in base a requisiti specifici. Sono incluse le descrizioni delle funzionalità offerte automaticamente dal database SQL."
   services="sql-database" 
   documentationCenter="" 
   authors="elfisher" 
   manager="jeffreyg" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management" 
   ms.date="04/13/2015"
   ms.author="elfish"/>

#Progettazione per la continuità aziendale

Quando si progetta un'applicazione per la continuità aziendale è necessario fornire una risposta alle domande seguenti:

1. Quale funzionalità per la continuità aziendale è adatta a proteggere l'applicazione da eventuali interruzioni del servizio?
2. Quale livello di ridondanza e topologia di replica è opportuno usare?

##Quando usare il ripristino geografico

Per impostazione predefinita, ogni database SQL offre una protezione incorporata di base, archiviando i backup del database in Archiviazione di Azure con ridondanza geografica (GRS). Se si sceglie questo metodo, non è necessaria alcuna configurazione speciale o allocazione di risorse aggiuntive. Con questi backup, è possibile ripristinare il database in qualsiasi area usando il comando per il ripristino geografico. Per informazioni dettagliate sull'uso della funzionalità di ripristino geografico per ripristinare l'applicazione, vedere [Ripristino dopo un'interruzione del servizio](sql-database-disaster-recovery.md).

È consigliabile usare la protezione incorporata se l'applicazione soddisfa i criteri seguenti:

1. Non è considerata cruciale. Non ha un contratto di servizio vincolante e, di conseguenza, un tempo di inattività di 24 o più ore non comporta alcuna responsabilità finanziaria.
2. La frequenza di modifica dei dati è bassa (ad esempio, è espressa in termini di transazioni all'ora). Un valore RPO di 1 ora non genererà una perdita di dati significativa.
3. L'applicazione è suscettibile alla variazione del costo e non può giustificare il costo aggiuntivo della replica geografica. 

> [AZURE.NOTE]La funzionalità di ripristino geografico non esegue un'allocazione preliminare della capacità di calcolo in un'area specifica per ripristinare i database attivi dal backup durante l'interruzione del servizio. Il servizio gestirà il carico di lavoro associato alle richieste di ripristino geografico in modo da ridurre al minimo l'impatto sui database esistenti in tale area e le loro richieste di capacità saranno prioritarie. Pertanto, il tempo di ripristino del database dipenderà da quanti altri database verranno ripristinati contemporaneamente nella stessa area.

##Quando usare la replica geografica

La funzionalità di replica geografica crea un database di replica (secondario) in un'area diversa da quella del database primario. Questa funzionalità garantisce che il database abbia le risorse di calcolo e i dati necessari per supportare il carico di lavoro dell'applicazione dopo il ripristino. Per informazioni sull'uso del failover per ripristinare l'applicazione, vedere [Ripristino dopo un'interruzione del servizio](sql-database-disaster-recovery.md).

È consigliabile usare la replica geografica se l'applicazione soddisfa i criteri seguenti:

1. È considerata cruciale. Ha un contratto di servizio vincolante con valori RPO e RTO elevati. La perdita di dati e disponibilità comporta una responsabilità finanziaria. 
2. La frequenza di modifica dei dati è alta (ad esempio, è espressa in termini di transazioni al minuto o al secondo). Un valore RPO di 1 ora associato alla protezione predefinita avrà come risultato una perdita di dati inaccettabile.
3. Il costo legato all'uso della replica geografica è notevolmente inferiore rispetto alla potenziale responsabilità finanziaria e alla perdita di profitto associata.

> [AZURE.NOTE]Se l'applicazione usa database di livello Basic, la replica geografica non è supportata.

##Confronto tra replica geografica standard e replica geografica attiva

I database di livello Standard non consentono di usare la replica geografica attiva. Pertanto, se l'applicazione usa database Standard e soddisfa i criteri sopra descritti, dovrebbe abilitare la replica geografica standard. Al contrario, per i database Premium è possibile scegliere una delle due opzioni. La replica geografica standard è stata progettata come soluzione di ripristino di emergenza più semplice e meno costosa, particolarmente adatta ad applicazioni che la usano solo per proteggersi da eventi imprevisti, ad esempio le interruzioni del servizio. Con la replica geografica standard, per il ripristino è possibile usare solo l'area abbinata di ripristino di emergenza e si ha la possibilità di creare più di un database secondario. Quest'ultima funzionalità è fondamentale per lo scenario aggiornamento dell'applicazione. Pertanto, se questo scenario ha un ruolo cruciale per l'applicazione, è consigliabile abilitare la replica geografica attiva. Per altre informazioni, vedere [Aggiornare l'applicazione senza tempo di inattività](sql-database-business-continuity-application-upgrade.md).

> [AZURE.NOTE]La replica geografica attiva supporta inoltre l'accesso in sola lettura al database secondario e fornisce quindi capacità aggiuntiva per i carichi di lavoro di sola lettura.

##Come abilitare la replica geografica

È possibile abilitare la replica geografica tramite il portale di Azure o mediante una chiamata dell'API REST o del comando di PowerShell.

###Portale di Azure

1. Accedere al [portale di Azure](https://portal.Azure.com).
2. Sul lato sinistro della schermata fare clic su **SFOGLIA** e quindi selezionare **Database SQL**.
3. Passare al pannello del database, selezionare la **mappa della replica geografica** e fare clic su **Configura replica geografica**.
4. Passare al pannello della replica geografica. Selezionare l'area di destinazione. 
5. Passare al pannello per la creazione del database secondario. Selezionare un server esistente nell'area di destinazione o crearne uno nuovo.
6. Selezionare il tipo secondario (*Leggibile* o *Non leggibile*).
7. Fare clic su **Crea** per completare la configurazione.

> [AZURE.NOTE]L'area abbinata di ripristino di emergenza nel pannello della replica geografica verrà contrassegnata come *consigliata*. Se si usa un database di livello Premium, è possibile scegliere un'area diversa. Se si usa un database Standard, non è possibile cambiare l'area. Con il database Premium, si ha la possibilità di selezionare il tipo di database secondario (*Leggibile* o *Non leggibile*). Il database Standard consente solo la selezione di un database secondario *Non leggibile*.
 
###PowerShell

Per automatizzare la configurazione della replica geografica, usare il cmdlet [Start-AzureSqlDatabaseCopy](https://msdn.microsoft.com/library/dn720220.aspx) di PowerShell.

Per creare la replica geografica con un database secondario non leggibile per un database Premium o Standard:
		
		Start-AzureSqlDatabaseCopy -ServerName "SecondaryServerName" -DatabaseName "SecondaryDatabaseName" -PartnerServer "PartnerServerName" –ContinuousCopy -OfflineSecondary
Per creare la replica geografica con un database secondario leggibile per un database Premium:

		Start-AzureSqlDatabaseCopy -ServerName "SecondaryServerName" -DatabaseName "SecondaryDatabaseName" -PartnerServer "PartnerServerName" –ContinuousCopy
		 
Questo comando è asincrono. Dopo la restituzione, usare il cmdlet [Get-AzureSqlDatabaseCopy](https://msdn.microsoft.com/library/dn720235.aspx) per controllare lo stato di questa operazione. Il campo ReplicationState dell'oggetto restituito avrà il valore CATCH_UP al termine dell'operazione.

		Get-AzureSqlDatabaseCopy -ServerName "PrimaryServerName" -DatabaseName "PrimaryDatabaseName" -PartnerServer "SecondaryServerName"


###API REST 

Per creare una configurazione di replica geografica a livello di codice, usare l'API [Start Database Copy](https://msdn.microsoft.com/library/azure/dn509576.aspx).

Questa API è asincrona. Dopo la restituzione, usare l'API [Get Database Copy](https://msdn.microsoft.com/library/azure/dn509570.aspx) per controllare lo stato di questa operazione. Il campo ReplicationState del corpo della risposta avrà il valore CATCH_UP al termine dell'operazione.


##Come scegliere la configurazione di failover 

Quando si progetta l'applicazione per la continuità aziendale, è necessario considerare alcune opzioni di configurazione. La scelta dipenderà dalla topologia di distribuzione dell'applicazione e dalle parti dell'applicazione più vulnerabili a un'interruzione del servizio. Per informazioni aggiuntive, vedere [Progettazione di soluzioni cloud per il ripristino di emergenza mediante la replica geografica attiva](https://msdn.microsoft.com/library/azure/dn741328.aspx).


 

<!---HONumber=July15_HO2-->