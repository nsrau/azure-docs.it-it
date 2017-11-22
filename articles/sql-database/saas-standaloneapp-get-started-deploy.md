---
title: Esercitazione sulle app SaaS multi-tenant - Database SQL di Azure | Microsoft Docs
description: Distribuire ed esplorare un'applicazione SaaS autonoma a singolo tenant che usa il database SQL di Azure.
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
ms.date: 11/14/2017
ms.author: sstein
ms.openlocfilehash: ce0268f800dcf1900730d6ad9c476fb06320a79e
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2017
---
# <a name="deploy-and-explore-a-standalone-single-tenant-application-that-uses-azure-sql-database"></a>Distribuire ed esplorare un'applicazione autonoma a singolo tenant che usa il database SQL di Azure

Questa esercitazione illustra come distribuire ed esplorare l'applicazione SaaS autonoma Wingtip Tickets. L'applicazione è progettata per dimostrare le funzionalità del database SQL di Azure che semplificano l'abilitazione degli scenari SaaS.

Il modello di applicazione autonoma distribuisce un gruppo di risorse di Azure contenente un'applicazione a singolo tenant e un database a singolo tenant per ogni tenant.  È possibile effettuare il provisioning di più istanze dell'applicazione per offrire una soluzione multi-tenant.

Anche se in questa esercitazione vengono distribuiti gruppi di risorse per alcuni tenant nella sottoscrizione di Azure, questo modello consente la distribuzione dei gruppi di risorse nella sottoscrizione di Azure di un tenant.  Azure offre programmi partner che consentono la gestione di questi gruppi di risorse da parte di un provider di servizi per conto del tenant come amministratore nella sottoscrizione del tenant.

Nella sezione relativa alla distribuzione sono disponibili tre pulsanti Distribuisci in Azure, ognuno dei quali distribuisce un'istanza diversa dell'applicazione personalizzata per un tenant specifico. Quando ogni pulsante viene selezionato, l'applicazione corrispondente viene distribuita completamente cinque minuti dopo.  Le app vengono distribuite nella sottoscrizione di Azure specificata.  Si ottiene l'accesso completo per l'esplorazione e l'uso dei singoli componenti dell'applicazione.

Il codice sorgente e gli script di gestione dell'applicazione sono disponibili nel repository [WingtipTicketsSaaS-StandaloneApp](https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp) di GitHub.


In questa esercitazione si apprenderà:

> [!div class="checklist"]

> * Come distribuire l'applicazione SaaS autonoma Wingtip Tickets
> * Dove ottenere il codice sorgente e gli script di gestione dell'applicazione
> * Informazioni su server e database che costituiscono l'app

Esercitazioni aggiuntive verranno rese disponibili in futuro e consentiranno di esplorare diversi scenari di gestione basati su questo modello di applicazione.   

## <a name="deploy-the-wingtip-tickets-saas-standalone-application"></a>Distribuire l'applicazione SaaS autonoma Wingtip Tickets

Distribuire l'app per i tre tenant specificati:

1. Fare clic su ogni pulsante **Distribuisci in Azure** per aprire il modello di distribuzione nel portale di Azure. Ogni modello richiede due valori di parametro, un nome per un nuovo gruppo di risorse e un nome utente che distingue questa distribuzione da altre distribuzioni dell'app. Il passaggio seguente fornisce dettagli per l'impostazione di questi valori.<br><br>
    <a href="http://aka.ms/deploywingtipsa-contoso" target="_blank"><img style="vertical-align:middle" src="media/saas-standaloneapp-get-started-deploy/deploy.png"/></a>     &nbsp;&nbsp;**Contoso Concert Hall**
<br><br>
    <a href="http://aka.ms/deploywingtipsa-dogwood" target="_blank"><img style="vertical-align:middle" src="media/saas-standaloneapp-get-started-deploy/deploy.png"/></a> &nbsp;&nbsp;**Dogwood Dojo**
<br><br>
    <a href="http://aka.ms/deploywingtipsa-fabrikam" target="_blank"><img style="vertical-align:middle" src="media/saas-standaloneapp-get-started-deploy/deploy.png"/></a>    &nbsp;&nbsp;**Fabrikam Jazz Club**

2. Immettere i valori dei parametri necessari per ogni distribuzione.

    > [!IMPORTANT]
    > Alcune impostazioni di autenticazione e per i firewall server sono intenzionalmente non protette a scopo dimostrativo. **Creare un nuovo gruppo di risorse** per ogni distribuzione di applicazione.  Non usare un gruppo di risorse esistente. Non utilizzare l'applicazione o le risorse che crea per la produzione. Eliminare tutti i gruppi di risorse quando non è più necessario usare le applicazioni, per interrompere la fatturazione correlata.

    È consigliabile usare solo lettere minuscole, numeri e trattini nei nomi delle risorse.
    * Per **Gruppo di risorse** selezionare Crea nuovo e quindi specificare un nome per il gruppo di risorse, rispettando la distinzione tra maiuscole e minuscole.
        * È consigliabile che tutte le lettere del nome del gruppo di risorse siano minuscole.
        * È consigliabile aggiungere un trattino, seguito dalle proprie iniziali e quindi da una cifra, ad esempio, _wingtip-sa-af1_.
        * Selezionare un percorso nell'elenco a discesa.

    * Per **Utente** è consigliabile scegliere un valore breve per l'utente, come le proprie iniziali e una cifra, ad esempio, _af1_.


1. **Distribuire l'applicazione**.

    * Fare clic per accettare i termini e le condizioni.
    * Fare clic su **Acquista**.

1. Monitorare lo stato delle tre distribuzioni facendo clic su **Notifiche** (l'icona a forma di campanella a destra della casella di ricerca). La distribuzione dell'app richiede circa cinque minuti.


## <a name="run-the-application"></a>Eseguire l'applicazione

L'app presenta sedi di eventi, come sale concerto, jazz club e club sportivi. Queste sedi vengono registrate come clienti (o tenant) di Wingtip Tickets, per presentare elenchi di eventi e vendere i biglietti con facilità. Ogni sede di eventi ottiene un sito Web personalizzato per gestire ed elencare gli eventi e vendere i biglietti, in modo indipendente e isolato dagli altri tenant. Dietro le quinte, ogni tenant ottiene un'istanza di applicazione separata e un database SQL autonomo.

1. Aprire la pagina degli eventi per ognuno dei tre tenant in schede separate del browser:

    http://events.contosoconcerthall.&lt;USER&gt;.trafficmanager.net <br>
    http://events.dogwooddojo.&lt;USER&gt;.trafficmanager.net<br>
    http://events.fabrikamjazzclub.&lt;USER&gt;.trafficmanager.net

    Sostituire &lt;USER&gt; con il valore relativo all'utente della distribuzione specifica.

   ![Events](./media/saas-standaloneapp-get-started-deploy/fabrikam.png)


Per controllare la distribuzione delle richieste in ingresso, l'app usa [*Gestione traffico di Microsoft Azure*](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview). Ogni app specifica del tenant include il nome del tenant come parte del nome di dominio nell'URL. Tutti gli URL dei tenant includono il valore *Utente* specifico nel formato: http://events.&lt;nomelocation&gt;.&lt;UTENTE&gt;.trafficmanager.net. La posizione del database di ogni tenant è inclusa nelle impostazioni dell'app distribuita corrispondente.

In un ambiente di produzione si crea un genere un record CNAME DNS per [*configurare un dominio Internet aziendale in modo che faccia riferimento*](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-point-internet-domain) all'URL del profilo di Gestione traffico.


## <a name="explore-the-servers-and-tenant-databases"></a>Esplorare i server e i database del tenant

È possibile esaminare alcune risorse distribuite:

1. Nel [portale di Azure](http://portal.azure.com) passare all'elenco di gruppi di risorse.  Dovrebbe essere visualizzato il gruppo di risorse **wingtip-sa-catalog-&lt;UTENTE&gt;** in cui il server **catalog-sa-&lt;UTENTE&gt;** viene distribuito con il database **tenantcatalog**. Dovrebbero essere visualizzati anche i tre gruppi di risorse del tenant.

1. Aprire il gruppo di risorse **wingtip-sa-fabrikam-&lt;UTENTE&gt;**, che contiene le risorse per la distribuzione di Fabrikam Jazz Club.  Il server **fabrikamjazzclub-&lt;UTENTE&gt;** contiene il database **fabrikamjazzclub**.


Ogni database del tenant è un database _autonomo_ con 50 DTU.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso:

> [!div class="checklist"]

> * Come distribuire l'applicazione SaaS autonoma Wingtip Tickets
> * Informazioni su server e database che costituiscono l'app
> * Come eliminare le risorse di esempio per interrompere la fatturazione correlata


## <a name="additional-resources"></a>Risorse aggiuntive

<!--* Additional [tutorials that build on the Wingtip SaaS application](sql-database-wtp-overview.md#sql-database-wingtip-saas-tutorials)
* To learn about elastic pools, see [*What is an Azure SQL elastic pool*](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool)
* To learn about elastic jobs, see [*Managing scaled-out cloud databases*](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-jobs-overview) -->
* Per informazioni sulle applicazioni SaaS multi-tenant, vedere [*Modelli di progettazione per le applicazioni SaaS multi-tenant*](saas-tenancy-app-design-patterns.md)
