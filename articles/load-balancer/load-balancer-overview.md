---
title: Informazioni su Azure Load Balancer
titleSuffix: Azure Load Balancer
description: Panoramica di funzionalità, architettura e implementazione di Azure Load Balancer. Informazioni sul funzionamento di Load Balancer e su come usarlo nel cloud.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
Customer intent: As an IT administrator, I want to learn more about the Azure Load Balancer service and what I can use it for.
ms.devlang: na
ms.topic: overview
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/21/2019
ms.author: allensu
ms.openlocfilehash: 335549f4ccae01fa36921e0e4668fa15e8b33835
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/23/2019
ms.locfileid: "74423901"
---
# <a name="what-is-azure-load-balancer"></a>Informazioni su Azure Load Balancer

Azure Load Balancer consente di ridimensionare le applicazioni e di creare disponibilità elevata per i servizi. Load Balancer supporta scenari in ingresso e in uscita, offre bassa latenza e velocità effettiva elevata e una scalabilità fino a milioni di flussi per tutte le applicazioni TCP e UDP.

Load Balancer distribuisce i nuovi flussi in ingresso che arrivano sul front-end relativo nelle istanze del pool back-end in base alle regole e ai probe di integrità.

Un servizio Load Balancer pubblico può anche offrire connessioni in uscita per le macchine virtuali all'interno della rete virtuale convertendo gli indirizzi IP privati in indirizzi IP pubblici.

Azure Load Balancer è disponibile in due piani tariffari o *SKU*: Di base e standard. con differenze di scalabilità, funzionalità e prezzo. Tutti gli scenari possibili con Load Balancer Basic possono essere creati anche con Load Balancer Standard, anche se l'approccio è leggermente diverso. Con le informazioni su Load Balancer, acquisire anche familiarità con le nozioni di base e le differenze specifiche degli SKU.

## <a name="why-use-load-balancer"></a>Perché usare Azure Load Balancer

È possibile usare Azure Load Balancer per eseguire queste operazioni:

* Bilanciare il carico del traffico Internet in ingresso nelle macchine virtuali. Questa configurazione è nota come [bilanciamento del carico pubblico](#publicloadbalancer).
* Bilanciare il carico del traffico tra le macchine virtuali in una rete virtuale. È anche possibile raggiungere un front-end di Load Balancer da una rete locale in uno scenario ibrido. Entrambi gli scenari usano una configurazione nota come [bilanciamento del carico interno](#internalloadbalancer).
* Inoltrare il traffico a una porta specifica su specifiche macchine virtuali con le regole NAT (Network Address Translation) in ingresso.
* Offrire [connettività in uscita](load-balancer-outbound-connections.md) per le macchine virtuali all'interno della rete virtuale usando un Load Balancer pubblico.

>[!NOTE]
> Azure offre una suite di soluzioni di bilanciamento del carico completamente gestite per tutti gli scenari. Per la terminazione del protocollo Transport Layer Security (TLS), detta anche "offload SSL", la richiesta per HTTP/HTTPS o l'elaborazione a livello di applicazione, vedere [Informazioni sul gateway applicazione di Azure](../application-gateway/overview.md). Per il bilanciamento del carico DNS globale, vedere [Informazioni su Gestione traffico](../traffic-manager/traffic-manager-overview.md). Gli scenari end-to-end possono trarre vantaggio dalla combinazione di queste soluzioni in base alle esigenze.
>
> Per un confronto tra le opzioni di bilanciamento del carico di Azure, vedere [Panoramica delle opzioni di bilanciamento del carico in Azure](https://docs.microsoft.com/azure/architecture/guide/technology-choices/load-balancing-overview).

## <a name="what-are-load-balancer-resources"></a>Informazioni sulle risorse di Load Balancer

Le risorse di Load Balancer sono oggetti che specificano il modo in cui Azure deve programmare la propria infrastruttura multi-tenant per ottenere lo scenario da creare. Non esiste una relazione diretta tra le risorse di Load Balancer e l'infrastruttura effettiva. La creazione di Load Balancer non crea un'istanza e la capacità è sempre disponibile.

Una risorsa di Load Balancer può esistere come servizio Load Balancer pubblico o interno. Le funzioni della risorsa di Load Balancer sono espresse come definizione di front-end, regola, probe di integrità e pool back-end. Le macchine virtuali vengono inserite nel pool back-end specificando il pool back-end dalla macchina virtuale stessa.

## <a name="fundamental-load-balancer-features"></a>Funzionalità principali di Load Balancer

Load Balancer offre le funzionalità principali seguenti per applicazioni TCP e UDP:

* **Bilanciamento del carico**

  Con Azure Load Balancer è possibile creare una regola di bilanciamento del carico per distribuire il traffico che arriva al front-end nelle istanze del pool back-end. Load Balancer usa un algoritmo hash per distribuire i flussi in ingresso e riscrive le intestazioni dei flussi nelle istanze del pool back-end. Un server è disponibile per ricevere i nuovi flussi quando un probe di integrità indica un endpoint di back-end integro.

  Per impostazione predefinita, Load Balancer usa un hash a 5 tuple. L'hash include l'indirizzo IP e la porta di origine, l'indirizzo IP e la porta di destinazione e il numero di protocollo IP per eseguire il mapping dei flussi ai server disponibili. È possibile creare affinità a un indirizzo IP di origine usando un hash a 2 o 3 tuple per una determinata regola. Tutti i pacchetti dello stesso flusso di pacchetti arrivano sulla stessa istanza dietro il front-end con carico bilanciato. Quando il client avvia un nuovo flusso dallo stesso indirizzo IP di origine, la porta di origine cambia. Di conseguenza, l'algoritmo hash a 5 tuple può far sì che il traffico venga indirizzato verso un endpoint di back-end diverso.

  Per altre informazioni, vedere [Configurare la modalità di distribuzione per Azure Load Balancer](load-balancer-distribution-mode.md). L'immagine seguente mostra la distribuzione basata su hash:

  ![Distribuzione basata su hash](./media/load-balancer-overview/load-balancer-distribution.png)

  *Figura: Distribuzione basata su hash*

* **Port forwarding**

  Con Load Balancer è possibile creare una regola NAT in ingresso. Questa regola NAT trasmette il traffico da una porta specifica di un indirizzo IP front-end specifico a una determinata porta di un'istanza di back-end specifica nella rete virtuale. Questa trasmissione viene eseguita dalla stessa distribuzione basata su hash del bilanciamento del carico. Scenari comuni per questa funzionalità sono le sessioni di Remote Desktop Protocol (RDP) o Secure Shell (SSH) per le singole istanze di macchina virtuale in una rete virtuale di Azure.
  
  È possibile eseguire il mapping di più endpoint interni a porte sullo stesso indirizzo IP front-end. È possibile usare gli indirizzi IP front-end per amministrare in remoto le macchine virtuali senza una finestra di collegamento aggiuntiva.

* **Indipendenza e trasparenza delle applicazioni**

  Load Balancer non interagisce direttamente con il protocollo TCP o UDP o con il livello dell'applicazione. Può essere supportato qualsiasi scenario di applicazione TCP o UDP. Load Balancer non termina né origina flussi, non interagisce con il payload del flusso né fornisce alcuna funzione del gateway a livello dell'applicazione. Gli handshake del protocollo si verificano sempre direttamente tra il client e l'istanza del pool back-end. Una risposta a un flusso in ingresso è sempre una risposta da una macchina virtuale. Quando il flusso arriva nella macchina virtuale, viene mantenuto anche l'indirizzo IP di origine.

  * A ogni endpoint risponde solo una macchina virtuale. Ad esempio, si verifica sempre un handshake TCP tra il client e la macchina virtuale di back-end selezionata. Una risposta a una richiesta in un front-end è una risposta generata da una macchina virtuale di back-end. Quando si convalida correttamente la connettività a un front-end, si convalida anche la connettività end-to-end per almeno una macchina virtuale di back-end.
  * I payload dell'applicazione sono trasparenti per Load Balancer. Qualsiasi applicazione UDP o TCP può essere supportata. Per i carichi di lavoro che richiedono l'elaborazione di richieste HTTP o la manipolazione di payload a livello di applicazione (ad esempio l'analisi di URL HTTP), usare un servizio di bilanciamento del carico di livello 7 come [gateway applicazione](https://azure.microsoft.com/services/application-gateway).
  * Poiché Load Balancer non interagisce con il payload TCP e fornisce l'offload TLS, è possibile creare scenari crittografati end-to-end. L'uso di Load Balancer consente di ottenere un'ampia scalabilità orizzontale per le applicazioni TLS terminando la connessione TLS nella macchina virtuale stessa. Ad esempio, la funzionalità di codifica della sessione TLS è limitata solo dal tipo e dal numero di macchine virtuali aggiunte al pool di back-end. Se è necessario un offload SSL o il trattamento al livello dell'applicazione oppure si vuole delegare la gestione dei certificati ad Azure, usare il [gateway applicazione](https://azure.microsoft.com/services/application-gateway) del servizio di bilanciamento del carico di livello 7 di Azure.

* **Riconfigurazione automatica**

  Load Balancer si riconfigura immediatamente in base all'aumento o alla riduzione delle istanze. L'aggiunta o la rimozione di macchine virtuali dal pool back-end riconfigura il servizio Load Balancer senza operazioni aggiuntive sulla risorsa relativa.

* **Probe di integrità**

  Load Balancer usa i probe di integrità definiti dall'utente per determinare l'integrità delle istanze nel pool back-end. Se un probe non risponde, il servizio Load Balancer interrompe l'invio di nuove connessioni alle istanze non integre. Un errore di probe non influisce sulle connessioni esistenti. La connessione continua a funzionare fino a quando l'applicazione non termina il flusso, non si verifica un timeout di inattività o la macchina virtuale non si arresta.

  Load Balancer fornisce diversi tipi di probe di integrità per endpoint TCP, HTTP e HTTPS. Per altre informazioni, vedere [Tipi di probe](load-balancer-custom-probe-overview.md#types).

  Quando si usano i servizi cloud classici, è consentito anche un tipo aggiuntivo, ovvero [Agente guest](load-balancer-custom-probe-overview.md#guestagent). Come probe di identità, un agente guest deve essere usato solo come ultima scelta. Se sono disponibili altre opzioni, Microsoft non ne consiglia l'uso.

* **Connessioni in uscita (SNAT)**

  Tutti i flussi in uscita dagli indirizzi IP privati all'interno della rete virtuale verso gli indirizzi IP pubblici in Internet possono essere convertiti in indirizzi IP front-end di Load Balancer. Quando un front-end pubblico è associato a una macchina virtuale back-end tramite una regola di bilanciamento del carico, Azure converte le connessioni in uscita nell'indirizzo IP front-end pubblico. Questa configurazione presenta i vantaggi seguenti:

  * Semplicità di aggiornamento e di ripristino di emergenza dei servizi, perché è possibile eseguire il mapping dinamico del front-end a un'altra istanza del servizio.
  * Gestione semplificata degli elenchi di controllo di accesso (ACL). Gli ACL espressi in termini di indirizzi IP front-end non si modificano in caso di ridimensionamento o di ridistribuzione dei servizi. La conversione delle connessioni in uscita in un numero di indirizzi IP inferiore a quello delle macchine riduce il carico correlato all'implementazione di elenchi di destinatari sicuri.

  Per altre informazioni, vedere [Connessioni un uscita in Azure](load-balancer-outbound-connections.md).

Oltre a quelle fondamentali, Load Balancer Standard offre capacità aggiuntive specifiche dello SKU, come descritto di seguito.

## <a name="skus"></a> Confronto tra gli SKU di Load Balancer

Load Balancer supporta SKU Basic e Standard, che variano in termini di scalabilità degli scenari, funzionalità e prezzi. Tutti gli scenari possibili con Load Balancer Basic possono essere creati anche con Load Balancer Standard. Le API per entrambi gli SKU sono simili e vengono richiamate tramite la specifica di uno SKU. L'API per supportare gli SKU di Load Balancer e l'indirizzo IP pubblico sono disponibili a partire dall'API `2017-08-01`. Entrambi gli SKU condividono la stessa API e la stessa struttura generale.

La configurazione dello scenario completo potrebbe variare leggermente in base allo SKU. La documentazione di Load Balancer specifica quando un articolo è applicabile solo a un determinato SKU. Esaminare la tabella seguente per confrontare e comprendere le differenze. Per altre informazioni, vedere [Azure Panoramica di Load Balancer Standard](load-balancer-standard-overview.md).

>[!NOTE]
> Per i nuovi progetti è consigliabile adottare Load Balancer Standard.

Le macchine virtuali autonome, i set di disponibilità e i set di scalabilità di macchine virtuali possono essere collegati solo a uno SKU, non a entrambi. Se usati con gli indirizzi IP pubblici, lo SKU di Load Balancer e quello dell'indirizzo IP pubblico devono corrispondere. Gli SKU di Load Balancer e quelli degli indirizzi IP pubblici non sono modificabili.

È consigliabile specificare gli SKU in modo esplicito. In questo momento le modifiche necessarie vengono mantenute al minimo. Se uno SKU non è specificato, il valore predefinito è la versione `2017-08-01` dell'API dello SKU Basic.

>[!IMPORTANT]
>Load Balancer Standard è un nuovo prodotto Load Balancer. Si tratta in gran parte di un soprainsieme di Load Balancer Basic, ma esistono differenze importanti tra i due prodotti. Tutti gli scenari end-to-end possibili con Load Balancer Basic possono essere creati anche con Load Balancer Standard. Se si conosce già Load Balancer Basic, confrontarlo con Load Balancer Standard per comprendere le modifiche di comportamento più recenti.

[!INCLUDE [comparison table](../../includes/load-balancer-comparison-table.md)]

Per altre informazioni, vedere [Limiti di Load Balancer](https://aka.ms/lblimits). Per informazioni dettagliate su Load Balancer Standard, vedere [panoramica](load-balancer-standard-overview.md), [prezzi](https://aka.ms/lbpricing) e [contratto di servizio](https://aka.ms/lbsla).

## <a name="concepts"></a>Concetti

### <a name = "publicloadbalancer"></a>Bilanciamento del carico pubblico

Un servizio Load Balancer pubblico esegue il mapping dell'indirizzo IP pubblico e della porta del traffico in ingresso all'indirizzo IP privato e alla porta della macchina virtuale. Load Balancer esegue il mapping in senso inverso del traffico di risposta dalla macchina virtuale. Se si applicano le regole di bilanciamento del carico, è possibile distribuire tipi specifici di traffico in più macchine virtuali o servizi. È ad esempio possibile dividere il carico del traffico delle richieste Web tra più server Web.

>[!NOTE]
>È possibile implementare solo un servizio Load Balancer pubblico e un servizio Load Balancer interno per set di disponibilità.

La figura seguente mostra un endpoint con carico bilanciato per il traffico Web condiviso fra tre macchine virtuali per la porta TCP 80 pubblica. Queste tre macchine virtuali fanno parte di un set con bilanciamento del carico.

![Esempio di servizio Load Balancer pubblico](./media/load-balancer-overview/IC727496.png)

*Figura: bilanciamento del traffico Web tramite un servizio Load Balancer pubblico*

I client Internet inviano richieste di pagine Web all'indirizzo IP pubblico di un'app Web sulla porta TCP 80. Azure Load Balancer distribuisce le richieste tra le tre macchine virtuali del set con carico bilanciato. Per altre informazioni sugli algoritmi di Load Balancer, vedere [Funzionalità principali di Load Balancer](load-balancer-overview.md##fundamental-load-balancer-features).

Per impostazione predefinita, Azure Load Balancer distribuisce il traffico di rete in modo uniforme tra più istanze di macchine virtuali. È anche possibile configurare l'affinità di sessione. Per altre informazioni, vedere [Configurare la modalità di distribuzione per Azure Load Balancer](load-balancer-distribution-mode.md).

### <a name = "internalloadbalancer"></a> Bilanciamento del carico interno

Un servizio Load Balancer interno indirizza il traffico solo alle risorse interne a una rete virtuale o che usano una rete VPN per accedere all'infrastruttura di Azure, a differenza di un servizio Load Balancer pubblico. L'infrastruttura di Azure limita l'accesso agli indirizzi IP front-end con carico bilanciato di una rete virtuale. Gli indirizzi IP front-end e le reti virtuali non sono mai esposti direttamente a un endpoint di Internet. Le applicazioni line-of-business interne vengono eseguite in Azure e sono accessibili dall'interno di Azure o da risorse locali.

Il servizio Load Balancer interno consente di bilanciare i tipi di carico seguenti:

* **In una rete virtuale**: bilanciamento del carico dalle macchine virtuali presenti nella rete virtuale a un set di macchine virtuali che si trovano nella stessa rete virtuale.
* **Per una rete virtuale cross-premise**: bilanciamento del carico dai computer locali a un set di macchine virtuali che si trovano nella stessa rete virtuale.
* **Per le applicazioni multilivello**: bilanciamento del carico per le applicazioni multilivello e con connessione a Internet in cui i livelli di back-end non sono esposti a Internet. I livelli di back-end richiedono il bilanciamento del carico per il traffico dal livello con connessione a Internet. Vedere la figura successiva.
* **Per le applicazioni line-of-business**: bilanciamento del carico per le applicazioni line-of-business ospitate in Azure senza applicazioni software o componenti hardware aggiuntivi per il bilanciamento del carico. Questo scenario include server locali che si trovano nel set di computer con carico bilanciato del traffico.

![Esempio di servizio Load Balancer interno](./media/load-balancer-overview/IC744147.png)

*Figura: bilanciamento del carico di applicazioni multilivello tramite il servizio Load Balancer sia pubblico che interno*

## <a name="pricing"></a>Prezzi

Il costo dell'utilizzo del servizio Load Balancer Standard viene addebitato in base ai criteri seguenti.

* Numero di regole in uscita e di bilanciamento del carico configurate. Le regole NAT in ingresso non sono conteggiate ai fini del numero totale di regole.
* Quantità di dati elaborati in ingresso e in uscita indipendenti dalle regole.

Per informazioni sui prezzi di Load Balancer Standard, vedere [Prezzi di Load Balancer](https://azure.microsoft.com/pricing/details/load-balancer/).

Load Balancer Basic è disponibile senza alcun addebito.

## <a name="sla"></a>Contratto di servizio

Per informazioni sul contratto di servizio di Load Balancer Standard, vedere [Contratto di servizio per Load Balancer](https://aka.ms/lbsla).

## <a name="limitations"></a>Limitazioni

* Load Balancer consente di eseguire il bilanciamento del carico e il port forwarding per protocolli TCP o UDP specifici. Le regole di bilanciamento del carico e le regole NAT in ingresso supportano TCP e UDP, ma non altri protocolli IP, ad esempio ICMP.

  Load Balancer non termina, non risponde o non interagisce in altro modo con il payload di un flusso UDP o TCP perché non è un proxy. La corretta convalida della connettività a un front-end deve essere eseguita in banda con lo stesso protocollo usato in una regola NAT in ingresso o di bilanciamento del carico. Per visualizzare una risposta da un front-end, almeno una delle macchine virtuali deve generare una risposta per un client.

  La mancata ricezione di una risposta in banda da parte del front-end di Load Balancer indica che nessuna macchina virtuale è stata in grado di rispondere. Nessun elemento può interagire con un front-end del servizio Load Balancer senza una macchina virtuale in grado di rispondere. Questo principio si applica anche alle connessioni in uscita in cui il mascheramento delle porte SNAT è supportato solo per i protocolli TCP e UDP. Per tutti gli altri protocolli IP, incluso ICMP, l'esito è negativo. Assegnare un indirizzo IP pubblico a livello di istanza per mitigare questo problema. Per altre informazioni, vedere [Informazioni su SNAT e PAT](load-balancer-outbound-connections.md#snat).

* I servizi Load Balancer interni non convertono le connessioni originate in uscita nel front-end di un servizio Load Balancer interno perché si trovano nello spazio di indirizzi IP privati. I servizi Load Balancer pubblici consentono di stabilire [connessioni in uscita](load-balancer-outbound-connections.md) dagli indirizzi IP privati interni alla rete virtuale a indirizzi IP pubblici. Per i servizi Load Balancer interni, questo approccio evita un potenziale esaurimento delle porte SNAT all'interno di uno spazio di indirizzi IP interno univoco, in cui la conversione non è obbligatoria.

  Un effetto collaterale consiste nella mancata corrispondenza dei due rami del flusso qualora un flusso in uscita da una macchina virtuale nel pool back-end tenti un flusso verso il front-end del servizio Load Balancer interno nel pool _e_ venga mappato a se stesso. A causa della mancata corrispondenza, il flusso ha esito negativo. Il flusso ha esito positivo se non viene mappato alla stessa macchina virtuale nel pool back-end che ha creato il flusso verso il front-end.

  Quando il flusso viene mappato nuovamente a se stesso, il flusso in uscita sembra provenire dalla macchina virtuale verso il front-end e il flusso in ingresso corrispondente sembra provenire dalla macchina virtuale verso se stesso. Dal punto di vista del sistema operativo guest, le parti in ingresso e in uscita dello stesso flusso non corrispondono all'interno della macchina virtuale. Lo stack TCP non riconosce queste metà come appartenenti allo stesso flusso, sebbene lo siano. L'origine e la destinazione non corrispondono. Quando il flusso viene mappato a qualsiasi altra macchina virtuale nel pool di back-end, le metà del flusso corrispondono e la macchina virtuale può rispondere al flusso stesso.

  Il sintomo di questo scenario è la presenza di timeout di connessione intermittenti quando il flusso torna nel back-end stesso che lo originato. Soluzioni alternative comuni includono l'inserimento di un livello proxy dietro il servizio Load Balancer interno e l'uso delle regole di stile DSR (Direct Server Return). Per altre informazioni, vedere [Più front-end per Azure Load Balancer](load-balancer-multivip-overview.md).

  È possibile combinare un servizio Load Balancer interno con qualsiasi proxy di terze parti o usare un [gateway applicazione](../application-gateway/application-gateway-introduction.md) per scenari proxy con HTTP/HTTPS. Sebbene sia possibile usare un servizio Load Balancer pubblico per attenuare questo problema, lo scenario risultante è soggetto a [esaurimento SNAT](load-balancer-outbound-connections.md#snat). Evitare questo secondo approccio, a meno che non sia gestito accuratamente.

* In generale, l'inoltro di frammenti IP non è supportato nelle regole di bilanciamento del carico né lo è frammentazione IP dei pacchetti UDP e TCP. È possibile usare le regole di bilanciamento del carico per le porte a disponibilità elevata per l'invio di frammenti IP esistenti. Per altre informazioni, vedere [Panoramica delle porte a disponibilità elevata](load-balancer-ha-ports-overview.md).

## <a name="next-steps"></a>Passaggi successivi

Vedere [Creare un Load Balancer Basic](quickstart-create-basic-load-balancer-portal.md) per iniziare a usare un servizio Load Balancer, crearne uno, creare macchine virtuali con un'estensione IIS personalizzata installata e bilanciare il carico dell'app Web tra le macchine virtuali.
