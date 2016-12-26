---
title: OMS di Azure per enti pubblici | Documentazione Microsoft
description: "Questo articolo illustra lo scenario in cui OMS è applicabile ai provider di soluzioni e agli enti pubblici degli Stati Uniti"
services: Azure-Government
cloud: gov
documentationcenter: 
author: sacha
manager: jobruno
editor: 
translationtype: Human Translation
ms.sourcegitcommit: ccbcf15eaf9b13625338ea1d66554eae7dc7d2eb
ms.openlocfilehash: 0d010bb21d807239da37172bc2885ddadc5c8028

---

# <a name="azure-government-cybersecurity-monitoring-and-securing-your-assets-with-operations-management-suite-oms"></a>Sicurezza informatica di Azure per enti pubblici: monitoraggio e protezione degli asset con Operations Management Suite (OMS)

## <a name="cybersecurity-in-the-cloud"></a>Sicurezza informatica nel cloud
Un aspetto importante per i clienti che passano al cloud è la possibilità di mantenere la gestione e la sicurezza degli asset dei servizi di Azure per enti pubblici distribuiti nel cloud. I firewall delle macchine virtuali devono essere configurati correttamente. Alle reti virtuali devono essere applicati i gruppi di sicurezza di rete appropriati. L'accesso agli asset deve essere bloccato per determinate persone al momento giusto. Questi sono i flussi di lavoro necessari di cui si deve effettuare la pianificazione, la progettazione e il provisioning per consentire all'ente di usare un'infrastruttura sicura.

La configurazione di un tale ambiente può risultare complessa. L'onboarding dell'insieme di server in un servizio di monitoraggio è difficile da ridimensionare e da aggiornare. Il monitoraggio dell'infrastruttura in provider di servizi cloud diversi, ma anche nel cloud e in locale è complicato. Infine, tenere aggiornato il sistema di monitoraggio e abilitare le informazioni dettagliate per monitorare, rilevare, generare avvisi e prendere provvedimenti contro le minacce alla sicurezza informatica richiede tempo, risorse e potenza di calcolo.

## <a name="microsoft-operations-management-suite-oms"></a>Microsoft Operations Management Suite (OMS)
Microsoft Operations Management Suite, ora disponibile in Azure per enti pubblici, è un servizio che consente di eseguire tutte queste operazioni sfruttando le funzionalità di MapReduce e di Machine Learning come servizio. OMS può:
* Distribuire agenti in singole VM (Linux e Windows) in Azure, in altri provider di servizi cloud e/o in locale.
* Connettere i log esistenti tramite un account di archiviazione di Azure per enti pubblici o un endpoint SCOM ai dati di registrazione esistenti.
* Eseguire servizi di Machine Learning e MapReduce classici con tecnologia di ricerca nei log con iperscalabilità per esporre immediatamente le minacce all'ambiente.

Verrà ora spiegato come integrare OMS nei server e verranno illustrate alcune soluzioni predefinite per risolvere le problematiche di cui si è parlato.

## <a name="onboarding-servers-to-oms"></a>Onboarding di server in OMS
La prima operazione da eseguire per integrare gli asset cloud con Operations Management Suite è l'installazione dell'agente OMS nelle origini dei log.
Per le macchine virtuali è sufficiente scaricare manualmente l'agente dall'area di lavoro di OMS.

![Figura 1: Server Windows connessi a OMS](./media/documentation-government-oms-figure1.png)
<p align="center">Figura 1: Server Windows connessi a OMS</p>

È possibile connettere le VM di Azure a OMS direttamente nel portale. Vedere le istruzioni [qui](https://blogs.technet.microsoft.com/momteam/2016/02/10/new-ways-to-enable-log-analytics-oms-on-your-azure-vms/).

È anche possibile connetterle a livello di codice e/o configurare l'estensione OMS nei modelli di Azure Resource Manager. Vedere le istruzioni per i computer basati su Windows [qui](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-windows-agents) e per quelli basati su Linux [qui](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-linux-agents).

## <a name="onboarding-storage-accounts-and-scom-to-oms"></a>Onboarding di account di archiviazione e SCOM in OMS
OMS può anche connettersi all'account di archiviazione e/o alle distribuzioni di SCOM 2013 esistenti per consentire la gestione delle operazioni in scenari ibridi (in provider di servizi cloud o in infrastrutture cloud/locali).

![Figura 2: Connessione di Archiviazione di Azure e SCOM a OMS](./media/documentation-government-oms-figure2.png)
<p align="center">Figura 2: Connessione di Archiviazione di Azure e SCOM a OMS</p>

OMS supporta anche la registrazione delle informazioni da altri servizi di monitoraggio, ad esempio Chef e/o Puppet. Per le distribuzioni di Azure sono anche disponibili modelli di Azure Resource Manager abilitati per le VM con OMS per poter distribuire la funzionalità di calcolo ed eseguire l'onboarding nell'area di lavoro di OMS contemporaneamente. 

![Figura 3: Modelli di Azure Resource Manager per VM di Azure con l'estensione OMS](./media/documentation-government-oms-figure3a.png)
![Figura 3: Modelli di Azure Resource Manager per VM di Azure con l'estensione OMS](./media/documentation-government-oms-figure3b.png)
<p align="center">Figura 3: Modelli di Azure Resource Manager per VM di Azure con l'estensione OMS</p>

Le informazioni sulla configurazione di OMS con l'implementazione di SCOM esistente in locale sono disponibili [qui](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-om-agents).

## <a name="leveraging-intelligence-through-oms-solution-packs"></a>Uso delle informazioni tramite i pacchetti della soluzione OMS
Quando sono a disposizione diverse origini per la registrazione di dati, si presenta un altro problema: dare un senso a tutti questi dati.
OMS è fondamentalmente un servizio di ricerca nei log che consente di scrivere query avanzate per eseguire rapidamente ricerche in migliaia o addirittura in milioni di log, ma individuare i problemi per cui è necessario scrivere query è molto difficile.

Immettere le soluzioni OMS: pacchetti di query integrati in modo nativo con la tecnologia di MapReduce e Machine Learning per OMS che offrono proattivamente informazioni dettagliate sui server gestiti OMS.

Nell'ambito della sicurezza informatica, verranno brevemente illustrati tre scenari che OMS può immediatamente risolvere.

###<a name="antimalware-assessment"></a>Antimalware Assessment
Antimalware Assessment offre un set predefinito di query, notifiche e dashboard di monitoraggio, che consente di verificare immediatamente il livello di protezione dei server contro il malware.

Questo dashboard visualizza 4 elementi:
* I server con minacce attive e/o con correzione.
* Minacce attualmente rilevate.
* Computer non sufficientemente protetti. A questo scopo, OMS effettua una ricerca per indicizzazione nei log dei computer e cerca i siti di FW aperti e/o regole non correttamente configurate nei Web browser comuni.
* Analisi per stabilire come vengono protetti i server, ad esempio con la protezione antivirus per i sistemi operativi Windows nativi e/o con Endpoint Protection di System Center o una soluzione simile.

La figura seguente, ad esempio, illustra una minaccia rilevata e automaticamente valutata da System Center:

![Figura 4: Soluzione Antimalware Assessment di OMS](./media/documentation-government-oms-figure4.png)
<p align="center">Figura 4: Soluzione Antimalware Assessment di OMS</p>

Altre informazioni su Antimalware Assessment sono disponibili all'indirizzo [https://azure.microsoft.com/en-us/documentation/articles/log-analytics-malware/](https://azure.microsoft.com/en-us/documentation/articles/log-analytics-malware/).

### <a name="identity-and-access"></a>Identità e accesso
Un altro comune scenario di sicurezza informatica nel cloud è quello relativo alla violazione delle credenziali. Non solo la sottoscrizione cloud ha le credenziali, ma a ogni singola VM sono associati un utente e/o un segreto (in genere un certificate e/o una password).

OMS sommerà e organizzerà tutti i tentativi di accesso ai server e li raggrupperà a seconda del tipo (remoto, locale, nome utente usato e così via). Nella figura seguente, ad esempio, si può osservare una considerevole quantità di tentativi di accesso non riusciti da stringhe principalmente casuali come nome utente. Questo indica che molto probabilmente i computer sono esposti e non correttamente protetti da firewall ed elenchi di controllo di accesso.

![Figura 5: 97,3% degli accessi non riusciti nelle ultime 24 ore](./media/documentation-government-oms-figure5.png)
<p align="center">Figura 5: 97,3% degli accessi non riusciti nelle ultime 24 ore</p>

### <a name="threat-intelligence"></a>Intelligence per le minacce
OMS offre anche protezione per gli scenari relativi a utenti interni malintenzionati, in cui si è verificata una violazione della sicurezza nell'organizzazione e un utente malintenzionato sta cercando di sottrarre dati.

L'intelligence per le minacce di OMS esamina tutti i log di rete nel computer e automaticamente cerca e invia notifiche sulle connessioni di rete in ingresso/in uscita a IP dannosi noti, ad esempio indirizzi IP nella darknet non indicizzata.
Nella figura seguente, ad esempio, si possono osservare connessioni di rete sia in ingresso che in uscita con la Repubblica popolare cinese. 

Facendo doppio clic sul tag in ingresso, si può osservare che una VM Linux gestita da OMS stabilisce connessioni in uscita a un IP di una darknet nota in Cina.

È anche possibile configurare avvisi per le soluzioni OMS, ad esempio Intelligence per le minacce. Di seguito è stato configurato un avviso in modo che OMS, se rileva più di 10 connessioni in uscita a un IP dannoso noto, invii un avviso tramite posta elettronica. Si configura quindi un avviso per generare un processo di Automazione di Azure configurato per arrestare automaticamente tale VM.

![Figura 6: Avvisi di OMS e Automazione](./media/documentation-government-oms-figure6.png)
<p align="center">Figura 6: Avvisi di OMS e Automazione</p>

Questo è solo un esempio di soluzione OMS predefinita che può essere applicata ai server, indipendentemente dal fatto che vengano eseguiti in Azure, in un altro provider di servizi cloud o in locale.
OMS continuerà ad aggiornare automaticamente la tecnologia di apprendimento automatico in base alle minacce più recenti e si continuerà anche a implementare nuove soluzioni nella raccolta soluzioni di OMS.

Per altre informazioni su OMS, vedere la pagina della documentazione all'indirizzo [https://azure.microsoft.com/en-us/documentation/articles/documentation-government-overview/](https://azure.microsoft.com/en-us/documentation/articles/documentation-government-overview/) 



<!--HONumber=Nov16_HO4-->


