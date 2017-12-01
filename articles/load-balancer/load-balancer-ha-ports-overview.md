---
title: "Panoramica delle porte a disponibilità elevata in Azure | Microsoft Docs"
description: "Informazioni sul bilanciamento del carico con le porte a disponibilità elevata in un servizio di bilanciamento del carico interno."
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
ms.openlocfilehash: 7a77e6ecbf59944c62aa4ae014bf5b8a5a7f7f1f
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2017
---
# <a name="high-availability-ports-overview"></a>Panoramica delle porta a disponibilità elevata

Azure Load Balancer Standard semplifica il processo di bilanciamento del carico dei flussi TCP e UDP su tutte le porte contemporaneamente quando si usa un servizio di bilanciamento del carico interno. 

>[!NOTE]
> La funzionalità Porte a disponibilità elevata è attualmente disponibile in anteprima con Load Balancer Standard. Durante l'anteprima, la funzionalità potrebbe non avere lo stesso livello di disponibilità e affidabilità delle funzionalità presenti nella versione con disponibilità generale. Per altre informazioni, vedere [Condizioni Supplementari di Microsoft Azure le Anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Per usare la funzionalità Porte a disponibilità elevata con le risorse Load Balancer Standard, iscriversi a Load Balancer Standard (anteprima). Seguire le istruzioni per registrarsi per l'[anteprima di Load Balancer Standard](https://aka.ms/lbpreview#preview-sign-up).

Una regola delle porte a disponibilità elevata è una variante di una regola di bilanciamento del carico configurata in un servizio Load Balancer Standard interno. Per semplificare l'uso di questo servizio, è possibile specificare un'unica regola di bilanciamento del carico per tutti i flussi TCP e UDP che raggiungono tutte le porte di un servizio Load Balancer Standard interno. La decisione di bilanciamento del carico viene presa per ogni flusso in base alle cinque tuple seguenti: indirizzo IP di origine, porta di origine, indirizzo IP di destinazione, porta di destinazione e protocollo.

La funzionalità Porte a disponibilità elevata consente di gestire scenari critici, ad esempio la disponibilità elevata e la scalabilità per appliance di rete virtuale all'interno di reti virtuali. Può essere utile anche quando è necessario eseguire il bilanciamento del carico di un numero elevato di porte. 

La funzionalità Porte a disponibilità elevata viene configurata quando si imposta le porte front-end e back-end su **0**e il protocollo su **Tutti**. La risorsa Load Balancer interna esegue quindi il bilanciamento di tutti i flussi TCP e UDP, indipendentemente dal numero di porta.

## <a name="why-use-ha-ports"></a>Vantaggi dell'uso delle porte a disponibilità elevata

### <a name="nva"></a>Appliance di rete virtuale

È possibile usare appliance di rete virtuale per proteggere il carico di lavoro di Azure da diversi tipi di minacce alla sicurezza. Le appliance di rete virtuale usate in questi scenari devono essere affidabili e a disponibilità elevata, ed essere scalabili orizzontalmente in base alle esigenze.

Per raggiungere questi obiettivi è sufficiente aggiungere istanze di appliance di rete virtuale al pool back-end di Azure Load Balancer interno e configurare una regola di bilanciamento del carico delle porte a disponibilità elevata.

Nell'ambito di scenari a disponibilità elevata di appliance di rete virtuale le porte a disponibilità elevata offrono diversi vantaggi:
- rapidità di failover verso istanze integre con probe di integrità per singola istanza
- prestazioni più elevate con scalabilità orizzontale fino a *n* istanze attive
- scenari di *n* istanze attive e di istanze attive-passive
- eliminazione della necessità di soluzioni complesse, ad esempio nodi Apache ZooKeeper, per il monitoraggio delle appliance

Il diagramma seguente illustra una distribuzione di rete virtuale hub e spoke. Gli spoke effettuano il tunneling forzato del relativo traffico verso la rete virtuale hub e tramite l'appliance di rete virtuale prima di abbandonare lo spazio attendibile. Le appliance di rete virtuale si trovano dietro a un servizio Load Balancer Standard con configurazione con porte a disponibilità elevata. Tutto il traffico può essere elaborato e inoltrato di conseguenza.

![Diagramma della rete virtuale hub e spoke con appliance di rete virtuale distribuite in modalità a disponibilità elevata](./media/load-balancer-ha-ports-overview/nvaha.png)

>[!NOTE]
> Se si usano appliance di rete virtuale, verificare con il provider come usare al meglio le porte a disponibilità elevata e quali scenari sono supportati.

### <a name="load-balancing-large-numbers-of-ports"></a>Bilanciamento del carico di un numero elevato di porte

È anche possibile usare porte a disponibilità elevata per applicazioni che richiedono il bilanciamento del carico di un numero elevato di porte. È possibile semplificare questi scenari con un servizio [Load Balancer Standard](https://aka.ms/lbpreview) interno e porte a disponibilità elevata. Un'unica regola di bilanciamento del carico sostituisce più regole di bilanciamento del carico associate alle singole porte.

## <a name="region-availability"></a>Aree di disponibilità

La funzionalità Porte a disponibilità elevata è disponibile nelle [stesse aree di Load Balancer Standard](https://aka.ms/lbpreview#region-availability).  

## <a name="preview-sign-up"></a>Iscrizione all'anteprima

Per partecipare e accedere all'anteprima della funzionalità Porte a disponibilità elevata nel servizio Load Balancer Standard, è necessario effettuare una sottoscrizione. È possibile usare l'interfaccia della riga di comando di Azure 2.0 o PowerShell.

>[!NOTE]
>Per usare questa funzionalità, è necessario iscriversi all'[anteprima di Load Balancer Standard](https://aka.ms/lbpreview#preview-sign-up) oltre alla funzionalità Porte a disponibilità elevata. La registrazione può richiedere fino a un'ora.

### <a name="sign-up-by-using-azure-cli-20"></a>Registrazione eseguita con l'interfaccia della riga di comando di Azure 2.0

1. Registrare la funzionalità con il provider:
    ```cli
    az feature register --name AllowILBAllPortsRule --namespace Microsoft.Network
    ```
    
2. Il completamento dell'operazione precedente può richiedere fino a 10 minuti. È possibile controllare lo stato dell'operazione con il comando seguente:

    ```cli
    az feature show --name AllowILBAllPortsRule --namespace Microsoft.Network
    ```
    
    L'operazione è stata completata correttamente quando lo stato della registrazione della funzionalità restituisce **Registrato**, come illustrato di seguito:
   
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
    
### <a name="sign-up-by-using-powershell"></a>Registrazione eseguita con PowerShell

1. Registrare la funzionalità con il provider:
    ```powershell
    Register-AzureRmProviderFeature -FeatureName AllowILBAllPortsRule -ProviderNamespace Microsoft.Network
    ```
    
2. Il completamento dell'operazione precedente può richiedere fino a 10 minuti. È possibile controllare lo stato dell'operazione con il comando seguente:

    ```powershell
    Get-AzureRmProviderFeature -FeatureName AllowILBAllPortsRule -ProviderNamespace Microsoft.Network
    ```
    L'operazione è stata completata correttamente quando lo stato della registrazione della funzionalità restituisce **Registrato**, come illustrato di seguito:
   
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

Di seguito sono elencate le configurazioni supportate o le eccezioni per la funzionalità Porte a disponibilità elevata:

- Una singola configurazione IP front-end può avere una sola regola di bilanciamento del carico DSR con porte a disponibilità elevata o una sola regola del servizio di bilanciamento del carico non DSR con porte a disponibilità elevata. Non può averle entrambe.
- Una singola configurazione IP dell'interfaccia di rete può avere solo una regola di bilanciamento del carico non DSR con porte a disponibilità elevata. Non è possibile configurare qualsiasi altra regola per questa configurazione IP.
- Una singola configurazione IP dell'interfaccia di rete può avere una o più regole di bilanciamento del carico DSR con porte a disponibilità elevata, purché tutte le rispettive configurazioni IP front-end siano univoche.
- Se tutte le regole di bilanciamento del carico sono relative alle porte a disponibilità elevata (solo DSR), possono coesistere due o più regole di bilanciamento del carico che puntano allo stesso pool back-end. Lo stesso vale se tutte le regole sono relative a porte non a disponibilità elevata (DSR e non DSR). Due regole di bilanciamento del carico di questo tipo non possono coesistere se è presente una combinazione di regole per porte a disponibilità elevata e per porte non a disponibilità elevata.
- La funzionalità Porte a disponibilità elevata non è disponibile per IPv6.
- La simmetria di flusso per gli scenari di appliance di rete virtuale è supportata soltanto con una sola scheda di interfaccia di rete. Vedere la descrizione e il diagramma per [gli appliance di rete virtuale](#nva). 



## <a name="next-steps"></a>Passaggi successivi

- [Configurare le porte a disponibilità elevata per un servizio Load Balancer Standard interno](load-balancer-configure-ha-ports.md)
- [Informazioni sull'anteprima di Load Balancer Standard](https://aka.ms/lbpreview)

