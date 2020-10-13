---
title: Azure Load Balancer regole in uscita
description: Questo articolo illustra come configurare le regole in uscita per controllare l'uscita del traffico Internet con Azure Load Balancer.
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: conceptual
ms.custom: contperfq1
ms.date: 10/13/2020
ms.author: allensu
ms.openlocfilehash: 51810876e3636b7023ce9c9318a071636bb00c4c
ms.sourcegitcommit: 090ea6e8811663941827d1104b4593e29774fa19
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2020
ms.locfileid: "92002639"
---
# <a name="outbound-rules-azure-load-balancer"></a><a name="outboundrules"></a>Azure Load Balancer regole in uscita

Le regole in uscita consentono di configurare SNAT di bilanciamento del carico standard pubblico (Source Network Address Translation). Questa configurazione consente di usare gli indirizzi IP pubblici del servizio di bilanciamento del carico come proxy.

Questa configurazione consente di:

* Mascheramento IP
* Semplificazione degli elenchi di accesso consentito.
* Riduce il numero di risorse IP pubbliche per la distribuzione.

Con le regole in uscita si ha un controllo dichiarativo completo sulla connettività Internet in uscita. Le regole in uscita consentono di ridimensionare e ottimizzare questa capacità in base alle esigenze specifiche. 

Le regole in uscita verranno seguite solo se la VM back-end non dispone di un indirizzo IP pubblico a livello di istanza (ILPIP).

![Regole in uscita di Load Balancer](media/load-balancer-outbound-rules-overview/load-balancer-outbound-rules.png)

Con le regole in uscita è possibile definire in modo esplicito il comportamento di **SNAT** in uscita.

Le regole in uscita consentono di controllare:

* **Quali macchine virtuali vengono convertite in indirizzi IP pubblici.**
     * Due regole erano il pool back-end A USA l'indirizzo IP A e B. il pool back-end B usa l'indirizzo IP C e D.
* **Modalità di utilizzo delle porte SNAT in uscita.**
     * Il pool back-end B è l'unico pool che effettua connessioni in uscita, assegna tutte le porte SNAT al pool back-end B e nessuna al pool back-end a.
* **Protocolli per i quali fornire la traduzione in uscita.**
     * Il pool back-end B necessita di porte UDP per l'uscita. Il pool back-end A richiede TCP. Assegnare le porte TCP a e a porte UDP a B.
* **La durata da usare per il timeout di inattività della connessione in uscita (4-120 minuti).**
     * Se sono presenti connessioni con esecuzione prolungata con KeepAlive, riservare le porte inattive per le connessioni a esecuzione prolungata per un massimo di 120 minuti. Si supponga che le connessioni non aggiornate vengano abbandonate e rilasciate in 4 minuti per le connessioni recenti 
* **Indica se inviare una reimpostazione TCP al timeout di inattività.**
     * Quando si verifica il timeout delle connessioni inattive, si invia un RST TCP al client e al server in modo che il flusso venga abbandonato?

## <a name="outbound-rule-definition"></a>Definizione della regola in uscita

Le regole in uscita seguono la stessa sintassi familiare come bilanciamento del carico e regole NAT in ingresso **:**  +  **parameters**  +  **pool back-end**dei parametri front-end. 

Una regola in uscita configura NAT in uscita per _tutte le macchine virtuali identificate dal pool back-end_ da convertire in _front-end_.  

I _parametri_ forniscono un controllo granulare aggiuntivo per l'algoritmo NAT in uscita.

## <a name="scale-outbound-nat-with-multiple-ip-addresses"></a><a name="scale"></a> Scalabilità di NAT in uscita con più indirizzi IP

Ogni indirizzo IP aggiuntivo fornito da un front-end fornisce altre porte effimere 64.000 per il bilanciamento del carico da usare come porte SNAT. 

Usare più indirizzi IP per pianificare scenari su larga scala. Usare le regole in uscita per attenuare l' [esaurimento SNAT](troubleshoot-outbound-connection.md#snatexhaust). 

È anche possibile usare un [prefisso IP pubblico](https://aka.ms/lbpublicipprefix) direttamente con una regola in uscita. 

Un prefisso IP pubblico aumenta il ridimensionamento della distribuzione. Il prefisso può essere aggiunto all'elenco Consenti dei flussi originati dalle risorse di Azure. È possibile configurare una configurazione IP front-end all'interno del servizio di bilanciamento del carico per fare riferimento a un prefisso di indirizzo IP pubblico.  

Il servizio di bilanciamento del carico ha il controllo sul prefisso IP pubblico. La regola in uscita utilizzerà automaticamente tutti gli indirizzi IP pubblici contenuti nel prefisso IP pubblico per le connessioni in uscita. 

Ognuno degli indirizzi IP all'interno del prefisso IP pubblico fornisce altre porte temporanee 64.000 per ogni indirizzo IP per il servizio di bilanciamento del carico da usare come porte SNAT.

## <a name="outbound-flow-idle-timeout-and-tcp-reset"></a><a name="idletimeout"></a> Timeout di inattività del flusso in uscita e ripristino TCP

Le regole in uscita includono un parametro di configurazione per controllare il timeout di inattività per i flussi in uscita in modo da adattarlo alle esigenze dell'applicazione. Il timeout di inattività in uscita è per impostazione predefinita di 4 minuti. Per altre informazioni, vedere [configurare i timeout di inattività](load-balancer-tcp-idle-timeout.md). 

Il comportamento predefinito del servizio di bilanciamento del carico prevede l'eliminazione invisibile del flusso quando è stato raggiunto il timeout di inattività in uscita. Il `enableTCPReset` parametro abilita un comportamento e un controllo dell'applicazione stimabile. Il parametro determina se inviare la reimpostazione TCP (TCP RST) bidirezionale al timeout del timeout di inattività in uscita. 

Esaminare [il timeout di inattività](https://aka.ms/lbtcpreset) per i dettagli, inclusa la disponibilità dell'area.

## <a name="securing-and-controlling-outbound-connectivity-explicitly"></a><a name="preventoutbound"></a>Sicurezza e controllo della connettività in uscita in modo esplicito

Le regole di bilanciamento del carico forniscono la programmazione automatica del NAT in uscita. Alcuni scenari traggono vantaggio o richiedono la disabilitazione della programmazione automatica del NAT in uscita dalla regola di bilanciamento del carico. La disabilitazione tramite la regola consente di controllare o affinare il comportamento.  

È possibile usare questo parametro in due modi:

1. Prevenzione dell'indirizzo IP in ingresso per SNAT in uscita. Disabilitare SNAT in uscita nella regola di bilanciamento del carico.
  
2. Ottimizzare i parametri di **SNAT** in uscita di un indirizzo IP usato per il traffico in ingresso e in uscita simultaneamente. Il NAT in uscita automatico deve essere disabilitato per consentire a una regola in uscita di assumere il controllo. Per modificare l'allocazione delle porte SNAT di un indirizzo utilizzato anche per il traffico in ingresso, il `disableOutboundSnat` parametro deve essere impostato su true. 

L'operazione di configurazione di una regola in uscita avrà esito negativo se si tenta di ridefinire un indirizzo IP utilizzato per il traffico in ingresso.  Disabilitare prima la NAT in uscita della regola di bilanciamento del carico.

>[!IMPORTANT]
> La macchina virtuale non avrà connettività in uscita se si imposta questo parametro su true e non si dispone di una regola in uscita per definire la connettività in uscita.  Alcune operazioni della macchina virtuale o dell'applicazione possono dipendere dalla disponibilità della connettività in uscita. Assicurarsi di valutare le dipendenze dello scenario e l'impatto di questa modifica.

A volte non è auspicabile che una macchina virtuale crei un flusso in uscita. Potrebbe essere necessario gestire le destinazioni che ricevono i flussi in uscita o le destinazioni che iniziano i flussi in ingresso. Usare i [gruppi di sicurezza di rete](../virtual-network/security-overview.md) per gestire le destinazioni raggiunte dalla macchina virtuale. Usare gruppi per gestire le destinazioni pubbliche che avviano i flussi in ingresso.

Quando si applica un gruppo di sicurezza di rete a una macchina virtuale con carico bilanciato, considerare i [tag del servizio](../virtual-network/security-overview.md#service-tags) e le [regole di sicurezza predefinite](../virtual-network/security-overview.md#default-security-rules). 

Assicurarsi che la macchina virtuale sia in grado di ricevere richieste di probe di integrità da Azure Load Balancer.

Se un NSG blocca le richieste di probe di integrità dal AZURE_LOADBALANCER tag predefinito, il probe di integrità della macchina virtuale ha esito negativo e la macchina virtuale è contrassegnata come Il servizio di bilanciamento del carico interrompe l'invio di nuovi flussi a tale macchina virtuale.

## <a name="limitations"></a>Limitazioni

- Il numero massimo di porte temporanee utilizzabili per ogni indirizzo IP front-end è 64.000.
- L'intervallo del timeout di inattività in uscita configurabile è compreso tra 4 a 120 minuti (da 240 a 7200 secondi).
- Il servizio di bilanciamento del carico non supporta ICMP per la NAT in uscita.
- Le regole in uscita possono essere applicate solo alla configurazione IP primaria di una scheda di interfaccia di rete.  Non è possibile creare una regola in uscita per l'IP secondario di una VM o di un appliance virtuale di dispositivo. Sono supportate più schede di rete.
- Tutte le macchine virtuali all'interno di un **set di disponibilità** devono essere aggiunte al pool back-end per la connettività in uscita. 
- Tutte le macchine virtuali all'interno di un **set di scalabilità di macchine virtuali** devono essere aggiunte al pool back-end per la connettività in uscita.

## <a name="next-steps"></a>Passaggi successivi

- Scopri di più su [Azure Load Balancer standard](load-balancer-overview.md)
- Vedere le [domande frequenti su Azure Load Balancer](load-balancer-faqs.md)

