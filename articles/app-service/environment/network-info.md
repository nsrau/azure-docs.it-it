---
title: Considerazioni sulla rete per un ambiente del servizio app di Azure
description: Viene spiegato il traffico di rete dell'ambiente del servizio app e come impostare gruppi di sicurezza di rete (NSG) e route definite dall'utente (UDR) con l'ambiente del servizio app
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 955a4d84-94ca-418d-aa79-b57a5eb8cb85
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2017
ms.author: ccompy
ms.translationtype: HT
ms.sourcegitcommit: 8f9234fe1f33625685b66e1d0e0024469f54f95c
ms.openlocfilehash: 121dd1a90e9bde66f1c3b752412a657a67295084
ms.contentlocale: it-it
ms.lasthandoff: 09/20/2017

---
# <a name="networking-considerations-for-an-app-service-environment"></a>Considerazioni sulla rete per un ambiente del servizio app #

## <a name="overview"></a>Panoramica ##

 L'[ambiente del servizio app di Azure][Intro] è una distribuzione di Servizio app di Azure in una subnet nella rete virtuale di Azure (VNet). È possibile distribuire un ambiente del servizio app in due modi:

- **Ambiente del servizio app esterno**: espone le app ospitate dall'ambiente del servizio app su un indirizzo IP accessibile da Internet. Per altre informazioni, vedere [Create an External ASE][MakeExternalASE] (Creare un ambiente del servizio app esterno).
- **Ambiente del servizio app con bilanciamento del carico interno**: espone le app ospitate dall'ambiente del servizio app su un indirizzo IP all'interno della VNet. L'endpoint interno è un servizio di bilanciamento del carico interno (ILB, Internal Load Balancer) ed è per questo motivo che si usa la definizione "ambiente del servizio app con bilanciamento del carico interno". Per altre informazioni, vedere [Create and use an ILB ASE][MakeILBASE] (Creare e usare un ambiente del servizio app ILB).

Esistono ora due versioni dell'ambiente del servizio app: ASEv1 e ASEv2. Per informazioni sulla versione ASEv1, vedere [Introduzione all'ambiente del servizio app][ASEv1Intro]. Un ambiente ASEv1 può essere distribuito in una rete virtuale classica o di Resource Manager. Un ambiente ASEv2 può essere distribuito solo in una rete virtuale di Resource Manager.

Tutte le chiamate da un ambiente del servizio app indirizzate a Internet lasciano la rete virtuale tramite un indirizzo VIP assegnato per l'ambiente del servizio app. L'indirizzo IP pubblico di questo indirizzo VIP è quindi l'indirizzo IP di origine per tutte le chiamate dall'ambiente del servizio app indirizzate a Internet. Se le app nell'ambiente del servizio app effettuano chiamate a risorse nella rete virtuale o tramite una VPN, l'IP di origine sarà uno degli indirizzi IP nella subnet usata dall'ambiente del servizio app. Dato che l'ambiente del servizio app è all'interno della rete virtuale, consente anche di accedere alle risorse all'interno della rete virtuale senza ulteriori configurazioni. Se la rete virtuale è connessa alla rete locale, le app nell'ambiente del servizio app hanno accesso anche alle relative risorse. Non è necessario configurare ulteriormente l'ambiente del servizio app o l'app.

![Ambiente del servizio app esterno][1] 

Se si dispone di un ambiente del servizio app esterno, il VIP pubblico è anche l'endpoint in cui si risolvono le app dell'ambiente del servizio app per:

* HTTP/S. 
* FTP/S. 
* Distribuzione Web.
* Debug remoto.

![Ambiente del servizio app con bilanciamento del carico interno][2]

Se è disponibile un ambiente del servizio app con bilanciamento del carico interno, l'indirizzo IP del servizio di bilanciamento del carico interno è l'endpoint per HTTP/S, FTP/S, la distribuzione Web e il debug remoto.

Le porte di accesso alle app normali sono:

| Uso | Da | A |
|----------|---------|-------------|
|  HTTP/HTTPS  | Configurabile dall'utente |  80, 443 |
|  FTP/FTPS    | Configurabile dall'utente |  21, 990, 10001-10020 |
|  Debug remoto in Visual Studio  |  Configurabile dall'utente |  4016, 4018, 4020, 4022 |

Ciò vale se si usa un ambiente del servizio app esterno o con bilanciamento del carico. Se si è su un ambiente del servizio app esterno, tali porte possono essere raggiunte sul VIP pubblico. Se si è su un ambiente del servizio app con bilanciamento del carico interno, tali porte possono essere raggiunte sul servizio di bilanciamento del carico interno. Il blocco della porta 443 ha un possibile impatto su alcune funzionalità esposte nel portale. Per altre informazioni, vedere [Dipendenze per il portale](#portaldep).

## <a name="ase-dependencies"></a>Dipendenze dell'ambiente del servizio app ##

Una dipendenza per l'accesso in ingresso dell'ambiente del servizio app è:

| Uso | Da | A |
|-----|------|----|
| gestione | Indirizzi di gestione del servizio app | Subnet dell'ambiente del servizio app: 454, 455 |
|  Comunicazione interna dell'ambiente del servizio app | Subnet dell'ambiente del servizio app: tutte le porte | Subnet dell'ambiente del servizio app: tutte le porte
|  Consenti bilanciamento del carico di Azure in ingresso | Servizio di bilanciamento del carico di Azure | Subnet dell'ambiente del servizio app: tutte le porte
|  Indirizzi IP assegnati alle app | Indirizzi assegnati alle app | Subnet dell'ambiente del servizio app: tutte le porte

Il traffico in ingresso fornisce comandi e controllo dell'ambiente del servizio app oltre al monitoraggio del sistema. Gli IP di origine per il traffico sono indicati nel documento [Indirizzi di gestione dell'Ambiente del servizio app][ASEManagement]. La configurazione della sicurezza di rete deve consentire l'accesso da tutti gli indirizzi IP sulle porte 454 e 455.

All'interno della subnet dell'ambiente del servizio app ci sono molte porte usate per la comunicazioni dei componenti interni e che possono cambiare.  Per questo motivo, è necessario che a tutte le porte nella subnet dell'ambiente del servizio app sia possibile accedere dalla subnet dell'ambiente del servizio app. 

Per le comunicazioni tra Azure Load Balancer e la subnet dell'ambiente del servizio app, le porte minime che devono essere aperte sono le porte 454, 455 e 16001. La porta 16001 viene usata per mantenere attivo il traffico tra il servizio di bilanciamento del carico e l'ambiente del servizio app. Se si usa un ambiente del servizio app ILB, è possibile limitare il traffico alle porte 454, 455 e 16001.  Se si usa un ambiente del servizio app esterno, è necessario prendere in considerazione le normali porte di accesso delle app.  Se si usano indirizzi assegnati alle app, è necessario aprire tutte le porte.  Quando un indirizzo è assegnato a un'app specifica, il servizio di bilanciamento del carico usa porte non conosciute in anticipo per inviare il traffico HTTP e HTTPS all'ambiente del servizio app.

Se si usano indirizzi IP assegnati alle app, è necessario consentire il traffico dagli indirizzi IP assegnati alle app alla subnet dell'ambiente del servizio app.

Per l'accesso in uscita, un ambiente del servizio app dipende da più sistemi esterni. Tali dipendenze di sistema sono definite con nomi DNS e non corrispondono a un set fisso di indirizzi IP. Per questo, l'ambiente del servizio app richiede l'accesso in uscita dalla subnet dell'ambiente del servizio app a tutti gli indirizzi IP esterni su un'ampia gamma di porte. Per un ambiente del servizio app esistono le seguenti dipendenze in uscita:

| Uso | Da | A |
|-----|------|----|
| Archiviazione di Azure | Subnet dell'ambiente del servizio app | table.core.windows.net, blob.core.windows.net, queue.core.windows.net, file.core.windows.net: 80, 443, 445 (445 necessaria solo per ASEv1.) |
| Database SQL di Azure | Subnet dell'ambiente del servizio app | database.windows.net: 1433, 11000-11999, 14000-14999 (per altre informazioni, vedere [Porte successive alla 1433 per ADO.NET 4.5](../../sql-database/sql-database-develop-direct-route-ports-adonet-v12.md))|
| Gestione di Azure | Subnet dell'ambiente del servizio app | management.core.windows.net, management.azure.com: 443 
| Verifica dei certificati SSL |  Subnet dell'ambiente del servizio app            |  ocsp.msocsp.com, mscrl.microsoft.com, crl.microsoft.com: 443
| Azure Active Directory        | Subnet dell'ambiente del servizio app            |  Internet: 443
| Gestione del servizio app        | Subnet dell'ambiente del servizio app            |  Internet: 443
| DNS di Azure                     | Subnet dell'ambiente del servizio app            |  Internet: 53
| Comunicazione interna dell'ambiente del servizio app    | Subnet dell'ambiente del servizio app: tutte le porte |  Subnet dell'ambiente del servizio app: tutte le porte

L'ambiente del servizio app smette di funzionare se perde l'accesso a queste dipendenze. Se questa condizione si prolunga nel tempo, l'ambiente del servizio app viene sospeso.

### <a name="customer-dns"></a>DNS del cliente ###

Se la rete virtuale è configurata con un server DNS definito dal cliente, i carichi di lavoro tenant lo useranno. L'ambiente del servizio app deve comunque comunicare con DNS di Azure per scopi di gestione. 

Se la rete virtuale è configurata con un DNS del cliente sull'altra estremità di una VPN, il server DNS deve essere raggiungibile dalla subnet che contiene l'ambiente del servizio app.

<a name="portaldep"></a>

## <a name="portal-dependencies"></a>Dipendenze per il portale ##

Oltre alle dipendenze funzionali per un ambiente del servizio app, esistono alcune altre dipendenze correlate all'esperienza del portale. Alcune delle funzionalità nel portale di Azure dipendono dall'accesso diretto al _sito di Gestione controllo servizi_. Per ogni app nel Servizio app di Azure esistono due URL. Il primo URL consente l'accesso all'app. Il secondo URL consente l'accesso al sito di Gestione controllo servizi, chiamato anche _console di Kudu_. Le funzionalità che usano il sito di Gestione controllo servizi includono:

-   Processi Web
-   Funzioni
-   Streaming dei log
-   Kudu
-   Estensioni
-   Esplora processi
-   Console

Quando si usa un ambiente del servizio app con bilanciamento del carico interno, il sito di Gestione controllo servizi non è accessibile dall'esterno della rete virtuale. Quando l'app è ospitata in un ambiente del servizio app con bilanciamento del carico interno, non sarà possibile usare alcune funzionalità dal portale.  

Molte delle funzionalità che dipendono dal sito di Gestione controllo servizi sono anche disponibili direttamente nella console di Kudu. È possibile connettersi direttamente anziché tramite il portale. Se l'app è ospitata in un ambiente del servizio app con bilanciamento del carico interno, usare le credenziali di pubblicazione per accedere. L'URL per accedere al sito di Gestione controllo servizi di un'app ospitata in un ambiente del servizio app ILB ha il formato seguente: 

```
<appname>.scm.<domain name the ILB ASE was created with> 
```

Se l'ambiente del servizio app con bilanciamento del carico interno è il nome di dominio *contoso.net* e il nome dell'app è *testapp*, l'app viene raggiunta in *testapp.contoso.net*. Il sito di Gestione controllo servizi abbinato viene raggiunto in *testapp.scm.contoso.net*.

## <a name="functions-and-web-jobs"></a>Funzioni e processi Web ##

Le funzioni e i processi Web dipendono dal sito di Gestione controllo servizi, ma il relativo uso nel portale è supportato, anche se le app sono presenti in un ambiente del servizio app con bilanciamento del carico interno, purché il browser possa raggiungere il sito di Gestione controllo servizi.  Se si usa un certificato autofirmato con l'ambiente del servizio app con bilanciamento del carico interno, è necessario impostare il browser in modo da considerare attendibile il certificato.  A tale scopo, per IE ed Edge, è necessario che il certificato si trovi nell'archivio Attendibilità del computer.  Se si usa Chrome, ciò implica che il certificato sia stato accettato nel browser in precedenza, presumibilmente accedendo direttamente al sito di Gestione controllo servizi.  La soluzione migliore consiste nell'usare un certificato commerciale incluso nella catena di certificati del browser.  

## <a name="ase-ip-addresses"></a>Indirizzi IP dell'ambiente del servizio app ##

Un ambiente del servizio app ha alcuni indirizzi IP di cui tenere conto. Sono:

- **Indirizzo IP in ingresso pubblico**: usato per il traffico di app in un ambiente del servizio app esterno e per il traffico di gestione sia per un ambiente del servizio app esterno che con bilanciamento del carico interno.
- **IP pubblico in uscita**: usato come indirizzo IP di origine per le connessioni in uscita dall'ambiente del servizio app che lasciano la rete virtuale e non vengono indirizzate su una VPN.
- **Indirizzo IP con bilanciamento del carico interno**: se si usa un ambiente del servizio app con bilanciamento del carico interno.
- **Indirizzi SSL basati su IP assegnati alle app**: possibili solo con un ambiente del servizio app esterno e quando è configurato SSL basato su IP.

Tutti questi indirizzi IP sono facilmente visibili in un ambiente ASEv2 nel portale di Azure dall'interfaccia utente dell'ambiente del servizio app. Con un ambiente del servizio app con bilanciamento del carico interno viene elencato l'indirizzo IP per il servizio di bilanciamento del carico interno.

![Indirizzi IP][3]

### <a name="app-assigned-ip-addresses"></a>Indirizzi IP assegnati alle app ###

Con un ambiente del servizio app esterno è possibile assegnare gli indirizzi IP alle singole app. Ciò non è possibile con un ambiente del servizio app con bilanciamento del carico interno. Per altre informazioni su come configurare l'app in modo che abbia un indirizzo IP proprio, vedere [Associare un certificato SSL personalizzato esistente ad app Web di Azure](../app-service-web-tutorial-custom-ssl.md).

Quando a un'app viene assegnato un indirizzo SSL basato su IP proprio, l'ambiente del servizio app riserva due porta da mappare a tale indirizzo IP. Una porta è destinata al traffico HTTP e l'altra al traffico HTTPS. Tali porte sono elencate nell'interfaccia utente dell'ambiente del servizio app nella sezione degli indirizzi IP. Il traffico deve essere in grado di raggiungere tali porte dall'indirizzo VIP o le app saranno inaccessibili. Questo requisito è importante da ricordare quando si configurano gruppi di sicurezza di rete (NSG).

## <a name="network-security-groups"></a>Gruppi di sicurezza di rete ##

I [gruppi di sicurezza di rete][NSGs] consentono di controllare l'accesso alla rete all'interno di una rete virtuale. Quando si usa il portale, esiste una regola implicita con la priorità più bassa che nega tutto. Quello che è necessario creare sono le regole di autorizzazione.

In un ambiente del servizio app non si ha accesso alle macchine virtuali usate per ospitare l'ambiente del servizio app stesso. Tali macchine virtuali si trovano in una sottoscrizione gestita da Microsoft. Se si vuole limitare l'accesso alle app nell'ambiente del servizio app, impostare gruppi di sicurezza di rete nella subnet dell'ambiente del servizio app. In questo caso, prestare particolare attenzione alle dipendenze dell'ambiente del servizio app. In caso di blocco delle dipendenze, l'ambiente del servizio app smette di funzionare.

I gruppi di sicurezza di rete possono essere configurati tramite il portale di Azure o PowerShell. Le informazioni riportate di seguito si riferiscono al portale di Azure. I gruppi di sicurezza di rete vengono creati e gestiti nel portale come risorsa di primo livello in **Rete**.

Tenendo conto dei requisiti in ingresso e in uscita, i gruppi di sicurezza di rete dovrebbero avere un aspetto simile a quelli illustrati in questo esempio. L'intervallo di indirizzi della rete virtuale è _192.168.250.0/16_ e la subnet dell'ambiente del servizio app è inclusa in _192.168.251.128/25_.

I primi due requisiti in ingresso per il funzionamento dell'ambiente del servizio app sono riportati all'inizio dell'elenco in questo esempio. e consentono la gestione dell'ambiente del servizio app oltre a consentire all'ambiente del servizio app di comunicare con se stesso. Le altre voci sono tutte configurabili a livello di tenant e consentono di regolare l'accesso alle applicazioni ospitate dall'ambiente del servizio app. 

![Regole di sicurezza in ingresso][4]

Una regola predefinita consente agli indirizzi IP di comunicare con la subnet dell'ambiente del servizio app nella rete virtuale. Un'altra regola predefinita consente al bilanciamento del carico, noto anche come VIP pubblico, di comunicare con l'ambiente del servizio app. Per visualizzare le regole predefinite selezionare **Regole predefinite** accanto all'icona **Aggiungi**. Aggiungendo una regola per negare tutto il resto dopo le regole dei gruppi di sicurezza di rete visualizzate, si impedisce il traffico tra l'indirizzo VIP e l'ambiente del servizio app. Per impedire il traffico proveniente da all'interno della rete virtuale, aggiungere una regola personalizzata per consentire connessioni in entrata. Usare un'origine uguale ad AzureLoadBalancer con una destinazione **qualsiasi** e un intervallo di porte di **\***. Dato che la regola del gruppo di sicurezza di rete viene applicata solo alla subnet dell'ambiente del servizio app, non è necessario impostare una destinazione specifica.

Se è stato assegnato un indirizzo IP all'app, accertarsi di mantenere le porte aperte. Per visualizzare le porte selezionare **Ambiente del servizio app** > **Indirizzi IP**.  

Tutti gli elementi visualizzati nelle regole in uscita seguenti sono necessari, tranne per l'ultimo elemento. Queste voci consentono l'accesso alla rete alle dipendenze dell'ambiente del servizio app presentate più indietro in questo articolo. Se si bloccano una o più di queste voci, l'ambiente del servizio app smette di funzionare. L'ultima voce nell'elenco consente all'ambiente del servizio app di comunicare con altre risorse nella rete virtuale.

![Regole di sicurezza in uscita][5]

Dopo aver definito i gruppi di sicurezza di rete, assegnarli alla subnet in cui è incluso l'ambiente del servizio app. Se non si ricorda la rete virtuale o la subnet dell'ambiente del servizio app, è possibile visualizzarla dal portale di gestione dell'ambiente del servizio app. Per assegnare il gruppo di sicurezza di rete alla subnet, passare all'interfaccia utente della subnet e selezionare il gruppo di sicurezza di rete.

## <a name="routes"></a>Route ##

Le route diventano problematiche più di frequente quando si configura la rete virtuale con Azure ExpressRoute. Esistono tre tipi di route in una rete virtuale:

-   Route di sistema
-   Route BGP
-   Route definite dall'utente

Le route BGP sono prioritarie rispetto alle route di sistema. Le route definite dall'utente sono prioritarie rispetto alle route BGP. Per altre informazioni sulle route nelle reti virtuali di Azure, vedere [Panoramica delle route definite dall'utente][UDRs].

Il database SQL di Azure che l'ambiente del servizio app usa per gestire il sistema è dotato di un firewall. Richiede che le comunicazioni provengano dall'indirizzo VIP pubblico dell'ambiente del servizio app. Le connessioni al database SQL dall'ambiente del servizio app verranno negate se vengono inviate alla connessione ExpressRoute e a un altro indirizzo IP.

Se le risposte alle richieste di gestione in ingresso vengono inviate a ExpressRoute, l'indirizzo di risposta è diverso da quello di destinazione originale. Questa mancata corrispondenza interrompe la comunicazione TCP.

Per il corretto funzionamento dell'ambiente del servizio app quando la rete virtuale è configurata con ExpressRoute, la cosa più semplice da fare è:

-   Configurare ExpressRoute in modo che annunci _0.0.0.0/0_. Per impostazione predefinita, tutto il traffico locale in uscita viene forzato.
-   Creare una route definita dall'utente. Applicarla alla subnet contenente l'ambiente del servizio app, con un prefisso dell'indirizzo _0.0.0.0/0_ e tipo di hop successivo _Internet_.

Se si apportano queste due modifiche, il traffico destinato a Internet, proveniente dalla subnet dell'ambiente del servizio app, non verrà forzato verso ExpressRoute e l'ambiente del servizio app potrà funzionare. 

> [!IMPORTANT]
> Le route definite in una route definita dall'utente devono essere sufficientemente specifiche per avere la precedenza su qualsiasi route annunciata dalla configurazione di ExpressRoute. Nell'esempio precedente viene usato l'intervallo di indirizzi ampio 0.0.0.0/0. Può essere accidentalmente sostituito dagli annunci di route che usano intervalli di indirizzi più specifici.
>
> Gli ambienti del servizio app non sono supportati con le configurazioni di ExpressRoute con annuncio incrociato di route dal percorso di peering pubblico al percorso di peering privato. Le configurazioni di ExpressRoute con peering pubblico configurato riceveranno gli annunci di route da Microsoft. Gli annunci contengono un ampio set di intervalli di indirizzi IP di Microsoft Azure. In caso di annuncio incrociato di questi intervalli di indirizzi nel percorso di peering privato, tutti i pacchetti di rete in uscita dalla subnet dell'ambiente del servizio app verranno sottoposti a tunneling forzato verso un'infrastruttura di rete locale del cliente. Questo flusso di rete non è attualmente supportato con ambienti del servizio app. Una soluzione a questo problema consiste nell'interrompere l'annuncio incrociato di route dal percorso di peering pubblico al percorso di peering privato.

Per creare una route definita dall'utente, seguire questi passaggi:

1. Accedere al portale di Azure. Selezionare **Rete** > **Tabelle route**.

2. Creare una nuova tabella route nella stessa area della rete virtuale.

3. Dall'interfaccia utente per le tabelle route selezionare **Route** > **Aggiungi**.

4. Impostare **Tipo hop successivo** su **Internet** e **Prefisso indirizzo** su **0.0.0.0/0**. Selezionare **Salva**.

    Verrà visualizzata una schermata simile alla seguente:

    ![Route funzionali][6]

5. Dopo aver creato la nuova tabella di route, passare alla subnet contenente l'ambiente del servizio app. Selezionare la tabella di route nell'elenco del portale. Dopo aver salvato la modifica, dovrebbero essere visibili i gruppi di sicurezza di rete e le route associati alla subnet.

    ![Gruppi di sicurezza di rete e route][7]

### <a name="deploy-into-existing-azure-virtual-networks-that-are-integrated-with-expressroute"></a>Distribuzione nelle reti virtuali di Azure esistenti integrate con ExpressRoute ###

Per distribuire l'ambiente del servizio app in una rete virtuale integrata con ExpressRoute, preconfigurare la subnet in cui si desidera distribuire l'ambiente del servizio app. Quindi, usare un modello di Resource Manager per distribuirla. Per creare un ambiente del servizio app in una rete virtuale per cui è già configurato ExpressRoute:

- Creare una subnet per ospitare l'ambiente del servizio app.

    > [!NOTE]
    > La subnet non può contenere altro oltre all'ambiente del servizio app. Assicurarsi di scegliere uno spazio di indirizzi che consente la crescita futura. Non è possibile modificare questa impostazione in un secondo momento. È consigliabile una dimensione pari a `/25` con 128 indirizzi.

- Creare route definite dall'utente (per esempio tabelle di route) come descritto in precedenza e impostarle sulla subnet.
- Creare l'ambiente del servizio app usando un modello di Resource Manager come descritto in [Come creare un ambiente del servizio app usando modelli di Azure Resource Manager][MakeASEfromTemplate].

<!--Image references-->
[1]: ./media/network_considerations_with_an_app_service_environment/networkase-overflow.png
[2]: ./media/network_considerations_with_an_app_service_environment/networkase-overflow2.png
[3]: ./media/network_considerations_with_an_app_service_environment/networkase-ipaddresses.png
[4]: ./media/network_considerations_with_an_app_service_environment/networkase-inboundnsg.png
[5]: ./media/network_considerations_with_an_app_service_environment/networkase-outboundnsg.png
[6]: ./media/network_considerations_with_an_app_service_environment/networkase-udr.png
[7]: ./media/network_considerations_with_an_app_service_environment/networkase-subnet.png

<!--Links-->
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/virtual-networks-nsg.md
[ConfigureASEv1]: app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: app-service-app-service-environment-intro.md
[mobileapps]: ../../app-service-mobile/app-service-mobile-value-prop.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: http://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md
[ConfigureSSL]: ../web-sites-purchase-ssl-web-site.md
[Kudu]: http://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
[ASEManagement]: ./management-addresses.md

