---
title: Configurare Ambiente del servizio app di Azure per il tunneling forzato
description: "Abilitare Ambiente del servizio app per i casi in cui il traffico in uscita è sottoposto a tunneling forzato"
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 384cf393-5c63-4ffb-9eb2-bfd990bc7af1
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 11/10/2017
ms.author: ccompy
ms.custom: mvc
ms.openlocfilehash: 4caaf0df3f1dd4b2cb9b76283a6beed897531c1c
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2017
---
# <a name="configure-your-app-service-environment-with-forced-tunneling"></a>Configurare Ambiente del servizio app con il tunneling forzato

Ambiente del servizio app di Azure è una distribuzione di Servizio app di Azure in un'istanza del cliente di Rete virtuale di Azure. Molti clienti configurano le proprie reti virtuali come estensioni delle reti locali con VPN o connessioni ExpressRoute. A causa dei criteri aziendali o di altri vincoli di sicurezza, configurano le route in modo che inviino tutto il traffico in uscita alla rete locale prima che possa passare su Internet. La modifica del routing della rete virtuale in modo che il traffico in uscita dalla rete virtuali passi alla rete locale attraverso la VPN o la connessione ExpressRoute viene chiamata tunneling forzato. 

Il tunneling forzato può causare problemi per Ambiente del servizio app. Ambiente del servizio app ha un certo numero di dipendenze esterne, che sono elencate nel documento [Architettura di rete di Ambiente del servizio app][network]. Per impostazione predefinita, Ambiente del servizio app richiede che tutte le comunicazioni in uscita passino attraverso l'indirizzo VIP fornito con Ambiente del servizio app.

Le route sono un elemento critico del tunneling forzato e del modo in cui viene gestito. In una rete virtuale di Azure il routing viene eseguito in base all'algoritmo LPM (Longest Prefix Match). Se è presente più di una route con la stessa corrispondenza LPM, viene selezionata la route in base all'origine nell'ordine seguente:

* Route definita dall'utente
* Route BGP (quando viene utilizzato ExpressRoute)
* Route di sistema

Per altre informazioni sul routing in una rete virtuale, vedere [Route definite dall'utente e inoltro degli indirizzi IP][routes]. 

Se si vuole che Ambiente del servizio app operi in una rete virtuale con tunneling forzato, è possibile scegliere tra due opzioni:

* Concedere ad Ambiente del servizio app l'accesso a Internet diretto.
* Modificare l'endpoint in uscita per Ambiente del servizio app.

## <a name="enable-your-app-service-environment-to-have-direct-internet-access"></a>Concedere ad Ambiente del servizio app l'accesso a Internet diretto

Perché Ambiente del servizio app possa funzionare quando la rete virtuale è configurata con una connessione ExpressRoute, è possibile eseguire le operazioni seguenti:

* Configurare ExpressRoute perché annunci 0.0.0.0/0. Per impostazione predefinita, tutto il traffico locale in uscita viene forzato.
* Creare una route definita dall'utente. Applicarla alla subnet che contiene Ambiente del servizio app, con un prefisso di indirizzo 0.0.0.0/0 e un tipo di hop successivo Internet.

Se si apportano queste due modifiche, il traffico destinato a Internet proveniente dalla subnet di Ambiente del servizio app non verrà forzato verso ExpressRoute e Ambiente del servizio app potrà funzionare.

> [!IMPORTANT]
> Le route definite in una route definita dall'utente devono essere sufficientemente specifiche per avere la precedenza su qualsiasi route annunciata dalla configurazione di ExpressRoute. Nell'esempio precedente viene usato l'intervallo di indirizzi ampio 0.0.0.0/0. Può essere accidentalmente sostituito dagli annunci di route che usano intervalli di indirizzi più specifici.
>
> Le istanze di Ambiente del servizio app non sono supportate con configurazioni ExpressRoute con annuncio incrociato di route dal percorso di peering pubblico al percorso di peering privato. Le configurazioni di ExpressRoute con peering pubblico configurato riceveranno gli annunci di route da Microsoft. Gli annunci contengono un ampio set di intervalli di indirizzi IP di Microsoft Azure. In caso di annuncio incrociato degli intervalli di indirizzi nel percorso di peering privato, tutti i pacchetti di rete in uscita dalla subnet di Ambiente del servizio app verranno sottoposti a tunneling forzato verso un'infrastruttura di rete locale del cliente. Questo flusso di rete non è attualmente supportato per impostazione predefinita con Ambiente del servizio app. Una soluzione a questo problema consiste nell'interrompere l'annuncio incrociato di route dal percorso di peering pubblico al percorso di peering privato. Un'altra soluzione consiste nel consentire ad Ambiente del servizio app di operare in una configurazione con tunneling forzato.

## <a name="change-the-egress-endpoint-for-your-app-service-environment"></a>Modificare l'endpoint in uscita per Ambiente del servizio app ##

Questa sezione descrive come abilitare Ambiente del servizio app per una configurazione con tunneling forzato modificando l'endpoint in uscita usato da Ambiente del servizio app. Se il traffico in uscita da Ambiente del servizio app è sottoposto a tunneling forzato verso una rete locale, è necessario consentire a questo traffico di avere origine da indirizzi IP diversi dall'indirizzo VIP di Ambiente del servizio app.

Ambiente del servizio app non solo ha dipendenze esterne, ma deve anche essere in ascolto del traffico in ingresso per rispondere a tale traffico. Le risposte non possono essere inviate di nuovo da un altro indirizzo perché questo determinerebbe l'interruzione del traffico TCP. Per modificare l'endpoint in uscita per Ambiente del servizio app, sono necessari tre passaggi:

1. Impostare una tabella di route per avere la certezza che il traffico di gestione in ingresso possa essere trasmesso di nuovo dallo stesso indirizzo IP.

2. Aggiungere gli indirizzi IP da usare per l'uscita verso il firewall di Ambiente del servizio app.

3. Impostare le route per il traffico in uscita da Ambiente del servizio app da sottoporre a tunneling.

   ![Flusso di rete con tunneling forzato][1]

È possibile configurare Ambiente del servizio app con indirizzi in uscita diversi dopo che Ambiente del servizio app è attivo e operativo oppure è possibile impostare questi indirizzi durante la distribuzione di Ambiente del servizio app.

### <a name="change-the-egress-address-after-the-app-service-environment-is-operational"></a>Modificare l'indirizzo in uscita dopo che Ambiente del servizio app è operativo ###
1. Ottenere gli indirizzi IP che si vuole usare come IP in uscita per Ambiente del servizio app. Se si esegue il tunneling forzato, questi indirizzi provengono da NAT o indirizzi IP gateway. Se si vuole instradare il traffico in uscita di Ambiente del servizio app attraverso un'appliance virtuale di rete, l'indirizzo in uscita è l'indirizzo IP pubblico dell'appliance virtuale di rete.

2. Impostare gli indirizzi in uscita nelle informazioni di configurazione di Ambiente del servizio di app. Passare a resource.azure.com e quindi a Subscription/<subscription id>/resourceGroups/<ase resource group>/providers/Microsoft.Web/hostingEnvironments/<ase name>. Sarà possibile visualizzare il codice JSON che descrive Ambiente del servizio app. Assicurarsi che sia specificato **Lettura/Scrittura** nella parte superiore. Selezionare **Modifica**. Scorrere verso il basso e modificare il valore di **userWhitelistedIpRanges** da **null** a un altro valore simile al seguente. Usare gli indirizzi che si desidera impostare come intervallo degli indirizzi in uscita. 

        "userWhitelistedIpRanges": ["11.22.33.44/32", "55.66.77.0/24"] 

   Selezionare **PUT** nella parte superiore. Questa opzione attiva un'operazione di ridimensionamento in Ambiente del servizio app e modifica il firewall.
 
3. Creare o modificare una tabella di route e inserire le regole per consentire l'accesso agli/dagli indirizzi di gestione mappati alla posizione di Ambiente del servizio app. Per trovare gli indirizzi di gestione, vedere [Indirizzi di gestione di Ambiente del servizio app][management].

4. Modificare le route applicate alla subnet di Ambiente del servizio app con una tabella di route o con route BGP. 

Se Ambiente del servizio app non risponde dal portale, le modifiche hanno causato un problema. Il problema potrebbe essere dovuto a un elenco di indirizzi in uscita non completo, alla perdita del traffico o al blocco del traffico. 

### <a name="create-a-new-app-service-environment-with-a-different-egress-address"></a>Creare una nuova istanza di Ambiente del servizio app con un indirizzo in uscita diverso ###

Se la rete virtuale è già configurata per eseguire il tunneling forzato di tutto il traffico, sono necessari alcuni passaggi aggiuntivi per creare l'istanza di Ambiente del servizio app in modo corretto. È necessario abilitare l'uso di un altro endpoint in uscita durante la creazione di Ambiente del servizio app. A questo scopo, è necessario creare l'istanza di Ambiente del servizio app con un modello che specifichi gli indirizzi in uscita consentiti.

1. Ottenere gli indirizzi IP da usare come indirizzi in uscita per Ambiente del servizio app.

2. Pre-creare la subnet che verrà usata da Ambiente del servizio app. Questa operazione è necessaria per poter impostare le route e anche perché è richiesta dal modello.

3. Creare una tabella di route con gli indirizzi IP di gestione mappati alla posizione di Ambiente del servizio app. Assegnarla ad Ambiente del servizio app.

4. Seguire le indicazioni fornite in [Creare un ambiente del servizio app con un modello][template]. Selezionare il modello appropriato.

5. Modificare la sezione "resources" del file azuredeploy.json. Includere una riga per **userWhitelistedIpRanges** con i valori personalizzati, in questo modo:

       "userWhitelistedIpRanges":  ["11.22.33.44/32", "55.66.77.0/30"]

Se questa sezione è configurata correttamente, Ambiente del servizio app verrà avviato senza problemi. 


<!--IMAGES-->
[1]: ./media/forced-tunnel-support/forced-tunnel-flow.png

<!--Links-->
[management]: ./management-addresses.md
[network]: ./network-info.md
[routes]: ../../virtual-network/virtual-networks-udr-overview.md
[template]: ./create-from-template.md
