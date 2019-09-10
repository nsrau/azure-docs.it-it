---
title: Informazioni sul firewall di Azure
description: Informazioni sulle funzionalità di Firewall di Azure.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: overview
ms.custom: mvc
ms.date: 09/4/2019
ms.author: victorh
Customer intent: As an administrator, I want to evaluate Azure Firewall so I can determine if I want to use it.
ms.openlocfilehash: c685b2314d15e431ccac3470fd337ca92697e1a5
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70241172"
---
# <a name="what-is-azure-firewall"></a>Informazioni sul firewall di Azure

Firewall di Azure è un servizio di sicurezza di rete gestito basato sul cloud che consente di proteggere le risorse della rete virtuale di Azure. È un firewall con stato completo distribuito come servizio con disponibilità elevata e scalabilità cloud senza limiti.

![Panoramica del firewall](media/overview/firewall-threat.png)

È possibile creare, applicare e registrare criteri di connettività di applicazione e di rete in modo centralizzato tra le sottoscrizioni e le reti virtuali. Firewall di Azure usa un indirizzo IP pubblico statico per le risorse della rete virtuale consentendo ai firewall esterni di identificare il traffico proveniente dalla rete virtuale.  Il servizio è completamente integrato con Monitoraggio di Azure per la registrazione e l'analisi.

Il Firewall di Azure offre le funzionalità seguenti:

## <a name="built-in-high-availability"></a>Disponibilità elevata predefinita

La disponibilità elevata è integrata, quindi non sono necessari servizi di bilanciamento del carico aggiuntivi e non è necessario eseguire altre configurazioni.

## <a name="availability-zones"></a>Zone di disponibilità

Firewall di Azure può essere configurato durante la distribuzione con più zone di disponibilità per una maggiore disponibilità. Con le zone di disponibilità, la disponibilità viene aumentata a un tempo di attività pari al 99,99%. Per altre informazioni, vedere il [Contratto di servizio](https://azure.microsoft.com/support/legal/sla/azure-firewall/v1_0/) per Firewall di Azure. Il contratto di servizio con un tempo di attività del 99,99% è disponibile quando si selezionano due o più zone di disponibilità.

È anche possibile associare Firewall di Azure a una zona specifica solo per motivi di prossimità, tramite il contratto di servizio standard pari al 99,95%.

La distribuzione di un firewall in una zona di disponibilità non comporta costi aggiuntivi. Sono tuttavia previsti costi aggiuntivi per i trasferimenti di dati in ingresso e in uscita associati alle zone di disponibilità. Per altre informazioni, vedere [Dettagli sui prezzi per la larghezza di banda](https://azure.microsoft.com/pricing/details/bandwidth/).

Le zone di disponibilità di Firewall di Azure sono disponibili nelle aree che supportano le zone di disponibilità. Per altre informazioni, vedere [Informazioni sulle zone di disponibilità di Azure](../availability-zones/az-overview.md#services-support-by-region)

> [!NOTE]
> Le zone di disponibilità possono essere configurate solo durante la distribuzione. Non è possibile configurare un firewall esistente per includere le zone di disponibilità.

Per altre informazioni sulle zone di disponibilità, vedere [Informazioni sulle zone di disponibilità di Azure](../availability-zones/az-overview.md)

## <a name="unrestricted-cloud-scalability"></a>Scalabilità del cloud senza restrizioni

È possibile aumentare le prestazioni di Firewall di Azure a seconda delle esigenze in modo da soddisfare i mutevoli flussi del traffico di rete senza dover stabilire un budget per i periodi di traffico più intenso.

## <a name="application-fqdn-filtering-rules"></a>Regole di filtro del nome di dominio completo dell'applicazione

È possibile limitare il traffico HTTP/S in uscita o il traffico SQL di Azure (anteprima) a un elenco specifico di nomi di dominio completo (FQDN), inclusi i caratteri jolly. Questa funzionalità non richiede la terminazione SSL.

## <a name="network-traffic-filtering-rules"></a>Regole di filtro per il traffico di rete

È possibile creare in modo centralizzato regole di filtro di rete per *consentire* o *negare* il traffico in base all'indirizzo IP di origine e di destinazione, alla porta e al protocollo. Firewall di Azure è un servizio con stato completo, quindi in grado di distinguere i pacchetti validi per diversi tipi di connessioni. Le regole vengono applicate e registrate in più sottoscrizioni e reti virtuali.

## <a name="fqdn-tags"></a>Tag FQDN

I tag FQDN rendono più semplice consentire il traffico di rete noto del servizio di Azure attraverso il firewall. Ad esempio, si supponga di voler consentire il traffico di rete di Windows Update attraverso il firewall. Creare una regola di applicazione e includere il tag di Windows Update. A questo punto il traffico di rete da Windows Update può attraversare il firewall.

## <a name="service-tags"></a>Tag di servizio

Un tag di servizio rappresenta un gruppo di prefissi di indirizzo IP che consente di ridurre al minimo la complessità nella creazione di regole di sicurezza. Non è possibile creare tag di servizio personalizzati, né specificare gli indirizzi IP da includere in un tag. I prefissi di indirizzo inclusi nel tag di servizio sono gestiti da Microsoft, che aggiorna automaticamente il tag in caso di modifica degli indirizzi.

## <a name="threat-intelligence"></a>Intelligence per le minacce

I filtri basati sull'intelligence per le minacce possono essere abilitati per il firewall per la creazione di avvisi e il rifiuto del traffico da o verso indirizzi IP e domini dannosi noti. Gli indirizzi IP e i domini sono originati dal feed Intelligence sulle minacce Microsoft.

## <a name="outbound-snat-support"></a>Supporto SNAT in uscita

Tutti gli indirizzi IP del traffico di rete virtuale in uscita vengono convertiti nell'indirizzo IP pubblico di Firewall di Azure (Source Network Address Translation). È possibile identificare e consentire il traffico proveniente dalla rete virtuale a destinazioni Internet remote. Firewall di Azure non usa SNAT quando l'indirizzo IP di destinazione è un intervallo di indirizzi IP privati, in conformità a [RFC 1918 di IANA](https://tools.ietf.org/html/rfc1918). Se l'organizzazione usa un intervallo di indirizzi IP pubblici per le reti private, Firewall di Azure invierà il traffico tramite SNAT a uno degli indirizzi IP privati firewall in AzureFirewallSubnet.

## <a name="inbound-dnat-support"></a>Supporto DNAT in ingresso

Il traffico di rete in ingresso all'indirizzo IP pubblico del firewall viene convertito (Destination Network Address Translation) e filtrato per gli indirizzi IP nelle reti virtuali.

## <a name="multiple-public-ip-addresses"></a>Più indirizzi IP pubblici

> [!IMPORTANT]
> Il servizio Firewall di Azure con più indirizzi IP pubblici è disponibile tramite il portale di Azure, Azure PowerShell, l'interfaccia della riga di comando di Azure, REST e i modelli.


È possibile associare al firewall più indirizzi IP pubblici (fino a 100).

Questo supporta gli scenari seguenti:

- **DNAT** - È possibile convertire più istanze di porta standard per i server back-end. Se ad esempio si dispone di due indirizzi IP pubblici, è possibile convertire la porta TCP 3389 (RDP) per entrambi gli indirizzi IP.
- **SNAT** - Sono disponibili porte aggiuntive per le connessioni SNAT in uscita, riducendo il rischio di esaurimento delle porte SNAT. Al momento, Firewall di Azure seleziona in modo casuale l'indirizzo IP pubblico di origine da usare per una connessione. Se sono presenti filtri downstream nella rete, è necessario consentire tutti gli indirizzi IP pubblici associati al firewall.

## <a name="azure-monitor-logging"></a>Registrazione di Monitoraggio di Azure

Tutti gli eventi vengono integrati con Monitoraggio di Azure, per consentire di archiviare i log in un account di archiviazione e di trasmettere i flussi di eventi all'hub eventi o inviarli ai log di Monitoraggio di Azure.

## <a name="pci-soc-and-iso-compliant"></a>Conforme a PCI, SOC e ISO

Il firewall di Azure è conforme a PCI (Payment Card Industry), SOC (Service Organization Controls) e ISO (International Organization for Standardization). Attualmente supporta SOC 1 tipo 2, SOC 2 tipo 2, SOC 3, PCI DSS e ISO 27001, 27018, 20000-1, 22301, 9001, 27017.

Per altre informazioni, vedere la [Guida alla conformità Microsoft](https://servicetrust.microsoft.com/ViewPage/MSComplianceGuide).

## <a name="known-issues"></a>Problemi noti

Il Firewall di Azure presenta i problemi noti seguenti:

|Problema  |DESCRIZIONE  |Mitigazione  |
|---------|---------|---------|
Le regole di filtro di rete per i protocolli non TCP/UDP (ad esempio ICMP) non funzionano per il traffico associato a Internet|Le regole di filtro di rete per i protocolli non TCP/UDP non funzionano con SNAT per l'indirizzo IP pubblico. I protocolli non TCP/UDP sono supportati tra le subnet spoke e le reti virtuali.|Firewall di Azure usa Load Balancer Standard, [che attualmente non supporta SNAT per i protocolli IP](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview#limitations). Sono in fase di studio opzioni per supportare questo scenario in una versione futura.|
|Supporto di PowerShell e CLI mancante per ICMP|Azure PowerShell e CLI non supportano ICMP come protocollo valido nelle regole di rete.|È comunque possibile usare ICMP come protocollo tramite il portale e l'API REST. A breve ICMP sarà aggiunto anche in PowerShell e nell'interfaccia della riga di comando.|
|I tag FQDN richiedono l'impostazione di protocol:port|Le regole di applicazione con tag FQDN richiedono la definizione port:protocol.|È possibile usare **https** come valore port:protocol. A breve questo campo sarà reso facoltativo quando vengono usati i tag FQDN.|
|Lo spostamento di un firewall in un altro gruppo di risorse o un'altra sottoscrizione non è supportato|Lo spostamento di un firewall in un altro gruppo di risorse o un'altra sottoscrizione non è supportato.|Il supporto di questa funzionalità è previsto per il futuro. Per spostare un firewall in un altro gruppo di risorse o sottoscrizione, è necessario eliminare l'istanza corrente e ricrearla nel nuovo gruppo di risorse o sottoscrizione.|
|Intervallo di porte nelle regole di rete e dell'applicazione|Il numero di porte è limitato a 64.000 perché le porte con numeri elevati sono riservate ai probe di gestione e integrità. |Microsoft sta lavorando per ovviare a questa limitazione.|
|Gli avvisi dell'intelligence per le minacce possono essere mascherati|Le regole di rete con destinazione 80/443 per i filtri in uscita mascherano gli avvisi di intelligence quando sono configurati in modalità di solo avviso.|Creare filtri in uscita per 80/443 usando le regole dell'applicazione. Oppure impostare la modalità di intelligence per le minacce su **Alert and Deny** (Avviso e rifiuto).|
|Il firewall di Azure usa DNS di Azure solo per la risoluzione dei nomi|Il firewall di Azure risolve i nomi di dominio completo usando solo DNS di Azure. Non è supportato un server DNS personalizzato. Questo non influisce in alcun modo sulla risoluzione DNS in altre subnet.|Microsoft sta lavorando per ovviare a questa limitazione.|
|La modalità SNAT/DNAT del Firewall di Azure non funziona per le destinazioni IP private|Il supporto di SNAT/DNAT del Firewall di Azure è limitato al traffico in uscita/in ingresso su Internet. La modalità SNAT/DNAT attualmente non funziona per le destinazioni IP private, ad esempio da spoke a spoke.|Si tratta di una limitazione corrente.|
|Non è possibile rimuovere la configurazione del primo indirizzo IP pubblico|Ogni indirizzo IP pubblico di Firewall di Azure è assegnato a una *configurazione IP*.  La prima configurazione IP viene assegnata durante la distribuzione del firewall e contiene in genere anche un riferimento alla subnet del firewall (a meno che non sia configurata diversamente in modo esplicito tramite una distribuzione modello). Non è possibile eliminare questa configurazione IP perché l'eliminazione comporterebbe la deallocazione del firewall. È comunque possibile modificare o rimuovere l'indirizzo IP pubblico associato a questa configurazione IP se per il firewall esiste almeno un altro indirizzo IP pubblico disponibile per l'uso.|Si tratta di un comportamento previsto da progettazione.|
|Le zone di disponibilità possono essere configurate solo durante la distribuzione.|Le zone di disponibilità possono essere configurate solo durante la distribuzione. Non è possibile configurare le zone di disponibilità dopo aver distribuito un firewall.|Si tratta di un comportamento previsto da progettazione.|
|SNAT sulle connessioni in ingresso|Oltre a DNAT, le connessioni tramite l'indirizzo IP pubblico del firewall (in ingresso) sono inviate tramite SNAT a uno degli indirizzi IP privati del firewall. Questo requisito è attualmente previsto (anche per le appliance virtuali di rete in modalità attivo/attivo) per consentire il routing simmetrico.|Per mantenere l'origine iniziale per HTTP/S, prendere in considerazione l'uso delle intestazioni [XFF](https://en.wikipedia.org/wiki/X-Forwarded-For). Usare ad esempio un servizio come [Frontdoor di Azure](../frontdoor/front-door-http-headers-protocol.md#front-door-service-to-backend) davanti al firewall. È anche possibile aggiungere WAF come parte di Frontdoor di Azure e concatenarlo al firewall.
|Supporto per il filtro FQDN di SQL disponibile solo in modalità proxy (porta 1433)|Per il database SQL di Azure, Azure SQL Data Warehouse e Istanza gestita di SQL di Azure:<br><br>Durante l'anteprima il filtro FQDN di SQL è supportato solo in modalità proxy (porta 1433).<br><br>Per SQL IaaS di Azure:<br><br>Se si usano porte non standard, è possibile specificare tali porte nelle regole dell'applicazione.|Per SQL in modalità di reindirizzamento, che è l'impostazione predefinita se la connessione viene effettuata dall'interno di Azure, è invece possibile filtrare l'accesso usando il tag di servizio SQL nell'ambito delle regole di rete di Firewall di Azure.
|Il traffico in uscita dalla porta TCP 25 non è consentito| Le connessioni SMTP in uscita che usano la porta TCP 25 vengono bloccate. La porta 25 viene usata principalmente per il recapito di posta elettronica non autenticata. Si tratta del comportamento predefinito della piattaforma per le macchine virtuali. Per altre informazioni, vedere [Risolvere i problemi di connettività SMTP in uscita in Azure](../virtual-network/troubleshoot-outbound-smtp-connectivity.md). Tuttavia, a differenza delle macchine virtuali, non è attualmente possibile abilitare questa funzionalità nel firewall di Azure.|Seguire il metodo consigliato per inviare messaggi di posta elettronica come illustrato nell'articolo sulla risoluzione dei problemi di SMTP. In alternativa, escludere la macchina virtuale che necessita dell'accesso SMTP in uscita dalla route predefinita al firewall e configurare invece l'accesso in uscita direttamente in Internet.

## <a name="next-steps"></a>Passaggi successivi

- [Esercitazione: Distribuire e configurare Firewall di Azure tramite il portale di Azure](tutorial-firewall-deploy-portal.md)
- [Distribuire Firewall di Azure con un modello](deploy-template.md)
- [Creare un ambiente di test di Firewall di Azure](scripts/sample-create-firewall-test.md)
