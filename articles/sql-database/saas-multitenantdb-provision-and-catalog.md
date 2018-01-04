---
title: Eseguire il provisioning in SaaS multi-tenant Azure | Documenti Microsoft
description: Informazioni su come effettuare il provisioning di nuovi tenant e catalogarli in un'app SaaS multi-tenant del database SQL di Azure
keywords: esercitazione database SQL
services: sql-database
documentationcenter: 
author: MightyPen
manager: craigg
editor: MightyPen
ms.reviewer: billgib;andrela;genemi
ms.assetid: 
ms.service: sql-database
ms.custom: saas apps
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/19/2017
ms.author: billgib
ms.openlocfilehash: 42bbb6131aa71520410b22af4d74e99a63fe81cf
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/20/2017
---
# <a name="provision-and-catalog-new-tenants-in-a-saas-application-using-a-sharded-multi-tenant-azure-sql-database"></a>Catalogo e il provisioning di nuovi tenant in un'applicazione SaaS utilizzando un database SQL di Azure multi-tenant partizionato

Questo articolo viene illustrato il provisioning e catalogazione dei nuovi tenant, in un *database partizionato multi-tenant* modello o modello.

In questo articolo presenta due parti principali:

- [Discussione concettuale](#goto_2_conceptual) di provisioning e catalogare di nuovi tenant.

- [Esercitazione](#goto_1_tutorial) che evidenzia il codice di script di PowerShell che esegue il provisioning e il catalogo.
    - L'esercitazione Usa l'applicazione SaaS ticket Wingtip, adattato al modello di database partizionato multi-tenant.

<a name="goto_2_conceptual"/>

## <a name="database-pattern"></a>Modello di database

In questa sezione, più alcune altre che seguono, illustrare i concetti del modello di database partizionato multi-tenant.

In questo modello partizionato multi-tenant, gli schemi di tabella all'interno di ogni database includono una chiave del tenant nella chiave primaria delle tabelle che archiviano i dati di tenant. La chiave del tenant consente a ogni singolo database archiviare 0, 1 o molti tenant. L'utilizzo di database partizionati rende più semplice per il sistema di applicazioni supportare un numero molto elevato di tenant. Tutti i dati per qualsiasi tenant è archiviata in un database. Il numero elevato di tenant viene distribuita tra i molti database partizionati. Un database di catalogo vengono archiviati il mapping di ogni tenant al relativo database.

#### <a name="isolation-versus-lower-cost"></a>Isolamento rispetto al costo inferiore

Un tenant che dispone di un database tutti a se stessa gode dei vantaggi dell'isolamento. Il tenant può contenere il database ripristinato a una data precedente senza essere limitati dall'impatto su altri tenant. Le prestazioni del database possono essere ottimizzata per ottimizzare nuovamente un tenant, senza compromettere con altri tenant. Il problema è che isolamento costi maggiore sarà il costo per condividere un database con altri tenant.

Quando viene eseguito il provisioning di un nuovo tenant, che è possibile condividere un database con altri tenant, o può essere inserita in un nuovo database. È possibile in seguito si cambia idea e spostare il database nel caso di altri.

Nella stessa applicazione SaaS, per ottimizzare i costi o isolamento per ogni tenant sono presenti database con più tenant e tenant singolo.

   ![App del database multi-tenant partizionato con catalogo del tenant](media/saas-multitenantdb-provision-and-catalog/MultiTenantCatalog.png)

## <a name="tenant-catalog-pattern"></a>Modello di catalogo del tenant

Quando si dispone di due o più database, ciascuno dei quali contiene almeno un tenant, l'applicazione deve avere un modo per individuare il database che archivia il tenant di interesse corrente. Un database di catalogo archivia questo mapping.

#### <a name="tenant-key"></a>Chiave del tenant

Per ogni tenant, l'applicazione Wingtip possibile derivare una chiave univoca, ovvero la chiave del tenant. L'applicazione estrae il nome del tenant dall'URL della pagina Web. L'applicazione genera un hash per il nome per ottenere la chiave. L'app Usa la chiave per accedere al catalogo. Il catalogo di riferimenti incrociati informazioni sul database in cui è archiviato il tenant. L'app utilizza le informazioni sul database per la connessione. È possibile usare anche altri schemi della chiave del tenant.

L'uso di un catalogo consente di modificare il nome o il percorso di un database tenant in seguito al provisioning senza interrompere l'applicazione. In un modello di database multi-tenant, il catalogo supporta lo spostamento di un tenant tra database.

#### <a name="tenant-metadata-beyond-location"></a>Metadati del tenant oltre percorso

Il catalogo può anche indicare se un tenant è non in linea per manutenzione o altre azioni. E il catalogo può essere estesa per archiviare altri tenant o metadati del database, ad esempio gli elementi seguenti:
- Il livello di servizio o l'edizione di un database.
- La versione dello schema del database.
- Il nome del tenant e il relativo contratto di servizio (contratto di servizio).
- Informazioni per abilitare la gestione delle applicazioni, il supporto tecnico o processi devops.  

Il catalogo può essere utile anche per abilitare la creazione di report tra tenant, la gestione dello schema e l'estrazione dei dati allo scopo di analizzarli. 

### <a name="elastic-database-client-library"></a>Libreria client del database elastico 

In Wingtip, il catalogo viene implementato nel *tenantcatalog* database. Il *tenantcatalog* viene creato utilizzando le funzionalità di gestione di partizioni del [elastico Database Client libreria (EDCL)](sql-database-elastic-database-client-library.md). La libreria consente a un'applicazione creare, gestire e utilizzare un *mappa partizioni* che viene archiviato in un database. Una mappa partizioni di riferimenti incrociati la chiave del tenant con la partizione, vale a dire il relativo database partizionato.

Durante il provisioning del tenant, funzioni EDCL possono essere utilizzate da applicazioni o script di PowerShell per creare le voci nella mappa partizioni. Le funzioni EDCL in un secondo momento possono essere utilizzate per connettersi al database corretto. Il EDCL memorizza nella cache le informazioni di connessione per ridurre al minimo il traffico nel database di catalogo e velocizzare il processo di connessione.

> [!IMPORTANT]
> Eseguire *non* modificare i dati nel database del catalogo tramite accesso diretto. Gli aggiornamenti diretti non sono supportati a causa dell'alto rischio di danneggiamento dei dati. In alternativa, modificare i dati di mapping solo tramite le API EDCL.

## <a name="tenant-provisioning-pattern"></a>Modello di provisioning del tenant

#### <a name="checklist"></a>Elenco di controllo

Quando si desidera eseguire il provisioning di un nuovo tenant in un database condiviso esistente, del database condiviso è necessario porre le domande seguenti:
- Ha spazio sufficiente per il nuovo tenant?
- Disporre le tabelle con i dati di riferimento necessari per il nuovo tenant, o possono essere aggiunti i dati?
- Ha la variante appropriata dello schema di base per il nuovo tenant?
- È nella posizione geografica appropriata vicino al nuovo tenant?
- È a livello di servizio corretto per il nuovo tenant?

Quando si desidera che il nuovo tenant vengono isolate in un proprio database, è possibile creare in modo da soddisfare le specifiche per il tenant.

Al termine del provisioning, è necessario registrare il tenant nel catalogo. Infine, è possibile inserire il mapping del tenant per fare riferimento alla partizione appropriata.

#### <a name="template-database"></a>Database modello

Eseguire il provisioning del database eseguendo gli script SQL, distribuendo un bacpac o copiando un database del modello. Le app Wingtip copiare un database di modello per creare nuovi database tenant.

Come qualsiasi altra applicazione, Wingtip evolveranno nel tempo. In alcuni casi, Wingtip richiederà modifiche al database. Le modifiche possono includere gli elementi seguenti:
- Schema nuovo o modificato.
- Dati di riferimento nuove o modificate.
- Attività di manutenzione di routine del database per garantire prestazioni ottimali dell'applicazione.

Con un'applicazione SaaS queste modifiche devono essere distribuite in modo coordinato a un gruppo potenzialmente enorme di database tenant. Per essere incluse in database tenant futuri, le modifiche devono essere incorporate nel processo di provisioning. Questo problema è esplorare ulteriormente nel [esercitazione Gestione schema](saas-tenancy-schema-management.md).

#### <a name="scripts"></a>Script

Gli script di provisioning tenant in questa esercitazione supportano entrambi gli scenari seguenti:
- Provisioning di un tenant in un database esistente condiviso con altri tenant.
- Provisioning di un tenant nel proprio database.

I dati del tenant vengono quindi inizializzati e registrati nella mappa partizioni del catalogo. Nell'applicazione di esempio, i database che contengono più tenant vengono assegnati un nome generico, ad esempio *tenants1* o *tenants2*. I database contenenti un singolo tenant vengono assegnati il nome del tenant. Le convenzioni di denominazione specifiche usate nell'esempio non sono una parte essenziale del modello, poiché l'uso di un catalogo consente l'assegnazione di un nome qualsiasi al database.  

<a name="goto_1_tutorial"/>

## <a name="tutorial-begins"></a>Esercitazione inizia

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Eseguire il provisioning di un tenant in un database multi-tenant
> * Eseguire il provisioning di un tenant in un database a tenant singolo
> * Eseguire il provisioning di un batch di tenant in database multi-tenant e a tenant singolo
> * Registrare un database ed eseguire il mapping del tenant in un catalogo

#### <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, verificare che i prerequisiti seguenti siano completati:

- Azure PowerShell è installato. Per informazioni dettagliate, vedere [Introduzione ad Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)

- È stata distribuita l'app SaaS di database multi-tenant Wingtip Tickets. Per eseguire la distribuzione in meno di cinque minuti, vedere [Distribuire ed esplorare l'applicazione SaaS di database multi-tenant Wingtip Tickets](saas-multitenantdb-get-started-deploy.md)

- Ottenere Wingtip script e codice sorgente:
    - Gli script e il codice sorgente dell'applicazione SaaS di database multi-tenant Wingtip Tickets sono disponibili nel repository [WingtipTicketsSaaS-MultiTenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) di GitHub.
    - Vedere il [indicazioni generali](saas-tenancy-wingtip-app-guidance-tips.md) per i passaggi scaricare e sbloccare gli script Wingtip. 

## <a name="provision-a-tenant-into-a-database-shared-with-other-tenants"></a>Eseguire il provisioning di un tenant in un database *condivisa* con altri tenant

In questa sezione è visualizzato un elenco delle azioni principali per il provisioning che vengono eseguite dagli script di PowerShell. Quindi utilizzare il debugger di PowerShell ISE per eseguire gli script per visualizzare le azioni nel codice.

#### <a name="major-actions-of-provisioning"></a>Azioni principali di provisioning

Di seguito sono indicati gli elementi chiave del flusso di lavoro di provisioning che si esegue:

- **Calcolare la nuova chiave tenant**: una funzione hash viene utilizzata per creare la chiave del tenant dal nome del tenant.
- **Verifica se la chiave del tenant esiste già**: il catalogo viene controllato per verificare la chiave non è già stata registrata.
- **Inizializzare i tenant nel database tenant predefinito**: il database tenant verrà aggiornato per aggiungere le nuove informazioni di tenant.  
- **Registrare i tenant nel catalogo**: il mapping tra la nuova chiave e il database tenants1 esistente viene aggiunto al catalogo. 
- **Aggiungere il nome del tenant a una tabella di estensione catalogo**: il nome di evento viene aggiunto alla tabella tenant nel catalogo.  In questo modo viene illustrato come il database del catalogo può essere esteso per supportare ulteriori dati specifici dell'applicazione.
- **Aprire la pagina eventi per il nuovo tenant**: il *Bushwillow blu* pagina eventi viene aperto nel browser.

   ![eventi](media/saas-multitenantdb-provision-and-catalog/bushwillow.png)

#### <a name="debugger-steps"></a>Passaggi di debugger

Per comprendere il modo in cui l'app Wingtip implementa tenant di nuovo il provisioning in un database condiviso, aggiungere un punto di interruzione ed eseguire il flusso di lavoro:

1. In *PowerShell ISE* aprire ...\\Learning Modules\\ProvisionTenants\\*Demo-ProvisionTenants.ps1* e impostare i parametri seguenti:
   - **$TenantName** = **Bushwillow Blues**, nome di una nuova sede di eventi.
   - **$VenueType** = **blu**, uno dei tipi di eventi predefiniti: blu, classicalmusic, danza, jazz, judo, motorracing, con più finalità, opera, rockmusic, calcio (lettere minuscole, senza spazi).
   - **$DemoScenario** = **1**, eseguire il provisioning di un tenant in un database condiviso con altri tenant.

2. Aggiungere un punto di interruzione, posizionando il cursore in un punto qualsiasi nella riga 38, la seguente riga: *nuovo Tenant '*, quindi premere **F9**.

   ![punto di interruzione](media/saas-multitenantdb-provision-and-catalog/breakpoint.png)

3. Eseguire lo script premendo **F5**.

4. Quando l'esecuzione dello script si arresta in corrispondenza del punto di interruzione, premere **F11** per eseguire l'istruzione del codice.

   ![debug](media/saas-multitenantdb-provision-and-catalog/debug.png)

5. Tenere traccia dell'esecuzione dello script usando le opzioni del menu **Debug** **F10** e **F11** per eseguire le istruzioni delle funzioni chiamate.

Per altre informazioni sul debug degli script di PowerShell, vedere [Suggerimenti per l'utilizzo e il debug degli script di PowerShell](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise).

## <a name="provision-a-tenant-in-its-own-database"></a>Eseguire il provisioning di un tenant nel relativo *proprio* database

#### <a name="major-actions-of-provisioning"></a>Azioni principali di provisioning

Di seguito sono indicati gli elementi chiave del flusso di lavoro di provisioning che si esegue quando si tiene traccia dello script:

- **Calcolare la nuova chiave tenant**: una funzione hash viene utilizzata per creare la chiave del tenant dal nome del tenant.
- **Verifica se la chiave del tenant esiste già**: il catalogo viene controllato per verificare la chiave non è già stata registrata.
- **Creare un nuovo database tenant**: il database viene creato copiando il *basetenantdb* utilizzando un modello di gestione risorse di database.  Il nuovo nome del database si basa sul nome del tenant.
- **Aggiungere i database di catalogo**: il nuovo database tenant è registrato come una partizione nel catalogo.
- **Inizializzare i tenant nel database tenant predefinito**: il database tenant verrà aggiornato per aggiungere le nuove informazioni di tenant.  
- **Registrare i tenant nel catalogo**: il mapping tra la nuova chiave del tenant e *sequoiasoccer* database viene aggiunto al catalogo.
- **Nome del tenant viene aggiunto al catalogo**: il nome di evento viene aggiunto alla tabella nel catalogo estensione tenant.
- **Aprire la pagina eventi per il nuovo tenant**: il *Sequoia calcio* pagina eventi viene aperto nel browser.

   ![eventi](media/saas-multitenantdb-provision-and-catalog/sequoiasoccer.png)

#### <a name="debugger-steps"></a>Passaggi di debugger

Ora esaminare il processo di script durante la creazione di un tenant nel relativo database:

1. Ancora in ...\\Learning Modules\\ProvisionTenants\\*Demo-ProvisionTenants.ps1* impostare i parametri seguenti:
   - **$TenantName** = **Sequoia Soccer**, il nome di una nuova sede di eventi.
   - **$VenueType** = **soccer**, uno dei tipi di sede predefiniti: blues, classicalmusic, dance, jazz, judo, motorracing, multipurpose, opera, rockmusic, soccer (in minuscolo e senza spazi).
   - **$DemoScenario** = **2**, eseguire il provisioning di un tenant in un proprio database.

2. Aggiungere un nuovo punto di interruzione posizionando il cursore in un punto qualsiasi nella riga 57 contenente il testo: *&&nbsp;$PSScriptRoot\New-TenantAndDatabase `* e premere **F9**.

   ![punto di interruzione](media/saas-multitenantdb-provision-and-catalog/breakpoint2.png)

3. Eseguire lo script premendo **F5**.

4. Quando l'esecuzione dello script si arresta in corrispondenza del punto di interruzione, premere **F11** per eseguire l'istruzione del codice.  Usare **F10** e **F11** per proseguire ed eseguire l'istruzione relativa alle funzioni per tracciare l'esecuzione.

## <a name="provision-a-batch-of-tenants"></a>Eseguire il provisioning di un batch di tenant

Questo esercizio descrive come effettuare il provisioning di un batch di 17 tenant. È consigliabile eseguire il provisioning di questo batch di tenant prima di iniziare le altre esercitazioni di Wingtip Tickets, in modo da avere più database da usare.

1. In *PowerShell ISE* aprire ...\\Learning Modules\\ProvisionTenants\\*Demo-ProvisionTenants.ps1* e impostare il parametro *$DemoScenario* su 4:
   - **$DemoScenario** = **4**, eseguire il provisioning di un batch di tenant in un database condiviso.

2. Premere **F5** ed eseguire lo script.

### <a name="verify-the-deployed-set-of-tenants"></a>Verificare il set di tenant distribuito 

In questa fase, è presente una combinazione di tenant distribuito in un database condiviso e i tenant distribuiti nei relativi database. Il portale di Azure può essere usato per controllare i database creati. Nel [portale di Azure](https://portal.azure.com) aprire il server **tenants1-mt-\<UTENTE\>** passando all'elenco di server SQL.  L'elenco di **database SQL** deve includere il database **tenants1** condiviso e i database per i tenant che si trovano nel proprio database:

   ![elenco di database](media/saas-multitenantdb-provision-and-catalog/Databases.png)

Sebbene il portale di Azure mostri il tenant di database, non è possibile vedere i tenant *all'interno* del database condiviso. L'elenco completo di tenant può essere visualizzato nel **Hub eventi** pagina Web di Wingtip ed esplorando il catalogo.

#### <a name="using-wingtip-tickets-events-hub-page"></a>Pagina hub di eventi Wingtip ticket

Aprire la pagina Hub eventi nel browser (http:events.wingtip-mt.\<UTENTE\>.trafficmanager.net)  

#### <a name="using-catalog-database"></a>Utilizzo di database di catalogo

L'elenco completo dei tenant e il database corrispondente per ognuna è disponibile nel catalogo. Una vista SQL è purché il tenant di join nome per il nome del database. La vista viene correttamente il valore dell'estensione di metadati archiviati nel catalogo.
- La vista SQL è disponibile nel database tenantcatalog.
- Il nome del tenant viene archiviato nella tabella tenant.
- Il nome del database viene archiviato nelle tabelle di gestione di partizioni.

1. In SQL Server Management Studio (SSMS), connettersi al server di tenant **catalogo MT\<utente\>. database.windows.net**, con l'account di accesso = **developer**e la Password =**P@ssword1**

    ![Finestra di dialogo di connessione di SQL Server Management Studio](media/saas-multitenantdb-provision-and-catalog/SSMSConnection.png)

2. In Esplora oggetti di SQL Server Management Studio, selezionare le viste di *tenantcatalog* database.

3. Fare clic con il pulsante destro del mouse sulla vista *TenantsExtended* e scegliere **Select Top 1000 Rows** (Seleziona le prime 1000 righe). Si noti il mapping tra nome e il database del tenant per diversi tenant.

    ![Visualizzazione ExtendedTenants in SQL Server Management Studio](media/saas-multitenantdb-provision-and-catalog/extendedtenantsview.png)
      
## <a name="other-provisioning-patterns"></a>Altri modelli di provisioning

Questa sezione vengono illustrati altri modelli di provisioning interessanti.

#### <a name="pre-provisioning-databases-in-elastic-pools"></a>Pre-provisioning del database nel pool elastico

Il modello di pre-provisioning sfrutta il fatto che quando si usa un pool elastico, la fatturazione dipende dal pool e non dai database. Pertanto è possibile aggiungere i database a un pool elastico prima che siano necessari senza incorrere in costi aggiuntivi. Questa pre-visioning notevolmente riduce il tempo impiegato per eseguire il provisioning di un tenant in un database. Il numero di database di cui viene effettuato il pre-provisioning può essere modificato in base alle esigenze, per ottenere un buffer adatto alla frequenza di provisioning prevista.

#### <a name="auto-provisioning"></a>Provisioning automatico

Nel modello di provisioning automatico, un servizio di provisioning dedicato viene utilizzato per eseguire il provisioning di server e i pool database automaticamente in base alle esigenze. Questa automazione include il pre-provisioning di database nel pool elastico. E se i database sono state rimosse e viene eliminati, gli spazi vuoti che viene creato un pool elastico possono essere compilati dal servizio di provisioning in base alle esigenze.

Questo tipo di servizio automatica può essere semplice o complessa. Ad esempio, l'automazione in grado di gestire il provisioning in più aree geografiche ed è stato possibile impostare la replica geografica per il ripristino di emergenza. Con il modello di provisioning automatico, un'applicazione client o script deve inviare una richiesta di provisioning per una coda per essere elaborato da un servizio di provisioning. Lo script potrebbe quindi eseguire il polling per rilevare il completamento. Se viene utilizzato pre-provisioning, le richieste verrebbero gestite rapidamente, mentre un servizio in background si gestiscono il provisioning di un database di sostituzione.

## <a name="additional-resources"></a>Risorse aggiuntive

<!-- - Additional [tutorials that build upon the Wingtip SaaS application](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)-->
- [Libreria client dei database elastici](sql-database-elastic-database-client-library.md)
- [Modalità di esecuzione del debug degli script in Windows PowerShell ISE](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise)


## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come:

> [!div class="checklist"]
> * Eseguire il provisioning di un singolo tenant nuovo in un database multi-tenant condiviso e nel database autonomo
> * Effettuare il provisioning di un batch di altri tenant
> * Eseguire in dettaglio le procedure per il provisioning dei tenant e la relativa registrazione nel catalogo

Provare l'[Esercitazione sul monitoraggio delle prestazioni](saas-multitenantdb-performance-monitoring.md).

