---
title: Esercitazione su SaaS a tenant singolo-database SQL di Azure
description: Distribuire ed esplorare un'applicazione SaaS autonoma a singolo tenant che usa il database SQL di Azure.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: sstein
ms.date: 11/07/2018
ms.openlocfilehash: df9c3913851055f1bb477264cf5a7486f79b56b0
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73691970"
---
# <a name="deploy-and-explore-a-standalone-single-tenant-application-that-uses-azure-sql-database"></a>Distribuire ed esplorare un'applicazione autonoma a singolo tenant che usa il database SQL di Azure

In questa esercitazione, si distribuisce ed esplora l'applicazione di esempio SaaS Wingtip Tickets sviluppata utilizzando il modello di applicazione autonoma o app per tenant.  L'applicazione è progettata per dimostrare le funzionalità del database SQL di Azure che semplificano l'abilitazione degli scenari SaaS multi-tenant.

L'applicazione autonoma o un modello di applicazione per tenant consente di distribuire un'istanza di applicazione per ogni tenant.  Ogni applicazione è configurata per un tenant specifico e distribuita in un gruppo di risorse di Azure separato. È possibile effettuare il provisioning di più istanze dell'applicazione per offrire una soluzione multi-tenant. Questo modello è più adatto per piccoli numeri di tenant, in cui l'isolamento è una priorità. Azure offre programmi partner che consentono di distribuire risorse nella sottoscrizione del tenant e di gestirle tramite un provider di servizi per conto del tenant. 

In questa esercitazione verranno distribuite tre applicazioni autonome per tre tenant nella sottoscrizione di Azure.  Si ottiene l'accesso completo per l'esplorazione e l'uso dei singoli componenti dell'applicazione.

Il codice sorgente e gli script di gestione dell'applicazione sono disponibili nel repository [WingtipTicketsSaaS-StandaloneApp](https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp) di GitHub. L'applicazione è stata creata con Visual Studio 2015 e non viene aperta e compilata correttamente in Visual Studio 2019 senza aggiornamenti.


In questa esercitazione si apprenderà:

> [!div class="checklist"]
> * Come distribuire l'applicazione SaaS autonoma Wingtip Tickets
> * Dove ottenere gli script di gestione e il codice sorgente dell'applicazione
> * Informazioni su server e database che costituiscono l'app

Verranno rese disponibili esercitazioni aggiuntive Consentono di esplorare una gamma di scenari di gestione basati su questo modello di applicazione.   

## <a name="deploy-the-wingtip-tickets-saas-standalone-application"></a>Distribuire l'applicazione SaaS autonoma Wingtip Tickets

Distribuire l'app per i tre tenant specificati:

1. Fare clic su ogni pulsante **Distribuisci in Azure** blu per aprire il modello di distribuzione nel [portale di Azure](https://portal.azure.com). Ogni modello richiede due valori di parametro, un nome per un nuovo gruppo di risorse e un nome utente che distingue questa distribuzione da altre distribuzioni dell'app. Il passaggio seguente fornisce dettagli per l'impostazione di questi valori.<br><br>
    <a href="https://aka.ms/deploywingtipsa-contoso" target="_blank"><img style="vertical-align:middle" src="media/saas-standaloneapp-get-started-deploy/deploy.png"/></a> &nbsp; **Contoso Concert Hall**
<br><br>
    <a href="https://aka.ms/deploywingtipsa-dogwood" target="_blank"><img style="vertical-align:middle" src="media/saas-standaloneapp-get-started-deploy/deploy.png"/></a> &nbsp; **Dogwood Dojo**
<br><br>
    <a href="https://aka.ms/deploywingtipsa-fabrikam" target="_blank"><img style="vertical-align:middle" src="media/saas-standaloneapp-get-started-deploy/deploy.png"/></a> &nbsp; **Fabrikam Jazz Club**

2. Immettere i valori dei parametri necessari per ogni distribuzione.

    > [!IMPORTANT]
    > Alcune impostazioni di autenticazione e per i firewall server sono intenzionalmente non protette a scopo dimostrativo. **Creare un nuovo gruppo di risorse** per ogni distribuzione di applicazione.  Non usare un gruppo di risorse esistente. Non utilizzare l'applicazione o le risorse che crea per la produzione. Eliminare tutti i gruppi di risorse quando non è più necessario usare le applicazioni, per interrompere la fatturazione correlata.

    È consigliabile usare solo lettere minuscole, numeri e trattini nei nomi delle risorse.
    * Per **Gruppo di risorse** selezionare Crea nuovo e quindi specificare un valore in minuscolo in Nome. **Wingtip-sa -\<venueName\>-\<user\>**  è il modello consigliato.  Per \<venuename\>, sostituire il nome del luogo senza spazi. Per \<\>utente, sostituire il valore dell'utente riportato di seguito.  Utilizzando questo modello, i possibili nomi dei gruppi di risorse sono *wingtip-sa-contosoconcerthall-af1*, *wingtip-sa-dogwooddojo-af1*, *wingtip-sa-fabrikamjazzclub-af1*.
    * Selezionare un **percorso** nell'elenco a discesa.

    * Per **User** è consigliabile un valore breve, ad esempio le iniziali seguite da una cifra, ad esempio *af1*.


3. **Distribuire l'applicazione**.

    * Fare clic per accettare i termini e le condizioni.
    * Fare clic su **Acquista**.

4. Monitorare lo stato delle tre distribuzioni facendo clic su **Notifiche** (l'icona a forma di campanella a destra della casella di ricerca). La distribuzione dell'app richiede circa cinque minuti.


## <a name="run-the-applications"></a>Eseguire le applicazioni

L'app illustra le sedi che ospitano gli eventi.  Le sedi sono i tenant dell'applicazione. Ogni sede è associata a un sito Web personalizzato in cui sono elencati i relativi eventi e dove è possibile la vendita dei biglietti. I tipi di sede includono sale concerto, jazz club e club sportivi. Nell'esempio, il tipo di sede determina la fotografia di sfondo visualizzata nel sito web della struttura.   Nel modello di applicazione autonoma, ogni sede ottiene un'istanza di applicazione separata associata a un proprio database SQL autonomo.

1. Aprire la pagina degli eventi per ognuno dei tre tenant in schede separate del browser:

   - http://events.contosoconcerthall.&lt;utente&gt;.trafficmanager.net
   - http://events.dogwooddojo.&lt;utente&gt;.trafficmanager.net
   - http://events.fabrikamjazzclub.&lt;utente&gt;.trafficmanager.net

     In ogni URL sostituire &lt;user&gt; con il valore relativo all'utente della distribuzione specifica.

   ![Events](./media/saas-standaloneapp-get-started-deploy/fabrikam.png)

Per controllare la distribuzione delle richieste in ingresso, l'app usa [*Gestione traffico di Microsoft Azure*](../traffic-manager/traffic-manager-overview.md). Ogni istanza dell'app specifica del tenant include il nome del tenant come parte del nome di dominio nell'URL. Gli URL di tutti i tenant includono valori di **User** specifici. Gli URL hanno il formato seguente:
- http://events.&lt;nomeluogo&gt;.&lt;utente&gt;.trafficmanager.net

La **posizione** del database di ogni tenant è inclusa nelle impostazioni dell'app distribuita corrispondente.

In un ambiente di produzione si crea in genere un record CNAME DNS per [*configurare un dominio Internet aziendale in modo che faccia riferimento*](../traffic-manager/traffic-manager-point-internet-domain.md) all'URL del profilo di Gestione traffico.


## <a name="explore-the-servers-and-tenant-databases"></a>Esplorare i server e i database del tenant

È possibile esaminare alcune risorse distribuite:

1. Nel [portale di Azure](https://portal.azure.com) passare all'elenco di gruppi di risorse.
2. Dovrebbero essere visualizzati i tre gruppi di risorse del tenant.
3. Aprire il gruppo di risorse **wingtip-sa-fabrikam-&lt;user&gt;** , che contiene le risorse per la distribuzione di Fabrikam Jazz Club.  Il server **fabrikamjazzclub-&lt;user&gt;** contiene il database **fabrikamjazzclub**.

Ogni database del tenant è un database *autonomo* con 50 DTU.

## <a name="additional-resources"></a>Risorse aggiuntive

<!--
* Additional [tutorials that build on the Wingtip SaaS application](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* To learn about elastic pools, see [*What is an Azure SQL elastic pool*](sql-database-elastic-pool.md)
* To learn about elastic jobs, see [*Managing scaled-out cloud databases*](elastic-jobs-overview.md)
-->

- Per informazioni sulle applicazioni SaaS multi-tenant, vedere [Modelli di progettazione per le applicazioni SaaS multi-tenant](saas-tenancy-app-design-patterns.md).

 
## <a name="delete-resource-groups-to-stop-billing"></a>Eliminare gruppi di risorse per interrompere la fatturazione ##

Quando si finisce di utilizzare l'esempio, è importante eliminare tutti i gruppi di risorse creati per interrompere la fatturazione correlata.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso:

> [!div class="checklist"]
> * Come distribuire l'applicazione SaaS autonoma Wingtip Tickets
> * Informazioni su server e database che costituiscono l'app
> * Come eliminare le risorse di esempio per interrompere la fatturazione correlata

Successivamente, provare l'esercitazione sul [provisioning e il catalogo](saas-standaloneapp-provision-and-catalog.md) in cui verrà illustrato l'uso di un catalogo di tenant che Abilita una gamma di scenari tra tenant come la gestione dello schema e l'analisi dei tenant.
 

