---
title: Considerazioni sulla rete per un ambiente del servizio app - Azure
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
ms.date: 08/29/2018
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: d9a0ab84e133863092f68cc949c2b7933bc5da31
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/12/2018
ms.locfileid: "53271012"
---
# <a name="networking-considerations-for-an-app-service-environment"></a>Considerazioni sulla rete per un ambiente del servizio app #

## <a name="overview"></a>Panoramica ##

 L'[ambiente del servizio app di Azure][Intro] è una distribuzione di Servizio app di Azure in una subnet nella rete virtuale di Azure (VNet). È possibile distribuire un ambiente del servizio app in due modi:

- **Ambiente del servizio app di Azure esterno**: espone le app ospitate dall'ambiente del servizio app su un indirizzo IP accessibile da Internet. Per altre informazioni, vedere [Create an External ASE][MakeExternalASE] (Creare un ambiente del servizio app esterno).
- **Ambiente del servizio app di Azure con bilanciamento del carico interno**: espone le app ospitate dall'ambiente del servizio app su un indirizzo IP all'interno della VNet. L'endpoint interno è un servizio di bilanciamento del carico interno (ILB, Internal Load Balancer) ed è per questo motivo che si usa la definizione "ambiente del servizio app con bilanciamento del carico interno". Per altre informazioni, vedere [Create and use an ILB ASE][MakeILBASE] (Creare e usare un ambiente del servizio app ILB).

Esistono due versioni dell'ambiente del servizio app: ASEv1 e ASEv2. Per informazioni sulla versione ASEv1, vedere [Introduzione all'ambiente del servizio app][ASEv1Intro]. Un ambiente ASEv1 può essere distribuito in una rete virtuale classica o di Resource Manager. Un ambiente ASEv2 può essere distribuito solo in una rete virtuale di Resource Manager.

Tutte le chiamate da un ambiente del servizio app indirizzate a Internet lasciano la rete virtuale tramite un indirizzo VIP assegnato per l'ambiente del servizio app. L'indirizzo IP pubblico di questo indirizzo VIP è l'indirizzo IP di origine per tutte le chiamate dall'ambiente del servizio app indirizzate a Internet. Se le app nell'ambiente del servizio app effettuano chiamate a risorse nella rete virtuale o tramite una VPN, l'IP di origine sarà uno degli indirizzi IP nella subnet usata dall'ambiente del servizio app. Dato che l'ambiente del servizio app è all'interno della rete virtuale, consente anche di accedere alle risorse all'interno della rete virtuale senza ulteriori configurazioni. Se la rete virtuale è connessa alla rete locale, le app nell'ambiente del servizio app hanno accesso anche alle relative risorse senza un'ulteriore configurazione.

![Ambiente del servizio app esterno][1] 

Se si dispone di un ambiente del servizio app esterno, il VIP pubblico è anche l'endpoint in cui si risolvono le app dell'ambiente del servizio app per:

* HTTP/S. 
* FTP/S. 
* Distribuzione Web.
* Debug remoto.

![Ambiente del servizio app con bilanciamento del carico interno][2]

Se è disponibile un ambiente del servizio app con bilanciamento del carico interno, l'indirizzo del servizio di bilanciamento del carico interno è l'endpoint per HTTP/S, FTP/S, la distribuzione Web e il debug remoto.

Le porte di accesso alle app normali sono:

| Uso | Da | A |
|----------|---------|-------------|
|  HTTP/HTTPS  | Configurabile dall'utente |  80, 443 |
|  FTP/FTPS    | Configurabile dall'utente |  21, 990, 10001-10020 |
|  Debug remoto in Visual Studio  |  Configurabile dall'utente |  4020, 4022, 4024 |

Ciò vale se si usa un ambiente del servizio app esterno o con bilanciamento del carico. Se si è su un ambiente del servizio app esterno, tali porte possono essere raggiunte sul VIP pubblico. Se si è su un ambiente del servizio app con bilanciamento del carico interno, tali porte possono essere raggiunte sul servizio di bilanciamento del carico interno. Il blocco della porta 443 ha un possibile impatto su alcune funzionalità esposte nel portale. Per altre informazioni, vedere [Dipendenze per il portale](#portaldep).

## <a name="ase-subnet-size"></a>Dimensioni della subnet dell'ambiente del servizio app ##

Le dimensioni della subnet usata per ospitare un ambiente del servizio app non possono essere modificate dopo la distribuzione dell'ambiente.  L'ambiente del servizio app usa un indirizzo per ogni ruolo di infrastruttura e per ogni istanza del piano di servizio app Isolato.  Inoltre, la rete di Azure usa 5 indirizzi per ogni subnet creata.  Un ambiente del servizio app senza alcun piano di servizio app userà 12 indirizzi prima che venga creata un'app.  Un ambiente del servizio app ILB userà invece 13 indirizzi prima che venga creata un'app in tale ambiente. Quando si scala orizzontalmente l'ambiente del servizio app, i ruoli di infrastruttura vengono aggiunti ogni multiplo di 15 e 20 istanze di piani di servizio app.

   > [!NOTE]
   > La subnet non può contenere altro oltre all'ambiente del servizio app. Assicurarsi di scegliere uno spazio di indirizzi che consente la crescita futura. Non è possibile modificare questa impostazione in un secondo momento. È consigliabile una dimensione pari a `/24` con 256 indirizzi.

Quando si aumenta o si riduce il numero di istanze, vengono aggiunti nuovi ruoli di dimensioni appropriate e viene quindi eseguita la migrazione dei carichi di lavoro dalle dimensioni correnti a quelle di destinazione. Le macchine virtuali originali vengono rimosse solo dopo la migrazione delle app. Ciò significa che se si ha un ambiente del servizio app con 100 istanze ASP, ci sarà un periodo in cui sarà necessario raddoppiare il numero di macchine virtuali.  È per questo motivo che è consigliabile usare '/24' per supportare le eventuali modifiche che potrebbero essere necessarie.  

## <a name="ase-dependencies"></a>Dipendenze dell'ambiente del servizio app ##

### <a name="ase-inbound-dependencies"></a>Dipendenze in ingresso dell'ambiente del servizio app ###

Le dipendenze per l'accesso in ingresso dell'ambiente del servizio app sono:

| Uso | Da | A |
|-----|------|----|
| Gestione | Indirizzi di gestione del servizio app | Subnet dell'ambiente del servizio app: 454, 455 |
|  Comunicazione interna dell'ambiente del servizio app | Subnet dell'ambiente del servizio app: Tutte le porte | Subnet dell'ambiente del servizio app: Tutte le porte
|  Consenti bilanciamento del carico di Azure in ingresso | Servizio di bilanciamento del carico di Azure | Subnet dell'ambiente del servizio app: Tutte le porte
|  Indirizzi IP assegnati alle app | Indirizzi assegnati alle app | Subnet dell'ambiente del servizio app: Tutte le porte

Il traffico di gestione in ingresso fornisce comandi e controllo dell'ambiente del servizio app oltre al monitoraggio del sistema. Gli indirizzi di origine per il traffico sono indicati nel documento [Indirizzi di gestione dell'Ambiente del servizio app][ASEManagement]. La configurazione della sicurezza di rete deve consentire l'accesso da tutti gli indirizzi IP sulle porte 454 e 455. Se si blocca l'accesso da questi indirizzi, l'ambiente del servizio app risulterà non integro e quindi verrà sospeso.

All'interno della subnet dell'ambiente del servizio app ci sono molte porte usate per la comunicazioni dei componenti interni e che possono cambiare.  Per questo motivo, è necessario che a tutte le porte nella subnet dell'ambiente del servizio app sia possibile accedere dalla subnet dell'ambiente del servizio app. 

Per le comunicazioni tra Azure Load Balancer e la subnet dell'ambiente del servizio app, le porte minime che devono essere aperte sono le porte 454, 455 e 16001. La porta 16001 viene usata per mantenere attivo il traffico tra il servizio di bilanciamento del carico e l'ambiente del servizio app. Se si usa un ambiente del servizio app ILB, è possibile limitare il traffico alle porte 454, 455 e 16001.  Se si usa un ambiente del servizio app esterno, è necessario prendere in considerazione le normali porte di accesso delle app.  Se si usano indirizzi assegnati alle app, è necessario aprire tutte le porte.  Quando un indirizzo è assegnato a un'app specifica, il servizio di bilanciamento del carico usa porte non conosciute in anticipo per inviare il traffico HTTP e HTTPS all'ambiente del servizio app.

Se si usano indirizzi IP assegnati alle app, è necessario consentire il traffico dagli indirizzi IP assegnati alle app alla subnet dell'ambiente del servizio app.

Il traffico TCP in ingresso sulle porte 454 e 455 deve essere ritrasmesso dallo stesso indirizzo VIP oppure si verificherà un problema di routing asimmetrico. 

### <a name="ase-outbound-dependencies"></a>Dipendenze in uscita dell'ambiente del servizio app ###

Per l'accesso in uscita, un ambiente del servizio app dipende da più sistemi esterni. Molte di queste dipendenze di sistema sono definite con nomi DNS e non corrispondono a un set fisso di indirizzi IP. Per questo, l'ambiente del servizio app richiede l'accesso in uscita dalla subnet dell'ambiente del servizio app a tutti gli indirizzi IP esterni su un'ampia gamma di porte. 

L'elenco completo delle dipendenze in uscita è disponibile nel documento che descrive il [blocco del traffico in uscita dell'ambiente del servizio app](./firewall-integration.md). L'ambiente del servizio app smette di funzionare se perde l'accesso alle relative dipendenze. Se questa condizione si prolunga nel tempo, l'ambiente del servizio app viene sospeso. 

### <a name="customer-dns"></a>DNS del cliente ###

Se la rete virtuale è configurata con un server DNS definito dal cliente, i carichi di lavoro tenant lo useranno. L'ambiente del servizio app deve comunque comunicare con DNS di Azure per scopi di gestione. 

Se la rete virtuale è configurata con un DNS del cliente sull'altra estremità di una VPN, il server DNS deve essere raggiungibile dalla subnet che contiene l'ambiente del servizio app.

Per testare la risoluzione dall'app Web, è possibile usare il comando della console *nameresolver*. Passare alla finestra di debug nel sito scm per l'app o passare all'app nel portale e selezionare la console. Dal prompt della shell è possibile eseguire il comando *nameresolver* insieme all'indirizzo da cercare. Il risultato ottenuto è lo stesso di quello ottenuto dall'app dopo l'esecuzione della stessa ricerca. Se si usa nslookup, si eseguirà invece una ricerca con DNS di Azure.

Se si modifica l'impostazione DNS della rete virtuale in cui si trova l'ambiente del servizio app, sarà necessario riavviare l'ambiente del servizio app. Per evitare il riavvio dell'ambiente del servizio app, è consigliabile configurare le impostazioni DNS per la rete virtuale prima di creare l'ambiente del servizio app.  

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
- **Indirizzo IP in uscita pubblico**: usato come indirizzo IP di origine per le connessioni in uscita dall'ambiente del servizio app che lasciano la rete virtuale e non vengono indirizzate su una VPN.
- **Indirizzo IP del servizio ILB**: Se si usa un ambiente del servizio app ILB.
- **Indirizzi SSL basati su IP assegnati alle app**: possibili solo con un ambiente del servizio app esterno e quando è configurato SSL basato su IP.

Tutti questi indirizzi IP sono facilmente visibili in un ambiente ASEv2 nel portale di Azure dall'interfaccia utente dell'ambiente del servizio app. Con un ambiente del servizio app con bilanciamento del carico interno viene elencato l'indirizzo IP per il servizio di bilanciamento del carico interno.

   > [!NOTE]
   > Questi indirizzi IP non verranno modificati finché l'ambiente del servizio app resta operativo e in esecuzione.  Se l'ambiente del servizio app viene sospeso e ripristinato, gli indirizzi usati dall'ambiente del servizio app verranno modificati. Il motivo più comune per la sospensione di un ambiente del servizio app è il blocco dell'accesso della gestione in ingresso o il blocco dell'accesso a una dipendenza dell'ambiente del servizio app. 

![Indirizzi IP][3]

### <a name="app-assigned-ip-addresses"></a>Indirizzi IP assegnati alle app ###

Con un ambiente del servizio app esterno è possibile assegnare gli indirizzi IP alle singole app. Ciò non è possibile con un ambiente del servizio app con bilanciamento del carico interno. Per altre informazioni su come configurare l'app in modo che abbia un indirizzo IP proprio, vedere [Associare un certificato SSL personalizzato esistente ad app Web di Azure](../app-service-web-tutorial-custom-ssl.md).

Quando a un'app viene assegnato un indirizzo SSL basato su IP proprio, l'ambiente del servizio app riserva due porta da mappare a tale indirizzo IP. Una porta è destinata al traffico HTTP e l'altra al traffico HTTPS. Tali porte sono elencate nell'interfaccia utente dell'ambiente del servizio app nella sezione degli indirizzi IP. Il traffico deve essere in grado di raggiungere tali porte dall'indirizzo VIP o le app saranno inaccessibili. Questo requisito è importante da ricordare quando si configurano gruppi di sicurezza di rete (NSG).

## <a name="network-security-groups"></a>Gruppi di sicurezza di rete ##

I [gruppi di sicurezza di rete][NSGs] consentono di controllare l'accesso alla rete all'interno di una rete virtuale. Quando si usa il portale, esiste una regola implicita con la priorità più bassa che nega tutto. Quello che è necessario creare sono le regole di autorizzazione.

In un ambiente del servizio app non si ha accesso alle macchine virtuali usate per ospitare l'ambiente del servizio app stesso. Tali macchine virtuali si trovano in una sottoscrizione gestita da Microsoft. Se si vuole limitare l'accesso alle app nell'ambiente del servizio app, impostare gruppi di sicurezza di rete nella subnet dell'ambiente del servizio app. In questo caso, prestare particolare attenzione alle dipendenze dell'ambiente del servizio app. In caso di blocco delle dipendenze, l'ambiente del servizio app smette di funzionare.

I gruppi di sicurezza di rete possono essere configurati tramite il portale di Azure o PowerShell. Le informazioni riportate di seguito si riferiscono al portale di Azure. I gruppi di sicurezza di rete vengono creati e gestiti nel portale come risorsa di primo livello in **Rete**.

Tenendo conto dei requisiti in ingresso e in uscita, i gruppi di sicurezza di rete dovrebbero avere un aspetto simile a quelli illustrati in questo esempio. L'intervallo di indirizzi della rete virtuale è _192.168.250.0/23_ e la subnet dell'ambiente del servizio app è _192.168.251.128/25_.

I primi due requisiti in ingresso per il funzionamento dell'ambiente del servizio app sono riportati all'inizio dell'elenco in questo esempio. e consentono la gestione dell'ambiente del servizio app oltre a consentire all'ambiente del servizio app di comunicare con se stesso. Le altre voci sono tutte configurabili a livello di tenant e consentono di regolare l'accesso alle applicazioni ospitate dall'ambiente del servizio app. 

![Regole di sicurezza in ingresso][4]

Una regola predefinita consente agli indirizzi IP di comunicare con la subnet dell'ambiente del servizio app nella rete virtuale. Un'altra regola predefinita consente al bilanciamento del carico, noto anche come VIP pubblico, di comunicare con l'ambiente del servizio app. Per visualizzare le regole predefinite selezionare **Regole predefinite** accanto all'icona **Aggiungi**. Aggiungendo una regola per negare tutto il resto dopo le regole dei gruppi di sicurezza di rete visualizzate, si impedisce il traffico tra l'indirizzo VIP e l'ambiente del servizio app. Per impedire il traffico proveniente da all'interno della rete virtuale, aggiungere una regola personalizzata per consentire connessioni in entrata. Usare un'origine uguale ad AzureLoadBalancer con una destinazione **qualsiasi** e un intervallo di porte di **\***. Dato che la regola del gruppo di sicurezza di rete viene applicata solo alla subnet dell'ambiente del servizio app, non è necessario impostare una destinazione specifica.

Se è stato assegnato un indirizzo IP all'app, accertarsi di mantenere le porte aperte. Per visualizzare le porte selezionare **Ambiente del servizio app** > **Indirizzi IP**.  

Tutti gli elementi visualizzati nelle regole in uscita seguenti sono necessari, tranne per l'ultimo elemento. Queste voci consentono l'accesso alla rete alle dipendenze dell'ambiente del servizio app presentate più indietro in questo articolo. Se si bloccano una o più di queste voci, l'ambiente del servizio app smette di funzionare. L'ultima voce nell'elenco consente all'ambiente del servizio app di comunicare con altre risorse nella rete virtuale.

![Regole di sicurezza in uscita][5]

Dopo aver definito i gruppi di sicurezza di rete, assegnarli alla subnet in cui è incluso l'ambiente del servizio app. Se non si ricorda la rete virtuale o la subnet dell'ambiente del servizio app, è possibile visualizzarla dalla pagina del portale dell'ambiente del servizio app. Per assegnare il gruppo di sicurezza di rete alla subnet, passare all'interfaccia utente della subnet e selezionare il gruppo di sicurezza di rete.

## <a name="routes"></a>Route ##

Si definisce tunneling forzato l'impostazione delle route nella rete virtuale in modo che il traffico in uscita non passi direttamente a Internet ma altrove, ad esempio in un gateway ExpressRoute o in un dispositivo virtuale.  Se è necessario configurare l'ambiente del servizio app in questo modo, leggere il documento in [Configurare l'ambiente del servizio app con il tunneling forzato][forcedtunnel].  Questo documento indica le opzioni disponibili per l'uso di ExpressRoute e del tunneling forzato.

Quando si crea un ambiente del servizio app nel portale, viene creato anche un set di tabelle di route nella subnet creata con l'ambiente del servizio app.  Tali route indicano semplicemente di inviare il traffico in uscita direttamente a Internet.  
Per creare le stesse route manualmente, seguire questa procedura:

1. Accedere al portale di Azure. Selezionare **Rete** > **Tabelle route**.

2. Creare una nuova tabella route nella stessa area della rete virtuale.

3. Dall'interfaccia utente per le tabelle route selezionare **Route** > **Aggiungi**.

4. Impostare **Tipo hop successivo** su **Internet** e **Prefisso indirizzo** su **0.0.0.0/0**. Selezionare **Salva**.

    Verrà visualizzata una schermata simile alla seguente:

    ![Route funzionali][6]

5. Dopo aver creato la nuova tabella di route, passare alla subnet contenente l'ambiente del servizio app. Selezionare la tabella di route nell'elenco del portale. Dopo aver salvato la modifica, dovrebbero essere visibili i gruppi di sicurezza di rete e le route associati alla subnet.

    ![Gruppi di sicurezza di rete e route][7]

## <a name="service-endpoints"></a>Endpoint servizio ##

Gli endpoint servizio consentono di limitare l'accesso ai servizi multi-tenant a un set di reti e subnet virtuali di Azure. Per altre informazioni sugli endpoint servizio, vedere la pagina [Endpoint del servizio Rete virtuale][serviceendpoints] della documentazione. 

Quando si abilitano gli endpoint del servizio su una risorsa, alcune route sono create con una priorità maggiore rispetto a tutte le altre route. Se si usano gli endpoint servizio con un ambiente del servizio app con tunneling forzato, il tunneling del traffico di gestione di SQL di Azure e Archiviazione di Azure non viene forzato. 

Quando gli endpoint servizio sono abilitati in una subnet con un'istanza di SQL di Azure, tutte le istanze di SQL di Azure verso cui si esegue la connessione da tale subnet devono avere gli endpoint servizio abilitati. Se si desidera accedere a più istanze di SQL di Azure dalla stessa subnet, non è possibile abilitare gli endpoint servizio in un'istanza di SQL di Azure e non in un'altra. Il comportamento di Archiviazione di Azure è diverso da quello di SQL di Azure. Quando si abilitano gli endpoint servizio con Archiviazione di Azure, si blocca l'accesso a tale risorsa dalla propria subnet ma è comunque possibile accedere ad altri account di archiviazione di Azure, anche se non hanno gli endpoint servizio abilitati.  

![Endpoint servizio][8]

<!--Image references-->
[1]: ./media/network_considerations_with_an_app_service_environment/networkase-overflow.png
[2]: ./media/network_considerations_with_an_app_service_environment/networkase-overflow2.png
[3]: ./media/network_considerations_with_an_app_service_environment/networkase-ipaddresses.png
[4]: ./media/network_considerations_with_an_app_service_environment/networkase-inboundnsg.png
[5]: ./media/network_considerations_with_an_app_service_environment/networkase-outboundnsg.png
[6]: ./media/network_considerations_with_an_app_service_environment/networkase-udr.png
[7]: ./media/network_considerations_with_an_app_service_environment/networkase-subnet.png
[8]: ./media/network_considerations_with_an_app_service_environment/serviceendpoint.png

<!--Links-->
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/security-overview.md
[ConfigureASEv1]: app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: app-service-app-service-environment-intro.md
[mobileapps]: ../../app-service-mobile/app-service-mobile-value-prop.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md
[ConfigureSSL]: ../web-sites-purchase-ssl-web-site.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
[ASEManagement]: ./management-addresses.md
[serviceendpoints]: ../../virtual-network/virtual-network-service-endpoints-overview.md
[forcedtunnel]: ./forced-tunnel-support.md
[serviceendpoints]: ../../virtual-network/virtual-network-service-endpoints-overview.md
