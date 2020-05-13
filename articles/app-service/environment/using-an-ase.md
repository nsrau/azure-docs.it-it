---
title: Usare e gestire un ambiente del servizio app
description: Informazioni su come creare, pubblicare e ridimensionare le app in un ambiente del servizio app. Trovare tutte le attività comuni in questo articolo.
author: ccompy
ms.assetid: a22450c4-9b8b-41d4-9568-c4646f4cf66b
ms.topic: article
ms.date: 5/10/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: fd1ffc8636e11ca20bc32b4b6f600e03d923d8b5
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83125809"
---
# <a name="use-an-app-service-environment"></a>Usare un ambiente del servizio app

Un ambiente del servizio app (ASE) è una distribuzione di app Azure servizio in una subnet nell'istanza di rete virtuale di Azure di un cliente. Un ambiente del servizio app è costituito da:

- **Front-end**: dove http o HTTPS termina in un ambiente del servizio app
- **Worker**: risorse che ospitano le app
- **Database**: include le informazioni che definiscono l'ambiente
- **Archiviazione**: usata per ospitare le app pubblicate dal cliente

È possibile distribuire un ambiente del servizio app con un indirizzo IP virtuale esterno o interno (VIP) per l'accesso all'app. Una distribuzione con un indirizzo VIP esterno è comunemente denominata ambiente del servizio app *esterno*. Una distribuzione con un indirizzo VIP interno viene chiamata ambiente del servizio app *ILB* perché usa un servizio di bilanciamento del carico interno (ILB). Per altre informazioni sull'ambiente del servizio app con bilanciamento del carico interno, vedere [Creazione e uso di un servizio di bilanciamento del carico interno con un ambiente del servizio app][MakeILBASE].

## <a name="create-an-app-in-an-ase"></a>Creare un'app in un ambiente del servizio app

Per creare un'app in un ambiente del servizio app, è possibile usare lo stesso processo quando si crea in genere un'app, ma con alcune piccole differenze. Quando si crea un nuovo piano di servizio app:

- Invece di selezionare una località geografica per distribuire l'app, selezionare un ambiente del servizio app come posizione.
- Tutti i piani di servizio app creati in un ambiente del servizio app possono trovarsi solo in un piano tariffario isolato.

Se non si dispone di un ambiente del servizio app, è possibile crearne uno seguendo le istruzioni riportate in [creare un ambiente del servizio app][MakeExternalASE].

Per creare un'app in un ambiente del servizio app:

1. Selezionare **Crea una risorsa**  >  **Web e dispositivi mobili**  >  **app Web**.

1. Immettere un nome per l'app. Se è già stato selezionato un piano di servizio app in un ambiente del servizio app, il nome di dominio per l'app riflette il nome di dominio dell'ambiente del servizio app:

    ![Selezione del nome per l'app][1]

1. Selezionare una sottoscrizione.

1. Immettere un nome per un nuovo gruppo di risorse, oppure fare clic su **Usa esistente** e selezionarne uno dall'elenco a discesa.

1. Selezionare il sistema operativo.

1. Selezionare un piano di servizio app esistente nell'ambiente del servizio app o crearne uno nuovo con la procedura seguente:

    a. Dal menu portale di Azure lato sinistro selezionare **Crea una risorsa > app Web**.

    b. Selezionare la sottoscrizione.

    c. Selezionare o creare il gruppo di risorse.

    d. Immettere il nome dell'app Web.

    e. Selezionare il **codice** o **DockerContainer**.

    f. Selezionare uno stack di Runtime.

    g. Selezionare **Linux** o **Windows**. 

    h. Selezionare l'ambiente del servizio app nell'elenco a discesa **Region (area** ). 

    i. Selezionare o creare un nuovo piano di servizio app. Se si crea un nuovo piano di servizio app, selezionare le dimensioni dello SKU **isolato** appropriato.

    ![Piani tariffari isolati][2]

    > [!NOTE]
    > Le app Linux e le app di Windows non possono trovarsi nello stesso piano di servizio app, ma possono trovarsi nello stesso ambiente del servizio app.
    >

1. Selezionare **Verifica + crea**, verificare che le informazioni siano corrette e quindi selezionare **Crea**.

## <a name="how-scale-works"></a>Come funziona il ridimensionamento

Ogni app del servizio app viene eseguita in un piano di servizio app. Gli ambienti del servizio app di Azure contengono piani del servizio app, che a loro volta contengono app. Quando si ridimensiona un'app, è anche possibile ridimensionare il piano di servizio app e tutte le app nello stesso piano.

Quando si ridimensiona un piano di servizio app, l'infrastruttura necessaria viene aggiunta automaticamente. Si verifica un ritardo di tempo per la scalabilità delle operazioni durante l'aggiunta dell'infrastruttura. Se si eseguono diverse operazioni di ridimensionamento in sequenza, viene eseguita la prima richiesta di scalabilità dell'infrastruttura e le altre vengono accodate. Al termine della prima operazione di ridimensionamento, le altre richieste di infrastruttura interagiscono. Quando viene aggiunta l'infrastruttura, i piani di servizio app vengono assegnati nel modo appropriato. La creazione di un nuovo piano di servizio app è a sua volta un'operazione di ridimensionamento, perché richiede hardware aggiuntivo.

Nel servizio app multi-tenant il ridimensionamento è immediato perché un pool di risorse è immediatamente disponibile per supportarlo. In un ambiente del servizio app, non esiste un buffer di questo tipo e le risorse vengono allocate in base alle esigenze.

In un ambiente del servizio app è possibile ridimensionare un piano di servizio app fino a 100 istanze. Un ambiente del servizio app può avere fino a 201 istanze totali in tutti i piani di servizio app nell'ambiente del servizio app.

## <a name="ip-addresses"></a>Indirizzi IP

Il servizio app può allocare un indirizzo IP dedicato a un'app. Questa funzionalità è disponibile dopo la configurazione di SSL basato su IP, come descritto in [associare un certificato TLS/SSL personalizzato esistente al servizio app Azure][ConfigureSSL]. In un ambiente del servizio app ILB non è possibile aggiungere altri indirizzi IP da usare per SSL basato su IP.

Con un ambiente del servizio app esterno, è possibile configurare il protocollo SSL basato su IP per l'app nello stesso modo in cui si utilizza il servizio app multi-tenant. C'è sempre un indirizzo di riserva nell'ambiente del servizio app, fino a 30 indirizzi IP. Ogni volta che ne viene usato uno, ne viene aggiunto un altro in modo che un indirizzo sia sempre immediatamente disponibile. Per allocare un altro indirizzo IP è necessario un ritardo di tempo. Questo ritardo impedisce l'aggiunta di indirizzi IP in rapida successione.

## <a name="front-end-scaling"></a>Scalabilità front-end

Quando si scalano i piani di servizio app, i thread di lavoro vengono aggiunti automaticamente per supportarli. Ogni ambiente del servizio app viene creato con due front-end. Il front-end viene automaticamente scalato in orizzontale a una frequenza di un front-end per ogni set di 15 istanze del piano di servizio app. Se, ad esempio, si dispone di tre piani di servizio app con cinque istanze ciascuno, si avranno un totale di 15 istanze e tre front-end. Se si passa a un totale di 30 istanze, si avranno quattro front-end. Questo modello continua con la scalabilità orizzontale.

Il numero di front-end allocati per impostazione predefinita è adatto per un carico moderato. È possibile ridurre il rapporto a un minimo di un front-end per ogni cinque istanze. È anche possibile modificare le dimensioni dei front-end. Per impostazione predefinita, si tratta di un core singolo. Nel portale di Azure è possibile modificare le dimensioni in due o quattro core.

Per modificare il rapporto o le dimensioni del front-end è previsto un addebito. Per altre informazioni, vedere [Prezzi del Servizio app di Azure][Pricing]. Se si vuole migliorare la capacità di carico dell'ambiente del servizio app, è possibile ottenere maggiori miglioramenti eseguendo il primo ridimensionamento dei front-end a due core prima di modificare il rapporto di scala. La modifica della dimensione principale dei front-end comporta un aggiornamento dell'ambiente del servizio app e deve essere eseguita al di fuori dei normali orari di ufficio.

Le risorse front-end sono gli endpoint HTTP/HTTPS per l'ambiente del servizio app. Con la configurazione front-end predefinita, l'uso della memoria per ogni front-end è di circa il 60%. Il motivo principale per la scalabilità dei front-end è l'utilizzo della CPU, che è principalmente basato sul traffico HTTPS.

## <a name="app-access"></a>Accesso all'app

In un ambiente del servizio app esterno, il suffisso di dominio usato per la creazione dell'app è *. &lt; asename &gt; . p.azurewebsites.NET*. Se l'ambiente del servizio app è denominato _External-ASE_ e si ospita un'app denominata _Contoso_ in tale ambiente del servizio app, è possibile accedervi con questi URL:

- contoso.external-ase.p.azurewebsites.net
- contoso.scm.external-ase.p.azurewebsites.net

Per informazioni su come creare un ambiente del servizio app esterno, vedere [creare un ambiente del servizio app][MakeExternalASE].

In un ambiente del servizio app ILB, il suffisso di dominio usato per la creazione dell'app è *. &lt; asename &gt; . appserviceenvironment.NET*. Se l'ambiente del servizio app è denominato _ILB-ASE_ e si ospita un'app denominata _Contoso_ in tale ambiente del servizio app, è possibile accedervi con questi URL:

- contoso.ilb-ase.appserviceenvironment.net
- contoso.scm.ilb-ase.appserviceenvironment.net

Per informazioni su come creare un ambiente del servizio app ILB, vedere [creare e usare un][MakeILBASE]ambiente del servizio app ILB.

L'URL SCM viene usato per accedere alla console Kudu o per pubblicare l'app usando Distribuzione Web. Per informazioni sulla console Kudu, vedere [Kudu console for Azure App Service][Kudu] (Console Kudu per il servizio app di Azure). La console Kudu offre un'interfaccia utente Web per il debug, il caricamento di file, la modifica di file e altro ancora.

### <a name="dns-configuration"></a>Configurazione del DNS 

Quando si usa un ambiente del servizio app esterno, le app eseguite nell'ambiente del servizio app vengono registrate con DNS di Azure. Non sono previsti passaggi aggiuntivi in un ambiente del servizio app esterno perché le app siano disponibili pubblicamente. Con un ambiente del servizio app ILB è necessario gestire il proprio DNS. Questa operazione può essere eseguita nel proprio server DNS o con le zone private di DNS di Azure.

Per configurare DNS nel proprio server DNS con l'ambiente del servizio app ILB:

1. creare una zona per <ASE name> . appserviceenvironment.NET
1. creare un record A in tale zona che punti * all'indirizzo IP del servizio ILB
1. creare un record A in tale zona che punti @ all'indirizzo IP del servizio ILB
1. creare una zona in <ASE name> . appserviceenvironment.NET denominata SCM
1. creare un record A nella zona che punti * all'indirizzo IP del servizio ILB

Per configurare DNS in zone private di DNS di Azure:

1. creare una zona privata di DNS di Azure denominata <ASE name> . appserviceenvironment.NET
1. creare un record A in tale zona che punti * all'indirizzo IP del servizio ILB
1. creare un record A in tale zona che punti @ all'indirizzo IP del servizio ILB
1. creare un record A in tale zona che punti *. scm all'indirizzo IP ILB

Le impostazioni DNS per il suffisso di dominio predefinito dell'ambiente del servizio app non limitano l'accesso delle app a tali nomi. È possibile impostare un nome di dominio personalizzato senza alcuna convalida per le app in un ambiente del servizio app ILB. Se quindi si vuole creare una zona denominata *contoso.NET*, è possibile fare in modo che punti all'indirizzo IP di ILB. Il nome di dominio personalizzato funziona per le richieste dell'app, ma non per il sito SCM. Il sito SCM è disponibile solo in * &lt; appname &gt; . SCM. &lt; asename &gt; . appserviceenvironment.NET*. 

Area denominata *. &lt; asename &gt; . appserviceenvironment.NET* è univoco a livello globale. Prima del 2019 maggio, i clienti potevano specificare il suffisso del dominio dell'ambiente del servizio app ILB. Se si desidera utilizzare *. contoso.com* per il suffisso di dominio, è possibile eseguire questa operazione e includere il sito SCM. Si sono verificati problemi con questo modello, tra cui; gestione del certificato SSL predefinito, mancanza di Single Sign-On con il sito SCM e requisiti per l'utilizzo di un certificato con caratteri jolly. Il processo di aggiornamento del certificato predefinito dell'ambiente del servizio app ILB è stato anche di disturbo e ha causato il riavvio dell'applicazione. Per risolvere questi problemi, il comportamento dell'ambiente del servizio app ILB è stato modificato in modo da usare un suffisso di dominio basato sul nome dell'ambiente del servizio app e con un suffisso di proprietà di Microsoft. La modifica al comportamento dell'ambiente del servizio app ILB influiscono solo su ILB gli ambienti effettuati dopo il 2019 maggio. Gli ambienti ILB preesistenti devono comunque gestire il certificato predefinito dell'ambiente del servizio app e la relativa configurazione DNS.

## <a name="publishing"></a>Pubblicazione

In un ambiente del servizio app, come per il servizio app multi-tenant, è possibile pubblicare con questi metodi:

- Distribuzione Web
- FTP
- Integrazione continua (CI)
- Trascinamento della selezione nella console Kudu
- Un IDE, ad esempio Visual Studio, Eclipse o IntelliJ IDEA

Con un ambiente del servizio app esterno, queste opzioni di pubblicazione funzionano allo stesso modo. Per altre informazioni, vedere [Distribuzione nel servizio app di Azure][AppDeploy].

Con un ambiente del servizio app ILB, gli endpoint di pubblicazione sono disponibili solo tramite ILB. Il servizio di bilanciamento del carico interno è in un IP privato nella subnet dell'ambiente del servizio app nella rete virtuale. Se non si ha accesso alla rete ILB, non è possibile pubblicare app in tale ambiente del servizio app. Come indicato nella pagina relativa alla [creazione e all'uso di un][MakeILBASE]ambiente del servizio app ILB, è necessario configurare DNS per le app nel sistema. Tale requisito include l'endpoint SCM. Se gli endpoint non sono definiti correttamente, non è possibile pubblicarli. Gli IDE devono anche avere accesso di rete al ILB per pubblicarli direttamente.

Senza modifiche aggiuntive, i sistemi CI basati su Internet come GitHub e Azure DevOps non funzionano con un ambiente del servizio app ILB perché l'endpoint di pubblicazione non è accessibile da Internet. È possibile abilitare la pubblicazione in un ambiente del servizio app ILB da Azure DevOps installando un agente di rilascio self-hosted nella rete virtuale che contiene l'ambiente del servizio app ILB. In alternativa, è anche possibile usare un sistema CI che usa un modello pull, ad esempio Dropbox.

Gli endpoint di pubblicazione per le app in un ambiente del servizio app con bilanciamento del carico interno usano il dominio con cui l'ambiente del servizio app con bilanciamento del carico interno è stato creato, È possibile visualizzarlo nel profilo di pubblicazione dell'app e nel riquadro del portale dell'app (in **Panoramica**  >  **Essentials** e anche in **Proprietà**).

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

Per creare un avviso nei log, seguire le istruzioni riportate in [creare, visualizzare e gestire gli avvisi di log con monitoraggio di Azure][logalerts]. In breve:

* Aprire la pagina degli avvisi nel portale dell'ambiente del servizio app
* Seleziona **nuova regola di avviso**
* Selezionare la risorsa da Log Analytics area di lavoro
* Impostare la condizione con una ricerca nei log personalizzata per usare una query come "AppServiceEnvironmentPlatformLogs | dove ResultDescription contiene "ha iniziato la scalabilità" o quello che preferisci. Impostare la soglia nel modo appropriato. 
* Aggiungere o creare un gruppo di azioni nel modo desiderato. Il gruppo di azioni è il punto in cui si definisce la risposta all'avviso, ad esempio l'invio di un messaggio di posta elettronica o un SMS
* Assegnare un nome all'avviso e salvarlo.

## <a name="upgrade-preference"></a>Preferenza di aggiornamento

Se si dispone di più gli ambienti, potrebbe essere necessario aggiornare alcuni gli ambienti prima di altre. All'interno dell'oggetto **HostingEnvironment gestione risorse** dell'ambiente del servizio app, è possibile impostare un valore per **upgradePreference**. L'impostazione **upgradePreference** può essere configurata usando un modello, ARMClient o https://resources.azure.com . I tre valori possibili sono:

- **None**: Azure aggiornerà l'ambiente del servizio app in nessun batch specifico. Questo è il valore predefinito.
- **Anticipatamente**: l'ambiente del servizio app verrà aggiornato nella prima metà degli aggiornamenti del servizio app.
- **Tardi**: l'ambiente del servizio app verrà aggiornato nella seconda metà degli aggiornamenti del servizio app.

Se si usa https://resources.azure.com , attenersi alla procedura seguente per impostare il valore di **upgradePreferences** :

1. Passare a resources.azure.com e accedere con l'account Azure.
1. Passare attraverso le risorse al \/ \[ nome sottoscrizione sottoscrizioni resourceGroups nome \] \/ \/ \[ gruppo di risorse \] \/ provider \/ Microsoft. Web \/ hostingEnvironments \/ \[ ASE nome \] .
1. Selezionare **lettura/scrittura** nella parte superiore.
1. Selezionare **Modifica**.
1. Impostare **upgradePreference** su uno dei tre valori desiderati.
1. Selezionare **patch**.

![risorse-visualizzazione di Azure com][5]

La funzionalità **upgradePreferences** è più sensata quando si dispone di più gli ambienti perché la gli ambienti "Early" verrà aggiornata prima del gli ambienti "tardivo". Quando si dispone di più gli ambienti, è necessario impostare il gli ambienti di sviluppo e test su "Early" e la gli ambienti di produzione su "late".

## <a name="pricing"></a>Prezzi

Lo SKU dei prezzi denominato *isolated* è da usare solo con gli ambienti. Tutti i piani di servizio app ospitati nell'ambiente del servizio app si trovano nello SKU dei prezzi isolati. Le tariffe isolate per i piani di servizio app possono variare in base all'area.

Oltre al prezzo dei piani di servizio app, è prevista una tariffa fissa per l'ambiente del servizio app. La tariffa flat non cambia con le dimensioni dell'ambiente del servizio app. Paga per l'infrastruttura dell'ambiente del servizio app a una velocità di scala predefinita di un front-end aggiuntivo per ogni 15 istanze del piano di servizio app.

Se il tasso di ridimensionamento predefinito di un front-end per ogni 15 istanze del piano di servizio app non è sufficientemente veloce, è possibile regolare il rapporto in base al quale vengono aggiunti i front-end o le dimensioni dei front-end. Quando si modificano il rapporto o le dimensioni, si paga per i core front-end che non verrebbero aggiunti per impostazione predefinita.

Se ad esempio si imposta la proporzione di ridimensionamento su 10, verrà aggiunto un front-end ogni 10 istanze nei piani di servizio app. La tariffa fissa copre una proporzione di ridimensionamento pari a un front-end ogni 15 istanze. Con una proporzione di ridimensionamento di 10, il costo verrà addebitato al terzo front-end aggiunto per le 10 istanze del piano di servizio app. Non è necessario pagare quando si raggiungono le 15 istanze, perché il front-end è stato aggiunto automaticamente.

Se si modificano le dimensioni dei front-end in due core, ma non si regola il rapporto, si pagano i core aggiuntivi. Un ambiente del servizio app viene creato con due front-end, quindi, anche al di sotto della soglia di scalabilità automatica, si paga per due core aggiuntivi se si aumentano le dimensioni ai front-end a due core.

Per altre informazioni, vedere [Prezzi del Servizio app di Azure][Pricing].

## <a name="delete-an-ase"></a>Eliminare un ambiente del servizio app

Per eliminare un ambiente del servizio app:

1. Selezionare **Elimina** nella parte superiore del riquadro **ambiente del servizio app** .

1. Immettere il nome dell'ambiente del servizio app per confermare l'eliminazione. Quando si elimina un ambiente del servizio app, viene eliminato anche tutto il contenuto al suo interno.

    ![Eliminazione dell'ambiente del servizio app][3]

1. Selezionare **OK**.

<!--Image references-->
[1]: ./media/using_an_app_service_environment/usingase-appcreate.png
[2]: ./media/using_an_app_service_environment/usingase-pricingtiers.png
[3]: ./media/using_an_app_service_environment/usingase-delete.png
[4]: ./media/using_an_app_service_environment/usingase-logsetup.png
[4]: ./media/using_an_app_service_environment/usingase-logs.png
[5]: ./media/using_an_app_service_environment/usingase-upgradepref.png

<!--Links-->
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/security-overview.md
[ConfigureASEv1]: app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: app-service-app-service-environment-intro.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/management/overview.md
[ConfigureSSL]: ../configure-ssl-certificate.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[AppDeploy]: ../deploy-local-git.md
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
[logalerts]: ../../azure-monitor/platform/alerts-log.md
