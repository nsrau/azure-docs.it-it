---
title: Creare e usare un servizio di bilanciamento del carico interno con un ambiente del servizio app di Azure
description: Informazioni dettagliate sulla creazione e sull'uso di un ambiente del servizio app di Azure isolato da Internet
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 0f4c1fa4-e344-46e7-8d24-a25e247ae138
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 06/13/2017
ms.author: ccompy
ms.custom: mvc
ms.openlocfilehash: 9f7343102cf7af6d7f2ba6b4b2f08b7b855da6f8
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2017
---
# <a name="create-and-use-an-internal-load-balancer-with-an-app-service-environment"></a>Creare e usare un servizio di bilanciamento del carico interno con un ambiente del servizio app #

 L'ambiente del servizio app di Azure è una distribuzione di Servizio app di Azure in una subnet in una rete virtuale di Azure. È possibile distribuire un ambiente del servizio app in due modi: 

- Con un indirizzo VIP su un indirizzo IP esterno, spesso denominato ambiente del servizio app esterno.
- Con un indirizzo VIP su un indirizzo IP interno, spesso denominato ambiente del servizio app ILB perché l'endpoint interno è un servizio di bilanciamento del carico interno (ILB). 

Questo articolo illustra come creare un ambiente del servizio app ILB. Per una panoramica dell'ambiente del servizio app, vedere [Introduzione ad Ambiente del servizio app][Intro]. Per informazioni sulla creazione di un ambiente del servizio app esterno, vedere [Creare un ambiente del servizio app esterno][MakeExternalASE].

## <a name="overview"></a>Panoramica ##

È possibile distribuire un Ambiente del servizio app con un endpoint accessibile da Internet o con un indirizzo IP nella rete virtuale. Per impostare l'indirizzo IP su un indirizzo di rete virtuale, è necessario distribuire l'ambiente del servizio app con un bilanciamento del carico interno (ILB). Per la distribuzione dell'ambiente del servizio app con un bilanciamento del carico interno, è necessario fornire:

-   Il proprio dominio usato durante la creazione delle app.
-   Il certificato usato per HTTPS.
-   La gestione del DNS per il dominio.

È possibile eseguire operazioni come:

-   l'hosting di applicazioni intranet in modo sicuro nel cloud, a cui si accede tramite una VPN ExpressRoute di Azure o da sito a sito.
-   l'hosting di app nel cloud non presenti nei server DNS pubblici.
-   la creazione di applicazioni back-end isolate da Internet con cui le app front-end possono integrarsi in modo sicuro.

### <a name="disabled-functionality"></a>Funzionalità disabilitata ###

Quando si usa un ambiente del servizio app con bilanciamento del carico interno, non è possibile eseguire alcune operazioni.

-   Uso di IPSSL
-   Assegnazione di indirizzi IP ad app specifiche
-   Acquisto e uso di un certificato con un'app tramite il portale di Azure. È possibile ottenere i certificati direttamente da un'autorità di certificazione e usarli con le app. Non è possibile ottenerli tramite il portale di Azure.

## <a name="create-an-ilb-ase"></a>Creare un ambiente del servizio app con bilanciamento del carico interno ##

Per creare un ambiente del servizio app con bilanciamento del carico interno:

1. Nel portale di Azure selezionare **Nuovo** > **Web e dispositivi mobili** > **Ambiente del servizio app**.

2. Selezionare la propria sottoscrizione.

3. Selezionare o creare un gruppo di risorse.

4. Selezionare o creare una rete virtuale.

5. Se si seleziona una rete virtuale esistente, è necessario creare una subnet per contenere l'ambiente del servizio app. Assicurarsi di impostare una subnet con dimensioni sufficientemente grandi per supportare la crescita futura dell'ambiente del servizio app. La dimensione consigliata è `/25`, contenente 128 indirizzi, in grado di gestire un ambiente del servizio app con dimensione massima. La dimensione minima selezionabile è `/28`. In base alle esigenze dell'infrastruttura, è possibile ridimensionare questo valore fino a un massimo di 11 istanze.

    * Superare il valore massimo predefinito di 100 istanze nei piani di servizio app.

    * Usare una scalabilità intorno a 100, ma con ridimensionamento front-end più rapido.

6. Selezionare **Rete virtuale/Località** > **Configurazione rete virtuale**. Impostare **Tipo di indirizzo VIP** su **Interno**.

7. Immettere un nome di dominio che sarà il dominio usato per le app create in questo ambiente del servizio app. Si applicano alcune restrizioni. Non usare:

    * net   

    * azurewebsites.net

    * p.azurewebsites.net

    * &lt;asename&gt;.p.azurewebsites.net

   Il nome di dominio personalizzato usato per le app e il nome di dominio usato dall'ambiente del servizio app non possono sovrapporsi. Per un ambiente del servizio app ILB con nome di dominio _contoso.com_, non è possibile usare nomi di dominio personalizzati per le app come:

    * www.contoso.com

    * abcd.def.contoso.com

    * abcd.contoso.com

   Se si conoscono i nomi di dominio personalizzati per le app, scegliere un dominio per l'ambiente del servizio app ILB che non sia in conflitto con questi nomi di dominio personalizzati. In questo esempio è possibile usare un nome come *contoso-internal.com* per il dominio dell'ambiente del servizio app perché non è in conflitto con i nomi di dominio personalizzati che terminano in *.contoso.com*.

8. Selezionare **OK**, quindi **Crea**.

    ![Creazione dell'ambiente del servizio app][1]

Nel pannello **Rete virtuale** è disponibile un'opzione **Configurazione della rete virtuale**. È possibile usarla per selezionare un indirizzo VIP esterno o interno. L'opzione predefinita è **Esterno**. Se si seleziona **Esterno**, l'ambiente del servizio app userà un indirizzo VIP accessibile da Internet. Se si seleziona l'opzione **Interno**, l'ambiente del servizio app verrà configurato con un bilanciamento del carico interno su un indirizzo IP all'interno della rete virtuale.

Dopo avere selezionato **Interno**, non è più possibile aggiungere altri indirizzi IP all'ambiente del servizio app. È invece necessario fornire il dominio dell'ambiente del servizio app. In un ambiente del servizio app con un indirizzo VIP esterno viene usato il nome dell'ambiente nel dominio delle app create in questo ambiente.

Se si imposta il **Tipo di indirizzo VIP** su **Interno**, il nome dell'ambiente del servizio app non viene usato nel dominio per l'ambiente. Specificare il dominio in modo esplicito. Se il dominio è *contoso.corp.net* ed è stata creata un'app in un ambiente del servizio app denominata *timereporting*, l'URL per questa app sarà timereporting.contoso.corp.net.


## <a name="create-an-app-in-an-ilb-ase"></a>Creare un'app in un ambiente del servizio app con bilanciamento del carico interno ##

La creazione di un'app in un ambiente del servizio app con bilanciamento del carico interno è la stessa eseguita in un ambiente del servizio app regolare.

1. Nel portale di Azure selezionare **Nuovo** > **Web e dispositivi mobili** > **Web** o **Dispositivi mobili** o **App per le API**.

2. Immettere il nome dell'app.

3. Selezionare la sottoscrizione.

4. Selezionare o creare un gruppo di risorse.

5. Selezionare o creare un piano di servizio app. Se si vuole creare un nuovo piano di servizio app, selezionare l'ambiente del servizio app in uso come località. Selezionare il pool di lavoro in cui si vuole creare il piano di servizio app. Quando si crea il piano di servizio app, selezionare l'ambiente del servizio app come località e il pool di lavoro. Quando si specifica il nome dell'app, il dominio sotto il nome dell'app verrà sostituito con il dominio dell'ambiente del servizio app.

6. Selezionare **Crea**. Se si vuole che l'app venga visualizzata nel dashboard, selezionare la casella di controllo **Aggiungi al dashboard**.

    ![Creazione del piano di servizio app][2]

    In **Nome app** il nome di dominio viene aggiornato per riflettere il dominio dell'ambiente del servizio app.

## <a name="post-ilb-ase-creation-validation"></a>Convalida dopo la creazione dell'ambiente del servizio app con bilanciamento del carico interno ##

Un ambiente del servizio app con bilanciamento del carico interno è leggermente diverso rispetto all'ambiente del servizio app senza bilanciamento del carico interno. Come già osservato, è necessario gestire il proprio servizio DNS e fornire il proprio certificato per le connessioni HTTPS.

Dopo la creazione dell'ambiente del servizio app, il nome di dominio indica il dominio specificato. Viene visualizzato un nuovo elemento nel menu **Impostazione** denominato **Certificato ILB**. L'ambiente del servizio app viene creato con un certificato che non specifica il dominio dell'ambiente del servizio app ILB. Se si usa l'ambiente del servizio app con tale certificato, il browser indicherà che non è valido. Questo certificato rende più semplice testare HTTPS, ma è necessario caricare il proprio certificato associato al dominio dell'ambiente del servizio app ILB. Questo passaggio è necessario indipendentemente dal fatto che sia autofirmato o acquisito da un'autorità di certificazione (CA).

![Nome di dominio dell'ambiente del servizio app ILB][3]

L'ambiente del servizio app ILB richiede un certificato SSL valido. Usare autorità di certificazione interne, acquistare un certificato da un'autorità di certificazione esterna o usare un certificato autofirmato. Indipendentemente dall'origine del certificato SSL è necessario configurare correttamente gli attributi del certificato seguenti:

* **Soggetto**: questo attributo deve essere impostato su *.your-root-domain-here.
* **Nome alternativo soggetto**: questo attributo deve includere sia *.your-root-domain-here* che **.scm.your-root-domain-here*. Le connessioni SSL al sito SCM/Kudu associato a ogni app usano un indirizzo nel formato *your-app-name.scm.your-root-domain-here*.

Convertire/salvare il certificato SSL come file con estensione pfx. Il file con estensione pfx deve includere tutti i certificati intermedi e quelli radice. Proteggerlo con una password.

Se si vuole creare un certificato autofirmato, è possibile usare i comandi di PowerShell indicati di seguito. Assicurarsi di usare il nome di dominio del proprio ambiente del servizio app ILB anziché *internal.contoso.com*: 

    $certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "\*.internal-contoso.com","\*.scm.internal-contoso.com"
    
    $certThumbprint = "cert:\localMachine\my\" +$certificate.Thumbprint
    $password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText
    
    $fileName = "exportedcert.pfx" 
    Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password

Il certificato generato da questi comandi di PowerShell viene contrassegnato dai browser perché non è stato creato da un'autorità di certificazione nella catena di certificati usata dal browser. Per ottenere un certificato considerato attendibile dal browser, ottenerlo da un'autorità di certificazione commerciale presente nella catena di certificati del browser. 

![Impostare il certificato ILB][4]

Per caricare i propri certificati e testare l'accesso:

1. Dopo la creazione dell'ambiente del servizio app, passare alla relativa interfaccia utente. Selezionare **Ambiente del servizio app** > **Impostazioni** > **Certificato ILB**.

2. Per impostare il certificato ILB, selezionare il file con estensione pfx del certificato e immettere la password. L'elaborazione di questo passaggio richiede tempo. Viene visualizzato un messaggio che informa che è in corso un'operazione di caricamento.

3. Ottenere l'indirizzo ILB per l'ambiente del servizio app. Selezionare **Ambiente del servizio app** > **Proprietà** > **Indirizzo IP virtuale**.

4. Creare un'app Web nell'ambiente del servizio app dopo la creazione dell'ambiente.

5. Creare una macchina virtuale, se non ne è già presente una nella rete virtuale.

    > [!NOTE] 
    > Non tentare di creare questa macchina virtuale nella stessa subnet in cui si trova l'ambiente del servizio app per evitare esiti negativi o problemi.
    >
    >

6. Impostare il DNS per il dominio dell'ambiente del servizio app. È possibile usare un carattere jolly con il dominio nel DNS. Per eseguire alcuni semplici test, modificare il file hosts nella macchina virtuale per impostare il nome dell'app Web sull'indirizzo IP VIP:

    a. Se il nome di dominio dell'ambiente del servizio app è _.ilbase.com_ e si crea l'applicazione Web denominata _mytestapp_, verrà indirizzato su _mytestapp.ilbase.com_. Impostare quindi _mytestapp.ilbase.com_ per risolvere l'indirizzo ILB. (In Windows, il file host si trova in _C:\Windows\System32\drivers\etc\_.)

    b. Per testare la pubblicazione della distribuzione Web o l'accesso alla console avanzata, creare un record per _mytestapp.scm.ilbase.com_.

7. Usare un browser su tale macchina virtuale e passare a http://mytestapp.ilbase.com. In alternativa, passare a qualunque sia il nome dell'app Web con il dominio.

8. Usare un browser in questa macchina virtuale e passare a https://mytestapp.ilbase.com. Se si usa un certificato autofirmato, accettare la mancanza di sicurezza.

    L'indirizzo IP per il bilanciamento del carico interno è riportato in **Indirizzi IP**. Questo elenco include anche gli indirizzi IP usati dall'indirizzo VIP esterno e per il traffico di gestione in ingresso.

    ![Indirizzo IP del servizio ILB][5]

## <a name="web-jobs-functions-and-the-ilb-ase"></a>Funzioni, processi Web e ambiente del servizio app ILB ##

In un ambiente del servizio app ILB sono supportati sia i processi Web che Funzioni, ma per poterli usare dal portale è necessario avere l'accesso di rete al sito SCM.  Il browser deve quindi trovarsi in un host incluso nella rete virtuale o connesso a essa.  

Quando si usa Funzioni di Azure in un ambiente del servizio app ILB, è possibile che venga visualizzato il messaggio di errore: "Non è possibile recuperare le funzioni in questo momento. Riprovare più tardi". Questo errore si verifica perché l'interfaccia utente di Funzioni usa il sito SCM tramite HTTPS e il certificato radice non è incluso nella catena di certificati del browser. I processi Web presentano un problema simile. Per evitarlo, è possibile eseguire una di queste operazioni:

- Aggiungere il certificato all'archivio certificati attendibili. Questa operazione sblocca Microsoft Edge e Internet Explorer.
- Usare Chrome e prima di tutto accedere al sito SCM, accettare il certificato non attendibile e quindi passare al portale.
- Usare un certificato commerciale incluso nella catena di certificati del browser.  Questa è l'opzione migliore.  

## <a name="dns-configuration"></a>Configurazione del DNS ##

Quando si usa un indirizzo VIP esterno, il DNS viene gestito da Azure. Qualsiasi app creata nell'ambiente del servizio app viene aggiunta automaticamente al DNS di Azure, che è un DNS pubblico. In un ambiente del servizio app ILB, è necessario gestire il proprio DNS. Per un dominio specifico, ad esempio _contoso.net_, è necessario creare record A DNS nel proprio DNS che puntano all'indirizzo del bilanciamento del carico interno per:

- *.contoso.net
- *.scm.contoso.net

Se il dominio dell'ambiente del servizio app ILB viene usato per più operazioni all'esterno di questo ambiente del servizio app, è possibile che sia necessario gestire il DNS in base al nome dell'applicazione. Questo metodo è complesso perché è necessario aggiungere ogni nuovo nome di app al DNS in fase di creazione. Per questo motivo, è consigliabile usare un dominio dedicato.

## <a name="publish-with-an-ilb-ase"></a>Pubblicare con un ambiente del servizio app ILB ##

Per ogni app creata sono disponibili due endpoint. In un ambiente del servizio app ILB sono presenti *&lt;nome app>.&lt;Dominio ambiente del servizio app ILB>* e *&lt;nome app>.scm.&lt;Dominio ambiente del servizio app ILB>*. 

Il nome del sito di gestione controllo servizi consente di passare alla console Kudu, denominata **Advanced Portal** (Portale avanzato) all'interno del portale di Azure. La console Kudu consente di visualizzare le variabili di ambiente, esplorare il disco, usare una console e molto altro ancora. Per informazioni, vedere [Kudu console for Azure App Service][Kudu] (Console Kudu per il servizio app di Azure). 

Nel servizio app multi-tenant e in un ambiente del servizio app esterno, è incluso l'accesso Single Sign-On tra il portale di Azure e la console Kudu. Per l'ambiente del servizio app ILB, è necessario usare invece le credenziali di pubblicazione per accedere alla console Kudu.

I sistemi di integrazione continua basati su Internet, ad esempio GitHub e Visual Studio Team Services, non funzionano con un ambiente del servizio app con bilanciamento del carico interno perché l'endpoint di pubblicazione non è accessibile tramite Internet. È invece necessario usare un sistema di integrazione continua che usa un modello pull, ad esempio Dropbox.

Gli endpoint di pubblicazione per le app in un ambiente del servizio app con bilanciamento del carico interno usano il dominio con cui l'ambiente del servizio app con bilanciamento del carico interno è stato creato, che può essere visualizzato nel profilo di pubblicazione dell'app e nel pannello del portale dell'app (in **Panoramica** > **Informazioni di base** e anche in **Proprietà**). Se si dispone di un ambiente del servizio app ILB con il sottodominio *contoso.net* e di un'app denominata *mytest*, usare *mytest.contoso.net* per FTP e *mytest.scm.contoso.net* per la distribuzione Web.

## <a name="couple-an-ilb-ase-with-a-waf-device"></a>Accoppiare un ambiente del servizio app ILB con un dispositivo WAF ##

Servizio App di Azure fornisce molte misure di sicurezza che proteggono il sistema. Consente anche di determinare se un'app è stata oggetto di un attacco. La migliore protezione per un'applicazione Web è l'accoppiamento di una piattaforma di hosting, come Servizio app di Azure con un firewall di applicazione Web (WAF). Poiché l'ambiente del servizio app ILB ha un endpoint applicazione con isolamento di rete, è ideale per tale utilizzo.

Per altre informazioni sulla configurazione dell'ambiente del servizio app ILB con un dispositivo WAF, vedere [Configurazione di un Web application firewall (WAF) per l'ambiente del servizio app][ASEWAF]. Questo articolo spiega come usare un'appliance virtuale Barracuda con il proprio ambiente del servizio app. Un'altra opzione consiste nell'usare il gateway applicazione Azure. Il gateway applicazione usa le regole di base OWASP per proteggere le applicazioni associate allo stesso. Per altre informazioni sul gateway applicazione Azure, vedere [Web application firewall (WAF)][AppGW].

## <a name="get-started"></a>Introduzione ##

* Per iniziare a usare gli ambienti del servizio app, vedere [Introduzione ad Ambiente del servizio app][Intro].
 
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
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/virtual-networks-nsg.md
[ConfigureASEv1]: app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: app-service-app-service-environment-intro.md
[webapps]: ../app-service-web-overview.md
[mobileapps]: ../../app-service-mobile/app-service-mobile-value-prop.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: http://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md
[ConfigureSSL]: ../web-sites-purchase-ssl-web-site.md
[Kudu]: http://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
