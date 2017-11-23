---
title: Configurare l'ambiente del servizio app di Azure per il tunneling forzato
description: Abilitare l'ambiente del servizio app per funzionare con traffico in uscita forzato
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 384cf393-5c63-4ffb-9eb2-bfd990bc7af1
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/10/2017
ms.author: ccompy
ms.openlocfilehash: cd89bd23074dec1de6fa0e8784d42a5c539938b1
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/11/2017
---
# <a name="configure-your-app-service-environment-with-forced-tunneling"></a>Configurare l'ambiente del servizio app con il tunneling forzato tramite un tunnel

L'ambiente del servizio app (ASE) è una distribuzione del servizio app di Azure in una rete virtuale (VNet) di Azure di un cliente. Molti clienti configurano le loro VNet come estensioni delle loro reti locali con VPN o connessioni ExpressRoute. A causa dei criteri aziendali o di altri vincoli di sicurezza, configurano le route in modo che inviino tutto il traffico in uscita alla rete locale prima che possa passare su Internet. La modifica del routing della VNet in modo che il traffico in uscita dalla VNet passi alla rete locale attraverso la VPN o la connessione ExpressRoute viene chiamata tunneling forzato.  

Il tunneling forzato può causare problemi a un ambiente del servizio app. L'ambiente del servizio app ha un certo numero di dipendenze esterne, che sono enumerate nel documento [ASE Network Architecture (Architettura di rete dell'ambiente del servizio di app)][network]. Per impostazione predefinita, l'ambiente del servizio di app richiede che tutte le comunicazioni in uscita passino attraverso il VIP fornito con l'ambiente del servizio di app.

Le route sono un elemento critico del tunneling forzato e ne determinano la modalità d'uso. In una rete virtuale di Azure, il routing viene effettuato in base all'algoritmo Longest Prefix Match (LPM).  Se è presente più di una route con la stessa corrispondenza LPM, allora la route viene selezionata in base alla sua origine nell'ordine seguente:

1. Route definita utente
1. Route BGP (quando viene utilizzato ExpressRoute)
1. La route di sistema

Per altre informazioni sul routing in una VNet, leggere [User defined routes and IP forwarding (Route definite dall'utente e inoltro degli indirizzi IP)][routes]. 

Se si desidera usare l'ambiente del servizio di app con il tunneling forzato, si hanno due opzioni:

1. Abilitare un accesso a Internet diretto per l'ambiente del servizio di app
1. Modificare l'endpoint in uscita dell'ambiente del servizio di app

## <a name="enable-your-ase-to-have-direct-internet-access"></a>Abilitare un accesso a Internet diretto per l'ambiente del servizio di app

Per fare funzionare l'ambiente del servizio di app mentre la VNet è configurata con una connessione ExpressRoute, è possibile:

* Configurare ExpressRoute perché annunci 0.0.0.0/0. Per impostazione predefinita, tutto il traffico locale in uscita viene forzato.
* Creare una route definita dall'utente. Applicarla alla subnet che contiene l'ambiente del servizio app con un prefisso indirizzo 0.0.0.0/0 e un tipo di hop successivo di Internet.

Se si apportano queste due modifiche, il traffico destinato a Internet, proveniente dalla subnet dell'ambiente del servizio app, non verrà forzato verso ExpressRoute e l'ambiente del servizio app potrà funzionare.

> [!IMPORTANT]
> Le route definite in una route definita dall'utente devono essere sufficientemente specifiche per avere la precedenza su qualsiasi route annunciata dalla configurazione di ExpressRoute. Nell'esempio precedente viene usato l'intervallo di indirizzi ampio 0.0.0.0/0. Può essere accidentalmente sostituito dagli annunci di route che usano intervalli di indirizzi più specifici.
>
> Gli ambienti del servizio app non sono supportati con le configurazioni di ExpressRoute con annuncio incrociato di route dal percorso di peering pubblico al percorso di peering privato. Le configurazioni di ExpressRoute con peering pubblico configurato riceveranno gli annunci di route da Microsoft. Gli annunci contengono un ampio set di intervalli di indirizzi IP di Microsoft Azure. In caso di annuncio incrociato di questi intervalli di indirizzi nel percorso di peering privato, tutti i pacchetti di rete in uscita dalla subnet dell'ambiente del servizio app verranno sottoposti a tunneling forzato verso un'infrastruttura di rete locale del cliente. Per impostazione predefinita, questo flusso di rete non è attualmente supportato dagli ambienti del servizio di app. Una soluzione a questo problema consiste nell'interrompere l'annuncio incrociato di route dal percorso di peering pubblico al percorso di peering privato.  L'altra soluzione consiste nell'abilitare l'ambiente del servizio di rete perché possa lavorare in una configurazione con tunneling forzato.

## <a name="change-the-egress-endpoint-for-your-ase"></a>Modificare l'endpoint in uscita dell'ambiente del servizio di app ##

Questa sezione spiega come abilitare l'ambiente del servizio di app perché possa funzionare in una configurazione con tunneling forzato modificando l'endpoint in uscita usato dall'ambiente del servizio di app. Se il traffico in uscita dall'ambiente del servizio di app è forzato mediante un tunnel verso una rete locale, è necessario permettere a tale traffico di avere origine da indirizzi IP diversi dall'indirizzo VIP dell'ambiente del servizio di app.

Un ambiente del servizio di app non ha solo dipendenze esterne, ma deve rimanere in ascolto del traffico di rete in entrata per gestire l'ambiente del servizio di app. L'ambiente del servizio di app deve essere in grado di rispondere a questo traffico e le risposte non possono essere inviate da un indirizzo diverso perché ciò interromperebbe il TCP.  Pertanto, è necessario effettuare tre operazioni per modificare l'endpoint in uscita per l'ambiente del servizio di app.

1. Impostare una tabella di route per essere certi che il traffico di gestione in entrata possa essere ritrasmesso dallo stesso indirizzo IP
1. Aggiungere gli indirizzi IP usati per l'uscita al firewall dell'ambiente del servizio di app
1. Impostare le route per il traffico in uscita dall'ambiente del servizio di app da forzare mediante il tunnel

![Flusso di rete con tunneling forzato][1]

È possibile configurare l'ambiente del servizio di app con indirizzi in uscita diversi dopo che l'ambiente del servizio di app è attivo e funzionante, oppure impostare tali indirizzi durante la distribuzione dell'ambiente del servizio di app.  

### <a name="changing-the-egress-address-after-the-ase-is-operational"></a>Modificare l'indirizzo in uscita dopo che l'ambiente del servizio di app è già operativo ###
1. Ottenere gli indirizzi IP che si desidera usare come IP in uscita per l'ambiente del servizio di app. Per il tunneling forzato, gli indirizzi IP sono i NAT o gli IP dei gateway.  Se si desidera eseguire il routing del traffico in uscita dell'ambiente del servizio di app, l'indirizzo in uscita corrisponde generalmente all'indirizzo IP pubblico di NVA.
2. Impostare gli indirizzi in uscita nelle informazioni di configurazione dell'ambiente del servizio di app. Passare a resource.azure.com e ricercare: Subscription/<subscription id>/resourceGroups/<ase resource group>/providers/Microsoft.Web/hostingEnvironments/<ase name> fino a individuare il file json che descrive l'ambiente del servizio di app in uso.  Verificare che nella sezione superiore ci sia l'indicazione lettura/scrittura.  Fare clic su Modifica   Scorrere la schermata fino in fondo e cambiare userWhitelistedIpRanges da  

       "userWhitelistedIpRanges": null 
      
  in qualcosa simile al seguente esempio. Usare gli indirizzi che si desidera impostare come intervallo degli indirizzi in uscita. 

      "userWhitelistedIpRanges": ["11.22.33.44/32", "55.66.77.0/24"] 

  Fare clic su PUT in alto. Ciò attiva un'operazione di ridimensionamento nell'ambiente del servizio di app e regola il firewall.
   
3. Creare o modificare una tabella di route e inserire le regole per consentire l'accesso agli/dagli indirizzi di gestione che sono mappati alla posizione dell'ambiente del servizio di app.  Gli indirizzi di gestione sono riportati in [App Service Environment management addresses (Indirizzi di gestione dell'ambiente del servizio app)][management] 

4. Regolare le route applicate alla subnet dell'ambiente del servizio di app con una tabella di route o route BGP.  

Se l'ambiente del servizio di app non risponde dal portale, c'è un problema con le modifiche.  È possibile che l'elenco degli indirizzi in uscita sia incompleto, che il traffico si sia interrotto o che sia stato bloccato.  

### <a name="create-a-new-ase-with-a-different-egress-address"></a>Creare un nuovo ambiente del servizio di app con un indirizzo in uscita diverso  ###

Se la VNet è già configurata per eseguire il tunneling forzato di tutto il traffico, è necessario effettuare alcune operazioni aggiuntive per creare un ambiente del servizio di app che funzioni correttamente. Ciò comporta abilitare l'uso di un altro endpoint in uscita durante la creazione dell'ambiente del servizio di app.  Per effettuare questa operazione, è necessario creare l'ambiente del servizio di app usando un modello che specifichi gli indirizzi in uscita permessi.

1. Ottenere gli indirizzi IP da usare come indirizzi in uscita per l'ambiente del servizio di app.
1. Pre-creare la subnet che sarà usata dall'ambiente del servizio di app. Questa operazione è necessaria per poter impostare le route e anche perché è richiesta dal modello.  
1. Creare una tabella di route con gli indirizzi IP di gestione che sono mappati alla posizione dell'ambiente del servizio di app e assegnarla all'ambiente
1. Seguire le istruzioni riportate in [Creating an ASE with a template][template] (Creare un ambiente del servizio di app con un modello) e scaricare il modello appropriato
1. Modificare la sezione "resources" del file azuredeploy.json. Includere una riga per **userWhitelistedIpRanges** con valori personalizzati come:

       "userWhitelistedIpRanges":  ["11.22.33.44/32", "55.66.77.0/30"]

Se la configurazione è stata eseguita correttamente, l'ambiente del servizio di app dovrebbe avviarsi senza problemi.  


<!--IMAGES-->
[1]: ./media/forced-tunnel-support/forced-tunnel-flow.png

<!--Links-->
[management]: ./management-addresses.md
[network]: ./network-info.md
[routes]: ../../virtual-network/virtual-networks-udr-overview.md
[template]: ./create-from-template.md
