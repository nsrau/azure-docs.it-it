---
title: Creare un servizio di bilanciamento del carico interno con un ambiente del servizio app - Azure
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
ms.date: 05/28/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 6f571342b02084ceb8d67fbb889e030194663489
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66493824"
---
# <a name="create-and-use-an-internal-load-balancer-app-service-environment"></a>Creare e usare un ambiente del servizio app con bilanciamento del carico interno 

L'ambiente del servizio app di Azure è una distribuzione del servizio app di Azure in una subnet in una rete virtuale di Azure. È possibile distribuire un ambiente del servizio app in due modi: 

- Con un indirizzo VIP su un indirizzo IP esterno, spesso denominato ambiente del servizio app esterno.
- Con un indirizzo VIP su un indirizzo IP interno, spesso denominato ambiente del servizio app ILB perché l'endpoint interno è un servizio di bilanciamento del carico interno (ILB). 

Questo articolo illustra come creare un ambiente del servizio app ILB. Per una panoramica dell'ambiente del servizio app, vedere [Introduzione agli ambienti del servizio app][Intro]. Per informazioni sulla creazione di un ambiente del servizio app esterno, vedere [Creare un ambiente del servizio app esterno][MakeExternalASE].

## <a name="overview"></a>Panoramica 

È possibile distribuire un Ambiente del servizio app con un endpoint accessibile da Internet o con un indirizzo IP nella rete virtuale. Per impostare l'indirizzo IP su un indirizzo di rete virtuale, è necessario distribuire l'ambiente del servizio app con un bilanciamento del carico interno (ILB). Per la distribuzione dell'ambiente del servizio app con un bilanciamento del carico interno, è necessario fornire il nome dell'ambiente del servizio app. Il nome dell'ambiente del servizio app viene usato nel suffisso di dominio per le app nell'ambiente del servizio app.  Il suffisso di dominio per l'ambiente del servizio app ILB è &lt;nome ambiente del servizio app&gt;.appservicewebsites.net. Le app create in un ambiente del servizio app ILB non vengono inserite nel DNS pubblico. 

Nelle versioni precedenti dell'ambiente del servizio app ILB è necessario fornire un suffisso di dominio e un certificato predefinito per le connessioni HTTPS. Il suffisso di dominio e il certificato predefinito non vengono più raccolti al momento della creazione dell'ambiente del servizio app ILB. Ora, quando si crea un ambiente del servizio app ILB, il certificato predefinito viene fornito da Microsoft e considerato attendibile dal browser. È ancora possibile impostare nomi di dominio personalizzati per le app nell'ambiente del servizio app e impostare certificati su tali nomi di dominio personalizzati. 

Con un ambiente del servizio app ILB è possibile eseguire operazioni come:

-   l'hosting sicuro nel cloud di applicazioni Intranet, a cui si accede tramite una VPN ExpressRoute o da sito a sito.
-   la protezione di app con un dispositivo WAF
-   l'hosting di app nel cloud non presenti nei server DNS pubblici.
-   la creazione di applicazioni back-end isolate da Internet con cui le app front-end possono integrarsi in modo sicuro.

### <a name="disabled-functionality"></a>Funzionalità disabilitata ###

Quando si usa un ambiente del servizio app con bilanciamento del carico interno, non è possibile eseguire alcune operazioni.

-   Uso di IPSSL
-   Assegnazione di indirizzi IP ad app specifiche
-   Acquisto e uso di un certificato con un'app tramite il portale di Azure. È possibile ottenere i certificati direttamente da un'autorità di certificazione e usarli con le app. Non è possibile ottenerli tramite il portale di Azure.

## <a name="create-an-ilb-ase"></a>Creare un ambiente del servizio app con bilanciamento del carico interno ##

Per creare un ambiente del servizio app con bilanciamento del carico interno:

1. Nel portale di Azure selezionare **Crea una risorsa** > **Web**  > **Ambiente del servizio app**.

2. Selezionare la propria sottoscrizione.

3. Selezionare o creare un gruppo di risorse.

4. Immettere il nome dell'ambiente del servizio app.

5. Selezionare il tipo di IP virtuale interno.

    ![Creazione dell'ambiente del servizio app](media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase.png)

6. Selezionare Rete

7. Selezionare o creare una rete virtuale. Se si crea una nuova rete virtuale, verrà definita con un intervallo di indirizzi 192.168.250.0/23. Per creare una rete virtuale con un intervallo di indirizzi diverso o in un gruppo di risorse diverso rispetto all'ambiente del servizio app, usare il portale di creazione rete virtuale di Azure. 

8. Selezionare o creare una subnet vuota. Se si vuole selezionare una subnet, deve essere vuota e non delegata. Non è possibile modificare le dimensioni della subnet dopo la creazione dell'ambiente del servizio app. La dimensione consigliata è `/24`, che include 256 indirizzi ed è in grado di gestire un ambiente del servizio app di dimensioni massime e qualsiasi esigenza di ridimensionamento. 

    ![Rete dell'ambiente del servizio app][1]

7. Selezionare **Rivedi e crea** e quindi **Crea**.

## <a name="create-an-app-in-an-ilb-ase"></a>Creare un'app in un ambiente del servizio app con bilanciamento del carico interno ##

La creazione di un'app in un ambiente del servizio app con bilanciamento del carico interno è la stessa eseguita in un ambiente del servizio app regolare.

1. Nel portale di Azure selezionare **Crea una risorsa** > **Web** > **App Web**.

1. Immettere il nome dell'app.

1. Selezionare la sottoscrizione.

1. Selezionare o creare un gruppo di risorse.

1. Selezionare pubblicazione, stack di runtime e sistema operativo.

1. Selezionare un percorso che corrisponda a un ambiente del servizio app ILB esistente.  È anche possibile creare un nuovo ambiente del servizio app durante la creazione dell'app, selezionando un piano di servizio app Isolato. Se si vuole creare un nuovo ambiente del servizio app, selezionare l'area in cui va creato.

1. Selezionare o creare un piano di servizio app. 

1. Selezionare **Rivedi e crea** e quindi **Crea** quando si è pronti.

### <a name="web-jobs-functions-and-the-ilb-ase"></a>Funzioni, processi Web e ambiente del servizio app ILB 

In un ambiente del servizio app ILB sono supportati sia i processi Web che Funzioni, ma per poterli usare dal portale è necessario avere l'accesso di rete al sito SCM.  Il browser deve quindi trovarsi in un host incluso nella rete virtuale o connesso a essa. Se l'ambiente del servizio app ILB ha un nome di dominio che non termina con *appserviceenvironment.net*, sarà necessario impostare il browser in modo che consideri attendibile il certificato HTTPS usato dal sito SCM.

## <a name="dns-configuration"></a>Configurazione del DNS 

Quando si usa un indirizzo VIP esterno, il DNS viene gestito da Azure. Qualsiasi app creata nell'ambiente del servizio app viene aggiunta automaticamente al DNS di Azure, che è un DNS pubblico. In un ambiente del servizio app ILB, è necessario gestire il proprio DNS. Il suffisso di dominio usato con un ambiente del servizio app ILB dipende dal nome dell'ambiente del servizio app. Il suffisso di dominio è *&lt;nome ambiente del servizio app&gt;.appserviceenvironment.net*. L'indirizzo IP del bilanciamento del carico interno è riportato nel portale in **Indirizzi IP**. 

Per configurare il DNS:

- creare una zona per *&lt;nome ambiente del servizio app&gt;.appserviceenvironment.net*
- creare un record A in tale zona che punti * all'indirizzo IP del servizio ILB 
- creare una zona in *&lt;nome ambiente del servizio app&gt;.appserviceenvironment.net* denominata SCM
- creare un record A nella zona che punti * all'indirizzo IP del servizio ILB

## <a name="publish-with-an-ilb-ase"></a>Pubblicare con un ambiente del servizio app ILB

Per ogni app creata sono disponibili due endpoint. In un ambiente del servizio app ILB sono presenti *&lt;nome app&gt;.&lt;Dominio ambiente del servizio app ILB&gt;* e *&lt;nome app&gt;.scm.&lt;Dominio ambiente del servizio app ILB&gt;* . 

Il nome del sito di gestione controllo servizi consente di passare alla console Kudu, denominata **Advanced Portal** (Portale avanzato) all'interno del portale di Azure. La console Kudu consente di visualizzare le variabili di ambiente, esplorare il disco, usare una console e molto altro ancora. Per informazioni, vedere [Kudu console for Azure App Service][Kudu] (Console Kudu per il servizio app di Azure). 

I sistemi di integrazione continua basati su Internet, come GitHub e Azure DevOps, continueranno a funzionare con un ambiente del servizio app ILB se l'agente di compilazione è accessibile da Internet e si trova nella stessa rete dell'ambiente del servizio app ILB. Quindi, nel caso di Azure DevOps, se l'agente di compilazione viene creato nella stessa rete virtuale dell'ambiente del servizio app ILB (anche se la subnet è diversa), potrà eseguire il pull del codice dal GIT di Azure DevOps e distribuirlo nell'ambiente del servizio app ILB. Se non si vuole creare il proprio agente di compilazione, è necessario usare un sistema di integrazione continua che adotta un modello pull, ad esempio Dropbox.

Gli endpoint di pubblicazione per le app in un ambiente del servizio app con bilanciamento del carico interno usano il dominio con cui l'ambiente del servizio app con bilanciamento del carico interno è stato creato, che può essere visualizzato nel profilo di pubblicazione dell'app e nel pannello del portale dell'app (in **Panoramica** > **Informazioni di base** e anche in **Proprietà**). Se si ha un ambiente del servizio app ILB con il suffisso di dominio *&lt;nome ambiente del servizio app&gt;.appserviceenvironment.net* e un'app denominata *mytest*, usare *mytest.&lt;nome ambiente del servizio app&gt;.appserviceenvironment.net* per il servizio FTP e *mytest.scm.contoso.net* per la distribuzione Web.

## <a name="configure-an-ilb-ase-with-a-waf-device"></a>Configurare un ambiente del servizio app ILB con un dispositivo WAF ##

È possibile combinare un dispositivo web application firewall (WAF) con l'ambiente del servizio app ILB in modo da esporre a Internet solo le app desiderate e mantenere le altre accessibili solo dall'interno della rete virtuale. Questo consente, tra l'altro, di creare applicazioni multilivello sicure.

Per altre informazioni sulla configurazione dell'ambiente del servizio app ILB con un dispositivo WAF, vedere [Configurazione di un Web application firewall (WAF) per l'ambiente del servizio app][ASEWAF]. Questo articolo spiega come usare un'appliance virtuale Barracuda con il proprio ambiente del servizio app. Un'altra opzione consiste nell'usare il gateway applicazione Azure. Il gateway applicazione usa le regole di base OWASP per proteggere le applicazioni associate allo stesso. Per altre informazioni sul gateway applicazione Azure, vedere [Web application firewall (WAF)][AppGW].

## <a name="ilb-ases-made-before-may-2019"></a>Ambienti del servizio app creati prima del maggio 2019

Gli ambienti del servizio app con bilanciamento del carico interno creati prima del maggio 2019 richiedevano di impostare il suffisso di dominio durante la creazione dell'ambiente del servizio app. Richiedevano inoltre di caricare un certificato predefinito basato sul suffisso di dominio. In più, con un ambiente del servizio app ILB precedente, non è possibile eseguire l'accesso Single Sign-On alla console Kudu con le app che si trovano nell'ambiente. Durante la configurazione del DNS per un ambiente del servizio app ILB precedente, è necessario impostare il record A con caratteri jolly in una zona corrispondente al suffisso di dominio. 

## <a name="get-started"></a>Attività iniziali ##

* Per iniziare a usare gli ambienti del servizio app, vedere [Introduzione ad Ambiente del servizio app][Intro]. 

<!--Image references-->
[1]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-network.png
[2]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-webapp.png
[5]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-ipaddresses.png

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
[webapps]: ../overview.md
[mobileapps]: ../../app-service-mobile/app-service-mobile-value-prop.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md
[ConfigureSSL]: ../web-sites-purchase-ssl-web-site.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
[customdomain]: ../app-service-web-tutorial-custom-domain.md
[linuxapp]: ../containers/app-service-linux-intro.md
