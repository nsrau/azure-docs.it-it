---
title: Uso dei servizi di bilanciamento del carico nel cloud di Azure | Documentazione Microsoft
description: 'Questa esercitazione illustra come creare uno scenario di uso del portafoglio di soluzioni Azure per il bilanciamento del carico: Gestione traffico, il gateway applicazione e Load Balancer'
services: traffic-manager
documentationcenter: 
author: liumichelle
manager: vitinnan
editor: 
ms.assetid: f89be3be-a16f-4d47-bcae-db2ab72ade17
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/27/2016
ms.author: limichel
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: 90918f6bb23f0f4e3c009506e1b12c028c45f02b

---

# <a name="using-load-balancing-services-in-the-azure-cloud"></a>Uso dei servizi di bilanciamento del carico nel cloud di Azure

## <a name="introduction"></a>Introduzione

Microsoft Azure offre numerosi servizi che consentono di gestire la distribuzione del traffico e il bilanciamento del carico. È possibile usare questi servizi singolarmente o combinarne i metodi in base alle esigenze specifiche, così da realizzare una soluzione ottimale.

In questa esercitazione viene innanzitutto illustrato un caso d'uso di un cliente e viene mostrato in che modo è possibile assicurare maggiore affidabilità e prestazioni più elevate mediante il portafoglio di soluzioni Azure per il bilanciamento del carico: Gestione traffico, il gateway applicazione e Load Balancer. Vengono quindi fornite istruzioni dettagliate per la creazione di una distribuzione che sia geograficamente ridondante, distribuisca il traffico alle macchine virtuali e consenta di gestire differenti tipi di richieste.

A livello concettuale, ognuno di questi servizi svolge un ruolo distinto nella gerarchia di bilanciamento del carico.

1. **Gestione traffico** offre il bilanciamento del carico DNS globale.  Esamina le richieste DNS in ingresso e risponde con un endpoint integro e in base ai criteri di routing selezionati dal cliente. Le opzioni per i metodi di routing sono le seguenti: routing delle prestazioni, per inviare il richiedente all'endpoint più vicino in termini di latenza; routing prioritario, per indirizzare tutto il traffico a un endpoint con altri endpoint come backup; routing round robin ponderato, che distribuisce il traffico in base al peso assegnato a ciascun endpoint. Il client si connette direttamente a tale endpoint. Gestione traffico di Azure rileva gli endpoint non integri e reindirizza i client a un'altra istanza integra. Per altre informazioni sul servizio, vedere [Gestione traffico di Azure](traffic-manager-overview.md).
2. Il **gateway applicazione** offre un servizio di controller per la distribuzione di applicazioni con numerose funzionalità di bilanciamento del carico di livello 7. Consente ai clienti di ottimizzare la produttività delle Web farm tramite l'offload della terminazione SSL con utilizzo elevato di CPU al gateway applicazione. Altre funzionalità di routing di livello 7 includono la distribuzione round robin del traffico in ingresso, l'affinità di sessione basata su cookie, il routing basato su percorso URL e la possibilità di ospitare più siti Web dietro un unico gateway applicazione. Il gateway applicazione può essere configurato come gateway con connessione Internet, come gateway solo interno o come una combinazione di queste due opzioni. È completamente gestito in Azure e offre scalabilità e disponibilità elevata, oltre a un set completo di funzionalità di registrazione e diagnostica che ne migliorano la gestibilità.
3. **Load Balancer** è parte integrante dello stack SDN di Azure. Load Balancer offre servizi di bilanciamento del carico di livello 4 a elevate prestazioni e bassa latenza per tutti i protocolli UDP e TCP.  Gestisce le connessioni in ingresso e in uscita.  È possibile configurare endpoint pubblici e interni con carico bilanciato e definire regole per mappare le connessioni in ingresso a destinazioni pool back-end con opzioni di probe dell'integrità TCP e HTTP per gestire la disponibilità del servizio.

## <a name="scenario"></a>Scenario

In questo scenario di esempio viene usato un semplice sito Web che gestisce due tipi di contenuto: immagini e pagine Web con rendering dinamico. Questo sito Web deve essere geograficamente ridondante e deve servire gli utenti dalla località ad essi più vicina (latenza più bassa). Lo sviluppatore dell'applicazione ha deciso che qualsiasi URL corrispondente al pattern /images/* venga servito da un pool dedicato di macchine virtuali separato dal resto della Web farm.

Inoltre, il pool predefinito di macchine virtuali che rende disponibile il contenuto dinamico deve comunicare con un database back-end ospitato in un cluster a disponibilità elevata. Il provisioning dell'intera distribuzione viene eseguito tramite Azure Resource Manager.

L'uso di Gestione traffico, del gateway applicazione e di Load Balancer consente al sito Web di raggiungere gli obiettivi di progettazione seguenti:

1. **Ridondanza multi-geografica**: grazie all'uso di Gestione traffico, se un'area geografica risulta non disponibile, il traffico viene indirizzato senza problemi all'area geografica appropriata più vicina senza alcun intervento da parte del proprietario dell'applicazione.
2. **Latenza ridotta**: il cliente viene automaticamente indirizzato da Gestione traffico all'area geografica più vicina. Per questo motivo, quando il cliente richiede il contenuto delle pagine Web, si registra una latenza più bassa.
3. **Scalabilità indipendente**: poiché il carico di lavoro delle applicazioni Web è separato in base al tipo di contenuto, il proprietario dell'applicazione può ridimensionare i carichi di lavoro relativi alle richieste in modo indipendente l'uno dall'altro. Il gateway applicazione assicura che il traffico venga indirizzato ai pool corretti in base alle regole specificate e all'integrità dell'applicazione.
4. **Bilanciamento del carico interno**: con il posizionamento di Load Balancer davanti al cluster a disponibilità elevata, viene esposto all'applicazione solo il rispettivo endpoint attivo e integro per un database.  Inoltre, un amministratore del database può ottimizzare il carico di lavoro distribuendo le repliche attive e passive nel cluster indipendentemente dall'applicazione front-end.  Load Balancer fornisce le connessioni al cluster a disponibilità elevata e assicura che soltanto i database integri ricevano le richieste di connessione.

Il diagramma seguente illustra l'architettura di questi scenario:

![immagine del diagramma dello scenario](./media/traffic-manager-load-balancing-azure/scenario-diagram.png)

> [!NOTE]
> Questo esempio è solo una delle numerose configurazioni possibili dei servizi di bilanciamento del carico offerti da Azure. È possibile combinare e associare Gestione traffico, il gateway applicazione e Load Balancer di Azure per soddisfare al meglio le specifiche esigenze di bilanciamento del carico. Se, ad esempio, l'offload SSL o l'elaborazione di livello 7 non sono necessari, è possibile usare Load Balancer al posto del gateway applicazione.

## <a name="setting-up-the-load-balancing-stack"></a>Impostazione dello stack di bilanciamento del carico

### <a name="step-1-create-a-traffic-manager-profile"></a>Passaggio 1: creare un profilo di Gestione traffico

1. Passare al portale di Azure, fare clic su **Nuovo**, e cercare "Profilo di Gestione traffico" in Marketplace
2. Nel pannello "Crea profilo di Gestione traffico" immettere le informazioni di base per la creazione di un profilo di Gestione traffico:

   * **Nome**: assegnare al profilo di Gestione traffico un nome del prefisso DNS
   * **Metodo di routing**: selezionare il criterio del metodo di routing del traffico. Per altre informazioni, vedere [Metodi di routing del traffico di Gestione traffico](traffic-manager-routing-methods.md)
   * **Sottoscrizione**: la sottoscrizione contenente il profilo
   * **Gruppo di risorse**: il gruppo di risorse contenente il profilo di Gestione traffico. Può trattarsi di un gruppo di risorse nuovo o già esistente
   * **Località del gruppo di risorse**: il servizio Gestione traffico è globale e non è associato a una località. È tuttavia necessario specificare un'area geografica per il gruppo in cui si trovano i metadati associati al profilo di Gestione traffico. La località non ha alcun impatto sulla disponibilità di runtime del profilo.

3. Fare clic su **Crea** per generare il profilo di Gestione traffico

    ![pannello crea profilo di gestione traffico](./media/traffic-manager-load-balancing-azure/s1-create-tm-blade.png)

### <a name="step-2-create-the-application-gateways"></a>Passaggio 2: creare il gateway applicazione

1. Passare al portale di Azure e, nel menu a sinistra, fare clic su **Nuovo** > **Rete** > **Gateway applicazione**
2. Inserire quindi le informazioni di base sul gateway applicazione. Al termine fare clic su OK. Per le impostazioni di base sono necessarie le informazioni seguenti.

   * **Nome** : nome del gateway applicazione.
   * **Dimensioni SKU**: la dimensione del gateway applicazione. Le opzioni disponibili sono Small, Medium e Large.
   * **Numero di istanze**: il numero di istanze. Questo valore deve essere un numero compreso tra 2 e 10.
   * **Gruppo di risorse**: il gruppo di risorse in cui includere il gateway applicazione. Può essere un gruppo di risorse esistente o nuovo.
   * **Località**: l'area del gateway applicazione. È la stessa località del gruppo di risorse. La località è importante perché la rete virtuale e l'IP pubblico devono trovarsi nella stessa località del gateway.

3. Definire quindi le configurazioni della rete virtuale, della subnet, dell'IP front-end e del listener per il gateway applicazione. In questo scenario l'indirizzo IP front-end è **Pubblico**. Questo consente, in un secondo tempo, di aggiungerlo come endpoint al profilo di Gestione traffico.
4. La successiva impostazione del gateway applicazione da definire è la configurazione del listener. Se si usa HTTP, non è necessario configurare altro e si può fare clic su **OK**. Per usare HTTPS sono necessarie operazioni di configurazione aggiuntive. Per informazioni, vedere [Creare un gateway applicazione con il portale](../application-gateway/application-gateway-create-gateway-portal.md), a partire dal passaggio 9.

#### <a name="configure-url-routing-for-application-gateways"></a>Configurare il routing degli URL per i gateway applicazione

Per la scelta del pool back-end, un gateway applicazione configurato con una regola basata sul percorso tiene conto non solo della distribuzione round robin ma anche del modello di percorso dell'URL della richiesta. In questo scenario viene aggiunta una regola basata sul percorso per indirizzare qualsiasi URL con "/images/\*" al pool di server di immagine. Per altri dettagli sulla configurazione del routing degli URL in base al percorso per un gateway applicazione, vedere [Create a Path-based rule for an Application Gateway](../application-gateway/application-gateway-create-url-route-portal.md) (Creare una regola basata sul percorso per un gateway applicazione).

![diagramma del livello Web](./media/traffic-manager-load-balancing-azure/web-tier-diagram.png)

1. Dal gruppo di risorse passare all'istanza del gateway applicazione creata nei passaggi precedenti.
2. Nella sezione Impostazioni selezionare **Pool back-end**, quindi selezionare Aggiungi per aggiungere le macchine virtuali da associare ai pool back-end di livello Web.
3. Nel pannello "Aggiungi pool back-end" immettere il nome del pool back-end e tutti gli indirizzi IP dei computer presenti nel pool. In questo scenario vengono connessi due pool back-end di server di macchine virtuali.

    ![gateway applicazione - aggiungi pool back-end](./media/traffic-manager-load-balancing-azure/s2-appgw-add-bepool.png)

4. Nella sezione Impostazioni del gateway applicazione selezionare **Regole** e quindi fare clic sul pulsante **Basata sul percorso** per aggiungere una nuova regola.

    ![gateway applicazione - aggiungi regola basata sul percorso](./media/traffic-manager-load-balancing-azure/s2-appgw-add-pathrule.png)

5. Nel pannello "Aggiungi regola basata sul percorso" immettere le informazioni seguenti per configurare la regola:

   * Impostazioni di base:
     + **Nome**: nome descrittivo della regola accessibile nel portale
     + **Listener**: listener usato per la regola
     + **Pool back-end predefinito**: pool back-end da usare per la regola predefinita
     + **Impostazioni HTTP predefinite**: impostazioni HTTP da usare per la regola predefinita
   * Regole basate sul percorso:
     + **Nome**: nome descrittivo della regola basata sul percorso
     + **Percorsi**: regola basata sul percorso usata per l'inoltro del traffico.
     + **Pool back-end**: pool back-end da usare con questa regola
     + **Impostazione HTTP**: impostazioni HTTP da usare con questa regola

     > [!IMPORTANT]
     > Percorsi: i percorsi validi devono iniziare con "/". Il carattere jolly "\*" è consentito solo alla fine del percorso. Alcuni esempi validi: /xyz, /xyz\* o /xyz/\*

     ![gateway applicazione - pannello aggiungi regola basata sul percorso](./media/traffic-manager-load-balancing-azure/s2-appgw-pathrule-blade.png)

### <a name="step-3-add-application-gateways-to-the-traffic-manager-endpoints"></a>Passaggio 3: aggiungere gateway applicazione agli endpoint di Gestione traffico

In questo scenario Gestione traffico è connesso a istanze del gateway applicazione (come configurato nei passaggi precedenti) presenti in aree geografiche diverse. Dopo aver configurato i gateway applicazione, il passaggio successivo consiste nel connettere tali gateway al profilo di Gestione traffico.

1. Passare all'istanza del profilo di Gestione traffico. A questo scopo, è possibile cercare all'interno del gruppo di risorse o cercare il nome del profilo di Gestione traffico da "All Resources" (Tutte le risorse).
2. Da questo pannello selezionare **Endpoint** e quindi selezionare **Aggiungi** per aggiungere un nuovo endpoint.

    ![gestione traffico - aggiungi endpoint](./media/traffic-manager-load-balancing-azure/s3-tm-add-endpoint.png)

3. Nel pannello "Aggiungi endpoint" immettere le informazioni per la creazione di un nuovo endpoint

   * **Tipo**: tipo di endpoint di cui bilanciare il carico. In questo scenario, dal momento che si connette l'endpont alle istanze del gateway applicazione configurate in precedenza, si tratta di un endpoint di Azure
   * **Nome**: nome dell'endpoint
   * **Tipo di risorsa di destinazione**: selezionare Indirizzo IP pubblico, quindi, nell'impostazione "Risorsa di destinazione" riportata sotto, selezionare l'IP pubblico del gateway applicazione configurato in precedenza

     ![gestione traffico - aggiungi endpoint](./media/traffic-manager-load-balancing-azure/s3-tm-add-endpoint-blade.png)

4. Da questa posizione è possibile testare la configurazione accedendovi con il DNS del profilo di Gestione traffico, in questo esempio TrafficManagerScenario.trafficmanager.net. È possibile inviare nuovamente le richieste, attivare/disattivare le macchine virtuali e/o i server Web creati in aree geografiche differenti e modificare le impostazioni del profilo di Gestione traffico per testare la configurazione.

### <a name="step-4-create-the-load-balancer"></a>Passaggio 4: creare il servizio di bilanciamento del carico

In questo scenario Load Balancer distribuisce le connessioni dal livello Web al database all'interno di un cluster a disponibilità elevata.

Se il cluster di database a disponibilità elevata usa SQL AlwaysOn, per istruzioni dettagliate vedere [Configurare uno o più listener nei gruppi di disponibilità AlwaysOn - Resource Manger](../virtual-machines/virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Per altri dettagli sulla configurazione di un servizio di bilanciamento del carico interno, vedere [Creare un servizio di bilanciamento del carico interno nel portale di Azure](../load-balancer/load-balancer-get-started-ilb-arm-portal.md)

1. Passare al portale di Azure e, nel menu a sinistra, fare clic su **Nuovo** > **Rete** > **Servizio di bilanciamento del carico**
2. Nel pannello "Crea servizio di bilanciamento del carico" scegliere un nome per il servizio di bilanciamento del carico
3. Impostare **Tipo** su Interno e scegliere la rete virtuale e la subnet appropriate per il servizio di bilanciamento del carico
4. In **Assegnazione indirizzo IP** selezionare Dinamico o Statico
5. Quindi, in **Gruppo di risorse**, scegliere il gruppo di risorse per il servizio di bilanciamento del carico
6. In **Località** scegliere l'area geografica appropriata per il servizio di bilanciamento del carico
7. Al termine, fare clic su **Crea** per generare il servizio di bilanciamento del carico

#### <a name="connect-backend-database-tier-to-load-balancer"></a>Connettere il livello del database back-end al servizio di bilanciamento del carico

1. Dal gruppo di risorse individuare il servizio di bilanciamento del carico creato nei passaggi precedenti.
2. Nella sezione Impostazioni fare clic su **Pool back-end** e quindi fare clic su **Aggiungi** per aggiungere un nuovo pool back-end
3. Nel pannello "Aggiungi pool back-end" immettere il nome del pool back-end
4. Da questa posizione è possibile aggiungere al pool back-end o singoli computer o un set di disponibilità.

   ![servizio di bilanciamento del carico - aggiungi pool beck-end](./media/traffic-manager-load-balancing-azure/s4-ilb-add-bepool.png)

#### <a name="configure-a-probe"></a>Configurare un probe

1. Nella sezione Impostazioni del servizio di bilanciamento del carico selezionare **Probe** e quindi selezionare **Aggiungi** per aggiungere un nuovo probe
2. Nel pannello "Aggiungi probe" immettere il **Nome** del probe
3. Selezionare il **Protocollo** per il probe. Per un database, può essere preferibile un probe TCP anziché un probe HTTP.   Per altre informazioni sui probe del servizio di bilanciamento del carico, vedere [Probe del servizio di bilanciamento del carico](../load-balancer/load-balancer-custom-probe-overview.md).
4. Immettere quindi la **Porta** del database da utilizzare per l'accesso al probe.
5. In **Intervallo** specificare la frequenza di probe per l'applicazione
6. In **Soglia di non integrità** specificare il numero di tentativi continui di probe non riusciti che possono verificarsi prima che una macchina virtuale back-end venga considerata non integra.
7. Fare clic su **OK** per creare il probe

   ![probe del servizio di bilanciamento del carico](./media/traffic-manager-load-balancing-azure/s4-ilb-add-probe.png)

#### <a name="configure-load-balancing-rules"></a>Configurare le regole del servizio di bilanciamento del carico

1. Nella sezione Impostazioni del servizio di bilanciamento del carico selezionare **Regole di bilanciamento del carico** e quindi selezionare **Aggiungi** per creare una regola
2. Nel pannello "Aggiungi regola di bilanciamento del carico" immettere il **Nome** della regola di bilanciamento del carico
3. Impostare le opzioni **Frontend IP Address of the load balancer** (Indirizzo IP front-end del servizio di bilanciamento del carico), **Protocollo** e **Porta**
4. In **Porta back-end** specificare la porta da usare nel pool di back-end
5. Selezionare il **Pool back-end** e il **Probe** creati nei passaggi precedenti per applicarvi la regole
6. In **Salvataggio permanente sessione** scegliere la modalità di salvataggio delle sessioni
7. In **Timeout di inattività** specificare il numero di minuti prima di un timeout di inattività
8. Per **Indirizzo IP mobile** selezionare Disabilitato o Abilitato
9. Fare clic su **OK** per creare la regola

### <a name="step-5-connect-web-tier-vms-to-load-balancer"></a>Passaggio 5: connettere macchine virtuali di livello Web al servizio di bilanciamento del carico

Vengono ora configurati l'indirizzo IP e la porta font-end del servizio di bilanciamento del carico nell'applicazione in esecuzione sulle macchine virtuali di livello Web per qualsiasi connessione di database. Questa configurazione è specifica dell'applicazione in esecuzione su tali macchine virtuali. Per dettagli sulla configurazione dell'indirizzo IP e della porta di destinazione, fare riferimento alla documentazione dell'applicazione. Per individuare l'indirizzo IP del front-end, passare a Pool di indirizzi IP front-end nel pannello delle impostazioni del servizio di bilanciamento del carico nel portale di Azure.

![servizio di bilanciamento del carico - pool di indirizzi IP front-end](./media/traffic-manager-load-balancing-azure/s5-ilb-frontend-ippool.png)

## <a name="next-steps"></a>Passaggi successivi

* [Gestione traffico di Azure](traffic-manager-overview.md)
* [Panoramica del gateway applicazione](../application-gateway/application-gateway-introduction.md)
* [Panoramica di Azure Load Balancer](../load-balancer/load-balancer-overview.md)



<!--HONumber=Nov16_HO3-->


