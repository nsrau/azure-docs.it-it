---
title: "Panoramica delle porte a disponibilità elevata in Azure | Microsoft Docs"
description: "Informazioni sul bilanciamento del carico con le porte a disponibilità elevata in un servizio di bilanciamento del carico interno"
services: load-balancer
documentationcenter: na
author: rdhillon
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 46b152c5-6a27-4bfc-bea3-05de9ce06a57
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/26/2017
ms.author: kumud
ms.openlocfilehash: e72fc0d4323f7a2d203fee66311c3fea10ad7a09
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2017
---
# <a name="high-availability-ports-overview-preview"></a>Panoramica delle porte a disponibilità elevata (anteprima)

Azure Load Balancer Standard introduce una nuova capacità. Quando si usa un servizio di bilanciamento del carico interno, infatti, offre la possibilità di bilanciare il carico dei flussi TCP e UDP su tutte le porte contemporaneamente. 

>[!NOTE]
> La funzionalità Porte a disponibilità elevata è attualmente disponibile in anteprima con Load Balancer Standard. Durante l'anteprima, la funzionalità potrebbe non avere lo stesso livello di disponibilità e affidabilità delle funzionalità presenti nella versione con disponibilità generale. Per altre informazioni, vedere [Condizioni Supplementari di Microsoft Azure le Anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Per usare la funzionalità Porte a disponibilità elevata con le risorse Load Balancer Standard è necessario iscriversi a Load Balancer Standard (anteprima). Seguire le istruzioni per registrarsi anche per l'[anteprima di Load Balancer Standard](https://aka.ms/lbpreview#preview-sign-up).

Una regola delle porte a disponibilità elevata è una variante di una regola di bilanciamento del carico configurata in un servizio Load Balancer Standard interno.  Per semplificare gli scenari è possibile specificare un'unica regola di bilanciamento del carico per tutti i flussi TCP e UDP che raggiungono tutte le porte di un front-end Load Balancer Standard interno. La decisione di bilanciamento del carico viene presa per ogni flusso in base alle cinque tuple corrispondenti a indirizzo IP di origine, porta di origine, indirizzo IP di destinazione, porta di destinazione e protocollo.

Le porte a disponibilità elevata consentono scenari critici, ad esempio la disponibilità e la scalabilità elevate necessarie per le appliance virtuali di rete all'interno di reti virtuali, oltre ad altri scenari in cui è necessario eseguire il bilanciamento del carico di un numero di porte elevato. 

Le porte a disponibilità elevata vengono configurate impostando le porte front-end e back-end su **0** e il protocollo su **All**.  La risorsa Load Balancer interna esegue ora il bilanciamento di tutti i flussi TCP e UDP, indipendentemente dal numero di porta.

## <a name="why-use-ha-ports"></a>Vantaggi dell'uso delle porte a disponibilità elevata

### <a name="nva"></a>Appliance virtuali di rete

È possibile usare appliance virtuali di rete per proteggere il carico di lavoro di Azure da diversi tipi di minacce alla sicurezza. Le appliance virtuali di rete usate in questi scenari devono essere affidabili e a disponibilità elevata, ed essere scalabili orizzontalmente in base alle esigenze.

Per raggiungere questi obiettivi nello scenario corrente è sufficiente aggiungere istanze di appliance virtuali di rete al pool di back-end di Azure Load Balancer interno e configurare una regola di bilanciamento del carico delle porte a disponibilità elevata.

Nell'ambito di scenari a disponibilità elevata di appliance virtuali di rete, le porte a disponibilità elevata offrono diversi vantaggi:
- rapidità di failover verso istanze integre con probe di integrità per singola istanza
- prestazioni più elevate con scalabilità orizzontale fino a n istanze attive
- scenari di n istanze attive e di istanze attive-passive
- eliminazione della necessità di soluzioni complesse, ad esempio nodi Zookeeper, per il monitoraggio delle appliance

L'esempio seguente presenta una distribuzione della rete virtuale di tipo hub e spoke, in cui gli spoke forzano il tunneling del traffico verso la rete virtuale dell'hub e attraverso l'appliance virtuale di rete prima che il traffico lasci lo spazio attendibile. Le appliance virtuali di rete si trovano dietro a un servizio Load Balancer Standard con configurazione con porte a disponibilità elevata.  Tutto il traffico può essere elaborato e inoltrato di conseguenza. 

![Esempio di porte a disponibilità elevata](./media/load-balancer-ha-ports-overview/nvaha.png)

Figura 1: Rete virtuale hub e spoke con appliance virtuali di rete distribuite in modalità a disponibilità elevata

Se si usano appliance virtuali di rete, verificare con il provider come usare al meglio le porte a disponibilità elevata e quali scenari sono supportati.

### <a name="load-balancing-large-numbers-of-ports"></a>Bilanciamento del carico di un numero elevato di porte

È anche possibile usare porte a disponibilità elevata per scenari di applicazioni che richiedono il bilanciamento del carico di un numero elevato di porte. Questi scenari possono essere semplificati tramite un servizio [Load Balancer Standard](https://aka.ms/lbpreview) interno con porte a disponibilità elevata in cui una singola regola di bilanciamento di carico sostituisce regole di bilanciamento di carico multiple, una per ogni porta.

## <a name="region-availability"></a>Aree di disponibilità

Le porte a disponibilità elevata sono disponibili nelle [stesse aree di Load Balancer Standard](https://aka.ms/lbpreview#region-availability).  

## <a name="preview-sign-up"></a>Iscrizione all'anteprima

Per partecipare all'anteprima della funzionalità delle porte a disponibilità elevata nel servizio Load Balancer Standard e poter accedere tramite interfaccia della riga di comando di Azure 2.0 o PowerShell, è necessario effettuare una sottoscrizione.  Eseguire questi tre passaggi:

>[!NOTE]
>Per usare questa funzionalità, è necessario iscriversi all'[Anteprima Standard](https://aka.ms/lbpreview#preview-sign-up) di Load Balancer oltre alla funzionalità Porte a disponibilità elevata. La registrazione delle funzionalità Porte a disponibilità elevata o Load Balancer Standard (anteprima) potrebbe richiedere fino a un'ora.

### <a name="sign-up-using-azure-cli-20"></a>Iscrizione tramite l'interfaccia della riga di comando di Azure 2.0

1. Registrare la funzionalità con il provider
    ```cli
    az feature register --name AllowILBAllPortsRule --namespace Microsoft.Network
    ```
    
2. Il completamento dell'operazione precedente può richiedere fino a 10 minuti.  È possibile controllare lo stato dell'operazione con il comando seguente:

    ```cli
    az feature show --name AllowILBAllPortsRule --namespace Microsoft.Network
    ```
    
    Quando lo stato della registrazione della funzionalità corrisponde a 'Registrato', come illustrato di seguito, procedere con il passaggio 3:
   
    ```json
    {
       "id": "/subscriptions/foo/providers/Microsoft.Features/providers/Microsoft.Network/features/AllowLBPreview",
       "name": "Microsoft.Network/AllowILBAllPortsRule",
       "properties": {
          "state": "Registered"
       },
       "type": "Microsoft.Features/providers/features"
    }
    ```
    
3. Completare la registrazione all'anteprima registrando nuovamente la sottoscrizione con il provider di risorse:

    ```cli
    az provider register --namespace Microsoft.Network
    ```
    
### <a name="sign-up-using-powershell"></a>Iscrizione tramite PowerShell

1. Registrare la funzionalità con il provider
    ```powershell
    Register-AzureRmProviderFeature -FeatureName AllowILBAllPortsRule -ProviderNamespace Microsoft.Network
    ```
    
2. Il completamento dell'operazione precedente può richiedere fino a 10 minuti.  È possibile controllare lo stato dell'operazione con il comando seguente:

    ```powershell
    Get-AzureRmProviderFeature -FeatureName AllowILBAllPortsRule -ProviderNamespace Microsoft.Network
    ```
    Quando lo stato della registrazione della funzionalità corrisponde a 'Registrato', come illustrato di seguito, procedere con il passaggio 3:
   
    ```
    FeatureName          ProviderName      RegistrationState
    -----------          ------------      -----------------
    AllowILBAllPortsRule Microsoft.Network Registered
    ```
    
3. Completare la registrazione all'anteprima registrando nuovamente la sottoscrizione con il provider di risorse:

    ```powershell
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
    ```


## <a name="limitations"></a>Limitazioni

Di seguito sono elencate le configurazioni supportate o le eccezioni per le porte a disponibilità elevata:

- Una singola configurazione IP front-end può avere una sola regola di bilanciamento del carico DSR con porte a disponibilità elevata o una sola regola del servizio di bilanciamento del carico non DSR con porte a disponibilità elevata. Non può averle entrambe.
- Una singola configurazione IP dell'interfaccia di rete può avere solo una regola di bilanciamento del carico non DSR con porte a disponibilità elevata. Non possono essere configurate altre regole per questa configurazione IP.
- Una singola configurazione IP dell'interfaccia di rete può avere una o più regole di bilanciamento del carico DSR con porte a disponibilità elevata, purché tutte le rispettive configurazioni IP front-end siano univoche.
- Se tutte le regole di bilanciamento del carico sono relative alle porte a disponibilità elevata (solo DSR) o se tutte le regole sono relative alle porte non a disponibilità elevata (DSR e non DSR), due o più regole di bilanciamento del carico che puntano allo stesso pool back-end possono coesistere. Due regole di bilanciamento del carico di questo tipo non possono coesistere se è presente una combinazione di regole per porte a disponibilità elevata e per porte non a disponibilità elevata.
- Le porte a disponibilità elevata non sono disponibili per IPv6.
- La simmetria di flusso per gli scenari di appliance virtuali di rete è supportata soltanto con una sola scheda di interfaccia di rete. Vedere la descrizione e il diagramma per [gli appliance virtuali di rete](#nva). 



## <a name="next-steps"></a>Passaggi successivi

- [Configurare le porte a disponibilità elevata per un servizio Load Balancer Standard interno](load-balancer-configure-ha-ports.md)
- [Informazioni sull'anteprima di Load Balancer Standard](https://aka.ms/lbpreview)

