---
title: Integrazione dell'ambiente del servizio app con bilanciamento del carico interno con un gateway applicazione di Azure
description: Descrizione dettagliata dell'integrazione di un'app nell'ambiente del servizio app con bilanciamento del carico interno con il gateway applicazione di Azure
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: a6a74f17-bb57-40dd-8113-a20b50ba3050
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2017
ms.author: ccompy
ms.openlocfilehash: eedad8824add7fe425d34975dab640fbee82c2bc
ms.sourcegitcommit: b723436807176e17e54f226fe00e7e977aba36d5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/19/2017
---
# <a name="integrating-your-ilb-ase-with-an-application-gateway"></a>Integrazione dell'ambiente del servizio app con bilanciamento del carico interno con un gateway applicazione #

L'[ambiente del servizio app di Azure](./intro.md) è una distribuzione del servizio app di Azure nella subnet della rete virtuale di Azure di un cliente. Può essere distribuito con un endpoint pubblico o privato per l'accesso dell'app. La distribuzione dell'ambiente del servizio app con un endpoint privato viene chiamata ambiente del servizio app con bilanciamento del carico interno.  
Il gateway applicazione di Azure è un'appliance virtuale che fornisce servizio di bilanciamento del carico di livello 7, offload SSL e protezione WAF. Può essere in ascolto su un indirizzo IP pubblico e instradare il traffico all'endpoint applicazione. Le informazioni seguenti descrivono come integrare un gateway applicazione configurato per WAF con un'app in un ambiente del servizio app con bilanciamento del carico interno.  

L'integrazione del gateway applicazione con l'ambiente del servizio app con bilanciamento del carico interno viene eseguita a livello di app.  La configurazione del gateway applicazione con l'ambiente del servizio app con bilanciamento del carico interno viene eseguita per app specifiche nell'ambiente del servizio app con bilanciamento del carico interno. Ciò consente l'hosting di applicazioni multi-tenant sicure in un singolo ambiente del servizio app con bilanciamento del carico interno.  

![Gateway applicazione che punta all'app in un ambiente del servizio app con bilanciamento del carico interno][1]

In questa procedura dettagliata si eseguiranno i passaggi seguenti:

* Creare un gateway applicazione
* Configurare il gateway applicazione in modo che punti a un'app nell'ambiente del servizio app con bilanciamento del carico interno
* Configurare l'app in modo che rispetti il nome di dominio personalizzato
* Modificare il nome host DNS pubblico che punta al gateway applicazione

Per integrare il gateway applicazione con l'ambiente del servizio app con bilanciamento del carico interno, sono necessari:

* Un ambiente del servizio app con bilanciamento del carico interno
* Un'app in esecuzione nell'ambiente del servizio app con bilanciamento del carico interno
* Un nome di dominio instradabile Internet da usare con l'app nell'ambiente del servizio app con bilanciamento del carico interno
* L'indirizzo del servizio di bilanciamento del carico interno usato dall'ambiente del servizio app con bilanciamento del carico interno (disponibile nel portale dell'ambiente del servizio app in **Impostazioni -> Indirizzi IP**)

    ![Indirizzi IP usati dall'ambiente del servizio app con bilanciamento del carico interno][9]
    
* Un nome DNS pubblico che verrà usato in un secondo momento per puntare al gateway applicazione 

Per informazioni dettagliate su come creare un ambiente del servizio app con bilanciamento del carico interno, vedere il documento [Creazione uso di un ambiente del servizio app con bilanciamento del carico interno][ilbase]

Questa guida presuppone che il gateway applicazione debba essere nella stessa rete virtuale in cui è distribuito l'ambiente del servizio app. Prima di avviare la creazione del gateway applicazione, selezionare o creare una subnet da usare per ospitare il gateway applicazione. È consigliabile usare una subnet diversa da quella denominata GatewaySubnet o dalla subnet usata dall'ambiente del servizio app con bilanciamento del carico interno.
Se si inserisce il gateway applicazione in GatewaySubnet, in seguito non sarà possibile creare un gateway di rete virtuale. Non è neppure possibile inserirlo nella subnet usata dall'ambiente del servizio app con bilanciamento del carico interno perché l'ambiente del servizio app è il solo elemento che può trovarsi nella subnet.

## <a name="steps-to-configure"></a>Passaggi per la configurazione ##

1. Dal portale di Azure andare a **Nuovo > Rete > Gateway applicazione** 
    1. Specificare:
        1. Nome del gateway applicazione
        1. Selezionare WAF
        1. Selezionare la stessa sottoscrizione usata per la rete virtuale dell'ambiente del servizio app
        1. Creare o selezionare il gruppo di risorse
        1. Selezionare la località in cui si trova la rete virtuale dell'ambiente del servizio app

    ![Informazioni di base sulla creazione del nuovo gateway applicazione][2]   
    1. Nell'area Impostazioni impostare:
        1. La rete virtuale dell'ambiente del servizio app
        1. La subnet in cui è necessario distribuire il gateway applicazione. Non usare GatewaySubnet perché impedirà la creazione di gateway VPN
        1. Selezionare Pubblico
        1. Selezionare un indirizzo IP pubblico. Se non se ne ha uno, crearlo ora
        1. Scegliere la configurazione per HTTP o HTTPS. Se si sceglie la configurazione per HTTPS, è necessario specificare un certificato PFX
        1. Selezionare le impostazioni per Web application firewall. È possibile abilitare il firewall e impostarlo per il rilevamento o la prevenzione nel modo più idoneo.

    ![Impostazioni per la creazione del nuovo gateway applicazione][3]
    
    1. Nella sezione di riepilogo fare clic su **OK**. Il completamento della configurazione del gateway applicazione può richiedere poco più di 30 minuti.  

2. Al termine della configurazione del gateway applicazione, andare al portale del gateway applicazione. Selezionare **Pool back-end**.  Aggiungere l'indirizzo del servizio di bilanciamento del carico interno per l'ambiente del servizio app con bilanciamento del carico interno.

    ![Configurare il pool back-end][4]

3. Al termine dell'elaborazione della configurazione del pool back-end, selezionare **Probe integrità**. Creare un probe di integrità per il nome di dominio che si vuole usare per l'app. 

    ![Configurare probe di integrità][5]
    
4. Al termine dell'elaborazione della configurazione dei probe di integrità, selezionare **Impostazioni HTTP**.  Modificare l'impostazione esistente, selezionare **Usa probe personalizzato** e selezionare il probe configurato.

    ![Configurare le impostazioni HTTP][6]
    
5. Andare alla **panoramica** del gateway applicazione e copiare l'indirizzo IP pubblico usato per il gateway applicazione.  Impostare tale indirizzo IP come record A per il nome di dominio dell'app o usare il nome DNS di tale indirizzo in un record CNAME.  Selezionare l'indirizzo IP pubblico e copiarlo dall'interfaccia utente dell'indirizzo IP pubblico è più facile che copiarlo dal collegamento nella sezione Panoramica del gateway applicazione. 

    ![Portale del gateway applicazione][7]

6. Impostare il nome di dominio personalizzato per l'app nell'ambiente del servizio app con bilanciamento del carico interno.  Andare all'app nel portale e in Impostazioni selezionare **Domini personalizzati**

![Impostare il nome di dominio personalizzato per l'app][8]

Per informazioni sull'impostazione di nomi di dominio personalizzato per le app Web, vedere [Impostazione di nomi di dominio personalizzato per l'app Web][custom-domain]. La differenza rispetto a un'app in un ambiente del servizio app con bilanciamento del carico interno e tale documento è che non vengono eseguite convalide nel nome di dominio.  Poiché si è proprietari del DNS che gestisce gli endpoint dell'app, è possibile inserirvi qualsiasi elemento. In questo caso non è necessario che il nome di dominio personalizzato che si aggiunge sia nel DNS, ma è ugualmente necessario configurarlo con l'app. 

Dopo che la configurazione è stata completata e che è stato consentito un breve intervallo di tempo per la propagazione delle modifiche del DNS, è possibile accedere all'app con il nome di dominio personalizzato creato. 


<!--IMAGES-->
[1]: ./media/integrate-with-application-gateway/appgw-highlevel.png
[2]: ./media/integrate-with-application-gateway/appgw-createbasics.png
[3]: ./media/integrate-with-application-gateway/appgw-createsettings.png
[4]: ./media/integrate-with-application-gateway/appgw-backendpool.png
[5]: ./media/integrate-with-application-gateway/appgw-healthprobe.png
[6]: ./media/integrate-with-application-gateway/appgw-httpsettings.png
[7]: ./media/integrate-with-application-gateway/appgw-publicip.png
[8]: ./media/integrate-with-application-gateway/appgw-customdomainname.png
[9]: ./media/integrate-with-application-gateway/appgw-iplist.png

<!--LINKS-->
[appgw]: http://docs.microsoft.com/azure/application-gateway/application-gateway-introduction
[custom-domain]: ../app-service-web-tutorial-custom-domain.md
[ilbase]: ./create-ilb-ase.md
