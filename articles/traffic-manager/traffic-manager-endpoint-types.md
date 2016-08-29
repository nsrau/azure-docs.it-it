<properties
   pageTitle="Tipi di endpoint di Gestione traffico | Microsoft Azure"
   description="Questo articolo illustra tipi diversi di endpoint che è possibile usare con Gestione traffico di Azure"
   services="traffic-manager"
   documentationCenter=""
   authors="jtuliani"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="06/08/2016"
   ms.author="jtuliani" />

# Endpoint di Gestione traffico

Gestione traffico di Microsoft Azure permette di controllare la distribuzione del traffico utente alle distribuzioni di applicazioni in esecuzione in diversi data center o in diverse località nel mondo.

In Gestione traffico ogni distribuzione di applicazioni deve essere configurata come un "endpoint". Quando Gestione traffico riceve una richiesta DNS, è possibile scegliere uno degli endpoint da restituire nella risposta DNS in base alla disponibilità dell'endpoint corrente e al metodo di routing del traffico scelto. Per altre informazioni, vedere [Modalità di funzionamento di Gestione traffico](traffic-manager-how-traffic-manager-works.md).

Questo articolo illustra il supporto di endpoint di vario tipo da parte di Gestione traffico.

## Tipi di endpoint di Gestione traffico

Gli endpoint supportati da Gestione traffico sono di tre tipi:

- **Endpoint di Azure**, usati per i servizi ospitati in Azure.
- **Endpoint esterni**, usati per i servizi ospitati all'esterno di Azure, in locale o da un provider di hosting diverso.
- **Endpoint annidati**, usati per combinare i profili di Gestione traffico e creare schemi di routing del traffico più flessibili, per supportare le esigenze di distribuzioni più grandi e complesse.

Non ci sono limitazioni al modo in cui è possibile combinare tipi di endpoint diversi in un unico profilo di Gestione traffico. Ogni profilo può contenere qualsiasi combinazione di tipi di endpoint.

Le sezioni seguenti descrivono i singoli tipi di endpoint in modo più approfondito.

### Endpoint di Azure

Gli endpoint di Azure vengono usati per configurare servizi basati su Azure in Gestione traffico. Gli endpoint di Azure attualmente supportano i tipi di risorse di Azure seguenti:

- Macchine virtuali IaaS classiche e servizi cloud PaaS.
- App Web
- Risorse PublicIPAddress, che possono essere collegate alle macchine virtuali direttamente o tramite Azure Load Balancer. Si noti che è necessario che al valore publicIpAddress sia assegnato un nome DNS, da usare in Gestione traffico.

Le risorse PublicIPAddress sono risorse di Azure Resource Manager che non sono disponibili nelle Azure Service Management API. Sono quindi supportate unicamente nelle esperienze di tipo Azure Resource Manager di Gestione traffico. Gli altri tipi di endpoint sono supportati tramite esperienze Resource Manager ed esperienze Service Management in Gestione traffico.

Quando si usano gli endpoint di Azure, Gestione traffico rileva le attività di arresto e avvio di macchine virtuali IaaS classiche, servizi cloud PaaS o app Web. Questo si riflette nello stato dell'endpoint. Per altre informazioni, vedere la relativa sezione nell'articolo [Informazioni sul monitoraggio di Gestione traffico](traffic-manager-monitoring.md#endpoint-and-profile-status). Quando il servizio sottostante viene arrestato, Gestione traffico interrompe la fatturazione per i controlli di integrità dell'endpoint e smette di indirizzare il traffico all'endpoint. Quando il servizio viene riavviato, la fatturazione riprende e l'endpoint è di nuovo idoneo a ricevere il traffico. Questo non si applica agli endpoint PublicIpAddress.

### Endpoint esterni

Gli endpoint esterni vengono usati per configurare Gestione traffico per l'indirizzamento del traffico ai servizi all'esterno di Azure, ad esempio un servizio ospitato in locale o un servizio ospitato da un provider diverso.

Gli endpoint esterni possono essere usati autonomamente o in combinazione con endpoint di Azure nello stesso profilo di Gestione traffico. La combinazione di endpoint di Azure con endpoint esterni consente un'ampia gamma di scenari:

- Uso di Azure per offrire maggiore ridondanza per un'applicazione locale esistente, in un modello di failover attivo-passivo o attivo-attivo.
- Uso di Azure per estendere un'applicazione locale esistente ad aree geografiche aggiuntive, insieme al [metodo di routing del traffico Prestazioni di Gestione traffico](traffic-manager-routing-methods.md#performance-traffic-routing-method), per ridurre la latenza dell'applicazione e migliorare le prestazioni per gli utenti finali.
- Uso di Azure per offrire capacità aggiuntiva a un'applicazione locale esistente, in modo continuo o come "burst nel cloud" per gestire un picco di domanda.

In alcuni casi può essere utile usare endpoint esterni per fare riferimento a servizi di Azure. Per alcuni esempi, vedere la sezione [Domande frequenti](#faq). In questo caso la fatturazione per i controlli di integrità avviene alla tariffa degli endpoint di Azure anziché alla tariffa degli endpoint esterni. A differenza degli endpoint di Azure, tuttavia, se si arresta o si elimina il servizio sottostante, la fatturazione prosegue per i controlli di integrità corrispondenti fino a quando non si disabilita o si elimina l'endpoint in Gestione traffico in modo esplicito.

### Endpoint annidati

Gli Endpoint annidati vengono usati per combinare i profili di Gestione traffico e creare schemi di routing del traffico più flessibili, per supportare le esigenze di distribuzioni più grandi e complesse.

Quando si usano gli endpoint annidati, viene creata una gerarchia aggiungendo come endpoint un profilo "figlio" a un profilo "padre". Entrambi i profili padre e figlio possono contenere altri endpoint di qualsiasi tipo, inclusi altri profili annidati.

Per altre informazioni, vedere [Profili nidificati di Gestione traffico](traffic-manager-nested-profiles.md).

## App Web come endpoint

Per la configurazione di app Web come endpoint in Gestione traffico si rendono necessarie alcune considerazioni aggiuntive:

1. Solo le app Web a partire dallo SKU Standard sono idonee all'uso con Gestione traffico. Le chiamate a Gestione traffico per l'aggiunta di app Web dello SKU di una versione inferiore avranno esito negativo. Eseguendo il downgrade dello SKU di un'app Web esistente, Gestione traffico smette di inviare traffico all'app Web specifica ed è possibile rimuovere l'endpoint da Gestione traffico.

2. Quando il servizio app Web riceve una richiesta HTTP, usa l'intestazione "host" nella richiesta per determinare quale app Web usare per soddisfarla. L'intestazione host contiene il nome DNS usato per avviare la richiesta, ad esempio "contosoapp.azurewebsites.net". Per usare un nome DNS diverso con l'app Web, il nome DNS deve essere registrato come dominio personalizzato per l'app. Quando si aggiunge un endpoint di app Web a un profilo di Gestione traffico come endpoint di Azure, il nome DNS del profilo di Gestione traffico viene registrato automaticamente come dominio personalizzato per l'app. Questa registrazione viene rimossa automaticamente quando l'endpoint viene eliminato.

3. In genere, un'app Web viene configurata come endpoint di Azure. In alcuni casi, tuttavia, è utile configurare un'app Web con un endpoint esterno. Per un esempio, vedere il punto seguente. In Gestione traffico è possibile configurare le app Web come endpoint esterni unicamente con esperienze Resource Manager di Gestione traffico. Questo tipo di configurazione non è supportato con le esperienze Service Management.

4. Ogni profilo di Gestione traffico può avere al massimo un endpoint di app Web da ogni area di Azure. Una soluzione alternativa a questo vincolo viene fornita nella sezione [Domande frequenti](#faq).

## Abilitazione e disabilitazione di endpoint

La disabilitazione di un endpoint in Gestione traffico risulta particolarmente utile per rimuovere temporaneamente il traffico da un endpoint in modalità di manutenzione o in corso di ridistribuzione. Quando l'endpoint è di nuovo operativo, è possibile abilitarlo nuovamente.

Per abilitare e disabilitare gli endpoint è possibile usare il portale di Gestione traffico, PowerShell, l'interfaccia della riga di comando o l'API REST, tutti supportati sia in esperienze Resource Manager che Service Management.

>[AZURE.NOTE] La disabilitazione di un endpoint di Azure non ha nulla a che vedere con il relativo stato di distribuzione in Azure. Un servizio di Azure, ad esempio una macchina virtuale o un'app Web, rimane in esecuzione e può ricevere traffico anche se è disabilitato in Gestione traffico, se il traffico viene indirizzato direttamente al servizio anziché tramite il nome DNS del profilo di Gestione traffico. Per altre informazioni, vedere [Modalità di funzionamento di Gestione traffico](traffic-manager-how-traffic-manager-works.md).

Quando un endpoint è disabilitato, non viene più restituito nelle risposte DNS e quindi non riceve più traffico. Anche i controlli di integrità per l'endpoint vengono arrestati e non vengono più fatturati. La disabilitazione di un endpoint equivale all'eliminazione, se non per il fatto che nel primo caso è più semplice abilitarlo nuovamente.

L'idoneità corrente di ogni endpoint a ricevere il traffico dipende dallo stato del profilo (abilitato/disabilitato), dallo stato dell'endpoint (abilitato/disabilitato) e dai risultati dei controlli di integrità per l'endpoint. Per altre informazioni, vedere [Informazioni sul monitoraggio di Gestione traffico](traffic-manager-monitoring.md#endpoint-and-profile-status).

>[AZURE.NOTE] Dal momento che Gestione traffico lavora a livello di DNS, non è in grado di influenzare le connessioni esistenti verso qualsiasi endpoint. Quando si indirizza il traffico tra gli endpoint, modificando le impostazioni del profilo oppure durante il failover o il failback, Gestione traffico indirizza le nuove connessioni agli endpoint disponibili. Altri endpoint possono tuttavia continuare a ricevere il traffico tramite le connessioni esistenti fino a quando quelle sessioni non vengono terminate. Per consentire lo smaltimento del traffico dalle connessioni esistenti, le applicazioni devono limitare la durata della sessione usata con ogni endpoint.

Se tutti gli endpoint di un profilo sono disabilitati o se il profilo stesso è disabilitato, le query DNS ottengono una risposta "NXDOMAIN". Questa situazione equivale all'eliminazione del profilo.

## Domande frequenti

### È possibile usare Gestione traffico con endpoint di più sottoscrizioni?
Per le app Web di Azure, non è possibile. Questo perché ogni nome di dominio personalizzato usato nelle app Web deve essere usato solo all'interno di una singola sottoscrizione. Non essendo possibile usare le app Web da più sottoscrizioni con lo stesso nome di dominio, le app Web non possono essere utilizzate con Gestione traffico.

Per altri tipi di endpoint, è possibile utilizzare Gestione traffico con gli endpoint da più di una sottoscrizione. La procedura varia a seconda che si usino Azure Service Management API oppure API di Resource Manager per Gestione traffico. Il [portale di Azure](https://portal.azure.com) usa Azure Resource Manager, mentre il [portale classico](https://manage.windowsazure.com) usa Azure Service Management.

In Resource Manager è possibile aggiungere endpoint di qualsiasi sottoscrizione a Gestione traffico, purché la persona che configura il profilo di Gestione traffico abbia accesso in lettura all'endpoint. Queste autorizzazioni possono essere concesse tramite il [controllo di accesso in base al ruolo di Azure Resource Manager](../active-directory/role-based-access-control-configure.md).

In Service Management è necessario che tutti i servizi cloud e le app Web configurati come endpoint di Azure si trovino nella stessa sottoscrizione del profilo di Gestione traffico. È possibile aggiungere endpoint del servizio cloud di altre sottoscrizioni a Gestione traffico come endpoint esterni. Verranno comunque fatturati alla tariffa degli endpoint interni.

### È possibile usare Gestione traffico con slot di staging del servizio cloud?
Sì. Gli slot di staging del servizio cloud possono essere configurati come endpoint esterni in Gestione traffico.

Dal momento che il servizio a cui fa riferimento l'endpoint si trova in Azure, i controlli di integrità verranno addebitati alla tariffa degli endpoint di Azure.

Dato che viene usato il tipo di endpoint esterno, le modifiche al servizio sottostante non vengono rilevate automaticamente. Quindi, se il servizio cloud viene arrestato o eliminato, i controlli di integrità continueranno a essere addebitati all'endpoint di Gestione traffico fino a quando l'endpoint non viene disabilitato o eliminato all'interno di Gestione traffico.

### Gestione traffico supporta endpoint IPv6?

Sì. Anche se Gestione traffico attualmente non fornisce server dei nomi indirizzabili tramite IPv6, può comunque essere usato da client IPv6 che si connettono a endpoint IPv6.

Anche un client solo IPv6 può usare Gestione traffico, dato che non invia richieste DNS direttamente a Gestione traffico, ma usa un servizio DNS ricorsivo. Il client può inviare richieste al servizio tramite IPv6. Il servizio ricorsivo può quindi contattare i server dei nomi di Gestione traffico tramite IPv4.

Quando riceve una query DNS, Gestione traffico risponde con il nome DNS dell'endpoint a cui il client deve connettersi. Per supportare un endpoint IPv6 è sufficiente configurare un record AAAA DNS che punta il nome DNS dell'endpoint all'indirizzo IPv6.

Si noti che per il corretto funzionamento dei controlli di integrità di Gestione traffico, il servizio deve anche esporre un endpoint IPv4. Il mapping di tale endpoint deve essere eseguito dallo stesso nome DNS dell'endpoint usando un record A DNS.

### È possibile usare Gestione traffico con più app Web nella stessa area?

In genere, Gestione traffico viene usato per indirizzare il traffico ad applicazioni distribuite in aree diverse. Tuttavia, può anche essere usato in un'applicazione che abbia più distribuzioni nella stessa area.

Per quanto riguarda le app Web, gli endpoint di Azure di Gestione traffico non permettono di aggiungere più endpoint di app Web della stessa area di Azure allo stesso profilo di Gestione traffico. I passaggi seguenti offrono una soluzione alternativa a questo vincolo:

1.	Verificare che le app Web della stessa area si trovino in unità di scala diverse dell'app Web, vale a dire in istanze diverse del servizio app Web. A tale scopo, cercare la voce DNS <...>.azurewebsites.net nel percorso DNS. L'unità di scala avrà un aspetto simile a "waws-prod-xyz-123.vip.azurewebsites.net". Un determinato nome di dominio deve eseguire il mapping a un unico sito in un'unità di scala specificata. È per questo motivo che due app Web nella stessa unità di scala non possono condividere un profilo di Gestione traffico.
2.	Supponendo che ogni app Web sia un'unità di scala diversa, aggiungere il nome di dominio personalizzato come nome host personalizzato a ogni app Web. Per poter eseguire questa operazione è necessario che tutte le app Web facciano parte della stessa sottoscrizione.
3.	Aggiungere normalmente un unico endpoint di app Web a Gestione traffico come endpoint di Azure.
4.	Aggiungere gli altri endpoint di app Web al profilo di Gestione traffico come endpoint esterni. A questo scopo è necessario usare l'esperienza Resource Manager per Gestione traffico, anziché Service Management.
5.	Dal dominio personalizzato usato nel passaggio 2 precedente creare un record CNAME DNS nel nome DNS del profilo di Gestione traffico, <…>.trafficmanager.net.
6.	Accedere al sito tramite il nome di dominio personalizzato anziché dal nome DNS del profilo di Gestione traffico.

## Passaggi successivi

- Informazioni sul [funzionamento di Gestione traffico](traffic-manager-how-traffic-manager-works.md).

- Informazioni sul [monitoraggio degli endpoint e sul failover automatico](traffic-manager-monitoring.md) di Gestione traffico.

- Informazioni sui [metodi di routing del traffico](traffic-manager-routing-methods.md) di Gestione traffico.

<!---HONumber=AcomDC_0817_2016-->