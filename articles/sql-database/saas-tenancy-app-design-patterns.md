---
title: Criteri delle applicazioni SaaS multi-tenant - Database SQL di Azure | Microsoft Docs
description: Informazioni sui requisiti e sui criteri comuni di architettura dei dati delle applicazioni di database SaaS (Software-as-a-Service) multi-tenant in esecuzione nell'ambiente cloud di Azure.
keywords: esercitazione database SQL
services: sql-database
documentationcenter: 
author: billgib
manager: craigg
editor: MightyPen,srinia
ms.assetid: 1dd20c6b-ddbb-40ef-ad34-609d398d008a
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Active
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/12/2017
ms.author: billgib
ms.openlocfilehash: 0377baaa4a0db7e3cb2041f3ca018322e379f0df
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2017
---
# <a name="multi-tenant-saas-database-tenancy-patterns"></a>Criteri di tenancy di database delle applicazioni SaaS multi-tenant

Quando si progetta un'applicazione SaaS multi-tenant, è necessario scegliere con attenzione il modello di tenancy più adatto alle esigenze dell'applicazione.  Un modello di tenancy determina la modalità di mapping dei dati di ogni tenant alla risorsa di archiviazione.  La scelta del modello di tenancy influisce sulla progettazione e sulla gestione dell'applicazione.  In alcuni casi il passaggio a un modello diverso in un secondo momento è dispendioso.

Di seguito è riportata una discussione sui vari modelli di tenancy.

## <a name="a-how-to-choose-the-appropriate-tenancy-model"></a>R. Come scegliere il modello di tenancy appropriato

In generale, il modello di tenancy non influisce sul funzionamento di un'applicazione, ma è probabile che influisca su altri aspetti della soluzione globale.  Per valutare ogni modello vengono usati i criteri seguenti:

- **Scalabilità:**
    - Numero di tenant.
    - Archiviazione per tenant.
    - Archiviazione in modo aggregato.
    - Carico di lavoro.

- **Isolamento del tenant:** &nbsp; isolamento dei dati e prestazioni (se il carico di lavoro di un tenant influisce su altri).

- **Costo per tenant:** &nbsp; costi del database.

- **Complessità di sviluppo:**
    - Modifiche allo schema.
    - Modifiche alle query (richieste dal modello).

- **Complessità operativa:**
    - Prestazioni di monitoraggio e gestione.
    - Gestione degli schemi.
    - Ripristino di un tenant.
    - Ripristino di emergenza.

- **Personalizzabilità:**&nbsp; facilità di supporto delle personalizzazioni degli schemi specifiche del tenant o della classe del tenant.

La discussione sui tenancy è incentrata sul livello dei *dati*.  Consideriamo tuttavia per un momento il livello dell'*applicazione*.  Il livello dell'applicazione viene considerato come un'entità monolitica.  Se si divide l'applicazione in vari componenti di dimensioni inferiori, la scelta del modello di tenancy potrebbe cambiare.  È possibile trattare alcuni componenti in modo diverso rispetto ad altri in merito alla tenancy e alla tecnologia di archiviazione o alla piattaforma usata.

## <a name="b-standalone-single-tenant-app-with-single-tenant-database"></a>B. App autonoma a tenant singolo con database a tenant singolo

#### <a name="application-level-isolation"></a>Isolamento del livello dell'applicazione

In questo modello, l'intera applicazione viene installata più volte, una per ogni tenant.  Ogni istanza dell'app è un'istanza autonoma, in modo che non interagisca mai con altre istanze autonome.  Ogni istanza dell'app ha un solo tenant e pertanto necessita di un solo database.  Il database è specifico del tenant.

![Progettazione di un'app autonoma con un solo database a tenant singolo.][image-standalone-app-st-db-111a]

Ogni istanza dell'app viene installata in un gruppo di risorse di Azure distinto.  Il gruppo di risorse può appartenere a una sottoscrizione di proprietà del fornitore del software o del tenant.  In entrambi i casi, il fornitore può gestire il software per il tenant.  Ogni istanza dell'applicazione è configurata per connettersi al database corrispondente.

Ogni database tenant viene distribuito come database autonomo.  Questo modello fornisce il livello maggiore di isolamento del database.  Tuttavia, l'isolamento richiede l'allocazione di risorse sufficienti in ogni database per gestire i carichi di picco.  In questo caso è importante sottolineare che i pool elastici non possono essere usati per i database distribuiti in gruppi di risorse o in sottoscrizioni diverse.  Questa limitazione rende questo modello di app autonoma a tenant singolo la soluzione più costosa dal punto di vista dei costi generali del database.

#### <a name="vendor-management"></a>Gestione del fornitore

Il fornitore può accedere a tutti i database in tutte le istanze di app autonome, anche se sono installate in sottoscrizioni tenant diverse.  L'accesso avviene tramite connessioni SQL.  Questo accesso tra istanze consente al fornitore di centralizzare la gestione degli schemi e le query tra database per scopi di report o analisi.  Per ottenere questo tipo di gestione centralizzata, è necessario distribuire un catalogo che esegua il mapping degli identificatori dei tenant agli URI del database.  Il database SQL di Azure fornisce una libreria di partizionamento orizzontale usata insieme a un database SQL per fornire un catalogo.  La libreria di partizionamento orizzontale è denominata formalmente [Libreria client dei database elastici][docu-elastic-db-client-library-536r].

## <a name="c-multi-tenant-app-with-database-per-tenant"></a>C. App multi-tenant con database per tenant

Questo criterio successivo usa un'applicazione multi-tenant con vari database, tutti a tenant singolo.  Viene eseguito il provisioning di un nuovo database per ogni nuovo tenant.  Il livello applicazione viene *aumentato* verticalmente mediante l'aggiunta di altre risorse per ogni nodo.  In alternativa, l'app viene *scalata* orizzontalmente aggiungendo altri nodi.  Il ridimensionamento è basato sul carico di lavoro ed è indipendente dal numero o dalla scalabilità dei singoli database.

![Progettazione di app multi-tenant con database per tenant.][image-mt-app-db-per-tenant-132d]

#### <a name="customize-for-a-tenant"></a>Personalizzazione per un tenant

Come per il criterio delle app autonome, l'uso di database a tenant singolo conferisce un livello elevato di isolamento dei tenant.  In qualsiasi app il cui modello specifichi solo database a tenant singolo, lo schema per ogni singolo database specificato può essere personalizzato e ottimizzato per il relativo tenant.  Questa personalizzazione non influisce sugli altri tenant nell'app. Un tenant potrebbe necessitare di altri dati rispetto ai campi dati di base richiesti da tutti i tenant.  Il campo dati aggiuntivo potrebbe a sua volta richiedere un indice.

Grazie alla funzione di database per tenant, è semplice personalizzare lo schema per uno o più tenant singoli.  Il fornitore dell'applicazione deve definire procedure per gestire con attenzione le personalizzazioni dello schema su larga scala.

#### <a name="elastic-pools"></a>Pool elastici

Quando vengono distribuiti nello stesso gruppo di risorse, i database possono essere raggruppati in pool elastici.  I pool forniscono un modo economico per condividere le risorse tra più database.  Questa opzione di pool è più economica rispetto alla necessità per ogni database di contenere i picchi di utilizzo che si trova a gestire.  Anche se i database in pool condividono l'accesso alle risorse, possono comunque conseguire un elevato livello di isolamento delle prestazioni.

![Progettazione di app multi-tenant con database per tenant mediante pool elastico.][image-mt-app-db-per-tenant-pool-153p]

Il database SQL di Azure fornisce gli strumenti necessari per configurare, monitorare e gestire la condivisione.  Nel portale di Azure e in Log Analytics sono disponibili le metriche delle prestazioni a livello di pool e di database.  Le metriche possono fornire dettagli sulle prestazioni aggregate e specifiche del tenant.  I singoli database possono essere spostati tra i pool per fornire risorse riservate a un tenant specifico.  Questi strumenti garantiscono prestazioni ottimali in modo conveniente.

#### <a name="operations-scale-for-database-per-tenant"></a>Scalabilità delle operazioni per la funzione di database per tenant

La piattaforma di database SQL di Azure offre numerose funzionalità di gestione progettate per gestire un numero elevato di database su larga scala, ad esempio ben più di 100.000 database.  Queste funzionalità rendono plausibile il criterio di database per tenant.

Si supponga ad esempio un sistema con un database di 1.000 tenant come unico database.  Il database potrebbe avere 20 indici.  Se il sistema esegue una conversione per ottenere 1.000 database a tenant singolo, la quantità degli indici aumenta a 20.000.  Nel database SQL, nell'ambito dell'[ottimizzazione automatica][docu-sql-db-automatic-tuning-771a], le funzionalità di indicizzazione automatica sono abilitate per impostazione predefinita.  L'indicizzazione automatica gestisce tutti i 20.000 indici e le ottimizzazioni costanti di creazione ed eliminazione.  Queste azioni automatizzate si verificano all'interno di un singolo database e non vengono coordinate o limitate da azioni simili in altri database.  L'indicizzazione automatica considera gli indici in modo diverso in un database occupato rispetto a un database meno occupato.  Questo tipo di personalizzazione della gestione degli indici potrebbe risultare poco pratico a livello di database per tenant se questa significativa attività di gestione deve essere eseguita manualmente.

Altre funzionalità di gestione facilmente scalabili includono quanto segue:

- Backup predefiniti.
- Disponibilità elevata.
- Crittografia su disco.
- Dati di telemetria relativi alle prestazioni.

#### <a name="automation"></a>Automazione

Le operazioni di gestione possono essere eseguite tramite script e offerte mediante un modello [devops][ http-visual-studio-devops-485m].  Le operazioni possono anche essere automatizzate ed esposte nell'applicazione.

Ad esempio, è possibile automatizzare un ripristino temporizzato di un tenant singolo.  Il ripristino prevede soltanto il recupero del database a tenant singolo che archivia il tenant.  Questo ripristino non ha alcun impatto su altri tenant, a conferma del fatto che le operazioni di gestione si verificano a livello di granularità di ogni singolo tenant.

## <a name="d-multi-tenant-app-with-multi-tenant-databases"></a>D. App multi-tenant con database multi-tenant

Un altro criterio disponibile permette di archiviare diversi tenant in un database multi-tenant.  L'istanza dell'applicazione può avere un numero qualsiasi di database multi-tenant.  Lo schema di un database multi-tenant deve contenere una o più colonne di identificatori dei tenant, in modo che i dati di un determinato tenant possano essere recuperati in modo selettivo.  Lo schema potrebbe anche richiedere alcune tabelle o colonne utilizzate solo da un subset di tenant.  Tuttavia, codice statico e dati di riferimento vengono archiviati una sola volta e condivisi da tutti i tenant.

#### <a name="tenant-isolation-is-sacrificed"></a>L'isolamento dei tenant viene ignorato

*Dati:*&nbsp; un database multi-tenant comporta necessariamente che venga ignorato l'isolamento dei tenant.  I dati di più tenant vengono archiviati insieme in un database.  Durante lo sviluppo, verificare che le query non espongano mai i dati di più tenant.  Il database SQL supporta la [sicurezza a livello di riga][docu-sql-svr-db-row-level-security-947w], in base alla quale i dati restituiti da una query possono rientrare nell'ambito di un singolo tenant.

*Elaborazione:*&nbsp; un database multi-tenant condivide le risorse di calcolo e archiviazione tra tutti i relativi tenant.  È possibile monitorare l'intero database per verificare che le prestazioni siano accettabili.  Tuttavia, il sistema Azure non è in grado di monitorare o gestire l'uso di queste risorse da un singolo tenant per impostazione predefinita.  Il database multi-tenant rischia quindi maggiormente di incontrare elementi che potrebbero influire negativamente sulle prestazioni, in cui il carico di lavoro di un tenant iperattivo influisce sulle prestazioni di altri tenant nello stesso database.  Un livello aggiuntivo di monitoraggio a livello di applicazione potrebbe monitorare le prestazioni a livello di tenant.

#### <a name="lower-cost"></a>Costi ridotti

In generale, i database multi-tenant sono caratterizzati dal costo minimo per tenant.  I costi delle risorse per un database autonomo sono inferiori rispetto a quelli per un pool elastico di dimensioni equivalenti.  Inoltre, per i casi in cui i tenant necessitano di risorse di archiviazione limitate, è possibile archiviare milioni di tenant in un singolo database.  Nessun pool elastico può contenere milioni di database.  Tuttavia, una soluzione contenente 1.000 database per pool, con 1.000 pool, può raggiungere una scala di milioni, al rischio di comprometterne la gestibilità.

Di seguito vengono discusse due varianti di un modello di database multi-tenant, in cui il modello multi-tenant partizionato rappresenta la soluzione più flessibile e scalabile.

## <a name="e-multi-tenant-app-with-a-single-multi-tenant-database"></a>E. App multi-tenant con un singolo database multi-tenant

Il criterio di database multi-tenant più semplice usa un singolo database autonomo per ospitare i dati di tutti i tenant.  Con l'aggiunta di più tenant, il database viene aumentato con maggiori risorse di archiviazione e calcolo.  Questo aumento potrebbe essere sufficiente, sebbene esista sempre un limite di scalabilità.  Tuttavia, molto prima che venga raggiunto questo limite, il database diventa complesso da gestire.

Le operazioni di gestione incentrate sui singoli tenant sono più complesse da implementare in un database multi-tenant.  Su larga scala, queste operazioni potrebbero diventare inaccettabilmente lente.  Un esempio è rappresentato da un ripristino temporizzato dei dati per un solo tenant.

## <a name="f-multi-tenant-app-with-sharded-multi-tenant-databases"></a>F. App multi-tenant con database multi-tenant partizionati

La maggior parte delle applicazioni SaaS accede ai dati di un solo tenant alla volta.  Questo criterio di accesso consente di distribuire i dati in più database o partizioni, in cui tutti i dati di ogni tenant sono contenuti in una sola partizione.  In combinazione con un criterio di database multi-tenant, un modello partizionato consente un livello quasi illimitato di scalabilità.

![Progettazione di app multi-tenant con database multi-tenant partizionati.][image-mt-app-sharded-mt-db-174s]

#### <a name="manage-shards"></a>Gestire le partizioni

Il partizionamento orizzontale aggiunge complessità a livello di progettazione e di gestione operativa.  È necessario un catalogo in cui gestire il mapping tra i tenant e i database.  Sono anche necessarie procedure di gestione per gestire le partizioni e il popolamento dei tenant.  Ad esempio, è necessario progettare procedure per aggiungere e rimuovere le partizioni e per spostare i dati dei tenant tra le partizioni.  Una procedura di scalabilità consiste nell'aggiungere una nuova partizione e popolarla con nuovi tenant.  In altri casi si potrebbe suddividere una partizione densamente popolata in due partizioni con minore densità.  Dopo aver spostato o sospeso diversi tenant, si potrebbero unire le partizioni meno densamente popolate.  L'unione comporterebbe un uso delle risorse più economico.  Per bilanciare i carichi di lavoro, è possibile spostare anche i tenant tra le partizioni.

Il database SQL offre uno strumento di divisione/unione che opera unitamente alla libreria di partizionamento orizzontale e al database del catalogo.  L'app fornita consente di dividere e unire le partizioni e di spostare i dati dei tenant tra le partizioni.  L'app gestisce anche il catalogo durante queste operazioni, contrassegnando i tenant interessati come offline prima di spostarli.  Dopo lo spostamento, l'app aggiornerà di nuovo il catalogo con il nuovo mapping, contrassegnando il tenant come online.

#### <a name="smaller-databases-more-easily-managed"></a>Database di dimensioni ridotte più facili da gestire

Distribuendo i tenant tra più database, la soluzione multi-tenant partizionata determina una maggiore facilità di gestione dei database di dimensioni ridotte.  Ad esempio, il ripristino temporizzato di un tenant specifico prevede ora il ripristino di un singolo database di dimensioni ridotte da un backup anziché da un database di dimensioni maggiori che contiene tutti i tenant. È possibile scegliere la dimensione del database e il numero di tenant per database per bilanciare il carico di lavoro e gli impegni di gestione.

#### <a name="tenant-identifier-in-the-schema"></a>Identificatore di tenant nello schema

In base all'approccio di partizionamento orizzontale usato, potrebbero essere imposti vincoli aggiuntivi sullo schema di database.  L'applicazione di divisione/unione del database SQL richiede che lo schema includa la chiave di partizionamento orizzontale, che in genere corrisponde all'identificatore del tenant.  L'identificatore del tenant è l'elemento iniziale nella chiave primaria di tutte le tabelle partizionate.  L'identificatore del tenant consente all'applicazione di divisione/unione di individuare e spostare rapidamente i dati associati a un tenant specifico.

#### <a name="elastic-pool-for-shards"></a>Pool elastico per partizioni

I database multi-tenant partizionati possono essere inseriti in pool elastici.  In generale, includere molti database a tenant singolo in un pool è vantaggioso tanto quanto includere più tenant in alcuni database multi-tenant.  I database multi-tenant risultano vantaggiosi in presenza di un numero elevato di tenant relativamente inattivi.

## <a name="g-hybrid-sharded-multi-tenant-database-model"></a>G. Modello di database multi-tenant partizionato ibrido

Nel modello ibrido tutti i database presentano l'identificatore del tenant nello schema.  I database sono tutti in grado di archiviare più tenant e possono essere partizionati.  A livello di schema sono quindi tutti database multi-tenant.  In pratica, però, alcuni database contengono un solo tenant.  Indipendentemente da ciò, la quantità di tenant archiviati in un determinato database non ha effetto sullo schema del database.

#### <a name="move-tenants-around"></a>Spostamento dei tenant

È possibile spostare in qualsiasi momento un tenant specifico nel relativo database multi-tenant.  E sempre in qualsiasi momento è possibile cambiare idea e ripristinare il tenant in un database contenente più tenant.  È anche possibile assegnare un tenant a un nuovo database a tenant singolo quando si esegue il provisioning del nuovo database.

Il modello ibrido risulta particolarmente indicato se sussistono grandi differenze tra le esigenze di risorse dei gruppi identificabili di tenant.  Si supponga ad esempio che ai tenant che fanno parte di una versione di valutazione gratuita non venga garantito lo stesso livello di prestazioni elevate dei tenant che fanno parte di una sottoscrizione.  I criteri per i tenant nella fase della versione di valutazione gratuita potrebbero prevedere l'archiviazione in un database multi-tenant condiviso tra tutti i tenant della versione di valutazione gratuita.  Quando un tenant di una versione di valutazione gratuita esegue la sottoscrizione al livello di servizio di base, può essere spostato in un altro database multi-tenant che potrebbe avere un numero inferiore di tenant.  Un sottoscrittore che ha acquistato il livello di servizio premium potrebbe essere spostato nel nuovo database a tenant singolo corrispondente.

#### <a name="pools"></a>Pool

In questo modello ibrido i database a tenant singolo per i tenant con sottoscrizione possono essere posizionati in pool di risorse per ridurre i costi di database per ogni tenant.  Questa situazione si verifica anche nel modello di database per tenant.

## <a name="h-tenancy-models-compared"></a>H. Modelli di tenancy a confronto

La tabella seguente riepiloga le differenze tra i modelli di tenancy principali.

| Misura | App autonoma | Database per tenant | Multi-tenant partizionato |
| :---------- | :------------- | :------------------ | :------------------- |
| Scalabilità | Media<br />1-centinaia | Molto alta<br />1-centinaia di migliaia | Senza limiti<br />1-milioni |
| Isolamento dei tenant | Molto alto | Alto | Basso, ad eccezione di eventuali tenant singleton (soli in un database multi-tenant). |
| Costo di database per tenant | Alto; dimensionato per i picchi. | Basso; vengono usati i pool. | Minimo, per tenant di piccole dimensioni nei database multi-tenant. |
| Monitoraggio e gestione delle prestazioni | Solo per singolo tenant | Aggregati + per singolo tenant | Aggregati, ma per singolo tenant solo per singleton. |
| Complessità di sviluppo | Basso | Basso | Media, a causa del partizionamento orizzontale. |
| Complessità operativa | Bassa-alta. Semplice a livello individuale, complessa su larga scala. | Bassa-media. I criteri gestiscono la complessità su larga scala. | Bassa-alta. La gestione dei singoli tenant è complessa. |
| &nbsp; ||||

## <a name="next-steps"></a>Passaggi successivi

- [Deploy and explore a multi-tenant Wingtip application that uses the database-per-tenant SaaS model - Azure SQL Database][docu-sql-db-saas-tutorial-deploy-wingtip-db-per-tenant-496y] (Distribuire ed esplorare un'applicazione Wingtip multi-tenant che usa il modello SaaS di database per tenant - Database SQL di Azure)

- [Welcome to the Wingtip Tickets sample SaaS Azure SQL Database tenancy app][docu-saas-tenancy-welcome-wingtip-tickets-app-384w] (Introduzione all'app SaaS Wingtip Tickets del tenancy del database SQL di Azure di esempio)


<!--  Article link references.  -->

[http-visual-studio-devops-485m]: https://www.visualstudio.com/devops/

[docu-sql-svr-db-row-level-security-947w]: https://docs.microsoft.com/sql/relational-databases/security/row-level-security

[docu-elastic-db-client-library-536r]: sql-database-elastic-database-client-library.md
[docu-sql-db-saas-tutorial-deploy-wingtip-db-per-tenant-496y]: sql-database-saas-tutorial.md
[docu-sql-db-automatic-tuning-771a]: sql-database-automatic-tuning.md
[docu-saas-tenancy-welcome-wingtip-tickets-app-384w]: saas-tenancy-welcome-wingtip-tickets-app.md


<!--  Image references.  -->

[image-standalone-app-st-db-111a]: media/saas-tenancy-app-design-patterns/saas-standalone-app-single-tenant-database-11.png "Progettazione di un'app autonoma con un solo database a tenant singolo."

[image-mt-app-db-per-tenant-132d]: media/saas-tenancy-app-design-patterns/saas-multi-tenant-app-database-per-tenant-13.png "Progettazione di app multi-tenant con database per tenant."

[image-mt-app-db-per-tenant-pool-153p]: media/saas-tenancy-app-design-patterns/saas-multi-tenant-app-database-per-tenant-pool-15.png "Progettazione di app multi-tenant con database per tenant mediante pool elastico."

[image-mt-app-sharded-mt-db-174s]: media/saas-tenancy-app-design-patterns/saas-multi-tenant-app-sharded-multi-tenant-databases-17.png "Progettazione di app multi-tenant con database multi-tenant partizionati."

