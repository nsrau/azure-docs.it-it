---
title: Configurare il failover in più endpoint della rete CDN di Azure con Gestione traffico di Azure | Microsoft Docs
description: Informazioni su come configurare Gestione traffico di Azure con gli endpoint della rete CDN di Azure.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: magattus
ms.custom: ''
ms.openlocfilehash: 956df17c821b86d95b1d87c3c8d8197bab7a95be
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65955276"
---
# <a name="set-up-failover-across-multiple-azure-cdn-endpoints-with-azure-traffic-manager"></a>Configurare il failover in più endpoint della rete CDN di Azure con Gestione traffico di Azure

Quando si configura la rete per la distribuzione di contenuti, o rete CDN di Azure, è possibile selezionare il provider e il livello di prezzo ottimali per le proprie esigenze. La rete CDN di Azure, con la sua infrastruttura distribuita a livello globale, crea per impostazione predefinita ridondanza geografica e locale e bilanciamento del carico globale per migliorare le prestazioni e la disponibilità del servizio. Se un percorso non è disponibile per servire contenuto, le richieste vengono indirizzate automaticamente a un'altra posizione e viene usato il POP ottimale, in base a fattori quali la posizione della richiesta e il carico del server, per elaborare ogni richiesta client. 
 
Se si dispone di più profili di rete CDN, è possibile migliorare ulteriormente la disponibilità e le prestazioni con Gestione traffico di Azure. È possibile usare Gestione traffico di Azure con la rete CDN di Azure per bilanciare il carico tra più endpoint della rete CDN per il failover, il bilanciamento del carico geografico e altri scenari. In uno scenario di failover tipico tutte le richieste client vengono indirizzate innanzitutto al profilo della rete CDN primario; se il profilo non è disponibile, le richieste vengono passate al profilo della rete CDN secondario fino a quando il profilo della rete CDN primario torna online. Usando Gestione traffico di Azure in questo modo si garantisce che l'applicazione Web sia sempre disponibile. 

Questo articolo include materiale sussidiario e un esempio di come configurare il failover con i profili **rete CDN Standard di Azure di Verizon** e **rete CDN Standard di Azure di Akamai**.

## <a name="set-up-azure-cdn"></a>Configurare la rete CDN di Azure 
Creare due o più profili ed endpoint di rete CDN di Azure con provider diversi.

1. Creare un profilo **rete CDN Standard di Azure di Verizon** e un profilo **rete CDN Standard di Azure di Akamai** seguendo i passaggi descritti in [Creare un nuovo profilo di rete CDN](cdn-create-new-endpoint.md#create-a-new-cdn-profile).
 
   ![Più profili CDN](./media/cdn-traffic-manager/cdn-multiple-profiles.png)

2. In ognuno dei nuovi profili creare almeno un endpoint seguendo i passaggi descritti in [Creare un nuovo endpoint della rete CDN](cdn-create-new-endpoint.md#create-a-new-cdn-endpoint).

## <a name="set-up-azure-traffic-manager"></a>Configurare Gestione traffico di Azure
Creare un profilo di Gestione traffico di Azure e configurare il bilanciamento del carico tra gli endpoint della rete CDN. 

1. Creare un profilo di Gestione traffico di Azure seguendo i passaggi descritti in [Creare un profilo di Gestione traffico](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-create-profile). 

    Per **Routing method** (Metodo di routing) selezionare **Priority** (Priorità).

2. Aggiungere gli endpoint della rete CDN nel profilo di Gestione traffico seguendo la procedura descritta in [Aggiungere endpoint di Gestione traffico](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-create-profile#add-traffic-manager-endpoints)

    Per **Type** (Tipo) selezionare **External endpoints** (Endpoint esterni). Per **Priority** (Priorità) immettere un numero.

    Ad esempio, creare *cdndemo101akamai.azureedge.net* con la priorità *1* e *cdndemo101verizon.azureedge.net* con la priorità *2*.

   ![Endpoint di Gestione traffico della rete CDN](./media/cdn-traffic-manager/cdn-traffic-manager-endpoints.png)


## <a name="set-up-custom-domain-on-azure-cdn-and-azure-traffic-manager"></a>Configurare un dominio personalizzato nella rete CDN di Azure e in Gestione traffico di Azure
Dopo aver configurato i profili della rete CDN e di Gestione traffico, seguire questi passaggi per aggiungere il mapping DNS e registrare il dominio personalizzato negli endpoint della rete CDN. Per questo esempio il nome di dominio personalizzato è *cdndemo101.dustydogpetcare.online*.

1. Passare al sito Web per il provider di dominio del dominio personalizzato, ad esempio GoDaddy, e creare due voci DNS CNAME. 

    a. Per la prima voce CNAME mappare il dominio personalizzato, con il sottodominio cdnverify, all'endpoint della rete CDN. Questa voce è un passaggio obbligatorio per registrare il dominio personalizzato nell'endpoint della rete CDN che è stato aggiunto a Gestione traffico nel passaggio 2.

      Ad esempio: 

      `cdnverify.cdndemo101.dustydogpetcare.online  CNAME  cdnverify.cdndemo101akamai.azureedge.net`  

    b. Per la seconda voce CNAME mappare il dominio personalizzato, senza il sottodominio cdnverify, all'endpoint della rete CDN. Questa voce esegue il mapping del dominio personalizzato a Gestione traffico. 

      Ad esempio: 
      
      `cdndemo101.dustydogpetcare.online  CNAME  cdndemo101.trafficmanager.net`   

    > [!NOTE]
    > Se il dominio è attualmente attivo e non può essere interrotto, eseguire questo passaggio in un secondo momento. Prima di aggiornare il DNS del dominio personalizzato a Gestione traffico, verificare che gli endpoint della rete CDN e i domini di Gestione traffico siano attivi.
    >


2.  Dal profilo della rete CDN di Azure selezionare il primo endpoint della rete CDN (Akamai). Selezionare **Aggiungi dominio personalizzato** e di input *cdndemo101.dustydogpetcare.online*. Verificare che il segno di spunta per convalidare il dominio personalizzato sia verde. 

    La rete CDN di Azure usa il sottodominio *cdnverify* per convalidare il mapping DNS per completare questo processo di registrazione. Per altre informazioni, vedere [Creare un record DNS CNAME](cdn-map-content-to-custom-domain.md#create-a-cname-dns-record). Questo passaggio consente alla rete CDN di Azure di riconoscere il dominio personalizzato in modo da rispondere alle sue richieste.
    
 > [!NOTE]
    > Per abilitare SSL in un' **rete CDN di Azure fornita da Akamai** profili, è necessario direttamente cname di dominio personalizzato all'endpoint. cdnverify per l'abilitazione di SSL non è ancora supportata. 
    >

3.  Tornare al sito Web per il provider di dominio del dominio personalizzato e aggiornare il primo mapping DNS creato, in modo che il dominio personalizzato sia mappato al secondo endpoint della rete CDN.
                             
    Ad esempio: 

    `cdnverify.cdndemo101.dustydogpetcare.online  CNAME  cdnverify.cdndemo101verizon.azureedge.net`  

4. Dal profilo della rete CDN di Azure selezionare il secondo endpoint della rete CDN, Verizon, e ripetere il passaggio 2. Selezionare **Aggiungi dominio personalizzato**e di input *cdndemo101.dustydogpetcare.online*.
 
Dopo aver completato questi passaggi, il servizio con più reti CDN e funzionalità di failover è configurato con Gestione traffico di Azure. Si potrà accedere agli URL di test dal dominio personalizzato. Per testare la funzionalità, disabilitare l'endpoint primario della rete CDN e verificare che la richiesta sia spostata correttamente all'endpoint secondario della rete CDN. 

## <a name="next-steps"></a>Passaggi successivi
È possibile anche configurare altri metodi di routing, ad esempio il routing geografico, per bilanciare il carico tra endpoint diversi della rete CDN. Per altre informazioni, vedere [Configurare il metodo di routing del traffico Geografico tramite Gestione traffico](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-configure-geographic-routing-method).



