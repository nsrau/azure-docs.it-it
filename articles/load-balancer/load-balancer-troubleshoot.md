---
title: Risolvere i problemi di Azure Load Balancer | Microsoft Docs
description: Risolvere i problemi noti di Azure Load Balancer
services: load-balancer
documentationcenter: na
author: RamanDhillon
manager: timlt
editor: 
ms.assetid: 
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: df5c836abbf09889f4859170359c0ee6c1b03378
ms.contentlocale: it-it
ms.lasthandoff: 07/21/2017

---

# <a name="troubleshoot-azure-load-balancer"></a>Risolvere i problemi di Azure Load Balancer

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

Questa pagina fornisce informazioni sulla risoluzione dei problemi comuni di Azure Load Balancer. Quando la connettività di Load Balancer non è disponibile, i sintomi più comuni sono i seguenti: 
- Le macchine virtuali controllate da Load Balancer non rispondono ai probe di integrità 
- Le macchine virtuali dietro Load Balancer non rispondono al traffico nella porta configurata

## <a name="symptom-vms-behind-the-load-balancer-are-not-responding-to-health-probes"></a>Sintomo: le macchine virtuali dietro Load Balancer non rispondono ai probe di integrità
Per partecipare al set di bilanciamento del carico, i server back-end devono superare il controllo del probe. Per altre informazioni sui probe di integrità, vedere [Informazioni sui probe di bilanciamento del carico](load-balancer-custom-probe-overview.md). 

Le macchine virtuali del pool back-end di Load Balancer possono non rispondere ai probe per i motivi seguenti: 
- La macchina virtuale del pool back-end di Load Balancer non è integra 
- La macchina virtuale del pool back-end di Load Balancer non è in ascolto nella porta probe 
- Un firewall o un gruppo di sicurezza di rete blocca la porta nelle macchine virtuali del pool back-end di Load Balancer 
- Altri errori di configurazione in Load Balancer

### <a name="cause-1-load-balancer-backend-pool-vm-is-unhealthy"></a>Causa 1: la macchina virtuale del pool back-end di Load Balancer non è integra 

**Convalida e la risoluzione**

Per risolvere questo problema, accedere alle macchine virtuali partecipanti e verificare se la macchina virtuale è integra e può rispondere a richieste **PsPing** o **TCPing** inviate da un'altra macchina virtuale nel pool. Se la macchina virtuale non è integra o non riesce a rispondere al probe, è necessario risolvere il problema e ripristinare l'integrità della macchina virtuale prima che questa possa partecipare al bilanciamento del carico.

### <a name="cause-2-load-balancer-backend-pool-vm-is-not-listening-on-the-probe-port"></a>Causa 2: la macchina virtuale del pool back-end di Load Balancer non è in ascolto nella porta probe
Se la macchina virtuale è integra, ma non risponde al probe, è possibile che la porta probe non sia aperta nella macchina virtuale partecipante o che la macchina virtuale non sia in ascolto su tale porta.

**Convalida e la risoluzione**

1. Accedere alla macchina virtuale back-end. 
2. Aprire un prompt dei comandi ed eseguire questo comando per verificare che un'applicazione sia in ascolto nella porta probe:   
            netstat -an
3. Se lo stato della porta non è elencato come **LISTENING**, configurare la porta corretta. 
4. In alternativa, selezionare un'altra porta che sia elencata come **LISTENING** e aggiornare la configurazione di Load Balancer di conseguenza.              

###<a name="cause-3-firewall-or-a-network-security-group-is-blocking-the-port-on-the-load-balancer-backend-pool-vms"></a>Causa 3: un firewall o un gruppo di sicurezza di rete blocca la porta nelle macchine virtuali del pool back-end di Load Balancer  
Se il firewall nella macchina virtuale blocca la porta probe oppure uno o più gruppi di sicurezza di rete configurati nella subnet o nella macchina virtuale non consentono al probe di raggiungere la porta, la macchina virtuale non può rispondere al probe di integrità.          

**Convalida e la risoluzione**

* Se il firewall è abilitato, verificare se è configurato per consentire la porta probe. In caso contrario, configurare il firewall per consentire il traffico nella porta probe e riprovare. 
* Dall'elenco dei gruppi di sicurezza di rete, verificare se il traffico in ingresso o in uscita nella porta probe ha interferenze. 
* Verificare anche se nella scheda di interfaccia di rete della macchina virtuale o nella subnet è presente una regola di tipo **Nega tutto** di un gruppo di sicurezza di rete avente una priorità superiore rispetto alla regola predefinita che consente il traffico e i probe di Load Balancer. I gruppi di sicurezza di rete devono consentire l'IP 168.63.129.16 di Load Balancer. 
* Se alcune di queste regole bloccano il traffico probe, rimuoverle e riconfigurarle per consentire il traffico probe.  
* Verificare se la macchina virtuale ha ora iniziato a rispondere ai probe di integrità. 

### <a name="cause-4-other-misconfigurations-in-load-balancer"></a>Causa 4: altri errori di configurazione in Load Balancer
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
* Modificare il tipo di probe, ad esempio da HTTP a TCP, e configurare la porta corrispondente negli ACL dei gruppi di sicurezza di rete e nel firewall per verificare se il problema è la configurazione della risposta probe. Per altre informazioni sulla configurazione del probe di integrità, vedere la [configurazione del probe di integrità di bilanciamento del carico con endpoint](https://blogs.msdn.microsoft.com/mast/2016/01/26/endpoint-load-balancing-heath-probe-configuration-details/).

## <a name="symptom-vms-behind-load-balancer-are-not-responding-to-traffic-on-the-configured-data-port"></a>Sintomo: le macchine virtuali dietro Load Balancer non rispondono al traffico nella porta di dati configurata

Se una macchina virtuale del pool back-end è elencata come integra e risponde ai probe di integrità, ma comunque non partecipa al bilanciamento del carico o non risponde al traffico dati, i motivi potrebbero essere i seguenti: 
* La macchina virtuale del pool back-end di Load Balancer non è in ascolto nella porta dati 
* Un gruppo di sicurezza di rete blocca la porta nella macchina virtuale del pool back-end di Load Balancer  
* Accesso a Load Balancer dalla stessa macchina virtuale e interfaccia di rete 
* Accesso all'indirizzo VIP del servizio di bilanciamento del carico Internet dalla macchina virtuale del pool back-end di Load Balancer 

### <a name="cause-1-load-balancer-backend-pool-vm-is-not-listening-on-the-data-port"></a>Causa 1: la macchina virtuale del pool back-end di Load Balancer non è in ascolto nella porta dati 
Se una macchina virtuale non risponde al traffico dati, è possibile che la porta di destinazione non sia aperta nella macchina virtuale partecipante oppure che la macchina virtuale non sia in ascolto nella porta. 

**Convalida e la risoluzione**

1. Accedere alla macchina virtuale back-end. 
2. Aprire un prompt dei comandi ed eseguire questo comando per verificare che un'applicazione sia in ascolto nella porta dati:  
            netstat -an 
3. Se lo stato della porta non è elencato come "LISTENING", configurare la porta di attesa corretta. 
4. Se la porta è contrassegnata come LISTENING, verificare se sono presenti problemi nell'applicazione di destinazione in ascolto su tale porta. 

### <a name="cause-2-network-security-group-is-blocking-the-port-on-the-load-balancer-backend-pool-vm"></a>Causa 2: un gruppo di sicurezza di rete blocca la porta nella macchina virtuale del pool back-end di Load Balancer  

Se uno o più gruppi di sicurezza di rete configurati nella subnet o nella macchina virtuale bloccano l'indirizzo IP o la porta di origine, la macchina virtuale non potrà rispondere.

* Elencare i gruppi di sicurezza di rete configurati nella macchina virtuale back-end. Per altre informazioni, vedere:
    -  [Gestire gruppi di sicurezza di rete usando il portale](../virtual-network/virtual-network-manage-nsg-arm-portal.md)
    -  [Gestire i gruppi di sicurezza di rete usando PowerShell](../virtual-network/virtual-network-manage-nsg-arm-ps.md)
* Dall'elenco dei gruppi di sicurezza di rete verificare se:
    - il traffico in ingresso o in uscita nella porta dati ha interferenze. 
    - nella scheda di interfaccia di rete della macchina virtuale o nella subnet è presente una regola di tipo **Nega tutto** di un gruppo di sicurezza di rete avente una priorità superiore rispetto alla regola predefinita che consente il traffico e i probe di Load Balancer. I gruppi di sicurezza di rete devono consentire l'IP 168.63.129.16 di Load Balancer, ovvero la porta probe 
* Se alcune di queste regole bloccano il traffico, rimuoverle e riconfigurarle per consentire il traffico dati.  
* Verificare se la macchina virtuale ha ora iniziato a rispondere ai probe di integrità.

### <a name="cause-3-accessing-the-load-balancer-from-the-same-vm-and-network-interface"></a>Causa 3: accesso a Load Balancer dalla stessa macchina virtuale e interfaccia di rete 

Se l'applicazione ospitata nella macchina virtuale back-end di un servizio di bilanciamento del carico sta provando ad accedere a un'altra applicazione ospitata nella stessa macchina virtuale back-end tramite la stessa interfaccia di rete, questo scenario non è supportato e avrà esito negativo. 

**Risoluzione**: è possibile risolvere questo problema con uno dei metodi seguenti:
* Configurare macchine virtuali del pool back-end separate per ogni applicazione. 
* Configurare l'applicazione in macchine virtuali con due schede di interfaccia di rete in modo che ogni applicazione usi un'interfaccia di rete e un indirizzo IP propri. 

### <a name="cause-4-accessing-the-internal-load-balancer-vip-from-the-participating-load-balancer-backend-pool-vm"></a>Causa 4: accesso all'indirizzo VIP del servizio di bilanciamento del carico interno dalla macchina virtuale del pool back-end di Load Balancer

Se è configurato un indirizzo VIP del bilanciamento del carico interno in una rete virtuale e una delle VM del back-end sta tentando di accedere all'indirizzo VIP del servizio di bilanciamento del carico Internet, verrà generato un errore. Questo scenario non è supportato.
**Risoluzione**: valutare un gateway applicazione o altri proxy, ad esempio nginx o haproxy, per supportare questo tipo di scenario. Per altre informazioni sul gateway applicazione, vedere [Panoramica del gateway applicazione](../application-gateway/application-gateway-introduction.md)

## <a name="additional-network-captures"></a>Altre acquisizioni di rete
Se si decide di aprire un caso di supporto, raccogliere le informazioni seguenti per una soluzione più rapida. Scegliere una singola macchina virtuale back-end per eseguire questi test:
- Usare Psping da una delle macchine virtuali back-end all'interno della rete virtuale per testare la risposta della porta probe, ad esempio psping 10.0.0.4:3389, e registrare i risultati. 
- Usare TCPing da una delle macchine virtuali back-end all'interno della rete virtuale per testare la risposta della porta probe, ad esempio psping 10.0.0.4:3389, e registrare i risultati.
- Se non si ricevono risposte in questi test di ping, eseguire una traccia Netsh simultanea nella macchina virtuale back-end e nella macchina virtuale di test della rete virtuale mentre si esegue PsPing, quindi arrestare la traccia Netsh. 
  
## <a name="next-steps"></a>Passaggi successivi

Se i passaggi precedenti non risolvono il problema, aprire un [ticket di supporto](https://azure.microsoft.com/support/options/).


