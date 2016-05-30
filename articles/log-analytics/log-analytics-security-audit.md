<properties
	pageTitle="Soluzione Security and Audit in Log Analytics | Microsoft Azure"
	description="La soluzione Security and Audit in Log Analytics consente di ottenere una panoramica completa sulla situazione della sicurezza IT all'interno dell'organizzazione grazie alla disponibilità di query di ricerca predefinite relative a problemi rilevanti che richiedono attenzione."
	services="log-analytics"
	documentationCenter=""
	authors="bandersmsft"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="log-analytics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/03/2016"
	ms.author="banders"/>

# Soluzione Security and Audit in Log Analytics

La soluzione Security and Audit in Log Analytics consente di ottenere una panoramica completa sulla situazione della sicurezza IT all'interno dell'organizzazione grazie alla disponibilità di query di ricerca predefinite relative a problemi rilevanti che richiedono attenzione.

## Installazione e configurazione della soluzione
Usare le informazioni seguenti per installare e configurare la soluzione.

- Aggiungere la soluzione Security and Audit all'area di lavoro OMS usando la procedura descritta nell'articolo [Aggiungere soluzioni di Log Analytics dalla Raccolta soluzioni](log-analytics-add-solutions.md). Non è richiesta alcuna ulteriore configurazione.



## Dettagli sulla raccolta dei dati relativi a Security and Audit

La tabella seguente mostra i metodi di raccolta dati e altri dettagli sul modo in cui vengono raccolti i dati per le soluzioni Security and Audit.

|tipo di dati| piattaforma | Agente diretto | Agente SCOM | Archiviazione di Azure | SCOM obbligatorio? | Dati dell'agente SCOM inviati con il gruppo di gestione | frequenza della raccolta |
|---|---|---|---|---|---|---|---|
|Registri eventi della sicurezza di Windows|Windows|![Sì](./media/log-analytics-security-audit/oms-bullet-green.png)|![Sì](./media/log-analytics-security-audit/oms-bullet-green.png)|![Sì](./media/log-analytics-security-audit/oms-bullet-green.png)| ![No](./media/log-analytics-security-audit/oms-bullet-red.png)|![No](./media/log-analytics-security-audit/oms-bullet-red.png)| Per l'Archiviazione di Azure: 10 min.; per l'agente: all'arrivo|
|Registri firewall di Windows|Windows|![Sì](./media/log-analytics-security-audit/oms-bullet-green.png)|![Sì](./media/log-analytics-security-audit/oms-bullet-green.png)|![No](./media/log-analytics-security-audit/oms-bullet-red.png)| ![No](./media/log-analytics-security-audit/oms-bullet-red.png)|![No](./media/log-analytics-security-audit/oms-bullet-red.png)| all'arrivo|
|Registri eventi di Windows|Windows|![Sì](./media/log-analytics-security-audit/oms-bullet-green.png)|![Sì](./media/log-analytics-security-audit/oms-bullet-green.png)|![Sì](./media/log-analytics-security-audit/oms-bullet-green.png)| ![No](./media/log-analytics-security-audit/oms-bullet-red.png)|![Sì](./media/log-analytics-security-audit/oms-bullet-green.png)| per l'Archiviazione di Azure: 1 min.; per l'agente: all'arrivo|


## Procedure consigliate per le analisi forensi

**Cercare le prove**

Quando si esegue un'analisi forense usando la soluzione Security and Audit, si cercano le prove lasciate da potenziali utenti malintenzionati. Indipendentemente dalle operazioni svolte dagli utenti nel proprio ambiente IT, molte delle attività a cui partecipano generano elementi di sicurezza. È importante notare che spesso gli autori degli attacchi eliminano i log e che questa operazione è in genere la prima misura adottata per cancellare le tracce.

Tuttavia, che gli utenti accedano ai propri computer in modalità locale o remota, la prova dell'accesso è archiviata nei registri eventi. OMS raccoglie questi elementi *nel momento stesso in cui vengono generati*, prima che chiunque possa alterarli, e consente di eseguire diversi tipi di analisi correlando i dati tra più computer.

**Informazioni sulla configurazione e la raccolta di eventi di controllo**

Per sfruttare al massimo la soluzione Security and Audit, configurare il livello di eventi di controllo raccolti dall'ambiente Windows più adatto alle proprie esigenze usando le seguenti risorse Web.

- [Come configurare le impostazioni dei criteri di sicurezza](https://technet.microsoft.com/library/dn135243&#40;v=ws.10&#41;.aspx)
- [Configurazione dei criteri di controllo avanzati](https://technet.microsoft.com/library/jj852202&#40;v=ws.10&#41;.aspx)
- [Suggerimenti per i criteri di controllo](https://technet.microsoft.com/library/dn487457.aspx)

**Abilitare AppLocker**

È inoltre consigliabile abilitare gli eventi di AppLocker per ottenere informazioni dettagliate sulle esecuzioni di processi nel proprio ambiente IT. Per ulteriori informazioni, vedere l'articolo relativo alla [configurazione dei criteri di AppLocker solo a scopo di controllo](https://technet.microsoft.com/library/hh994622.aspx).

**Configurare il livello di controllo per gli eventi di Windows**

L'ambiente di elaborazione di Windows offre la possibilità di configurare il livello di acquisizione di record correlati alla sicurezza. È ad esempio possibile configurare l'ambiente in modo che venga generato un evento ogni volta che un utente accede a un file oppure lo legge o lo apre. Il livello di dettaglio che si desidera ottenere varia in base alle specifiche esigenze. Tutte le opzioni abilitate, tuttavia, sono associate a un costo, perché è necessario archiviare tutte le informazioni raccolte. Per questo motivo, negli ambienti IT di molte organizzazioni si decide di non abilitare la raccolta di dati relativi alla lettura o scrittura di oggetti. Ogni volta che un utente accede a un file possono essere generati migliaia di eventi, alcuni dei quali assolutamente superflui. Il livello di raccolta che viene scelto dipende dal giudizio dell'utente.

>[AZURE.NOTE] Se si usa l'agente diretto e si dispone di un server proxy nell'organizzazione, è necessario configurarlo per consentire all'agente di accedere a OMS. Per ulteriori informazioni, vedere l'articolo [Configurare le impostazioni di proxy e firewall in Log Analytics](log-analytics-proxy-firewall.md).

## Procedure consigliate per l'esecuzione di indagini in caso di violazione della sicurezza

**Individuazione di modelli di attività anomale**

Le violazioni della sicurezza in genere originano da credenziali legittime e prevedono il tentativo da parte di un utente malintenzionato di ottenere privilegi elevati tramite attacchi. La soluzione Security and Audit non è preposta al rilevamento delle intrusioni, bensì all'analisi e all'individuazione di modelli di attività anomale con problemi rilevanti. È consigliabile, ad esempio, analizzare le attività anomale seguenti e tutte le altre eventualmente visualizzate nel pannello **Notable Issues**.

- Accessi insoliti a un computer da parte di un utente che normalmente non lo usa
- Enumerazione di rete insolita da utenti o computer atipici
- Nuovi account utente creati con diritti amministrativi
- Modifiche apportate a criteri di registrazione o sicurezza
- File eseguibili sospetti

## Procedere consigliate per gli scenari di controllo

È possibile che l'organizzazione debba rispettare criteri e normative di conformità per computer e reti la cui applicazione richiede l'uso di record di controllo estesi. Se, ad esempio, l'organizzazione opera nel settore finanziario, è possibile che debba mantenere record che indicano quale utente ha eseguito una specifica operazione sulla rete in un particolare momento. Potrebbe inoltre essere necessario generare a richiesta report dettagliati dell'attività di un utente specifico o di un server selezionato ed essere in grado di tornare indietro nel tempo di molti mesi, a volte anche di anni.

La soluzione Security and Audit consente di raccogliere dati di controllo in tutto l'ambiente IT, indipendentemente dal fatto che i computer siano in locale o nel cloud. Tutti i dati di controllo vengono archiviati, indicizzati e mantenuti per la sottoscrizione. Con una sottoscrizione Premium a OMS, viene archiviata una quantità di dati illimitata per un anno. In questo modo è possibile visualizzare i dati di controllo, eseguire ricerche e correlare diversi tipi di dati e computer per ottenere risultati completi per qualsiasi intervallo di tempo a partire dall'inizio della raccolta dei dati.

**Usare Criteri di gruppo per raccogliere dati di controllo**

Tutti i dati di controllo che si desidera raccogliere e inviare a OMS sono completamente gestiti con Criteri di gruppo, che consente di definire configurazioni di sicurezza come parte di un oggetto Criteri di gruppo. Tali configurazioni sono collegate a contenitori Active Directory come siti, domini e unità organizzative e consentono di gestire le impostazioni di sicurezza. I dati dei criteri vengono registrati e successivamente inviati al servizio OMS.

**Usare AppLocker per raccogliere dati di controllo**

Se oltre alle impostazioni dei criteri locali si usa AppLocker per raccogliere dati di controllo, OMS raccoglierà tali dati consentendone la visualizzazione.

## Condurre una semplice indagine riguardo a un file eseguibile sospetto

1. Accedere a OMS.
2. Nella pagina **Overview** rivedere le informazioni visualizzate nel riquadro **Security and Audit** e quindi fare clic sugli elementi di interesse. ![Immagine della pagina Overview](./media/log-analytics-security-audit/oms-security-audit-dash02.png)
3. Nella pagina **Security and Audit** rivedere le informazioni riportate nel pannello **NOTABLE ISSUES**. Nella schermata di esempio per la giornata odierna sono visualizzati 6 problemi rilevanti, 2 dei quali risalgono a ieri. In questo esempio è presente 1 file eseguibile sospetto. Fare clic su **Suspicious Executables** nel pannello **NOTABLE ISSUES**. ![Immagine della pagina Security and Audit](./media/log-analytics-security-audit/oms-security-audit-dash03.png)
4. Nella pagina Search vengono visualizzati la query e i risultati relativi al file eseguibile sospetto selezionato. Nell'esempio viene restituito 1 risultato e viene visualizzato il relativo hash file. Fare clic sull'ID **FILEHASH**. ![Immagine dei risultati di hash file nella pagina Search](./media/log-analytics-security-audit/oms-security-audit-search01.png)
5. Nella pagina Search vengono visualizzate informazioni aggiuntive sul file eseguibile, inclusi il relativo percorso e il nome del processo. Fare clic su **Process &lt;file name&gt;**, nell'esempio HEXEDIT.EXE. ![Immagine dell'elaborazione dei risultati nella pagina Search](./media/log-analytics-security-audit/oms-security-audit-search02.png)
6. Nella pagina Search il nome del processo viene aggiunto alla query tra virgolette. In questo esempio il nome è "**HEXEDIT.EXE"**. ![Immagine della query nella pagina Search](./media/log-analytics-security-audit/oms-security-audit-search03.png)
7. Nella casella Search rimuovere qualsiasi informazione tranne il nome del processo e le virgolette e quindi fare clic sull'icona Search. ![Immagine delle informazioni di ricerca dettagliate](./media/log-analytics-security-audit/oms-security-audit-search04.png)
8. Nella pagina Search vengono visualizzate informazioni dettagliate sul processo, inclusi i computer sui quali viene eseguito e l'account utente usato, nonché la data e l'ora in cui è stato creato l'evento relativo al processo.
9. Le informazioni trovate consentono di eseguire le azioni correttive necessarie. Se, ad esempio, risulta che il file eseguibile è un malware, è necessario rimuoverlo da tutti i computer che ha infettato. Il file eseguibile viene rimosso e OMS riceve eventi di log e controllo aggiornati relativi ai computer. I valori presenti nel pannello NOTABLE ISSUES verranno modificati il giorno successivo.

## Analizzare gli indirizzi IP dannosi

Un metodo per proteggere i server consiste nell’assicurarsi che non comunichino con nessun indirizzo IP noto dannoso o sospetto. In OMS una visualizzazione centralizzata mostra tutti gli IP dannosi noti con cui è possibile che comunichino i server e i client gestiti. Usando Microsoft Threat Intelligence Center (MSTIC), OMS ottiene aggiornamenti orari sui più recenti IP dannosi noti e informa se qualcuno dei server può essere stato compromesso. Il team MSTIC collabora con diversi partner che analizzano le informazioni sulle minacce per raccogliere e fornire questo elenco consolidato al servizio OMS.

Gli IP dannosi vengono visualizzati nella soluzione Security and Audit nel portale di OMS, in **Distinct Suspicious IP Addresses Accessed**.

È possibile aprire il riquadro e visualizzare l’elenco completo dei singoli indirizzi IP sospetti con cui i dispositivi potrebbero essere in comunicazione. OMS analizza tutte le origini dati inviate al servizio, tra cui:

- Registri firewall di Windows
- Log di IIS
- WireData


OMS visualizza diversi campi relativi agli indirizzi IP sospetti nei risultati della ricerca nei log. Alcuni dei più importanti sono:

- **IndicatorThreatType**: alcuni esempi botnet, proxy, darknet, comandi malware e nodi di controllo.
- **Description**: tipo di minaccia e livello di probabilità che l’indirizzo IP sia dannoso. Sono gli stessi dati usati da Microsoft per proteggersi, resi disponibili per gli utenti di OMS.

Per un elenco completo di campi di indirizzi IP sospetti, vedere [Campi della ricerca nei log di IP dannosi](#Malicious-IP-log-search-fields) di seguito.

### Per eseguire un’analisi degli indirizzi IP dannosi

In questo esempio verrà esaminato un possibile indirizzo IP dannoso usando i dati del log di Windows Firewall.

1. Accedere a OMS.
2. Nella pagina **Overview** rivedere le informazioni visualizzate nel riquadro **Security and Audit** e quindi fare clic sugli elementi di interesse. ![Immagine della pagina Overview](./media/log-analytics-security-audit/oms-security-audit-dash02.png)  
3. Nella pagina **Security and Audit** riesaminare le informazioni di riepilogo contenute nel riquadro **Distinct Suspicious IP Addresses Accessed**. ![Pagina Security and Audit con l’IP sospetto](./media/log-analytics-security-audit/oms-maliciousip-01.png)  
4. In questo esempio sono presenti 6 indirizzi IP sospetti. Fare clic sul riquadro **Distinct Suspicious IP Addresses Accessed**.
5. La ricerca visualizza la query e i risultati per gli indirizzi IP dannosi trovati. Nell’esempio ci sono 6 risultati e vengono visualizzati gli indirizzi IP. ![Risultati della ricerca con gli indirizzi IP sospetti](./media/log-analytics-security-audit/oms-maliciousip-02-revised.png)  
6. Nell'immagine precedente si noti l’ultimo indirizzo IP. In questo esempio è **94.102.56.130**. Fare clic sull’indirizzo.
7. La ricerca visualizza i risultati in dettaglio, mostrando diversi dati degli indicatori di minaccia. Fare clic su **show more** per visualizzare il set completo di risultati. ![Risultati della ricerca con i dati dettagliati degli indicatori di minaccia](./media/log-analytics-security-audit/oms-maliciousip-03-revised.png)  
8. Per visualizzare un elenco di tutti i server dell’ambiente che potrebbero essere in comunicazione con un IP dannoso, è possibile usare la query di ricerca nei log seguente.

    ```
    IsActive=True | measure count() by Computer
    ```

### Esecuzione di azioni correttive per la comunicazione con gli IP dannosi

Se si è certi che un componente o un processo in rete stia comunicando con indirizzi IP dannosi noti e inviando i dati, l’azione da eseguire è ovvia:

- Verificare che la risorsa stia inviando dati all’indirizzo IP dannoso.
- Se possibile, rimuovere o bloccare il software nel computer che sta inviando i dati e impedire al software specifico la comunicazione con indirizzi IP specifici.
- Determinare se le credenziali utente che eseguono un eseguibile o un processo possono essere state compromesse analizzando gli account con cui viene eseguito il processo in questione. Determinare a quali altre risorse può avere effettuato l’accesso.
- Determinare come il software è stato installato nel computer.
- Lasciare temporaneamente installato il software in questione per poterne monitorare attentamente l’attività. Dopo una valutazione completa, eseguire l’azione correttiva, se necessario.


Se tuttavia *non si è certi* che i dati vengano inviati a un indirizzo IP dannoso noto o se la *probabilità è in discussione*, le azioni correttive sono meno ovvie. In generale, è consigliabile indagare eseguendo alcuni dei passaggi elencati sopra. In altri casi, è forse preferibile non eseguire alcuna azione. Infine, è opportuno che sia la persona che conosce meglio l’infrastruttura IT dell’organizzazione a stabilire come gestire le potenziali minacce.


I log del firewall indicano la direzione del flusso di dati. Quando il firewall è configurato per bloccare le comunicazioni in uscita (visualizzate come invii nei log) con IP specifici, i tentativi di comunicazione da parte del software potenzialmente compromesso con IP dannosi vengono visualizzati come *bloccati* nei risultati della ricerca nei log. In questo caso è consigliabile un’azione aggiuntiva. Infatti, il software compromesso, anche se può essere bloccato per un indirizzo IP specifico, in futuro potrebbe cercare di comunicare con altri indirizzi IP dannosi sconosciuti e potenziali.

I log del firewall mostrano anche quando i dati in ingresso vengono bloccati e per questo è possibile rilevare un certo grado di attività. È il caso di preoccuparsi? Molto probabilmente, no. Quando i log del firewall visualizzano dati in ingresso bloccati, è la prova che il firewall sta svolgendo la propria funzione, ovvero proteggere l’infrastruttura.


### Informazioni sulle minacce degli IP dannosi

Microsoft condivide dati con numerose organizzazioni per creare un elenco riepilogativo di tutti gli indicatori noti che possono compromettere i dati. Il riepilogo ha lo scopo di identificare gli accessi ai set di dati e di mostrare i dettagli estesi delle minacce come collegamenti per poter visualizzare facilmente ulteriori dettagli. Queste informazioni sulle minacce vengono visualizzate nei risultati della ricerca nei log per gli IP dannosi.

Sono 3 i metodi con cui Microsoft riceve i dati sulle minacce, che vengono aggregati per formare un set di dati di informazioni sulle minacce. Questi provider si suddividono sostanzialmente nelle categorie seguenti:

- Sottoscrizioni a pagamento, ovvero società con cui Microsoft ha stipulato un contratto per acquistarne i dati.
- Dati dei partner, ovvero aziende che raccolgono informazioni sulla sicurezza. Questi dati sono in pratica “informazioni di qualità”, perché, pur non contenendo report dettagliati, sono considerati validi.
- Processi Microsoft interni, essenzialmente Microsoft Threat Intelligence Center (MSTIC).

#### Campi della ricerca nei log di IP dannosi

Nei risultati della ricerca nei log vengono visualizzati i campi seguenti degli indirizzi IP dannosi.

|Campo di ricerca| Descrizione |
|---|---|
| **IsActive**| Segnala che l’indicatore è attivo e deve essere considerato un riscontro valido se corrisponde a un indirizzo IP trovato. MSTIC gestisce questo indicatore.|
|**Confidence**| Visualizza un valore compreso tra 1 e 100. MSTIC gestisce questo valore e gli algoritmi che lo definiscono. È un valore utile per filtrare i risultati della ricerca.|
|**TLPLevel**| Livello del protocollo di segnalazione del traffico. I valori sono Green, Amber e Red. Questi valori corrispondono rispettivamente a un impatto aziendale basso, medio e alto.|
| **IndicatorThreatType** | Breve descrizione del tipo di minaccia presentata dall’indicatore, ad esempio Botnet, DDoS, Malware, Malicious URL, Malicious IP, Phishing e Spam.|
| **Severity** | Visualizza un valore compreso tra 0 e 5. Indica la gravità della minaccia.|
| **MaliciousIP** | Indirizzo IP dell’host dannoso.|
| **CommunicationDirection** | Indica la direzione seguita dal traffico IP verso l’indirizzo IP dannoso.|

## Passaggi successivi

- [Eseguire ricerche nei log](log-analytics-log-searches.md) per visualizzare dati dettagliati di Security and Audit.

<!---HONumber=AcomDC_0518_2016-->