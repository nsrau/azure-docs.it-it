<properties
    pageTitle="Soluzione Stato replica di Active Directory in Log Analytics | Microsoft Azure"
    description="Il pacchetto della soluzione Stato replica di Active Directory monitora a intervalli regolari l'ambiente Active Directory per rilevare eventuali errori di replica e segnala i risultati nel dashboard di OMS."
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
    ms.date="10/10/2016"
    ms.author="banders"/>


# <a name="active-directory-replication-status-solution-in-log-analytics"></a>Soluzione Stato replica di Active Directory in Log Analytics

Active Directory è un componente chiave di un ambiente IT aziendale. Per garantire disponibilità e prestazioni elevate, ogni controller di dominio ha la propria copia del database di Active Directory. I controller di dominio si replicano tra loro per propagare le modifiche all'interno dell'azienda. Gli errori in questo processo di replica possono causare una serie di problemi all'interno dell'azienda.

Il pacchetto della soluzione Stato replica di Active Directory monitora a intervalli regolari l'ambiente Active Directory per rilevare eventuali errori di replica e segnala i risultati nel dashboard di OMS.

## <a name="installing-and-configuring-the-solution"></a>Installazione e configurazione della soluzione
Usare le informazioni seguenti per installare e configurare la soluzione.

- Gli agenti devono essere installati nei controller di dominio membri del dominio da valutare o nei server membri configurati per inviare dati di replica di AD a OMS. Per informazioni su come connettere i computer Windows a OMS, vedere [Connettere computer Windows a Log Analytics](log-analytics-windows-agents.md). Se il controller di dominio fa già parte di un ambiente System Center Operations Manager esistente che si intende connettere a OMS, vedere [Connettere Operations Manager a Log Analytics](log-analytics-om-agents.md).
- Aggiungere la soluzione Stato replica di Active Directory all'area di lavoro di OMS usando la procedura descritta in [Aggiungere soluzioni di Log Analytics dalla Raccolta soluzioni](log-analytics-add-solutions.md).  Non è richiesta alcuna ulteriore configurazione.


## <a name="ad-replication-status-data-collection-details"></a>Dettagli sulla raccolta dati di Stato replica di Active Directory

La tabella seguente descrive i metodi di raccolta dati e altri dettagli sul modo in cui vengono raccolti i dati per Stato replica di Active Directory.

| piattaforma | Agente diretto | Agente SCOM | Archiviazione di Azure | SCOM obbligatorio? | Dati dell'agente SCOM inviati con il gruppo di gestione | frequenza della raccolta |
|---|---|---|---|---|---|---|
|Windows|![Sì](./media/log-analytics-ad-replication-status/oms-bullet-green.png)|![Sì](./media/log-analytics-ad-replication-status/oms-bullet-green.png)|![No](./media/log-analytics-ad-replication-status/oms-bullet-red.png)|![No](./media/log-analytics-ad-replication-status/oms-bullet-red.png)|![Sì](./media/log-analytics-ad-replication-status/oms-bullet-green.png)| ogni 5 giorni|


## <a name="optionally,-enable-a-non-domain-controller-to-send-ad-data-to-oms"></a>Abilitare un controller non di dominio per l'invio di dati di Active Directory a OMS (facoltativo)
Se non si intende connettere i controller di dominio direttamente a OMS è possibile usare qualsiasi altro computer connesso a OMS nel dominio per raccogliere i dati per il pacchetto della soluzione Stato replica di Active Directory e abilitarlo per l'invio dei dati.

### <a name="to-enable-a-non-domain-controller-to-send-ad-data-to-oms"></a>Per abilitare un controller non di dominio per l'invio di dati di Active Directory a OMS
1.  Verificare che il computer sia membro del dominio da monitorare con la soluzione Stato replica di Active Directory.
2.  [Connettere il computer Windows a OMS](log-analytics-windows-agents.md) oppure [connetterlo usando l'ambiente Operations Manager esistente](log-analytics-om-agents.md), se non è già connesso.
3.  Nel computer impostare la chiave del Registro di sistema seguente:
    - Chiave: **HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\HealthService\Parameters\Management Groups\<ManagementGroupName>\Solutions\ADReplication**
    - Valore: **IsTarge**
    - Dati valore: **true**

    >[AZURE.NOTE]Queste modifiche non saranno effettive fino al riavvio del servizio Microsoft Monitoring Agent (HealthService.exe).

## <a name="understanding-replication-errors"></a>Informazioni sugli errori di replica
Dopo l'invio dei dati dello stato replica di Active Directory a OMS, nel dashboard OMS verrà visualizzato un riquadro simile al seguente, con il numero di errori di replica attuale.  
![Riquadro Stato replica di Active Directory](./media/log-analytics-ad-replication-status/oms-ad-replication-tile.png)

**Gli errori di replica critici** sono quelli che si trovano almeno al 75% della [durata di rimozione definitiva](https://technet.microsoft.com/library/cc784932%28v=ws.10%29.aspx) della foresta Active Directory.

Facendo clic sul riquadro verranno visualizzate altre informazioni sugli errori.
![Dashboard Stato replica di Active Directory](./media/log-analytics-ad-replication-status/oms-ad-replication-dash.png)


### <a name="destination-server-status-and-source-server-status"></a>Stato del server di destinazione e stato del server di origine
Questi pannelli indicano lo stato dei server di destinazione e dei server di origine in cui si verificano errori di replica. Il numero accanto al nome di ogni controller di dominio indica il numero di errori di replica presenti.

Gli errori vengono visualizzati sia per i server di destinazione, sia per i server di origine perché alcuni problemi sono più facili da risolvere dalla prospettiva del server di origine e altri dalla prospettiva del server di destinazione.

In questo esempio si noterà che molti server di destinazione mostrano approssimativamente lo stesso numero di errori, ma uno dei server di origine (ADDC35) presenta più errori di tutti gli altri. È probabile che un problema nel server ADDC35 impedisca l'invio dei dati ai partner di replica. Con la correzione dei problemi in ADDC35 verranno probabilmente risolti molti degli errori visualizzati nel pannello dei server di destinazione.

### <a name="replication-error-types"></a>Tipi di errori di replica
Questo pannello mostra informazioni sui tipi di errori rilevati nell'azienda. Ogni errore ha un codice numerico univoco e un messaggio che consentono di determinare la causa radice dell'errore.

L'anello nella parte superiore dà un'idea degli errori più e meno frequenti nell'ambiente.

È così possibile determinare quando più controller di dominio presentano lo stesso errore di replica. In questo caso, una soluzione individuata per un controller di dominio potrà essere riprodotta in altri controller di dominio interessati dallo stesso errore.

### <a name="tombstone-lifetime"></a>durata di rimozione definitiva
La durata di rimozione definitiva determina per quanto tempo un oggetto eliminato viene conservato nel database di Active Directory. Quando un oggetto eliminato supera la durata di rimozione definitiva, un processo di garbage collection lo rimuove automaticamente dal database di Active Directory.

La durata di rimozione definitiva predefinita è di 180 giorni per le versioni più recenti di Windows, mentre è di 60 giorni per le versioni precedenti e può essere modificata in modo esplicito da un amministratore di Active Directory.

È importante sapere se sono presenti errori di replica che stanno per raggiungere o hanno superato la durata di rimozione definitiva. Se due controller di dominio presentano un errore di replica che persiste oltre la durata di rimozione definitiva, la replica verrà disabilitata tra i due controller di dominio, anche se viene risolto l'errore di replica sottostante.

Il pannello della durata di rimozione definitiva consente di identificare le aree in cui esiste questo rischio. Ogni errore della categoria **Over 100% TSL** rappresenta una partizione che non è stata replicata tra server di origine e di destinazione per almeno la durata di rimozione definitiva della foresta.

In questo caso non sarà sufficiente correggere l'errore di replica. Sarà necessario effettuare almeno un'analisi manuale per identificare e pulire gli oggetti residui prima di poter riavviare la replica. Potrebbe anche essere necessario ritirare un controller di dominio.

Oltre a identificare eventuali errori di replica che si sono protratti oltre la durata di rimozione definitiva, è opportuno prestare attenzione a eventuali errori che rientrano nelle categorie **50-75% TSL** o **75-100% TSL**.

Si tratta di errori chiaramente residui, non temporanei, quindi è probabilmente necessario l'intervento dell'utente per risolverli. L'aspetto positivo è che non hanno ancora raggiunto la durata di rimozione definitiva. Se questi problemi vengono risolti tempestivamente e *prima* che raggiungano la durata di rimozione definitiva, la replica potrà essere riavviata con un intervento manuale minimo.

Come accennato in precedenza, il riquadro del dashboard per la soluzione Stato replica di Active Directory indica il numero di errori di replica *critici* presenti nell'ambiente, ovvero gli errori oltre il 75% della durata di rimozione definitiva, compresi gli errori oltre il 100% della durata di rimozione definitiva. Cercare di mantenere questo numero a 0.

>[AZURE.NOTE] Tutti i calcoli della percentuale della durata di rimozione definitiva si basano sulla durata di rimozione definitiva effettiva per la foresta di Active Directory in uso, quindi tali percentuali sono accurate anche se è impostato un valore di durata di rimozione definitiva personalizzato.

### <a name="ad-replication-status-details"></a>Dettagli di Stato replica di Active Directory
Quando si fa clic su un elemento in uno degli elenchi, sarà possibile accedere ad altre informazioni usando la ricerca nei log. I risultati vengono filtrati per indicare solo gli errori correlati all'elemento. Se ad esempio si fa clic sul primo controller di dominio elencato in **Stato server di destinazione (ADDC02)**, i risultati della ricerca verranno filtrati per indicare gli errori con quel controller di dominio elencato come server di destinazione:

![Errori di stato replica di Active Directory nei risultati della ricerca](./media/log-analytics-ad-replication-status/oms-ad-replication-search-details.png)

A questo punto è possibile filtrare ulteriormente, modificare la query di ricerca e così via. Per altre informazioni sull'uso della ricerca nei log vedere [Ricerche nei log](log-analytics-log-searches.md).

Il campo **HelpLink** indica l'URL di una pagina di TechNet con altre informazioni sullo specifico errore. È possibile copiare e incollare il collegamento nella finestra del browser per visualizzare le informazioni sulla risoluzione dei problemi e la correzione dell'errore.

È anche possibile fare clic su **Export** per esportare i risultati in Excel. Sarà così possibile visualizzare i dati degli errori di replica nel modo desiderato.

![errori di stato replica di Active Directory esportati in Excel](./media/log-analytics-ad-replication-status/oms-ad-replication-export.png)

## <a name="ad-replication-status-faq"></a>Domande frequenti su Stato replica di Active Directory
**D: con quale frequenza vengono aggiornati i dati di Stato replica di Active Directory?**
 R: le informazioni vengono aggiornate ogni 5 giorni.

**D: è possibile configurare la frequenza di aggiornamento dei dati?**
 R: attualmente non è possibile.

**D: è necessario aggiungere tutti i controller di dominio all'area di lavoro di OMS per visualizzare lo stato della replica?**
 R: no, è necessario aggiungere un solo controller di dominio. Se sono presenti più controller di dominio nell'area di lavoro di OMS, verranno inviati a OMS i dati di tutti i controller.

**D: Non intendo aggiungere controller di dominio all'area di lavoro di OMS. È possibile usare comunque la soluzione Stato replica di Active Directory?**
A: Sì. È possibile impostare il valore di una chiave del Registro di sistema per abilitare questa opzione. Vedere [Per abilitare un controller non di dominio per l'invio di dati di Active Directory a OMS](#to-enable-a-non-domain-controller-to-send-ad-data-to-oms).

**D: come si chiama il processo che esegue la raccolta dati?**
 R: AdvisorAssessment.exe

**D: quanto tempo occorre per la raccolta dati?**
 R: il tempo necessario per la raccolta dati dipende dalle dimensioni dell'ambiente Active Directory, ma in genere è inferiore a 15 minuti.

**D: quali tipi di dati vengono raccolti?**
 R: le informazioni di replica vengono raccolte tramite LDAP.

**D: è possibile definire la data/ora per la raccolta dati?**
 R: attualmente non è possibile.

**D: quali autorizzazioni sono necessarie per raccogliere i dati?**
 R: le normali autorizzazioni utente in Active Directory sono in genere sufficienti.

## <a name="troubleshoot-data-collection-problems"></a>Risolvere i problemi di raccolta dati
Per raccogliere i dati, il pacchetto della soluzione Stato replica di Active Directory richiede la connessione di almeno un controller di dominio all'area di lavoro di OMS. Fino a quel momento verrà visualizzato un messaggio che indica che **la raccolta dati è in corso**.

Se occorre assistenza per la connessione di uno dei controller di dominio è possibile vedere il documento [Connettere computer Windows a Log Analytics](log-analytics-windows-agents.md). In alternativa, se il controller di dominio è già connesso a un ambiente System Center Operations Manager esistente, è possibile vedere il documento [Connettere System Center Operations Manager a Log Analytics](log-analytics-om-agents.md).

Se non si intende connettere i controller di dominio direttamente a OMS o SCOM, vedere [Per abilitare un controller non di dominio per l'invio di dati di Active Directory a OMS](#to-enable-a-non-domain-controller-to-send-ad-data-to-oms).


## <a name="next-steps"></a>Passaggi successivi

- Usare le [ricerche nei log in Log Analytics](log-analytics-log-searches.md) per visualizzare dati dettagliati dello stato di replica di Active Directory.



<!--HONumber=Oct16_HO2-->


