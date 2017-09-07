---
title: "Risoluzione dei problemi di connettività tra macchine virtuali di Azure | Documenti Microsoft"
description: "Informazioni su come risolvere i problemi di connettività tra macchine virtuali di Azure."
services: virtual-network
documentationcenter: na
author: chadmath
manager: cshepard
editor: 
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/25/2017
ms.author: genli
ms.translationtype: HT
ms.sourcegitcommit: 8351217a29af20a10c64feba8ccd015702ff1b4e
ms.openlocfilehash: 524f6303f71590de75f7eb8fd2a9082c35dfa651
ms.contentlocale: it-it
ms.lasthandoff: 08/29/2017

---

# <a name="troubleshooting-connectivity-problems-between-azure-vms"></a>Risoluzione dei problemi di connettività tra macchine virtuali di Azure

Si potrebbero riscontrare problemi di connettività tra macchine virtuali di Azure. Questo articolo illustra la procedura per risolvere questo tipo di problema. 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="symptom"></a>Sintomo

Una macchina virtuale di Azure non riesce a connettersi a un'altra macchina virtuale di Azure.

## <a name="troubleshooting-guidance"></a>Guida alla risoluzione dei problemi 

1. [Controllare che NIC sia configurato correttamente](#step-1-check-whether-nic-is-misconfigured)
2. [Controllare se il traffico è bloccato dal gruppo di sicurezza di rete o UDR](#step-2-check-whether-network-traffic-is-blocked-by-nsg-or-udr)
3. [Controllare se il traffico è bloccato da un firewall della macchina virtuale](#step-3-check-whether-network-traffic-is-blocked-by-vm-firewall)
4. [Controllare se un'applicazione o un servizio della macchina virtuale sono in ascolto sulla porta](#step-4-check-whether-vm-app-or-service-is-listening-on-the-port)
5. [Controllare se il problema è causato dall'architettura SNAT](#step-5-check-whether-the-problem-is-caused-by-snat)
6. [Controllare se il traffico è bloccato da ACL per la macchina virtuale classica](#step-6-check-whether-traffic-is-blocked-by-acls-for-the-classic-vm)
7. [Controllare che l'endpoint per la macchina virtuale classica sia stato creato](#step-7-check-whether-the-endpoint-is-created-for-the-classic-vm)
8. [Provare a connettersi a una condivisione di rete della macchina virtuale](#step-8-try-to-connect-to-a-vm-network-share)
9. [Controllare la connettività tra reti virtuali](#step-9-check-inter-vnet-connectivity)

## <a name="troubleshooting-steps"></a>Passaggi per la risoluzione dei problemi

Seguire questa procedura per risolvere il problema. Dopo avere completato ogni passaggio, verificare se il problema è risolto. 

### <a name="step-1-check-whether-nic-is-misconfigured"></a>Passaggio 1: Controllare che NIC sia configurato correttamente

Seguire i passaggi illustrati in [Come reimpostare l'interfaccia di rete per la VM Windows Azure](../virtual-machines/windows/reset-network-interface.md). 

Se il problema si verifica dopo aver modificato l'interfaccia di rete, procedere come segue:

**Macchine virtuali a più NIC**

1. Aggiungere un NIC.
2. Risolvere i problemi nella scheda di rete non valida o rimuovere quest'ultima,  quindi aggiungere di nuovo la scheda di rete.

Per altre informazioni, vedere [Aggiungere o rimuovere interfacce di rete da macchine virtuali](virtual-network-network-interface-vm.md).

**Macchina virtuale con una sola scheda di rete** 

- [Ridistribuire una VM Windows](../virtual-machines/windows/redeploy-to-new-node.md)
- [Ridistribuire una VM Linux](../virtual-machines/linux/redeploy-to-new-node.md)

### <a name="step-2-check-whether-network-traffic-is-blocked-by-nsg-or-udr"></a>Passaggio 2: Controllare se il traffico è bloccato dal gruppo di sicurezza di rete o UDR

Usare [Network Watcher IP Flow Verify](../network-watcher/network-watcher-ip-flow-verify-overview.md) (Verifica flusso IP di Network Watcher) e [NSG Flow Logging](../network-watcher/network-watcher-nsg-flow-logging-overview.md) (Registrazione del flusso NSG) per determinare se vi è o meno un gruppo di sicurezza di rete (NSG) o una route definita dall'utente (UDR) che interferisce con il flusso del traffico.

### <a name="step-3-check-whether-network-traffic-is-blocked-by-vm-firewall"></a>Passaggio 3: Controllare se il traffico è bloccato da un firewall della macchina virtuale

Disattivare il firewall, quindi testare i risultati. Se il problema viene risolto, verificare le impostazioni del firewall e quindi riattivarlo.

### <a name="step-4-check-whether-vm-app-or-service-is-listening-on-the-port"></a>Passaggio 4: Controllare se un'applicazione o un servizio della macchina virtuale sono in ascolto sulla porta

Per verificare se un'applicazione o un servizio della macchina virtuale sono in ascolto sulla porta, usare uno dei metodi seguenti.

- Eseguire i comandi seguenti per verificare se il server è in ascolto su quella porta.

**VM Windows**

    netstat –ano

**VM Linux**

    netstat -l

- Per testare la porta, eseguire il comando **telnet** nella macchina virtuale. Se il test non riesce, l'applicazione o il servizio non sono configurati per restare in ascolto su quella porta.

### <a name="step-5-check-whether-the-problem-is-caused-by-snat"></a>Passaggio 5: Controllare se il problema è causato dall'architettura SNAT

In alcuni scenari, la macchina virtuale è posizionata dietro una soluzione di bilanciamento del carico che presenta una dipendenza da risorse esterne ad Azure. In questi scenari, eventuali problemi di connessione intermittenti possono essere causati dall'[esaurimento delle porte SNAT](../load-balancer/load-balancer-outbound-connections.md). Per risolvere il problema, creare un indirizzo VIP (o ILPIP per le macchine virtuali classiche) per ogni macchina virtuale correlata alla soluzione di bilanciamento del carico e protetta mediante NSG o ACL. 

### <a name="step-6-check-whether-traffic-is-blocked-by-acls-for-the-classic-vm"></a>Passaggio 6: Controllare se il traffico è bloccato da ACL per la macchina virtuale classica

Un elenco di controllo di accesso offre la possibilità di consentire o negare in modo selettivo il traffico per un endpoint di macchina virtuale. Per altre informazioni, vedere [Gestire l'elenco di controllo di accesso su un endpoint](../virtual-machines/windows/classic/setup-endpoints.md#manage-the-acl-on-an-endpoint).

### <a name="step-7-check-whether-the-endpoint-is-created-for-the-classic-vm"></a>Passaggio 7: Controllare che l'endpoint per la macchina virtuale classica sia stato creato

Tutte le macchine virtuali create in Azure con il modello di distribuzione classico possono comunicare automaticamente mediante un canale di rete privato con altre macchine virtuali dello stesso servizio cloud o nella stessa rete virtuale. Tuttavia, i computer in altre reti virtuali richiedono degli endpoint per indirizzare il traffico di rete in ingresso a una macchina virtuale. Per altre informazioni, vedere [Come configurare gli endpoint](../virtual-machines/windows/classic/setup-endpoints.md).

### <a name="step-8-try-to-connect-to-a-vm-network-share"></a>Passaggio 8: Provare a connettersi a una condivisione di rete della macchina virtuale

Se non è possibile connettersi a una condivisione di rete della macchina virtuale, il problema potrebbe essere causato dalla mancata disponibilità di alcune schede interfaccia nella macchina virtuale. Per eliminare le schede di interfaccia non disponibili, vedere [Eliminare le schede di interfaccia non disponibili](../virtual-machines/windows/reset-network-interface.md#delete-the-unavailable-nics)

### <a name="step-9-check-inter-vnet-connectivity"></a>Passaggio 9: Controllare la connettività tra reti virtuali

Usare [Network Watcher IP Flow Verify](../network-watcher/network-watcher-ip-flow-verify-overview.md) (Verifica flusso IP di Network Watcher) e [NSG Flow Logging](../network-watcher/network-watcher-nsg-flow-logging-overview.md) (Registrazione del flusso NSG) per determinare se vi è o meno un NSG o UDR che interferisce con il flusso del traffico. È possibile verificare la configurazione della rete virtuale anche da [qui](https://support.microsoft.com/en-us/help/4032151/configuring-and-validating-vnet-or-vpn-connections).

### <a name="need-help-contact-support"></a>Richiesta di assistenza Contattare il supporto tecnico.
Se si necessita ancora di assistenza, [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per ottenere una rapida risoluzione del problema.
