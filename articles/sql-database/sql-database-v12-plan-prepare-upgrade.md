---
title: Pianificare l&quot;aggiornamento a SQL Database V12 | Documentazione Microsoft
description: In questo argomento vengono descritte le operazioni di preparazione e le limitazioni relative all&quot;aggiornamento del database SQL di Azure alla versione 12.
services: sql-database
documentationcenter: 
author: MightyPen
manager: jhubbard
editor: 
ms.assetid: 8020f904-ad27-40c5-8c59-bdf2e690f77d
ms.service: sql-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2016
ms.author: genemi
translationtype: Human Translation
ms.sourcegitcommit: e8bb9e5a02a7caf95dae0101c720abac1c2deff3
ms.openlocfilehash: 1e72343e419061c5059d27a6fae2d578d2ba6780


---
# <a name="plan-and-prepare-to-upgrade-to-sql-database-v12"></a>Pianificazione e predisposizione dell'aggiornamento alla versione 12 del database SQL
Questo argomento descrive la pianificazione e i preparativi da effettuare per eseguire l'aggiornamento dei database SQL di Azure dalla versione 11 alla versione 12.

È disponibile un nuovo [portale di Azure](https://portal.azure.com/) per supportare l'aggiornamento alla versione 12.

Nella seguente tabella vengono elencati altri argomenti della Guida relativi alla versione 12.

| Titolo e collegamento | Descrizione del contenuto |
|:--- |:--- |
| [Funzionalità di database SQL di Azure](sql-database-features.md) |Fornisce una matrice di funzionalità per il database SQL di Azure e SQL Server. |
| [Creare un database nella versione 12 del database SQL](sql-database-get-started.md) |Viene descritto come creare un nuovo database SQL di Azure versione 12. Illustra le varie opzioni oltre alla semplice creazione di un database vuoto. |

## <a name="plan-ahead"></a>Pianificazione
Le seguenti sottosezioni forniscono le informazioni da acquisire e indicano le decisioni da prendere prima di procedere con l'aggiornamento del database SQL di Azure alla versione 12.

### <a name="version-clarification"></a>Chiarimento sulla versione
Questo documento riguarda l'aggiornamento del database SQL di Microsoft Azure dalla versione 11 alla versione 12. A livello più formale, i numeri di versione si avvicinano ai due seguenti valori, come indicato dall'istruzione Transact-SQL **SELECT@@version;**:

* 12.0.2000.8 *(o appena superiore, V12)*
* 11.0.9228.18 *(V11)*
  * V11 è indicato anche come SAWA v2.

### <a name="service-tier-planning"></a>Pianificazione del livello di servizio
A partire dalla versione 12, il database SQL di Azure supporterà solo i livelli di servizio denominati Basic, Standard e Premium. I livelli di servizio Web e Business non sono supportati nella versione 12. Quindi, se si intende aggiornare un database SQL di Azure che attualmente è al livello di servizio Web o Business, è necessario decidere quale sarà il suo nuovo livello di servizio.

Per informazioni dettagliate sui livelli di servizio Basic, Standard e Premium, vedere:

* [Livelli di servizio del database SQL](sql-database-service-tiers.md)
* [Aggiornare le edizioni Web e Business del database SQL ai nuovi livelli di servizio](sql-database-upgrade-server-portal.md)

### <a name="review-the-geo-replication-configuration"></a>Rivedere la configurazione della replica geografica
Se il database SQL di Azure è configurato per la replica geografica, è necessario documentarne la configurazione corrente e arrestare la replica geografica prima di iniziare i preparativi per l'aggiornamento. Al termine dell'aggiornamento occorre riconfigurare il database per la replica geografica.

La strategia consiste nel lasciare inalterata l'origine ed eseguire il test su una copia del database.

## <a name="preparation-actions"></a>Preparativi
Terminata la pianificazione, è possibile eseguire i passaggi descritti nelle seguenti sottosezioni per preparare la fase finale di aggiornamento.

Negli argomenti della Guida indicati all'inizio di questo argomento sono riportate descrizioni dettagliate della fase finale di aggiornamento.

### <a name="service-tier-actions"></a>Azioni relative al livello di servizio
I livelli di prezzo Web e Business non sono supportati nella versione 12.

Se il database SQL di Azure versione 11 è un database Web o Business, durante il processo di aggiornamento verrà proposto di passare a un piano supportato. Viene suggerito un piano adeguato in base alla cronologia del carico di lavoro del database. È comunque possibile scegliere il piano supportato che si preferisce.

Per ridurre i passaggi da eseguire durante l'aggiornamento, è possibile disattivare il piano Web e Business per il database versione 11 prima di avviare l'aggiornamento. Questa operazione può essere eseguita nel [portale di Azure](https://portal.azure.com/).

In caso di incertezza sul livello di servizio a cui passare, il livello S2 del piano Standard rappresenta una scelta iniziale adeguata. Qualsiasi livello inferiore disporrà di un minor numero di risorse rispetto al livello Web e Business.

### <a name="suspend-geo-replication-during-upgrade"></a>Sospendere la replica geografica durante l'aggiornamento
Non è possibile eseguire l'aggiornamento alla versione 12 se nel database è attiva la replica geografica. È necessario prima di tutto riconfigurare il database in modo che non venga usata la replica geografica.

Al termine dell'aggiornamento sarà possibile configurare di nuovo il database per l'uso della replica geografica.

### <a name="open-ports-on-an-azure-vm-for-client-connectivity"></a>Porte aperte su una macchina virtuale di Azure per la connettività client
Se il programma client si connette a SQL Database V12 mentre il client viene eseguito in una macchina virtuale (VM) di Azure, è necessario aprire i seguenti intervalli di porta nella macchina virtuale:

* 11000-11999
* 14000-14999

Fare clic [qui](sql-database-develop-direct-route-ports-adonet-v12.md) per ulteriori informazioni sulle porte per il Database SQL V12. Le porte sono necessarie per il miglioramento delle prestazioni in SQL Database V12.

## <a name="a-idlimitationsalimitations-during-and-after-upgrade-to-v12"></a><a id="limitations"></a>Limitazioni durante e dopo l'aggiornamento alla versione 12
### <a name="portals-for-v12"></a>Portali per la versione 12
Sono disponibili tre portali per Azure e ognuno dispone di capacità diverse relativamente alla versione 12 del database SQL.

* [http://portal.azure.com/](https://portal.azure.com/)<br/>Questo portale di Azure è nuovo ed è ancora in stato di anteprima. Questo portale non ha ancora raggiunto lo stato di disponibilità generale (GA). Portale in uso:
  
  * Consente di gestire i server e i database versione 12.
  * Consente l'aggiornamento del database versione 11 alla versione 12.
* [http://manage.windowsazure.com/](http://manage.windowsazure.com/)<br/>Questo portale di Azure classico, infine, potrebbe essere eliminato. Portale in uso:
  
  * Consente di gestire i server e i database versione 12.
  * *Non* consente l'aggiornamento del database versione 11 alla versione 12.
* (http://*nomeserver*.database.windows.net)<br/> Portale classico di database SQL di Azure:
  
  * *Non* consente la gestione di server versione 12.

Si consiglia di connettersi ai database SQL di Azure con Visual Studio 2015 (VS2015). VS2015 può essere usato per le seguenti attività:

* Per eseguire un'istruzione Transact-SQL.
* Per progettare uno schema.
* Per sviluppare un database, sia online che offline.

Oppure, è invece possibile scaricare gratuitamente [Visual Studio Community 2015](https://www.visualstudio.com/vs/community/), una versione completa di VS2015.

Nella pagina del database del portale di Azure classico precedente, per avviare VS2015 nel computer e connettersi al database SQL di Azure è possibile fare clic su **Apri in Visual Studio**.

In alternativa, per connettersi al database SQL di Azure è possibile utilizzare SQL Server Management Studio (SSMS) 2014 con [CU6](http://support.microsoft.com/kb/3031047/) . Ulteriori dettagli sono in questo post di blog:<br/>[Aggiornamenti degli strumenti client per il database SQL di Azure](https://azure.microsoft.com/blog/2014/12/22/client-tooling-updates-for-azure-sql-database/).

> [!IMPORTANT]
> È consigliabile usare sempre la versione più aggiornata di Management Studio per restare sincronizzati con gli aggiornamenti di Microsoft Azure e del database SQL. [Aggiornare SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
> 
> 

### <a name="limitation-during-upgrade-to-v12"></a>Limitazione *durante* l'aggiornamento alla versione 12
Il database V11 rimane disponibile per l'accesso ai dati durante l'aggiornamento alla versione V12. Tuttavia è necessario considerare due limitazioni.

| Limitazione  | Descrizione |
|:--- |:--- |
| Durata dell'aggiornamento |La durata dell'aggiornamento dipende dalla dimensione, dall'edizione e dal numero di database presenti nel server. Il processo di aggiornamento può durare da ore a giorni per i server, soprattutto per server che dispongono di database con le seguenti caratteristiche:<br/><br/>* maggiore di 50 GB, o<br/>* a un livello di servizio non premium<br/><br/>Anche la creazione di nuovi database nel server durante l'aggiornamento può aumentare la durata dell'aggiornamento. |
| Replica geografica non supportata |La replica geografica non è supportata in un server versione 12 di cui è in corso l'aggiornamento dalla versione 11. |
| Il database è temporaneamente non disponibile nella fase finale dell'aggiornamento a V12 |I database che appartengono al server V11 restano disponibili durante il processo di aggiornamento. Tuttavia, la connessione al server e ai database è temporaneamente non disponibile nella fase finale, quando inizia la transizione da V11 al V12 pronto.<br/><br/>La durata del passaggio può variare da 40 secondi a 5 minuti. Per la maggior parte dei server, il passaggio avviene in 90 secondi. La durata del passaggio aumenta per i server che dispongono di un numero elevato di database, o quando i database hanno carichi di lavoro di scrittura pesanti. |

### <a name="limitation-after-upgrade-to-v12"></a>Limitazione *dopo* l'aggiornamento alla versione 12
| Limitazione  | Descrizione |
|:--- |:--- |
| Non è possibile ripristinare la versione 11 |Dopo un aggiornamento sul posto, il risultato non può essere annullato. |
| Livello Web o Business |Dopo l'avvio dell'aggiornamento, il server per il nuovo database versione 12 non è più in grado di riconoscere o accettare il livello di servizio Web o Business. |

### <a name="export-and-import-after-upgrade-to-v12"></a>Esportazione e importazione *dopo* l'aggiornamento alla versione 12
È possibile esportare o importare un database versione 12 usando il [portale di Azure](https://portal.azure.com/). In alternativa, è possibile esportare o importare usando uno dei seguenti strumenti:

* SQL Server Management Studio (SSMS)
* Visual Studio 2015
* Data-Tier Application Framework (DacFx)

Tuttavia, per usare gli strumenti, è innanzitutto necessario disporre o installare gli aggiornamenti più recenti per assicurarsi che supportino le nuove funzionalità della versione 12:

* [Aggiornamento cumulativo 6 per SQL Server Management Studio 2014](http://support2.microsoft.com/kb/3031047)
* [Aggiornamento di febbraio 2015 di SQL Server Data Tools in Visual Studio 2013](https://msdn.microsoft.com/data/hh297027)
* [Aggiornamento di febbraio 2015 di Data-Tier Application Framework (DacFx) per il database SLQ di Azure versione 12](http://www.microsoft.com/download/details.aspx?id=45886)

> [!NOTE]
> I precedenti collegamenti sono stati aggiornati il giorno 2 marzo 2015 o in data successiva. Si consiglia di usare gli aggiornamenti più recenti di questi strumenti.
> 
> 

### <a name="restore-to-v12-of-a-deleted-v11-database"></a>Ripristino di un database V11 eliminato nella versione V12
Il seguente scenario illustra che un database SQL di Azure versione 11 eliminato può essere ripristinato in un server di database SQL di Azure versione 12.

1. Si supponga di disporre di un server di database SQL di Azure versione 11. <br/>  Nel server è presente un database al livello di servizio Web e Business obsoleto.
2. Il database viene eliminato.
3. Il server viene aggiornato alla versione 12.
4. In seguito il database viene ripristinato nel server. <br/>  Il database viene quindi aggiornato alla versione 12, al livello S0 del livello di servizio Standard.
5. Per il database è possibile attivare qualsiasi livello di servizio supportato, se il livello S0 non è in linea con le proprie esigenze.

### <a name="powershell-cmdlets"></a>Cmdlet PowerShell
I cmdlet di PowerShell consentono di avviare, arrestare o monitorare un aggiornamento alla versione 12 del database SQL di Azure dalla versione 11 o qualsiasi altra versione precedente alla 12.

* [Eseguire l'aggiornamento a database SQL V12 tramite PowerShell](sql-database-upgrade-server-powershell.md)

Per la documentazione di riferimento sui cmdlet di Powershell, vedere:

* [Get-AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt603582.aspx)
* [Start-AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt619403.aspx)
* [Stop-AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt603589.aspx)

Il cmdlet Stop comporta l'annullamento non la sospensione. Non è possibile riprendere un aggiornamento, è possibile solamente riavviarlo dall'inizio. Il cmdlet Stop libera e rilascia tutte le risorse appropriate.

## <a name="failure-resolution"></a>Risoluzione in caso di errore
Se l'aggiornamento non riesce per qualche motivo, il database versione 11 rimane attivo e disponibile come di consueto.

## <a name="related-links"></a>Collegamenti correlati
*  [Funzionalità di anteprima](https://azure.microsoft.com/services/preview/)

<!--Anchors-->
[Subheading 1]: #subheading-1



<!--HONumber=Nov16_HO4-->


