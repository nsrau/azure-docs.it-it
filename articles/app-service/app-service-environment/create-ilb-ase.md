---
title: Creazione e uso di un servizio di bilanciamento del carico interno con un ambiente del servizio app di Azure
description: Informazioni dettagliate sulla creazione e sull'uso di un ambiente del servizio app di Azure isolato
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 0f4c1fa4-e344-46e7-8d24-a25e247ae138
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: ccompy
ms.translationtype: Human Translation
ms.sourcegitcommit: cb4d075d283059d613e3e9d8f0a6f9448310d96b
ms.openlocfilehash: 8bc82a297ef8e3f1beac773a6a3b0c4a9b80334f
ms.contentlocale: it-it
ms.lasthandoff: 06/26/2017

---
# <a name="create-and-use-an-internal-load-balancer-with-an-app-service-environment"></a>Creazione e uso di un servizio di bilanciamento del carico interno con un ambiente del servizio app #

L'ambiente del servizio app è una distribuzione di Servizio app di Azure in una subnet nella rete virtuale di Azure (VNet). Per distribuire un ambiente del servizio app è possibile procedere in due modi: 

- con un indirizzo VIP sull'indirizzo IP esterno, spesso denominato _ambiente del servizio app esterno_.
- con l'indirizzo VIP su un indirizzo IP interno, spesso denominato _ambiente del servizio app ILB_ perché l'endpoint interno è un servizio di bilanciamento del carico interno (ILB). 

In questo articolo viene illustrato come creare un _ambiente del servizio app ILB_.  Per una panoramica sull'ambiente del servizio app è possibile iniziare con [An Introduction to the App Service Environments] [ Intro] (Introduzione agli ambienti del servizio app) e se si desidera imparare a creare un ambiente del servizio app esterno, iniziare da [Creating an External ASE][MakeExternalASE] (Creazione di un ambiente del servizio app esterno).

## <a name="overview"></a>Panoramica ##

Un ambiente del servizio app può essere distribuito con un endpoint accessibile da Internet o con un indirizzo IP nella rete virtuale. Per impostare l'indirizzo IP su un indirizzo di rete virtuale, è necessario distribuire l'ambiente del servizio app con un bilanciamento del carico interno (ILB). Per la distribuzione dell'ambiente del servizio app con un bilanciamento del carico interno, è necessario fornire:

-   il proprio dominio, con cui vengono create le app
-   il certificato usato per HTTPS
-   la gestione del DNS per il dominio

È possibile eseguire operazioni come:

-   l'hosting di applicazioni intranet in modo sicuro nel cloud, a cui si accede tramite una VPN ExpressRoute o da sito a sito
-   l'hosting delle app nel cloud non presenti nei server DNS pubblici
-   la creazione di applicazioni back-end isolate da Internet con cui le app front-end possono integrarsi in modo sicuro

***Funzione disabilitata***

Non è possibile eseguire alcune operazioni quando si usa un ambiente del servizio app con bilanciamento del carico interno, ad esempio:

-   uso di IPSSL
-   assegnazione di indirizzi IP ad app specifiche
-   acquisto e uso di un certificato con un'app tramite il portale. È ancora possibile ottenere i certificati direttamente con un'autorità di certificazione e usarli con le app, ma non tramite il portale di Azure.

## <a name="create-an-ilb-ase"></a>Creare un ambiente del servizio app con bilanciamento del carico interno ##

Per creare un ambiente del servizio app con bilanciamento del carico interno:

1.  Nel portale di Azure selezionare **Nuovo -&gt; Web e dispositivi mobili -&gt; Ambiente del servizio app**.
2.  Selezionare la propria sottoscrizione.
3.  Selezionare o creare un gruppo di risorse.
4.  Selezionare o creare una rete virtuale.
5.  Creare una subnet se si seleziona una rete virtuale. Assicurarsi di impostare una subnet con dimensioni sufficientemente grandi per supportare la crescita futura dell'ambiente del servizio app. La dimensione consigliata è un `/25`, contenente 128 indirizzi, in grado di gestire un ambiente del servizio app con dimensione massima. Non è possibile usare una dimensione di subnet pari a `/29` o inferiore.  L'infrastruttura deve usare almeno 5 indirizzi.  Anche con `/28` sarebbe necessario un ridimensionamento massimo di 11 istanze in una subnet `/28`. Se si ritiene che in futuro sarà necessario superare il valore massimo predefinito di 100 istanze nei piani di servizio app o eseguire un ridimensionamento intorno a 100, ma con ridimensionamento front-end più rapido, usare /24 con 256 indirizzi.
6.  Selezionare **Rete virtuale/Località -&gt; Configurazione rete virtuale** e impostare **Tipo di indirizzo VIP** su **Interno**.
7.  Specificare il nome di dominio, che sarà il dominio usato per le app create in questo ambiente del servizio app. Si applicano alcune restrizioni. Non può essere:
    - net
    - azurewebsites.net
    - p.azurewebsites.net
    - &lt;asename&gt;.p.azurewebsites.net

    Anche se si prevede di usare un nome di dominio personalizzato per le app ospitate in questo ambiente del servizio app, non è possibile avere una sovrapposizione tra il nome di dominio personalizzato e il nome di dominio utilizzato dal proprio ambiente del servizio app. Per un ambiente del servizio app ILB con nome di dominio _contoso.com_, non è possibile usare nomi di dominio personalizzati per le app simili a:
    - www.contoso.com
    - abcd.def.contoso.com
    - abcd.contoso.com

    Se si conoscono i nomi di dominio personalizzati da usare con le applicazioni da distribuire nel proprio ambiente del servizio app ILB, selezionare un dominio per l'ambiente del servizio app ILB durante la relativa creazione che non sia in conflitto. In questo esempio, potrebbe essere un nome simile a _contoso-internal.com_.
8.  Selezionare **OK**, quindi **Crea**.

    ![][1]

Nel pannello della rete virtuale è disponibile un'opzione di **Configurazione della rete virtuale**. È possibile scegliere tra un indirizzo VIP esterno o interno. L'opzione predefinita è **Esterno**. Scegliendo **Esterno**, l'ambiente del servizio app userà un indirizzo VIP accessibile da Internet. Se si seleziona l'opzione **Interno**, l'ambiente del servizio app verrà configurato con un bilanciamento del carico interno su un indirizzo IP all'interno della rete virtuale.

Dopo aver selezionato **Interno**, la possibilità di aggiungere più indirizzi IP dell'ambiente del servizio app viene rimossa ed è invece necessario fornire il dominio dell'ambiente del servizio app. In un ambiente del servizio app con un indirizzo VIP esterno viene usato il nome dell'ambiente nel dominio delle app create in questo ambiente.

Se si imposta il **Tipo di indirizzo VIP** su **Interno**, il nome dell'ambiente del servizio app non viene usato nel dominio per l'ambiente. Specificare il dominio in modo esplicito. Se il dominio è ***contoso.corp.net*** ed è stata creata un'app in ambiente del servizio app denominata ***timereporting***, l'URL per questa app sarà ***timereporting.contoso.corp.net***.

## <a name="apps-in-an-ilb-ase"></a>App in un ambiente del servizio app con bilanciamento del carico interno ##

La creazione di un'app in un ambiente del servizio app con bilanciamento del carico interno è la stessa che in un ambiente del servizio app regolare.

1.  Nel portale di Azure selezionare **Nuovo &gt; Web e dispositivi mobili &gt; Web** o **Dispositivi mobili** o **App per le API**.
2.  Immettere il nome dell'app.
3.  Selezionare la sottoscrizione.
4.  Selezionare o creare un gruppo di risorse.
5.  Selezionare o creare un piano di servizio app. Se si desidera creare un nuovo piano di servizio app, selezionare l'ambiente del servizio app come posizione e il pool di lavoro in cui si vuole creare il piano di servizio app. Quando si crea il piano di servizio app, selezionare l'ambiente del servizio app come posizione e il pool di lavoro. Quando si specifica il nome dell'app, si noterà che il dominio sotto il nome dell'app verrà sostituito con il dominio dell'ambiente del servizio app.
6.  Selezionare **Crea**. Selezionare la casella di controllo **Aggiungi al dashboard** se si vuole che l'app venga visualizzata nel dashboard.

    ![][2]

Sotto il nome dell'app, il nome di dominio viene aggiornato per riflettere il dominio dell'ambiente del servizio app.

## <a name="post-ilb-ase-creation-validation"></a>Convalida dopo la creazione dell'ambiente del servizio app con bilanciamento del carico interno ##

Un ambiente del servizio app con bilanciamento del carico interno è leggermente diverso rispetto all'ambiente del servizio app senza bilanciamento del carico interno. Come già notato, è necessario gestire il proprio DNS e fornire un proprio certificato per le connessioni HTTPS.

Dopo la creazione dell'ambiente del servizio app, si noterà che il nome di dominio mostra il dominio specificato e che esiste un nuovo elemento nel menu **Impostazione** denominato **Certificato ILB**. L'ambiente del servizio app viene creato con un certificato che non specifica il dominio dell'ambiente del servizio app ILB. Se si utilizza l'ambiente del servizio app con tale certificato, il browser indicherà che non è valido. Questo certificato rende più semplice testare HTTPS, ma la condizione è che è necessario caricare il proprio certificato associato al dominio dell'ambiente del servizio app ILB, indipendentemente dal fatto che sia autofirmato o acquisito da un'autorità di certificazione (CA).

![][3]

Esistono diversi modi per ottenere un certificato SSL valido, tra cui CA interne, l'acquisto di un certificato da un'autorità di certificazione esterna e l'uso di un certificato autofirmato. Indipendentemente dall'origine del certificato SSL è necessario configurare correttamente gli attributi del certificato seguenti:

-   _Soggetto_: questo attributo deve essere impostato su _\*.your-root-domain-here.com_
-   _Nome alternativo soggetto_: questo attributo deve includere sia _\*.your-root-domain-here_ che _\*.scm.your-root-domain-here_. La seconda voce è necessaria perché le connessioni SSL al sito SCM/Kudu associato a ogni app verranno eseguite con un indirizzo nel formato _your-app-name.scm.your-root-domain-here_

Dopo aver ottenuto un certificato SSL valido sono necessari altri due passaggi preliminari. Il certificato SSL deve essere convertito/salvato come file con estensione pfx. Il file con estensione pfx deve includere tutti i certificati intermedi e radice ed essere protetto con una password.

Se si desidera creare un certificato personalizzato mediante PowerShell è possibile usare i comandi riportati di seguito.  Assicurarsi di usare il nome di dominio del proprio ambiente del servizio app ILB anziché *internal.contoso.com*. 

    $certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "\*.internal-contoso.com","\*.scm.internal-contoso.com"
    
    $certThumbprint = "cert:\localMachine\my\" +$certificate.Thumbprint
    $password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText
    
    $fileName = "exportedcert.pfx" 
    Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password

Il certificato generato da questi comandi di PowerShell sarà ancora contrassegnato dai browser, perché non è stato creato da un'autorità di certificazione nella catena di certificati usata dal browser. L'unico modo per ottenere un certificato che non crei problemi con il browser è di ottenerlo da una CA commerciale presente nella catena di certificati del browser.  

![][4]

Per caricare i propri certificati e testare l'accesso:

1.  Passare all'interfaccia utente dell'ambiente del servizio app dopo la creazione dell'ambiente (**Ambiente del servizio app -> Impostazioni -> Certificati ILB**).
2.  Impostare il certificato ILB selezionando il file PFX del certificato e fornire la password. L'elaborazione di questo passaggio richiede tempo e verrà visualizzato il messaggio indicante un'operazione di caricamento in corso.
3.  Ottenere l'indirizzo ILB per l'ambiente del servizio app (**Ambiente del servizio app -> Proprietà -> Indirizzo IP virtuale**).
4.  Creare un'app Web nell'ambiente del servizio app dopo la creazione.
5.  Creare una macchina virtuale se non ne è già presente una nella rete virtuale.

    > [!NOTE] 
    > Non creare la macchina virtuale nella stessa subnet dell'ambiente del servizio app. In caso contrario, verrà interrotto il programma di installazione.
    >
    >

6.  Impostare il DNS per il dominio dell'ambiente del servizio app. È possibile usare un carattere jolly con il dominio nel DNS o se si vuole eseguire alcuni semplici test, modificare il file host nella macchina virtuale per impostare il nome dell'app Web sull'indirizzo IP VIP. Se il nome di dominio dell'ambiente del servizio app è _.ilbase.com_ e si crea l'applicazione web denominata _mytestapp_, verrà indirizzato su _mytestapp.ilbase.com_. Si dovrà quindi impostare _mytestapp.ilbase.com_ per risolvere l'indirizzo ILB. (In Windows, il file host si trova in _C:\Windows\System32\drivers\etc\_.) Se si desidera testare la pubblicazione distribuzione web o l'accesso alla console avanzata, è necessario creare un record per _mytestapp.scm.ilbase.com_.
7.  Usare un browser in questa VM e passare a ***http://mytestapp.ilbase.com*** (o qualunque sia il nome dell'app web con il dominio).
8.  Usare un browser su tale macchina virtuale e passare a ***https://mytestapp.ilbase.com***. È necessario accettare la mancanza di sicurezza se si utilizza un certificato autofirmato.

L'indirizzo IP per il bilanciamento del carico interno è riportato in **Indirizzi IP**. Sono presenti anche gli indirizzi IP usati dall'indirizzo VIP esterno e per il traffico di gestione in ingresso.

![][5]

### <a name="functions-and-the-ilb-ase"></a>Funzioni e ambiente del servizio app ILB

Quando si usano le funzioni su un ambiente del servizio app ILB è possibile riscontrare il messaggio di errore: *"Non è possibile recuperare le funzioni in questo momento.  Riprovare più tardi."*  Il motivo è che l'interfaccia utente delle funzioni si basa sul sito di gestione controllo servizi su HTTPS.  Se si utilizza un certificato HTTP per l'ambiente del servizio app che non dispone di un certificato radice presente nel browser, è possibile riscontrare questa situazione.  Inoltre, i browser IE\Edge non condividono l'impostazione *accept-invalid-cert* (accetta-cert-non valido) tra le schede. Pertanto, è possibile:

- aggiungere il certificato all'archivio certificati attendibili 
- oppure usare Chrome, ma è necessario accedere prima al sito di gestione controllo servizi, accettare il certificato non attendibile e passare al portale

## <a name="dns-configuration"></a>Configurazione del DNS ##

Quando si usa un indirizzo VIP esterno, il DNS viene gestito da Azure. Qualsiasi app creata nell'ambiente del servizio app viene aggiunta automaticamente al DNS di Azure, che è un DNS pubblico. In un ambiente del servizio app ILB, è necessario gestire il proprio DNS. Per un dominio specifico, ad esempio _contoso.net_, è necessario creare record A DNS nel proprio DNS che puntano all'indirizzo del bilanciamento del carico interno per:

- *.contoso.net
- *.scm.contoso.net

Se il dominio dell'ambiente del servizio app ILB viene utilizzato per più operazioni all'esterno di questo ambiente del servizio app, può essere necessario gestire il DNS in base al nome dell'applicazione. Ciò è molto più complesso perché sarebbe necessario aggiungere ogni nuovo nome di applicazione nel DNS durante la creazione. Per questo motivo, è consigliabile l'utilizzo di un dominio dedicato.

## <a name="publishing-with-an-ilb-ase"></a>Pubblicazione con un ambiente del servizio app ILB ##

Per ogni applicazione creata, sono disponibili due endpoint. In un ambiente del servizio app ILB, sono presenti *&lt;nome app>.&lt;Dominio ASE ILB>* e *&lt;nome app>.scm.&lt;Dominio ASE ILB>*. 

Il nome del sito di gestione controllo servizi consente di passare alla console Kudu, denominata **Advanced Portal** (Portale avanzato) all'interno del portale di Azure. La console Kudu consente di eseguire numerose operazioni, tra cui la visualizzazione delle variabili di ambiente, l'esplorazione del disco, l'uso di una console e molto altro ancora. Per informazioni, vedere [Kudu console for Azure App Service][Kudu] (Console Kudu per il servizio app di Azure). 

Nel servizio app multi-tenant e in un ambiente del servizio app esterno, è incluso il Single Sign-On tra il portale di Azure e la console Kudu. Per l'ambiente del servizio app ILB, tuttavia, è necessario usare invece le credenziali di pubblicazione per accedere alla console Kudu.

I sistemi di integrazione continua basati su Internet, ad esempio Github e VSTS, non funzionano con un ambiente del servizio app con bilanciamento del carico interno perché l'endpoint di pubblicazione non è accessibile tramite Internet. È invece necessario usare un sistema di integrazione continua che usa un modello pull, ad esempio Dropbox.

Gli endpoint di pubblicazione per le app in un ambiente del servizio app con bilanciamento del carico interno usano il dominio con cui l'ambiente del servizio app con bilanciamento del carico interno è stato creato, che può essere visualizzato nel profilo di pubblicazione dell'app e nel pannello del portale dell'app (in **Panoramica** > **Informazioni di base** e anche in **Proprietà**). Se si dispone di un ambiente del servizio app ILB con il sottodominio *contoso.net* e di un'app denominata *mytest*, è necessario eseguire il trasferimento FTP a *mytest.contoso.net* ed eseguire la distribuzione web su *mytest.scm.contoso.net*.

## <a name="coupling-an-ilb-ase-with-a-waf-device"></a>Accoppiamento di un ambiente del servizio app ILB con un dispositivo WAF ##

Il servizio app di Azure fornisce molte misure di sicurezza che proteggono il sistema e aiutano a stabilire se un'app è stata oggetto di un attacco. La migliore protezione per un'applicazione web è l'accoppiamento di una piattaforma di hosting, come il servizio app di Azure con un Firewall di applicazione Web (WAF). Poiché l'ambiente del servizio app ILB ha un endpoint applicazione con isolamento di rete, è ideale per tale utilizzo.  

Per altre informazioni sulla configurazione dell'ambiente del servizio app ILB con un dispositivo WAF, vedere [Configure a web application firewall with your App Service Environment][ASEWAF] (Configurazione di un Web application firewall (WAF) per l'ambiente del servizio app). Questo articolo spiega come usare un'appliance virtuale Barracuda con il proprio ambiente del servizio app. Un'altra opzione consiste nell'usare il Gateway applicazione Azure. Il Gateway applicazione usa le regole di base OWASP per proteggere le applicazioni associate allo stesso. Per altre informazioni sul Gateway applicazione Azure, vedere [Introduction to the Azure Web Application Firewall][AppGW](Web application firewall (WAF).

### <a name="getting-started"></a>Introduzione ###

Tutti gli articoli e le procedure sugli ambienti del servizio app sono disponibili nel [file LEGGIMI per gli ambienti di servizio dell'applicazione][ASEReadme].

Per iniziare a usare gli ambienti del servizio app, vedere [Introduction to App Service Environments][Intro] (Introduzione all'ambiente del servizio app).

Per altre informazioni sulla piattaforma del servizio app di Azure, vedere [Servizio app di Azure](http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/).
 
<!--Image references-->
[1]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-network.png
[2]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-webapp.png
[3]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-certificate.png
[4]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-certificate2.png
[5]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-ipaddresses.png

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

