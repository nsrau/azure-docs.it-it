---
title: Effettuare il provisioning in Azure multi-tenant SaaS| Documentazione Microsoft
description: Informazioni su come effettuare il provisioning di nuovi tenant e catalogarli in un'app SaaS multi-tenant del database SQL di Azure
keywords: esercitazione database SQL
services: sql-database
author: MightyPen
manager: craigg
ms.reviewer: billgib;andrela;genemi
ms.service: sql-database
ms.custom: saas apps
ms.topic: article
ms.date: 12/19/2017
ms.author: billgib
ms.openlocfilehash: fb2f2bcbbc8b7f0b0012c4e7baf4a274671d4af0
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
# <a name="provision-and-catalog-new-tenants-in-a-saas-application-using-a-sharded-multi-tenant-azure-sql-database"></a>Effettuare il provisioning di nuovi tenant e catalogarli in un'applicazione SaaS con un database SQL partizionato multi-tenant di Azure

L'articolo illustra il provisioning e la catalogazione di nuovi tenant, in uno schema o modello di *database partizionato multi-tenant*.

L'articolo presenta due parti principali:

- [Descrizione concettuale](#goto_2_conceptual) del provisioning e della catalogazione di nuovi tenant.

- [Esercitazione](#goto_1_tutorial) che presenta il codice dello script di PowerShell che effettua il provisioning e la catalogazione.
    - L'esercitazione utilizza l'applicazione SaaS Wingtip Tickets, adattata al modello di database partizionato multi-tenant.

<a name="goto_2_conceptual"/>

## <a name="database-pattern"></a>Modello di database

Questa sezione e altre che seguono illustrano i concetti del modello di database partizionato multi-tenant.

In questo modello partizionato multi-tenant gli schemi di tabella interni a ogni database includono una chiave di tenant nella chiave primaria delle tabelle che archiviano i dati del tenant. La chiave di tenant consente a ogni singolo database di archiviare 0, 1 o molti tenant. L'utilizzo di database partizionati semplifica il supporto di un numero molto elevato di tenant da parte del sistema dell'applicazione. Tutti i dati per qualsiasi tenant sono archiviati in un database. Il numero elevato di tenant è distribuito tra molti database partizionati. Un database di catalogo archivia il mapping di ogni tenant al relativo database.

#### <a name="isolation-versus-lower-cost"></a>Isolamento e costo ridotto

Un tenant che dispone di un database dedicato sfrutta i vantaggi dell'isolamento. Il database del tenant può essere ripristinato a una data precedente senza essere limitato dall'impatto su altri tenant. Le prestazioni del database possono essere ottimizzate per un tenant specifico, senza compromettere altri tenant. Il problema dell'isolamento è dato dai costi superiori rispetto a un database condiviso con altri tenant.

Quando viene effettuato il provisioning di un nuovo tenant, esso può condividere un database con altri tenant o essere collocato in un nuovo database autonomo. Successivamente è possibile cambiare la situazione del database.

I database con più tenant e con tenant singoli sono mescolati nella stessa applicazione SaaS per ottimizzare i costi o l'isolamento per ciascun tenant.

   ![App del database multi-tenant partizionato con catalogo del tenant](media/saas-multitenantdb-provision-and-catalog/MultiTenantCatalog.png)

## <a name="tenant-catalog-pattern"></a>Modello di catalogo del tenant

Quando sono presenti due o più database, ciascuno contenente almeno un tenant, l'applicazione deve essere in grado di individuare il database che archivia il tenant attualmente di interesse. Un database di catalogo archivia questo mapping.

#### <a name="tenant-key"></a>Chiave di tenant

Per ogni tenant, l'applicazione Wingtip può derivare una chiave univoca, ossia la chiave di tenant. L'app estrae il nome del tenant dall'URL della pagina Web. L'app genera un hash del nome per ottenere la chiave. L'app utilizza la chiave per accedere al catalogo. Il catalogo crea riferimenti incrociati tra le informazioni sul database in cui è archiviato il tenant. L'app utilizza le informazioni del database per la connessione. È possibile usare anche altri schemi della chiave del tenant.

L'uso di un catalogo consente di modificare il nome o il percorso di un database tenant in seguito al provisioning senza interrompere l'applicazione. In un modello di database multi-tenant, il catalogo consente di "spostare" un tenant da un database a un altro.

#### <a name="tenant-metadata-beyond-location"></a>Metadati del tenant oltre il percorso

Il catalogo può anche indicare se un tenant è offline per manutenzione o altre azioni. Il catalogo può essere esteso per archiviare altri metadati di tenant o database, ad esempio gli elementi seguenti:
- Livello di servizio o edizione di un database.
- Versione dello schema del database.
- Nome del tenant e relativo contratto di servizio.
- Informazioni per abilitare la gestione delle applicazioni, il supporto tecnico o i processi di sviluppo.  

Il catalogo può essere utile anche per abilitare la creazione di report tra tenant, la gestione dello schema e l'estrazione dei dati allo scopo di analizzarli. 

### <a name="elastic-database-client-library"></a>Libreria client del database elastico 

In Wingtip il catalogo viene implementato nel database *tenantcatalog*. Il database *tenantcatalog* viene creato tramite le funzionalità di gestione delle partizioni della [libreria EDCL (Elastic Database Client Library, libreria client dei database elastici)](sql-database-elastic-database-client-library.md). La libreria consente a un'applicazione di creare, gestire e usare una *mappa partizioni* archiviata in un database. Una mappa partizioni crea riferimenti incrociati tra la chiave di tenant e la partizione, ossia il relativo database partizionato.

Durante il provisioning del tenant, è possibile usare le funzioni della libreria client dei database elastici da applicazioni o script di PowerShell per creare le voci della mappa partizioni. In un secondo momento, tali funzioni possono essere utilizzate per connettersi al database corretto. La libreria client dei database elastici memorizza nella cache le informazioni di connessione per ridurre al minimo il traffico verso il database del catalogo e velocizzare il processo di connessione.

> [!IMPORTANT]
> *Non* modificare i dati nel database del catalogo mediante l'accesso diretto. Gli aggiornamenti diretti non sono supportati dato l'elevato rischio di danneggiamento dei dati. Modificare, invece, i dati di mapping usando solo le API della libreria client dei database elastici.

## <a name="tenant-provisioning-pattern"></a>Modello di provisioning del tenant

#### <a name="checklist"></a>Elenco di controllo

Quando si desidera eseguire il provisioning di un nuovo tenant in un database condiviso esistente, è necessario porre le domande seguenti sul database condiviso:
- Ha spazio sufficiente per il nuovo tenant?
- Dispone di tabelle con i dati di riferimento necessari per il nuovo tenant o è possibile aggiungere i dati?
- Dispone della variazione appropriata dello schema base per il nuovo tenant?
- È nella posizione geografica appropriata vicino al nuovo tenant?
- È al livello di servizio corretto per il nuovo tenant?

Quando si desidera che il nuovo tenant sia isolato in un database autonomo, è possibile crearlo in modo che soddisfi le specifiche per il tenant.

Al termine del provisioning, è necessario registrare il tenant nel catalogo. Infine, è possibile inserire il mapping del tenant per fare riferimento alla partizione appropriata.

#### <a name="template-database"></a>Database modello

Eseguire il provisioning del database eseguendo gli script SQL, distribuendo un bacpac o copiando un database del modello. Le app Wingtip copiano un database modello per creare nuovi database di tenant.

Come qualsiasi applicazione, Wingtip evolverà nel tempo. In alcuni casi, Wingtip richiederà modifiche al database. Le modifiche possono includere gli elementi seguenti:
- Schema nuovo o modificato.
- Dati di riferimento nuovi o modificati.
- Attività di manutenzione del database di routine per garantire prestazioni ottimali dell'app.

Con un'applicazione SaaS queste modifiche devono essere distribuite in modo coordinato a un gruppo potenzialmente enorme di database tenant. Per essere incluse in database tenant futuri, le modifiche devono essere incorporate nel processo di provisioning. Questo aspetto viene esaminato in modo approfondito nell'[esercitazione relativa alla gestione dello schema](saas-tenancy-schema-management.md).

#### <a name="scripts"></a>Script

Gli script di provisioning del tenant in questa esercitazione supportano entrambi gli scenari seguenti:
- Provisioning di un tenant in un database esistente condiviso con altri tenant.
- Provisioning di un tenant in un database autonomo.

I dati del tenant vengono quindi inizializzati e registrati nella mappa partizioni del catalogo. Nell'applicazione di esempio, ai database che contengono più tenant viene assegnato un nome generico, ad esempio *tenants1* o *tenants2*. Ai database contenenti un singolo tenant viene assegnato il nome del tenant. Le convenzioni di denominazione specifiche usate nell'esempio non sono una parte essenziale del modello, poiché l'uso di un catalogo consente l'assegnazione di un nome qualsiasi al database.  

<a name="goto_1_tutorial"/>

## <a name="tutorial-begins"></a>Inizio dell'esercitazione

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Eseguire il provisioning di un tenant in un database multi-tenant
> * Eseguire il provisioning di un tenant in un database a tenant singolo
> * Eseguire il provisioning di un batch di tenant in database multi-tenant e a tenant singolo
> * Registrare un database ed eseguire il mapping del tenant in un catalogo

#### <a name="prerequisites"></a>prerequisiti

Per completare questa esercitazione, verificare che i prerequisiti seguenti siano completati:

- Azure PowerShell è installato. Per informazioni dettagliate, vedere [Introduzione ad Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)

- È stata distribuita l'app SaaS di database multi-tenant Wingtip Tickets. Per eseguire la distribuzione in meno di cinque minuti, vedere [Distribuire ed esplorare l'applicazione SaaS di database multi-tenant Wingtip Tickets](saas-multitenantdb-get-started-deploy.md)

- Ottenere gli script e il codice sorgente di Wingtip:
    - Gli script e il codice sorgente dell'applicazione SaaS di database multi-tenant Wingtip Tickets sono disponibili nel repository [WingtipTicketsSaaS-MultiTenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) di GitHub.
    - Vedere le [linee guida generali](saas-tenancy-wingtip-app-guidance-tips.md) per i passaggi da seguire per scaricare e sbloccare gli script dell'app Wingtip. 

## <a name="provision-a-tenant-into-a-database-shared-with-other-tenants"></a>Effettuare il provisioning di un tenant in un database *condiviso* con altri tenant

Questa sezione contiene un elenco delle azioni principali per il provisioning eseguite dagli script di PowerShell. Utilizzare quindi il debugger PowerShell ISE per eseguire gli script e visualizzare le azioni nel codice.

#### <a name="major-actions-of-provisioning"></a>Azioni principali del provisioning

Di seguito sono indicati gli elementi chiave del flusso di lavoro di provisioning che si esegue:

- **Calcolare la nuova chiave del tenant**: viene usata una funzione hash per creare la chiave del tenant dal nome del tenant.
- **Controllare se la chiave del tenant esiste già**: il catalogo viene controllato per verificare che la chiave non sia già stata registrata.
- **Inizializzare il tenant nel database del tenant predefinito**: il database del tenant viene aggiornato per aggiungere le nuove informazioni del tenant.  
- **Registrare il tenant nel catalogo**: il mapping tra la chiave del nuovo tenant e il database tenants1 esistente viene aggiunto al catalogo. 
- **Aggiungere il nome del tenant a una tabella di estensione del catalogo**: il nome della sede viene aggiunto alla tabella dei tenant nel catalogo.  Questa aggiunta illustra in che modo è possibile estendere il database Catalogo perché sia in grado di supportare dati aggiuntivi specifici dell'applicazione.
- **Aprire la pagina degli eventi per il nuovo tenant**: la pagina degli eventi *Bushwillow Blues* si apre nel browser.

   ![eventi](media/saas-multitenantdb-provision-and-catalog/bushwillow.png)

#### <a name="debugger-steps"></a>Passaggi del debugger

Per comprendere in che modo l'app Wingtip implementi il nuovo provisioning di tenant in un database condiviso, procedere passo per passo attraverso il flusso di lavoro dopo aver aggiunto un punto di interruzione:

1. In *PowerShell ISE* aprire ...\\Learning Modules\\ProvisionTenants\\*Demo-ProvisionTenants.ps1* e impostare i parametri seguenti:
   - **$TenantName** = **Bushwillow Blues**, nome di una nuova sede di eventi.
   - **$VenueType** = **blues**, uno dei tipi di sede predefiniti: blues, classicalmusic, dance, jazz, judo, motorracing, multipurpose, opera, rockmusic, soccer (in minuscolo e senza spazi).
   - **$DemoScenario** = **1** per eseguire il provisioning di un tenant in un database condiviso con altri tenant.

2. Aggiungere un punto di interruzione posizionando il cursore in un punto qualsiasi nella riga 38 contenente il testo: *New-Tenant `* e premere **F9**.

   ![punto di interruzione](media/saas-multitenantdb-provision-and-catalog/breakpoint.png)

3. Eseguire lo script premendo **F5**.

4. Quando l'esecuzione dello script si arresta in corrispondenza del punto di interruzione, premere **F11** per eseguire l'istruzione del codice.

   ![debug](media/saas-multitenantdb-provision-and-catalog/debug.png)

5. Tenere traccia dell'esecuzione dello script usando le opzioni del menu **Debug** **F10** e **F11** per eseguire le istruzioni delle funzioni chiamate.

Per altre informazioni sul debug degli script di PowerShell, vedere [Suggerimenti per l'utilizzo e il debug degli script di PowerShell](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise).

## <a name="provision-a-tenant-in-its-own-database"></a>Effettuare il provisioning di un tenant in un database *autonomo*

#### <a name="major-actions-of-provisioning"></a>Azioni principali del provisioning

Di seguito sono indicati gli elementi chiave del flusso di lavoro di provisioning che si esegue quando si tiene traccia dello script:

- **Calcolare la nuova chiave del tenant**: viene usata una funzione hash per creare la chiave del tenant dal nome del tenant.
- **Controllare se la chiave del tenant esiste già**: il catalogo viene controllato per verificare che la chiave non sia già stata registrata.
- **Creare un nuovo database del tenant**: il database viene creato copiando il database *basetenantdb* tramite un modello di Resource Manager.  Il nuovo nome del database si basa sul nome del tenant.
- **Aggiungere il database al catalogo**: il nuovo database tenant è registrato come una partizione nel catalogo.
- **Inizializzare il tenant nel database del tenant predefinito**: il database del tenant viene aggiornato per aggiungere le nuove informazioni del tenant.  
- **Registrare il tenant nel catalogo**: il mapping tra la nuova chiave del tenant e il database *sequoiasoccer* viene aggiunto al catalogo.
- **Il nome del tenant viene aggiunto al catalogo**: il nome della sede viene aggiunto alla tabella di estensione Tenants nel catalogo.
- **Aprire la pagina degli eventi per il nuovo tenant**: la pagina degli eventi *Sequoia Soccer* si apre nel browser.

   ![eventi](media/saas-multitenantdb-provision-and-catalog/sequoiasoccer.png)

#### <a name="debugger-steps"></a>Passaggi del debugger

A questo punto seguire dettagliatamente il processo dello script durante la creazione di un tenant nel database autonomo:

1. Ancora in ...\\Learning Modules\\ProvisionTenants\\*Demo-ProvisionTenants.ps1* impostare i parametri seguenti:
   - **$TenantName** = **Sequoia Soccer**, il nome di una nuova sede di eventi.
   - **$VenueType** = **soccer**, uno dei tipi di sede predefiniti: blues, classicalmusic, dance, jazz, judo, motorracing, multipurpose, opera, rockmusic, soccer (in minuscolo e senza spazi).
   - **$DemoScenario** = **2** per effettuare il provisioning di un tenant in un database autonomo.

2. Aggiungere un nuovo punto di interruzione posizionando il cursore in un punto qualsiasi nella riga 57 contenente il testo: *&&nbsp;$PSScriptRoot\New-TenantAndDatabase `* e premere **F9**.

   ![punto di interruzione](media/saas-multitenantdb-provision-and-catalog/breakpoint2.png)

3. Eseguire lo script premendo **F5**.

4. Quando l'esecuzione dello script si arresta in corrispondenza del punto di interruzione, premere **F11** per eseguire l'istruzione del codice.  Usare **F10** e **F11** per proseguire ed eseguire l'istruzione relativa alle funzioni per tracciare l'esecuzione.

## <a name="provision-a-batch-of-tenants"></a>Eseguire il provisioning di un batch di tenant

Questo esercizio descrive come effettuare il provisioning di un batch di 17 tenant. È consigliabile eseguire il provisioning di questo batch di tenant prima di iniziare le altre esercitazioni di Wingtip Tickets, in modo da avere più database da usare.

1. In *PowerShell ISE* aprire ...\\Learning Modules\\ProvisionTenants\\*Demo-ProvisionTenants.ps1* e impostare il parametro *$DemoScenario* su 4:
   - **$DemoScenario** = **4** per effettuare il provisioning di un batch di tenant in un database condiviso.

2. Premere **F5** ed eseguire lo script.

### <a name="verify-the-deployed-set-of-tenants"></a>Verificare il set di tenant distribuito 

A questo punto l'utente dispone di diversi tenant distribuiti in un database condiviso e tenant distribuiti in database dedicati. Il portale di Azure può essere usato per controllare i database creati. Nel [portale di Azure](https://portal.azure.com) aprire il server **tenants1-mt-\<UTENTE\>** passando all'elenco di server SQL.  L'elenco di **database SQL** deve includere il database **tenants1** condiviso e i database per i tenant che si trovano nel proprio database:

   ![elenco di database](media/saas-multitenantdb-provision-and-catalog/Databases.png)

Sebbene il portale di Azure mostri il tenant di database, non è possibile vedere i tenant *all'interno* del database condiviso. L'elenco completo dei tenant è consultabile nella pagina Web dell'**hub eventi** di Wingtip ed esplorando il catalogo.

#### <a name="using-wingtip-tickets-events-hub-page"></a>Uso della pagina dell'hub eventi di Wingtip Tickets

Aprire la pagina Hub eventi nel browser (http:events.wingtip-mt.\<UTENTE\>.trafficmanager.net)  

#### <a name="using-catalog-database"></a>Uso del database di catalogo

L'elenco completo dei tenant e i database corrispondenti a ciascuno è disponibile nel catalogo. Viene fornita una vista SQL che associa il nome del tenant al nome del database. Questa vista dimostra correttamente il valore dell'estensione dei metadati archiviati nel catalogo.
- La vista SQL è disponibile nel database tenantcatalog.
- Il nome del tenant viene archiviato nella tabella Tenants.
- Il nome del database viene archiviato nelle tabelle di gestione delle partizioni.

1. In SQL Server Management Studio (SSMS) connettersi al server del tenant in **catalog-mt.\<UTENTE\>.database.windows.net**, con l'account di accesso **developer** e la password **P@ssword1**

    ![Finestra di dialogo di connessione di SQL Server Management Studio](media/saas-multitenantdb-provision-and-catalog/SSMSConnection.png)

2. In Esplora oggetti di SSMS selezionare le viste del database *tenantcatalog*.

3. Fare clic con il pulsante destro del mouse sulla vista *TenantsExtended* e scegliere **Select Top 1000 Rows** (Seleziona le prime 1000 righe). Si noti il mapping tra nome e il database del tenant per diversi tenant.

    ![Visualizzazione ExtendedTenants in SQL Server Management Studio](media/saas-multitenantdb-provision-and-catalog/extendedtenantsview.png)
      
## <a name="other-provisioning-patterns"></a>Altri modelli di provisioning

Questa sezione presenta altri interessanti modelli di provisioning.

#### <a name="pre-provisioning-databases-in-elastic-pools"></a>Pre-provisioning di database in pool elastici

Il modello di pre-provisioning sfrutta il fatto che quando si usa un pool elastico, la fatturazione dipende dal pool e non dai database. Pertanto è possibile aggiungere i database a un pool elastico prima che siano necessari senza incorrere in costi aggiuntivi. Il pre-provisioning riduce notevolmente il tempo impiegato per effettuare il provisioning di un tenant in un database. Il numero di database di cui viene effettuato il pre-provisioning può essere modificato in base alle esigenze, per ottenere un buffer adatto alla frequenza di provisioning prevista.

#### <a name="auto-provisioning"></a>Provisioning automatico

In questo modello viene usato un servizio di provisioning dedicato per eseguire il provisioning di server, pool e database automaticamente in base alle esigenze. Questa automazione include il pre-provisioning di database in pool elastici. In caso di ritiro ed eliminazione di database, i vuoti creati nei pool elastici possono essere riempiti dal servizio di provisioning in base alle esigenze.

Questo tipo di servizio automatizzato può essere semplice o complesso. Ad esempio l'automazione può gestire il provisioning in più aree geografiche e può includere la configurazione della replica geografica per il ripristino di emergenza. Con il modello di provisioning automatico, un'applicazione client o uno script invia una richiesta di provisioning a una coda per l'elaborazione da parte del servizio di provisioning. Lo script eseguirà quindi il polling per rilevare il completamento. Se si usa il pre-provisioning, le richieste vengono gestite rapidamente ed è un servizio in background a gestire il provisioning di un database sostitutivo.

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

