---
title: Ripristinare un database SQL di Azure in un'app SaaS multi-tenant | Microsoft Docs
description: Informazioni su come ripristinare un database SQL a tenant singolo dopo l'eliminazione accidentale di dati
keywords: esercitazione database SQL
services: sql-database
documentationcenter: 
author: stevestein
manager: craigg
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: billgib;sstein
ms.openlocfilehash: 60788b8a1b417e9bdfbe5ea8424dff9a39f36f2d
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2017
---
# <a name="restore-a-single-tenants-azure-sql-database-in-a-multi-tenant-saas-app"></a>Ripristinare un database SQL di Azure a tenant singolo in un'app SaaS multi-tenant

L'applicazione SaaS Wingtip viene compilata usando un modello database per ogni tenant, in cui ogni tenant dispone di un proprio database. Uno dei vantaggi di questo modello è che è facile ripristinare i dati di un singolo tenant in isolamento senza alcun impatto sugli altri tenant.

Questa esercitazione illustra due modelli di ripristino dei dati:

> [!div class="checklist"]

> * Ripristinare un database in un database parallelo (side-by-side)
> * Ripristinare un database sul posto


|||
|:--|:--|
| **Ripristinare un tenant a un punto precedente nel tempo, in un database parallelo** | Questo modello può essere usato dal tenant per la revisione, il controllo, la conformità e così via. Il database originale rimane online e invariato. |
| **Ripristinare un tenant sul posto** | Questo modello viene in genere usato per ripristinare un tenant a un punto precedente nel tempo, dopo che il tenant ha accidentalmente eliminato i dati. Il database originale viene portato offline e sostituito con il database ripristinato. |
|||

Per completare questa esercitazione, verificare che siano soddisfatti i prerequisiti seguenti:

* L'app SaaS Wingtip viene distribuita. Per distribuire in meno di cinque minuti, vedere [Distribuire ed esplorare l'applicazione SaaS Wingtip](saas-dbpertenant-get-started-deploy.md)
* Azure PowerShell è installato. Per informazioni dettagliate, vedere [Introduzione ad Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)

## <a name="introduction-to-the-saas-tenant-restore-pattern"></a>Introduzione al modello di ripristino di tenant SaaS

Per il modello di ripristino di tenant, sono disponibili due modelli semplici per il ripristino dei dati di un tenant singolo. Poiché i database dei tenant sono isolati tra loro, il ripristino di un tenant non ha alcun impatto sui dati di qualsiasi altro tenant.

Nel primo modello i dati vengono ripristinati in un nuovo database. Al tenant viene quindi dato l'accesso al database insieme ai relativi dati di produzione. Questo modello consente a un amministratore di tenant di esaminare i dati ripristinati e potenzialmente usarli per sovrascrivere in modo selettivo i valori dei dati correnti. Spetta alla progettazione delle app SaaS determinare quanto dovrebbero essere sofisticate le opzioni di ripristino dei dati. In alcuni scenari potrebbe essere necessaria solamente la possibilità di esaminare i dati nello stato in cui si trovavano in un determinato punto nel tempo. Se il database usa la [replica geografica](sql-database-geo-replication-overview.md), è consigliabile copiare i dati necessari dalla copia ripristinata nel database originale. Se il database originale viene sostituito con il database ripristinato, è necessario riconfigurare e risincronizzare la replica geografica.

Nel secondo modello, in cui si presuppone che il tenant abbia subito una perdita o un danneggiamento dei dati, viene ripristinato il database di produzione del tenant a un punto precedente nel tempo. Nel modello di ripristino sul posto il tenant viene portato offline per un breve periodo di tempo, mentre il database viene ripristinato e riportato online. Il database originale viene eliminato, ma comunque può essere ripristinato se si desidera tornare anche a un punto precedente nel tempo. Una variante di questo modello consiste nel rinominare il database anziché eliminarlo, anche se la ridenominazione del database non offre alcun vantaggio aggiuntivo in termini di sicurezza dei dati.

## <a name="get-the-wingtip-application-scripts"></a>Ottenere gli script dell'applicazione Wingtip

Gli script dell'app SaaS Wingtip e il codice sorgente dell'applicazione sono disponibili nel repository GitHub [WingtipSaaS](https://github.com/Microsoft/WingtipSaaS). [Procedura per scaricare gli script dell'app SaaS Wingtip](saas-dbpertenant-wingtip-app-guidance-tips.md#download-and-unblock-the-wingtip-saas-scripts).

## <a name="simulate-a-tenant-accidentally-deleting-data"></a>Simulare un tenant che elimina accidentalmente i dati

Per illustrare gli scenari di ripristino, è necessario eliminare *accidentalmente* alcuni dati in uno dei database tenant. Mentre è possibile eliminare qualsiasi record, il passaggio successivo consente di impostare la demo in modo da non essere bloccato da violazioni di integrità referenziale. Aggiunge anche alcuni dati di acquisto dei ticket che è possibile usare successivamente nelle *esercitazioni su Wingtip SaaS Analytics*.

Eseguire lo script del generatore di ticket e creare dati aggiuntivi. Il generatore di ticket intenzionalmente non acquista ticket per l'ultimo evento di ogni tenant.

1. Aprire... \\Moduli Learning\\Utilità\\*Demo-TicketGenerator.ps1* nel *PowerShell ISE*
1. Premere **F5** per eseguire lo script e generare i clienti e i dati di vendita dei ticket.


### <a name="open-the-events-app-to-review-the-current-events"></a>Aprire l'app Eventi per esaminare gli eventi correnti

1. Aprire *Hub eventi* (http://events.wtp.&lt;utente&gt;. trafficmanager.net) e fare clic su **Contoso Concert Hall**:

   ![Hub eventi](media/saas-dbpertenant-restore-single-tenant/events-hub.png)

1. Scorrere l'elenco di eventi e prendere nota dell'ultimo evento nell'elenco:

   ![ultimo evento](media/saas-dbpertenant-restore-single-tenant/last-event.png)


### <a name="run-the-demo-scenario-to-accidentally-delete-the-last-event"></a>Eseguire lo scenario demo per eliminare accidentalmente l'ultimo evento

1. Aprire... \\Moduli Learning\\Continuità aziendale e ripristino di emergenza\\RestoreTenant\\*Demo-RestoreTenant.ps1* nel *PowerShell ISE* e impostare il valore seguente:
   * **$DemoScenario** = **1**, impostare su **1** - Eliminare gli eventi senza vendita di ticket.
1. Premere **F5** per eseguire lo script ed eliminare l'ultimo evento. Verrà visualizzato un messaggio di conferma simile al seguente:

   ```Console
   Deleting unsold events from Contoso Concert Hall ...
   Deleted event 'Seriously Strauss' from Contoso Concert Hall venue.
   ```

1. Verrà visualizzata la pagina eventi Contoso. Scorrere verso il basso e verificare che l'evento non è più presente. Se l'evento è ancora presente nell'elenco, fare clic su Aggiorna e verificare che non è più presente.

   ![ultimo evento](media/saas-dbpertenant-restore-single-tenant/last-event-deleted.png)


## <a name="restore-a-tenant-database-in-parallel-with-the-production-database"></a>Ripristinare un database tenant in parallelo con il database di produzione

Questo esercizio ripristina il database Contoso Concert Hall a un punto nel tempo precedente all'eliminazione dell'evento. Dopo che l'evento è stato eliminato nella procedura precedente, si desidera ripristinare e visualizzare i dati eliminati. Non è necessario ripristinare il database di produzione con il record eliminato, ma è necessario ripristinare il precedente database per accedere ai vecchi dati per altri motivi aziendali.

 Lo script *Restore-TenantInParallel.ps1* crea un database tenant parallelo e una voce di catalogo parallela, entrambi denominati *ContosoConcertHall\_old*. Questo modello di ripristino è più adatto per il ripristino da una perdita di dati minore o per la conformità e il controllo di scenari di ripristino. È anche l'approccio consigliato quando si usa la [Replica geografica](sql-database-geo-replication-overview.md).

1. Completare la sezione [Simulare un tenant che elimina accidentalmente i dati](#simulate-a-tenant-accidentally-deleting-data).
1. Aprire... \\Moduli Learning\\Continuità aziendale e ripristino di emergenza\\RestoreTenant\\_Demo-RestoreTenant.ps1_ nel *PowerShell ISE*.
1. Impostare **$DemoScenario** = **2**, impostare questa proprietà su **2** per *ripristinare il tenant in parallelo*.
1. Premere **F5** per eseguire lo script.

Lo script ripristina il database tenant (in un database parallelo) a un punto nel tempo precedente all'eliminazione dell'evento nella sezione precedente. Crea un secondo database, rimuove i metadati di catalogo esistenti nel database e aggiunge il database al catalogo sotto la voce *ContosoConcertHall\_old*.

Lo script della demo apre la pagina eventi nel browser. Si noti dall'URL: ```http://events.wtp.&lt;user&gt;.trafficmanager.net/contosoconcerthall_old``` che vengono visualizzati i dati dal database ripristinato in cui *_old* è aggiunto al nome.

Scorrere gli eventi elencati nel browser per confermare che l'evento eliminato nella sezione precedente è stato ripristinato.

Si noti che, se si espone il tenant ripristinato come tenant aggiuntivo, con la propria app Eventi per sfogliare i ticket, è improbabile che venga fornito al tenant l'accesso ai dati ripristinati, ma serve a illustrare facilmente il modello di ripristino.

In realtà, è probabile che venga conservato solo il database ripristinato per un periodo definito. È possibile eliminare la voce tenant ripristinato dopo aver finito chiamando lo script *Remove-RestoredTenant.ps1*.

1. Impostare **$DemoScenario** su **4** per selezionare lo scenario *rimuovere tenant ripristinato*.
1. **Eseguire** **tramite** **F5**
1. La voce *ContosoConcertHall\_old* viene ora eliminata dal catalogo. Proseguire e chiudere la pagina degli eventi per questo tenant nel browser.


## <a name="restore-a-tenant-in-place-replacing-the-existing-tenant-database"></a>Ripristinare un tenant sul posto, sostituendo il database tenant esistente

Questo esercizio ripristina il tenant Contoso Concert Hall a un punto nel tempo precedente all'eliminazione dell'evento. Lo script *Restore-TenantInPlace* ripristina il database tenant corrente in un nuovo database che fa riferimento a un punto precedente nel tempo ed elimina il database originale. Questo modello di ripristino è particolarmente adatto per il ripristino da un grave danneggiamento dei dati come una perdita significativa di dati che il tenant deve gestire.

1. Aprire il file **Demo-RestoreTenant.ps1** in PowerShell ISE
1. Impostare **$DemoScenario** su **5** per selezionare lo scenario *ripristinare tenant sul posto*.
1. Eseguire tramite **F5**.

Lo script ripristina il database tenant al punto corrispondente a cinque minuti prima dell'eliminazione dell'evento. Ciò viene fatto portando prima il tenant Contoso Concert Hall offline in modo che non si verifichino altri aggiornamenti dei dati. Un database parallelo viene quindi creato tramite il ripristino dal punto di ripristino e denominato con un timestamp per garantire che il nome del database non sia in conflitto con il nome del database tenant esistente. Successivamente, il database tenant precedente viene eliminato e il database ripristinato viene rinominato con il nome del database originale. Infine, Contoso Concert Hall viene portato online per consentire all'applicazione l'accesso al database ripristinato.

Il database è stato ripristinato a un punto nel tempo precedente all'eliminazione dell'evento. Verrà visualizzata la pagina Eventi, in modo da confermare che l'ultimo evento è stato ripristinato.


## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

> [!div class="checklist"]

> * Ripristinare un database in un database parallelo (side-by-side)
> * Ripristinare un database sul posto

[Gestire lo schema del database tenant](saas-tenancy-schema-management.md)

## <a name="additional-resources"></a>Risorse aggiuntive

* Altre [esercitazioni basate sull'applicazione SaaS Wingtip](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Panoramica della continuità aziendale del database SQL di Azure](sql-database-business-continuity.md)
* [Informazioni sul backup del database SQL](sql-database-automated-backups.md)
