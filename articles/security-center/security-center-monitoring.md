<properties
   pageTitle="Monitoraggio dello stato di sicurezza nel Centro sicurezza di Azure | Microsoft Azure"
   description="Questo documento consente di iniziare a utilizzare le funzionalità di monitoraggio nel Centro sicurezza di Azure."
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
   ms.date="01/22/2016"
   ms.author="yurid"/>

#Monitoraggio dello stato di sicurezza nel Centro sicurezza di Azure
Questo documento consente di utilizzare le funzionalità di monitoraggio nel Centro sicurezza di Azure per monitorare la conformità ai criteri.

> [AZURE.NOTE] Le informazioni contenute in questo documento si applicano alla versione di anteprima del Centro sicurezza di Azure.

## Informazioni sul Centro sicurezza PC di Azure
Il Centro sicurezza di Azure impedisce, rileva e risponde alle minacce mediante visibilità e controllo avanzati della sicurezza delle risorse di Azure. Offre funzionalità integrate di monitoraggio della sicurezza e gestione dei criteri tra le sottoscrizioni, facilita il rilevamento delle minacce che altrimenti passerebbero inosservate e funziona con un ampio ecosistema di soluzioni di sicurezza

##Che cos'è il monitoraggio dello stato di sicurezza?
Spesso si pensa al monitoraggio come l'osservazione e l'attesa di un evento al fine di reagire alla situazione verificatasi. Per monitoraggio della sicurezza si intende una strategia proattiva per il controllo delle risorse, al fine di identificare i sistemi non conformi agli standard o alle procedure consigliate della società.

##Monitoraggio dello stato di sicurezza
Dopo l'abilitazione dei [criteri di sicurezza](security-center-policies.md) per le risorse della sottoscrizione, il Centro sicurezza di Azure analizzerà la sicurezza delle risorse per identificare le potenziali vulnerabilità. Mentre le informazioni sulla configurazione di rete sono disponibili immediatamente, le informazioni sulla configurazione della macchina virtuale, come ad esempio lo stato dell'aggiornamento di sicurezza e la configurazione del sistema operativo, potrebbero essere disponibili dopo un'ora o più. Nel pannello **Integrità sicurezza delle risorse** è possibile visualizzare lo stato di sicurezza delle risorse ed eventuali problemi rilevati, mentre nel pannello **Raccomandazioni** è disponibile l'elenco dei problemi riscontrati.

Per ulteriori informazioni su come applicare i suggerimenti leggere [Implementazione dei suggerimenti per la sicurezza nel Centro sicurezza di Azure](security-center-recommendations.md).

Il riquadro **Integrità delle risorse** consente di monitorare lo stato di sicurezza delle risorse. Verrà visualizzata una serie di problemi con livelli di gravità medio e alto che richiedono attenzione, come illustrato di seguito:

![Integrità delle risorse](./media/security-center-monitoring/security-center-monitoring-fig1-new.png)

I criteri di sicurezza abilitati interessano i tipi di controlli monitorati qui. Se il Centro di sicurezza di Azure identifica una vulnerabilità che deve essere risolta, ad esempio una macchina virtuale con aggiornamenti di sicurezza mancanti o una subnet senza [gruppo di sicurezza di rete](virtual-networks-nsg.md), questa verrà identificata qui.

###Macchine virtuali
Quando fa clic su **Macchine virtuali** nel riquadro **Integrità delle risorse**, si aprirà il pannello **Macchine virtuali** con ulteriori dettagli sulle operazioni di caricamento e prevenzione, nonché un elenco di tutte le VM monitorate dal Centro sicurezza di Azure, come illustrato di seguito:

![Aggiornamento di sistema mancante per VM](./media/security-center-monitoring/security-center-monitoring-fig2-new.png)

Dopo aver aperto il pannello, verranno visualizzate tre sezioni:

- Operazioni di caricamento
- Raccomandazioni per le macchine virtuali
- Macchine virtuali

Per ciascuna sezione è possibile selezionare una singola opzione per visualizzare ulteriori dettagli riguardanti il passaggio consigliato per risolvere tale problema. Di seguito viene fornita una descrizione dettagliata delle singole sezioni.

####Operazioni di caricamento
Questa sezione mostra la quantità totale di VM inizializzate per la raccolta dei dati, con il relativo stato corrente. Dopo essere stata inizializzata per la raccolta dei dati, ogni VM può ricevere i criteri di sicurezza dal Centro sicurezza di Azure. Quando si fa clic su questa voce, viene visualizzato il pannello **Inizializzazione della raccolta dati in corso** in cui è possibile consultare il nome delle VM e lo stato corrente della raccolta dei dati, come riportato nella colonna **Stato dell'installazione** illustrata di seguito:

![Stato inizializzazione](./media/security-center-monitoring/security-center-monitoring-fig3-new.png)


####Raccomandazioni per le macchine virtuali
Questa sezione include una serie di raccomandazioni per ogni VM monitorata dal Centro sicurezza di Azure. La prima colonna riporta una descrizione della raccomandazione, la seconda la quantità totale di VM interessate dalla raccomandazione e la terza il livello di gravità, come illustrato di seguito:

![Raccomandazioni per le VM](./media/security-center-monitoring/security-center-monitoring-fig4-new.png)

Ogni raccomandazione è inoltre associata a una serie di azioni che è possibile eseguire facendo clic su di essa. Se, ad esempio, si fa clic su **Aggiornamenti di sistema mancanti**, si aprirà il pannello **Aggiornamenti di sistema mancanti** con l'elenco delle VM ancora senza patch e il livello di gravità per ciascuna di esse, come illustrato di seguito:

![Aggiornamenti di sistema mancanti](./media/security-center-monitoring/security-center-monitoring-fig5-new.png)

Il pannello relativo agli aggiornamenti di sistema mancanti riporta una tabella con le informazioni seguenti:

- **MACCHINA VIRTUALE**: nome della macchina virtuale su cui non sono stati eseguiti gli aggiornamenti.
- **AGGIORNAMENTI DI SISTEMA**: numero di aggiornamenti di sistema mancanti.
- **ORA ULTIMA ANALISI**: ora dell'ultima volta in cui il Centro sicurezza di Azure ha verificato la disponibilità di aggiornamenti per le VM.
- **STATO**: stato attuale della raccomandazione: 
	- **Aperta**: non ancora presa in esame
	- **In corso**: la raccomandazione è stata applicata alle risorse specificate e non è richiesta alcuna azione da parte dell'utente
	- **Risolta**: la raccomandazione è già stata completata (quando lo stato è impostato su Risolta, la riga è di colore grigio)
- **GRAVITÀ**: descrive il livello di gravità della raccomandazione: 
	- **Elevata**: esiste una vulnerabilità associata a una risorsa significativa (applicazione, macchina virtuale, gruppo di sicurezza di rete), che richiede attenzione
	- **Media**: sono necessari passaggi aggiuntivi (o non critici) per completare un processo o eliminare una vulnerabilità
	- **Bassa**: una vulnerabilità che è opportuno risolvere ma non richiede attenzione immediata (Per impostazione predefinita, le raccomandazioni con gravità bassa non appaiono, ma è possibile visualizzarle applicando il filtro corrispondente).

Per visualizzare i dettagli degli aggiornamenti consigliati, fare clic sul nome della VM. Viene visualizzato un nuovo pannello con l'elenco degli aggiornamenti non ancora eseguiti sulla VM, come illustrato di seguito:

![Aggiornamenti di sistema mancanti per le VM](./media/security-center-monitoring/security-center-monitoring-fig6-new.png)

> [AZURE.NOTE] I suggerimenti per la sicurezza contenuti qui sono identici a quelli nel pannello relativo ai suggerimenti. Vedere l'articolo [Implementazione dei suggerimenti per la sicurezza nel Centro sicurezza di Azure](security-center-recommendations.md) per ulteriori informazioni sui suggerimenti per la risoluzione dei problemi. Questo vale non solo per le VM, ma anche per tutte le risorse disponibili nel riquadro Integrità delle risorse.

####Macchine virtuali
La sezione relativa alle macchine virtuali offre una panoramica di tutte le VM con le relative raccomandazioni. Ogni colonna di raccomandazione rappresenta in realtà una serie di raccomandazioni, come illustrato di seguito:

![VM](./media/security-center-monitoring/security-center-monitoring-fig7-new.png)

L'icona visualizzata sotto ogni tipo di raccomandazione consente di identificare rapidamente le VM interessate dal tipo di raccomandazione corrispondente.

Nell'esempio precedente, per una VM è disponibile una raccomandazione critica relativa al software antimalware. Facendo clic sulla VM, si aprirà un nuovo pannello con informazioni più dettagliate sulla VM, come illustrato di seguito:

![Dettagli di sicurezza delle VM](./media/security-center-monitoring/security-center-monitoring-fig8-new.png)

Nel pannello sono presenti anche dettagli di sicurezza relativi alla VM. Nella parte inferiore, inoltre, è possibile visualizzare l'azione consigliata e il livello di gravità di ogni raccomandazione.

###Rete
Lo stato di prevenzione per le rete mostra le reti virtuali monitorate dal Centro sicurezza di Azure. Quando si fa clic su **Rete** nel riquadro Integrità delle risorse, si aprirà il pannello **Rete** con ulteriori dettagli, come illustrato di seguito:

![Rete](./media/security-center-monitoring/security-center-monitoring-fig9-new.png)

Dopo aver aperto il pannello sarà possibile individuare due sezioni: Raccomandazioni di rete e Rete.
 
Per ciascuna sezione è possibile selezionare una singola opzione per visualizzare ulteriori dettagli riguardanti il passaggio consigliato per risolvere tale problema. Di seguito viene fornita una descrizione dettagliata delle singole sezioni.

####Raccomandazioni di rete

Analogamente alle informazioni sull'integrità delle risorse delle macchine virtuali, il pannello fornisce un elenco riepilogativo dei problemi nella parte superiore e un elenco delle reti monitorate nella parte inferiore.

Nella sezione dei dettagli sullo stato della rete sono elencate alcune procedure di prevenzione, ad esempio [elenchi di controllo di accesso sugli endpoint](virtual-machines-set-up-endpoints.md) non abilitati, [gruppi di sicurezza di rete](virtual-networks-nsg.md) non abilitati, subnet integre e **accesso al gruppo di sicurezza di rete non limitato**. Facendo clic su una delle raccomandazioni si aprirà un pannello con maggiori informazioni su di essa, come illustrato nell'esempio seguente:

![Endpoint](./media/security-center-monitoring/security-center-monitoring-fig10-new.png)

In questo esempio, il pannello **Limitare l'accesso tramite un endpoint esterno pubblico** include l'elenco dei gruppi di sicurezza di rete interessati dall'avviso, la subnet e la rete a cui è associato il gruppo, lo stato corrente della raccomandazione e il livello di gravità. Facendo clic su un gruppo di sicurezza di rete si aprirà un altro pannello, come illustrato di seguito:

![Limita endpoint](./media/security-center-monitoring/security-center-monitoring-fig11-new.png)

Nel pannello sono disponibili informazioni sul gruppo di sicurezza di rete, la posizione del gruppo e l'elenco di regole in ingresso attualmente abilitate. Nella parte inferiore del pannello, invece, è riportata la VM associata al gruppo di sicurezza di rete. Se si vogliono abilitare le regole in ingresso per bloccare una porta indesiderata attualmente aperta o modificare l'origine della regola in ingresso corrente, fare clic sul pulsante **Modifica regola in ingresso** nella parte superiore del pannello.

####Rete
Nella sezione **Rete** è disponibile una visualizzazione gerarchica del gruppo di risorse, della subnet e dell'interfaccia di rete associate alla VM, come illustrato di seguito:

![Struttura di rete](./media/security-center-monitoring/security-center-monitoring-fig121-new.png)

Questa sezione divide le [VM basate su Gestione risorse da quelle classiche](resource-manager-deployment-model.md), in modo da poter identificare più facilmente se nella macchina virtuale sono disponibili le funzionalità di rete di Gestione dei servizi di Azure (ASM/Classica) o di Gestione risorse di Azure (ARM). Per accedere alle proprietà di una scheda di rete da questa posizione, è necessario espandere la subnet e fare clic sul nome della VM. Se si esegue questa operazione per una VM basata su Gestione risorse, si aprirà un nuovo pannello simile a quello illustrato di seguito:

![Struttura di rete](./media/security-center-monitoring/security-center-monitoring-fig13-new.png)

Nel pannello sono riepilogati i dettagli della scheda di rete, con le relative raccomandazioni. Se la VM selezionata è di tipo classico, si aprirà un pannello con opzioni diverse, come illustrato di seguito:

![Elenco di controllo di accesso](./media/security-center-monitoring/security-center-monitoring-fig14-new.png)

Questo pannello consente di apportare modifiche alla porta pubblica e a quella privata e di creare un elenco di controllo di accesso per la VM.

###SQL
Se si fa clic su **SQL** nel riquadro **Integrità delle risorse**, si aprirà il pannello SQL con raccomandazioni relative a problemi come la mancata abilitazione del servizio di controllo, la mancata abilitazione di Transparent Data Encryption e lo stato di integrità generale del database.

![Integrità delle risorse SQL](./media/security-center-monitoring/security-center-monitoring-fig15-new.png)

È possibile fare clic su uno di questi suggerimenti e visualizzare maggiori dettagli, nonché eseguire ulteriori azioni per risolvere il problema. L'esempio seguente illustra l'espansione della raccomandazione **Servizio di controllo del database non abilitato**.

![Integrità delle risorse SQL](./media/security-center-monitoring/security-center-monitoring-fig16-new.png)

Nel pannello **Abilitare il servizio di controllo sui database SQL** è riportato un elenco di database SQL e il server in cui si trovano, viene indicato se questa impostazione è stata ereditata dal server o se è univoca del database ed è illustrato lo stato corrente del problema con il livello di gravità. Facendo clic sul database per gestire la raccomandazione, si aprirà il pannello **Controllo e rilevamento minacce**, come illustrato di seguito:

![Integrità delle risorse SQL](./media/security-center-monitoring/security-center-monitoring-fig17-new.png)

Per abilitare questa funzionalità, selezionare **ON** sotto l'opzione **Controllo** e fare clic su **Salva**.

###Applicazioni
Se il carico di lavoro di Azure dispone di applicazioni che si trovano in [VM di Gestione risorse](resource-manager-deployment-model.md) con porte Web esposte (porte TCP 80 e 443), il Centro sicurezza di Azure è in grado di monitorarle per identificare potenziali problemi di sicurezza, con suggerimenti per le procedure correttive. Quando si fa clic sul riquadro **Applicazioni**, si apre il pannello **Applicazioni** con una serie di suggerimenti nella sezione relativa alla procedura di prevenzione. Inoltre, viene visualizzata la suddivisione dell'applicazione per host/IP virtuale come illustrato di seguito:

![Integrità sicurezza delle applicazioni](./media/security-center-monitoring/security-center-monitoring-fig18-new.png)

Proprio come per qualsiasi altro suggerimento relativo alle risorse precedenti, è possibile fare clic su di esso per visualizzare ulteriori dettagli sul problema e su come risolverlo. L'esempio illustrato nella figura seguente è un'applicazione identificata come **Applicazione Web non protetta**. Se si seleziona l'applicazione considerata non sicura, si aprirà un altro pannello con l'opzione seguente disponibile:

![App](./media/security-center-monitoring/security-center-monitoring-fig19-new.png)

Nel pannello **Applicazioni Web non sicure** è disponibile un elenco di tutte le VM contenenti applicazioni non considerate sicure. L'elenco riporta il nome della VM, lo stato corrente del problema e il livello di gravità. Facendo clic su questa applicazione Web, si aprirà il pannello **Aggiungere un Web application firewall** da cui è possibile installare un WAF (Web application firewall) di terze parti, come illustrato di seguito:

![Aggiungi WAF](./media/security-center-monitoring/security-center-monitoring-fig20-new.png)

## Passaggi successivi
In questo documento è stato descritto come utilizzare le funzionalità di monitoraggio nel Centro sicurezza di Azure. Per ulteriori informazioni sul Centro sicurezza di Azure, vedere gli argomenti seguenti:

- [Impostazione dei criteri di sicurezza nel Centro sicurezza di Azure](security-center-policies.md): informazioni su come configurare le impostazioni di sicurezza nel Centro sicurezza di Azure
- [Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure](security-center-managing-and-responding-alerts.md): informazioni su come gestire e rispondere agli avvisi di sicurezza
- [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md): domande frequenti sull'utilizzo del servizio
- [Blog sulla sicurezza di Azure](http://blogs.msdn.com/b/azuresecurity/): post di blog sulla sicurezza e sulla conformità di Azure

<!---HONumber=AcomDC_0128_2016-->