---
title: Integrare l'ambiente del servizio app con bilanciamento del carico interno con un gateway applicazione
description: Descrizione dettagliata dell'integrazione di un'app dell'ambiente del servizio app con bilanciamento del carico interno con un gateway applicazione
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
ms.openlocfilehash: d56eab79c3b3f6b37dc39d8e4bea0d5b7759631a
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/18/2018
---
# <a name="integrate-your-ilb-app-service-environment-with-an-application-gateway"></a>Integrare l'ambiente del servizio app con bilanciamento del carico interno con un gateway applicazione #

L'[ambiente del servizio app per PowerApps](./intro.md) è una distribuzione del servizio app di Azure nella subnet della rete virtuale di Azure di un cliente. Può essere distribuito con un endpoint pubblico o privato per l'accesso dell'app. La distribuzione dell'ambiente del servizio app con un endpoint privato, ovvero un servizio di bilanciamento del carico interno, viene definita ambiente del servizio app con bilanciamento del carico interno.  

Il gateway applicazione di Azure è un'appliance virtuale che offre bilanciamento del carico di livello 7, offload SSL e protezione web application firewall (WAF). Può essere in ascolto su un indirizzo IP pubblico e instradare il traffico all'endpoint applicazione. 

Le informazioni seguenti illustrano come integrare un gateway applicazione configurato WAF con un'app di un ambiente del servizio app con bilanciamento del carico interno.  

L'integrazione del gateway applicazione con l'ambiente del servizio app con bilanciamento del carico interno viene eseguita a livello di app. La configurazione del gateway applicazione con l'ambiente del servizio app con bilanciamento del carico interno viene eseguita per app specifiche nell'ambiente del servizio app con bilanciamento del carico interno. Questa tecnica consente l'hosting di applicazioni multitenant sicure in un unico ambiente del servizio app con bilanciamento del carico interno.  

![Gateway applicazione che punta all'app in un ambiente del servizio app con bilanciamento del carico interno][1]

In questa procedura dettagliata si eseguiranno i passaggi seguenti:

* Creare un gateway applicazione.
* Configurare il gateway applicazione in modo che punti a un'app nell'ambiente del servizio app con bilanciamento del carico interno.
* Configurare l'app in modo che rispetti il nome di dominio personalizzato.
* Modificare il nome host DNS pubblico che punta al gateway applicazione.

## <a name="prerequisites"></a>Prerequisiti

Per integrare il gateway applicazione con l'ambiente del servizio app con bilanciamento del carico interno sono necessari:

* Un ambiente del servizio app con bilanciamento del carico interno.
* Un'app in esecuzione nell'ambiente del servizio app con bilanciamento del carico interno.
* Un nome di dominio instradabile su Internet da usare con l'app nell'ambiente del servizio app con bilanciamento del carico interno.
* L'indirizzo del servizio di bilanciamento del carico interno usato nell'ambiente del servizio app. Questa informazione è disponibile nel portale Ambiente del servizio app in **Impostazioni** > **Indirizzi IP**:

    ![Esempio di elenco di indirizzi IP usati nell'ambiente del servizio app con bilanciamento del carico interno][9]
    
* Un nome DNS pubblico che verrà usato in un secondo momento per puntare al gateway applicazione. 

Per altre informazioni su come creare un ambiente del servizio app con bilanciamento del carico interno, vedere [Creazione e uso di un ambiente del servizio app con bilanciamento del carico interno][ilbase].

Questo articolo presuppone che si voglia creare un gateway applicazione nella stessa rete virtuale di Azure in cui è distribuito l'ambiente del servizio app. Prima di iniziare a creare il gateway applicazione, selezionare o creare una subnet da usare per ospitare il gateway. 

È consigliabile usare una subnet diversa da quella denominata GatewaySubnet. Se si inserisce il gateway applicazione in GatewaySubnet, non sarà possibile in seguito creare un gateway di rete virtuale. 

Non è possibile inserire il gateway nemmeno nella subnet usata dall'ambiente del servizio app con bilanciamento del carico interno. L'unico elemento che può essere presente in questa subnet è l'ambiente del servizio app.

## <a name="configuration-steps"></a>Procedura di configurazione ##

1. Nel portale di Azure passare a **Nuovo** > **Rete** > **Gateway applicazione**.

2. Nell'area **Informazioni di base**:

   a. In **Nome** immettere il nome del gateway applicazione.

   b. In **Livello** selezionare **WAF**.

   c. In **Sottoscrizione** selezionare la stessa sottoscrizione usata dalla rete virtuale dell'ambiente del servizio app.

   d. In **Gruppo di risorse** creare o selezionare il gruppo di risorse.

   e. In **Località** selezionare l'area geografica della rete virtuale dell'ambiente del servizio app.

   ![Informazioni di base sulla creazione del nuovo gateway applicazione][2]

3. Nell'area **Impostazioni**:

   a. In **Rete virtuale** selezionare la rete virtuale dell'ambiente del servizio app.

   b. In **Subnet** selezionare la subnet in cui distribuire il gateway applicazione. Non usare GatewaySubnet perché impedirà la creazione di gateway VPN.

   c. In **Tipo di indirizzo IP** selezionare **Pubblico**.

   d. In **Indirizzo IP pubblico** selezionare un indirizzo IP pubblico. Se non sono disponibili indirizzi di questo tipo, crearne uno.

   e. In **Protocollo** selezionare **HTTP** o **HTTPS**. Se si sceglie la configurazione per HTTPS, è necessario specificare un certificato PFX.

   f. In **Web application firewall** è possibile abilitare il firewall e impostarlo per **Rilevamento** o **Prevenzione** a seconda delle esigenze.

   ![Impostazioni per la creazione del nuovo gateway applicazione][3]
    
4. Nella sezione **Riepilogo** verificare le impostazioni e selezionare **OK**. Per completare la configurazione del gateway applicazione possono essere necessari poco più di 30 minuti.  

5. Al termine della configurazione del gateway applicazione, passare al portale del gateway applicazione. Selezionare **Pool back-end**. Aggiungere l'indirizzo del servizio di bilanciamento del carico interno dell'ambiente del servizio app.

   ![Configurare il pool back-end][4]

6. Al termine della configurazione del pool back-end, selezionare **Probe integrità**. Creare un probe di integrità per il nome di dominio che si vuole usare per l'app. 

   ![Configurare probe di integrità][5]
    
7. Al termine della configurazione dei probe di integrità, selezionare **Impostazioni HTTP**. Modificare le impostazioni esistenti, selezionare **Usa probe personalizzato** e scegliere il probe configurato.

   ![Configurare le impostazioni HTTP][6]
    
8. Passare alla sezione **Panoramica** del gateway applicazione e copiare l'indirizzo IP pubblico usato per il gateway applicazione. Impostare tale indirizzo IP come record A per il nome di dominio dell'app o usare il nome DNS di tale indirizzo in un record CNAME. Selezionare l'indirizzo IP pubblico e copiarlo dall'interfaccia utente dell'indirizzo IP pubblico è più facile che copiarlo dal collegamento nella sezione **Panoramica** del gateway applicazione. 

   ![Portale del gateway applicazione][7]

9. Impostare il nome di dominio personalizzato per l'app nell'ambiente del servizio app con bilanciamento del carico interno. Passare all'app nel portale e in **Impostazioni** selezionare **Domini personalizzati**.

   ![Impostare il nome di dominio personalizzato per l'app][8]

Per informazioni sull'impostazione di nomi di dominio personalizzato per le app Web, vedere [Impostazione di nomi di dominio personalizzato per l'app Web][custom-domain]. Tuttavia, per un'app di un ambiente del servizio app con bilanciamento del carico interno non è prevista alcuna convalida del nome di dominio. Poiché si è proprietari del DNS che gestisce gli endpoint dell'app, è possibile inserirvi qualsiasi elemento. In questo caso non è necessario che il nome di dominio personalizzato che si aggiunge sia nel DNS, ma è ugualmente necessario configurarlo con l'app. 

Dopo aver completato la configurazione e aver lasciato passare un breve intervallo di tempo per consentire la propagazione delle modifiche del DNS, è possibile accedere all'app con il nome di dominio personalizzato creato. 


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
