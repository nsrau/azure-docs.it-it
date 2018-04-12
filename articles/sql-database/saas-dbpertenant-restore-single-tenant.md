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
ms.date: 04/01/2018
ms.author: sstein
ms.reviewer: billgib
ms.openlocfilehash: eb7e87934269a5e1ba453e20f6f409a10dfbda5b
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2018
---
# <a name="restore-a-single-tenant-with-a-database-per-tenant-saas-application"></a>Ripristinare un singolo tenant con un'applicazione SaaS di database per tenant

Il modello database per tenant semplifica le operazioni di ripristino temporizzato di un singolo tenant senza influire sugli altri.

Questa esercitazione illustra due modelli di ripristino dei dati:

> [!div class="checklist"]

> * Ripristino di un database in un database parallelo (side-by-side).
> * Ripristino di un database con la sostituzione del database esistente.


|||
|:--|:--|
| Ripristino in un database parallelo | Questo modello può essere usato per attività quali la revisione, il controllo e la conformità, per consentire a un tenant di controllare i dati da un punto precedente. Il database corente del tenant rimane online e invariato. |
| Ripristino con sostituzione | Questo modello viene in genere usato per ripristinare lo stato di un tenant relativo a un momento precedente, dopo che il tenant ha accidentalmente eliminato o danneggiato i dati. Il database originale viene portato offline e sostituito con il database ripristinato. |
|||

Per completare questa esercitazione, verificare che i prerequisiti seguenti siano completati:

* L'app SaaS Wingtip viene distribuita. Per eseguire la distribuzione in meno di cinque minuti, vedere [Distribuire ed esplorare l'applicazione SaaS Wingtip](saas-dbpertenant-get-started-deploy.md).
* Azure PowerShell è installato. Per informazioni dettagliate, vedere [Introduzione ad Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

## <a name="introduction-to-the-saas-tenant-restore-patterns"></a>Introduzione ai modelli di ripristino dei tenant SaaS

Per il ripristino dei dati di un tenant singolo sono disponibili due modelli semplici. Poiché i database dei tenant sono isolati tra loro, il ripristino di un tenant non ha alcun impatto sui dati di qualsiasi altro tenant. La funzionalità di ripristino temporizzato del database SQL di Azure viene usata in entrambi i modelli. PITR crea sempre un nuovo database.   

* **Ripristino in parallelo**: nel primo modello viene creato un nuovo database parallelo accanto al database corrente del tenant. Al tenant viene quindi assegnato l'accesso in sola lettura al database ripristinato. I dati ripristinati possono essere esaminati e potenzialmente usati per sovrascrivere i valori dei dati correnti. Spetta alla progettazione delle app stabilire il modo in cui il tenant deve accedere al database ripristinato e le opzioni per il ripristino disponibili. In alcuni scenari può essere sufficiente consentire al tenant di esaminare i dati di un momento precedente specifico. 

* **Ripristino con sostituzione**: il secondo modello è utile nel caso in cui il tenant voglia ripristinare uno stato precedente in seguito a una perdita o a un danneggiamento dei dati. Mentre il database viene ripristinato, il tenant viene portato offline. Il database originale viene eliminato e il database ripristinato viene rinominato. La catena di backup del database originale rimane accessibile dopo l'eliminazione. In questo modo è possibile eseguire un ripristino temporizzato, se necessario.

Se il database usa la [replica geografica](sql-database-geo-replication-overview.md) e si esegue il ripristino in parallelo, è consigliabile copiare i dati necessari dalla copia ripristinata nel database originale. Se il database originale viene sostituito con il database ripristinato, è necessario riconfigurare e risincronizzare la replica geografica.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Ottenere gli script dell'applicazione SaaS di database per tenant Wingtip Tickets

Gli script e il codice sorgente dell'applicazione del database multi-tenant SaaS Wingtip Tickets sono disponibili nel repository [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) di GitHub. Vedere le [linee guida generali](saas-tenancy-wingtip-app-guidance-tips.md) per i passaggi da seguire per scaricare e sbloccare gli script dell'app SaaS Wingtip Tickets.

## <a name="before-you-start"></a>Prima di iniziare

Quando viene creato un database, possono essere necessari da 10 a 15 minuti prima che il primo backup completo sia disponibile per il ripristino. Se l'applicazione è stata appena installata, può essere necessario attendere alcuni minuti prima di poter provare questo scenario.

## <a name="simulate-a-tenant-accidentally-deleting-data"></a>Simulare un tenant che elimina accidentalmente i dati

Per illustrare questi scenari di ripristino, è prima necessario eliminare "accidentalmente" un evento in uno dei database tenant. 

### <a name="open-the-events-app-to-review-the-current-events"></a>Aprire l'app Eventi per esaminare gli eventi correnti

1. Aprire l'hub eventi (http://events.wtp.&lt;utente&gt;.trafficmanager.net), e selezionare **Contoso Concert Hall**.

   ![Hub eventi](media/saas-dbpertenant-restore-single-tenant/events-hub.png)

2. Scorrere l'elenco di eventi e prendere nota dell'ultimo evento nell'elenco.

   ![Viene visualizzato l'ultimo evento](media/saas-dbpertenant-restore-single-tenant/last-event.png)


### <a name="accidentally-delete-the-last-event"></a>Eliminare "accidentalmente" l'ultimo evento

1. In PowerShell ISE aprire ...\\Learning Modules\\Business Continuity and Disaster Recovery\\RestoreTenant\\*Demo-RestoreTenant.ps1* e impostare il valore seguente:

   * **$DemoScenario** = **1**, *Eliminare l'ultimo evento (senza vendite di biglietti)*.
2. Premere F5 per eseguire lo script ed eliminare l'ultimo evento. Viene visualizzato il messaggio di conferma seguente:

   ```Console
   Deleting last unsold event from Contoso Concert Hall ...
   Deleted event 'Seriously Strauss' from Contoso Concert Hall venue.
   ```

3. Verrà visualizzata la pagina eventi Contoso. Scorrere verso il basso e verificare che l'evento non sia più presente. Se l'evento è ancora presente nell'elenco, selezionare **Aggiorna** e verificare che non sia più presente.

   ![Ultimo evento rimosso](media/saas-dbpertenant-restore-single-tenant/last-event-deleted.png)


## <a name="restore-a-tenant-database-in-parallel-with-the-production-database"></a>Ripristinare un database tenant in parallelo con il database di produzione

Questo esercizio ripristina il database Contoso Concert Hall a un punto nel tempo precedente all'eliminazione dell'evento. Questo scenario presuppone che si vogliano esaminare i dati eliminati in un database parallelo.

 Lo script *Restore-TenantInParallel.ps1* crea un database tenant parallelo denominato *ContosoConcertHall\_old* con una voce di catalogo parallela. Questo modello di ripristino è più adatto per il ripristino da una perdita di dati non grave. È possibile usare questo modello anche nel caso in cui sia necessario esaminare i dati a scopi di conformità o di controllo. Si tratta dell'approccio consigliato quando si usa la [replica geografica](sql-database-geo-replication-overview.md).

1. Completare la sezione [Simulare un tenant che elimina accidentalmente i dati](#simulate-a-tenant-accidentally-deleting-data).
2. In PowerShell ISE aprire ...\\Learning Modules\\Business Continuity and Disaster Recovery\\RestoreTenant\\_Demo-RestoreTenant.ps1_.
3. Impostare **$DemoScenario** = **2**, *Ripristinare il tenant in parallelo*.
4. Per eseguire lo script, premere F5.

Lo script ripristina lo stato del database tenant precedente rispetto all'eliminazione dell'evento. Il database viene ripristinato in un nuovo database, denominato _ContosoConcertHall\_old_. I metadati di catalogo esistenti nel database ripristinato vengono eliminati e quindi il database viene aggiunto al catalogo tramite una chiave calcolata dal nome *ContosoConcertHall\_old*.

Lo script della demo apre la pagina eventi per questo nuovo database tenant nel browser. Dall'URL ```http://events.wingtip-dpt.&lt;user&gt;.trafficmanager.net/contosoconcerthall_old``` si può notare che la pagina visualizza i dati dal database ripristinato con l'aggiunta di *_old* al nome.

Scorrere gli eventi elencati nel browser per confermare che l'evento eliminato nella sezione precedente è stato ripristinato.

È improbabile che si usi l'esposizione del tenant ripristinato come tenant aggiuntivo, con una propria app Eventi, come metodo per fornire al tenant l'accesso ai dati ripristinati, ma questo metodo è utile per illustrare il modello di ripristino. In genere, si concede l'accesso in sola lettura ai dati precedenti e il database viene mantenuto solo per un periodo definito. Nell'esempio, al termine è possibile eliminare la voce del tenant ripristinato eseguendo lo scenario _Rimuovere il tenant ripristinato_.

1. Impostare **$DemoScenario** = **4**, *Rimuovere il tenant ripristinato*.
2. Per eseguire lo script, premere F5.
3. La voce *ContosoConcertHall\_old* viene ora eliminata dal catalogo. Chiudere la pagina degli eventi per questo tenant nel browser.


## <a name="restore-a-tenant-in-place-replacing-the-existing-tenant-database"></a>Ripristinare un tenant sul posto, sostituendo il database tenant esistente

Questo esercizio ripristina uno stato del tenant Contoso Concert Hall precedente rispetto all'eliminazione dell'evento. Lo script *Restore-TenantInPlace* ripristina un database tenant in un nuovo database ed elimina l'originale. Questo modello di ripristino è particolarmente adatto per il ripristino dopo un grave danneggiamento dei dati, quando il tenant deve gestire una perdita significativa di dati.

1. In PowerShell ISE aprire il file **Demo-RestoreTenant.ps1**.
2. Impostare **$DemoScenario** = **5**, *Ripristinare il tenant sul posto*.
3. Per eseguire lo script, premere F5.

Lo script ripristina uno stato del database tenant precedente l'eliminazione dell'evento. Prima porta il tenant Contoso Concert Hall offline in modo che non si verifichino altri aggiornamenti. Un database parallelo viene quindi creato tramite il ripristino dal punto di ripristino e denominato con un timestamp per garantire che il nome del database non sia in conflitto con il nome del database tenant esistente. Successivamente, il database tenant precedente viene eliminato e il database ripristinato viene rinominato con il nome del database originale. Infine, Contoso Concert Hall viene portato online per consentire all'applicazione l'accesso al database ripristinato.

Il database è stato ripristinato a un punto nel tempo precedente all'eliminazione dell'evento. Quando si apre la pagina **Eventi**, verificare che l'ultimo evento sia stato ripristinato.

Dopo il ripristino del database sono necessari altri 10-15 minuti prima che il primo backup completo sia nuovamente disponibile per il ripristino. 

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

> [!div class="checklist"]

> * Ripristino di un database in un database parallelo (side-by-side).
> * Ripristinare un database con sostituzione del precedente.

Provare l'esercitazione [Gestire lo schema del database tenant](saas-tenancy-schema-management.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Altre esercitazioni basate sull'applicazione SaaS Wingtip](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Panoramica della continuità aziendale del database SQL di Azure](sql-database-business-continuity.md)
* [Informazioni sul backup del database SQL](sql-database-automated-backups.md)
