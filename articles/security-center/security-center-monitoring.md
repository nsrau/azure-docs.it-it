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
   ms.date="12/01/2015"
   ms.author="yurid"/>

#Monitoraggio dello stato di sicurezza nel Centro sicurezza di Azure
Informazioni su come utilizzare le funzionalità di monitoraggio nel Centro sicurezza di Azure mediante questa procedura dettagliata.

> [AZURE.NOTE]Le informazioni contenute in questo documento si applicano alla versione di anteprima del Centro sicurezza di Azure.

##Che cos'è il monitoraggio dello stato di sicurezza?
Spesso si pensa al monitoraggio come l'osservazione e l'attesa di un evento al fine di reagire alla situazione verificatasi. Per monitoraggio della sicurezza si intende una strategia proattiva per il controllo delle risorse, al fine di identificare i sistemi non conformi agli standard o alle procedure consigliate della società.

##Monitoraggio della conformità ai criteri
Dopo l'abilitazione dei [criteri di sicurezza](security-center-policies.md) per le risorse della sottoscrizione, il Centro sicurezza di Azure analizzerà la sicurezza delle risorse per identificare le potenziali vulnerabilità. Mentre le informazioni sulla configurazione di rete sono disponibili immediatamente, le informazioni sulla configurazione della macchina virtuale, come ad esempio lo stato dell'aggiornamento di sicurezza e la configurazione del sistema operativo, potrebbero essere disponibili dopo un'ora o più. È possibile visualizzare lo stato di sicurezza delle risorse, insieme a eventuali problemi presenti nei pannelli relativi allo stato di sicurezza delle risorse. È possibile anche visualizzare un elenco di tali problemi nei pannelli relativi ai suggerimenti.

Per ulteriori informazioni su come applicare i suggerimenti leggere [Implementazione dei suggerimenti per la sicurezza nel Centro sicurezza di Azure](security-center-recommendations.md).

##Integrità delle risorse
Il riquadro **Integrità delle risorse** consente di monitorare lo stato di sicurezza delle risorse. Verrà visualizzata una serie di problemi con livelli di gravità medio e alto che richiedono attenzione, come illustrato di seguito:

![Integrità delle risorse](./media/security-center-monitoring/security-center-monitoring-fig1.png)

I criteri di sicurezza abilitati interessano i tipi di controlli monitorati qui. Se il Centro di sicurezza di Azure identifica una vulnerabilità che deve essere risolta, ad esempio una VM con aggiornamenti di sicurezza mancanti o una subnet senza [gruppo di sicurezza di rete](virtual-networks-nsg.md), questa verrà identificata qui.

###Macchine virtuali
Quando fa clic su **Macchine virtuali** nel riquadro **Integrità delle risorse**, si aprirà il pannello **Macchine virtuali** con ulteriori dettagli sulle operazioni di caricamento e prevenzione, nonché un elenco di tutte le VM monitorate dal Centro sicurezza di Azure, come illustrato di seguito:

![Aggiornamento di sistema mancante per VM](./media/security-center-monitoring/security-center-monitoring-fig2.png)

Dopo aver aperto il pannello, verranno visualizzate tre sezioni. Per ciascuna sezione è possibile selezionare una singola opzione per visualizzare ulteriori dettagli riguardanti il passaggio consigliato per risolvere tale problema.

Ad esempio, se si fa clic su **Aggiornamenti di sistema mancanti** in **Procedura di prevenzione**, si aprirà il pannello **Aggiornamenti di sistema mancanti** con l'elenco delle VM in cui mancano le patch e il livello di gravità per ciascuna di esse come illustrato di seguito:

![Prevenzione degli aggiornamenti di sistema mancanti](./media/security-center-monitoring/security-center-monitoring-fig3.png)

Per visualizzare i dettagli degli aggiornamenti consigliati, fare clic sul nome della VM. Si apre un nuovo pannello per tale VM con l'elenco degli aggiornamenti mancanti:

![Aggiornamento di sistema consigliato per VM](./media/security-center-monitoring/security-center-monitoring-fig4.png)

> [AZURE.NOTE]I suggerimenti per la sicurezza contenuti qui sono identici a quelli nel pannello relativo ai suggerimenti. Vedere l'articolo [Implementazione dei suggerimenti per la sicurezza nel Centro sicurezza di Azure](security-center-recommendations.md) per ulteriori informazioni sui suggerimenti per la risoluzione dei problemi. Questo vale non solo per le VM, ma anche per tutte le risorse disponibili nel riquadro Integrità delle risorse.

###Rete
Lo stato di prevenzione per le rete mostra le reti virtuali monitorate dal Centro sicurezza di Azure. Quando si fa clic su **Rete** nel riquadro Integrità delle risorse, si aprirà il pannello **Rete** con ulteriori dettagli, come illustrato di seguito:

![Aggiornamento di sistema consigliato per VM](./media/security-center-monitoring/security-center-monitoring-fig5.png)

Analogamente alle informazioni sull'integrità delle risorse delle macchine virtuali, il pannello fornisce un elenco riepilogativo dei problemi nella parte superiore e un elenco delle reti monitorate nella parte inferiore.

Nella sezione dei dettagli sullo stato della rete, sono elencate le procedure di prevenzione, ad esempio [ACL sugli endpoint](virtual-machines-set-up-endpoints.md) non abilitata, [Gruppi di sicurezza di rete](virtual-networks-nsg.md) non abilitata e le subnet integre. È possibile fare clic su una di queste e visualizzare maggiori dettagli, nonché eseguire ulteriori azioni per risolvere il problema.

Un esempio è l'avviso **NSG su subnet non abilitati**. Se si fa clic su questo avviso, si apre il pannello **Configurazione gruppi di sicurezza di rete mancanti per le subnet**. Verrà visualizzata una descrizione di una subnet in cui il gruppo di sicurezza di rete non è abilitato e l'elenco di schede di interfaccia di rete che appartengono a tale subnet. Selezionare la subnet in cui applicare il gruppo di sicurezza e fare clic sull'opzione **Configura NSG**. Si aprirà il pannello del gruppo **Seleziona sicurezza di rete** come illustrato di seguito:

![Aggiornamento di sistema consigliato per VM](./media/security-center-monitoring/security-center-monitoring-fig6.png)

###SQL
Quando si fa clic **SQL** nel riquadro **Integrità delle risorse**, si aprirà il pannello **SQL** con suggerimenti relativi a problemi quali la mancata abilitazione del controllo, la mancata abilitazione di Transparent Data Encryption e lo stato di integrità generale del database. La parte inferiore del pannello contiene la suddivisione di SQL Server, anche per ciascun server di database di appartenenza, come illustrato di seguito:

![Aggiornamento di sistema consigliato per VM](./media/security-center-monitoring/security-center-monitoring-fig7.png)

È possibile fare clic su uno di questi suggerimenti e visualizzare maggiori dettagli, nonché eseguire ulteriori azioni per risolvere il problema. Nell'esempio illustrato di seguito è presente l'espansione del controllo abilitato nei server SQL.

![Aggiornamento di sistema consigliato per VM](./media/security-center-monitoring/security-center-monitoring-fig8.png)

###Applicazioni
Se il carico di lavoro di Azure dispone di applicazioni che si trovano in [VM di Gestione risorse](resource-manager-deployment-model.md) con porte Web esposte (porte TCP 80 e 443), il Centro sicurezza di Azure è in grado di monitorarle per identificare potenziali problemi di sicurezza, con suggerimenti per le procedure correttive. Quando si fa clic sul riquadro **Applicazioni**, si apre il pannello **Applicazioni** con una serie di suggerimenti nella sezione relativa alla procedura di prevenzione. Inoltre, viene visualizzata la suddivisione dell'applicazione per host/IP virtuale come illustrato di seguito:

![Aggiornamento di sistema consigliato per VM](./media/security-center-monitoring/security-center-monitoring-fig9.png)

Proprio come per qualsiasi altro suggerimento relativo alle risorse precedenti, è possibile fare clic su di esso per visualizzare ulteriori dettagli sul problema e su come risolverlo. L'esempio illustrato nella figura seguente è un'applicazione identificata come **Applicazione Web non protetta**. Quando si seleziona l'applicazione considerata non protetta, si aprirà un altro pannello con l'opzione disponibile, in questo caso per abilitare il Web application firewall.

![Aggiornamento di sistema consigliato per VM](./media/security-center-monitoring/security-center-monitoring-fig10.png)

## Passaggi successivi
In questo documento è stato descritto come utilizzare le funzionalità di monitoraggio nel Centro sicurezza di Azure. Per ulteriori informazioni sul Centro sicurezza di Azure, vedere gli argomenti seguenti:

- [Impostazione dei criteri di sicurezza nel Centro sicurezza di Azure](security-center-policies.md): informazioni su come configurare le impostazioni di sicurezza nel Centro sicurezza di Azure
- [Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure](security-center-managing-and-responding-alerts.md): informazioni su come gestire e rispondere agli avvisi di sicurezza
- [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md): domande frequenti sull'utilizzo del servizio
- [Blog sulla sicurezza di Azure](http://blogs.msdn.com/b/azuresecurity/): post di blog sulla sicurezza e sulla conformità di Azure

<!---HONumber=AcomDC_1203_2015-->