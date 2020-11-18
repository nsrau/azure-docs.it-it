---
title: Usare un ambiente del servizio app
description: Informazioni su come usare la ambiente del servizio app per ospitare applicazioni isolate.
author: ccompy
ms.assetid: 377fce0b-7dea-474a-b64b-7fbe78380554
ms.topic: article
ms.date: 11/16/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 3679bf9d55ddccefddb4bf3b2a96ec1b427315af
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94663659"
---
# <a name="using-an-app-service-environment"></a>Uso di un ambiente del servizio app

Il ambiente del servizio app (ASE) è una distribuzione a tenant singolo del servizio app Azure che inserisce direttamente in una rete virtuale di Azure (VNet) a scelta. Si tratta di un sistema che viene usato solo da un cliente. Le app distribuite nell'ambiente del servizio app sono soggette alle funzionalità di rete applicate alla subnet dell'ambiente del servizio app. Non sono presenti funzionalità aggiuntive che devono essere abilitate nelle app in modo da essere soggette a tali funzionalità di rete. 

## <a name="create-an-app-in-an-ase"></a>Creare un'app in un ambiente del servizio app

Per creare un'app in un ambiente del servizio app, è possibile usare lo stesso processo quando si crea in genere un'app, ma con alcune piccole differenze. Quando si crea un nuovo piano di servizio app:

- Invece di selezionare una località geografica per distribuire l'app, selezionare un ambiente del servizio app come posizione.
- Tutti i piani di servizio app creati in un ambiente del servizio app possono trovarsi solo in un piano tariffario con isolamento V2.

Se non si dispone di un ambiente del servizio app, è possibile crearne uno seguendo le istruzioni riportate in [creare un ambiente del servizio app][MakeASE].

Per creare un'app in un ambiente del servizio app:

1. Selezionare **Crea una risorsa**  >  **Web e dispositivi mobili**  >  **app Web**.

1. Selezionare una sottoscrizione.

1. Immettere un nome per un nuovo gruppo di risorse, oppure fare clic su **Usa esistente** e selezionarne uno dall'elenco a discesa.

1. Immettere un nome per l'app. Se è già stato selezionato un piano di servizio app in un ambiente del servizio app, il nome di dominio per l'app riflette il nome di dominio dell'ambiente del servizio app:

    ![creare un'app in un ambiente del servizio app][1]

1. Selezionare il tipo di pubblicazione, lo stack e il sistema operativo.

1.  Selezionare Region (area). Qui è necessario selezionare un ambiente del servizio app V3 pre-esistente.  Non è possibile creare un ASEv3 durante la creazione dell'app 

1. Selezionare un piano di servizio app esistente nell'ambiente del servizio app o crearne uno nuovo. Se si crea una nuova app, selezionare la dimensione desiderata per il piano di servizio app. L'unico SKU che è possibile selezionare per l'app è uno SKU di prezzi isolato V2.

    ![Piani tariffari con isolamento V2][2]

    > [!NOTE]
    > Le app Linux e le app di Windows non possono trovarsi nello stesso piano di servizio app, ma possono trovarsi nello stesso ambiente del servizio app.
    >

1. Selezionare * * Avanti: monitoraggio * * se si vuole abilitare App Insights con l'app, è possibile eseguire questa operazione durante il flusso di creazione. 

1.  Selezionare **Avanti: i tag** aggiungono i tag desiderati all'app  

1. Selezionare **Verifica + crea**, verificare che le informazioni siano corrette e quindi selezionare **Crea**.

## <a name="how-scale-works"></a>Come funziona il ridimensionamento

Ogni app del servizio app viene eseguita in un piano di servizio app. Gli ambienti del servizio app di Azure contengono piani del servizio app, che a loro volta contengono app. Quando si ridimensiona un'app, è anche possibile ridimensionare il piano di servizio app e tutte le app nello stesso piano.

Quando si ridimensiona un piano di servizio app, l'infrastruttura necessaria viene aggiunta automaticamente. Si verifica un ritardo di tempo per la scalabilità delle operazioni durante l'aggiunta dell'infrastruttura. Quando si ridimensiona un piano di servizio app, tutte le altre operazioni di ridimensionamento richieste dello stesso sistema operativo e dimensioni attendono fino a quando non viene completata la prima. Una volta completata l'operazione di blocco della scalabilità, tutte le richieste in coda vengono elaborate contemporaneamente. Un'operazione di ridimensionamento su una dimensione e un sistema operativo non blocca la scalabilità delle altre combinazioni di dimensioni e sistema operativo. Ad esempio, se è stato ridimensionato un piano di servizio app I2v2 Windows, tutte le altre richieste di ridimensionamento di Windows I2v2 in tale ambiente del servizio app verranno accodate fino a quando non viene completato.   

Nel servizio app multi-tenant il ridimensionamento è immediato perché un pool di risorse è immediatamente disponibile per supportarlo. In un ambiente del servizio app, non esiste un buffer di questo tipo e le risorse vengono allocate in base alle esigenze.

## <a name="app-access"></a>Accesso all'app

In un ambiente del servizio app, il suffisso di dominio usato per la creazione dell'app è *. &lt; asename &gt; . appserviceenvironment.NET*. Se l'ambiente del servizio app è denominato _My-ASE_ e si ospita un'app denominata _Contoso_ in tale ambiente del servizio app, è possibile accedervi con questi URL:

- contoso.my-ase.appserviceenvironment.net
- contoso.scm.my-ase.appserviceenvironment.net

Per informazioni su come creare un ambiente del servizio app, vedere [creare un ambiente del servizio app][MakeASE].

L'URL SCM viene usato per accedere alla console Kudu o per pubblicare l'app usando Distribuzione Web. Per informazioni sulla console Kudu, vedere [Kudu console for Azure App Service][Kudu] (Console Kudu per il servizio app di Azure). La console Kudu offre un'interfaccia utente Web per il debug, il caricamento di file, la modifica di file e altro ancora.

### <a name="dns-configuration"></a>Configurazione del DNS 

L'ambiente del servizio app usa endpoint privati per il traffico in ingresso e viene configurato automaticamente con le zone private di DNS di Azure. Se si vuole usare il proprio server DNS, è necessario aggiungere i record seguenti:

1. creare una zona per &lt;nome ambiente del servizio app&gt;.appserviceenvironment.net
1. creare un record A in tale zona che punti * all'indirizzo IP in ingresso usato dall'endpoint privato dell'ambiente del servizio app
1. creare un record A in tale zona che punti @ all'indirizzo IP in ingresso usato dall'endpoint privato dell'ambiente del servizio app
1. creare una zona in &lt;nome ambiente del servizio app&gt;.appserviceenvironment.net denominata SCM
1. creare un record A nell'area SCM che punti * all'indirizzo IP usato dall'endpoint privato dell'ambiente del servizio app

Le impostazioni DNS per il suffisso di dominio predefinito dell'ambiente del servizio app non limitano l'accesso delle app a tali nomi. È possibile impostare un nome di dominio personalizzato senza alcuna convalida per le app in un ambiente del servizio app. Se quindi si vuole creare una zona denominata *contoso.NET*, è possibile fare in modo che punti all'indirizzo IP in ingresso. Il nome del dominio personalizzato funziona per le richieste di app ma non per il sito scm. Il sito SCM è disponibile solo in *&lt; appname &gt; . SCM. &lt; asename &gt; . appserviceenvironment.NET*. 

## <a name="publishing"></a>Pubblicazione

In un ambiente del servizio app, come per il servizio app multi-tenant, è possibile pubblicare con questi metodi:

- Distribuzione Web
- Integrazione continua (CI)
- Trascinamento della selezione nella console Kudu
- Un IDE, ad esempio Visual Studio, Eclipse o IntelliJ IDEA

Con un ambiente del servizio app, gli endpoint di pubblicazione sono disponibili solo tramite l'indirizzo in ingresso usato dall'endpoint privato. Se non si ha accesso alla rete per l'indirizzo dell'endpoint privato, non è possibile pubblicare app in tale ambiente del servizio app.  Gli IDE devono anche avere accesso di rete al ILB per pubblicarli direttamente.

Senza modifiche aggiuntive, i sistemi CI basati su Internet come GitHub e Azure DevOps non funzionano con un ambiente del servizio app ILB perché l'endpoint di pubblicazione non è accessibile da Internet. È possibile abilitare la pubblicazione in un ambiente del servizio app ILB da Azure DevOps installando un agente di rilascio self-hosted nella rete virtuale che contiene l'ambiente del servizio app ILB. 

## <a name="storage"></a>Archiviazione

Un ambiente del servizio app ha 1 TB di spazio di archiviazione per tutte le app nell'ambiente del servizio app. Un piano di servizio app nello SKU dei prezzi isolati ha un limite di 250 GB. In un ambiente del servizio app sono stati aggiunti 250 GB di spazio di archiviazione per ogni piano di servizio app fino al limite di 1 TB. È possibile avere più piani di servizio app rispetto a quattro, ma non è stata aggiunta alcuna risorsa di archiviazione oltre il limite di 1 TB.

## <a name="logging"></a>Registrazione

È possibile integrare l'ambiente del servizio app con monitoraggio di Azure per inviare i log relativi all'ambiente del servizio app ad archiviazione di Azure, Hub eventi di Azure o Log Analytics. Questi elementi vengono registrati oggi:

| Situazione | Messaggio |
|---------|----------|
| Ambiente del servizio app non integro | L'ambiente del servizio app specificato non è integro a causa di una configurazione di rete virtuale non valida. L'ambiente del servizio app verrà sospeso se lo stato non integro continua. Verificare che siano seguite le linee guida definite in questo articolo: https://docs.microsoft.com/azure/app-service/environment/network-info . |
| La subnet dell'ambiente del servizio app è quasi esaurita | L'ambiente del servizio app specificato si trova in una subnet quasi esaurita. Sono presenti {0} indirizzi rimanenti. Una volta esauriti questi indirizzi, l'ambiente del servizio app non sarà in grado di eseguire la scalabilità.  |
| L'ambiente del servizio app si avvicina al limite totale di istanze | L'ambiente del servizio app specificato si avvicina al limite totale di istanze dell'ambiente del servizio app. Attualmente contiene {0} le istanze del piano di servizio app di un massimo di 201 istanze. |
| L'ambiente del servizio app non riesce a raggiungere una dipendenza | L'ambiente del servizio app specificato non è in grado di raggiungere {0} .  Verificare che siano seguite le linee guida definite in questo articolo: https://docs.microsoft.com/azure/app-service/environment/network-info . |
| Ambiente del servizio app sospeso | L'ambiente del servizio app specificato è sospeso. La sospensione dell'ambiente del servizio app potrebbe essere dovuta a un deficit dell'account o a una configurazione di rete virtuale non valida. Risolvere la causa principale e riprendere l'ambiente del servizio app per continuare a servire il traffico. |
| Aggiornamento dell'ambiente del servizio app avviato | È stato avviato un aggiornamento della piattaforma all'ambiente del servizio app specificato. Si prevede un ritardo nelle operazioni di ridimensionamento. |
| Aggiornamento dell'ambiente del servizio app completato | Aggiornamento della piattaforma all'ambiente del servizio app specificato completato. |
| Operazioni di ridimensionamento avviate | È iniziato il ridimensionamento di un piano di servizio app ( {0} ). Stato desiderato: {1} I {2} Worker.
| Operazioni di ridimensionamento completate | Il ridimensionamento di un piano di servizio app ( {0} ) è terminato. Stato corrente: {1} I {2} Worker. |
| Operazioni di ridimensionamento non riuscite | {0}Non è stato possibile ridimensionare un piano di servizio app (). Stato corrente: {1} I {2} Worker. |

Per abilitare la registrazione nell'ambiente del servizio app:

1. Nel portale passare a impostazioni di **diagnostica**.
1. Selezionare **Aggiungi impostazioni di diagnostica**.
1. Consente di specificare un nome per l'integrazione dei log.
1. Selezionare e configurare le destinazioni dei log desiderate.
1. Selezionare **AppServiceEnvironmentPlatformLogs**.

![Impostazioni del log di diagnostica ASE][4]

Se si integra con Log Analytics, è possibile visualizzare i log selezionando **log** dal portale dell'ambiente del servizio app e creando una query su **AppServiceEnvironmentPlatformLogs**. I log vengono generati solo quando l'ambiente del servizio app ha un evento che lo attiverà. Se l'ambiente del servizio app non ha un evento di questo tipo, non saranno presenti log. Per visualizzare rapidamente un esempio di log nell'area di lavoro Log Analytics, eseguire un'operazione di ridimensionamento con uno dei piani di servizio app nell'ambiente del servizio app. È quindi possibile eseguire una query su **AppServiceEnvironmentPlatformLogs** per visualizzare i log. 

**Creazione di un avviso**

Per creare un avviso nei log, seguire le istruzioni riportate in [creare, visualizzare e gestire gli avvisi di log con monitoraggio di Azure](../../azure-monitor/platform/alerts-log.md). In breve:

* Aprire la pagina degli avvisi nel portale dell'ambiente del servizio app
* Seleziona **nuova regola di avviso**
* Selezionare la risorsa da Log Analytics area di lavoro
* Impostare la condizione con una ricerca nei log personalizzata per usare una query come "AppServiceEnvironmentPlatformLogs | dove ResultDescription contiene "ha iniziato la scalabilità" o quello che preferisci. Impostare la soglia nel modo appropriato. 
* Aggiungere o creare un gruppo di azioni nel modo desiderato. Il gruppo di azioni è il punto in cui si definisce la risposta all'avviso, ad esempio l'invio di un messaggio di posta elettronica o un SMS
* Assegnare un nome all'avviso e salvarlo.

## <a name="internal-encryption"></a>Crittografia interna

L'ambiente del servizio app funziona come un sistema di black box in cui non è possibile visualizzare i componenti interni o la comunicazione all'interno del sistema. Per consentire una velocità effettiva più elevata, la crittografia non è abilitata per impostazione predefinita tra i componenti interni. Il sistema è sicuro perché il traffico è completamente inaccessibile per il monitoraggio o l'accesso. Se si dispone di un requisito di conformità che richiede la crittografia completa del percorso dati dalla crittografia end-to-end, è possibile abilitarla nell'interfaccia utente di **configurazione** dell'ambiente del servizio app.

![Abilitare la crittografia interna][5]

Verrà crittografato il traffico di rete interno nell'ambiente del servizio app tra i front-end e i ruoli di lavoro, verrà crittografato il file di paging e verranno crittografati i dischi del ruolo di lavoro. Dopo aver abilitato l'impostazione InternalEncryption in clusterSetting, è possibile che si verifichino problemi in termini di prestazioni del sistema. Quando si apporta la modifica per abilitare InternalEncryption, l'ambiente del servizio app sarà in uno stato instabile fino a quando la modifica non viene propagata completamente. Il completamento della propagazione completa della modifica può richiedere alcune ore, a seconda del numero di istanze disponibili nell'ambiente del servizio app. È consigliabile non abilitare questa impostazione in un ambiente del servizio app mentre è in uso. Se è necessario abilitare questa impostazione in un ambiente del servizio app usato attivamente, è opportuno deviare il traffico in un ambiente di backup fino al completamento dell'operazione.

## <a name="upgrade-preference"></a>Preferenza di aggiornamento

Se si dispone di più gli ambienti, potrebbe essere necessario aggiornare alcuni gli ambienti prima di altre. All'interno dell'oggetto **HostingEnvironment gestione risorse** dell'ambiente del servizio app, è possibile impostare un valore per **upgradePreference**. L'impostazione **upgradePreference** può essere configurata usando un modello, ARMClient o https://resources.azure.com . I tre valori possibili sono:

- **None**: Azure aggiornerà l'ambiente del servizio app in nessun batch specifico. Questo è il valore predefinito.
- **Anticipatamente**: l'ambiente del servizio app verrà aggiornato nella prima metà degli aggiornamenti del servizio app.
- **Tardi**: l'ambiente del servizio app verrà aggiornato nella seconda metà degli aggiornamenti del servizio app.

Per configurare le preferenze di aggiornamento, passare all'interfaccia utente di **configurazione** dell'ambiente del servizio app. 

La funzionalità **upgradePreferences** è più sensata quando si dispone di più gli ambienti perché la gli ambienti "Early" verrà aggiornata prima del gli ambienti "tardivo". Quando si dispone di più gli ambienti, è necessario impostare il gli ambienti di sviluppo e test su "Early" e la gli ambienti di produzione su "late".

## <a name="delete-an-ase"></a>Eliminare un ambiente del servizio app

Per eliminare un ambiente del servizio app:

1. Selezionare **Elimina** nella parte superiore del riquadro **ambiente del servizio app** .

1. Immettere il nome dell'ambiente del servizio app per confermare l'eliminazione. Quando si elimina un ambiente del servizio app, viene eliminato anche tutto il contenuto al suo interno.

    ![Eliminazione dell'ambiente del servizio app][3]

1. Selezionare **OK**.

<!--Image references-->
[1]: ./media/using/using-appcreate.png
[2]: ./media/using/using-appcreate-skus.png
[3]: ./media/using/using-delete.png
[4]: ./media/using/using-logsetup.png
[4]: ./media/using/using-logs.png
[5]: ./media/using/using-configuration.png

<!--Links-->
[Intro]: ./overview.md
[MakeASE]: ./creation.md
[ASENetwork]: ./networking.md
[UsingASE]: ./using.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/network-security-groups-overview.md
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/management/overview.md
[ConfigureSSL]: ../configure-ssl-certificate.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[AppDeploy]: ../deploy-local-git.md
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../web-application-firewall/ag/ag-overview.md
[logalerts]: ../../azure-monitor/platform/alerts-log.md
