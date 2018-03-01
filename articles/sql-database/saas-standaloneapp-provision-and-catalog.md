---
title: Esercitazione sulle app SaaS multi-tenant - Database SQL di Azure | Microsoft Docs
description: Effettuare il provisioning di nuovi tenant e catalogarli usando il modello di applicazione autonoma
keywords: esercitazione database SQL
services: sql-database
documentationcenter: 
author: stevestein
manager: craigg
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: SaaS
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2018
ms.author: billgib
ms.openlocfilehash: a13eeb79320360da078ee19a61cc32a2e1f35354
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/22/2018
---
# <a name="provision-and-catalog-new-tenants-using-the--application-per-tenant-saas-pattern"></a>Effettuare il provisioning di nuovi tenant e catalogarli usando il modello SaaS di un'applicazione per ogni tenant

L'articolo illustra il provisioning e la catalogazione di nuovi tenant, usando il modello SaaS di un'app autonoma per ogni tenant.
L'articolo presenta due parti principali:
* Descrizione concettuale del provisioning e della catalogazione di nuovi tenant.
* Un'esercitazione che illustra un esempio di codice di PowerShell che effettua il provisioning e la catalogazione.
    * L'esercitazione usa l'applicazione SaaS di esempio Wingtip Tickets, adattata al modello di un'app autonoma per ogni tenant.

## <a name="standalone-application-per-tenant-pattern"></a>Modello di applicazione autonoma per ogni tenant
Il modello di app autonoma per ogni tenant è uno dei diversi modelli per le applicazioni SaaS multi-tenant.  In questo modello viene effettuato il provisioning di un'app autonoma per ogni tenant. L'applicazione è costituita da componenti a livello di applicazione e un database SQL.  Ogni app del tenant può essere distribuita nella sottoscrizione del fornitore.  In alternativa, Azure offre un [programma di applicazioni gestite](https://docs.microsoft.com/en-us/azure/managed-applications/overview), in base a cui un'app può essere distribuita nella sottoscrizione di un tenant e gestita dal fornitore per conto del tenant. 

   ![modello di applicazione per ogni tenant](media/saas-standaloneapp-provision-and-catalog/standalone-app-pattern.png)

Quando si distribuisce un'applicazione per un tenant, il provisioning dell'app e del database viene effettuato in un nuovo gruppo di risorse creato per il tenant.  L'utilizzo di gruppi di risorse separate consente di isolare le risorse delle applicazioni di ogni tenant e di gestirle in modo indipendente. All'interno di ogni gruppo di risorse, ogni istanza dell'applicazione viene configurata per l'accesso diretto al database corrispondente.  Questo modello di connessione è diverso da altri modelli che usano un catalogo per gestire le connessioni tra l'app e il database.  Poiché non esiste una condivisione di risorse, è necessario effettuare il provisioning di ogni database tenant con risorse sufficienti per gestire il relativo carico di picco. In genere questo modello viene usato per le applicazioni SaaS con un numero ridotto di tenant, in cui si presta particolare importanza all'isolamento dei tenant e minore attenzione ai costi delle risorse.  

## <a name="using-a-tenant-catalog-with-the-application-per-tenant-pattern"></a>Utilizzo di un catalogo di tenant con il modello di un'applicazione per ogni tenant
Sebbene l'app e il database di ogni tenant siano completamente isolati, alcuni scenari di gestione e di analisi possono usare più tenant.  Ad esempio, l'applicazione di una modifica apportata allo schema per una nuova versione dell'applicazione richiede modifiche allo schema di ogni database tenant. Anche gli scenari di reporting e di analisi possono richiedere l'accesso a tutti i database tenant indipendentemente dalla posizione in cui vengono distribuiti.

   ![modello di applicazione per ogni tenant](media/saas-standaloneapp-provision-and-catalog/standalone-app-pattern-with-catalog.png)

Il catalogo di tenant contiene un mapping tra un identificatore del tenant e un database tenant, consentendo la risoluzione di un identificatore in un nome di server e di database.  Nell'app SaaS Wingtip Tickets l'identificatore del tenant viene calcolato come hash del nome del tenant, anche se possono essere usati altri schemi.  Sebbene le applicazioni autonome non necessitino del catalogo per la gestione delle connessioni, è possibile usare il catalogo per definire l'ambito di altre azioni a un set di database tenant. Ad esempio, la query elastica può usare il catalogo per determinare il set di database attraverso cui le query vengono distribuite per il reporting tra tenant.

## <a name="elastic-database-client-library"></a>Libreria client del database elastico
Nell'applicazione di esempio Wingtip Tickets il catalogo viene implementato dalle funzionalità di gestione delle partizioni della [libreria EDCL (Elastic Database Client Library, libreria client dei database elastici)](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-elastic-database-client-library).  La libreria consente a un'applicazione di creare, gestire e usare una mappa partizioni archiviata in un database. Nell'esempio in Wingtip Tickets il catalogo viene archiviato nel database del *catalogo di tenant*.  La partizione mappa una chiave del tenant alla partizione (database) in cui sono archiviati i dati del tenant.  Le funzioni della libreria client dei database elastici gestiscono una *mappa di partizioni globale* archiviata nelle tabelle nel database del *catalogo di tenant* e una *mappa di partizioni locale* archiviata in ogni partizione.

È possibile chiamare le funzioni della libreria client dei database elastici dalle applicazioni o dagli script di PowerShell per creare e gestire le voci della mappa partizioni. Altre funzioni della libreria client dei database elastici consentono il recupero del set di partizioni o la connessione al database corretto per una specifica chiave del tenant. 
    
> [!IMPORTANT] 
> Non modificare i dati direttamente nel database di catalogo o nella mappa partizioni locale nei database tenant. Gli aggiornamenti diretti non sono supportati per l'elevato rischio di danneggiamento dei dati. Modificare, invece, i dati di mapping usando solo le API della libreria client dei database elastici.

## <a name="tenant-provisioning"></a>Provisioning dei tenant 
Ogni tenant richiede un nuovo gruppo di risorse di Azure, che è necessario creare prima di poter effettuare il provisioning delle risorse in esso contenute. Dopo aver creato il gruppo di risorse, è possibile usare un modello di Azure Resource Manager per distribuire i componenti dell'applicazione e il database e quindi configurare la connessione del database. Per inizializzare lo schema del database, il modello può importare un file BACPAC.  In alternativa è possibile creare il database come copia di un database "modello".  Il database viene quindi ulteriormente aggiornato con i dati delle sedi iniziali e registrato nel catalogo.

## <a name="tutorial"></a>Esercitazione

In questa esercitazione si apprenderà come:
* Effettuare il provisioning di un catalogo
* Registrare i database tenant di esempio distribuiti in precedenza nel catalogo
* Effettuare il provisioning di un tenant aggiuntivo e registrarlo nel catalogo

Per distribuire e configurare l'applicazione, creare il database tenant e inizializzare il database importando un file BACPAC, viene usato un modello di Azure Resource Manager. La richiesta di importazione può essere accodata per alcuni minuti prima di essere eseguita.

Al termine di questa esercitazione si disporrà di un set di applicazioni autonome di tenant e ogni database sarà registrato nel catalogo.

## <a name="prerequisites"></a>prerequisiti
Per completare questa esercitazione, verificare che i prerequisiti seguenti siano completati: 
* Azure PowerShell è installato. Per informazioni dettagliate, vedere [Introduzione ad Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)
* Le tre app di tenant di esempio sono state distribuite. Per distribuire queste app in meno di cinque minuti, vedere [Distribuire ed esplorare il modello di applicazione SaaS autonoma Wingtip Tickets](https://docs.microsoft.com/en-us/azure/sql-database/saas-standaloneapp-get-started-deploy).

## <a name="provision-the-catalog"></a>Effettuare il provisioning del catalogo
In questa attività si apprenderà come effettuare il provisioning del catalogo usato per registrare tutti i database tenant. Si apprenderà come: 
* **Effettuare il provisioning del database di catalogo** usando un modello di gestione delle risorse di Azure. Il database viene inizializzato importando un file BACPAC.  
* **Registrare le app del tenant di esempio** che sono state distribuite in precedenza.  Ogni tenant viene registrato con una chiave calcolata da un hash del nome del tenant.  Anche il nome del tenant viene archiviato in una tabella di estensione nel catalogo.

1. In PowerShell ISE aprire *...\Learning Modules\UserConfig.psm* e aggiornare il valore **\<utente\>** in base al valore usato per la distribuzione delle tre applicazioni di esempio.  **Salvare il file**.  
1. In PowerShell ISE aprire *...\Learning Modules\ProvisionTenants\Demo-ProvisionAndCatalog.ps1* e impostare **$Scenario = 1**. Distribuire il catalogo di tenant e registrare i tenant predefiniti.

1. Aggiungere un punto di interruzione posizionando il cursore in un punto qualsiasi nella riga contenente il testo `& $PSScriptRoot\New-Catalog.ps1`, quindi premere **F9**.

    ![impostazione di un punto di interruzione per l'analisi](media/saas-standaloneapp-provision-and-catalog/breakpoint.png)

1. Eseguire lo script premendo **F5**. 
1.  Quando l'esecuzione dello script si arresta in corrispondenza del punto di interruzione, premere **F11** per eseguire l'istruzione nello script New-Catalog.ps1.
1.  Analizzare l'esecuzione dello script usando le opzioni F10 e F11 del menu Debug per eseguire un'istruzione alla volta o singolarmente le varie funzioni chiamate.
    *   Per altre informazioni sul debug degli script di PowerShell, vedere [Suggerimenti per l'utilizzo e il debug degli script di PowerShell](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise).

Al completamento dello script il catalogo sarà disponibile e saranno stati registrati tutti i tenant di esempio. 

A questo punto esaminare le risorse appena create.

1. Aprire il [portale di Azure](https://portal.azure.com/) ed esplorare i gruppi di risorse.  Aprire il gruppo di risorse **wingtip-sa-catalog-\<utente\>**  e prendere nota del server e del database del catalogo.
1. Aprire il database nel portale e scegliere *Esplora dati* nel menu a sinistra.  Fare clic sul comando Accesso e quindi immettere la password =  **P@ssword1** .


1. Esplorare lo schema del database *tenantcatalog*.  
   * Gli oggetti nello schema `__ShardManagement` vengono tutti forniti dalla libreria client dei database elastici.
   * La tabella `Tenants` e la visualizzazione `TenantsExtended` sono le estensioni aggiunte nell'esempio che illustrano come è possibile estendere il catalogo per fornire valore aggiunto.
1. Eseguire la query, `SELECT * FROM dbo.TenantsExtended`.          

   ![esplora dati](media/saas-standaloneapp-provision-and-catalog/data-explorer-tenantsextended.png)

    In alternativa all'utilizzo di Esplora dati è possibile connettersi al database da SQL Server Management Studio. A tale scopo, connettersi al server wingtip- 

    
    Si noti che non è opportuno modificare i dati direttamente nel catalogo; usare sempre le API di gestione delle partizioni. 

## <a name="provision-a-new-tenant-application"></a>Effettuare il provisioning di una nuova applicazione di tenant

In questa attività si apprenderà come effettuare il provisioning di una singola applicazione di tenant. Si apprenderà come:  
* **Creare un nuovo gruppo di risorse** per il tenant. 
* **Effettuare il provisioning dell'applicazione e del database** nel nuovo gruppo di risorse usando un modello di gestione delle risorse di Azure.  Questa azione include l'inizializzazione del database con i dati di riferimento e lo schema comune importando un file BACPAC. 
* **Inizializzare il database con le informazioni di base sul tenant**. Questa azione include la specifica del tipo di sede, che determina la fotografia usata come sfondo per il sito Web degli eventi. 
* **Registrare il database nel database di catalogo**. 

1. In PowerShell ISE aprire *...\Learning Modules\ProvisionTenants\Demo-ProvisionAndCatalog.ps1* e impostare **$Scenario = 2**. Distribuire il catalogo di tenant e registrare i tenant predefiniti.

1. Aggiungere un punto di interruzione nello script posizionando il cursore in un punto qualsiasi sulla riga 49 contenente il testo `& $PSScriptRoot\New-TenantApp.ps1`, quindi premere **F9**.
1. Eseguire lo script premendo **F5**. 
1.  Quando l'esecuzione dello script si arresta in corrispondenza del punto di interruzione, premere **F11** per eseguire l'istruzione nello script New-Catalog.ps1.
1.  Analizzare l'esecuzione dello script usando le opzioni F10 e F11 del menu Debug per eseguire un'istruzione alla volta o singolarmente le varie funzioni chiamate.

Dopo che è stato eseguito il provisioning del tenant, viene aperto il sito Web degli eventi del nuovo tenant.

   ![red maple racing](media/saas-standaloneapp-provision-and-catalog/redmapleracing.png)

È quindi possibile esaminare le nuove risorse create nel portale di Azure. 

   ![risorse di red maple racing](media/saas-standaloneapp-provision-and-catalog/redmapleracing-resources.png)


## <a name="to-stop-billing-delete-resource-groups"></a>Per interrompere la fatturazione, eliminare i gruppi di risorse ##

Quando si finisce di esplorare l'esempio, è importante eliminare tutti i gruppi di risorse creati per interrompere la fatturazione correlata.

## <a name="additional-resources"></a>Risorse aggiuntive

- Per altre informazioni sulle applicazioni del database SaaS multi-tenant, vedere [Modelli di progettazione per le applicazioni SaaS multi-tenant](saas-tenancy-app-design-patterns.md).

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso:

> [!div class="checklist"]
> * Come distribuire l'applicazione SaaS autonoma Wingtip Tickets
> * Informazioni su server e database che costituiscono l'app
> * Come eliminare le risorse di esempio per interrompere la fatturazione correlata

È possibile esplorare la modalità di utilizzo del catalogo per supportare i diversi scenari tra i tenant usando la versione con un database per ogni tenant dell'[applicazione SaaS Wingtip Tickets](https://docs.microsoft.com/en-us/azure/sql-database/saas-dbpertenant-wingtip-app-overview).  
