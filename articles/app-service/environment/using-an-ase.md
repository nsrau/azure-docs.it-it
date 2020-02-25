---
title: Usare e gestire un ambiente del servizio app
description: Come creare, pubblicare e ridimensionare le app in un ambiente del servizio app Azure. Trovare le attività comuni in un documento.
author: ccompy
ms.assetid: a22450c4-9b8b-41d4-9568-c4646f4cf66b
ms.topic: article
ms.date: 01/01/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 7be1676c8949cd30d5e1fe93a73afd75a5a9b67f
ms.sourcegitcommit: f27b045f7425d1d639cf0ff4bcf4752bf4d962d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/23/2020
ms.locfileid: "77565664"
---
# <a name="use-an-app-service-environment"></a>Usare un ambiente del servizio app #

Il ambiente del servizio app (ASE) è una distribuzione del servizio app Azure in una subnet nella rete virtuale di Azure di un cliente. Un ambiente del servizio app è costituito da:

- **Front-end**: i front-end sono i punti in cui http/https termina in un ambiente del servizio app.
- **Ruoli di lavoro**: risorse che ospitano le app.
- **Database**: contiene le informazioni che definiscono l'ambiente.
- **Archiviazione**: usata per ospitare le app pubblicate dal cliente.

È possibile distribuire un ambiente del servizio app con un indirizzo VIP esterno o interno per l'accesso all'app. La distribuzione con un indirizzo VIP esterno viene comunemente definita come un ambiente del servizio app esterno. La versione interna viene chiamata ambiente del servizio app ILB, poiché usa un servizio di bilanciamento del carico interno (ILB). Per altre informazioni sull'ambiente del servizio app ILB, vedere [creare e usare un][MakeILBASE]ambiente del servizio app ILB.

## <a name="create-an-app-in-an-ase"></a>Creare un'app in un ambiente del servizio app ##

Per creare un'app in un ambiente del servizio app, procedere come di consueto per la creazione di un'app, ma con alcune piccole differenze. Quando si crea un nuovo piano di servizio app (ASP):

- Invece di selezionare una località geografica per distribuire l'app, selezionare un ambiente del servizio app come posizione.
- Tutti i piani di servizio app creati in un ambiente del servizio app possono trovarsi solo in un piano tariffario isolato.

Se non si dispone di un ambiente del servizio app, è possibile crearne uno seguendo le istruzioni riportate in [creare un ambiente del servizio app][MakeExternalASE].

Per creare un'app in un ambiente del servizio app:

1. Selezionare **Crea una risorsa** > **Web e dispositivi mobili** > **App Web**.

2. Immettere un nome per l'app. Se si è già selezionato un piano di servizio app in un ambiente del servizio app, il nome di dominio dell'app rispecchierà il nome di dominio dell'ambiente del servizio app.

    ![Selezione del nome per l'app][1]

1. Selezionare una sottoscrizione.

1. Immettere un nome per un nuovo gruppo di risorse, oppure fare clic su **Usa esistente** e selezionarne uno dall'elenco a discesa.

1. Selezionare il sistema operativo. 

1. Selezionare un piano di servizio app esistente nell'ambiente del servizio app o crearne uno nuovo con la procedura seguente:

    a. Dal menu portale di Azure lato sinistro selezionare **Crea una risorsa > app Web**.

    b. Selezionare la sottoscrizione.
    
    c. Selezionare o creare il gruppo di risorse.
    
    d. Specificare il nome dell'app Web.
    
    e. Selezionare il codice o DockerContainer.
    
    f. Selezionare stack di Runtime.
    
    g. Selezionare Linux o Windows. 
    
    h. Selezionare l'ambiente del servizio app nell'elenco a discesa **Region (area** ). 
    
    i. Selezionare o creare un nuovo piano di servizio app. Se si crea un nuovo piano di servizio app, selezionare le dimensioni dello SKU **isolato** appropriato.
    
    ![Piani tariffari isolati][2]

    > [!NOTE]
    > Le app di Linux e Windows non possono essere presenti nello stesso piano di servizio app, ma possono essere presenti nello stesso ambiente del servizio app. 
    >

2. Selezionare **Verifica + crea** e quindi selezionare **Crea** se le informazioni sono corrette.

## <a name="how-scale-works"></a>Come funziona il ridimensionamento ##

Ogni app del servizio app viene eseguita in un piano di servizio app. Gli ambienti del servizio app di Azure contengono piani del servizio app, che a loro volta contengono app. Quando si ridimensiona un'app, si ridimensiona il piano di servizio app e quindi si ridimensionano tutte le app nello stesso piano.

Quando si ridimensiona un piano di servizio app, l'infrastruttura necessaria viene aggiunta automaticamente. Si verifica un ritardo nelle operazioni di ridimensionamento mentre viene aggiunta l'infrastruttura. Se si eseguono diverse operazioni di ridimensionamento in sequenza, viene eseguita la prima richiesta di scalabilità dell'infrastruttura e le altre vengono accodate. Al termine della prima operazione di ridimensionamento, le altre richieste di infrastruttura interagiscono. Quando viene aggiunta l'infrastruttura, i piani di servizio app vengono assegnati in base alle esigenze. La creazione di un nuovo piano di servizio app è a sua volta un'operazione di ridimensionamento in quanto richiede hardware aggiuntivo. 

Nel servizio app multi-tenant il ridimensionamento è immediato perché un pool di risorse è immediatamente disponibile per supportarlo. In un ambiente del servizio app tale buffer non è disponibile e le risorse vengono allocate quando se ne presenta la necessità.

In un ambiente del servizio app è possibile ridimensionare un piano di servizio app fino a 100 istanze. Un ambiente del servizio app può avere fino a 201 istanze totali in tutti i piani di servizio app presenti nell'ambiente del servizio app. 

## <a name="ip-addresses"></a>Indirizzi IP ##

Il servizio app ha la possibilità di allocare un indirizzo IP dedicato a un'app. La possibilità di allocare un indirizzo IP dedicato a un'app è disponibile dopo la configurazione di un SSL basato su IP, come descritto in [associare un certificato SSL personalizzato esistente al servizio app Azure][ConfigureSSL]. In un ambiente del servizio app ILB non è possibile aggiungere altri indirizzi IP da usare per un protocollo SSL basato su IP.

Con un ambiente del servizio app esterno è possibile configurare SSL basato su IP per l'app nello stesso modo in cui si esegue il servizio app multi-tenant. Nell'ambiente del servizio app è sempre presente un indirizzo di riserva fino a 30 indirizzi IP. Ogni volta che se ne usa uno, ne viene aggiunto un altro, in modo che sia sempre disponibile un indirizzo da usare. Si verifica un ritardo, necessario per allocare un altro indirizzo IP, che impedisce di aggiungere gli indirizzi IP in rapida successione.

## <a name="front-end-scaling"></a>Scalabilità front-end ##

Quando si scalano i piani di servizio app, i thread di lavoro vengono aggiunti automaticamente per supportarli. Ogni ambiente del servizio app viene creato con due front-end. Il front-end viene scalato automaticamente a una frequenza di un front-end per ogni 15 istanze del piano di servizio app. Se si dispone di tre piani di servizio app con cinque istanze ciascuno, si avranno un totale di 15 istanze e tre front-end. Se si passa a un totale di 30 istanze, si avranno quattro front-end e così via. 

Il numero di front-end allocati per impostazione predefinita è adatto per un carico moderato. È possibile modificare il rapporto in un livello inferiore come un front-end per ogni cinque istanze. È anche possibile modificare le dimensioni dei front-end. Per impostazione predefinita, si tratta di un core singolo. In alternativa, è possibile modificare le dimensioni dei front-end nel portale in due o quattro dimensioni di base. Per modificare il rapporto o le dimensioni dei front-end è previsto un addebito. Per altre informazioni, vedere [prezzi del servizio app Azure][Pricing]. Se si vuole migliorare la capacità di carico dell'ambiente del servizio app, è possibile ottenere maggiore miglioramento eseguendo il primo passaggio a due front-end principali prima di modificare il rapporto di scala. La modifica della dimensione principale dei front-end comporta un aggiornamento dell'ambiente del servizio app e deve essere eseguita al di fuori dei normali orari di ufficio.

Le risorse front-end sono l'endpoint HTTP/HTTPS per l'ambiente del servizio app. Con la configurazione front-end predefinita, l'utilizzo della memoria per ogni front-end è costantemente pari al 60%. Il motivo principale per la scalabilità dei front-end è la CPU, basata principalmente sul traffico HTTPS.

## <a name="app-access"></a>Accesso all'app ##

In un ambiente del servizio app esterno, il suffisso di dominio usato per la creazione dell'app è *.&lt;asename&gt;. p.azurewebsites.NET*. Se l'ambiente del servizio app è denominato _External-ASE_ e si ospita un'app denominata _Contoso_ in tale ambiente del servizio app, è possibile accedervi con i seguenti URL:

- contoso.external-ase.p.azurewebsites.net
- contoso.scm.external-ase.p.azurewebsites.net

Per altre informazioni su come creare un ambiente del servizio app esterno, vedere [creare un ambiente del servizio app][MakeExternalASE]

In un ambiente del servizio app ILB, il suffisso di dominio usato per la creazione dell'app è *.&lt;asename&gt;. appserviceenvironment.NET*. Se l'ambiente del servizio app è denominato _ILB-ASE_ e si ospita un'app denominata _Contoso_ in tale ambiente del servizio app, è possibile accedervi con i seguenti URL:

- contoso.ilb-ase.appserviceenvironment.net
- contoso.scm.ilb-ase.appserviceenvironment.net

Per altre informazioni su come creare un ambiente del servizio app ILB, vedere [creare e usare un][MakeILBASE]ambiente del servizio app ILB. 

L'URL SCM viene usato per accedere alla console Kudu o per pubblicare l'app usando distribuzione Web. Per informazioni sulla console Kudu, vedere la [console Kudu per il servizio app Azure][Kudu]. La console Kudu offre un'interfaccia utente Web per il debug, il caricamento di file, la modifica di file e altro ancora.

## <a name="publishing"></a>Pubblicazione ##

Come nel servizio app multi-tenant, in un ambiente del servizio app è possibile pubblicare con:

- Distribuzione Web.
- FTP.
- Integrazione continua.
- Trascinamento della selezione nella console Kudu.
- Un ambiente di sviluppo integrato, ad esempio Visual Studio, Eclipse o Intellij IDEA.

Con un ambiente del servizio app esterno, queste opzioni di pubblicazione hanno lo stesso comportamento. Per ulteriori informazioni, vedere [distribuzione nel servizio app Azure][AppDeploy]. 

La grande differenza dal punto di vista della pubblicazione è negli ambienti del servizio app con bilanciamento del carico interno. Con un ambiente del servizio app con bilanciamento del carico interno gli endpoint di pubblicazione sono tutti disponibili solo tramite il servizio di bilanciamento del carico interno. Il servizio di bilanciamento del carico interno è in un IP privato nella subnet dell'ambiente del servizio app nella rete virtuale. Se non si ha accesso di rete al servizio di bilanciamento del carico interno, non è possibile pubblicare app in tale ambiente del servizio app. Come indicato nella pagina relativa alla [creazione e all'uso di un][MakeILBASE]ambiente del servizio app ILB, è necessario configurare DNS per le app nel sistema. incluso l'endpoint Gestione controllo servizi. Se non definiti correttamente, non è possibile pubblicarli. Gli ambienti di sviluppo integrato devono anche avere accesso di rete al servizio di bilanciamento del carico interno per potervi eseguire direttamente la pubblicazione.

I sistemi CI basati su Internet, ad esempio GitHub e Azure DevOps, non funzionano con un ambiente del servizio app ILB perché l'endpoint di pubblicazione non è accessibile tramite Internet. È possibile abilitare la pubblicazione in un ambiente del servizio app ILB da Azure DevOps installando un agente di rilascio self-hosted nel VNet che contiene l'ambiente del servizio app ILB. In alternativa, è anche possibile usare un sistema CI che usa un modello pull, ad esempio Dropbox.

Gli endpoint di pubblicazione per le app in un ambiente del servizio app con bilanciamento del carico interno usano il dominio con cui l'ambiente del servizio app con bilanciamento del carico interno è stato creato, che può essere visualizzato nel profilo di pubblicazione dell'app e nel pannello del portale dell'app (in **Panoramica** > **Informazioni di base** e anche in **Proprietà**). 

## <a name="storage"></a>Archiviazione

Un ambiente del servizio app ha 1 TB di spazio di archiviazione per tutte le app all'interno dell'ambiente del servizio app. Per impostazione predefinita, un piano di servizio App SKU isolato ha un limite di 250 GB. Se si hanno cinque o più piani di servizio app, è necessario prestare attenzione a non superare il limite di 1 TB dell'ambiente del servizio app. Se è necessario più del limite di 250 GB in un piano di servizio app, contattare il supporto tecnico per modificare il limite del piano di servizio app fino a un massimo di 1 TB. Quando il limite del piano viene regolato, esiste comunque un limite di 1 TB in tutti i piani di servizio app nell'ambiente del servizio app. 

## <a name="logging"></a>Registrazione ##

È possibile integrare l'ambiente del servizio app con monitoraggio di Azure per inviare i log relativi all'ambiente del servizio app ad archiviazione, Hub eventi o Log Analytics. Gli elementi registrati oggi sono:

| Situazione | Message |
|---------|----------|
| Ambiente del servizio app non integro | L'ambiente del servizio app specificato non è integro a causa di una configurazione di rete virtuale non valida. L'ambiente del servizio app verrà sospeso se lo stato non integro continua. Verificare che le linee guida definite qui siano seguite: https://docs.microsoft.com/azure/app-service/environment/network-info |
| La subnet dell'ambiente del servizio app è quasi esaurita | L'ambiente del servizio app specificato si trova in una subnet quasi esaurita. Sono presenti {0} indirizzi rimanenti. Una volta esauriti questi indirizzi, l'ambiente del servizio app non sarà in grado di scalare  |
| L'ambiente del servizio app si avvicina al limite totale di istanze | L'ambiente del servizio app specificato si avvicina al limite totale di istanze dell'ambiente del servizio app. Contiene attualmente {0} istanze del piano di servizio app di un massimo di 201 istanze. |
| L'ambiente del servizio app non riesce a raggiungere una dipendenza | L'ambiente del servizio app specificato non è in grado di raggiungere {0}.  Verificare che le linee guida definite qui siano seguite: https://docs.microsoft.com/azure/app-service/environment/network-info |
| Ambiente del servizio app sospeso | L'ambiente del servizio app specificato è sospeso. La sospensione dell'ambiente del servizio app potrebbe essere dovuta a un deficit dell'account o a una configurazione di rete virtuale non valida. Risolvere la causa principale e riprendere l'ambiente del servizio app per continuare a servire il traffico |
| Aggiornamento dell'ambiente del servizio app avviato | È stato avviato un aggiornamento della piattaforma all'ambiente del servizio app specificato. Prevedere ritardi nelle operazioni di ridimensionamento |
| Aggiornamento dell'ambiente del servizio app completato | Aggiornamento della piattaforma all'ambiente del servizio app specificato completato |
| Operazioni di ridimensionamento avviate | È iniziato il ridimensionamento di un piano di servizio app ({0}). Stato desiderato: {1} I ruoli di lavoro{2} 
| Operazioni di ridimensionamento completate | Il ridimensionamento di un piano di servizio app ({0}) è terminato. Stato corrente: {1} I ruoli di lavoro{2} |
| Operazioni di ridimensionamento non riuscite | Non è stato possibile ridimensionare un piano di servizio app ({0}). Stato corrente: {1} I ruoli di lavoro{2} |

Per abilitare la registrazione nell'ambiente del servizio app: 

1. Passare a impostazioni di diagnostica nel portale.  
1. Selezionare Aggiungi impostazioni di diagnostica.
1. Specificare un nome per l'integrazione dei log
1. Controllare e configurare le destinazioni di log desiderate. 
1. Controllare AppServiceEnvironmentPlatformLogs

![Impostazioni del log di diagnostica ASE][4]

Se si integra con Log Analytics, è possibile visualizzare i log selezionando log dal portale dell'ambiente del servizio app e creando una query su AppServiceEnvironmentPlatformLogs. 

## <a name="upgrade-preference"></a>Preferenza di aggiornamento ##

Se si dispone di più gli ambienti, è possibile che alcuni gli ambienti vengano aggiornati prima di altri. All'interno dell'oggetto HostingEnvironment Gestione risorse dell'ambiente del servizio app, è possibile impostare un valore per UpgradePreference. L'impostazione upgradePreference può essere configurata tramite template, ARMClient o https://resources.azure.com.  Le tre opzioni di valore sono:

* None: None è l'impostazione predefinita e indica che Azure aggiornerà l'ambiente del servizio app in nessun batch specifico
* Anticipatamente significa che l'ambiente del servizio app verrà aggiornato nella prima metà degli aggiornamenti del servizio app
* In ritardo significa che l'ambiente del servizio app verrà aggiornato nella seconda metà degli aggiornamenti del servizio app

Se si utilizza https://resources.azure.com, è possibile impostare il valore upgradePreferences per:

1. Passare a resources.azure.com e accedere con l'account Azure
1. Spostarsi tra le sottoscrizioni\/\[nome della sottoscrizione\]\/resourceGroups\/\[nome del gruppo di risorse\]provider \/\/Microsoft. Web\/hostingEnvironments\/\[ASE nome\]
1. Selezione di lettura/scrittura nella parte superiore
1. Selezionare modifica
1. Modificare il valore di upgradePreference in qualsiasi valore desiderato tra le tre scelte.
1. Seleziona patch

![risorse-visualizzazione di Azure com][5]

La funzionalità upgradePreferences è particolarmente utile quando si dispone di più gli ambienti, perché la gli ambienti aggiornata "anticipata" verrà aggiornata prima del gli ambienti "tardivo". Quando si dispone di più gli ambienti, è necessario che il gli ambienti di sviluppo/test sia impostato su "Early" e che la gli ambienti di produzione venga impostata come "tardivo".

## <a name="pricing"></a>Prezzi ##

Lo SKU dei prezzi denominato **isolated** è usato solo per l'ambiente del servizio app. Tutti i piani di servizio app ospitati nell'ambiente del servizio app si trovano nello SKU dei prezzi isolati. I costi del piano di servizio app Isolato possono variare in base all'area. 

Oltre al prezzo dei piani di servizio app, è disponibile una tariffa fissa solo per l'ambiente del servizio app. La tariffa fissa non cambia con le dimensioni dell'ambiente del servizio app e paga per l'infrastruttura dell'ambiente del servizio app a una velocità di ridimensionamento predefinita di un front-end aggiuntivo per ogni 15 istanze del piano di servizio app.  

Se il tasso di ridimensionamento predefinito di un front-end per ogni 15 istanze del piano di servizio app non è sufficientemente veloce, è possibile regolare il rapporto in base al quale vengono aggiunti i front-end o le dimensioni dei front-end.  Quando si modifica il rapporto o la dimensione, si pagano i core front-end che non verranno aggiunti per impostazione predefinita.  

Se ad esempio si imposta il rapporto di scala su 10, viene aggiunto un front-end per ogni 10 istanze nei piani di servizio app. La tariffa fissa copre una velocità di ridimensionamento di un front-end ogni 15 istanze. Con un rapporto di ridimensionamento pari a 10, si paga una tariffa per il terzo front-end aggiunto per le 10 istanze del piano di servizio app. Non è necessario pagare quando si raggiungono le 15 istanze, perché il front-end è stato aggiunto automaticamente.

Se le dimensioni dei front-end sono state modificate in due core, ma non è possibile modificare il rapporto, si paga per i core aggiuntivi.  Un ambiente del servizio app viene creato con due front-end, quindi, anche al di sotto della soglia di scalabilità automatica, si paga per due core aggiuntivi se si aumentano le dimensioni a due front-end principali.

Per altre informazioni, vedere [prezzi del servizio app Azure][Pricing].

## <a name="delete-an-ase"></a>Eliminare un ambiente del servizio app ##

Per eliminare un ambiente del servizio app: 

1. Usare **Elimina** nella parte superiore del pannello **Ambiente del servizio app**. 

1. Immettere il nome dell'ambiente del servizio app per confermare l'eliminazione. Quando si elimina un ambiente del servizio app, viene eliminato anche tutto il relativo contenuto. 

    ![Eliminazione dell'ambiente del servizio app][3]

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
