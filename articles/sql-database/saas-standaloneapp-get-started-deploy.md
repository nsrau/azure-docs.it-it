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
ms.date: 11/30/2017
ms.author: genemi
ms.openlocfilehash: d38cd108821bce05824732bbdbdd322ae8563bde
ms.sourcegitcommit: be0d1aaed5c0bbd9224e2011165c5515bfa8306c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2017
---
# <a name="deploy-and-explore-a-standalone-single-tenant-application-that-uses-azure-sql-database"></a>Distribuire ed esplorare un'applicazione autonoma a singolo tenant che usa il database SQL di Azure

Questa esercitazione illustra come distribuire ed esplorare l'applicazione SaaS autonoma Wingtip Tickets. L'applicazione è progettata per dimostrare le funzionalità del database SQL di Azure che semplificano l'abilitazione degli scenari SaaS.

Il modello di applicazione autonoma distribuisce un gruppo di risorse di Azure contenente un'applicazione a singolo tenant e un database a singolo tenant per ogni tenant.  È possibile effettuare il provisioning di più istanze dell'applicazione per offrire una soluzione multi-tenant.

In questa esercitazione vengono distribuiti gruppi di risorse per più tenant nella sottoscrizione di Azure.  Questo modello consente la distribuzione di gruppi di risorse nella sottoscrizione di Azure di un tenant. Azure offre programmi partner che consentono la gestione di questi gruppi di risorse da parte di un provider di servizi per conto del tenant. Il provider di servizi è l'amministratore nella sottoscrizione del tenant.

Nella sezione successiva relativa alla distribuzione sono disponibili tre pulsanti **Distribuisci in Azure** blu. Ogni pulsante consente di distribuire un'istanza diversa dell'applicazione. Ogni istanza è personalizzata per un tenant specifico. Quando ogni pulsante viene selezionato, l'applicazione corrispondente viene distribuita completamente cinque minuti dopo.  Le app vengono distribuite nella sottoscrizione di Azure specificata.  Si ottiene l'accesso completo per l'esplorazione e l'uso dei singoli componenti dell'applicazione.

Il codice sorgente e gli script di gestione dell'applicazione sono disponibili nel repository [WingtipTicketsSaaS-StandaloneApp](https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp) di GitHub.


In questa esercitazione si apprenderà:

> [!div class="checklist"]
> * Come distribuire l'applicazione SaaS autonoma Wingtip Tickets
> * Dove ottenere gli script di gestione e il codice sorgente dell'applicazione
> * Informazioni su server e database che costituiscono l'app

Verranno rese disponibili esercitazioni aggiuntive che consentiranno di esplorare diversi scenari di gestione basati su questo modello di applicazione.   

## <a name="deploy-the-wingtip-tickets-saas-standalone-application"></a>Distribuire l'applicazione SaaS autonoma Wingtip Tickets

Distribuire l'app per i tre tenant specificati:

1. Fare clic su ogni pulsante **Distribuisci in Azure** blu per aprire il modello di distribuzione nel [portale di Azure](https://portal.azure.com). Ogni modello richiede due valori di parametro, un nome per un nuovo gruppo di risorse e un nome utente che distingue questa distribuzione da altre distribuzioni dell'app. Il passaggio seguente fornisce dettagli per l'impostazione di questi valori.<br><br>
    <a href="http://aka.ms/deploywingtipsa-contoso" target="_blank"><img style="vertical-align:middle" src="media/saas-standaloneapp-get-started-deploy/deploy.png"/></a> &nbsp; **Contoso Concert Hall**
<br><br>
    <a href="http://aka.ms/deploywingtipsa-dogwood" target="_blank"><img style="vertical-align:middle" src="media/saas-standaloneapp-get-started-deploy/deploy.png"/></a> &nbsp; **Dogwood Dojo**
<br><br>
    <a href="http://aka.ms/deploywingtipsa-fabrikam" target="_blank"><img style="vertical-align:middle" src="media/saas-standaloneapp-get-started-deploy/deploy.png"/></a> &nbsp; **Fabrikam Jazz Club**

2. Immettere i valori dei parametri necessari per ogni distribuzione.

    > [!IMPORTANT]
    > Alcune impostazioni di autenticazione e per i firewall server sono intenzionalmente non protette a scopo dimostrativo. **Creare un nuovo gruppo di risorse** per ogni distribuzione di applicazione.  Non usare un gruppo di risorse esistente. Non utilizzare l'applicazione o le risorse che crea per la produzione. Eliminare tutti i gruppi di risorse quando non è più necessario usare le applicazioni, per interrompere la fatturazione correlata.

    Nei nomi delle risorse è preferibile usare solo lettere minuscole, numeri e trattini.
    * Per **Gruppo di risorse** selezionare **Crea nuovo** e quindi specificare un valore in minuscolo in **Nome**.
        * È consigliabile aggiungere un trattino, seguito dalle proprie iniziali e quindi da una cifra, ad esempio, *wingtip-sa-af1*.
        * Selezionare un **percorso** nell'elenco a discesa.

    * Per **Utente** è consigliabile scegliere un valore breve, ad esempio le iniziali seguite da una cifra, ad esempio *af1*.


3. **Distribuire l'applicazione**.

    * Fare clic per accettare i termini e le condizioni.
    * Fare clic su **Acquista**.

4. Monitorare lo stato delle tre distribuzioni facendo clic su **Notifiche** (l'icona a forma di campanella a destra della casella di ricerca). La distribuzione dell'app richiede cinque minuti.


## <a name="run-the-application"></a>Eseguire l'applicazione

L'app illustra le sedi che ospitano gli eventi. I tipi di sede includono sale concerto, jazz club e club sportivi. Le sedi rappresentano i clienti dell'app Wingtip Tickets. Nell'app Wingtip Tickets le sedi sono registrate come *tenant*. Una sede registrata come tenant consente la visualizzazione dell'elenco di eventi e la vendita dei biglietti ai clienti. Ogni sede è associata a un sito Web personalizzato in cui sono elencati i relativi eventi e dove è possibile la vendita dei biglietti. Ogni tenant è indipendente e isolato rispetto agli altri tenant. Dietro le quinte, ogni tenant ottiene un'istanza di applicazione separata associata a un proprio database SQL autonomo.

1. Aprire la pagina degli eventi per ognuno dei tre tenant in schede separate del browser:

    - http://events.contosoconcerthall.&lt;USER&gt;.trafficmanager.net
    - http://events.dogwooddojo.&lt;USER&gt;.trafficmanager.net
    - http://events.fabrikamjazzclub.&lt;USER&gt;.trafficmanager.net

    In ogni URL sostituire &lt;USER&gt; con il valore relativo all'utente della distribuzione specifica.

   ![Events](./media/saas-standaloneapp-get-started-deploy/fabrikam.png)

Per controllare la distribuzione delle richieste in ingresso, l'app usa [*Gestione traffico di Microsoft Azure*](../traffic-manager/traffic-manager-overview.md). Ogni istanza dell'app specifica del tenant include il nome del tenant come parte del nome di dominio nell'URL. Gli URL di tutti i tenant includono valori di **User** specifici. Gli URL hanno il formato seguente:
- http://events.&lt;nomesede&gt;.&lt;USER&gt;.trafficmanager.net

La **posizione** del database di ogni tenant è inclusa nelle impostazioni dell'app distribuita corrispondente.

In un ambiente di produzione si crea in genere un record CNAME DNS per [*configurare un dominio Internet aziendale in modo che faccia riferimento*](../traffic-manager/traffic-manager-point-internet-domain.md) all'URL del profilo di Gestione traffico.


## <a name="explore-the-servers-and-tenant-databases"></a>Esplorare i server e i database tenant

È possibile esaminare alcune risorse distribuite:

1. Nel [portale di Azure](http://portal.azure.com) passare all'elenco dei gruppi di risorse.
2. Vedere il gruppo di risorse **wingtip-sa-catalog-&lt;USER&gt;**.
    - In questo gruppo di risorse viene distribuito il server **catalog-sa-&lt;USER&gt;**. Il server contiene il database **tenantcatalog**.
    - Dovrebbero essere visualizzati anche i tre gruppi di risorse del tenant.
3. Aprire il gruppo di risorse **wingtip-sa-fabrikam-&lt;UTENTE&gt;**, che contiene le risorse per la distribuzione di Fabrikam Jazz Club.  Il server **fabrikamjazzclub-&lt;UTENTE&gt;** contiene il database **fabrikamjazzclub**.

Ogni database del tenant è un database *autonomo* con 50 DTU.

## <a name="additional-resources"></a>Risorse aggiuntive

<!--
* Additional [tutorials that build on the Wingtip SaaS application](sql-database-wtp-overview.md#sql-database-wingtip-saas-tutorials)
* To learn about elastic pools, see [*What is an Azure SQL elastic pool*](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool)
* To learn about elastic jobs, see [*Managing scaled-out cloud databases*](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-jobs-overview)
-->

- Per informazioni sulle applicazioni SaaS multi-tenant, vedere [Modelli di progettazione per le applicazioni SaaS multi-tenant](saas-tenancy-app-design-patterns.md).


## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso:

> [!div class="checklist"]
> * Come distribuire l'applicazione SaaS autonoma Wingtip Tickets
> * Informazioni su server e database che costituiscono l'app
> * Come eliminare le risorse di esempio per interrompere la fatturazione correlata

