---
title: Informazioni sul firewall di Azure
description: Firewall di Azure è un servizio di sicurezza di rete gestito basato sul cloud che consente di proteggere le risorse della rete virtuale di Azure.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: overview
ms.custom: mvc
ms.date: 06/18/2020
ms.author: victorh
Customer intent: As an administrator, I want to evaluate Azure Firewall so I can determine if I want to use it.
ms.openlocfilehash: 7a5b21551cd549f6a495f6cca7a8c5f96c72ddaa
ms.sourcegitcommit: 971a3a63cf7da95f19808964ea9a2ccb60990f64
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/19/2020
ms.locfileid: "85081010"
---
# <a name="what-is-azure-firewall"></a>Informazioni sul firewall di Azure

![Certificazione ICSA](media/overview/icsa-cert-firewall-small.png)

Firewall di Azure è un servizio di sicurezza di rete gestito basato sul cloud che consente di proteggere le risorse della rete virtuale di Azure. È un firewall con stato completo distribuito come servizio con disponibilità elevata e scalabilità cloud senza limiti.

![Panoramica del firewall](media/overview/firewall-threat.png)

È possibile creare, applicare e registrare criteri di connettività di applicazione e di rete in modo centralizzato tra le sottoscrizioni e le reti virtuali. Firewall di Azure usa un indirizzo IP pubblico statico per le risorse della rete virtuale consentendo ai firewall esterni di identificare il traffico proveniente dalla rete virtuale.  Il servizio è completamente integrato con Monitoraggio di Azure per la registrazione e l'analisi.

## <a name="features"></a>Funzionalità

Per informazioni sulle funzionalità di Firewall di Azure, vedere [Funzionalità di Firewall di Azure](features.md).

## <a name="known-issues"></a>Problemi noti

Il Firewall di Azure presenta i problemi noti seguenti:

|Problema  |Descrizione  |Strategia di riduzione del rischio  |
|---------|---------|---------|
Le regole di filtro di rete per i protocolli non TCP/UDP (ad esempio ICMP) non funzionano per il traffico associato a Internet|Le regole di filtro di rete per i protocolli non TCP/UDP non funzionano con SNAT per l'indirizzo IP pubblico. I protocolli non TCP/UDP sono supportati tra le subnet spoke e le reti virtuali.|Firewall di Azure usa Load Balancer Standard, [che attualmente non supporta SNAT per i protocolli IP](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview). Sono in fase di studio opzioni per supportare questo scenario in una versione futura.|
|Supporto di PowerShell e CLI mancante per ICMP|Azure PowerShell e l'interfaccia della riga di comando non supportano ICMP come protocollo valido nelle regole di rete.|È comunque possibile usare ICMP come protocollo tramite il portale e l'API REST. A breve ICMP sarà aggiunto anche in PowerShell e nell'interfaccia della riga di comando.|
|I tag FQDN richiedono l'impostazione di protocol:port|Le regole di applicazione con tag FQDN richiedono la definizione port:protocol.|È possibile usare **https** come valore port:protocol. A breve questo campo sarà reso facoltativo quando vengono usati i tag FQDN.|
|Lo spostamento di un firewall in un altro gruppo di risorse o un'altra sottoscrizione non è supportato|Lo spostamento di un firewall in un altro gruppo di risorse o un'altra sottoscrizione non è supportato.|Il supporto di questa funzionalità è previsto per il futuro. Per spostare un firewall in un altro gruppo di risorse o sottoscrizione, è necessario eliminare l'istanza corrente e ricrearla nel nuovo gruppo di risorse o sottoscrizione.|
|Gli avvisi dell'intelligence per le minacce possono essere mascherati|Le regole di rete con destinazione 80/443 per i filtri in uscita mascherano gli avvisi di intelligence quando sono configurati in modalità di solo avviso.|Creare filtri in uscita per 80/443 usando le regole dell'applicazione. Oppure impostare la modalità di intelligence per le minacce su **Alert and Deny** (Avviso e rifiuto).|
|Il firewall di Azure usa DNS di Azure solo per la risoluzione dei nomi|Il firewall di Azure risolve i nomi di dominio completo usando solo DNS di Azure. Non è supportato un server DNS personalizzato. Questo non influisce in alcun modo sulla risoluzione DNS in altre subnet.|Microsoft sta lavorando per ovviare a questa limitazione.|
|La modalità DNAT del Firewall di Azure non funziona per le destinazioni IP private|Il supporto di DNAT del Firewall di Azure è limitato al traffico Internet in uscita/in ingresso. La modalità DNAT attualmente non funziona per le destinazioni IP private, ad esempio da spoke a spoke.|Si tratta di una limitazione corrente.|
|Non è possibile rimuovere la configurazione del primo indirizzo IP pubblico|Ogni indirizzo IP pubblico di Firewall di Azure è assegnato a una *configurazione IP*.  La prima configurazione IP viene assegnata durante la distribuzione del firewall e contiene in genere anche un riferimento alla subnet del firewall (a meno che non sia configurata diversamente in modo esplicito tramite una distribuzione modello). Non è possibile eliminare questa configurazione IP perché l'eliminazione comporterebbe la deallocazione del firewall. È comunque possibile modificare o rimuovere l'indirizzo IP pubblico associato a questa configurazione IP se per il firewall esiste almeno un altro indirizzo IP pubblico disponibile per l'uso.|Questo si verifica per motivi strutturali.|
|Le zone di disponibilità possono essere configurate solo durante la distribuzione.|Le zone di disponibilità possono essere configurate solo durante la distribuzione. Non è possibile configurare le zone di disponibilità dopo aver distribuito un firewall.|Questo si verifica per motivi strutturali.|
|SNAT sulle connessioni in ingresso|Oltre a DNAT, le connessioni tramite l'indirizzo IP pubblico del firewall (in ingresso) sono inviate tramite SNAT a uno degli indirizzi IP privati del firewall. Questo requisito è attualmente previsto (anche per le appliance virtuali di rete in modalità attivo/attivo) per consentire il routing simmetrico.|Per mantenere l'origine iniziale per HTTP/S, prendere in considerazione l'uso delle intestazioni [XFF](https://en.wikipedia.org/wiki/X-Forwarded-For). Usare ad esempio un servizio come [Frontdoor di Azure](../frontdoor/front-door-http-headers-protocol.md#front-door-to-backend) o [Gateway applicazione di Azure](../application-gateway/rewrite-http-headers.md) prima del firewall. È anche possibile aggiungere WAF come parte di Frontdoor di Azure e concatenarlo al firewall.
|Supporto per il filtro FQDN di SQL disponibile solo in modalità proxy (porta 1433)|Per il database SQL di Azure, Azure SQL Data Warehouse e Istanza gestita di SQL di Azure:<br><br>Durante l'anteprima il filtro FQDN di SQL è supportato solo in modalità proxy (porta 1433).<br><br>Per SQL IaaS di Azure:<br><br>Se si usano porte non standard, è possibile specificarle nelle regole dell'applicazione.|Per SQL in modalità di reindirizzamento, che è l'impostazione predefinita se la connessione viene effettuata dall'interno di Azure, è invece possibile filtrare l'accesso usando il tag di servizio SQL nell'ambito delle regole di rete di Firewall di Azure.
|Il traffico in uscita dalla porta TCP 25 non è consentito| Le connessioni SMTP in uscita che usano la porta TCP 25 vengono bloccate. La porta 25 viene usata principalmente per il recapito di posta elettronica non autenticata. Si tratta del comportamento predefinito della piattaforma per le macchine virtuali. Per altre informazioni, vedere [Risolvere i problemi di connettività SMTP in uscita in Azure](../virtual-network/troubleshoot-outbound-smtp-connectivity.md). Tuttavia, a differenza delle macchine virtuali, non è attualmente possibile abilitare questa funzionalità nel firewall di Azure. Nota: per consentire SMTP autenticato (porta 587) o SMTP su una porta diversa da 25, assicurarsi di configurare una regola di rete e non una regola dell'applicazione perché l'ispezione SMTP non è al momento supportata.|Seguire il metodo consigliato per inviare messaggi di posta elettronica come illustrato nell'articolo sulla risoluzione dei problemi di SMTP. In alternativa, escludere la macchina virtuale che necessita dell'accesso SMTP in uscita dalla route predefinita al firewall e configurare invece l'accesso in uscita direttamente in Internet.
|FTP attivo non è supportato|FTP attivo è disabilitato nel firewall di Azure per proteggere dagli attacchi di rimbalzo FTP tramite il comando FTP PORT.|In alternativa, è possibile usare FTP passivo. È comunque necessario aprire in modo esplicito le porte TCP 20 e 21 sul firewall.
|Il valore visualizzato dalla metrica di utilizzo delle porte SNAT è 0%|La metrica di utilizzo delle porte SNAT di Firewall di Azure può mostrare un utilizzo dello 0% anche quando vengono usate porte SNAT. In questo caso, l'uso della metrica come parte della metrica di integrità del firewall fornisce un risultato non corretto.|Questo problema è stato risolto e l'implementazione nell'ambiente di produzione è prevista nel mese di maggio 2020. La ridistribuzione del firewall risolve il problema in alcuni casi, ma non in modo coerente. Come soluzione alternativa intermedia, usare lo stato di integrità del firewall per cercare solo lo stato *degradato* e non lo stato *non integro*. L'esaurimento delle porte viene visualizzato come stato *degradato*. Lo stato *non integro* è riservato per un uso futuro, quando saranno disponibili più metriche di impatto per l'integrità del firewall.
|DNAT non è supportato con il tunneling forzato abilitato|I firewall distribuiti con il tunneling forzato abilitato non supportano l'accesso in ingresso da Internet a causa del routing simmetrico.|Si tratta di una scelta per progettazione. Il percorso di ritorno per le connessioni in ingresso passa attraverso il firewall locale, che non ha visto la connessione stabilita.
|L’FTP passivo in uscita non funziona per i firewall con più indirizzi IP pubblici|FTP passivo stabilisce connessioni diverse per canali di controllo e dei dati. Quando un firewall con più indirizzi IP pubblici invia dati in uscita, seleziona in modo casuale uno degli indirizzi IP pubblici come indirizzo IP di origine. FTP ha esito negativo quando i canali di controllo e dei dati usano indirizzi IP di origine diversi.|È stata pianificata una configurazione SNAT esplicita. Nel frattempo, è consigliabile usare un singolo indirizzo IP in questa situazione.|
|Per la metrica NetworkRuleHit manca una dimensione del protocollo|La metrica ApplicationRuleHit consente il protocollo basato su filtro, ma questa funzionalità non è presente nella metrica NetworkRuleHit corrispondente.|È in corso la ricerca di una correzione.|
|Le regole NAT con porte comprese tra 64000 e 65535 non sono supportate|Firewall di Azure consente l'uso di qualsiasi porta compresa nell'intervallo 1-65535 nelle regole di rete e dell'applicazione, tuttavia le regole NAT supportano solo le porte comprese nell'intervallo 1-63999.|Si tratta di una limitazione corrente.
|Gli aggiornamenti della configurazione possono richiedere in media cinque minuti|Un aggiornamento della configurazione di Firewall di Azure può richiedere in media da tre a cinque minuti e gli aggiornamenti paralleli non sono supportati.|È in corso la ricerca di una correzione.|
|Firewall di Azure usa le intestazioni TLS di SNI per filtrare il traffico HTTPS e MSSQL|Se il software del browser o del server non supporta l'estensione SNI (Server Name Indicator), non sarà possibile connettersi tramite Firewall di Azure.|Se il software del browser o del server non supporta SNI, potrebbe essere possibile controllare la connessione utilizzando una regola di rete anziché una regola dell'applicazione. Vedere [Indicazione nome server](https://wikipedia.org/wiki/Server_Name_Indication) per il software che supporta SNI.

## <a name="next-steps"></a>Passaggi successivi

- [Esercitazione: Distribuire e configurare Firewall di Azure tramite il portale di Azure](tutorial-firewall-deploy-portal.md)
- [Distribuire Firewall di Azure con un modello](deploy-template.md)
- [Creare un ambiente di test di Firewall di Azure](scripts/sample-create-firewall-test.md)
