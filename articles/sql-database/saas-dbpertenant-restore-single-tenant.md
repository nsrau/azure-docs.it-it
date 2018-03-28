---
title: Ripristinare un database SQL di Azure in un'app SaaS multi-tenant | Microsoft Docs
description: Informazioni su come ripristinare un database SQL a tenant singolo dopo l'eliminazione accidentale di dati
keywords: esercitazione database SQL
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 05/10/2017
ms.author: sstein
ms.reviewer: billgib
ms.openlocfilehash: 7ae8bcb6172d9f9d56c531e149635434057fc2af
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
# <a name="restore-a-single-tenant-with-a-database-per-tenant-saas-application"></a>Ripristinare un singolo tenant con un database per ogni applicazione SaaS di tenant

Il database per modello di tenant semplifica le operazioni di ripristino di uno stato precedente di un singolo tenant senza alcun impatto sugli altri.

Questa esercitazione illustra due modelli di ripristino dei dati:

> [!div class="checklist"]

> * Ripristinare un database in un database parallelo (side-by-side)
> * Ripristinare un database sul posto, sostituendo il database esistente


|||
|:--|:--|
| **Ripristinare in un database parallelo** | Questo modello può essere utilizzato per la revisione, il controllo, la conformità e così via, per consentire a un tenant di controllare i dati da un punto precedente.  Il database corente del tenant rimane online e invariato. |
| **Ripristinare sul posto** | Questo modello viene in genere usato per ripristinare lo stato di un tenant relativo a un momento precedente, dopo che il tenant ha accidentalmente eliminato o danneggiato i dati. Il database originale viene portato offline e sostituito con il database ripristinato. |
|||

Per completare questa esercitazione, verificare che i prerequisiti seguenti siano completati:

* L'app SaaS Wingtip viene distribuita. Per distribuire in meno di cinque minuti, vedere [Distribuire ed esplorare l'applicazione SaaS Wingtip](saas-dbpertenant-get-started-deploy.md)
* Azure PowerShell è installato. Per informazioni dettagliate, vedere [Introduzione ad Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)

## <a name="introduction-to-the-saas-tenant-restore-patterns"></a>Introduzione ai modelli di ripristino dei tenant SaaS

Per il ripristino dei dati di un tenant singolo sono disponibili due modelli semplici. Poiché i database dei tenant sono isolati tra loro, il ripristino di un tenant non ha alcun impatto sui dati di qualsiasi altro tenant.  La funzionalità point-in-time-restore (PITR) del database SQL viene usata in entrambi i modelli.  PITR crea sempre un nuovo database.   

Nel primo modello, **ripristinare in parallelo**, viene creato un nuovo database parallelo insieme al database corrente del tenant. Al tenant viene quindi assegnato l'accesso in sola lettura al database ripristinato. I dati ripristinati possono essere esaminati e potenzialmente usati per sovrascrivere i valori dei dati correnti. Spetta alla progettazione delle app stabilire il modo in cui il tenant deve accedere al database ripristinato e le opzioni per il ripristino disponibili. In alcuni scenari potrebbe essere sufficiente consentire al tenant di esaminare i dati in un determinato punto nel tempo. 

Il secondo modello, **ripristinare sul posto**, è utile nel caso in cui il tenant desideri ripristinare uno stato precedente in seguito a una perdita o a un danneggiamento dei dati.  Il tenant viene portato offline mentre il database viene ripristinato. Il database originale viene eliminato e il database ripristinato viene rinominato. La catena di backup del database originale rimane accessibile dopo l'eliminazione, consentendo di ripristinare uno stato precedente del database, se necessario.

Se il database usa la [replica geografica](sql-database-geo-replication-overview.md) e il ripristino in parallelo, è consigliabile copiare i dati necessari dalla copia ripristinata nel database originale. Se il database originale viene sostituito con il database ripristinato, è necessario riconfigurare e risincronizzare la replica geografica.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Ottenere gli script dell'applicazione del database per tenant SaaS Wingtip Tickets

Gli script e il codice sorgente dell'applicazione SaaS di database multi-tenant Wingtip Tickets sono disponibili nel repository [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) di GitHub. Leggere le [linee guida generali](saas-tenancy-wingtip-app-guidance-tips.md) per i passaggi da seguire per scaricare e sbloccare gli script dell'app SaaS Wingtip Tickets.

## <a name="before-you-start"></a>Prima di iniziare

Quando viene creato un database, possono essere necessari 10-15 minuti prima che il primo backup completo sia disponibile per il ripristino.  Se l'applicazione è stata installata da poco, potrebbe essere necessario attendere alcuni minuti prima di poter provare questo scenario.

## <a name="simulate-a-tenant-accidentally-deleting-data"></a>Simulare un tenant che elimina accidentalmente i dati

Per illustrare questi scenari di ripristino, è necessario prima eliminare *"accidentalmente"* un evento in uno dei database tenant. 

### <a name="open-the-events-app-to-review-the-current-events"></a>Aprire l'app Eventi per esaminare gli eventi correnti

1. Aprire *Hub eventi* (http://events.wtp.&lt;utente&gt;. trafficmanager.net) e fare clic su **Contoso Concert Hall**:

   ![Hub eventi](media/saas-dbpertenant-restore-single-tenant/events-hub.png)

1. Scorrere l'elenco di eventi e prendere nota dell'ultimo evento nell'elenco:

   ![ultimo evento](media/saas-dbpertenant-restore-single-tenant/last-event.png)


### <a name="accidentally-delete-the-last-event"></a>Eliminare "accidentalmente" l'ultimo evento

1. Aprire... \\Moduli Learning\\Continuità aziendale e ripristino di emergenza\\RestoreTenant\\*Demo-RestoreTenant.ps1* nel *PowerShell ISE* e impostare il valore seguente:
   * **$DemoScenario** = **1**, eliminare l'ultimo evento (senza vendita di ticket).
1. Premere **F5** per eseguire lo script ed eliminare l'ultimo evento. Dovrebbe essere visualizzato il messaggio di conferma seguente:

   ```Console
   Deleting last unsold event from Contoso Concert Hall ...
   Deleted event 'Seriously Strauss' from Contoso Concert Hall venue.
   ```

1. Verrà visualizzata la pagina eventi Contoso. Scorrere verso il basso e verificare che l'evento non è più presente. Se l'evento è ancora presente nell'elenco, fare clic su Aggiorna e verificare che non è più presente.

   ![ultimo evento](media/saas-dbpertenant-restore-single-tenant/last-event-deleted.png)


## <a name="restore-a-tenant-database-in-parallel-with-the-production-database"></a>Ripristinare un database tenant in parallelo con il database di produzione

Questo esercizio ripristina il database Contoso Concert Hall a un punto nel tempo precedente all'eliminazione dell'evento. In questo scenario si presuppone che si desideri solo esaminare i dati eliminati nel database parallelo.

 Lo script *Restore-TenantInParallel.ps1* crea un database tenant parallelo denominato *ContosoConcertHall\_old* con una voce di catalogo parallela. Questo modelloè adatto soprattutto per il ripristino da una perdita di dati minore o se si ha necessità di esaminare i dati a scopo di conformità o controllo. È anche l'approccio consigliato quando si usa la [Replica geografica](sql-database-geo-replication-overview.md).

1. Completare la sezione [Simulare un tenant che elimina accidentalmente i dati](#simulate-a-tenant-accidentally-deleting-data).
1. In *PowerShell ISE* aprire ...\\Moduli Learning\\Continuità aziendale e ripristino di emergenza\\RestoreTenant\\_Demo-RestoreTenant.ps1_.
1. Impostare **$DemoScenario** = **2**, *Ripristinare il tenant in parallelo*.
1. Premere **F5** per eseguire lo script.

Lo script ripristina lo stato del database tenant precedente rispetto all'eliminazione dell'evento. Il database viene ripristinato nel nuovo database denominato _ContosoConcertHall\_old_. I metadati di catalogo esistenti nel database ripristinato vengono eliminati e il database viene aggiunto al catalogo mediante una chiave calcolata dal nome *ContosoConcertHall\_old*.

Lo script della demo apre la pagina eventi per questo nuovo database tenant nel browser. Dall'URL: ```http://events.wingtip-dpt.&lt;user&gt;.trafficmanager.net/contosoconcerthall_old``` si può notare che nella pagina vengono visualizzati i dati dal database ripristinato con l'aggiunta di *_old* al nome.

Scorrere gli eventi elencati nel browser per confermare che l'evento eliminato nella sezione precedente è stato ripristinato.

Si noti che, se si espone il tenant ripristinato come tenant aggiuntivo, con la propria app Eventi, è improbabile che venga fornito al tenant l'accesso ai dati ripristinati, ma serve a illustrare il modello di ripristino. In realtà, è probabile che venga concesso l'accesso in sola lettura ai dati precedenti e che il database venga conservato solo per un periodo definito. Nell'esempio è possibile eliminare la voce del tenant ripristinato dopo aver finito eseguendo lo scenario _Rimuovere il tenant ripristinato_.

1. Impostare **$DemoScenario** = **4**, *Rimuovere il tenant ripristinato*
1. **Eseguire** **tramite** **F5**
1. La voce *ContosoConcertHall\_old* viene ora eliminata dal catalogo. Proseguire e chiudere la pagina degli eventi per questo tenant nel browser.


## <a name="restore-a-tenant-in-place-replacing-the-existing-tenant-database"></a>Ripristinare un tenant sul posto, sostituendo il database tenant esistente

Questo esercizio ripristina uno stato del tenant Contoso Concert Hall precedente rispetto all'eliminazione dell'evento. Lo script *Restore-TenantInPlace* ripristina un database tenant in un nuovo database ed elimina l'originale.  Questo modello di ripristino è particolarmente adatto per il ripristino da un grave danneggiamento dei dati come una perdita significativa di dati che il tenant deve gestire.

1. Aprire il file **Demo-RestoreTenant.ps1** in PowerShell ISE
1. Impostare **$DemoScenario** = **5**, *Ripristinare il tenant sul posto*.
1. Eseguire tramite **F5**.

Lo script ripristina uno stato del database tenant precedente l'eliminazione dell'evento. Ciò viene fatto portando prima il tenant Contoso Concert Hall offline in modo che non si verifichino altri aggiornamenti. Un database parallelo viene quindi creato tramite il ripristino dal punto di ripristino  e denominato con un timestamp per garantire che il nome del database non sia in conflitto con il nome del database tenant esistente. Successivamente, il database tenant precedente viene eliminato e il database ripristinato viene rinominato con il nome del database originale. Infine, Contoso Concert Hall viene portato online per consentire all'applicazione l'accesso al database ripristinato.

Il database è stato ripristinato a un punto nel tempo precedente all'eliminazione dell'evento. Verrà visualizzata la pagina Eventi, in modo da confermare che l'ultimo evento è stato ripristinato.

Si noti che dopo il ripristino del database saranno necessari altri 10-15 minuti prima che il primo backup completo sia nuovamente disponibile per il ripristino. 

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

> [!div class="checklist"]

> * Ripristinare un database in un database parallelo (side-by-side)
> * Ripristinare un database sul posto

[Gestire lo schema del database tenant](saas-tenancy-schema-management.md)

## <a name="additional-resources"></a>Risorse aggiuntive

* [Altre esercitazioni basate sull'applicazione SaaS Wingtip](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Panoramica della continuità aziendale del database SQL di Azure](sql-database-business-continuity.md)
* [Informazioni sul backup del database SQL](sql-database-automated-backups.md)
