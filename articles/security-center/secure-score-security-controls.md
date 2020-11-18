---
title: Punteggio di sicurezza nel Centro sicurezza di Azure
description: Descrizione del punteggio di sicurezza del Centro sicurezza di Azure e dei relativi controlli di sicurezza
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
ms.date: 10/21/2020
ms.author: memildin
ms.openlocfilehash: fd01e7e6853d69889d0ffba3d8775a0fd92f7cd2
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94842533"
---
# <a name="secure-score-in-azure-security-center"></a>Punteggio di sicurezza nel Centro sicurezza di Azure

## <a name="introduction-to-secure-score"></a>Introduzione al punteggio di sicurezza

Il Centro sicurezza di Azure ha due obiettivi principali: 

- per semplificare la comprensione della situazione di sicurezza corrente
- per contribuire a migliorare in modo efficiente e efficace la sicurezza

La funzionalità centrale del Centro sicurezza che consente di raggiungere questi obiettivi è un **Punteggio sicuro**.

Il Centro sicurezza valuta continuamente risorse, sottoscrizioni e organizzazione per rilevare problemi di sicurezza. Aggrega quindi tutti i risultati in un singolo punteggio, in modo da poter indicare, a colpo d'occhio, lo stato di sicurezza attuale: maggiore è il punteggio, minore è il livello di rischio identificato.

Il Punteggio sicuro viene visualizzato nelle pagine portale di Azure come valore percentuale, ma vengono presentati chiaramente anche i valori sottostanti:

:::image type="content" source="./media/secure-score-security-controls/single-secure-score-via-ui.png" alt-text="Punteggio sicuro complessivo come illustrato nel portale":::

Per aumentare la sicurezza, vedere la pagina raccomandazioni del Centro sicurezza per le azioni in attesa necessarie per aumentare il punteggio. Ogni raccomandazione include istruzioni che consentono di correggere il problema specifico.

I consigli sono raggruppati in **controlli di sicurezza**. Ogni controllo è un gruppo logico di raccomandazioni sulla sicurezza correlate e riflette le superfici di attacco vulnerabili. Il punteggio viene migliorato solo quando si correggono *tutte* le raccomandazioni relative a una singola risorsa all'interno di un controllo. Per verificare in che modo l'organizzazione protegga ogni singola superficie di attacco, esaminare i punteggi per ogni controllo di sicurezza.

Per altre informazioni, vedere [come viene calcolato il Punteggio sicuro](secure-score-security-controls.md#how-your-secure-score-is-calculated) di seguito. 


## <a name="access-your-secure-score"></a>Accedi al tuo punteggio sicuro

È possibile trovare il Punteggio sicuro complessivo, nonché il punteggio per ogni sottoscrizione, tramite il portale di Azure o a livello, come descritto nelle sezioni seguenti:

- [Ottenere il Punteggio sicuro dal portale](#get-your-secure-score-from-the-portal)
- [Ottenere il Punteggio sicuro dall'API REST](#get-your-secure-score-from-the-rest-api)
- [Ottieni il Punteggio sicuro da Azure Resource Graph (ARG)](#get-your-secure-score-from-azure-resource-graph-arg)

### <a name="get-your-secure-score-from-the-portal"></a>Ottenere il Punteggio sicuro dal portale

Il Centro sicurezza Visualizza il punteggio in primo piano nel portale: è il primo riquadro principale della pagina Panoramica del Centro sicurezza. Selezionando questo riquadro, viene visualizzata la pagina del Punteggio sicuro dedicato, in cui verrà visualizzato il Punteggio suddiviso in base alla sottoscrizione. Selezionare una singola sottoscrizione per visualizzare l'elenco dettagliato delle raccomandazioni con priorità e il potenziale impatto che il monitoraggio e l'aggiornamento avranno sul punteggio della sottoscrizione.

Per riepilogo, il Punteggio sicuro viene visualizzato nei percorsi seguenti nelle pagine del portale del Centro sicurezza.

- In una sezione **Panoramica** del Centro sicurezza (dashboard principale):

    :::image type="content" source="./media/secure-score-security-controls/score-on-main-dashboard.png" alt-text="Punteggio sicuro nel dashboard del Centro sicurezza":::

- Nella pagina del **Punteggio sicuro** dedicato:

    :::image type="content" source="./media/secure-score-security-controls/score-on-dedicated-dashboard.png" alt-text="Il Punteggio sicuro nella pagina del Punteggio sicuro del Centro sicurezza":::

- Nella parte superiore della pagina **raccomandazioni** :

    :::image type="content" source="./media/secure-score-security-controls/score-on-recommendations-page.png" alt-text="Il Punteggio sicuro nella pagina delle raccomandazioni del Centro sicurezza":::



### <a name="get-your-secure-score-from-the-rest-api"></a>Ottenere il Punteggio sicuro dall'API REST

È possibile accedere al Punteggio tramite l'API per il Punteggio sicuro (attualmente in anteprima). I metodi dell'API offrono la flessibilità necessaria per eseguire query nei dati e creare un meccanismo personalizzato per la creazione di report sui punteggi di sicurezza nel tempo. Ad esempio, è possibile usare l' [API dei punteggi sicuri](/rest/api/securitycenter/securescores) per ottenere il punteggio per una sottoscrizione specifica. Inoltre, è possibile usare l' [API di controllo del Punteggio sicuro](/rest/api/securitycenter/securescorecontrols) per elencare i controlli di sicurezza e il punteggio corrente delle sottoscrizioni.

![Recupero di un singolo Punteggio sicuro tramite l'API](media/secure-score-security-controls/single-secure-score-via-api.png)

Per esempi di strumenti basati sull'API per il Punteggio sicuro, vedere [l'area di valutazione sicura della community di GitHub](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score). 



### <a name="get-your-secure-score-from-azure-resource-graph-arg"></a>Ottieni il Punteggio sicuro da Azure Resource Graph (ARG)

Azure Resource Graph fornisce l'accesso immediato alle informazioni sulle risorse negli ambienti cloud con potenti funzionalità di filtro, raggruppamento e ordinamento. Si tratta di un modo rapido ed efficiente di eseguire query sulle informazioni nelle sottoscrizioni di Azure a livello di codice o dall'interno del portale di Azure. [Scopri di più sul grafico delle risorse di Azure](https://docs.microsoft.com/azure/governance/resource-graph/).

Per accedere al Punteggio sicuro per più sottoscrizioni con ARG:

1. Dal portale di Azure aprire **Esplora grafico risorse di Azure**.

    :::image type="content" source="./media/security-center-identity-access/opening-resource-graph-explorer.png" alt-text="Avvio della pagina consigli di Azure Resource Graph Explorer * *" :::

1. Immettere la query kusto (usando gli esempi riportati di seguito per informazioni aggiuntive).

    - Questa query restituisce l'ID sottoscrizione, il punteggio corrente in punti e come percentuale e il punteggio massimo per la sottoscrizione. 

        ```kusto
        SecurityResources 
        | where type == 'microsoft.security/securescores' 
        | extend current = properties.score.current, max = todouble(properties.score.max)
        | project subscriptionId, current, max, percentage = ((current / max)*100)
        ```

    - Questa query restituisce lo stato di tutti i controlli di sicurezza. Per ogni controllo, si otterrà il numero di risorse non integre, il punteggio corrente e il punteggio massimo. 

        ```kusto
        SecurityResources 
        | where type == 'microsoft.security/securescores/securescorecontrols'
        | extend SecureControl = properties.displayName, unhealthy = properties.unhealthyResourceCount, currentscore = properties.score.current, maxscore = properties.score.max
        | project SecureControl , unhealthy, currentscore, maxscore
        ```

1. Selezionare **Esegui query**.

## <a name="how-your-secure-score-is-calculated"></a>Calcolo del Punteggio sicuro 

Il contributo di ogni controllo di sicurezza rispetto al punteggio di sicurezza complessivo viene visualizzato chiaramente nella pagina Consigli.

[![Il punteggio di sicurezza migliorato introduce i controlli di sicurezza](media/secure-score-security-controls/security-controls.png)](media/secure-score-security-controls/security-controls.png#lightbox)

Per ottenere tutti i punti possibili per un controllo di sicurezza, tutte le risorse devono essere conformi a tutte le raccomandazioni sulla sicurezza all'interno del controllo di sicurezza. Ad esempio, il Centro sicurezza offre più raccomandazioni su come proteggere le porte di gestione. In passato, era possibile correggere alcune di queste raccomandazioni correlate e interdipendenti lasciando inalterate le altre soluzioni e il punteggio di sicurezza sarebbe migliorato. Con un'osservazione obiettiva, era facile affermare che la sicurezza non migliorava fino a quando non venivano corrette tutte. Ora, è necessario correggerle tutte perché si possa notare un cambiamento a livello di punteggio di sicurezza.

Ad esempio, il controllo di sicurezza denominato "Applica gli aggiornamenti del sistema" ha un punteggio massimo di sei punti, che è possibile visualizzare nella descrizione comando sul valore di incremento potenziale del controllo:

[![Controllo di sicurezza "Applica gli aggiornamenti del sistema"](media/secure-score-security-controls/apply-system-updates-control.png)](media/secure-score-security-controls/apply-system-updates-control.png#lightbox)

Il punteggio massimo per il controllo Applica gli aggiornamenti del sistema, è sempre 6. In questo esempio sono disponibili 50 risorse. Dividendo quindi il punteggio massimo per 50, il risultato è che ogni risorsa contribuisce con 0,12 punti. 

* **Incremento potenziale** (0,12 x 8 risorse non integre = 0,96): punti rimanenti disponibili nel controllo. Se si aggiornano tutte le raccomandazioni in questo controllo, il punteggio aumenterà del 2% (in questo caso, 0,96 punti arrotondati per eccesso a 1 punto). 
* **Punteggio corrente** (0,12 x 42 risorse integre = 5,04): punteggio corrente per il controllo. Ogni controllo contribuisce a raggiungere il punteggio totale. In questo esempio, il controllo contribuisce con 5,04 punti al punteggio di sicurezza totale corrente.
* **Punteggio massimo**: numero massimo di punti che è possibile ottenere completando tutte le raccomandazioni in un controllo. Il punteggio massimo per un controllo indica il significato relativo del controllo. Usare i valori del punteggio massimo per valutare i problemi da risolvere per primi. 


### <a name="calculations---understanding-your-score"></a>Calcoli: comprensione del punteggio

|Metrica|Formula ed esempio|
|-|-|
|**Punteggio corrente del controllo di sicurezza**|<br>![Equazione per il calcolo del punteggio corrente di un controllo di sicurezza](media/secure-score-security-controls/security-control-scoring-equation.png)<br><br>Ogni singolo controllo di sicurezza contribuisce a raggiungere il punteggio di sicurezza. Ogni risorsa interessata da una raccomandazione all'interno del controllo contribuisce al punteggio corrente del controllo. Il punteggio corrente per ogni controllo è una misura dello stato delle risorse *all'interno* del controllo.<br>![Descrizioni comando che mostrano i valori usati durante il calcolo del punteggio corrente del controllo di sicurezza](media/secure-score-security-controls/security-control-scoring-tooltips.png)<br>In questo esempio, il punteggio massimo di 6 viene diviso per 78 perché questa è la somma delle risorse integre e non integre.<br>6 / 78 = 0,0769<br>Moltiplicando questo valore per il numero di risorse integre (4) si ottiene il punteggio corrente:<br>0,0769 * 4 = **0,31**<br><br>|
|**Punteggio di sicurezza**<br>Singola sottoscrizione|<br>![Equazione per il calcolo del punteggio di sicurezza corrente](media/secure-score-security-controls/secure-score-equation.png)<br><br>![Punteggio di sicurezza di una singola sottoscrizione con tutti i controlli abilitati](media/secure-score-security-controls/secure-score-example-single-sub.png)<br>In questo esempio, è presente una singola sottoscrizione con tutti i controlli di sicurezza disponibili (punteggio massimo potenziale di 60 punti). Il punteggio mostra 28 punti su 60 possibili e i restanti 32 punti vengono riflessi nei valori di "Incremento potenziale del punteggio" dei controlli di sicurezza.<br>![Elenco di controlli e incremento potenziale del punteggio](media/secure-score-security-controls/secure-score-example-single-sub-recs.png)|
|**Punteggio di sicurezza**<br>Più sottoscrizioni|<br>Vengono aggiunti i punteggi correnti per tutte le risorse in tutte le sottoscrizioni e il calcolo corrisponde, quindi, a quello per una singola sottoscrizione.<br><br>Quando si visualizzano più sottoscrizioni, il punteggio di sicurezza valuta tutte le risorse all'interno dei criteri abilitati e raggruppa il loro effetto combinato sul punteggio massimo di ogni controllo di sicurezza.<br>![Punteggio di sicurezza di più sottoscrizioni con tutti i controlli abilitati](media/secure-score-security-controls/secure-score-example-multiple-subs.png)<br>Il punteggio combinato **non** è una media. Si tratta, piuttosto, della posizione valutata dello stato di tutte le risorse in tutte le sottoscrizioni.<br>Anche in questo caso, se si passa alla pagina Consigli e si aggiungono i punti potenziali disponibili, si noterà che si tratta della differenza tra il punteggio corrente (24) e il punteggio massimo disponibile (60).|
||||

### <a name="which-recommendations-are-included-in-the-secure-score-calculations"></a>Quali raccomandazioni sono incluse nei calcoli di valutazione sicura?

Solo le raccomandazioni predefinite hanno effetto sul punteggio di sicurezza.

Le raccomandazioni contrassegnate come **Anteprima** non sono incluse nei calcoli del Punteggio sicuro. È comunque necessario correggerle non appena possibile, in modo che possano contribuire al punteggio al termine del periodo di anteprima.

Esempio di una raccomandazione in anteprima:

:::image type="content" source="./media/secure-score-security-controls/example-of-preview-recommendation.png" alt-text="Raccomandazione con il flag di anteprima":::

## <a name="improve-your-secure-score"></a>Migliorare il punteggio di sicurezza

Per migliorare il punteggio di sicurezza, correggere le raccomandazioni sulla sicurezza presenti nell'elenco delle raccomandazioni. È possibile correggere ogni raccomandazione manualmente per ogni risorsa o usando l' opzione **Correzione rapida** (se disponibile) per applicare rapidamente una correzione per una raccomandazione a un gruppo di risorse. Per altre informazioni, vedere [Correggere le raccomandazioni](security-center-remediate-recommendations.md).

Un altro modo per migliorare il punteggio e assicurarsi che gli utenti non creino risorse che influiscono negativamente sul punteggio consiste nel configurare le opzioni Applica e nega sulle raccomandazioni pertinenti. Per altre informazioni, vedere [Impedire gli errori di configurazione con le raccomandazioni di tipo Imponi/Nega](prevent-misconfigurations.md).

## <a name="security-controls-and-their-recommendations"></a>Controlli di sicurezza e relative raccomandazioni

La tabella seguente elenca i controlli di sicurezza nel Centro sicurezza di Azure. Per ogni controllo è possibile visualizzare il numero massimo di punti da aggiungere al punteggio di sicurezza, se si correggono *tutte* le raccomandazioni elencate nel controllo per *tutte* le risorse. 

Il set di raccomandazioni sulla sicurezza fornito con il Centro sicurezza è adattato alle risorse disponibili nell'ambiente di ogni organizzazione. Le raccomandazioni possono essere ulteriormente personalizzate [disabilitando i criteri](tutorial-security-policy.md#disable-security-policies-and-disable-recommendations) ed [escludendo le risorse specifiche da un'indicazione](exempt-resource.md). 
 
È consigliabile esaminare con attenzione le iniziative di criteri di Azure assegnate a ogni organizzazione. 

> [!TIP]
> Per informazioni dettagliate sulla revisione e la modifica delle iniziative, vedere [utilizzo dei criteri di sicurezza](tutorial-security-policy.md). 

Anche se l'iniziativa di sicurezza predefinita del Centro sicurezza è basata su standard e procedure consigliate per il settore, esistono scenari in cui i consigli predefiniti elencati di seguito potrebbero non essere completamente idonei per l'organizzazione. Di conseguenza, talvolta è necessario modificare l'iniziativa predefinita, senza compromettere la sicurezza, per garantire che sia allineata ai criteri dell'organizzazione. standard del settore, standard normativi e benchmark che è necessario soddisfare.<br><br>
<div class="foo">

<style type="text/css"> .tg  {border-collapse:collapse;border-spacing:0;} .tg td{border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px; overflow:hidden;padding:10px 5px;word-break:normal;} .tg th{border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:18px; font-weight:normal;overflow:hidden;padding:10px 5px;word-break:normal;} .tg .tg-cly1{text-align:left;vertical-align:middle} .tg .tg-lboi{border-color:inherit;text-align:left;vertical-align:middle} </style>
<table class="tg">
<thead>
  <tr>
    <th class="tg-cly1"><b>Controllo di sicurezza, punteggio e descrizione</b><br></th>
    <th class="tg-cly1"><b>Indicazioni</b></th>
  </tr>
</thead>
<tbody>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Abilitare l'autenticazione a più fattori (punteggio massimo 10)</p></strong>Se si usa solo una password per autenticare un utente, viene lasciato un vettore aperto per l'attacco. Se la password è vulnerabile o è stata esposta altrove, come si può avere la certezza che sia effettivamente l'utente che accede con il nome utente e la password?<br>Se si abilita l'<a href="https://www.microsoft.com/security/business/identity/mfa">autenticazione a più fattori</a>, gli account sono più sicuri e gli utenti possono ancora eseguire l'autenticazione a quasi tutte le applicazioni con Single Sign-On (SSO).</td>
    <td class="tg-lboi"; width=55%>- È necessario abilitare l'autenticazione a più fattori negli account con autorizzazioni di proprietario per la sottoscrizione<br>- È necessario abilitare l'autenticazione a più fattori per gli account con autorizzazioni di scrittura per la sottoscrizione</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Porte di gestione sicure (punteggio massimo 8)</p></strong>Gli attacchi di forza bruta prendono di mira le porte di gestione per ottenere l'accesso a una macchina virtuale. Poiché le porte non devono sempre essere aperte, una strategia di mitigazione consiste nel ridurre l'esposizione alle porte usando i controlli di accesso di rete JIT, i gruppi di sicurezza di rete e la gestione delle porte delle macchine virtuali.<br>Poiché molte organizzazioni IT non bloccano le comunicazioni SSH in uscita dalla loro rete, gli utenti malintenzionati possono creare tunnel crittografati che consentono alle porte RDP nei sistemi infetti di comunicare al comando dell'utente malintenzionato di controllare i server. Gli utenti malintenzionati possono usare il sottosistema Gestione remota Windows per spostarsi lateralmente nell'ambiente e usare le credenziali rubate per accedere ad altre risorse in una rete.</td>
    <td class="tg-lboi"; width=55%>- Le porte di gestione delle macchine virtuali devono essere protette con il controllo di accesso di rete just-in-Time<br>- È necessario associare le macchine virtuali a un gruppo di sicurezza di rete<br>- È necessario chiudere le porte di gestione nelle macchine virtuali</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Applicare gli aggiornamenti del sistema (punteggio massimo 6)</p></strong>Gli aggiornamenti del sistema offrono alle organizzazioni la possibilità di mantenere l'efficienza operativa, ridurre le vulnerabilità di sicurezza e fornire un ambiente più stabile per gli utenti finali. La mancata applicazione degli aggiornamenti lascia vulnerabilità senza patch e produce ambienti vulnerabili ad attacchi. Queste vulnerabilità possono essere sfruttate e causare perdite di dati, esfiltrazioni di dati, ransomware e abusi di risorse. Per distribuire gli aggiornamenti del sistema, è possibile usare la <a href="/azure/automation/update-management/overview">soluzione Gestione aggiornamenti per gestire gli aggiornamenti e le patch</a> per le macchine virtuali. La gestione degli aggiornamenti è il processo di controllo della distribuzione e della manutenzione delle versioni software.</td>
    <td class="tg-lboi"; width=55%>- È necessario risolvere i problemi di integrità dell'agente di monitoraggio nei computer<br>- È necessario installare l'agente di monitoraggio nei set di scalabilità di macchine virtuali<br>- È necessario installare l'agente di monitoraggio nei computer<br>- È necessario aggiornare la versione del sistema operativo per i ruoli del servizio cloud<br>- È necessario installare gli aggiornamenti del sistema nei set di scalabilità di macchine virtuali<br>- È necessario installare gli aggiornamenti nei computer<br>- È necessario riavviare i computer per applicare gli aggiornamenti del sistema<br>- È necessario aggiornare i servizi Kubernetes a una versione di Kubernetes non vulnerabile<br>- È necessario installare l'agente di monitoraggio nelle macchine virtuali<br>- Log Analytics Agent deve essere installato nei computer Azure Arc basati su Windows (anteprima)<br>- Log Analytics Agent deve essere installato nei computer Azure Arc basati su Linux (anteprima)</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Correggere le vulnerabilità (punteggio massimo 6)</p></strong>Una vulnerabilità è una debolezza che un attore di minacce può sfruttare per compromettere riservatezza, disponibilità o integrità di una risorsa. La <a href="/windows/security/threat-protection/microsoft-defender-atp/next-gen-threat-and-vuln-mgt">gestione delle vulnerabilità</a> riduce l'esposizione organizzativa, protegge la superficie di attacco degli endpoint, aumenta la resilienza dell'organizzazione e riduce la superficie di attacco delle risorse. La gestione delle minacce e delle vulnerabilità offre visibilità sul software e sulle configurazioni errate della sicurezza e fornisce raccomandazioni per le mitigazioni.</td>
    <td class="tg-lboi"; width=55%>- È necessario abilitare la sicurezza dei dati avanzata nel database SQL<br>- È necessario correggere le vulnerabilità nelle immagini del Registro Azure Container<br>- È necessario correggere le vulnerabilità dei database SQL<br>- È necessario correggere le vulnerabilità tramite una soluzione di valutazione della vulnerabilità<br>- È necessario abilitare la valutazione della vulnerabilità in SQL Istanza gestita<br>- È necessario abilitare la valutazione della vulnerabilità nei server SQL<br>- È necessario installare una soluzione di valutazione della vulnerabilità nelle macchine virtuali<br>- Le immagini del contenitore devono essere distribuite solo da registri attendibili (anteprima)<br>- Il componente aggiuntivo criteri di Azure per Kubernetes deve essere installato e abilitato nei cluster (anteprima)</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Abilitare la crittografia dei dati inattivi (punteggio massimo 4)</p>La </strong><a href="/azure/security/fundamentals/encryption-atrest">crittografia dei dati inattivi</a> offre protezione dei dati per i dati archiviati. Gli attacchi contro i dati inattivi includono i tentativi di ottenere l'accesso fisico all'hardware in cui i dati sono archiviati. Azure usa la crittografia simmetrica per crittografare e decrittografare grandi quantità di dati inattivi. Viene usata una chiave di crittografia simmetrica per crittografare i dati quando vengono scritti nella risorsa di archiviazione. Questa chiave di crittografia viene usata anche per decrittografare i dati durante la loro preparazione per l'uso in memoria. Le chiavi devono essere archiviate in una posizione sicura con controllo di accesso basato su identità e criteri di controllo. Questa posizione sicura è Azure Key Vault. Se un utente malintenzionato ottiene i dati crittografati ma non le chiavi di crittografia, l'autore dell'attacco non può accedere ai dati senza violare la crittografia.</td>
    <td class="tg-lboi"; width=55%>- È necessario applicare la crittografia del disco alle macchine virtuali<br>- È necessario abilitare Transparent Data Encryption nel database SQL<br>- È necessario crittografare le variabili dell'account di automazione<br>- È necessario impostare la proprietà ClusterProtectionLevel dei cluster di Service Fabric su EncryptAndSign<br>- È necessario crittografare TDE Protector per il server SQL con la chiave personale</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Crittografare i dati in transito (punteggio massimo 4)</p></strong>Quando vengono trasferiti tra componenti, posizioni o programmi, i dati vengono considerati "in transito". Le organizzazioni che non riescono a proteggere i dati in transito sono più vulnerabili agli attacchi man-in-the-middle, eavesdropping e hijack della sessione. Per lo scambio dei dati, è necessario usare i protocolli SSL/TLS e una VPN. Quando si inviano dati crittografati tra una macchina virtuale di Azure e una posizione locale tramite Internet, è possibile usare un gateway di rete virtuale, ad esempio <a href="/azure/vpn-gateway/vpn-gateway-about-vpngateways">gateway VPN di Azure</a> per inviare il traffico crittografato.</td>
    <td class="tg-lboi"; width=55%>- È necessario che l'app per le API sia accessibile solo tramite HTTPS<br>- È necessario che l'app per le funzioni sia accessibile solo tramite HTTPS<br>- È necessario abilitare solo connessioni sicure alla Cache Redis<br>- È necessario abilitare il trasferimento sicuro agli account di archiviazione<br>- È necessario che l'applicazione Web sia accessibile solo tramite HTTPS<br>- È necessario abilitare l'endpoint privato per i server PostgreSQL<br>- Applicare la connessione SSL deve essere abilitata per i server di database PostgreSQL<br>- Applicare la connessione SSL deve essere abilitata per i server di database MySQL<br>- TLS deve essere aggiornato alla versione più recente per l'app per le API<br>- TLS deve essere aggiornato alla versione più recente per l'app per le funzioni<br>- TLS deve essere aggiornato alla versione più recente per l'app Web<br>- FTPS dovrebbe essere necessario nell'app per le API<br>- FTPS dovrebbe essere necessario nell'app per le funzioni<br>- FTPS dovrebbe essere necessario nell'app Web</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Gestire accessi e autorizzazioni (punteggio massimo 4)</p></strong>Un aspetto rilevante di un programma di sicurezza è la garanzia che gli utenti dispongano esclusivamente dell'accesso necessario ai relativi processi: ovvero il <a href="/windows-server/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models">modello di accesso con privilegi minimi</a>.<br>Controllare l'accesso alle risorse creando assegnazioni di ruolo con il <a href="/azure/role-based-access-control/overview">controllo degli accessi in base al ruolo</a>. Un'assegnazione di ruolo è costituita da tre elementi:<br>- <strong>Entità di sicurezza</strong>: oggetto a cui l'utente richiede l'accesso<br>- <strong>Definizione del ruolo</strong>: autorizzazioni dell'utente<br>- <strong>Ambito</strong>: set di risorse a cui si applicano le autorizzazioni</td>
    <td class="tg-lboi"; width=55%>- È necessario rimuovere dalla sottoscrizione gli account deprecati (anteprima)<br>- È necessario rimuovere dalla sottoscrizione gli account deprecati con autorizzazioni di tipo proprietario (anteprima)<br>- È necessario rimuovere dalla sottoscrizione gli account esterni con autorizzazioni di tipo proprietario (anteprima)<br>- È necessario rimuovere dalla sottoscrizione gli account esterni con autorizzazioni di scrittura (anteprima)<br>- È necessario assegnare alla sottoscrizione più di un proprietario<br>- È necessario usare il controllo degli accessi in base al ruolo nei servizi Kubernetes (anteprima)<br>- È necessario che i cluster di Service Fabric usino solo Azure Active Directory per l'autenticazione client<br>- Le entità servizio devono essere usate per proteggere le sottoscrizioni anziché i certificati di gestione<br>- È necessario applicare le funzionalità di Linux con privilegi minimi per i contenitori (anteprima)<br>- È necessario applicare il file system radice non modificabile (di sola lettura) per i contenitori (anteprima)<br>- Il contenitore con escalation dei privilegi deve essere evitato (anteprima)<br>- Evitare l'esecuzione di contenitori come utente root (anteprima)<br>- I contenitori che condividono spazi dei nomi host sensibili devono essere evitati (anteprima)<br>- L'utilizzo dei montaggi del volume Pod HostPath deve essere limitato a un elenco noto (anteprima)<br>- I contenitori con privilegi devono essere evitati (anteprima)<br>- Il componente aggiuntivo criteri di Azure per Kubernetes deve essere installato e abilitato nei cluster (anteprima)<br>- Le app Web devono richiedere un certificato SSL per tutte le richieste in ingresso<br>- L'identità gestita deve essere usata nell'app per le API<br>- È necessario usare l'identità gestita nell'app per le funzioni<br>- L'identità gestita deve essere usata nell'app Web</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Correggere le configurazioni di sicurezza (punteggio massimo 4)</p></strong>Gli asset IT non configurati correttamente hanno un rischio maggiore di essere attaccati. Le azioni di protezione di base vengono spesso dimenticate quando si distribuiscono asset ed è necessario rispettare delle scadenze. I problemi di configurazione della sicurezza possono essere a qualsiasi livello nell'infrastruttura: dai sistemi operativi e dalle appliance di rete alle risorse cloud.<br>Il Centro sicurezza di Azure confronta continuamente la configurazione delle risorse con i requisiti di benchmark, normative e standard del settore. Una volta configurati i "pacchetti di conformità" pertinenti (standard e di base), rilevanti per l'organizzazione, eventuali mancanze determineranno raccomandazioni sulla sicurezza che includono l'identificatore CCEID e una spiegazione del potenziale impatto sulla sicurezza.<br>I pacchetti usati di frequente sono <a href="/azure/security/benchmarks/introduction">Azure Security Benchmark</a> e <a href="https://www.cisecurity.org/benchmark/azure/">CIS Microsoft Azure Foundations Benchmark versione 1.1.0</a></td>
    <td class="tg-lboi"; width=55%>- È necessario correggere le vulnerabilità nelle configurazioni della sicurezza dei contenitori<br>- È necessario correggere le vulnerabilità nella configurazione di sicurezza dei computer<br>- È necessario correggere le vulnerabilità nella configurazione di sicurezza dei set di scalabilità di macchine virtuali<br>- È necessario installare l'agente di monitoraggio nelle macchine virtuali<br>- È necessario installare l'agente di monitoraggio nei computer<br>- Log Analytics Agent deve essere installato nei computer Azure Arc basati su Windows (anteprima)<br>- Log Analytics Agent deve essere installato nei computer Azure Arc basati su Linux (anteprima)<br>- È necessario installare l'agente di monitoraggio nei set di scalabilità di macchine virtuali<br>- È necessario risolvere i problemi di integrità dell'agente di monitoraggio nei computer<br>- L'override o la disabilitazione del profilo AppArmor dei contenitori deve essere limitata (anteprima)<br>- Il componente aggiuntivo criteri di Azure per Kubernetes deve essere installato e abilitato nei cluster (anteprima)</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Limitare l'accesso non autorizzato alla rete (punteggio massimo 4)</p></strong>Gli endpoint all'interno di un'organizzazione forniscono una connessione diretta dalla rete virtuale ai servizi di Azure supportati. Le macchine virtuali in una subnet possono comunicare con tutte le risorse. Per limitare le comunicazioni verso e dalle risorse in una subnet, creare un gruppo di sicurezza di rete e associarlo alla subnet. Le organizzazioni possono limitare e proteggere da traffico non autorizzato creando regole in ingresso e in uscita.</td>
    <td class="tg-lboi"; width=55%>- È necessario disabilitare l'inoltro IP nella macchina virtuale<br>- È necessario definire gli intervalli IP autorizzati nei servizi Kubernetes (anteprima)<br>- (DEPRECATO) È necessario limitare l'accesso ai Servizi app (anteprima)<br>- (DEPRECATO) È necessario applicare la protezione avanzata alle regole per le applicazioni Web nei gruppi di sicurezza di rete IaaS<br>- È necessario associare le macchine virtuali a un gruppo di sicurezza di rete<br>- È necessario che CORS non consenta a tutte le risorse di accedere all'app per le API dell'utente<br>- È necessario che CORS non consenta a tutte le risorse di accedere all'app per le funzioni dell'utente<br>- È necessario che CORS non consenta a ogni risorsa l'accesso all'applicazione Web<br>- È necessario disattivare il debug remoto per l'app per le API<br>- È necessario disattivare il debug remoto per l'app per le funzioni<br>- È necessario disattivare il debug remoto per l'applicazione Web<br>- È necessario limitare l'accesso per i gruppi di sicurezza di rete permissivi con macchine virtuali connesse a Internet<br>- È necessario rafforzare le regole per i gruppi di sicurezza di rete delle macchine virtuali con connessione Internet<br>- Il componente aggiuntivo criteri di Azure per Kubernetes deve essere installato e abilitato nei cluster (anteprima)<br>- I contenitori devono restare in ascolto solo sulle porte consentite (anteprima)<br>- I servizi devono essere in ascolto solo sulle porte consentite (anteprima)<br>- L'utilizzo della rete e delle porte dell'host deve essere limitato (anteprima)<br>- Le reti virtuali devono essere protette dal firewall di Azure (anteprima)<br>- È necessario abilitare l'endpoint privato per i server MariaDB<br>- È necessario abilitare l'endpoint privato per i server MySQL<br>- È necessario abilitare l'endpoint privato per i server PostgreSQL</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Applicare il controllo delle applicazioni adattivo (punteggio massimo 3)</p></strong>Il controllo delle applicazioni adattivo è una soluzione end-to-end intelligente e automatizzata che consente di controllare quali applicazioni possono essere eseguite nei computer Azure e non Azure. Consente anche di proteggere i computer dai malware.<br>Il Centro sicurezza USA Machine Learning per creare un elenco di applicazioni sicure per un gruppo di computer.<br>Questo approccio innovativo all'elenco di applicazioni approvate offre i vantaggi di sicurezza senza la complessità di gestione.<br>Il controllo delle applicazioni adattivo è particolarmente rilevante per i server sviluppati appositamente per eseguire un set specifico di applicazioni.</td>
    <td class="tg-lboi"; width=55%>- È necessario abilitare i controlli applicazioni adattivi nelle macchine virtuali<br>- È necessario installare l'agente di monitoraggio nelle macchine virtuali<br>- È necessario installare l'agente di monitoraggio nei computer<br>- Log Analytics Agent deve essere installato nei computer Azure Arc basati su Windows (anteprima)<br>- Log Analytics Agent deve essere installato nei computer Azure Arc basati su Linux (anteprima)<br>- È necessario risolvere i problemi di integrità dell'agente di monitoraggio nei computer</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Applicare la classificazione dei dati (punteggio massimo 2)</p></strong>La classificazione dei dati dell'organizzazione in base a sensibilità e impatto aziendale consente di determinare e assegnare valore ai dati e fornisce la strategia e le basi per la governance.<br><a href="/azure/information-protection/what-is-information-protection">Azure Information Protection</a> può supportare la classificazione dei dati. Usa la crittografia, l'identità e i criteri di autorizzazione per proteggere i dati e limitare l'accesso ai dati. Alcune classificazioni usate da Microsoft sono non aziendali, pubbliche, generali, riservate e altamente riservate.</td>
    <td class="tg-lboi"; width=55%>- È necessario classificare i dati sensibili nei database SQL (anteprima)</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Proteggere le applicazioni da attacchi DDoS (punteggio massimo 2)</p></strong>Gli attacchi DDoS (Distributed Denial of Service) sovraccaricano le risorse e rendono inutilizzabili le applicazioni. Usare <a href="/azure/virtual-network/ddos-protection-overview">Protezione DDoS di Azure Standard</a> per difendere l'organizzazione dai tre tipi principali di attacchi DDoS:<br>- <strong>Attacchi volumetrici</strong>, che sovraccaricano la rete con traffico legittimo. Protezione DDoS Standard attenua questi attacchi mediante l'assorbimento o la pulitura automatica.<br>- <strong>Attacchi ai protocolli</strong>, che rendono inaccessibile una destinazione sfruttando una vulnerabilità nello stack di protocolli di livello 3 e 4. Protezione DDoS Standard mitiga questi attacchi bloccando il traffico dannoso.<br>- <strong>Attacchi a livello di risorsa (applicazione)</strong>, che interessano i pacchetti dell'applicazione Web. Difendersi da questo tipo di attacco con un web application firewall e Protezione DDoS Standard.</td>
    <td class="tg-lboi"; width=55%>- È necessario abilitare lo standard di protezione DDoS<br>- È necessario applicare i limiti di memoria e CPU del contenitore (anteprima)<br>- Il componente aggiuntivo criteri di Azure per Kubernetes deve essere installato e abilitato nei cluster (anteprima)</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Abilitare la protezione dell'endpoint (punteggio massimo 2)</p></strong>Per assicurarsi che gli endpoint siano protetti da malware, i sensori di comportamento raccolgono ed elaborano i dati dai sistemi operativi degli endpoint e inviano questi dati al cloud privato per l'analisi. L'analisi della sicurezza sfrutta Big Data, Machine Learning e altre origini per consigliare risposte alle minacce. Ad esempio, <a href="/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection">Microsoft Defender ATP</a> usa l'intelligence sulle minacce per identificare i metodi di attacco e generare avvisi di sicurezza.<br>Il Centro sicurezza supporta le soluzioni di protezione endpoint seguenti: Windows Defender, System Center Endpoint Protection, Trend Micro, Symantec versione 12.1.1.1100, McAfee versione 10 per Windows, McAfee versione 10 per Linux e Sophos versione 9 per Linux. Se il Centro sicurezza rileva una qualsiasi di queste soluzioni, non verrà più visualizzata la raccomandazione di installare Endpoint Protection.</td>
    <td class="tg-lboi"; width=55%>- È necessario correggere gli errori di integrità di Endpoint Protection nei set di scalabilità di macchine virtuali<br>- È necessario risolvere i problemi di integrità di Endpoint Protection nei computer<br>- È necessario installare la soluzione Endpoint Protection nei set di scalabilità di macchine virtuali<br>- È necessario installare la soluzione Endpoint Protection nelle macchine virtuali<br>- È necessario risolvere i problemi di integrità dell'agente di monitoraggio nei computer<br>- È necessario installare l'agente di monitoraggio nei set di scalabilità di macchine virtuali<br>- È necessario installare l'agente di monitoraggio nei computer<br>- È necessario installare l'agente di monitoraggio nelle macchine virtuali<br>- Log Analytics Agent deve essere installato nei computer Azure Arc basati su Windows (anteprima)<br>- Log Analytics Agent deve essere installato nei computer Azure Arc basati su Linux (anteprima)<br>- È necessario installare la soluzione Endpoint Protection nei computer</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Abilitare il controllo e la registrazione (punteggio massimo 1)</p></strong>La registrazione dei dati fornisce informazioni approfondite sui problemi precedenti, impedisce i problemi potenziali, può migliorare le prestazioni dell'applicazione e consente di automatizzare le azioni che sarebbero altrimenti manuali.<br>- <strong>Log di gestione e controllo</strong>, che offrono informazioni sulle operazioni di <a href="/azure/azure-resource-manager/management/overview">Azure Resource Manager</a>.<br>- <strong>Log del piano dati</strong>, che offrono informazioni sugli eventi generati durante l'utilizzo di una risorsa di Azure.<br>- <strong>Eventi elaborati</strong>, che offrono informazioni sugli eventi o avvisi analizzati dopo essere stati elaborati.</td>
    <td class="tg-lboi"; width=55%>- È necessario abilitare il controllo in SQL Server<br>- È necessario abilitare i log di diagnostica in Servizi app<br>- È necessario abilitare i log di diagnostica in Azure Data Lake Store<br>- È necessario abilitare i log di diagnostica in Analisi di flusso di Azure<br>- È necessario abilitare i log di diagnostica negli account Batch<br>- È necessario abilitare i log di diagnostica in Data Lake Analytics<br>- È necessario abilitare i log di diagnostica nell'hub eventi<br>- È necessario abilitare i log di diagnostica nell'hub IoT<br>- È necessario abilitare i log di diagnostica in Key Vault<br>- È necessario abilitare i log di diagnostica in App per la logica<br>- È necessario abilitare i log di diagnostica nel servizio di ricerca<br>- È necessario abilitare i log di diagnostica nel bus di servizio<br>- È necessario abilitare i log di diagnostica nei set di scalabilità di macchine virtuali<br>- È necessario configurare le regole di avviso per le metriche negli account Batch<br>- È necessario configurare i gruppi di azione nelle impostazioni di controllo SQL per acquisire attività critiche<br>- È necessario configurare i server SQL con un periodo di conservazione di controllo maggiore di 90 giorni</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Abilita Advanced Threat Protection (Punteggio massimo 0)</p></strong>I piani facoltativi di protezione dalle minacce di Azure Defender del Centro sicurezza di Azure forniscono difese complete per l'ambiente. Quando il Centro sicurezza rileva una minaccia in qualsiasi area dell'ambiente, viene generato un avviso. Questi avvisi descrivono i dettagli sulle risorse interessate, le procedure di correzione consigliate e, in alcuni casi, un'opzione per attivare un'app per la logica in risposta.<br>Ogni piano di Azure Defender è un'offerta separata e facoltativa che è possibile abilitare usando la raccomandazione pertinente in questo controllo di sicurezza.<br><a href="/azure/security-center/threat-protection">Scopri di più sulla protezione dalle minacce nel centro sicurezza</a>.</td>
    <td class="tg-lboi"; width=55%>- La sicurezza dei dati avanzata deve essere abilitata nei server di database SQL di Azure<br>- È necessario abilitare la sicurezza dei dati avanzata in SQL Server nei computer<br>- Advanced Threat Protection deve essere abilitato nelle macchine virtuali<br>- È necessario abilitare Advanced Threat Protection nei piani di servizio app Azure<br>- Advanced Threat Protection deve essere abilitato negli account di archiviazione di Azure<br>- È necessario abilitare Advanced Threat Protection nei cluster del servizio Azure Kubernetes<br>- È necessario abilitare Advanced Threat Protection nei registri di Container Registry di Azure<br>- Advanced Threat Protection deve essere abilitato negli insiemi di credenziali Azure Key Vault</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Implementare le procedure consigliate per la sicurezza (punteggio massimo 0)</p></strong>Le procedure di sicurezza moderne "presuppongono la violazione" del perimetro di rete. Per questo motivo, molte delle procedure consigliate in questo controllo si concentrano sulla gestione delle identità.<br>Perdere chiavi e credenziali è un problema comune. <a href="/azure/key-vault/key-vault-overview">Azure Key Vault</a> protegge le chiavi e i segreti crittografando le chiavi, i file con estensione pfx e le password.<br>Le reti private virtuali (VPN) rappresentano un modo sicuro per accedere alle macchine virtuali. Se le VPN non sono disponibili, usare passphrase complesse e l'autenticazione a due fattori, ad esempio <a href="/azure/active-directory/authentication/concept-mfa-howitworks">Azure AD multi-factor authentication</a>. L'autenticazione a due fattori evita le vulnerabilità legate ad accessi basati solo su nome utente e password.<br>L'uso di piattaforme di autenticazione e autorizzazione robuste è un'altra procedura consigliata. L'uso delle identità federate consente alle organizzazioni di delegare la gestione delle identità autorizzate. Questo è importante anche quando i dipendenti vengono terminati e l'accesso deve essere revocato.</td>
    <td class="tg-lboi"; width=55%>- È necessario designare al massimo tre proprietari per la sottoscrizione<br>- È necessario rimuovere dalla sottoscrizione gli account esterni con autorizzazioni di lettura<br>- È necessario abilitare l'autenticazione a più fattori negli account con autorizzazioni di lettura per la sottoscrizione<br>- È necessario limitare l'accesso agli account di archiviazione con configurazioni del firewall e di rete virtuale<br>- È necessario rimuovere tutte le regole di autorizzazione, ad eccezione di RootManageSharedAccessKey, dallo spazio dei nomi dell'hub eventi<br>- È necessario effettuare il provisioning di un amministratore di Azure Active Directory per i server SQL<br>- È necessario abilitare la sicurezza dei dati avanzata nelle istanze gestite<br>- È necessario definire le regole di autorizzazione nell'istanza dell'hub eventi<br>- È necessario eseguire la migrazione degli account di archiviazione alle nuove risorse di Azure Resource Manager<br>- È necessario eseguire la migrazione delle macchine virtuali alle nuove risorse di Azure Resource Manager<br>- È necessario associare le subnet a un gruppo di sicurezza di rete<br>- [Anteprima] È necessario abilitare Windows Exploit Guard <br>- [Anteprima] È necessario installare l'agente di configurazione guest<br>- Le macchine virtuali non con connessione Internet devono essere protette con i gruppi di sicurezza di rete<br>- Backup di Azure deve essere abilitato per le macchine virtuali<br>- Il backup con ridondanza geografica deve essere abilitato per il database di Azure per MariaDB<br>- Il backup con ridondanza geografica deve essere abilitato per database di Azure per MySQL<br>- Il backup con ridondanza geografica deve essere abilitato per database di Azure per PostgreSQL<br>- PHP deve essere aggiornato alla versione più recente per l'app per le API<br>- PHP deve essere aggiornato alla versione più recente per l'app Web<br>- Java deve essere aggiornato alla versione più recente per l'app per le API<br>- Java deve essere aggiornato alla versione più recente per l'app per le funzioni<br>- Java deve essere aggiornato alla versione più recente per l'app Web<br>- Python deve essere aggiornato alla versione più recente per l'app per le API<br>- Python deve essere aggiornato alla versione più recente per l'app per le funzioni<br>- Python deve essere aggiornato alla versione più recente per l'app Web<br>- La conservazione dei controlli per SQL Server deve essere impostata almeno su 90 giorni</td>
  </tr>
</tbody>
</table>


</div>




## <a name="secure-score-faq"></a>Domande frequenti sul punteggio di sicurezza

### <a name="if-i-address-only-three-out-of-four-recommendations-in-a-security-control-will-my-secure-score-change"></a>Se si correggono solo tre su quattro raccomandazioni in un controllo di sicurezza, il punteggio di sicurezza cambierà?
No. Non verrà modificato fino a quando non si correggeranno tutte le raccomandazioni relative una singola risorsa. Per ottenere il punteggio massimo per un controllo, è necessario correggere tutte le raccomandazioni relative a tutte le risorse.

### <a name="if-a-recommendation-isnt-applicable-to-me-and-i-disable-it-in-the-policy-will-my-security-control-be-fulfilled-and-my-secure-score-updated"></a>Se una raccomandazione non è applicabile all'utente e viene disabilitata nei criteri, il controllo di sicurezza sarà soddisfatto e il punteggio di sicurezza sarà aggiornato?
Sì. Si consiglia di disabilitare le raccomandazioni quando non sono applicabili all'ambiente in uso. Per istruzioni su come disabilitare una raccomandazione specifica, vedere [Disabilitare i criteri di sicurezza](./tutorial-security-policy.md#disable-security-policies-and-disable-recommendations).

### <a name="if-a-security-control-offers-me-zero-points-towards-my-secure-score-should-i-ignore-it"></a>Se un controllo di sicurezza offre zero punti per il punteggio di sicurezza, è consigliabile ignorarlo?
In alcuni casi, si noterà un punteggio massimo del controllo maggiore di zero, ma l'effetto sarà lo stesso di un punteggio pari a zero. Quando il punteggio incrementale per la correzione delle risorse è trascurabile, viene arrotondato a zero. Non ignorare queste raccomandazioni perché contribuiscono a migliorare la sicurezza. L'unica eccezione è il controllo "Procedura consigliata aggiuntiva". La correzione di queste raccomandazioni non aumenterà il punteggio, ma migliorerà la sicurezza complessiva.

## <a name="next-steps"></a>Passaggi successivi

Questo articolo descrive il punteggio di sicurezza e i controlli di sicurezza introdotti. Per i materiali correlati, vedere gli articoli seguenti:

- [Learn about the different elements of a recommendation](security-center-recommendations.md) (Informazioni sui diversi elementi di una raccomandazione)
- [Learn how to remediate recommendations](security-center-remediate-recommendations.md) (Informazioni su come correggere le raccomandazioni)