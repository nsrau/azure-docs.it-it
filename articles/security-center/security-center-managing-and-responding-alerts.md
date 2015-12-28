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
   ms.date="12/16/2015"
   ms.author="yurid"/>

# Come gestire e rispondere agli avvisi di sicurezza nel Centro sicurezza di Azure
Questo documento illustra come usare le funzionalità del Centro sicurezza di Azure per gestire e rispondere agli avvisi di sicurezza.

> [AZURE.NOTE]Le informazioni contenute in questo documento si applicano alla versione di anteprima del Centro sicurezza di Azure.

## Che cos'è il Centro sicurezza di Azure?
Il Centro sicurezza PC di Azure impedisce, rileva e risponde alle minacce mediante visibilità e controllo avanzati della sicurezza delle risorse di Azure. Offre funzionalità integrate di monitoraggio della sicurezza e gestione dei criteri tra le sottoscrizioni, facilita il rilevamento delle minacce che altrimenti passerebbero inosservate e funziona con un ampio ecosistema di soluzioni di sicurezza.

## Informazioni sugli avvisi di sicurezza
Il Centro sicurezza di Azure raccoglie, analizza e integra automaticamente i dati di log dalle risorse di Azure, dalla rete e dalle soluzioni dei partner, ad esempio antimalware e firewall, per rilevare le minacce reali e ridurre i falsi positivi. Un elenco degli avvisi con priorità viene esposto come Avvisi di sicurezza.

È possibile esaminare gli avvisi correnti esaminando il riquadro Avvisi di sicurezza. Per visualizzare altri dettagli su ogni avviso, seguire questa procedura:

1. Nel dashboard del **Centro sicurezza di Azure** è visualizzato il riquadro **Avvisi di sicurezza**. 

    ![Abilitazione della raccolta dei dati](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig1.png)

2.  Fare clic sull'occorrenza dell'avviso di sicurezza nel grafico per visualizzare il pannello **Avvisi di sicurezza** con altri dettagli su questo avviso, come illustrato di seguito:
    
    ![Abilitazione della raccolta dei dati](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig2.png)

Nella parte inferiore del pannello sono disponibili i dettagli per ogni avviso. È possibile ordinare in base alle colonne facendo clic sulla colonna desiderata. Di seguito è riportata la definizione per ogni colonna:

- **Avviso**: breve spiegazione sull'avviso.
- **Conteggio**: rappresenta il numero di tutti gli avvisi di questo tipo specifico che sono stati rilevati in un giorno specifico.
- **Rilevato da**: servizio responsabile dell'attivazione dell'avviso.
- **Data**: data in cui si è verificato l'evento.
- **Stato**: mostra lo stato corrente per l'avviso. Esistono tre tipi di stato:
    - **Attivo**: l'avviso di sicurezza è stato rilevato.
    - **Chiuso**: l'avviso di sicurezza è stato chiuso dall'utente. Questo stato viene in genere usato per gli avvisi che sono stati esaminati e non sono stati valutati come un attacco effettivo oppure avvisi esaminati e quindi risolti

- **Gravità**: mostra il livello di gravità, che può essere alta, media o bassa.
  

### Rispondere agli avvisi di sicurezza
Numerose attività possono indicare un potenziale attacco all'organizzazione. Ad esempio, l'attività di un amministratore di rete che esegue un'acquisizione di rete legittima può sembrare simile a quella di un utente che avvia una qualche forma di attacco. In altri casi, un sistema configurato in modo non corretto può generare numerosi falsi positivi nell'ambito di un sistema di rilevamento delle intrusioni, rendendo così più difficile l'individuazione di attacchi reali. Dopo avere esaminato gli avvisi di sicurezza nel Centro sicurezza di Azure, è possibile iniziare a intraprendere le azioni appropriate in base alla gravità dell'avviso.

Per eseguire un'azione, selezionare l'avviso a cui si vuole rispondere. Sulla destra verrà visualizzato un nuovo pannello con altri dettagli, come illustrato di seguito:

![Abilitazione della raccolta dei dati](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig3.png)

In questo caso, gli avvisi attivati fanno riferimento a un'attività RDP (Remote Desktop Protocol) sospetta. La prima colonna indica le risorse che sono state attaccate, seguita dall'ora in cui è stato rilevato l'attacco, dallo stato dell'avviso e dal livello di gravità. Dopo aver esaminato queste informazioni, fare clic sulla risorsa che è stata attaccata per visualizzare un nuovo pannello con altri suggerimenti relativi alle operazioni successive, come illustrato nell'esempio seguente:

![Abilitazione della raccolta dei dati](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig4.png)
  
> [AZURE.NOTE]Le correzioni suggerite dal Centro sicurezza di Azure variano in base l'avviso di sicurezza e in alcuni casi, potrebbe essere necessario usare altre funzionalità di Azure per implementare la correzione consigliata. Ad esempio, la correzione per questo tipo di attacco indica di aggiungere alla blacklist l'indirizzo IP che genera questo tipo di attacco usando un [ACL di rete](virtual-networks-acl.md) o una regola del [gruppo di sicurezza di rete](virtual-networks-nsg.md).

### Gestire gli avvisi di sicurezza
È possibile filtrare gli avvisi in base a data, stato e gravità. Nel pannello Avvisi di sicurezza fare clic su Filtro e abilitare le opzioni desiderate come illustrato di seguito:

![Abilitazione della raccolta dei dati](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig5.png)

Il filtro degli avvisi può risultare utile per scenari in cui è necessario limitare l'ambito degli elementi da visualizzare nel dashboard, ad esempio, per controllare gli avvisi di sicurezza che si sono verificati nelle ultime 24 ore perché si sta esaminando una potenziale violazione del sistema. Anche se la maggior parte degli avvisi di sicurezza includerà i suggerimenti da applicare, in alcuni casi potrebbe essere necessario ignorare un avviso perché per l'ambiente corrente è un falso positivo o perché si tratta di un comportamento previsto per una determinata risorsa. Indipendentemente dal caso specifico, è possibile nascondere i suggerimenti per una particolare risorsa usando l'opzione **Chiudi**.

Per chiudere un'attività, fare clic sulla risorsa per visualizzare le informazioni aggiuntive o applicare la configurazione consigliata. Fare clic sull'attività per chiuderla. Verrà visualizzata l'opzione **Chiudi**, come illustrato nell'immagine seguente:

![Abilitazione della raccolta dei dati](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig6.png)

In una raccolta di avvisi per ogni avviso saranno visualizzate una correzione e una descrizione molto specifiche. Gli avvisi visualizzati nel Centro sicurezza di Azure sono basati sullo scenario di attacco. Gli scenari di attacco seguenti sono attivati dal motore Microsoft:

- **Rilevamento attacco di forza bruta sui dati di rete**: questi rilevamenti sono basati su modelli di Machine Learning che apprendono dai dati del traffico di rete. 
- **Rilevamento attacco di forza bruta sui dati degli endpoint**: questi rilevamenti sono basati sulle query eseguite dal Centro sicurezza di Azure sui log dei computer per consentire la differenziazione tra i tentativi riusciti e non riusciti. 
- **Macchine virtuali che comunicano con indirizzi IP dannosi**: questi rilevamenti sono basati sull'individuazione eseguita dal Centro di sicurezza di Azure di computer compromessi con bot che comunicano con i rispettivi server di comando e di controllo (C&C) e viceversa. 

## Passaggi successivi
In questo documento è stato descritto come configurare i criteri di sicurezza nel Centro sicurezza di Azure. Per ulteriori informazioni sul Centro sicurezza di Azure, vedere gli argomenti seguenti:

- [Monitoraggio dello stato della sicurezza nel Centro sicurezza di Azure](security-center-monitoring.md): informazioni su come monitorare l'integrità delle risorse di Azure
- [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md): domande frequenti sull'uso del servizio
- [Blog sulla sicurezza di Azure](http://blogs.msdn.com/b/azuresecurity/): post di blog sulla sicurezza e sulla conformità di Azure

<!---HONumber=AcomDC_1217_2015-->