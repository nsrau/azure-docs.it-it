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
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/02/2016"
   ms.author="yurid"/>

# Come gestire e rispondere agli avvisi di sicurezza nel Centro sicurezza di Azure
Questo documento illustra come usare le funzionalità del Centro sicurezza di Azure per gestire e rispondere agli avvisi di sicurezza.

> [AZURE.NOTE] Le informazioni contenute in questo documento si applicano alla versione di anteprima del Centro sicurezza di Azure.

## Informazioni sul Centro sicurezza PC di Azure
 Il Centro sicurezza impedisce, rileva e risponde alle minacce mediante visibilità e controllo avanzati della sicurezza delle risorse di Azure. Offre funzionalità integrate di monitoraggio della sicurezza e gestione dei criteri tra le sottoscrizioni, facilita il rilevamento delle minacce che altrimenti passerebbero inosservate e funziona con un ampio ecosistema di soluzioni di sicurezza.

## Informazioni sugli avvisi di sicurezza
 Il Centro sicurezza raccoglie, analizza e integra automaticamente i dati di log dalle risorse di Azure, dalla rete e dalle soluzioni dei partner, ad esempio programmi antimalware e firewall, per rilevare le minacce reali e ridurre i falsi positivi. Un elenco degli avvisi con priorità viene esposto sotto forma di avvisi di sicurezza.

È possibile esaminare gli avvisi correnti osservando il riquadro **Avvisi di sicurezza**. Per visualizzare altri dettagli su ogni avviso, seguire questa procedura:

1. Nel dashboard del Centro sicurezza è disponibile il riquadro **Avvisi di sicurezza**.

    ![Riquadro Avvisi di sicurezza nel Centro sicurezza di Azure](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig1.png)

2.  Fare clic sull'icona delle occorrenze degli avvisi di sicurezza. Verrà visualizzato il pannello **Avvisi di sicurezza** contenente altri dettagli sugli avvisi, come illustrato di seguito.

    ![Pannello Avvisi di sicurezza nel Centro sicurezza di Azure](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig2.png)

Nella parte inferiore del pannello sono riportati i dettagli relativi ad ogni avviso. Per ordinarli, fare clic sulla colonna in base alle quale si vuole ordinare. Di seguito è riportata una definizione per ogni colonna:

- **Avviso**: breve spiegazione dell'avviso.
- **Conteggio**: elenco di tutti gli avvisi di questo tipo rilevati in un giorno specifico.
- **Rilevato da**: servizio responsabile dell'attivazione dell'avviso.
- **Data**: data in cui si è verificato l'evento.
- **Stato**: stato corrente dell'avviso. Esistono tre tipi di stato:
    - **Attivo**: l'avviso di sicurezza è stato rilevato.
    - **Eliminato**: l'avviso di sicurezza è stato eliminato dall'utente. Questo stato viene usato generalmente per gli avvisi che, dopo essere stati esaminati, non sono stati valutati come un attacco effettivo oppure di cui ne sono stati attenuati gli effetti.

- **Gravità**: livello di gravità, che può essere alto, medio o basso.

### Rispondere agli avvisi di sicurezza
Molti tipi di attività possono indicare un potenziale attacco all'organizzazione. Ad esempio, l'attività di un amministratore di rete che esegue un'acquisizione di rete legittima può sembrare simile a quella di un utente che avvia una qualche forma di attacco. In altri casi, un sistema configurato in modo non corretto può generare numerosi falsi positivi nell'ambito di un sistema di rilevamento delle intrusioni, rendendo così più difficile l'individuazione di attacchi reali. Dopo aver esaminato gli avvisi di sicurezza mediante il Centro sicurezza, è possibile iniziare a intraprendere le azioni appropriate in base alla gravità dell'avviso.

Per eseguire un'azione, selezionare l'avviso a cui si vuole rispondere. Sulla destra verrà visualizzato un nuovo pannello con altri dettagli, come illustrato di seguito:

![Rispondere agli avvisi di sicurezza nel Centro sicurezza di Azure](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig3.png)

In questo caso, gli avvisi attivati fanno riferimento a un'attività RDP (Remote Desktop Protocol) sospetta. La prima colonna indica le risorse che sono state attaccate, la seconda l'ora in cui è stato rilevato l'attacco, la terza lo stato dell'avviso e la quarta il livello di gravità dell'attacco. Dopo aver esaminato queste informazioni, fare clic sulla risorsa attaccata. Verrà visualizzato un nuovo pannello con altri suggerimenti relativi alle operazioni successive, come illustrato nell'esempio seguente.

![Suggerimenti sulle operazioni da eseguire in presenza di avvisi di sicurezza nel Centro sicurezza di Azure](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig4.png)

> [AZURE.NOTE] Le correzioni suggerite dal Centro sicurezza variano in base all'avviso di sicurezza. In alcuni casi, può essere necessario usare altre funzionalità di Azure per implementare la correzione consigliata. Ad esempio, la correzione consigliata per questo tipo di attacco indica di aggiungere alla blacklist l'indirizzo IP che genera l'attacco usando un [ACL di rete](virtual-networks-acl.md) o una regola del [gruppo di sicurezza di rete](virtual-networks-nsg.md).

### Gestire gli avvisi di sicurezza
È possibile filtrare gli avvisi in base a data, stato e gravità. Nel pannello **Avvisi di sicurezza** fare clic su **Filtro** e abilitare le opzioni desiderate, come illustrato di seguito:

![Filtrare gli avvisi nel Centro sicurezza di Azure](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig5.png)

Il filtro degli avvisi può risultare utile per scenari in cui è necessario limitare l'ambito degli elementi da visualizzare nel dashboard, ad esempio per controllare gli avvisi di sicurezza che si sono verificati nelle ultime 24 ore mentre si sta esaminando una potenziale violazione del sistema.

Nella maggior parte dei casi, è consigliabile applicare le raccomandazioni incluse negli avvisi di sicurezza. In alcune circostanze, tuttavia, può essere necessario ignorare un avviso perché per l'ambiente corrente è un falso positivo o perché si tratta di un comportamento previsto per una determinata risorsa. Indipendentemente dal caso specifico, è possibile nascondere i suggerimenti per una particolare risorsa usando l'opzione **Chiudi**.

Per eliminare un'attività, fare clic su di essa con il pulsante destro del mouse. Verrà visualizzata l'opzione **Elimina**, come illustrato nell'immagine seguente:

![Opzione Elimina nel Centro sicurezza di Azure](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig6.png)

In una raccolta di avvisi, per ogni avviso sono disponibili una descrizione e una correzione specifiche. Gli avvisi visualizzati nel Centro sicurezza sono basati su uno scenario di attacco. Gli avvisi relativi agli scenari di attacco seguenti sono attivati dal motore Microsoft:

- **Rilevamento attacco di forza bruta su dati di rete**: basato su modelli di apprendimento automatico che apprendono dai dati del traffico di rete.
- **Rilevamento attacco di forza bruta su dati di endpoint**: basato sulle query eseguite dal Centro sicurezza sui log dei computer; consente la differenziazione tra i tentativi riusciti e quelli non riusciti.
- **Macchine virtuali che comunicano con indirizzi IP dannosi**: basato sull'individuazione eseguita dal Centro sicurezza di computer compromessi con bot che comunicano con i rispettivi server di comando e di controllo (C&C) e viceversa.

## Passaggi successivi
In questo documento si è appreso come configurare i criteri di sicurezza nel Centro sicurezza. Per altre informazioni sul Centro sicurezza, vedere gli argomenti seguenti:

- [Monitoraggio dello stato della sicurezza nel Centro sicurezza di Azure](security-center-monitoring.md): informazioni su come monitorare l'integrità delle risorse di Azure.
- [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md): domande frequenti sull'uso del servizio.
- [Blog sulla sicurezza di Azure](http://blogs.msdn.com/b/azuresecurity/): post di blog sulla sicurezza e sulla conformità di Azure.

<!---HONumber=AcomDC_0204_2016-->