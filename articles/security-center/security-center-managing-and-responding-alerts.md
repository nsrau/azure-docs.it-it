<properties
   pageTitle="Come gestire e rispondere agli avvisi di sicurezza nel Centro sicurezza di Azure | Microsoft Azure"
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
   ms.date="02/11/2016"
   ms.author="yurid"/>
 
# Come gestire e rispondere agli avvisi di sicurezza nel Centro sicurezza di Azure
Questo documento illustra come usare le funzionalità del Centro sicurezza di Azure per gestire e rispondere agli avvisi di sicurezza.

> [AZURE.NOTE] Le informazioni contenute in questo documento si applicano alla versione di anteprima del Centro sicurezza di Azure.

## Informazioni sul Centro sicurezza PC di Azure
 Il Centro sicurezza impedisce, rileva e risponde alle minacce mediante visibilità e controllo avanzati della sicurezza delle risorse di Azure. Offre funzionalità integrate di monitoraggio della sicurezza e gestione dei criteri tra le sottoscrizioni, facilita il rilevamento delle minacce che altrimenti passerebbero inosservate e funziona con un ampio ecosistema di soluzioni di sicurezza.

## Informazioni sugli avvisi di sicurezza
Il Centro sicurezza raccoglie, analizza e integra automaticamente i dati di log dalle risorse di Azure, dalla rete e dalle soluzioni antimalware e firewall, per rilevare le minacce reali e ridurre i falsi positivi. Il Centro sicurezza visualizza un elenco degli avvisi di sicurezza con priorità, inclusi gli avvisi relativi a soluzioni dei partner integrate, nonché le informazioni che è necessario esaminare rapidamente e indicazioni su come risolvere un attacco.
 
I ricercatori Microsoft sulla sicurezza sono costantemente impegnati nell'analisi delle minacce emergenti in tutto il mondo, inclusi nuovi modelli di attacco e tendenze osservati nei prodotti consumer ed enterprise, oltre che nei servizi online. Il Centro sicurezza può quindi aggiornare gli algoritmi di rilevamento man mano che vengono individuati nuovi attacchi e vulnerabilità, per consentire ai clienti a essere aggiornati sulle minacce in costante evoluzione. Ecco alcuni esempi di tipi di minacce che il Centro sicurezza può rilevare:

- **Attacchi di forza bruta sui dati di rete**: usa modelli di apprendimento automatico che riconoscono i modelli di traffico di rete tipici per le applicazioni e consente un rilevamento più efficace dei tentativi di accesso eseguiti da utenti malintenzionati invece che da utenti autorizzati.
- **Attacchi di forza bruta sui dati degli endpoint**: in base alle analisi dei log dei computer consente di distinguere i tentativi riusciti da quelli non riusciti.
- **Macchine virtuali che comunicano con indirizzi IP dannosi**: confronta il traffico di rete con le informazioni di Microsoft sulle minacce globali e individua i computer compromessi che comunicano con i server di comando e controllo (C&C) e viceversa.
- **Macchine virtuali compromesse**: in base all'analisi del comportamento dei log dei computer e in correlazione con altri segnali identifica gli eventi anomali, probabilmente risultanti da compromissione e sfruttamento delle vulnerabilità dei computer.

## Gestire gli avvisi di sicurezza 

È possibile esaminare gli avvisi correnti visualizzando il riquadro **Avvisi di sicurezza**. Per visualizzare altri dettagli su ogni avviso, seguire questa procedura:

1. Nel dashboard del Centro sicurezza è disponibile il riquadro **Avvisi di sicurezza**.

    ![Riquadro Avvisi di sicurezza nel Centro sicurezza di Azure](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig1-new.png)

2.  Fare clic sul riquadro per aprire il pannello **Avvisi di sicurezza** contenente altri dettagli sugli avvisi, come illustrato di seguito.

    ![Pannello Avvisi di sicurezza nel Centro sicurezza di Azure](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig2-new.png)

Nella parte inferiore del pannello sono riportati i dettagli relativi ad ogni avviso. Per ordinarli, fare clic sulla colonna in base alle quale si vuole ordinare. Di seguito è riportata una definizione per ogni colonna:

- **Avviso**: breve spiegazione dell'avviso.
- **Conteggio**: elenco di tutti gli avvisi di questo tipo rilevati in un giorno specifico.
- **Rilevato da**: servizio responsabile dell'attivazione dell'avviso.
- **Data**: data in cui si è verificato l'evento.
- **Stato**: stato corrente dell'avviso. Esistono tre tipi di stato:
    - **Attivo**: l'avviso di sicurezza è stato rilevato.
    - **Ignorato**: l'avviso di sicurezza è stato ignorato dall'utente. Questo stato viene usato generalmente per gli avvisi che, dopo essere stati esaminati, non sono stati valutati come un attacco effettivo oppure di cui ne sono stati attenuati gli effetti.

- **Gravità**: livello di gravità, che può essere alto, medio o basso.

È possibile filtrare gli avvisi in base a data, stato e gravità. Il filtro degli avvisi può risultare utile per scenari in cui è necessario limitare l'ambito degli avvisi di sicurezza da visualizzare. Ad esempio, potrebbe essere necessario gestire gli avvisi di sicurezza che si sono verificati nelle ultime 24 ore, perché si sta esaminando una potenziale violazione del sistema.

1. Fare clic su **Filtro** nel pannello **Avvisi di sicurezza**. Viene visualizzato il pannello **Filtro** in cui è possibile selezionare i valori relativi a data, stato e gravità da visualizzare.

	![Filtrare gli avvisi nel Centro sicurezza di Azure](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig5-new.png)

2. 	Dopo avere analizzato un avviso di sicurezza, si potrebbe scoprire che si tratta di un falso positivo per il proprio ambiente o di un comportamento previsto per una determinata risorsa. In ogni caso, se si ritiene che un avviso di sicurezza non sia applicabile, è possibile ignorarlo ed escluderlo dalla visualizzazione. Esistono due modi per ignorare un avviso di sicurezza. Fare clic con il pulsante destro del mouse su un avviso e scegliere **Ignora** oppure passare il puntatore del mouse su un elemento, fare clic sui tre punti a destra e selezionare **Ignora**. È possibile visualizzare gli avvisi di sicurezza ignorati facendo clic su **Filtro** e scegliendo **Ignorato**.

	![Filtrare gli avvisi nel Centro sicurezza di Azure](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig6-new.png)

### Rispondere agli avvisi di sicurezza
Selezionare un avviso di sicurezza per altre informazioni sugli eventi che hanno attivato l'avviso e, se presenti, i passaggi da eseguire per correggere un attacco. Gli avvisi di sicurezza sono raggruppati per tipo e data. Se si fa clic su un avviso di sicurezza, viene aperto un pannello contenente un elenco degli avvisi raggruppati.

![Rispondere agli avvisi di sicurezza nel Centro sicurezza di Azure](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig7.png)

In questo caso, gli avvisi attivati fanno riferimento a un'attività RDP (Remote Desktop Protocol) sospetta. La prima colonna indica le risorse che sono state attaccate, la seconda l'ora in cui è stato rilevato l'attacco, la terza lo stato dell'avviso e la quarta il livello di gravità dell'attacco. Dopo aver esaminato queste informazioni, fare clic sulla risorsa attaccata. Verrà visualizzato un nuovo pannello con altri suggerimenti relativi alle operazioni successive, come illustrato nell'esempio seguente.

![Suggerimenti sulle operazioni da eseguire in presenza di avvisi di sicurezza nel Centro sicurezza di Azure](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig8.png)

> [AZURE.NOTE] Le correzioni suggerite dal Centro sicurezza variano in base all'avviso di sicurezza. In alcuni casi, può essere necessario usare altre funzionalità di Azure per implementare la correzione consigliata. Ad esempio, la correzione consigliata per questo tipo di attacco consiste nell'aggiungere alla blacklist l'indirizzo IP che genera l'attacco usando un [ACL di rete](../virtual-network/virtual-networks-acl.md) o una regola del [gruppo di sicurezza di rete](../virtual-network/virtual-networks-nsg.md).


## Passaggi successivi
In questo documento si è appreso come configurare i criteri di sicurezza nel Centro sicurezza. Per altre informazioni sul Centro sicurezza, vedere gli argomenti seguenti:

- [Monitoraggio dello stato della sicurezza nel Centro sicurezza di Azure](security-center-monitoring.md): informazioni su come monitorare l'integrità delle risorse di Azure.
- [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md): domande frequenti sull'uso del servizio.
- [Blog sulla sicurezza di Azure](http://blogs.msdn.com/b/azuresecurity/): post di blog sulla sicurezza e sulla conformità di Azure.

<!---HONumber=AcomDC_0218_2016-->