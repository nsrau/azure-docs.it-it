---
title: "Lavorare in remoto: considerazioni sull'appliance virtuale di rete per il lavoro remoto | Gateway VPN di Azure"
description: Questo articolo consente di comprendere gli aspetti da prendere in considerazione nell'uso di appliance virtuali di rete (appliance virtuali) in Azure durante la pandemia COVID-19.
services: vpn-gateway
author: scottnap
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: scottnap
ms.openlocfilehash: 4783016e472907392f2d379efa0fed2d90ed21bc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89595360"
---
# <a name="working-remotely-network-virtual-appliance-nva-considerations-for-remote-work"></a>Lavorare in remoto: considerazioni sull'appliance virtuale di rete per il lavoro remoto

>[!NOTE]
>Questo articolo descrive come sfruttare le appliance virtuali di rete, Azure, Microsoft Network e l'ecosistema di partner di Azure per lavorare in modalità remota e mitigare i problemi di rete da affrontare a causa della crisi COVID-19.
>

Alcuni clienti di Azure usano appliance virtuali di rete di terze parti (appliance virtuali) da Azure Marketplace per fornire servizi critici come la VPN da punto a sito per i dipendenti che lavorano da casa durante l'epidemia COVID-19. Questo articolo illustra alcune indicazioni generali da tenere in considerazione quando si utilizza appliance virtuali in Azure per fornire soluzioni di accesso remoto.

## <a name="nva-performance-considerations"></a>Considerazioni sulle prestazioni di NVA

Tutti i principali fornitori di appliance virtuale di Azure in Azure Marketplace devono avere consigli sulle dimensioni e il numero di istanze della macchina virtuale da usare per la distribuzione delle soluzioni.  Sebbene quasi tutti i fornitori di appliance virtuale di Azure consentiranno di scegliere le dimensioni disponibili in una determinata area, è molto importante seguire i consigli dei fornitori per le dimensioni delle istanze di VM di Azure, in quanto queste raccomandazioni sono le dimensioni delle VM con cui il fornitore ha eseguito il test delle prestazioni in Azure.  

### <a name="consider-the-following"></a>Tenere presente quanto segue:

- **Capacità e numero di utenti simultanei** : questo numero è particolarmente importante per gli utenti VPN da punto a sito poiché ogni utente connesso creerà un tunnel crittografato (VPN IPSec o SSL).  
- **Velocità effettiva aggregata** : quale sarà la larghezza di banda aggregata necessaria per il numero di utenti necessari per fornire accesso remoto.
- **Dimensioni della macchina virtuale necessarie** : è consigliabile usare sempre le dimensioni delle VM consigliate dal fornitore dell'appliance virtuale di dispositivo.  Per la VPN da punto a sito, se si dispone di un numero elevato di connessioni utente simultanee, è consigliabile usare dimensioni di VM maggiori, ad esempio macchine virtuali della [serie dv2 e DSv2](https://docs.microsoft.com/azure/virtual-machines/dv2-dsv2-series "Serie dv2 e Dsv2") . Queste VM tendono a avere più vCPU e possono gestire più sessioni VPN simultanee.  Oltre a avere più core virtuali, le dimensioni delle VM più grandi in Azure hanno una capacità di larghezza di banda aggregata superiore rispetto alle dimensioni delle VM più piccole.
    > **Importante:** Ogni fornitore utilizza le risorse in modo diverso.  Se non è chiaro quali dimensioni delle istanze usare per soddisfare il carico utente stimato, è necessario contattare direttamente il fornitore del software e richiedere una raccomandazione.
- **Numero di istanze** : se si prevede di avere un numero elevato di utenti e connessioni, è possibile che si verifichi la scalabilità delle dimensioni delle istanze di appliance virtuale di dispositivo.  Prendere in considerazione la distribuzione di più istanze di VM.
- VPN **IPSec rispetto alla VPN SSL** : le implementazioni VPN IPSec generali offrono prestazioni migliori rispetto alle implementazioni VPN SSL.  
- **Gestione delle licenze** : assicurarsi che le licenze software acquistate per la soluzione di appliance virtuale di dispositivo coprano la crescita improvvisa che può verificarsi durante l'epidemia COVID-19.  Molti programmi di licenza per l'appliance virtuale di dispositivo limitano il numero di connessioni o la larghezza di banda supportata dalla soluzione.
- **Rete accelerata** : prendere in considerazione una soluzione appliance virtuale di rete con supporto per la rete accelerata.  La funzionalità rete accelerata abilita Single Root I/O Virtualization (SR-IOV) per le VM, migliorandone le prestazioni di rete. Questo percorso a prestazioni elevate ignora l'host dal percorso dati, riducendo la latenza, l'instabilità e l'utilizzo della CPU per l'uso con i carichi di lavoro di rete più complessi sui tipi di VM supportati. La funzionalità rete accelerata è supportata per la maggior parte delle istanze di utilizzo generico e ottimizzate per il calcolo con due o più vCPU.

## <a name="monitoring-resources"></a>Monitoraggio delle risorse

Ogni soluzione di appliance virtuale di dispositivo ha strumenti e risorse propri per il monitoraggio delle prestazioni dell'appliance virtuale di dispositivo.  Consultare la documentazione dei fornitori per assicurarsi di comprendere le limitazioni delle prestazioni e rilevare quando l'appliance virtuale di sistema si avvicina o raggiunge la capacità.  Inoltre, è possibile esaminare Azure monitor network Insights e visualizzare le informazioni di base sulle prestazioni delle appliance virtuali di rete, ad esempio:

- Utilizzo CPU
- Rete in ingresso
- Rete in uscita
- Flussi in ingresso
- Flussi in uscita

## <a name="next-steps"></a>Passaggi successivi

La maggior parte dei principali partner dell'appliance virtuale di terze parti ha pubblicato materiale sussidiario per una crescita improvvisa e imprevista durante COVID Di seguito sono riportati alcuni collegamenti utili per le risorse del partner.

[Barracuda Abilita il lavoro da casa e protegge i dati durante COVID-19](https://www.barracuda.com/covid-19/work-from-home "Abilita il lavoro da casa e Proteggi i dati durante COVID-19")

[Controllare la forza lavoro remoto sicura durante il coronavirus](https://www.checkpoint.com/solutions/secure-remote-workforce-during-coronavirus/ "Sicurezza della forza lavoro remoto durante il coronavirus")

[Informazioni di riferimento su implementazione e prestazioni/scalabilità Cisco AnyConnect per la preparazione di COVID-19](https://www.cisco.com/c/en/us/support/docs/security/anyconnect-secure-mobility-client/215331-anyconnect-implementation-and-performanc.html "Informazioni di riferimento su implementazione e prestazioni/scalabilità Cisco AnyConnect per la preparazione di COVID-19")

[Supporto tecnico per Citrix COVID-19 Response Center](https://www.citrix.com/support/covid-19-coronavirus.html "Supporto tecnico per Citrix COVID-19 Response Center")

[F5 linee guida per risolvere il notevole aumento dei ruoli di lavoro remoti](https://www.f5.com/business-continuity "F5 linee guida per risolvere il notevole aumento dei ruoli di lavoro remoti")

[Fortinet COVID-19 aggiornamenti per clienti e partner](https://www.fortinet.com/covid-19.html "COVID-19 aggiornamenti per clienti e partner")

[Palo Alto Networks COVID-19 Response Center](https://live.paloaltonetworks.com/t5/COVID-19-Response-Center/ct-p/COVID-19_Response_Center "Palo Alto Networks COVID-19 Response Center")
