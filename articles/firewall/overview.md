---
title: Informazioni sul firewall di Azure
description: Firewall di Azure è un servizio di sicurezza di rete gestito basato sul cloud che consente di proteggere le risorse della rete virtuale di Azure.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: overview
ms.custom: mvc, contperfq1
ms.date: 11/10/2020
ms.author: victorh
Customer intent: As an administrator, I want to evaluate Azure Firewall so I can determine if I want to use it.
ms.openlocfilehash: e714e88e47ec20adec44a104c659d03e62d8010a
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94658384"
---
# <a name="what-is-azure-firewall"></a>Informazioni sul firewall di Azure

![Certificazione ICSA](media/overview/icsa-cert-firewall-small.png)

Firewall di Azure è un servizio di sicurezza di rete gestito basato sul cloud che consente di proteggere le risorse della rete virtuale di Azure. È un firewall con stato completo distribuito come servizio con disponibilità elevata e scalabilità cloud senza limiti.

![Panoramica del firewall](media/overview/firewall-threat.png)

È possibile creare, applicare e registrare criteri di connettività di applicazione e di rete in modo centralizzato tra le sottoscrizioni e le reti virtuali. Firewall di Azure usa un indirizzo IP pubblico statico per le risorse della rete virtuale consentendo ai firewall esterni di identificare il traffico proveniente dalla rete virtuale.  Il servizio è completamente integrato con Monitoraggio di Azure per la registrazione e l'analisi.

## <a name="features"></a>Funzionalità

Per informazioni sulle funzionalità di Firewall di Azure, vedere [Funzionalità di Firewall di Azure](features.md).

## <a name="pricing-and-sla"></a>Prezzi e contratto di servizio

Per informazioni sui prezzi di Firewall di Azure, vedere [Prezzi di Firewall di Azure](https://azure.microsoft.com/pricing/details/azure-firewall/).

Per informazioni sul contratto di servizio di Firewall di Azure, vedere [Contratto di servizio di Firewall di Azure](https://azure.microsoft.com/support/legal/sla/azure-firewall/).

## <a name="whats-new"></a>Novità

Per informazioni sulle novità di Firewall di Azure, vedere [Aggiornamenti di Azure](https://azure.microsoft.com/updates/?category=networking&query=Azure%20Firewall).


## <a name="known-issues"></a>Problemi noti

Il Firewall di Azure presenta i problemi noti seguenti:

|Problema  |Descrizione  |Strategia di riduzione del rischio  |
|---------|---------|---------|
Le regole di filtro di rete per i protocolli non TCP/UDP (ad esempio ICMP) non funzionano per il traffico associato a Internet|Le regole di filtro di rete per i protocolli non TCP/UDP non funzionano con SNAT per l'indirizzo IP pubblico. I protocolli non TCP/UDP sono supportati tra le subnet spoke e le reti virtuali.|Firewall di Azure usa Load Balancer Standard, [che attualmente non supporta SNAT per i protocolli IP](../load-balancer/load-balancer-overview.md). Sono in fase di studio opzioni per supportare questo scenario in una versione futura.|
|Supporto di PowerShell e CLI mancante per ICMP|Azure PowerShell e l'interfaccia della riga di comando non supportano ICMP come protocollo valido nelle regole di rete.|È comunque possibile usare ICMP come protocollo tramite il portale e l'API REST. A breve ICMP sarà aggiunto anche in PowerShell e nell'interfaccia della riga di comando.|
|I tag FQDN richiedono l'impostazione di protocol:port|Le regole di applicazione con tag FQDN richiedono la definizione port:protocol.|È possibile usare **https** come valore port:protocol. A breve questo campo sarà reso facoltativo quando vengono usati i tag FQDN.|
|Lo spostamento di un firewall in un altro gruppo di risorse o un'altra sottoscrizione non è supportato|Lo spostamento di un firewall in un altro gruppo di risorse o un'altra sottoscrizione non è supportato.|Il supporto di questa funzionalità è previsto per il futuro. Per spostare un firewall in un altro gruppo di risorse o sottoscrizione, è necessario eliminare l'istanza corrente e ricrearla nel nuovo gruppo di risorse o sottoscrizione.|
|Gli avvisi dell'intelligence per le minacce possono essere mascherati|Le regole di rete con destinazione 80/443 per i filtri in uscita mascherano gli avvisi di intelligence quando sono configurati in modalità di solo avviso.|Creare filtri in uscita per 80/443 usando le regole dell'applicazione. Oppure impostare la modalità di intelligence per le minacce su **Alert and Deny** (Avviso e rifiuto).|
|La modalità DNAT del Firewall di Azure non funziona per le destinazioni IP private|Il supporto di DNAT del Firewall di Azure è limitato al traffico Internet in uscita/in ingresso. La modalità DNAT attualmente non funziona per le destinazioni IP private, ad esempio da spoke a spoke.|Si tratta di una limitazione corrente.|
|Non è possibile rimuovere la configurazione del primo indirizzo IP pubblico|Ogni indirizzo IP pubblico di Firewall di Azure è assegnato a una *configurazione IP*.  La prima configurazione IP viene assegnata durante la distribuzione del firewall e contiene in genere anche un riferimento alla subnet del firewall (a meno che non sia configurata diversamente in modo esplicito tramite una distribuzione modello). Non è possibile eliminare questa configurazione IP perché l'eliminazione comporterebbe la deallocazione del firewall. È comunque possibile modificare o rimuovere l'indirizzo IP pubblico associato a questa configurazione IP se per il firewall esiste almeno un altro indirizzo IP pubblico disponibile per l'uso.|Questo si verifica per motivi strutturali.|
|Le zone di disponibilità possono essere configurate solo durante la distribuzione.|Le zone di disponibilità possono essere configurate solo durante la distribuzione. Non è possibile configurare le zone di disponibilità dopo aver distribuito un firewall.|Questo si verifica per motivi strutturali.|
|SNAT sulle connessioni in ingresso|Oltre a DNAT, le connessioni tramite l'indirizzo IP pubblico del firewall (in ingresso) sono inviate tramite SNAT a uno degli indirizzi IP privati del firewall. Questo requisito è attualmente previsto (anche per le appliance virtuali di rete in modalità attivo/attivo) per consentire il routing simmetrico.|Per mantenere l'origine iniziale per HTTP/S, prendere in considerazione l'uso delle intestazioni [XFF](https://en.wikipedia.org/wiki/X-Forwarded-For). Usare ad esempio un servizio come [Frontdoor di Azure](../frontdoor/front-door-http-headers-protocol.md#front-door-to-backend) o [Gateway applicazione di Azure](../application-gateway/rewrite-http-headers.md) prima del firewall. È anche possibile aggiungere WAF come parte di Frontdoor di Azure e concatenarlo al firewall.
|Supporto per il filtro FQDN di SQL disponibile solo in modalità proxy (porta 1433)|Per Database SQL di Azure, Azure Synapse Analytics e Istanza gestita di SQL di Azure:<br><br>Il filtro FQDN di SQL è supportato solo in modalità proxy (porta 1433).<br><br>Per SQL IaaS di Azure:<br><br>Se si usano porte non standard, è possibile specificarle nelle regole dell'applicazione.|Per SQL in modalità di reindirizzamento, che è l'impostazione predefinita se la connessione viene effettuata dall'interno di Azure, è invece possibile filtrare l'accesso usando il tag di servizio SQL nell'ambito delle regole di rete di Firewall di Azure.
|Il traffico in uscita dalla porta TCP 25 non è consentito| Le connessioni SMTP in uscita che usano la porta TCP 25 vengono bloccate. La porta 25 viene usata principalmente per il recapito di posta elettronica non autenticata. Si tratta del comportamento predefinito della piattaforma per le macchine virtuali. Per altre informazioni, vedere [Risolvere i problemi di connettività SMTP in uscita in Azure](../virtual-network/troubleshoot-outbound-smtp-connectivity.md). Tuttavia, a differenza delle macchine virtuali, non è attualmente possibile abilitare questa funzionalità nel firewall di Azure. Nota: per consentire SMTP autenticato (porta 587) o SMTP su una porta diversa da 25, assicurarsi di configurare una regola di rete e non una regola dell'applicazione perché l'ispezione SMTP non è al momento supportata.|Seguire il metodo consigliato per inviare messaggi di posta elettronica come illustrato nell'articolo sulla risoluzione dei problemi di SMTP. In alternativa, escludere la macchina virtuale che necessita dell'accesso SMTP in uscita dalla route predefinita al firewall e configurare invece l'accesso in uscita direttamente in Internet.
|FTP attivo non è supportato|FTP attivo è disabilitato nel firewall di Azure per proteggere dagli attacchi di rimbalzo FTP tramite il comando FTP PORT.|In alternativa, è possibile usare FTP passivo. È comunque necessario aprire in modo esplicito le porte TCP 20 e 21 sul firewall.
|Il valore visualizzato dalla metrica di utilizzo delle porte SNAT è 0%|La metrica di utilizzo delle porte SNAT di Firewall di Azure può mostrare un utilizzo dello 0% anche quando vengono usate porte SNAT. In questo caso, l'uso della metrica come parte della metrica di integrità del firewall fornisce un risultato non corretto.|Questo problema è stato risolto e l'implementazione nell'ambiente di produzione è prevista nel mese di maggio 2020. La ridistribuzione del firewall risolve il problema in alcuni casi, ma non in modo coerente. Come soluzione alternativa intermedia, usare lo stato di integrità del firewall per cercare solo lo stato *degradato* e non lo stato *non integro*. L'esaurimento delle porte viene visualizzato come stato *degradato*. Lo stato *non integro* è riservato per un uso futuro, quando saranno disponibili più metriche di impatto per l'integrità del firewall.
|DNAT non è supportato con il tunneling forzato abilitato|I firewall distribuiti con il tunneling forzato abilitato non supportano l'accesso in ingresso da Internet a causa del routing simmetrico.|Si tratta di una scelta per progettazione. Il percorso di ritorno per le connessioni in ingresso passa attraverso il firewall locale, che non ha visto la connessione stabilita.
|FTP passivo in uscita potrebbe non funzionare per i firewall con più indirizzi IP pubblici, a seconda della configurazione del server FTP.|FTP passivo stabilisce connessioni diverse per canali di controllo e dei dati. Quando un firewall con più indirizzi IP pubblici invia dati in uscita, seleziona in modo casuale uno degli indirizzi IP pubblici come indirizzo IP di origine. Potrebbe verificarsi un errore FTP quando i canali di controllo e dei dati usano indirizzi IP di origine diversi, a seconda della configurazione del server FTP.|È stata pianificata una configurazione SNAT esplicita. Nell'attesa, è possibile configurare il server FTP in modo che accetti i canali di dati e di controllo da indirizzi IP di origine diversi (vedere un [esempio per IIS](/iis/configuration/system.applicationhost/sites/sitedefaults/ftpserver/security/datachannelsecurity)). In alternativa, è consigliabile usare un solo indirizzo IP in questa situazione.|
|FTP passivo in entrata potrebbe non funzionare a seconda della configurazione del server FTP. |FTP passivo stabilisce connessioni diverse per canali di controllo e dei dati. Le connessioni in ingresso su Firewall di Azure sono inviate tramite SNAT a uno degli indirizzi IP privati del firewall per assicurare il routing simmetrico. Potrebbe verificarsi un errore FTP quando i canali di controllo e dei dati usano indirizzi IP di origine diversi, a seconda della configurazione del server FTP.|La possibilità di mantenere l'indirizzo IP di origine originale è in fase di analisi. Nel frattempo, è possibile configurare il server FTP in modo che accetti i canali di dati e di controllo da indirizzi IP di origine diversi.|
|Per la metrica NetworkRuleHit manca una dimensione del protocollo|La metrica ApplicationRuleHit consente il protocollo basato su filtro, ma questa funzionalità non è presente nella metrica NetworkRuleHit corrispondente.|È in corso la ricerca di una correzione.|
|Le regole NAT con porte comprese tra 64000 e 65535 non sono supportate|Firewall di Azure consente l'uso di qualsiasi porta compresa nell'intervallo 1-65535 nelle regole di rete e dell'applicazione, tuttavia le regole NAT supportano solo le porte comprese nell'intervallo 1-63999.|Si tratta di una limitazione corrente.
|Gli aggiornamenti della configurazione possono richiedere in media cinque minuti|Un aggiornamento della configurazione di Firewall di Azure può richiedere in media da tre a cinque minuti e gli aggiornamenti paralleli non sono supportati.|È in corso la ricerca di una correzione.|
|Firewall di Azure usa le intestazioni TLS di SNI per filtrare il traffico HTTPS e MSSQL|Se il software del browser o del server non supporta l'estensione SNI (Server Name Indicator), non sarà possibile connettersi tramite Firewall di Azure.|Se il software del browser o del server non supporta SNI, potrebbe essere possibile controllare la connessione utilizzando una regola di rete anziché una regola dell'applicazione. Vedere [Indicazione nome server](https://wikipedia.org/wiki/Server_Name_Indication) per il software che supporta SNI.|
|Il DNS personalizzato non supporta il tunneling forzato|Se il tunneling forzato è abilitato, il DNS personalizzato non funziona.|È in corso la ricerca di una correzione.|
|Supporto del nuovo indirizzo IP pubblico per più zone di disponibilità|Non è possibile aggiungere un nuovo indirizzo IP pubblico quando si distribuisce un firewall con due zona di disponibilità (1 e 2, 2 e 3 o 1 e 3)|Questa è una limitazione della risorsa indirizzo IP pubblico.|
|L'avvio/arresto non funziona con un firewall configurato in modalità di tunneling forzato|L'avvio/arresto non funziona con Firewall di Azure configurato in modalità di tunneling forzato. Se si tenta di avviare Firewall di Azure con il tunneling forzato configurato, si verifica l'errore seguente:<br><br>*Set-AzFirewall: Non è possibile aggiungere la configurazione IP di gestione del Firewall di Azure FW-xx a un firewall esistente. Per usare il supporto del tunneling forzato, ridistribuire con una configurazione IP di gestione.<br>StatusCode: 400<br>ReasonPhrase: Richiesta non valida*|In fase di analisi.<br><br>Come soluzione alternativa, è possibile eliminare il firewall esistente e crearne uno nuovo con gli stessi parametri.|
|Non è possibile aggiungere tag dei criteri firewall usando il portale|I criteri di Firewall di Azure hanno una limitazione del supporto delle patch che impedisce di aggiungere un tag usando il portale di Azure. Viene generato l'errore seguente: *Non è stato possibile salvare i tag per la risorsa*.|È in corso la ricerca di una correzione. In alternativa, è possibile usare il cmdlet `Set-AzFirewallPolicy` di Azure PowerShell per aggiornare i tag.


## <a name="next-steps"></a>Passaggi successivi

- [Esercitazione: Distribuire e configurare Firewall di Azure tramite il portale di Azure](tutorial-firewall-deploy-portal.md)
- [Distribuire Firewall di Azure con un modello](deploy-template.md)
- [Creare un ambiente di test di Firewall di Azure](scripts/sample-create-firewall-test.md)