<properties
   pageTitle="Introduzione al Centro sicurezza di Azure | Microsoft Azure"
   description="Questo documento consente di configurare i criteri di sicurezza nel Centro sicurezza di Azure."
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

#Impostazione dei criteri di sicurezza nel Centro sicurezza di Azure
Questo documento consente di configurare i criteri di sicurezza nel Centro protezione Azure mostrando i passaggi necessari per eseguire questa attività.

> [AZURE.NOTE]Le informazioni contenute in questo documento si applicano alla versione di anteprima del Centro sicurezza di Azure.

## Che cos'è il Centro sicurezza di Azure?
Il Centro sicurezza PC di Azure impedisce, rileva e risponde alle minacce mediante visibilità e controllo avanzati della sicurezza delle risorse di Azure. Offre funzionalità integrate di monitoraggio della sicurezza e gestione dei criteri tra le sottoscrizioni, facilita il rilevamento delle minacce che altrimenti passerebbero inosservate e funziona con un ampio ecosistema di soluzioni di sicurezza.

##Informazioni sui criteri di sicurezza
I criteri di sicurezza definiscono il set di controlli consigliato per le risorse all'interno della sottoscrizione specificata. Nel Centro sicurezza di Azure, è possibile definire i criteri per le sottoscrizioni di Azure in base alle esigenze di sicurezza della società e al tipo di applicazione o al livello di riservatezza dei dati in ciascuna sottoscrizione.
 
Ad esempio, le risorse utilizzate per lo sviluppo o il test possono avere requisiti di sicurezza diversi da quelli delle applicazioni di produzione. In modo analogo, le applicazioni con dati regolamentati come le informazioni personali possono richiedere un maggiore livello di sicurezza. I criteri di protezione abilitati nel Centro sicurezza di Azure determineranno i suggerimenti per la sicurezza e il monitoraggio per identificare le potenziali vulnerabilità e attenuare le minacce.

##Impostare i criteri di sicurezza

I criteri di sicurezza vengono configurati per ciascuna sottoscrizione. Per modificare i criteri di sicurezza, è necessario essere proprietario o collaboratore di tale sottoscrizione. Attenersi ai passaggi seguenti per configurare i criteri di sicurezza nel Centro sicurezza di Azure:

1. Fare clic sul riquadro **Criteri di sicurezza** nel dashboard del Centro sicurezza di Azure.
 
2. Nel pannello **Criteri di sicurezza - Definisci i criteri per ogni sottoscrizione** visualizzato sul lato destro, selezionare la sottoscrizione in cui abilitare i criteri di sicurezza.

    ![Abilitazione della raccolta dati](./media/security-center-policies/security-center-policies-fig0.png)

3. Si apre il pannello **Criteri di sicurezza** per la sottoscrizione con un insieme di opzioni simile a quello indicato di seguito:

    ![Abilitazione della raccolta dati](./media/security-center-policies/security-center-policies-fig1.png)

    
4. Verificare che l'opzione **Raccogli i dati dalle macchine virtuali** sia **attiva**. Questa opzione abilita la raccolta automatica dei log per le risorse nuove ed esistenti.

5. Se l'account di archiviazione non è ancora configurato, è possibile notare un avviso simile a quello illustrato nella figura seguente quando si apre **Criteri di sicurezza**:

    ![Selezione dell'archiviazione](./media/security-center-policies/security-center-policies-fig2.png)

6. Se viene visualizzato questo avviso, fare clic su questa opzione e selezionare l'area, come illustrato nella figura seguente:

    ![Selezione dell'archiviazione](./media/security-center-policies/security-center-policies-fig3.png)

7. Per ciascuna area in cui si dispone di macchine virtuali in esecuzione, selezionare l'account di archiviazione in cui vengono archiviati i dati raccolti da tali macchine virtuali. Ciò semplifica la conservazione dei dati nella stessa area geografica per scopi di sovranità dei dati e di privacy. Una volta stabilita l'area da utilizzare, selezionarla e quindi selezionare l'account di archiviazione.

8. Nel pannello **Seleziona account di archiviazione** fare clic su **OK**.

    > [AZURE.NOTE]Se si preferisce, è possibile aggregare dati da macchine virtuali in diverse aree in un account di archiviazione centrale. Consultare le [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md) per ulteriori informazioni

9. Nel pannello **Criteri di sicurezza** fare clic su **Attivo** per abilitare i suggerimenti per la sicurezza da utilizzare per la sottoscrizione. Utilizzare la tabella seguente come riferimento per comprendere il funzionamento di ciascuna opzione:

    | Criterio | Stato Attivo |
|----- |-----|
| System Updates | Recupera un elenco di aggiornamenti disponibili da Windows Update o WSUS, a seconda di quale servizio è configurato per tale macchina virtuale, ogni 12 ore e suggerisce gli aggiornamenti mancanti da installare nelle macchine virtuali di Windows. |
| Regole delle baseline | Analizza tutte le macchine virtuali supportate ogni 12 ore per identificare le configurazioni del sistema operativo che potrebbero rendere la macchina virtuale più vulnerabile agli attacchi e suggerisce le modifiche di configurazione per risolvere tali problemi. Vedere l'[elenco delle line di base consigliate](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335) per ulteriori informazioni sulle configurazioni specifiche monitorate. |
| Antimalware | Suggerisce l'antimalware di cui eseguire il provisioning per tutte le macchine virtuali di Windows, per identificare e rimuovere virus, spyware e altro software dannoso. |
| Elenco di controllo di accesso sugli endpoint | Suggerisce la configurazione di un [elenco di controlli di accesso](virtual-machines-set-up-endpoints.md) (ACL) per limitare l'accesso agli endpoint di una macchina virtuale classica. Di solito, viene utilizzato per garantire che solo gli utenti connessi alla rete aziendale possano accedere alle macchine virtuali. |
| Gruppi di sicurezza di rete nelle subnet e nell'interfaccia di rete | Suggerisce la configurazione di [gruppi di sicurezza di rete](virtual-networks-nsg.md) (NSG) per controllare il traffico in ingresso e in uscita rispetto alle subnet e alle interfacce di rete per le macchine virtuali di Gestione risorse. Gli NSG configurati per una subnet verranno ereditati da tutte le interfacce di rete della macchina virtuale se non diversamente specificato. Oltre a controllare che sia stato configurato un NSG, le regole di sicurezza in ingresso vengono valutate per identificare le regole che consentono qualsiasi tipo di traffico in ingresso. |
| Web Application Firewall | Suggerisce il provisioning di un Web Application Firewall nelle macchine virtuali di Gestione risorse quando: viene utilizzato l'indirizzo [IP pubblico a livello di istanza](virtual-networks-instance-level-public-ip.md) (ILPIP) e vengono configurate le regole di sicurezza in ingresso degli NSG associati per consentire l'accesso alla porta 80/443. Viene utilizzato l'IP con carico bilanciato (indirizzo VIP) e vengono configurati il bilanciamento del carico e le regole NAT in entrata associati per consentire l'accesso alla porta 80/443 (per ulteriori informazioni, vedere [Supporto di Gestione risorse di Azure per il servizio di bilanciamento del carico](load-balancer-arm.md)) |
| Controllo SQL | Suggerisce l'abilitazione del controllo dell'accesso ai database e ai server SQL di Azure per scopi di conformità e rilevamento e analisi avanzati. |
| Transparent Data Encryption di SQL | Suggerisce l'abilitazione della crittografia dati inattivi per i database SQL di Azure, i backup associati e file di log delle transazioni, affinché i dati siano illeggibili in caso di violazione. |

10. Dopo aver completato la configurazione di tutte le opzioni, fare clic su **Salva** per confermare le modifiche.

> [AZURE.NOTE]Abilitando la raccolta dati viene installato l'agente di monitoraggio. Se non si desidera abilitare subito la raccolta di dati da questo percorso, è possibile farlo successivamente nelle visualizzazioni relative allo stato e ai suggerimenti. È inoltre possibile abilitarla per la sottoscrizione o solo per una VM selezionata. Consultare le [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md) per maggiori informazioni sulle macchine virtuali supportate.

## Passaggi successivi
In questo documento è stato descritto come configurare i criteri di sicurezza nel Centro sicurezza di Azure. Per ulteriori informazioni sul Centro sicurezza di Azure, vedere gli argomenti seguenti:

- [Monitoraggio dello stato della sicurezza nel Centro sicurezza di Azure](security-center-monitoring.md): informazioni su come monitorare l'integrità delle risorse di Azure
- [Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure](security-center-managing-and-responding-alerts.md): informazioni su come gestire e rispondere agli avvisi di sicurezza
- [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md): domande frequenti sull'uso del servizio
- [Blog sulla sicurezza di Azure](http://blogs.msdn.com/b/azuresecurity/): post di blog sulla sicurezza e sulla conformità di Azure

<!---HONumber=AcomDC_1217_2015-->