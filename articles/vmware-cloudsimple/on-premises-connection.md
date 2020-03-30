---
title: Azure VMware Solution by CloudSimple - On-premises connection using ExpressRoute
description: Descrive come richiedere una connessione locale usando ExpressRoute dalla rete di aree CloudSimpleDescribes how to request an on-premises connection using ExpressRoute from CloudSimple region network
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 0dd5ede110255b6e53bbc397e683e66b3beffc65
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77019622"
---
# <a name="connect-from-on-premises-to-cloudsimple-using-expressroute"></a>Connettersi da locale a CloudSimple usando ExpressRouteConnect from on-premises to CloudSimple using ExpressRoute

Se si dispone già di una connessione Azure ExpressRoute da una posizione esterna (ad esempio in locale) ad Azure, è possibile connetterla all'ambiente CloudSimple.If you already have an Azure ExpressRoute connection from an external location (such as on-premises) to Azure, you can connect it to your CloudSimple environment. È possibile eseguire questa operazione tramite una funzionalità di Azure che consente a due circuiti ExpressRoute di connettersi tra loro. Questo metodo stabilisce una connessione sicura, privata, ad alta larghezza di banda, a bassa latenza tra i due ambienti.

[![Connessione ExpressRoute locale - Copertura globale](media/cloudsimple-global-reach-connection.png)](media/cloudsimple-global-reach-connection.png)

## <a name="before-you-begin"></a>Prima di iniziare

Per stabilire la connessione a Global Reach dall'ambiente locale è necessario un blocco di indirizzi di rete **/29.A /29** network address block is required for establishing Global Reach connection from on-premises.  Lo spazio degli indirizzi /29 viene utilizzato per la rete di transito tra circuiti ExpressRoute.The /29 address space is used for transit network between ExpressRoute circuits.  La rete di transito non deve sovrapporsi alle reti virtuali di Azure, alle reti locali o alle reti CloudSimple Private Cloud.

## <a name="prerequisites"></a>Prerequisiti

* È necessario un circuito Azure ExpressRoute prima di poter stabilire la connessione tra il circuito e le reti CloudSimple Private Cloud.
* A un utente è necessario con privilegi per creare chiavi di autorizzazione in un circuito ExpressRoute.A user is required with privileges to create authorization keys on an ExpressRoute circuit.

## <a name="scenarios"></a>Scenari

La connessione della rete locale alla rete cloud privata consente di usare il cloud privato in vari modi, inclusi gli scenari seguenti:

* Accedi alla tua rete Private Cloud senza creare una connessione VPN da sito a sito.
* Usare Active Directory locale come origine di identità nel cloud privato.
* Eseguire la migrazione di macchine virtuali in esecuzione in locale nel cloud privato.
* Usa il tuo cloud privato come parte di una soluzione di ripristino di emergenza.
* Usare le risorse locali nelle macchine virtuali del carico di lavoro del cloud privato.

## <a name="connecting-expressroute-circuits"></a>Collegamento di circuiti ExpressRouteConnecting ExpressRoute circuits

To establish the ExpressRoute connection, you must create an authorization on your ExpressRoute circuit and provide the authorization information to CloudSimple.


### <a name="create-expressroute-authorization"></a>Creare l'autorizzazione ExpressRouteCreate ExpressRoute authorization

1. Accedere al portale di Azure.

2. Nella barra di ricerca superiore cercare **Circuito ExpressRoute** e fare clic su **Circuiti ExpressRoute** in **Servizi**.
    [![Circuiti ExpressRoute](media/azure-expressroute-transit-search.png)](media/azure-expressroute-transit-search.png)

3. Selezionare il circuito ExpressRoute che si desidera connettere alla rete CloudSimple.Select the ExpressRoute circuit that you intend to connect to your CloudSimple network.

4. Nella pagina ExpressRoute fare clic su **Autorizzazioni**, immettere un nome per l'autorizzazione e fare clic su **Salva**.
    [![Autorizzazione del circuito ExpressRouteExpressRoute Circuit Authorization](media/azure-expressroute-transit-authorizations.png)](media/azure-expressroute-transit-authorizations.png)

5. Copiare l'ID risorsa e la chiave di autorizzazione facendo clic sull'icona di copia. Incollare l'ID e la chiave in un file di testo.
    [![Copia autorizzazione circuito ExpressRouteExpressRoute Circuit Authorization Copy](media/azure-expressroute-transit-authorization-copy.png)](media/azure-expressroute-transit-authorization-copy.png)

    > [!IMPORTANT]
    > **L'ID risorsa** deve essere copiato dall'interfaccia utente e deve essere nel formato ```/subscriptions/<subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/expressRouteCircuits/<express-route-circuit-name>``` quando viene fornito per il supporto.

6. Presentare un ticket con <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">supporto</a> per la connessione da creare.
    * Tipo di problema: **Tecnico**
    * Sottoscrizione: **sottoscrizione in cui viene distribuito il servizio CloudSimpleSubscription: Subscription where CloudSimple service is deployed**
    * Servizio: **VMware Solution di CloudSimple**
    * Tipo di problema: **Richiesta di servizio**
    * Sottotipo di problema: creare la connessione ExpressRoute a quella localeProblem subtype: **Create ExpressRoute connection to on-premises**
    * Specificare l'ID risorsa e la chiave di autorizzazione copiati e salvati nel riquadro dei dettagli.
    * Fornire uno spazio di indirizzi di rete /29 per la rete di transito.
    * Si invia la route predefinita tramite ExpressRoute?
    * Il traffico del cloud privato deve usare la route predefinita inviata tramite ExpressRoute?

    > [!IMPORTANT]
    > L'invio del percorso predefinito consente di inviare tutto il traffico Internet dal cloud privato utilizzando la connessione Internet locale.  Per disabilitare la route predefinita configurata nel cloud privato e usare la route predefinita di connessione locale, fornire i dettagli nel ticket di supporto.

## <a name="next-steps"></a>Passaggi successivi

* [Altre informazioni sulle connessioni di rete di AzureLearn more about Azure network connections](cloudsimple-azure-network-connection.md)  
