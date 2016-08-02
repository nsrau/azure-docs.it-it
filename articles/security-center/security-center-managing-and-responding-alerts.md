<properties
   pageTitle="Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure | Microsoft Azure"
   description="Questo documento illustra come usare le funzionalità del Centro sicurezza di Azure per gestire e rispondere agli avvisi di sicurezza."
   services="security-center"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="security-center"
   ms.topic="hero-article"
   ms.devlang="na"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/21/2016"
   ms.author="yurid"/>

# Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure
Questo documento illustra come usare il Centro sicurezza di Azure per gestire e rispondere agli avvisi di sicurezza.

## Informazioni sugli avvisi di sicurezza
Il Centro sicurezza raccoglie, analizza e integra automaticamente i dati di log delle risorse di Azure, della rete e delle soluzioni dei partner connesse, come soluzioni di protezione endpoint e firewall, per rilevare le minacce reali e ridurre i falsi positivi. Il Centro sicurezza visualizza un elenco degli avvisi di sicurezza in ordine di priorità, nonché le informazioni necessarie per analizzare rapidamente il problema e indicazioni per risolvere un attacco.

> [AZURE.NOTE] Per altre informazioni sulle funzionalità di rilevamento del Centro sicurezza, vedere [Funzionalità di rilevamento del Centro sicurezza di Azure](security-center-detection-capabilities.md).

I ricercatori Microsoft sulla sicurezza sono costantemente impegnati nell'analisi delle minacce emergenti in tutto il mondo, inclusi nuovi modelli di attacco e tendenze osservati nei prodotti consumer ed enterprise, oltre che nei servizi online. Il Centro sicurezza può quindi aggiornare gli algoritmi di rilevamento man mano che vengono individuati nuovi attacchi e vulnerabilità, per consentire ai clienti a essere aggiornati sulle minacce in costante evoluzione. Ecco alcuni esempi di tipi di minacce che il Centro sicurezza può rilevare:

- **Attacchi di forza bruta sui dati di rete**: usa modelli di apprendimento automatico che riconoscono i modelli di traffico di rete tipici per le applicazioni e consente un rilevamento più efficace dei tentativi di accesso eseguiti da utenti malintenzionati invece che da utenti autorizzati.
- **Attacchi di forza bruta sui dati degli endpoint**: in base alle analisi dei log dei computer consente di distinguere i tentativi riusciti da quelli non riusciti.
- **Macchine virtuali che comunicano con indirizzi IP dannosi**: confronta il traffico di rete con le informazioni di Microsoft sulle minacce globali e individua i computer compromessi che comunicano con i server di comando e controllo (C&C) e viceversa.
- **Macchine virtuali compromesse**: in base all'analisi del comportamento dei log dei computer e in correlazione con altri segnali identifica gli eventi anomali, probabilmente risultanti da compromissione e sfruttamento delle vulnerabilità dei computer.

## Gestire gli avvisi di sicurezza

È possibile esaminare gli avvisi correnti visualizzando il riquadro **Avvisi di sicurezza**. Aprire il portale di Azure e seguire questa procedura per visualizzare altri dettagli su ogni avviso:

1. Nel dashboard del Centro sicurezza è disponibile il riquadro **Avvisi di sicurezza**.

    ![Riquadro Avvisi di sicurezza nel Centro sicurezza di Azure](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig1-ga.png)

2.  Fare clic sul riquadro per aprire il pannello **Avvisi di sicurezza** contenente altri dettagli sugli avvisi, come illustrato di seguito.

    ![Pannello Avvisi di sicurezza nel Centro sicurezza](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig2-ga.png)

Nella parte inferiore del pannello sono riportati i dettagli relativi ad ogni avviso. Per ordinarli, fare clic sulla colonna in base alle quale si vuole ordinare. Di seguito è riportata una definizione per ogni colonna:

- **Avviso**: breve spiegazione dell'avviso.
- **Conteggio**: elenco di tutti gli avvisi di questo tipo rilevati in un giorno specifico.
- **Rilevato da**: servizio responsabile dell'attivazione dell'avviso.
- **Data**: data in cui si è verificato l'evento.
- **Stato**: stato corrente dell'avviso. Esistono due tipi di stato:
    - **Attivo**: l'avviso di sicurezza è stato rilevato.
    - **Ignorato**: l'avviso di sicurezza è stato ignorato dall'utente. Questo stato viene usato generalmente per gli avvisi che, dopo essere stati esaminati, non sono stati valutati come un attacco effettivo oppure di cui ne sono stati attenuati gli effetti.

- **Gravità**: livello di gravità, che può essere alto, medio o basso.

### Filtro degli avvisi

È possibile filtrare gli avvisi in base a data, stato e gravità. Il filtro degli avvisi può risultare utile per scenari in cui è necessario limitare l'ambito degli avvisi di sicurezza da visualizzare. Ad esempio, potrebbe essere necessario gestire gli avvisi di sicurezza che si sono verificati nelle ultime 24 ore, perché si sta esaminando una potenziale violazione del sistema.

1. Fare clic su **Filtro** nel pannello **Avvisi di sicurezza**. Verrà visualizzato il pannello **Filtro** in cui è possibile selezionare i valori di data, stato e gravità da visualizzare.

	![Filtro degli avvisi nel Centro sicurezza](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig3-ga.png)

2. 	Dopo avere analizzato un avviso di sicurezza, si potrebbe scoprire che si tratta di un falso positivo per il proprio ambiente o di un comportamento previsto per una determinata risorsa. In ogni caso, se si ritiene che un avviso di sicurezza non sia applicabile, è possibile ignorarlo ed escluderlo dalla visualizzazione. Esistono due modi per ignorare un avviso di sicurezza. Fare clic con il pulsante destro del mouse su un avviso e scegliere **Ignora** oppure passare il puntatore del mouse su un elemento, fare clic sui tre punti a destra e selezionare **Ignora**. È possibile visualizzare gli avvisi di sicurezza ignorati facendo clic su **Filtro** e selezionando **Ignorato**.

	![Ignorare gli avvisi nel Centro sicurezza](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig4-ga.png)

### Rispondere agli avvisi di sicurezza

Selezionare un avviso di sicurezza per altre informazioni sugli eventi che hanno attivato l'avviso e, se presenti, i passaggi da eseguire per correggere un attacco. Gli avvisi di sicurezza sono raggruppati per tipo e data. Se si fa clic su un avviso di sicurezza, viene aperto un pannello contenente un elenco degli avvisi raggruppati.

![Rispondere agli avvisi di sicurezza nel Centro sicurezza di Azure](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig5-ga.png)

In questo caso, gli avvisi attivati fanno riferimento a un'attività RDP (Remote Desktop Protocol) sospetta. La prima colonna indica le risorse che sono state attaccate, la seconda quante volte la risorsa è stata attaccata, la terza l'ora dell'attacco, la quarta lo stato dell'avviso e la quinta il livello di gravità dell'attacco. Dopo aver esaminato queste informazioni, fare clic sulla risorsa che ha subito attacchi. Verrà visualizzato un nuovo pannello.

![Suggerimenti sulle operazioni da eseguire in presenza di avvisi di sicurezza nel Centro sicurezza di Azure](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig6-ga.png)

Nel campo **Descrizione** di questo pannello sono disponibili altri dettagli sull'evento. Tali dettagli aggiuntivi forniscono informazioni sull'azione che ha attivato l'avviso di sicurezza, la risorsa di destinazione, l'indirizzo IP di origine quando applicabile e raccomandazioni su come risolvere. In alcuni casi, l'indirizzo IP di origine sarà vuoto (non disponibile), perché non tutti i registri eventi di sicurezza di Windows includono l'indirizzo IP.

> [AZURE.NOTE] Le correzioni suggerite dal Centro sicurezza variano in base all'avviso di sicurezza. In alcuni casi, può essere necessario usare altre funzionalità di Azure per implementare la correzione consigliata. La correzione consigliata per questo tipo di attacco, ad esempio, consiste nell'aggiungere alla blacklist l'indirizzo IP che genera l'attacco usando un [ACL di rete](../virtual-network/virtual-networks-acl.md) o una regola del [gruppo di sicurezza di rete](../virtual-network/virtual-networks-nsg.md).

## Avvisi di sicurezza per tipo
La stessa procedura adottata per accedere all'avviso di attività RDP sospetta può essere usata per accedere ad altri tipi di avvisi. Di seguito sono illustrati alcuni altri esempi di avvisi che possono essere visualizzati tra gli avvisi del Centro sicurezza.

### Potenziale attacco SQL injection
In un attacco SQL injection, il malware viene inserito in stringhe che vengono successivamente passate a un'istanza di SQL Server per l'analisi e l'esecuzione. È consigliabile verificare la presenza di vulnerabilità a questo tipo di attacco in qualsiasi procedura che crea istruzioni SQL, perché SQL Server eseguirà tutte le query sintatticamente valide che riceve.

![Avviso per SQL injection](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig9.png)

L'avviso contiene informazioni che consentono di identificare la risorsa che ha subito attacchi, la data e l'ora di rilevamento e lo stato dell'attacco, nonché un collegamento ad altri passaggi di analisi.

### Rilevamento di traffico in uscita sospetto

I dispositivi di rete possono essere individuati e profilati così come altri tipi di sistemi. L'attacco ha in genere inizio con la scansione o la scansione sistematica delle porte. Nell'esempio di seguito è presente un traffico SSH sospetto da una VM che può eseguire un attacco di scansione sistematica delle porte o di forza bruta SSH contro una risorsa esterna.

![Avviso per traffico in uscita sospetto](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig-10.png)

L'avviso contiene informazioni che consentono di identificare la risorsa usata per avviare l'attacco, il computer compromesso, la data e l'ora di rilevamento, il protocollo e la porta usata. Il pannello contiene anche un elenco dei passaggi di correzione che possono essere usati per attenuare il problema.

### Comunicazione di rete con un computer dannoso
 
Sfruttando i feed di intelligence per le minacce di Microsoft, il Centro sicurezza di Azure può rilevare i computer compromessi che comunicano con indirizzi IP dannosi, corrispondenti in molti casi a un centro di comando e controllo. In questo caso, il Centro sicurezza di Azure ha rilevato la comunicazione con malware Pony Loader, noto anche come [Fareit](https://www.microsoft.com/security/portal/threat/encyclopedia/entry.aspx?Name=PWS:Win32/Fareit.AF).

![Avviso per comunicazione di rete con un computer dannoso](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig9-ga.png)

L'avviso contiene informazioni che consentono di identificare la risorsa usata per avviare l'attacco, la risorsa che ha subito attacchi, l'IP vittima, l'IP dell'utente malintenzionato e la data e l'ora di rilevamento.

> [AZURE.NOTE] Gli indirizzi IP attivi sono stati rimossi dallo screenshot per motivi di privacy.


## Vedere anche

In questo documento si è appreso come configurare i criteri di sicurezza nel Centro sicurezza. Per altre informazioni sul Centro sicurezza, vedere gli argomenti seguenti:

- [Guida alla pianificazione e alla gestione del Centro sicurezza di Azure](security-center-planning-and-operations-guide.md)
- [Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure](security-center-managing-and-responding-alerts.md)
- [Monitoraggio dell'integrità della sicurezza nel Centro sicurezza di Azure](security-center-monitoring.md): informazioni su come monitorare l'integrità delle risorse di Azure.
- [Monitoraggio delle soluzioni dei partner con il Centro sicurezza di Azure](security-center-partner-solutions.md): informazioni su come monitorare lo stato di integrità delle soluzioni dei partner.
- [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md): domande frequenti sull'uso del servizio.
- [Blog sulla sicurezza di Azure](http://blogs.msdn.com/b/azuresecurity/): post di blog sulla sicurezza e sulla conformità in Azure.

<!---HONumber=AcomDC_0727_2016-->