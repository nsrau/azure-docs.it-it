---
title: Uso di un ambiente del servizio app di Azure
description: Come creare, pubblicare e ridimensionare le app in un ambiente del servizio app di Azure
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: a22450c4-9b8b-41d4-9568-c4646f4cf66b
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: ccompy
ms.translationtype: Human Translation
ms.sourcegitcommit: cb4d075d283059d613e3e9d8f0a6f9448310d96b
ms.openlocfilehash: c24f716d58f534d1439377234fa1263269961db7
ms.contentlocale: it-it
ms.lasthandoff: 06/26/2017

---
# <a name="using-an-app-service-environment"></a>Uso di un ambiente del servizio app #

## <a name="overview"></a>Panoramica ##

Un ambiente del servizio app è una distribuzione del servizio app di Azure in una subnet nella rete virtuale di Azure di un cliente. È costituita da:

- Front-end: dove HTTP/HTTPS termina in un ambiente del servizio app
- Ruoli di lavoro: risorse che ospitano le app
- Database: contiene le informazioni che definiscono l'ambiente
- Spazio di archiviazione: usato per ospitare le app pubblicate dal cliente

> [!NOTE]
> Esistono due versioni dell'ambiente del servizio app: ASEv1 e ASEv2. In ASEv1 è necessario gestire le risorse prima di poterle usare. Per informazioni su come configurare e gestire ASEv1, vedere [Configurare un ambiente del servizio app v1][ConfigureASEv1]. La parte restante di questo documento illustra ASEv2.
>
>

È possibile distribuire un ambiente del servizio app (sia ASEv1 che ASEv2) con un indirizzo VIP esterno per l'accesso app o un indirizzo VIP interno per l'accesso app. La distribuzione con un indirizzo VIP esterno è definita in genere ambiente del servizio app esterno e la versione interna è definita ambiente del servizio app con bilanciamento del carico interno perché usa un servizio di bilanciamento del carico interno. Per altre informazioni sull'ambiente del servizio app con bilanciamento del carico interno, vedere [Creating and using an ILB ASE][MakeILBASE] (Creazione e uso di un ambiente del servizio app con bilanciamento del carico interno).

## <a name="create-a-web-app-in-an-ase"></a>Creare un'app Web in un ambiente del servizio app ##

Il processo di creazione di un'app Web in un ambiente del servizio app è uguale a quello normale, con alcune piccole differenze. Quando si crea un nuovo piano di servizio app:

- Invece di selezionare una località geografica per distribuire l'app, si seleziona un ambiente del servizio app come località
- Tutti i piani di servizio app creati in un ambiente del servizio app devono essere in un piano tariffario isolato.

Se non si ha un ambiente del servizio app, è possibile crearne uno seguendo le istruzioni in [Create an App Service Environment][MakeExternalASE] (Creare un ambiente del servizio app).

Per creare un'app Web in un ambiente del servizio app:

1. Fare clic su **Nuovo &gt; Web e dispositivi mobili**, selezionare **App Web**.
2. Specificare un nome per l'app Web. Se si è già selezionato un piano di servizio app in un ambiente del servizio app, il nome di dominio per l'app rispecchierà il nome di dominio dell'ambiente del servizio app.

    ![][1]

1. Selezionare una sottoscrizione.
2. Specificare un nome per un nuovo gruppo di risorse o selezionare **Usa esistente** e selezionarne uno dall'elenco a discesa.
3. Selezionare un piano di servizio app esistente nell'ambiente del servizio app o crearne uno nuovo con la procedura seguente:
    1. Selezionare **Crea nuovo**.
    2. Specificare un nome per il piano di servizio app.
    3. Selezionare l'ambiente del servizio app nel menu a discesa **Località**.
    4. Selezionare un piano tariffario **Isolato**. Fare clic su **Seleziona**.
    5. Fare clic su **OK**.
    
    ![][2]
1. Fare clic su **Crea**.

## <a name="how-scale-works"></a>Come funziona il ridimensionamento ##

Ogni app del servizio app viene eseguita in un piano di servizio app. Il modello di contenitore è: gli ambienti contengono i piani di servizio app e i piani di servizio app contengono le app. Quando si ridimensiona un'app, si ridimensiona il piano di servizio app e quindi si ridimensionano tutte le app nello stesso piano.

In ASEv2, quando si ridimensiona un piano di servizio app, viene automaticamente aggiunta l'infrastruttura necessaria. In ASEv1 invece l'infrastruttura necessaria deve essere aggiunta prima di poter creare il piano di servizio app o di aumentarne il numero di istanze. In ASEv2 si verifica quindi un ritardo nelle operazioni di ridimensionamento mentre viene aggiunta l'infrastruttura.

Nel servizio app multi-tenant il ridimensionamento è in genere immediato perché è già disponibile un pool di risorse che possono essere usate per supportare l'aumento del numero di istanze. In un ambiente del servizio app tale buffer non è disponibile e le risorse vengono allocate quando se ne presenta la necessità.

In un ambiente del servizio app è possibile ridimensionare fino a 100 istanze. Queste 100 istanze possono essere tutte in un solo piano di servizio app o distribuite in più piani di servizio app.

## <a name="ip-addresses"></a>Indirizzi IP ##

Il servizio app ha la possibilità di allocare un indirizzo IP dedicato a un'app. Questa capacità è disponibile configurando un certificato SSL basato su IP, come illustrato in [Associare un certificato SSL personalizzato esistente alle app Web di Azure][ConfigureSSL]. In un ambiente del servizio app esiste tuttavia un'importante eccezione: non è possibile aggiungere altri indirizzi IP da usare per SSL basato su IP in un ambiente del servizio app con bilanciamento del carico interno.

In ASEv1 è necessario allocare gli indirizzi IP come risorse prima di poterli usare. In ASEv2 li si usa semplicemente dall'app proprio come nel servizio app multi-tenant. In ASEv2 è sempre presente un indirizzo di riserva fino a 30 indirizzi IP. Ogni volta che se ne usa uno, ne viene aggiunto un altro, in modo che sia sempre disponibile un indirizzo da usare. Si verifica un ritardo, necessario per allocare un altro indirizzo IP, che impedisce di aggiungere gli indirizzi IP in rapida successione.

## <a name="front-end-scaling"></a>Ridimensionamento dei front-end ##

In ASEv2, quando si aumenta il numero di istanze dei piani di servizio app, i ruoli di lavoro vengono automaticamente aggiunti per supportarli. Oltre ai due front-end con cui ogni ambiente del servizio app viene creato, il numero di istanze dei front-end viene anche automaticamente aumentato al ritmo di un front-end ogni 15 istanze nei piani di servizio app. Se ad esempio si hanno 15 istanze, si hanno quindi tre front-end. Se si passa a 30 istanze, si avranno quattro front-end e così via.

Questo dovrebbe essere sufficiente per la maggior parte degli scenari, ma, se è necessario aumentare il numero di istanze con una frequenza maggiore, è possibile impostare una proporzione più bassa, ad esempio un front-end ogni 5 istanze nei piani di servizio app. Per la modifica della proporzione è previsto un costo, come illustrato in [Prezzi di Servizio app di Azure][Pricing].

Le risorse front-end sono gli endpoint HTTP/HTTPS per l'ambiente del servizio app. Con la configurazione front-end predefinita, l'utilizzo della memoria per ogni front-end è di circa il 60%. I carichi di lavoro del cliente non vengono eseguiti in un front-end. Il fattore chiave per un front-end dal punto di vista del ridimensionamento è la CPU, che dipende principalmente dal traffico HTTPS.

## <a name="app-access"></a>Accesso all'app ##

In un ambiente del servizio app esterno il dominio usato durante la creazione delle app è diverso dal servizio app multi-tenant e include il nome dell'ambiente del servizio app. Per altre informazioni sulla creazione di un ambiente del servizio app esterno, vedere [Creating an App Service Environment][MakeExternalASE] (Creazione di un ambiente del servizio app). Il nome di dominio in un ambiente del servizio app esterno è simile a *.&lt;nomeambientedelservizioapp&gt;.p.azurewebsites.net*. Se quindi l'ambiente del servizio app è denominato _external-ase_ e si ospita un'app denominata _contoso_ in tale ambiente del servizio app, l'app sarà raggiungibile agli URL seguenti:

- contoso.external-ase.p.azurewebsites.net
- contoso.scm.external-ase.p.azurewebsites.net

L'URL *contoso.scm.external-ase.p.azurewebsites.net* viene usato per accedere alla console Kudu o per pubblicare l'app usando la distribuzione Web. Per informazioni sulla console Kudu, vedere [Kudu console for Azure App Service][Kudu] (Console Kudu per il servizio app di Azure). La console Kudu offre un'interfaccia utente Web per il debug, il caricamento di file, la modifica di file e altro ancora.

In un ambiente del servizio app con bilanciamento del carico interno si determina il dominio in fase di distribuzione. Per altre informazioni sulla creazione di un ambiente del servizio app con bilanciamento del carico interno, vedere [Create and use an ILB ASE][MakeILBASE] (Creare e usare un ambiente del servizio app con bilanciamento del carico interno). Se si specifica il nome di dominio _ilb-ase.info_, le app in tale ambiente del servizio app usano questo dominio durante la creazione delle app. Per l'app denominata _contoso_, gli URL saranno:

- contoso.ilb-ase.info
- contoso.scm.ilb-ase.info

## <a name="publishing"></a>Pubblicazione ##

Come nel servizio app multi-tenant, in un ambiente del servizio app è possibile pubblicare con:

- Distribuzione Web
- FTP
- Integrazione continua
- Trascinamento della selezione nella console Kudu
- Un ambiente di sviluppo integrato, ad esempio Visual Studio, Eclipse o Intellij IDEA

In un ambiente del servizio app è esattamente lo stesso. Per informazioni, vedere [Distribuzione nel servizio app di Azure][AppDeploy]. 

La grande differenza dal punto di vista della pubblicazione è negli ambienti del servizio app con bilanciamento del carico interno. Con un ambiente del servizio app con bilanciamento del carico interno gli endpoint di pubblicazione sono tutti disponibili solo tramite il servizio di bilanciamento del carico interno. Il servizio di bilanciamento del carico interno è in un IP privato nella subnet dell'ambiente del servizio app nella rete virtuale. Se non si ha accesso di rete al servizio di bilanciamento del carico interno, non è possibile pubblicare app in tale ambiente del servizio app. Come indicato in [Create and use an ILB ASE][MakeILBASE] (Creare e usare un ambiente del servizio app con bilanciamento del carico interno), è necessario configurare DNS per le app nel sistema, incluso l'endpoint Gestione controllo servizi. Se non vengono definiti correttamente, non sarà possibile eseguire la pubblicazione. Gli ambienti di sviluppo integrato devono anche avere accesso di rete al servizio di bilanciamento del carico interno per potervi eseguire direttamente la pubblicazione.

I sistemi di integrazione continua basati su Internet, ad esempio Github e VSTS, non funzionano con un ambiente del servizio app con bilanciamento del carico interno perché l'endpoint di pubblicazione non è accessibile tramite Internet. È invece necessario usare un sistema di integrazione continua che usa un modello pull, ad esempio Dropbox.

Gli endpoint di pubblicazione per le app in un ambiente del servizio app con bilanciamento del carico interno usano il dominio con cui l'ambiente del servizio app con bilanciamento del carico interno è stato creato, che può essere visualizzato nel profilo di pubblicazione dell'app e nel pannello del portale dell'app (in **Panoramica** > **Informazioni di base** e anche in **Proprietà**). 

## <a name="pricing"></a>Prezzi ##

Con ASEv2, è disponibile un nuovo SKU di prezzi, usato solo con ASEv2, denominato **Isolato**. Tutti i piani di servizio app ospitati in ASEv2 fanno parte dello SKU di prezzi Isolato. Oltre al prezzo per i piani di servizio app, è disponibile una tariffa fissa solo per l'ambiente del servizio app. Questo prezzo non varia a seconda della dimensione dell'ambiente del servizio app. 

Le altre potenziali tariffe sono relative alla modifica della proporzione di ridimensionamento dei front-end o alle dimensioni dei front-end. Se si modifica la proporzione di ridimensionamento in modo che i front-end vengano aggiunti più rapidamente, sarà necessario pagare un costo per gli altri core che non vengono aggiunti automaticamente al sistema. Analogamente, se si seleziona una dimensione maggiore per i front-end, si pagherà un costo per i core che non vengono allocati automaticamente. Se ad esempio si imposta la proporzione di ridimensionamento su 10, verrà aggiunto un front-end ogni 10 istanze nei piani di servizio app. La tariffa fissa copre una proporzione di ridimensionamento pari a 1 front-end ogni 15 istanze. Con una proporzione di ridimensionamento pari a 10, si pagherà una tariffa per il terzo front-end aggiunto per le 10 istanze ASP, ma non sarà necessario pagarlo quando si raggiungono le 15 istanze, perché sarà già stato aggiunto automaticamente.

Per altre informazioni, vedere [Prezzi di Servizio app di Azure][Pricing].

## <a name="deleting-an-ase"></a>Eliminazione di un ambiente del servizio app ##

Per eliminare un ambiente del servizio app, è sufficiente usare l'azione **Elimina** nella parte superiore del pannello dell'ambiente del servizio app. In questo caso, verrà richiesto di immettere il nome dell'ambiente del servizio app per confermare che si vuole procedere con l'operazione. Si noti che quando si elimina un ambiente del servizio app, viene eliminato anche tutto il relativo contenuto. 

![][3]

<!--Image references-->
[1]: ./media/using_an_app_service_environment/usingase-appcreate.png
[2]: ./media/using_an_app_service_environment/usingase-pricingtiers.png
[3]: ./media/using_an_app_service_environment/usingase-delete.png


<!--Links-->
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[ASEReadme]: ./readme.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/virtual-networks-nsg.md
[ConfigureASEv1]: ../../app-service-web/app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: ../../app-service-web/app-service-app-service-environment-intro.md
[webapps]: ../../app-service-web/app-service-web-overview.md
[mobileapps]: ../../app-service-mobile/app-service-mobile-value-prop.md
[APIapps]: ../../app-service-api/app-service-api-apps-why-best-platform.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: http://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md
[ConfigureSSL]: ../../app-service-web/web-sites-purchase-ssl-web-site.md
[Kudu]: http://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[AppDeploy]: ../../app-service-web/web-sites-deploy.md
[ASEWAF]: ../../app-service-web/app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md

