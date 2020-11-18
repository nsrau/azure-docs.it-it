---
title: Risolvere i problemi di Azure Load Balancer
description: Informazioni su come risolvere i problemi noti relativi ad Azure Load Balancer.
services: load-balancer
documentationcenter: na
author: asudbring
manager: dcscontentpm
ms.custom: seodoc18
ms.service: load-balancer
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/28/2020
ms.author: allensu
ms.openlocfilehash: a1a8df6d503ec5f5bf9c1e739e5ecf6486a85776
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94697421"
---
# <a name="troubleshoot-azure-load-balancer"></a>Risolvere i problemi di Azure Load Balancer

Questa pagina fornisce informazioni sulla risoluzione dei problemi comuni di Azure Load Balancer Basic e Standard. Per altre informazioni su Load Balancer Standard, vedere la [panoramica di Load Balancer Standard](load-balancer-standard-diagnostics.md).

Quando la connettività di Load Balancer non è disponibile, i sintomi più comuni sono i seguenti: 

- Le macchine virtuali controllate da Load Balancer non rispondono ai probe di integrità 
- Le macchine virtuali dietro Load Balancer non rispondono al traffico nella porta configurata

Quando i client esterni alle macchine virtuali back-end usano il bilanciamento del carico, per la comunicazione verrà usato l'indirizzo IP dei client. Assicurarsi che l'indirizzo IP dei client venga aggiunto all'elenco degli indirizzi consentiti del gruppo di sicurezza di rete. 

## <a name="symptom-no-outbound-connectivity-from-standard-internal-load-balancers-ilb"></a>Sintomo: nessuna connettività in uscita dal servizio di bilanciamento del carico interno standard (ILB)

**Convalida e la risoluzione**

Le gli ILB standard sono **sicure per impostazione predefinita**. Gli ILB di base hanno consentito la connessione a Internet tramite un indirizzo IP pubblico *nascosto* . Questo non è recommened per i carichi di lavoro di produzione perché l'indirizzo IP non è né statico né bloccato tramite gruppi di cui si è proprietari. Se è stato recentemente spostato da un ILB di base a un ILB standard, è necessario creare un indirizzo IP pubblico in modo esplicito tramite la configurazione [solo in uscita](egress-only.md) che blocca l'IP tramite gruppi. È anche possibile usare un [gateway NAT](../virtual-network/nat-overview.md) nella subnet.

## <a name="symptom-vms-behind-the-load-balancer-are-not-responding-to-health-probes"></a>Sintomo: Le macchine virtuali controllate da Load Balancer non rispondono ai probe di integrità
Per partecipare al set di bilanciamento del carico, i server back-end devono superare il controllo del probe. Per altre informazioni sui probe di integrità, vedere [Informazioni sui probe di bilanciamento del carico](load-balancer-custom-probe-overview.md). 

Le macchine virtuali del pool back-end di Load Balancer possono non rispondere ai probe per i motivi seguenti: 
- La macchina virtuale del pool back-end di Load Balancer non è integra 
- La macchina virtuale del pool back-end di Load Balancer non è in ascolto nella porta probe 
- Un firewall o un gruppo di sicurezza di rete blocca la porta nelle macchine virtuali del pool back-end di Load Balancer 
- Altri errori di configurazione in Load Balancer

### <a name="cause-1-load-balancer-backend-pool-vm-is-unhealthy"></a>Causa 1: La macchina virtuale del pool back-end di Load Balancer non è integra 

**Convalida e la risoluzione**

Per risolvere questo problema, accedere alle macchine virtuali partecipanti e verificare se la macchina virtuale è integra e può rispondere a richieste **PsPing** o **TCPing** inviate da un'altra macchina virtuale nel pool. Se la macchina virtuale non è integra o non riesce a rispondere al probe, è necessario risolvere il problema e ripristinare l'integrità della macchina virtuale prima che questa possa partecipare al bilanciamento del carico.

### <a name="cause-2-load-balancer-backend-pool-vm-is-not-listening-on-the-probe-port"></a>Causa 2: La macchina virtuale del pool back-end di Load Balancer non è in ascolto nella porta probe
Se la macchina virtuale è integra, ma non risponde al probe, è possibile che la porta probe non sia aperta nella macchina virtuale partecipante o che la macchina virtuale non sia in ascolto su tale porta.

**Convalida e la risoluzione**

1. Accedere alla macchina virtuale back-end. 
2. Aprire un prompt dei comandi ed eseguire questo comando per verificare che un'applicazione sia in ascolto nella porta probe:   
            netstat -an
3. Se lo stato della porta non è elencato come **LISTENING**, configurare la porta corretta. 
4. In alternativa, selezionare un'altra porta che sia elencata come **LISTENING** e aggiornare la configurazione di Load Balancer di conseguenza.              

### <a name="cause-3-firewall-or-a-network-security-group-is-blocking-the-port-on-the-load-balancer-backend-pool-vms"></a>Causa 3: Un firewall o un gruppo di sicurezza di rete blocca la porta nelle macchine virtuali del pool back-end di Load Balancer  
Se il firewall nella macchina virtuale blocca la porta probe oppure uno o più gruppi di sicurezza di rete configurati nella subnet o nella macchina virtuale non consentono al probe di raggiungere la porta, la macchina virtuale non può rispondere al probe di integrità.          

**Convalida e la risoluzione**

* Se il firewall è abilitato, verificare se è configurato per consentire la porta probe. In caso contrario, configurare il firewall per consentire il traffico nella porta probe e riprovare. 
* Dall'elenco dei gruppi di sicurezza di rete, verificare se il traffico in ingresso o in uscita nella porta probe ha interferenze. 
* Verificare anche se nella scheda di interfaccia di rete della macchina virtuale o nella subnet è presente una regola di tipo **Nega tutto** di un gruppo di sicurezza di rete avente una priorità superiore rispetto alla regola predefinita che consente il traffico e i probe di Load Balancer. I gruppi di sicurezza di rete devono consentire l'IP 168.63.129.16 di Load Balancer. 
* Se alcune di queste regole bloccano il traffico probe, rimuoverle e riconfigurarle per consentire il traffico probe.  
* Verificare se la macchina virtuale ha ora iniziato a rispondere ai probe di integrità. 

### <a name="cause-4-other-misconfigurations-in-load-balancer"></a>Causa 4: Altri errori di configurazione in Load Balancer
Se tutte le cause precedenti sembrano essere state verificate e risolte correttamente, ma la macchina virtuale back-end continua a non rispondere al probe di integrità, testare manualmente la connettività e raccogliere alcune tracce per ottenere informazioni sulla connettività stessa.

**Convalida e la risoluzione**

* Usare **Psping** da una delle altre macchine virtuali all'interno della rete virtuale per testare la risposta della porta probe, ad esempio \psping.exe -t 10.0.0.4:3389, e registrare i risultati. 
* Usare **TCPing** da una delle altre macchine virtuali all'interno della rete virtuale per testare la risposta della porta probe (esempio: \tcping.exe 10.0.0.4 3389) e registrare i risultati. 
* Se non si ricevono risposte con questi test di ping:
    - Eseguire una traccia Netsh simultanea nella macchina virtuale del pool back-end e un'altra macchina virtuale di test dalla stessa rete virtuale. Eseguire ora un test PsPing per un certo periodo, raccogliere alcune tracce di rete e quindi interrompere il test. 
    - Analizzare l'acquisizione di rete e verificare se sono disponibili pacchetti in ingresso e in uscita correlati alla query di ping. 
        - Se non si osservano pacchetti in ingresso nella macchina virtuale del pool back-end, è possibile che un gruppo di sicurezza di rete o una route definita dall'utente configurata in modo errato blocchi il traffico. 
        - Se non si osservano pacchetti in uscita nella macchina virtuale del pool back-end, è necessario identificare eventuali problemi non correlati nella macchina virtuale, ad esempio la presenza di un'applicazione che blocca la porta probe. 
    - Verificare se i pacchetti probe vengono forzati verso un'altra destinazione, magari tramite impostazioni delle route definite dall'utente, prima di raggiungere il servizio di bilanciamento del carico. Ciò può impedire costantemente al traffico di raggiungere la macchina virtuale back-end. 
* Modificare il tipo di probe, ad esempio da HTTP a TCP, e configurare la porta corrispondente negli ACL dei gruppi di sicurezza di rete e nel firewall per verificare se il problema è la configurazione della risposta probe. Per altre informazioni sulla configurazione del probe di integrità, vedere la [configurazione del probe di integrità di bilanciamento del carico con endpoint](/archive/blogs/mast/endpoint-load-balancing-heath-probe-configuration-details).

## <a name="symptom-vms-behind-load-balancer-are-not-responding-to-traffic-on-the-configured-data-port"></a>Sintomo: le macchine virtuali dietro Load Balancer non rispondono al traffico sulla porta dati configurata

Se una macchina virtuale del pool back-end è elencata come integra e risponde ai probe di integrità, ma comunque non partecipa al bilanciamento del carico o non risponde al traffico dati, i motivi potrebbero essere i seguenti: 
* La macchina virtuale del pool back-end di Load Balancer non è in ascolto nella porta dati 
* Un gruppo di sicurezza di rete blocca la porta nella macchina virtuale del pool back-end di Load Balancer  
* Accesso a Load Balancer dalla stessa macchina virtuale e interfaccia di rete 
* Accesso al front-end del servizio di bilanciamento del carico Internet dalla macchina virtuale del pool back-end di Load Balancer 

### <a name="cause-1-load-balancer-backend-pool-vm-is-not-listening-on-the-data-port"></a>Causa 1: la macchina virtuale del pool back-end di Load Balancer non è in ascolto sulla porta dati 
Se una macchina virtuale non risponde al traffico dati, è possibile che la porta di destinazione non sia aperta nella macchina virtuale partecipante oppure che la macchina virtuale non sia in ascolto nella porta. 

**Convalida e la risoluzione**

1. Accedere alla macchina virtuale back-end. 
2. Aprire un prompt dei comandi ed eseguire questo comando per verificare che un'applicazione sia in ascolto nella porta dati:  
            netstat -an 
3. Se lo stato della porta non è elencato come "LISTENING", configurare la porta di attesa corretta. 
4. Se la porta è contrassegnata come LISTENING, verificare se sono presenti problemi nell'applicazione di destinazione in ascolto su tale porta.

### <a name="cause-2-network-security-group-is-blocking-the-port-on-the-load-balancer-backend-pool-vm"></a>Causa 2: Un gruppo di sicurezza di rete blocca la porta nella macchina virtuale del pool back-end di Load Balancer  

Se uno o più gruppi di sicurezza di rete configurati nella subnet o nella macchina virtuale bloccano l'indirizzo IP o la porta di origine, la macchina virtuale non potrà rispondere.

Con il bilanciamento del carico pubblico, per la comunicazione tra i client e le macchine virtuali back-end del bilanciamento del carico verrà usato l'indirizzo IP dei client Internet. Assicurarsi che l'indirizzo IP dei client sia consentito nel gruppo di sicurezza di rete della macchina virtuale back-end.

1. Elencare i gruppi di sicurezza di rete configurati nella macchina virtuale back-end. Per altre informazioni, vedere [Gestire i gruppi di sicurezza di rete](../virtual-network/manage-network-security-group.md).
1. Dall'elenco dei gruppi di sicurezza di rete verificare se:
    - il traffico in ingresso o in uscita nella porta dati ha interferenze. 
    - nella scheda di interfaccia di rete della macchina virtuale o nella subnet è presente una regola di tipo **Nega tutto** di un gruppo di sicurezza di rete avente una priorità superiore rispetto alla regola predefinita che consente il traffico e i probe di Load Balancer. I gruppi di sicurezza di rete devono consentire l'IP 168.63.129.16 di Load Balancer, ovvero la porta probe
1. Se alcune di queste regole bloccano il traffico, rimuoverle e riconfigurarle per consentire il traffico dati.  
1. Verificare se la macchina virtuale ha ora iniziato a rispondere ai probe di integrità.

### <a name="cause-3-accessing-the-load-balancer-from-the-same-vm-and-network-interface"></a>Causa 3: accesso a Load Balancer dalla stessa macchina virtuale e interfaccia di rete 

Se l'applicazione ospitata nella macchina virtuale back-end di un servizio di bilanciamento del carico sta provando ad accedere a un'altra applicazione ospitata nella stessa macchina virtuale back-end tramite la stessa interfaccia di rete, questo scenario non è supportato e avrà esito negativo. 

**Risoluzione**: è possibile risolvere questo problema con uno dei metodi seguenti:
* Configurare macchine virtuali del pool back-end separate per ogni applicazione. 
* Configurare l'applicazione in macchine virtuali con due schede di interfaccia di rete in modo che ogni applicazione usi un'interfaccia di rete e un indirizzo IP propri. 

### <a name="cause-4-accessing-the-internal-load-balancer-frontend-from-the-participating-load-balancer-backend-pool-vm"></a>Causa 4: accesso al front-end di Load Balancer interno dalla macchina virtuale del pool back-end di Load Balancer

Se è configurato un servizio di bilanciamento del carico interno in una rete virtuale e una delle macchine virtuali del back-end sta tentando di accedere al front-end del servizio di bilanciamento del carico interno, potrebbero verificarsi errori durante il mapping del flusso alla macchina virtuale di origine. Questo scenario non è supportato.

**Risoluzione** È possibile sbloccare questo scenario in diversi modi, incluso l'uso di un proxy. Valutare un gateway applicazione o altri proxy di terze parti, ad esempio nginx o haproxy. Per altre informazioni sul gateway applicazione, vedere [Panoramica del gateway applicazione](../application-gateway/overview.md)

**Dettagli** I servizi di bilanciamento del carico interni non convertono le connessioni originate in uscita nel front-end di un servizio di bilanciamento del carico interno perché entrambi risiedono in uno spazio indirizzi IP privati. I servizi Load Balancer pubblici consentono di stabilire [connessioni in uscita](load-balancer-outbound-connections.md) dagli indirizzi IP privati interni alla rete virtuale a indirizzi IP pubblici. Per i servizi Load Balancer interni, questo approccio evita un potenziale esaurimento delle porte SNAT all'interno di uno spazio di indirizzi IP interno univoco, in cui la conversione non è obbligatoria.

Un effetto collaterale consiste nella mancata corrispondenza dei due rami del flusso qualora un flusso in uscita da una macchina virtuale nel pool back-end tenti un flusso verso il front-end del servizio Load Balancer interno nel pool _e_ venga mappato a se stesso. A causa della mancata corrispondenza, il flusso ha esito negativo. Il flusso ha esito positivo se non viene mappato alla stessa macchina virtuale nel pool back-end che ha creato il flusso verso il front-end.

Quando il flusso viene mappato nuovamente a se stesso, il flusso in uscita sembra provenire dalla macchina virtuale verso il front-end e il flusso in ingresso corrispondente sembra provenire dalla macchina virtuale verso se stesso. Dal punto di vista del sistema operativo guest, le parti in ingresso e in uscita dello stesso flusso non corrispondono all'interno della macchina virtuale. Lo stack TCP non riconosce queste metà come appartenenti allo stesso flusso, sebbene lo siano. L'origine e la destinazione non corrispondono. Quando il flusso viene mappato a qualsiasi altra macchina virtuale nel pool di back-end, le metà del flusso corrispondono e la macchina virtuale può rispondere al flusso stesso.

Il sintomo di questo scenario è il verificarsi di timeout di connessione intermittenti quando il flusso torna nello stesso back-end che lo ha originato. Soluzioni alternative comuni includono l'inserimento di un livello proxy dietro il servizio Load Balancer interno e l'uso delle regole di stile DSR (Direct Server Return). Per altre informazioni, vedere [Più front-end per Azure Load Balancer](load-balancer-multivip-overview.md).

È possibile combinare un servizio Load Balancer interno con qualsiasi proxy di terze parti o usare un [gateway applicazione](../application-gateway/overview.md) per scenari proxy con HTTP/HTTPS. Sebbene sia possibile usare un servizio Load Balancer pubblico per attenuare questo problema, lo scenario risultante è soggetto a [esaurimento SNAT](load-balancer-outbound-connections.md). Evitare questo secondo approccio, a meno che non sia gestito accuratamente.

## <a name="symptom-cannot-change-backend-port-for-existing-lb-rule-of-a-load-balancer-which-has-vm-scale-set-deployed-in-the-backend-pool"></a>Sintomo: non è possibile modificare la porta back-end per la regola di bilanciamento del carico esistente di un servizio di bilanciamento del carico con set di scalabilità di macchine virtuali distribuiti nel pool back-end. 
### <a name="cause--the-backend-port-cannot-be-modified-for-a-load-balancing-rule-thats-used-by-a-health-probe-for-load-balancer-referenced-by-vm-scale-set"></a>Causa: non è possibile modificare la porta back-end per una regola di bilanciamento del carico usata da un probe di integrità per il servizio di bilanciamento del carico a cui fa riferimento il set di scalabilità di macchine virtuali.
**Risoluzione**: per cambiare porta, è possibile rimuovere il probe di integrità aggiornando il set di scalabilità di macchine virtuali, aggiornare la porta e quindi configurare di nuovo il probe di integrità.

## <a name="symptom-small-traffic-is-still-going-through-load-balancer-after-removing-vms-from-backend-pool-of-the-load-balancer"></a>Sintomo: dopo la rimozione delle macchine virtuali dal pool back-end del bilanciamento del carico è ancora presente traffico ridotto gestito tramite il bilanciamento del carico. 
### <a name="cause--vms-removed-from-backend-pool-should-no-longer-receive-traffic-the-small-amount-of-network-traffic-could-be-related-to-storage-dns-and-other-functions-within-azure"></a>Causa: le macchine virtuali rimosse dal pool back-end non devono più ricevere traffico. La presenza di traffico di rete ridotto potrebbe essere correlata all'archiviazione, a DNS e ad altre funzioni in Azure. 
Per verificarlo, è possibile eseguire una traccia di rete. Il nome di dominio completo usato per gli account di archiviazione BLOB è elencato nelle proprietà di ogni account di archiviazione.  Da una macchina virtuale all'interno della sottoscrizione di Azure, è possibile eseguire un comando nslookup per determinare l'indirizzo IP di Azure assegnato a tale account di archiviazione.

## <a name="additional-network-captures"></a>Altre acquisizioni di rete
Se si decide di aprire un caso di supporto, raccogliere le informazioni seguenti per una soluzione più rapida. Scegliere una singola macchina virtuale back-end per eseguire questi test:
- Usare Psping da una delle macchine virtuali back-end all'interno della rete virtuale per testare la risposta della porta probe, ad esempio psping 10.0.0.4:3389, e registrare i risultati. 
- Se non si ricevono risposte in questi test di ping, eseguire una traccia Netsh simultanea nella macchina virtuale back-end e nella macchina virtuale di test della rete virtuale mentre si esegue PsPing, quindi arrestare la traccia Netsh. 
 
## <a name="symptom-load-balancer-in-failed-state"></a>Sintomo: Load Balancer in stato di errore 

**Risoluzione**

- Una volta identificata la risorsa in stato di errore, passare a [Azure Resource Explorer](https://resources.azure.com/) e identificare la risorsa in questo stato. 
- Aggiornare l'interruttore sull'angolo superiore destro in lettura/scrittura.
- Fare clic su modifica per la risorsa in stato di errore.
- Fare clic su PUT seguito da GET per assicurarsi che lo stato di provisioning sia stato aggiornato a succeeded.
- È quindi possibile continuare con altre azioni perché la risorsa non è in stato di errore.


## <a name="next-steps"></a>Passaggi successivi

Se i passaggi precedenti non risolvono il problema, aprire un [ticket di supporto](https://azure.microsoft.com/support/options/).