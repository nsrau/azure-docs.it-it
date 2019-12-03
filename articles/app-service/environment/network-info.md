---
title: Considerazioni sulla rete
description: Informazioni sul traffico di rete dell'ambiente del servizio app e su come impostare gruppi di sicurezza di rete e route definite dall'utente con l'ambiente del servizio app.
author: ccompy
ms.assetid: 955a4d84-94ca-418d-aa79-b57a5eb8cb85
ms.topic: article
ms.date: 05/31/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: e7d181416123c96e2462180a82c6d0b9670ef5fc
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2019
ms.locfileid: "74687135"
---
# <a name="networking-considerations-for-an-app-service-environment"></a>Considerazioni sulla rete per un ambiente del servizio app #

## <a name="overview"></a>Panoramica ##

 Azure [ambiente del servizio app][Intro] è una distribuzione di app Azure servizio in una subnet nella rete virtuale di Azure (VNet). È possibile distribuire un ambiente del servizio app in due modi:

- **Ambiente del servizio app esterno**: espone le app ospitate dall'ambiente del servizio app su un indirizzo IP accessibile da Internet. Per altre informazioni, vedere [creare un][MakeExternalASE]ambiente del servizio app esterno.
- **Ambiente del servizio app con bilanciamento del carico interno**: espone le app ospitate dall'ambiente del servizio app su un indirizzo IP all'interno della VNet. L'endpoint interno è un servizio di bilanciamento del carico interno (ILB, Internal Load Balancer) ed è per questo motivo che si usa la definizione "ambiente del servizio app con bilanciamento del carico interno". Per altre informazioni, vedere [creare e usare un][MakeILBASE]ambiente del servizio app ILB.

Tutti gli ambienti, external e ILB, dispongono di un indirizzo VIP pubblico usato per il traffico di gestione in ingresso e come indirizzo da quando si effettuano chiamate dall'ambiente del servizio app a Internet. Le chiamate da un ambiente del servizio app che passano a Internet lasciano il VNet tramite l'indirizzo VIP assegnato per l'ambiente del servizio app. L'indirizzo IP pubblico di questo indirizzo VIP è l'indirizzo IP di origine per tutte le chiamate dall'ambiente del servizio app indirizzate a Internet. Se le app nell'ambiente del servizio app effettuano chiamate a risorse nella rete virtuale o tramite una VPN, l'IP di origine sarà uno degli indirizzi IP nella subnet usata dall'ambiente del servizio app. Dato che l'ambiente del servizio app è all'interno della rete virtuale, consente anche di accedere alle risorse all'interno della rete virtuale senza ulteriori configurazioni. Se la rete virtuale è connessa alla rete locale, le app nell'ambiente del servizio app hanno accesso anche alle relative risorse senza un'ulteriore configurazione.

![Ambiente del servizio app esterno][1] 

Se si dispone di un ambiente del servizio app esterno, il VIP pubblico è anche l'endpoint in cui si risolvono le app dell'ambiente del servizio app per:

* HTTP/S 
* FTP/S
* Distribuzione Web
* Debug remoto

![Ambiente del servizio app con bilanciamento del carico interno][2]

Se si dispone di un ambiente del servizio app ILB, l'indirizzo dell'indirizzo ILB è l'endpoint per HTTP/S, FTP/S, la distribuzione Web e il debug remoto.

## <a name="ase-subnet-size"></a>Dimensioni della subnet dell'ambiente del servizio app ##

Le dimensioni della subnet usata per ospitare un ambiente del servizio app non possono essere modificate dopo la distribuzione dell'ambiente.  L'ambiente del servizio app usa un indirizzo per ogni ruolo di infrastruttura e per ogni istanza del piano di servizio app Isolato.  Sono inoltre disponibili cinque indirizzi usati dalla rete di Azure per ogni subnet creata.  Un ambiente del servizio app senza alcun piano di servizio app userà 12 indirizzi prima che venga creata un'app.  Se si tratta di un ambiente del servizio app ILB, utilizzerà 13 indirizzi prima di creare un'app nell'ambiente del servizio app. Quando si scala orizzontalmente l'ambiente del servizio app, i ruoli di infrastruttura vengono aggiunti ogni multiplo di 15 e 20 istanze di piani di servizio app.

   > [!NOTE]
   > La subnet non può contenere altro oltre all'ambiente del servizio app. Assicurarsi di scegliere uno spazio di indirizzi che consente la crescita futura. Non è possibile modificare questa impostazione in un secondo momento. È consigliabile una dimensione pari a `/24` con 256 indirizzi.

Quando si aumenta o si riduce il numero di istanze, vengono aggiunti nuovi ruoli di dimensioni appropriate e viene quindi eseguita la migrazione dei carichi di lavoro dalle dimensioni correnti a quelle di destinazione. Le macchine virtuali originali sono state rimosse solo dopo la migrazione dei carichi di lavoro. Se si dispone di un ambiente del servizio app con 100 istanze ASP, si verificherà un periodo in cui è necessario raddoppiare il numero di macchine virtuali.  È per questo motivo che è consigliabile usare '/24' per supportare le eventuali modifiche che potrebbero essere necessarie.  

## <a name="ase-dependencies"></a>Dipendenze dell'ambiente del servizio app ##

### <a name="ase-inbound-dependencies"></a>Dipendenze in ingresso dell'ambiente del servizio app ###

Per il funzionamento dell'ambiente del servizio app, l'ambiente del servizio app richiede che siano aperte le porte seguenti:

| Uso | Da | Per |
|-----|------|----|
| Gestione | Indirizzi di gestione del servizio app | Subnet dell'ambiente del servizio app: 454, 455 |
|  Comunicazione interna dell'ambiente del servizio app | Subnet dell'ambiente del servizio app: tutte le porte | Subnet dell'ambiente del servizio app: tutte le porte
|  Consenti bilanciamento del carico di Azure in ingresso | Servizio di bilanciamento del carico di Azure | Subnet ASE: 16001

Sono disponibili altre due porte che possono essere visualizzate come aperte in un'analisi di porta, 7654 e 1221. Rispondono con un indirizzo IP e nient'altro. Se lo si desidera, è possibile bloccarle. 

Il traffico di gestione in ingresso fornisce comandi e controllo dell'ambiente del servizio app oltre al monitoraggio del sistema. Gli indirizzi di origine per questo traffico sono elencati nel documento degli [indirizzi di gestione][ASEManagement] dell'ambiente del servizio app. La configurazione della sicurezza di rete deve consentire l'accesso dagli indirizzi di gestione dell'ambiente del servizio app sulle porte 454 e 455. Se si blocca l'accesso da questi indirizzi, l'ambiente del servizio app risulterà non integro e quindi verrà sospeso. Il traffico TCP in ingresso sulle porte 454 e 455 deve essere ritrasmesso dallo stesso indirizzo VIP oppure si verificherà un problema di routing asimmetrico. 

All'interno della subnet dell'ambiente del servizio app sono presenti molte porte usate per la comunicazione interna dei componenti e possono essere modificate. Per questo motivo, è necessario che a tutte le porte nella subnet dell'ambiente del servizio app sia possibile accedere dalla subnet dell'ambiente del servizio app. 

Per le comunicazioni tra Azure Load Balancer e la subnet dell'ambiente del servizio app, le porte minime che devono essere aperte sono le porte 454, 455 e 16001. La porta 16001 viene usata per mantenere attivo il traffico tra il servizio di bilanciamento del carico e l'ambiente del servizio app. Se si usa un ambiente del servizio app ILB, è possibile bloccare il traffico solo con le porte 454, 455 e 16001.  Se si usa un ambiente del servizio app esterno, è necessario prendere in considerazione le normali porte di accesso alle app.  

Le altre porte di cui è necessario preoccuparsi sono le porte dell'applicazione:

| Uso | Porte |
|----------|-------------|
|  HTTP/HTTPS  | 80, 443 |
|  FTP/FTPS    | 21, 990, 10001-10020 |
|  Debug remoto in Visual Studio  |  4020, 4022, 4024 |
|  Servizio Distribuzione Web | 8172 |

Se si bloccano le porte dell'applicazione, l'ambiente del servizio app può comunque funzionare, ma potrebbe non essere possibile.  Se si usano indirizzi IP assegnati alle app con un ambiente del servizio app esterno, è necessario consentire il traffico dagli indirizzi IP assegnati alle app alla subnet dell'ambiente del servizio app sulle porte visualizzate nel portale dell'ambiente del servizio app > pagina indirizzi IP.

### <a name="ase-outbound-dependencies"></a>Dipendenze in uscita dell'ambiente del servizio app ###

Per l'accesso in uscita, un ambiente del servizio app dipende da più sistemi esterni. Molte di queste dipendenze di sistema sono definite con nomi DNS e non corrispondono a un set fisso di indirizzi IP. Per questo, l'ambiente del servizio app richiede l'accesso in uscita dalla subnet dell'ambiente del servizio app a tutti gli indirizzi IP esterni su un'ampia gamma di porte. 

L'ambiente del servizio app comunica con indirizzi Internet accessibili sulle porte seguenti:

| Utilizzi | Porte |
|-----|------|
| DNS | 53 |
| NTP | 123 |
| 8CRL, aggiornamenti di Windows, dipendenze di Linux, servizi di Azure | 80/443 |
| Azure SQL | 1433 | 
| Monitorare | 12000 |

Le dipendenze in uscita sono elencate nel documento in cui viene descritto il [blocco del traffico in uscita ambiente del servizio app](./firewall-integration.md). L'ambiente del servizio app smette di funzionare se perde l'accesso alle relative dipendenze. Se questa condizione si prolunga nel tempo, l'ambiente del servizio app viene sospeso. 

### <a name="customer-dns"></a>DNS del cliente ###

Se la rete virtuale è configurata con un server DNS definito dal cliente, i carichi di lavoro tenant lo useranno. L'ambiente del servizio app usa DNS di Azure per scopi di gestione. Se il VNet è configurato con un server DNS selezionato dal cliente, il server DNS deve essere raggiungibile dalla subnet che contiene l'ambiente del servizio app.

Per testare la risoluzione DNS dall'app Web, è possibile usare il comando console *nameresolver*. Passare alla finestra di debug nel sito scm per l'app o passare all'app nel portale e selezionare la console. Dal prompt della shell è possibile eseguire il comando *nameresolver* insieme al nome DNS che si vuole cercare. Il risultato ottenuto è lo stesso di quello ottenuto dall'app dopo l'esecuzione della stessa ricerca. Se si usa nslookup, viene invece eseguita una ricerca usando DNS di Azure.

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

Quando si usa un ambiente del servizio app ILB, il sito SCM non è accessibile dall'esterno della VNet. Alcune funzionalità non funzioneranno dal portale per le app perché richiedono l'accesso al sito SCM di un'app. È possibile connettersi direttamente al sito SCM anziché usare il portale. 

Se l'ambiente del servizio app ILB è il nome di dominio *contoso.appserviceenvironment.NET* e il nome dell'app è *TestApp*, l'app viene raggiunta in *TestApp.contoso.appserviceenvironment.NET*. Il sito SCM a cui viene associato viene raggiunto a *TestApp.SCM.contoso.appserviceenvironment.NET*.

## <a name="ase-ip-addresses"></a>Indirizzi IP dell'ambiente del servizio app ##

Un ambiente del servizio app ha alcuni indirizzi IP di cui tenere conto. Sono:

- **Indirizzo IP in ingresso pubblico**: usato per il traffico di app in un ambiente del servizio app esterno e per il traffico di gestione sia per un ambiente del servizio app esterno che con bilanciamento del carico interno.
- **IP pubblico in uscita**: usato come indirizzo IP di origine per le connessioni in uscita dall'ambiente del servizio app che lasciano la rete virtuale e non vengono indirizzate su una VPN.
- **Indirizzo IP ILB**: l'indirizzo IP ILB esiste solo in un ambiente del servizio app ILB.
- **Indirizzi SSL basati su IP assegnati alle app**: possibili solo con un ambiente del servizio app esterno e quando è configurato SSL basato su IP.

Tutti questi indirizzi IP sono visibili nella portale di Azure dall'interfaccia utente dell'ambiente del servizio app. Con un ambiente del servizio app con bilanciamento del carico interno viene elencato l'indirizzo IP per il servizio di bilanciamento del carico interno.

   > [!NOTE]
   > Questi indirizzi IP non verranno modificati finché l'ambiente del servizio app resta operativo e in esecuzione.  Se l'ambiente del servizio app viene sospeso e ripristinato, gli indirizzi usati dall'ambiente del servizio app verranno modificati. Il motivo più comune per la sospensione di un ambiente del servizio app è il blocco dell'accesso della gestione in ingresso o il blocco dell'accesso a una dipendenza dell'ambiente del servizio app. 

![Indirizzi IP][3]

### <a name="app-assigned-ip-addresses"></a>Indirizzi IP assegnati alle app ###

Con un ambiente del servizio app esterno è possibile assegnare gli indirizzi IP alle singole app. Ciò non è possibile con un ambiente del servizio app con bilanciamento del carico interno. Per altre informazioni su come configurare l'app in modo che disponga di un proprio indirizzo IP, vedere [proteggere un nome DNS personalizzato con un'associazione SSL nel servizio app Azure](../configure-ssl-bindings.md).

Quando a un'app viene assegnato un indirizzo SSL basato su IP proprio, l'ambiente del servizio app riserva due porta da mappare a tale indirizzo IP. Una porta è destinata al traffico HTTP e l'altra al traffico HTTPS. Tali porte sono elencate nell'interfaccia utente dell'ambiente del servizio app nella sezione degli indirizzi IP. Il traffico deve essere in grado di raggiungere tali porte dall'indirizzo VIP o le app saranno inaccessibili. Questo requisito è importante da ricordare quando si configurano gruppi di sicurezza di rete (NSG).

## <a name="network-security-groups"></a>Gruppi di sicurezza di rete ##

I [gruppi di sicurezza di rete][NSGs] offrono la possibilità di controllare l'accesso alla rete all'interno di un VNet. Quando si usa il portale, esiste una regola implicita con la priorità più bassa che nega tutto. Quello che è necessario creare sono le regole di autorizzazione.

In un ambiente del servizio app non si ha accesso alle macchine virtuali usate per ospitare l'ambiente del servizio app stesso. Tali macchine virtuali si trovano in una sottoscrizione gestita da Microsoft. Se si vuole limitare l'accesso alle app nell'ambiente del servizio app, impostare gruppi di sicurezza di rete nella subnet dell'ambiente del servizio app. In questo caso, prestare particolare attenzione alle dipendenze dell'ambiente del servizio app. In caso di blocco delle dipendenze, l'ambiente del servizio app smette di funzionare.

I gruppi di sicurezza di rete possono essere configurati tramite il portale di Azure o PowerShell. Le informazioni riportate di seguito si riferiscono al portale di Azure. I gruppi di sicurezza di rete vengono creati e gestiti nel portale come risorsa di primo livello in **Rete**.

Per il funzionamento di un ambiente del servizio app, è necessario che le voci richieste in un NSG consentano il traffico:

**In ingresso**
* dal tag del servizio IP AppServiceManagement sulle porte 454.455
* dal servizio di bilanciamento del carico sulla porta 16001
* dalla subnet dell'ambiente del servizio app alla subnet dell'ambiente del servizio app in tutte le porte

**Outbound**
* a tutti gli indirizzi IP sulla porta 123
* a tutti gli IP sulle porte 80, 443
* al tag del servizio IP AzureSQL sulle porte 1433
* a tutti gli indirizzi IP sulla porta 12000
* alla subnet dell'ambiente del servizio app in tutte le porte

Non è necessario aggiungere la porta DNS perché il traffico verso DNS non è influenzato dalle regole NSG. Queste porte non includono le porte richieste dalle app per un uso corretto. Le porte di accesso alle app normali sono:

| Uso | Porte |
|----------|-------------|
|  HTTP/HTTPS  | 80, 443 |
|  FTP/FTPS    | 21, 990, 10001-10020 |
|  Debug remoto in Visual Studio  |  4020, 4022, 4024 |
|  Servizio Distribuzione Web | 8172 |

Tenendo conto dei requisiti in ingresso e in uscita, i gruppi di sicurezza di rete dovrebbero avere un aspetto simile a quelli illustrati in questo esempio. 

![Regole di sicurezza in ingresso][4]

Una regola predefinita consente agli indirizzi IP di comunicare con la subnet dell'ambiente del servizio app nella rete virtuale. Un'altra regola predefinita consente al bilanciamento del carico, noto anche come VIP pubblico, di comunicare con l'ambiente del servizio app. Per visualizzare le regole predefinite selezionare **Regole predefinite** accanto all'icona **Aggiungi**. Se si inserisce una regola nega tutto il resto prima delle regole predefinite, si impedisce il traffico tra l'indirizzo VIP e l'ambiente del servizio app. Per impedire il traffico proveniente da all'interno della rete virtuale, aggiungere una regola personalizzata per consentire connessioni in entrata. Usare un'origine uguale ad AzureLoadBalancer con una destinazione **qualsiasi** e un intervallo di porte di **\*\** . Dato che la regola del gruppo di sicurezza di rete viene applicata solo alla subnet dell'ambiente del servizio app, non è necessario impostare una destinazione specifica.

Se è stato assegnato un indirizzo IP all'app, accertarsi di mantenere le porte aperte. Per visualizzare le porte selezionare **Ambiente del servizio app** > **Indirizzi IP**.  

Tutti gli elementi visualizzati nelle regole in uscita seguenti sono necessari, tranne per l'ultimo elemento. Queste voci consentono l'accesso alla rete alle dipendenze dell'ambiente del servizio app presentate più indietro in questo articolo. Se si bloccano una o più di queste voci, l'ambiente del servizio app smette di funzionare. L'ultima voce nell'elenco consente all'ambiente del servizio app di comunicare con altre risorse nella rete virtuale.

![Regole di sicurezza in uscita][5]

Dopo aver definito i gruppi di sicurezza di rete, assegnarli alla subnet in cui è incluso l'ambiente del servizio app. Se non si ricorda la rete virtuale o la subnet dell'ambiente del servizio app, è possibile visualizzarla dalla pagina del portale dell'ambiente del servizio app. Per assegnare il gruppo di sicurezza di rete alla subnet, passare all'interfaccia utente della subnet e selezionare il gruppo di sicurezza di rete.

## <a name="routes"></a>Route ##

Si definisce tunneling forzato l'impostazione delle route nella rete virtuale in modo che il traffico in uscita non passi direttamente a Internet ma altrove, ad esempio in un gateway ExpressRoute o in un dispositivo virtuale.  Se è necessario configurare l'ambiente del servizio app in questo modo, leggere il documento relativo alla [configurazione del ambiente del servizio app con il tunneling forzato][forcedtunnel].  Questo documento indica le opzioni disponibili per l'uso di ExpressRoute e del tunneling forzato.

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

## <a name="service-endpoints"></a>Endpoint del servizio ##

Gli endpoint servizio consentono di limitare l'accesso ai servizi multi-tenant a un set di reti e subnet virtuali di Azure. Per altre informazioni sugli endpoint servizio, vedere la pagina [Endpoint servizio di rete virtuale][serviceendpoints]. 

Quando si abilitano gli endpoint del servizio su una risorsa, alcune route sono create con una priorità maggiore rispetto a tutte le altre route. Se si usano gli endpoint di servizio in qualsiasi servizio di Azure, con un ambiente del servizio app con tunneling forzato, il traffico verso tali servizi non verrà forzato tramite tunneling. 

Quando gli endpoint servizio sono abilitati in una subnet con un'istanza di SQL di Azure, tutte le istanze di SQL di Azure verso cui si esegue la connessione da tale subnet devono avere gli endpoint servizio abilitati. Se si desidera accedere a più istanze di SQL di Azure dalla stessa subnet, non è possibile abilitare gli endpoint servizio in un'istanza di SQL di Azure e non in un'altra. Nessun altro servizio di Azure si comporta come Azure SQL rispetto agli endpoint di servizio. Quando si abilitano gli endpoint servizio con Archiviazione di Azure, si blocca l'accesso a tale risorsa dalla propria subnet ma è comunque possibile accedere ad altri account di archiviazione di Azure, anche se non hanno gli endpoint servizio abilitati.  

![Endpoint del servizio][8]

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
[ConfigureSSL]: ../configure-ss-cert.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
[ASEManagement]: ./management-addresses.md
[serviceendpoints]: ../../virtual-network/virtual-network-service-endpoints-overview.md
[forcedtunnel]: ./forced-tunnel-support.md
[serviceendpoints]: ../../virtual-network/virtual-network-service-endpoints-overview.md
