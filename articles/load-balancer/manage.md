---
title: Impostazioni del portale di Azure Load Balancer
description: Inizia a imparare a usare le impostazioni del portale di Azure Load Balancer
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/8/2020
ms.author: allensu
ms.openlocfilehash: e1080aea12e70f4312fbee07b063d5a5cfbd1201
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89596281"
---
# <a name="azure-load-balancer-portal-settings"></a>Impostazioni del portale di Azure Load Balancer

Quando si crea Azure Load Balancer, le informazioni contenute in questo articolo consentono di ottenere ulteriori informazioni sulle singole impostazioni e sulla configurazione corretta.

## <a name="create-load-balancer"></a>Creare un servizio di bilanciamento del carico

Azure Load Balancer è un servizio di bilanciamento del carico di rete che distribuisce il traffico tra le istanze di macchine virtuali nel pool back-end.

### <a name="basics"></a>Nozioni di base

Nella scheda **nozioni di base** della pagina creare il portale del servizio di bilanciamento del carico verranno visualizzate le informazioni seguenti:



| Impostazione |  Dettagli |
| ---------- | ---------- |
| Subscription  | Selezionare la propria sottoscrizione. Questa selezione è la sottoscrizione in cui si vuole distribuire il servizio di bilanciamento del carico. |
| Resource group | Selezionare **Crea nuovo** e digitare il nome del gruppo di risorse nella casella di testo. Se è stato creato un gruppo di risorse esistente, selezionarlo. |
| Nome | Questa impostazione è il nome del Azure Load Balancer. |
| Region | Selezionare un'area di Azure in cui si vuole distribuire il servizio di bilanciamento del carico. |
| Type | Il servizio di bilanciamento del carico ha due tipi: </br> **Interno (privato)** </br> **Pubblico (esterno)**.</br> Un servizio di bilanciamento del carico interno (ILB) instrada il traffico ai membri del pool back-end tramite un indirizzo IP privato.</br> Un servizio di bilanciamento del carico pubblico indirizza le richieste provenienti dai client tramite Internet al pool back-end.</br> Altre informazioni sui [tipi](components.md#frontend-ip-configuration-)di servizio di bilanciamento del carico.|
| SKU  | Selezionare **Standard**. </br> Load Balancer dispone di due SKU: **Basic** e **standard**. </br> Basic presenta funzionalità limitate. </br> **Standard** è consigliato per i carichi di lavoro di produzione. </br> Altre informazioni sugli [SKU](skus.md). |

Se si seleziona **public** come tipo, verranno visualizzate le informazioni seguenti:

| Impostazione |  Dettagli |
| ---------- | ---------- |
| Indirizzo IP pubblico | Selezionare **Crea nuovo** per creare un indirizzo IP pubblico per il servizio di bilanciamento del carico pubblico. </br> Se si dispone di un indirizzo IP pubblico esistente, selezionare **Usa esistente**.  |
| Nome dell'indirizzo IP pubblico | Nome dell'indirizzo IP pubblico.|
| SKU indirizzo IP pubblico | Gli indirizzi IP pubblici hanno due SKU: **Basic** e **standard**. </br> Basic non supporta la resilienza della zona e gli attributi di zona. </br> **Standard** è consigliato per i carichi di lavoro di produzione. </br> Il servizio di bilanciamento del carico e gli SKU degli indirizzi IP pubblici **devono corrispondere**. |
| Assegnazione | **Statico** è selezionato automaticamente per standard. </br> Per gli indirizzi IP pubblici di base sono disponibili due tipi: **Dynamic** e **static**. </br> Gli indirizzi IP pubblici dinamici non vengono assegnati fino alla creazione. </br> Se la risorsa viene eliminata, è possibile che gli indirizzi IP vadano persi. </br> È consigliabile utilizzare indirizzi IP statici. |
| Zona di disponibilità | Selezionare **Con ridondanza della zona** per creare un servizio di bilanciamento del carico resiliente. </br> Per creare un servizio di bilanciamento del carico di zona, selezionare una zona specifica da **1**, **2**o **3**. </br> Le zone di supporto di Load Balancer standard e degli indirizzi IP pubblici. </br> Altre informazioni sul [bilanciamento del carico e sulle zone di disponibilità](load-balancer-standard-availability-zones.md). </br> Non verrà visualizzata la selezione della zona per Basic. Il servizio Load Balancer Basic non supporta le zone. |
| Aggiungi un indirizzo IPv6 pubblico | Load Balancer supporta gli indirizzi **IPv6** per il front-end. </br> Altre informazioni su [Load Balancer e IPv6](load-balancer-ipv6-overview.md)
| Preferenza di routing | Selezionare **rete Microsoft**. </br> Microsoft Network significa che il traffico viene instradato tramite la rete globale Microsoft. </br> Internet indica che il traffico viene instradato attraverso la rete del provider di servizi Internet. </br> Altre informazioni sulle [Preferenze di routing](../virtual-network/routing-preference-overview.md)|

:::image type="content" source="./media/manage/create-public-load-balancer-basics.png" alt-text="Creare un servizio di bilanciamento del carico pubblico." border="true":::

Se si seleziona **interno** nel tipo, verranno visualizzate le informazioni seguenti:

| Impostazione |  Dettagli |
| ---------- | ---------- |
| Rete virtuale | Rete virtuale in cui si vuole che il servizio di bilanciamento del carico interno faccia parte. </br> L'indirizzo IP front-end privato selezionato per il servizio di bilanciamento del carico interno verrà da questa rete virtuale. |
| Assegnazione indirizzi IP | Le opzioni disponibili sono **statiche** o **dinamiche**. </br> Static garantisce che l'indirizzo IP non cambi. Un indirizzo IP dinamico potrebbe cambiare. |
| Zona di disponibilità | Le opzioni disponibili sono: </br> **Con ridondanza della zona** </br> **Zona 1** </br> **Zona 2** </br> **Zona 3** </br> Per creare un servizio di bilanciamento del carico a disponibilità elevata e resiliente agli errori della zona di disponibilità, selezionare un indirizzo IP con **ridondanza della zona** . |

:::image type="content" source="./media/manage/create-internal-load-balancer-basics.png" alt-text="Creare un servizio di bilanciamento del carico pubblico." border="true":::

## <a name="frontend-ip-configuration"></a>Configurazione dell'indirizzo IP front-end

L'indirizzo IP di Azure Load Balancer. Rappresenta il punto di contatto per i client. 

È possibile disporre di una o più configurazioni IP front-end. Se è stata esaminata la sezione Nozioni di base, è già stato creato un front-end per il servizio di bilanciamento del carico. 

Se si vuole aggiungere una configurazione IP front-end al servizio di bilanciamento del carico, passare al servizio di bilanciamento del carico nella portale di Azure selezionare **configurazione IP**front-end e quindi selezionare **+ Aggiungi**.

| Impostazione |  Dettagli |
| ---------- | ---------- |
| Nome | Nome della configurazione IP front-end. |
| Versione IP | Versione dell'indirizzo IP che si vuole avere per il front-end. </br> Load Balancer supporta le configurazioni IP front-end IPv4 e IPv6. |
| Tipo IP | Il tipo IP determina se un singolo indirizzo IP è associato al front-end o a un intervallo di indirizzi IP usando un prefisso IP. </br> Un [prefisso IP pubblico](../virtual-network/public-ip-address-prefix.md) assiste quando è necessario connettersi ripetutamente allo stesso endpoint. Il prefisso garantisce che siano disponibili porte sufficienti per supportare i problemi della porta SNAT. |
| Indirizzo IP pubblico (o prefisso se è stato selezionato il prefisso precedente) | Selezionare o creare un nuovo indirizzo IP pubblico (o prefisso) per il front-end del servizio di bilanciamento del carico. |

:::image type="content" source="./media/manage/frontend.png" alt-text="Creare un servizio di bilanciamento del carico pubblico." border="true":::

## <a name="backend-pools"></a>Pool back-end

Un pool di indirizzi back-end contiene gli indirizzi IP delle interfacce di rete virtuale nel pool back-end. 

Per aggiungere un pool back-end al servizio di bilanciamento del carico, passare al servizio di bilanciamento del carico nella portale di Azure, selezionare **pool back-end**e quindi selezionare **+ Aggiungi**.

| Impostazione | Dettagli |
| ---------- |  ---------- |
| Nome | Nome del pool back-end. |
| Rete virtuale | Rete virtuale in cui si trovano le istanze di back-end. |
| Versione IP | Le opzioni disponibili sono **IPv4** o **IPv6**. |

È possibile aggiungere macchine virtuali o set di scalabilità di macchine virtuali al pool back-end della Azure Load Balancer. Creare prima le macchine virtuali o i set di scalabilità di macchine virtuali. Successivamente, aggiungerli al servizio di bilanciamento del carico nel portale.

:::image type="content" source="./media/manage/backend.png" alt-text="Creare un servizio di bilanciamento del carico pubblico." border="true":::

## <a name="health-probes"></a>Probe di integrità

Viene usato un probe di integrità per monitorare lo stato delle macchine virtuali o delle istanze back-end. Lo stato del probe di integrità determina quando vengono inviate nuove connessioni a un'istanza di in base ai controlli di integrità. 

Se si vuole aggiungere un probe di integrità al servizio di bilanciamento del carico, passare al servizio di bilanciamento del carico nella portale di Azure, selezionare **Probe integrità**e quindi selezionare **+ Aggiungi**.

| Impostazione | Dettagli |
| ---------- | ---------- |
| Nome | Nome del probe di integrità. |
| Protocollo | Il protocollo selezionato determina il tipo di controllo usato per determinare se l'istanza o le istanze back-end sono integre. </br> Le opzioni disponibili sono: </br> **TCP** </br> **HTTPS** </br> **HTTP** </br> Assicurarsi di usare il protocollo appropriato. Questa selezione dipenderà dalla natura dell'applicazione. </br> La configurazione del probe di integrità e delle risposte Probe determina quali istanze del pool back-end riceveranno nuovi flussi. </br> È possibile usare i probe di integrità per rilevare l'errore di un'applicazione in un endpoint back-end. </br> Altre informazioni sui [Probe di integrità](load-balancer-custom-probe-overview.md). |
| Porta | Porta di destinazione per il probe di integrità. </br> Questa impostazione è la porta dell'istanza back-end che verrà utilizzata dal probe di integrità per determinare l'integrità dell'istanza. |
| Interval | Numero di secondi tra i tentativi di probe. </br> L'intervallo determinerà la frequenza con cui il probe di integrità tenterà di raggiungere l'istanza back-end. </br> Se si seleziona 5, il secondo tentativo di probe verrà effettuato dopo 5 secondi e così via. |
| Soglia non integra | Il numero di errori di probe consecutivi che devono verificarsi prima che una macchina virtuale venga considerata non integra.</br> Se si seleziona 2, nessun nuovo flusso verrà impostato su questa istanza back-end dopo due errori consecutivi. |

:::image type="content" source="./media/manage/health-probe.png" alt-text="Creare un servizio di bilanciamento del carico pubblico." border="true":::

## <a name="load-balancing-rules"></a>Regole di bilanciamento del carico

Definisce il modo in cui il traffico in ingresso viene distribuito a tutte le istanze all'interno del pool back-end. Una regola di bilanciamento del carico esegue il mapping di una determinata configurazione IP front-end e di una porta a più indirizzi IP e porte back-end.

Se si vuole aggiungere una regola del servizio di bilanciamento del carico al servizio di bilanciamento del carico, passare al servizio di bilanciamento del carico nella portale di Azure selezionare **regole di bilanciamento del carico**e quindi selezionare **+ Aggiungi**.
    
| Impostazione | Dettagli |
| ---------- | ---------- |
| Nome | Nome della regola del servizio di bilanciamento del carico. |
| Versione indirizzo IP | Le opzioni disponibili sono **IPv4** o **IPv6**.  |
| Indirizzo IP front-end | Selezionare l'indirizzo IP front-end. </br> Indirizzo IP front-end del servizio di bilanciamento del carico a cui si vuole associare la regola del servizio di bilanciamento del carico.|
| Protocollo | Azure Load Balancer è un servizio di bilanciamento del carico di rete di livello 4. </br> Le opzioni disponibili sono: **TCP** o **UDP**. |
| Porta | Questa impostazione è la porta associata all'IP front-end in cui si vuole distribuire il traffico in base a questa regola di bilanciamento del carico. |
| Porta back-end | Questa impostazione è la porta delle istanze nel pool back-end a cui il servizio di bilanciamento del carico deve inviare il traffico. Questa impostazione può essere identica a quella della porta front-end o a una diversa se è necessaria la flessibilità per l'applicazione. |
| Pool back-end | Pool back-end a cui si desidera applicare questa regola di bilanciamento del carico. |
| Probe di integrità | Il probe di integrità creato per verificare lo stato delle istanze nel pool back-end. </br> Solo le istanze integre riceveranno il nuovo traffico. |
| Persistenza della sessione |  Le opzioni disponibili sono: </br> **Nessuno** </br> **IP client** </br> **IP e protocollo client**</br> </br> Mantenere il traffico da un client alla stessa macchina virtuale nel pool back-end. Questo traffico verrà mantenuto per la durata della sessione. </br> **None** specifica che le richieste successive provenienti dallo stesso client possono essere gestite da qualsiasi macchina virtuale. </br> **IP client** specifica che le richieste successive provenienti dallo stesso indirizzo IP client verranno gestite dalla stessa macchina virtuale. </br> **IP e protocollo client** garantiscono che le richieste successive provenienti dallo stesso indirizzo IP e protocollo client verranno gestite dalla stessa macchina virtuale. </br> Altre informazioni sulle [modalità di distribuzione](load-balancer-distribution-mode.md). |
| Timeout di inattività (minuti) | Mantieni aperta una connessione **TCP** o **http** senza basarsi sui client per inviare messaggi keep-alive |  
| Ripristino TCP | Il servizio di bilanciamento del carico può inviare **reimpostazioni TCP** per facilitare la creazione di un comportamento più prevedibile dell'applicazione in quando la connessione è inattiva. </br> Altre informazioni sulla [reimpostazione TCP](load-balancer-tcp-reset.md)|
| IP mobile | L'indirizzo IP mobile è la terminologia di Azure per una parte di ciò che è noto come **Direct Server Return (DSR)**. </br> La configurazione DSR è costituita da due parti: <br> 1. topologia flusso </br> 2. Schema di mapping degli indirizzi IP a livello di piattaforma. </br></br> Azure Load Balancer funziona sempre in una topologia del flusso DSR se l'indirizzo IP mobile è abilitato o meno. </br> Questa operazione significa che la parte in uscita di un flusso viene sempre riscritta correttamente in modo che fluisca direttamente nell'origine. </br> Senza IP mobile, Azure espone uno schema di mapping degli indirizzi IP con bilanciamento del carico tradizionale, ovvero l'IP delle istanze di VM. </br> L'abilitazione dell'indirizzo IP mobile cambia il mapping degli indirizzi IP all'IP front-end del servizio di bilanciamento del carico per consentire una maggiore flessibilità. </br> Per ulteriori informazioni, vedere la pagina relativa a più Front- [end per Azure Load Balancer](load-balancer-multivip-overview.md).|
| Crea regole in uscita implicite | Selezionare **No**. </br> Impostazione predefinita: **disableOutboundSnat = false**  </br> In questo caso l'uscita avviene tramite lo stesso IP front-end. </br></br> **disableOutboundSnat = true** </br>In questo caso, le regole in uscita sono necessarie per l'uscita. |

:::image type="content" source="./media/manage/load-balancing-rule.png" alt-text="Creare un servizio di bilanciamento del carico pubblico." border="true":::

## <a name="inbound-nat-rules"></a>Regole NAT in ingresso

Una regola NAT in ingresso inoltra il traffico in ingresso inviato alla combinazione di porta e indirizzo IP front-end. 

Il traffico viene inviato a una specifica macchina virtuale o istanza nel pool back-end. Il port forwarding viene eseguito dalla stessa distribuzione basata su hash del bilanciamento del carico.

Se lo scenario richiede sessioni di Remote Desktop Protocol (RDP) o Secure Shell (SSH) per separare le istanze di VM in un pool back-end. È possibile eseguire il mapping di più endpoint interni alle porte sullo stesso indirizzo IP front-end. 

Gli indirizzi IP front-end possono essere usati per amministrare in remoto le VM senza una finestra di salto aggiuntiva.

Se si vuole aggiungere una regola NAT in ingresso al servizio di bilanciamento del carico, passare al servizio di bilanciamento del carico nella portale di Azure selezionare **regole NAT in ingresso**e quindi selezionare **+ Aggiungi**.

| Impostazione | Dettagli |
| ---------- | ---------- |
| Nome | Nome della regola NAT in ingresso |
| Indirizzo IP front-end | Selezionare l'indirizzo IP front-end. </br> Indirizzo IP front-end del servizio di bilanciamento del carico a cui si vuole associare la regola NAT in ingresso. |
| Versione indirizzo IP | Le opzioni disponibili sono IPv4 e IPv6. |
| Servizio | Il tipo di servizio che verrà eseguito in Azure Load Balancer. </br> Una selezione qui aggiornerà correttamente le informazioni sulla porta. |
| Protocollo | Azure Load Balancer è un servizio di bilanciamento del carico di rete di livello 4. </br> Le opzioni disponibili sono: TCP o UDP. |
| Timeout di inattività (minuti) | Mantieni aperta una connessione TCP o HTTP senza basarsi sui client per inviare messaggi keep-alive. |
| Reimpostazione TCP | Load Balancer possibile inviare le reimpostazioni TCP per facilitare la creazione di un comportamento più prevedibile dell'applicazione in quando la connessione è inattiva. </br> Altre informazioni sulla [reimpostazione TCP](load-balancer-tcp-reset.md) |
| Porta | Questa impostazione è la porta associata all'IP front-end in cui si vuole distribuire il traffico in base a questa regola NAT in ingresso. |
| Macchina virtuale di destinazione | Parte della macchina virtuale del pool back-end a cui si vuole associare questa regola. |
| Mapping delle porte | Questa impostazione può essere predefinita o personalizzata in base alle preferenze dell'applicazione. |

:::image type="content" source="./media/manage/inbound-nat-rule.png" alt-text="Creare un servizio di bilanciamento del carico pubblico." border="true":::

## <a name="outbound-rules"></a>Regole in uscita

Le regole in uscita del servizio di bilanciamento del carico configurano la conversione degli indirizzi di rete di origine in uscita per le macchine virtuali nel pool back-end.

Se si vuole aggiungere una regola in uscita al servizio di bilanciamento del carico, passare al servizio di bilanciamento del carico nella portale di Azure, selezionare **regole in uscita**e quindi selezionare **+ Aggiungi**.

| Impostazione | Dettagli |
| ------- | ------ |
| Nome | Nome della regola in uscita. |
| Indirizzo IP front-end | Selezionare l'indirizzo IP front-end. </br> Indirizzo IP front-end del servizio di bilanciamento del carico a cui si vuole associare la regola in uscita. |
| Protocollo | Azure Load Balancer è un servizio di bilanciamento del carico di rete di livello 4. </br> Le opzioni disponibili sono: **All**, **TCP**o **UDP**. |
| Timeout di inattività (minuti) | Mantieni aperta una connessione **TCP** o **http** senza basarsi sui client per inviare messaggi keep-alive. |
| Reimpostazione TCP | Il servizio di bilanciamento del carico può inviare **reimpostazioni TCP** per facilitare la creazione di un comportamento più prevedibile dell'applicazione in quando la connessione è inattiva. </br> Altre informazioni sulla [reimpostazione TCP](load-balancer-tcp-reset.md) |
| Pool back-end | Pool back-end a cui si desidera applicare questa regola in uscita. |

### <a name="port-allocation"></a>Allocazione porta

| Impostazione | Dettagli |
| ------- | ------ |
| Allocazione porta | Si consiglia di selezionare il **numero di porte in uscita manualmente**.|

### <a name="outbound-ports"></a>Porte in uscita

| Impostazione | Dettagli |
| ------- | ------ |
| Scegli per | Selezionare **Porte per istanza** |
| Porte per istanza | Immettere **10.000**. |

:::image type="content" source="./media/manage/outbound-rule.png" alt-text="Creare un servizio di bilanciamento del carico pubblico." border="true":::

## <a name="next-steps"></a>Passaggi successivi

In questo articolo sono stati illustrati i diversi termini e impostazioni nel portale di Azure per Azure Load Balancer.

* [Altre informazioni](./load-balancer-overview.md) su Azure Load Balancer.
* [Domande frequenti](./load-balancer-faqs.md) per Azure Load Balancer.