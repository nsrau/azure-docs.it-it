---
title: Punteggio sicuro migliorato (anteprima) nel centro sicurezza di Azure
description: Descrizione del Punteggio sicuro migliorato (anteprima) e dei controlli di sicurezza nel centro sicurezza di Azure
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetd: c42d02e4-201d-4a95-8527-253af903a5c6
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/04/2019
ms.author: memildin
ms.openlocfilehash: 8f0b7b1225d78c7626c5be3e68c3dcd2dd557aa3
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/16/2020
ms.locfileid: "76158156"
---
# <a name="the-enhanced-secure-score-preview"></a>Punteggio sicuro migliorato (anteprima) 

Questo articolo presenta il miglior punteggio sicuro (attualmente in anteprima), i controlli di sicurezza associati e i vantaggi offerti.

## <a name="introduction-to-secure-score"></a>Introduzione al Punteggio sicuro

Il Centro sicurezza di Azure ha due obiettivi principali: per comprendere la situazione di sicurezza attuale e per contribuire a migliorare in modo efficiente e efficace la sicurezza. L'aspetto centrale del Centro sicurezza che consente di raggiungere questi obiettivi è un punteggio sicuro.

Il Centro sicurezza valuta continuamente le risorse, le sottoscrizioni e l'organizzazione per i problemi di sicurezza. Quindi aggrega tutti i risultati in un singolo punteggio, in modo da poter indicare, a colpo d'occhio, la situazione di sicurezza attuale: maggiore è il punteggio, minore è il livello di rischio identificato. Usare il punteggio per tenere traccia dei progetti e delle attività di sicurezza dell'organizzazione. 

Il Punteggio sicuro *migliorato* , attualmente in anteprima, è **incentrato sulla superficie di attacco** e offre tre vantaggi:

- Controlli di sicurezza: le raccomandazioni sulla sicurezza sono ora raggruppate in set logici che riflettono meglio le superfici di attacco vulnerabili. Per altre informazioni, vedere [come viene calcolato il Punteggio sicuro](secure-score-security-controls.md#how-the-secure-score-is-calculated) di seguito.

- Il Punteggio complessivo riflette meglio la postura complessiva a cui sono stati assegnati i punti a livello di raccomandazione. Con questo miglioramento, il Punteggio migliorerà solo quando si correggeranno *tutti* i consigli per una singola risorsa all'interno di un controllo. Ciò significa che il punteggio è migliore solo quando la sicurezza di una risorsa migliora. 

- Lo stato di sicurezza delle singole superfici di attacco è più visibile, mostrando il punteggio per controllo di sicurezza, la pagina Punteggio sicuro diventa la posizione in cui è possibile ottenere una visualizzazione granulare del modo in cui l'organizzazione protegge ogni singola superficie di attacco.

Il Punteggio sicuro migliorato viene visualizzato come percentuale, come illustrato nello screenshot seguente:

[![il Punteggio sicuro avanzato (anteprima) include ora una percentuale](media/secure-score-security-controls/secure-score-with-percentage.png)](media/secure-score-security-controls/secure-score-with-percentage.png#lightbox)


## <a name="locating-your-secure-score"></a>Individuazione del Punteggio sicuro

Il Centro sicurezza Visualizza il punteggio in evidenza: è il primo elemento visualizzato nella pagina panoramica. Se si fa clic sulla pagina del Punteggio sicuro dedicato, verrà visualizzato il Punteggio suddiviso in base alla sottoscrizione. Fare clic su una singola sottoscrizione per visualizzare l'elenco dettagliato delle raccomandazioni con priorità e il potenziale impatto che la correzione avrà sul punteggio della sottoscrizione.

## <a name="how-the-secure-score-is-calculated"></a>Modalità di calcolo del Punteggio sicuro 

Prima di questa versione di anteprima, il Centro sicurezza considerava ciascuna raccomandazione singolarmente e ne assegnava un valore in base alla gravità. I team addetti alla sicurezza che lavorano per migliorare la loro postura di sicurezza hanno dovuto assegnare priorità alle risposte alle raccomandazioni del Centro sicurezza in base all'elenco completo dei risultati. Ogni volta che è stata risolta una raccomandazione per una singola risorsa, il Punteggio sicuro è stato migliorato.

Come parte dei miglioramenti apportati al Punteggio sicuro, i suggerimenti vengono ora raggruppati in **controlli di sicurezza**. Un controllo è un set di raccomandazioni per la sicurezza e le istruzioni che consentono di implementare tali raccomandazioni. I controlli sono raggruppamenti logici di consigli correlati. I punti non vengono più assegnati a livello di raccomandazione. Al contrario, il Punteggio migliorerà solo quando si correggono *tutte* le raccomandazioni per una singola risorsa all'interno di un controllo.

Il contributo di ogni controllo di sicurezza verso il Punteggio sicuro complessivo viene visualizzato chiaramente nella pagina raccomandazioni.

[![il Punteggio sicuro avanzato (anteprima) introduce i controlli di sicurezza](media/secure-score-security-controls/security-controls.png)](media/secure-score-security-controls/security-controls.png#lightbox)

Per ottenere tutti i punti possibili per un controllo di sicurezza, tutte le risorse devono essere conformi a tutte le raccomandazioni di sicurezza all'interno del controllo di sicurezza. Ad esempio, il Centro sicurezza offre più raccomandazioni su come proteggere le porte di gestione. In passato, era possibile correggere alcune di queste raccomandazioni correlate e interdipendenti lasciando inalterate le altre soluzioni e il Punteggio sicuro sarebbe migliorato. Quando si esaminano gli obiettivi, è facile affermare che la sicurezza non è stata migliorata fino a quando non sono stati risolti tutti. A questo punto, è necessario risolverli tutti per distinguere il Punteggio sicuro.

Ad esempio, il controllo di sicurezza denominato "Applica aggiornamenti del sistema" ha un punteggio massimo di sei punti, che è possibile visualizzare nella descrizione comando sul valore potenziale di aumento del controllo:

[![il controllo di sicurezza "Applica aggiornamenti del sistema"](media/secure-score-security-controls/apply-system-updates-control.png)](media/secure-score-security-controls/apply-system-updates-control.png#lightbox)

Il valore per il controllo di sicurezza "Applica aggiornamenti del sistema" nella schermata precedente Mostra "2% (1 punto)". Ciò significa che se si esegue il monitoraggio e l'aggiornamento di tutte le raccomandazioni in questo controllo, il punteggio aumenterà del 2% (che in questo caso è un punto). Per semplicità, i valori nella colonna "aumento potenziale" dell'elenco di raccomandazioni vengono arrotondati a numeri interi. Le descrizioni comandi mostrano i valori esatti:

* **Potenziale aumento** : i punti rimanenti disponibili all'interno del controllo. Per aggiungere questi punti al Punteggio sicuro, correggere tutte le raccomandazioni del controllo. Nell'esempio precedente, l'unico punto visualizzato per il controllo è effettivamente 0,96 punti.
* **Punteggio corrente** : punteggio corrente per questo controllo. Ogni controllo contribuisce a raggiungere il punteggio totale. In questo esempio, il controllo contribuisce a 5,04 punti al totale. 
* **Punteggio massimo** : somma dei due valori precedenti.

### <a name="calculations---understanding-your-score"></a>Calcoli-comprensione del Punteggio

|Metrica|Formula ed esempio|
|-|-|
|**Punteggio corrente del controllo di sicurezza**|<br>![equazione per il calcolo del punteggio corrente di un controllo di sicurezza](media/secure-score-security-controls/security-control-scoring-equation.png)<br><br>Ogni singolo controllo di sicurezza contribuisce a raggiungere il Punteggio di sicurezza. Ogni risorsa interessata da una raccomandazione all'interno del controllo contribuisce alla valutazione del punteggio corrente del controllo. Il punteggio corrente per ogni controllo è una misura dello stato delle risorse *all'interno* del controllo.<br>![descrizioni comando che mostrano i valori utilizzati durante il calcolo del punteggio corrente del controllo di sicurezza](media/secure-score-security-controls/security-control-scoring-tooltips.png)<br>In questo esempio, il punteggio massimo di 6 verrebbe diviso per 78 perché è la somma delle risorse integre e non integre.<br>6/78 = 0,0769<br>Il punteggio corrente viene moltiplicato per il numero di risorse integre (74):<br>0,0769 * 74 = **5,69**<br><br>|
|**Punteggio di sicurezza**<br>Singola sottoscrizione|<br>![Equazione per il calcolo del Punteggio sicuro corrente](media/secure-score-security-controls/secure-score-equation.png)<br><br>![Punteggio sicuro di una singola sottoscrizione con tutti i controlli abilitati](media/secure-score-security-controls/secure-score-example-single-sub.png)<br>In questo esempio è presente una singola sottoscrizione con tutti i controlli di sicurezza disponibili (un punteggio massimo potenziale di 60 punti). Il Punteggio Mostra 28 punti di un possibile 60 e i restanti 32 punti vengono riflessi nelle cifre "potenziale aumento del punteggio" dei controlli di sicurezza.<br>![Elenco di controlli e il potenziale aumento del Punteggio](media/secure-score-security-controls/secure-score-example-single-sub-recs.png)|
|**Punteggio di sicurezza**<br>Più sottoscrizioni|<br>Viene aggiunto il punteggio corrente per tutte le risorse in tutte le sottoscrizioni e il calcolo è quindi uguale a quello di una singola sottoscrizione<br><br>Quando si visualizzano più sottoscrizioni, il Punteggio sicuro valuta tutte le risorse all'interno di tutti i criteri abilitati e raggruppa il loro effetto combinato sul punteggio massimo del controllo di sicurezza.<br>![Punteggio sicuro per più sottoscrizioni con tutti i controlli abilitati](media/secure-score-security-controls/secure-score-example-multiple-subs.png)<br>Il punteggio combinato **non** è una media; piuttosto, si tratta della posizione valutata dello stato di tutte le risorse in tutte le sottoscrizioni.<br>Anche in questo caso, se si passa alla pagina raccomandazioni e si aggiungono i punti potenziali disponibili, si noterà che si tratta della differenza tra il punteggio corrente (24) e il punteggio massimo disponibile (60).|
||||

## <a name="improving-your-secure-score"></a>Miglioramento del Punteggio sicuro

Per migliorare il Punteggio sicuro, correggere le raccomandazioni relative alla sicurezza dall'elenco di raccomandazioni. È possibile monitorare e aggiornare manualmente ogni raccomandazione per ogni risorsa oppure usando la **correzione rapida.** opzione (se disponibile) per applicare una correzione per una raccomandazione a un gruppo di risorse in modo rapido. Per altre informazioni, vedere [correggere le raccomandazioni](security-center-remediate-recommendations.md).

Solo le raccomandazioni predefinite hanno un effetto sul punteggio sicuro.

## <a name="security-controls-and-their-recommendations"></a>Controlli di sicurezza e relative raccomandazioni

La tabella seguente elenca i controlli di sicurezza nel centro sicurezza di Azure. Per ogni controllo è possibile visualizzare il numero massimo di punti che è possibile aggiungere al Punteggio sicuro se si aggiornano *tutte* le raccomandazioni elencate nel controllo per *tutte* le risorse. 

> [!TIP]
> Se si desidera filtrare o ordinare questo elenco in modo diverso, copiarlo e incollarlo in Excel.

|Controllo di sicurezza|Numero massimo di punti di Punteggio sicuro|Consigli|
|----------------|:-------------------:|---------------|
|**Abilitare MFA**|10|-L'autenticazione a più fattori deve essere abilitata per gli account con autorizzazioni proprietario per la sottoscrizione<br>-L'autenticazione a più fattori deve essere abilitata per gli account con autorizzazioni di lettura per la sottoscrizione<br>-L'autenticazione a più fattori deve essere abilitata per gli account con autorizzazioni di scrittura sulla sottoscrizione|
|**Porte di gestione sicure**|8|-Il controllo di accesso di rete just-in-time deve essere applicato alle macchine virtuali<br>-Le macchine virtuali devono essere associate a un gruppo di sicurezza di rete<br>-Le porte di gestione devono essere chiuse nelle macchine virtuali|
|**Applicare gli aggiornamenti di sistema**|6|-È necessario risolvere i problemi di integrità dell'agente di monitoraggio nei computer<br>-È necessario installare l'agente di monitoraggio nei set di scalabilità di macchine virtuali<br>-Il Monitoring Agent deve essere installato nei computer<br>-La versione del sistema operativo deve essere aggiornata per i ruoli del servizio cloud<br>-È necessario installare gli aggiornamenti del sistema nei set di scalabilità di macchine virtuali<br>-Gli aggiornamenti del sistema devono essere installati nei computer<br>-I computer devono essere riavviati per applicare gli aggiornamenti del sistema<br>-I servizi Kubernetes devono essere aggiornati a una versione di Kubernetes non vulnerabile<br>-L'agente di monitoraggio deve essere installato nelle macchine virtuali|
|**Correggi le vulnerabilità**|6|-La sicurezza dei dati avanzata deve essere abilitata nei server SQL<br>-Vulnerabilità in Azure Container Registry immagini da correggere (anteprima)<br>-È necessario correggere le vulnerabilità nei database SQL<br>-Le vulnerabilità devono essere risolte da una soluzione di valutazione della vulnerabilità<br>-La valutazione della vulnerabilità deve essere abilitata nelle istanze gestite di SQL<br>-La valutazione della vulnerabilità deve essere abilitata nei server SQL<br>-La soluzione di valutazione della vulnerabilità deve essere installata nelle macchine virtuali|
|**Abilitare la crittografia inattiva**|4|-La crittografia del disco deve essere applicata alle macchine virtuali<br>-Transparent Data Encryption nei database SQL deve essere abilitata<br>-Le variabili dell'account di automazione devono essere crittografate<br>-Per i cluster Service Fabric la proprietà ClusterProtectionLevel deve essere impostata su EncryptAndSign<br>-La protezione Transparent Data Encryption di SQL Server deve essere crittografata con la propria chiave|
|**Crittografare i dati in transito**|4|-L'app per le API deve essere accessibile solo tramite HTTPS<br>-App per le funzioni dovrebbe essere accessibile solo tramite HTTPS<br>-È necessario abilitare solo le connessioni sicure alla cache Redis<br>-Il trasferimento sicuro agli account di archiviazione deve essere abilitato<br>-L'applicazione Web deve essere accessibile solo tramite HTTPS|
|**Gestire l'accesso e le autorizzazioni**|4|-È necessario designare un massimo di 3 proprietari per la sottoscrizione<br>-Gli account deprecati devono essere rimossi dalla sottoscrizione (anteprima)<br>-Gli account deprecati con autorizzazioni proprietarie devono essere rimossi dalla sottoscrizione (anteprima)<br>-Gli account esterni con autorizzazioni proprietario devono essere rimossi dalla sottoscrizione (anteprima)<br>-Gli account esterni con autorizzazioni di lettura devono essere rimossi dalla sottoscrizione<br>-Gli account esterni con autorizzazioni di scrittura devono essere rimossi dalla sottoscrizione (anteprima)<br>-La sottoscrizione deve essere assegnata a più di un proprietario<br>-Il controllo degli accessi in base al ruolo (RBAC) deve essere usato nei servizi Kubernetes (anteprima)<br>-Service Fabric i cluster devono usare solo Azure Active Directory per l'autenticazione client|
|**Correggere le configurazioni di sicurezza**|4|-I criteri di sicurezza Pod devono essere definiti nei servizi Kubernetes (anteprima)<br>-È necessario correggere le vulnerabilità nelle configurazioni di sicurezza del contenitore<br>-È necessario correggere le vulnerabilità nella configurazione della sicurezza nei computer<br>-Le vulnerabilità nella configurazione della sicurezza nei set di scalabilità di macchine virtuali devono essere risolte<br>-L'agente di monitoraggio deve essere installato nelle macchine virtuali<br>-Il Monitoring Agent deve essere installato nei computer<br>-È necessario installare l'agente di monitoraggio nei set di scalabilità di macchine virtuali<br>-È necessario risolvere i problemi di integrità dell'agente di monitoraggio nei computer|
|**Limitare l'accesso alla rete non autorizzato**|4|-L'invio IP nella macchina virtuale deve essere disabilitato<br>-Gli intervalli IP autorizzati devono essere definiti nei servizi Kubernetes (anteprima)<br>-L'accesso ai servizi app deve essere limitato (anteprima)<br>-Le regole per le applicazioni Web in IaaS gruppi devono essere finalizzate<br>-Le macchine virtuali devono essere associate a un gruppo di sicurezza di rete<br>-CORS non deve consentire a tutte le risorse di accedere all'app per le API<br>-CORS non deve consentire a ogni risorsa di accedere al app per le funzioni<br>-CORS non deve consentire a tutte le risorse di accedere all'applicazione Web<br>-Il debug remoto deve essere disattivato per l'app per le API<br>-Il debug remoto deve essere disattivato per app per le funzioni<br>-Il debug remoto deve essere disattivato per l'applicazione Web<br>-L'accesso deve essere limitato per i gruppi di sicurezza di rete permissivi con macchine virtuali con connessione Internet<br>-Le regole del gruppo di sicurezza di rete per le macchine virtuali con connessione Internet devono essere finalizzate|
|**Applicare il controllo delle applicazioni adattivi**|3|-I controlli applicazione adattivi devono essere abilitati nelle macchine virtuali<br>-L'agente di monitoraggio deve essere installato nelle macchine virtuali<br>-Il Monitoring Agent deve essere installato nei computer<br>-È necessario risolvere i problemi di integrità dell'agente di monitoraggio nei computer|
|**Applicare la classificazione dei dati**|2|-I dati sensibili nei database SQL devono essere classificati (anteprima)|
|**Proteggi le applicazioni da attacchi DDoS**|2|-È necessario abilitare la protezione DDoS standard|
|**Abilita Endpoint Protection**|2|-Gli errori di integrità di Endpoint Protection devono essere corretti nei set di scalabilità di macchine virtuali<br>-È necessario risolvere i problemi di integrità di Endpoint Protection nei computer<br>-La soluzione Endpoint Protection deve essere installata nei set di scalabilità di macchine virtuali<br>-Installare la soluzione Endpoint Protection nelle macchine virtuali<br>-È necessario risolvere i problemi di integrità dell'agente di monitoraggio nei computer<br>-È necessario installare l'agente di monitoraggio nei set di scalabilità di macchine virtuali<br>-Il Monitoring Agent deve essere installato nei computer<br>-L'agente di monitoraggio deve essere installato nelle macchine virtuali<br>-Installare la soluzione Endpoint Protection nei computer|
|**Abilitare il controllo e la registrazione**|1|-Il controllo su SQL Server deve essere abilitato<br>-I log di diagnostica nei servizi app devono essere abilitati<br>-I log di diagnostica in Azure Data Lake Store devono essere abilitati<br>-I log di diagnostica in analisi di flusso di Azure devono essere abilitati<br>-I log di diagnostica negli account batch devono essere abilitati<br>-I log di diagnostica in Data Lake Analytics devono essere abilitati<br>-I log di diagnostica nell'hub eventi devono essere abilitati<br>-I log di diagnostica nell'hub degli eventi devono essere abilitati<br>-I log di diagnostica in Key Vault devono essere abilitati<br>-I log di diagnostica nelle app per la logica devono essere abilitati<br>-I log di diagnostica nel servizio di ricerca devono essere abilitati<br>-I log di diagnostica nel bus di servizio devono essere abilitati<br>-I log di diagnostica nei set di scalabilità di macchine virtuali devono essere abilitati<br>-Le regole di avviso delle metriche devono essere configurate negli account batch<br>-Le impostazioni di controllo SQL devono avere gruppi di azione configurati per acquisire attività critiche<br>-I server SQL devono essere configurati con il controllo dei giorni di conservazione superiori a 90 giorni.|
|**Implementare le procedure di sicurezza consigliate**|0|-L'accesso agli account di archiviazione con configurazioni del firewall e della rete virtuale deve essere limitato<br>-Tutte le regole di autorizzazione tranne RootManageSharedAccessKey devono essere rimosse dallo spazio dei nomi dell'hub eventi<br>-È necessario eseguire il provisioning di un amministratore Azure Active Directory per SQL Server<br>-È necessario definire le regole di autorizzazione nell'istanza dell'hub eventi<br>-È necessario eseguire la migrazione degli account di archiviazione a nuove risorse Azure Resource Manager<br>-È necessario eseguire la migrazione delle macchine virtuali a nuove risorse Azure Resource Manager<br>-Le impostazioni avanzate di sicurezza dei dati per SQL Server devono contenere un indirizzo di posta elettronica per ricevere gli avvisi di sicurezza<br>-La sicurezza dei dati avanzata deve essere abilitata nelle istanze gestite<br>-Tutti i tipi di protezione avanzata dalle minacce devono essere abilitati nelle impostazioni di sicurezza dei dati avanzate dell'istanza gestita di SQL<br>-Le notifiche tramite posta elettronica agli amministratori e ai proprietari delle sottoscrizioni devono essere abilitate nelle impostazioni di sicurezza dei dati avanzate di SQL Server<br>-I tipi Advanced Threat Protection devono essere impostati su' all'in SQL Server Advanced Data Security Settings<br>-Le subnet devono essere associate a un gruppo di sicurezza di rete<br>-Tutti i tipi di protezione avanzata dalle minacce devono essere abilitati nelle impostazioni di sicurezza avanzata dei dati di SQL Server|
||||

## <a name="secure-score-faq"></a>Domande frequenti sul punteggio sicuro

### <a name="why-has-my-secure-score-gone-down"></a>Perché il Punteggio sicuro è scaduto?
Con le modifiche introdotte in questo punteggio sicuro avanzato, è necessario risolvere tutte le raccomandazioni per una risorsa per la ricezione di punti. I punteggi cambiano anche in una scala di 0-10.

### <a name="if-i-address-only-three-out-of-four-recommendations-in-a-security-control-will-my-secure-score-change"></a>Se si risolvono solo tre delle quattro raccomandazioni in un controllo di sicurezza, il Punteggio sicuro cambierà?
Non non verrà modificato fino a quando non si aggiornano tutte le raccomandazioni per una singola risorsa. Per ottenere il punteggio massimo per un controllo, è necessario correggere tutte le raccomandazioni per tutte le risorse.

### <a name="will-this-enhanced-secure-score-replace-the-existing-secure-score"></a>Il Punteggio sicuro migliorato sostituirà il Punteggio sicuro esistente? 
Sì, ma per un periodo di tempo verranno eseguite side-by-side per semplificare la transizione.

### <a name="if-a-recommendation-isnt-applicable-to-me-and-i-disable-it-in-the-policy-will-my-security-control-be-fulfilled-and-my-secure-score-updated"></a>Se una raccomandazione non è applicabile all'utente e viene disabilitata nei criteri, il controllo di sicurezza sarà soddisfatto e il Punteggio sicuro sarà aggiornato?
Sì. Si consiglia di disabilitare le raccomandazioni quando non sono applicabili all'ambiente in uso. Per istruzioni su come disabilitare una raccomandazione specifica, vedere [disabilitare i criteri di sicurezza](https://docs.microsoft.com/azure/security-center/tutorial-security-policy#disable-security-policies).

### <a name="if-a-security-control-offers-me-zero-points-towards-my-secure-score-should-i-ignore-it"></a>Se un controllo di sicurezza offre zero punti per il Punteggio sicuro, è consigliabile ignorarlo?
In alcuni casi si noterà un punteggio massimo del controllo maggiore di zero, ma l'effetto è zero. Quando il Punteggio incrementale per la correzione delle risorse è trascurabile, viene arrotondato a zero. Non ignorare questi consigli perché continuano a migliorare la sicurezza. L'unica eccezione è il controllo "procedura consigliata aggiuntiva". Il monitoraggio e l'aggiornamento di questi consigli non aumenteranno il punteggio, ma la sicurezza complessiva sarà migliorata.

## <a name="next-steps"></a>Passaggi successivi

Questo articolo descrive il Punteggio sicuro migliorato e i nuovi controlli di sicurezza introdotti. Per materiale correlato, vedere gli articoli seguenti:

- [Informazioni sui diversi elementi di un'indicazione](security-center-recommendations.md)
- [Scopri come correggere le raccomandazioni](security-center-remediate-recommendations.md)