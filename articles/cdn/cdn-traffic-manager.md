---
title: Eseguire il failover tra più endpoint con gestione traffico
titleSuffix: Azure Content Delivery Network
description: Informazioni su come configurare il failover tra più endpoint della rete per la distribuzione di contenuti di Azure tramite Gestione traffico di Azure.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: how-to
ms.date: 10/08/2020
ms.author: allensu
ms.custom: ''
ms.openlocfilehash: b75643d0d526bae4d7b2879dffab3d90dbcbe1eb
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91875870"
---
# <a name="failover-across-multiple-endpoints-with-azure-traffic-manager"></a>Failover tra più endpoint con gestione traffico di Azure

Quando si configura la rete per la distribuzione di contenuti, o rete CDN di Azure, è possibile selezionare il provider e il livello di prezzo ottimali per le proprie esigenze. 

La rete CDN di Azure, con la sua infrastruttura distribuita a livello globale, crea per impostazione predefinita ridondanza geografica e locale e bilanciamento del carico globale per migliorare le prestazioni e la disponibilità del servizio. 

Se una località non è disponibile per gestire il contenuto, le richieste vengono indirizzate automaticamente a un'altra posizione. Il punto di presenza ottimale (POP) viene usato per gestire ogni richiesta client. Il routing automatico si basa su fattori come il percorso della richiesta e il carico del server.
 
Se si dispone di più profili di rete CDN, è possibile migliorare ulteriormente la disponibilità e le prestazioni con Gestione traffico di Azure. 

Usare gestione traffico di Azure con la rete CDN di Azure per bilanciare il carico tra più endpoint della rete CDN per:
 
* Failover
* Bilanciamento del carico geografico 

In uno scenario di failover tipico, tutte le richieste client vengono indirizzate al profilo di rete CDN primario. 

Se il profilo non è disponibile, le richieste vengono indirizzate al profilo secondario.  Le richieste vengono riprendete nel profilo primario quando torna online.

Usando Gestione traffico di Azure in questo modo si garantisce che l'applicazione Web sia sempre disponibile. 

Questo articolo fornisce indicazioni e un esempio di come configurare il failover con i profili da: 

* **Rete CDN standard di Azure da Verizon**
* **Rete CDN standard di Azure di Akamai**

È supportata anche la rete **CDN di Azure di Microsoft** .

## <a name="create-azure-cdn-profiles"></a>Creare profili della rete CDN di Azure
Creare due o più profili ed endpoint di rete CDN di Azure con provider diversi.

1. Creare due profili di rete CDN:
    * **Rete CDN standard di Azure da Verizon**
    * **Rete CDN standard di Azure di Akamai** 

    Per creare i profili, seguire i passaggi descritti in [creare un nuovo profilo CDN](cdn-create-new-endpoint.md#create-a-new-cdn-profile).
 
   ![Più profili CDN](./media/cdn-traffic-manager/cdn-multiple-profiles.png)

2. In ognuno dei nuovi profili creare almeno un endpoint seguendo i passaggi descritti in [Creare un nuovo endpoint della rete CDN](cdn-create-new-endpoint.md#create-a-new-cdn-endpoint).

## <a name="create-traffic-manager-profile"></a>Crea profilo di gestione traffico
Creare un profilo di gestione traffico di Azure e configurare il bilanciamento del carico negli endpoint della rete CDN. 

1. Creare un profilo di Gestione traffico di Azure seguendo i passaggi descritti in [Creare un profilo di Gestione traffico](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-create-profile). 

    * **Metodo di routing**selezionare **priorità**.

2. Aggiungere gli endpoint della rete CDN nel profilo di Gestione traffico seguendo la procedura descritta in [Aggiungere endpoint di Gestione traffico](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-create-profile#add-traffic-manager-endpoints)

    * **Digitare**, selezionare **endpoint esterni**.
    * **Priority**, immettere un numero.

    Ad esempio, creare **cdndemo101akamai.azureedge.net** con la priorità **1** e **cdndemo101verizon.azureedge.net** con la priorità **2**.

   ![Endpoint di Gestione traffico della rete CDN](./media/cdn-traffic-manager/cdn-traffic-manager-endpoints.png)


## <a name="configure-custom-domain-on-azure-cdn-and-azure-traffic-manager"></a>Configurare un dominio personalizzato nella rete CDN di Azure e in gestione traffico di Azure
Dopo aver configurato i profili della rete CDN e di gestione traffico, attenersi alla procedura seguente per aggiungere il mapping DNS e registrare il dominio personalizzato negli endpoint della rete CDN. Per questo esempio il nome di dominio personalizzato è **cdndemo101.dustydogpetcare.online**.

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


2.  Dal profilo della rete CDN di Azure selezionare il primo endpoint della rete CDN (Akamai). Selezionare **Aggiungi dominio personalizzato** e input **cdndemo101. dustydogpetcare. online**. Verificare che il segno di spunta per convalidare il dominio personalizzato sia verde. 

    La rete CDN di Azure usa il sottodominio **cdnverify** per convalidare il mapping DNS per completare questo processo di registrazione. Per altre informazioni, vedere [Creare un record DNS CNAME](cdn-map-content-to-custom-domain.md#create-a-cname-dns-record). Questo passaggio consente alla rete CDN di Azure di riconoscere il dominio personalizzato in modo da rispondere alle sue richieste.
    
    > [!NOTE]
    > Per abilitare TLS in una rete **CDN di Azure da profili Akamai** , è necessario eseguire il CNAME direttamente del dominio personalizzato all'endpoint. la cdnverify per l'abilitazione di TLS non è ancora supportata. 
    >

3.  Tornare al sito Web per il provider di dominio del dominio personalizzato. Aggiornare il primo mapping DNS creato. Eseguire il mapping del dominio personalizzato al secondo endpoint della rete CDN.
                             
    Ad esempio: 

    `cdnverify.cdndemo101.dustydogpetcare.online  CNAME  cdnverify.cdndemo101verizon.azureedge.net`  

4. Dal profilo della rete CDN di Azure selezionare il secondo endpoint della rete CDN, Verizon, e ripetere il passaggio 2. Selezionare **Aggiungi dominio personalizzato**e immettere **cdndemo101. dustydogpetcare. online**.
 
Dopo aver completato questi passaggi, il servizio di rete CDN con funzionalità di failover viene configurato con gestione traffico di Azure. 

È possibile accedere agli URL di test dal dominio personalizzato. 

Per testare la funzionalità, disabilitare l'endpoint primario della rete CDN e verificare che la richiesta sia spostata correttamente all'endpoint secondario della rete CDN. 

## <a name="next-steps"></a>Passaggi successivi
È possibile configurare altri metodi di routing, ad esempio geografico, per bilanciare il carico tra diversi endpoint della rete CDN. 

Per altre informazioni, vedere [Configurare il metodo di routing del traffico Geografico tramite Gestione traffico](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-configure-geographic-routing-method).



