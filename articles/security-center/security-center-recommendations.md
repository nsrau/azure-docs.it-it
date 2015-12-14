<properties
   pageTitle="Implementazione delle raccomandazioni di sicurezza nel Centro sicurezza di Azure | Microsoft Azure"
   description="Questo documento dimostra come le raccomandazioni presenti nel Centro sicurezza di Azure facilitino la protezione delle risorse di Azure e garantiscano la conformità ai criteri di sicurezza."
   services="security-center"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/02/2015"
   ms.author="terrylan"/>

# Implementazione delle raccomandazioni di sicurezza nel Centro sicurezza di Azure

Questo documento dimostra come le raccomandazioni presenti nel Centro sicurezza di Azure facilitino la protezione delle risorse di Azure.

> [AZURE.NOTE]Le informazioni contenute in questo documento si applicano alla versione di anteprima del Centro sicurezza di Azure. Questa è un'introduzione al servizio con un esempio di distribuzione, non una guida dettagliata.

## Informazioni sul Centro sicurezza di Azure
Il Centro sicurezza di Azure impedisce, rileva e risponde alle minacce mediante visibilità e controllo maggiori sulla sicurezza delle risorse di Azure. Offre funzionalità integrate di monitoraggio della sicurezza e gestione dei criteri tra le sottoscrizioni, facilita il rilevamento delle minacce che altrimenti passerebbero inosservate e funziona con un ampio ecosistema di soluzioni di sicurezza.

##Che cos'è una raccomandazione di sicurezza?
Il Centro sicurezza di Azure analizza periodicamente lo stato di sicurezza delle risorse di Azure. Quando identifica potenziali vulnerabilità di sicurezza, crea delle raccomandazioni, che illustrano in dettaglio la procedura di configurazione dei controlli necessari.

## Configurazione delle raccomandazioni

In [Impostazione dei criteri di sicurezza nel Centro sicurezza di Azure](security-center-policies.md) si impara a:

- Configurare i criteri di sicurezza
- Attivare la raccolta dei dati
- Scegliere le raccomandazioni da visualizzare nell'ambito dei criteri di sicurezza

Le raccomandazioni relative ai criteri di sicurezza si basano attualmente su aggiornamenti di sistema, regole della linea di base, anti-malware, [elenchi di controllo di accesso per gli endpoint](virtual-machines-set-up-endpoints.md), [gruppi di sicurezza di rete](virtual-networks-nsg.md) su subnet e interfacce di rete, controllo del Database SQL, Transparent Data Encryption per il Database SQL e Web Application Firewall. L'articolo [Impostazione dei criteri di sicurezza](security-center-policies.md) fornisce una descrizione di ogni opzione relativa alle raccomandazioni.

La sezione **Mostra raccomandazioni per** del pannello **Criterio di sicurezza** presenta un elenco di raccomandazioni che è possibile attivare per le risorse incluse nella sottoscrizione specificata.

![][1]

## Monitoraggio delle raccomandazioni

Dopo aver impostato un criterio di sicurezza, il Centro sicurezza di Azure analizza lo stato di sicurezza delle risorse per identificare potenziali vulnerabilità. Il riquadro **Raccomandazioni** del pannello **Centro sicurezza PC** consente di conoscere il numero totale di raccomandazioni identificate dal Centro sicurezza di Azure.

![][2]

Per visualizzare i dettagli di ogni raccomandazione:

1. Fare clic sul riquadro **Raccomandazioni** del pannello **Centro sicurezza PC**. Viene visualizzato il pannello **Raccomandazioni**.
2. È possibile filtrare le raccomandazioni visualizzate in base allo stato e alla gravità. Nel pannello **Raccomandazioni** fare clic su **Filtro**. Viene visualizzato il pannello Filtro in cui è possibile selezionare i valori di gravità e stato desiderati. ![][3]

3. Se si ritiene che una delle raccomandazioni non sia applicabile, è possibile ignorarla ed escluderla dalla visualizzazione. Esistono due modi per ignorare una raccomandazione. Fare clic con il pulsante destro del mouse su un elemento e selezionare **Ignora** oppure passare il puntatore del mouse su un elemento, fare clic sui tre punti che appaiono a destra e selezionare **Ignora**. È possibile visualizzare le raccomandazioni ignorate facendo clic su **Filtro** e selezionando **Ignorato**. ![][4]

Le raccomandazioni vengono visualizzate sotto forma di tabella, in cui ogni riga rappresenta una particolare raccomandazione. Le colonne della tabella sono:

- **DESCRIZIONE**: spiegazione della raccomandazione e descrizione delle azioni da compiere per metterla in pratica
- **RISORSA**: risorse a cui fa riferimento la raccomandazione
- **STATO**: stato attuale della raccomandazione:
  - **Aperta**: non ancora presa in esame
  - **In corso**: la raccomandazione è stata applicata alle risorse specificate e non è richiesta alcuna azione da parte dell'utente
  - **Risolta**: la raccomandazione è già stata completata (quando lo stato è impostato su Risolta, la riga risulta disabilitata)
- **GRAVITÀ**: descrive il livello di gravità della raccomandazione:
  - **Elevata**: esiste una vulnerabilità associata a una risorsa significativa (applicazione, macchina virtuale, gruppo di sicurezza di rete), che richiede attenzione
  - **Media**: sono necessari passaggi aggiuntivi (o non critici) per completare un processo o eliminare una vulnerabilità
  - **Bassa**: una vulnerabilità che è opportuno risolvere ma non richiede attenzione immediata (Per impostazione predefinita, le raccomandazioni con gravità bassa non appaiono, ma è possibile visualizzarle applicando il filtro corrispondente).

Usare la tabella seguente come riferimento per conoscere le raccomandazioni disponibili e gli effetti che producono se si decide di metterle in pratica:

| Raccomandazione | Descrizione |
|----- |-----|
| Abilitare la raccolta dati per le sottoscrizioni/macchine virtuali | Consiglia di attivare l'opzione Raccolta dati in Criteri di sicurezza per le macchine virtuali selezionate |
| Risolvere la mancata corrispondenza alle regole della linea di base | Consiglia di allineare le configurazioni dei sistemi operativi alle linee di base raccomandate, ad esempio non consentire il salvataggio delle password. |
| Applicare gli aggiornamenti di sistema | Consiglia di distribuire nelle macchine virtuali gli aggiornamenti di sistema mancanti (solo macchine virtuali Windows). |
| Configurare gli elenchi di controllo di accesso per gli endpoint | Consiglia di configurare un elenco di controllo di accesso per limitare l'accesso in ingresso solo alle macchine virtuali (solo macchine virtuali classiche). |
| Aggiungere un Web Application Firewall | Consiglia di distribuire un Web Application Firewall (WAF) per gli endpoint Web (solo le macchine virtuali di Gestione risorse). |
| Finalizzare la configurazione del Web Application Firewall | Per completare la configurazione di un Web Application Firewall, il traffico deve essere reindirizzato al dispositivo WAF. Questa raccomandazione consente di completare le necessarie modifiche di configurazione. |
| Abilitare il servizio antimalware | Consiglia di eseguire il provisioning del servizio antimalware nelle macchine virtuali (solo macchine virtuali Windows). |
| Abilitare i gruppi di sicurezza di rete nelle subnet/interfacce di rete | Consiglia di abilitare i gruppi di sicurezza di rete (NSG) su subnet e interfacce di rete (solo le macchine virtuali di Gestione risorse). |
| Limitare l'accesso tramite endpoint esterni pubblici | Consiglia di configurare le regole del traffico in ingresso per i gruppi di sicurezza di rete. |
| Abilitare il servizio di controllo SQL per i server | Consiglia di attivare il controllo per i server SQL di Azure (solo il servizio SQL di Azure, non SQL in esecuzione sulle macchine virtuali). |
| Abilitare il servizio di controllo SQL per i database | Consiglia di attivare il controllo per i database SQL di Azure (solo il servizio SQL di Azure, non SQL in esecuzione sulle macchine virtuali). |
| Abilitare Transparent Data Encryption sui database SQL | Consiglia di abilitare la crittografia per i database SQL (solo il servizio di SQL Azure). |
| Distribuire l'agente di macchine virtuali | Consente di identificare le macchine virtuali per le quali è necessario l'agente di macchine virtuali, che deve essere installato nelle macchine virtuali per poter eseguire il provisioning dei servizi di analisi delle patch, analisi della linea di base e antimalware. Per impostazione predefinita, l'agente di macchine virtuali è installato nelle macchine virtuali distribuite da Azure Marketplace. L'articolo relativo all'[agente di macchine virtuali e relative estensioni, parte 2](http://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/) fornisce informazioni su come installare l'agente di macchine virtuali. |

## Applicazione delle raccomandazioni
Dopo aver esaminato tutte le raccomandazioni, è possibile decidere quale applicare per prima. Si consiglia di usare il livello di gravità come parametro principale per scegliere le raccomandazioni da applicare per prime. Prendendo come esempio la raccomandazione sul servizio antimalware, viene illustrata di seguito la procedura da seguire per applicare le raccomandazioni:

1. Nel pannello **Raccomandazioni** selezionare **Abilitare il servizio antimalware**. ![][5]

2. Nel pannello **Installa antimalware** selezionare una delle macchine virtuali senza antimalware abilitato incluse nell'elenco e fare clic su **Installa antimalware**.
3. Viene visualizzato il pannello **Nuova risorsa**, che consente di selezionare la soluzione antimalware da usare. Selezionare **Microsoft Antimalware**.
4. Vengono visualizzate altre informazioni sulla soluzione antimalware selezionata. Selezionare **Crea**.
5. Specificare le impostazioni di configurazione richieste nel pannello **Aggiungi estensione** e selezionare **OK**. ![][6]

[Microsoft Antimalware](azure-security-antimalware.md) è ora attivo nella macchina virtuale selezionata.

## Distribuire soluzioni integrate di aziende partner

Una raccomandazione potrebbe essere quella di distribuire una soluzione di sicurezza integrata di un partner Microsoft. Di seguito viene illustrata una procedura di esempio:

1. Tornare al pannello **Raccomandazioni**.
2.	Selezionare la raccomandazione **Proteggere l'applicazione Web tramite Web Application Firewall**. Verrà visualizzato il pannello **Applicazioni Web non protette**. ![][7]
3. Selezionare un'applicazione Web: viene visualizzato il pannello **Aggiungere un Web Application Firewall**.
4. Selezionare **Barracuda Web Application Firewall**. Viene visualizzato un pannello contenente informazioni su **Barracuda Web Application Firewall**.
5. Nel pannello informativo fare clic su **Crea**. Viene visualizzato il pannello **Nuovo Web Application Firewall**, in cui è possibile eseguire la procedura di **Configurazione macchina virtuale** e fornire i dati richiesti in **Informazioni WAF**.
6. Selezionare **Configurazione macchina virtuale**. Nel pannello **Configurazione macchina virtuale** immettere le informazioni necessarie per avviare la macchina virtuale che eseguirà il Web Application Firewall. ![][8]
7. Tornare al pannello **Nuovo Web Application Firewall** e selezionare **Informazioni WAF**. Nel pannello **Informazioni WAF** è possibile configurare il Web Application Firewall. Il passaggio 6 consente di configurare la macchina virtuale che eseguirà il firewall, mentre con il passaggio 7 si esegue il provisioning del firewall.

8. Tornare al pannello **Raccomandazioni**. Dopo la creazione del Web Application Firewall è stata aggiunta una nuova voce: **Finalizzare la configurazione del Web Application Firewall**. Questa raccomandazione informa l'utente che è necessario completare il processo di connessione del WAF all'interno della rete virtuale di Azure in modo che possa proteggere l'applicazione. ![][9]

9. Selezionare **Finalizzare la configurazione del Web Application Firewall**. Viene visualizzato un nuovo pannello, in cui è presente un'applicazione Web per la quale è necessario reindirizzare il traffico.
10. Selezionare l'applicazione Web. Viene visualizzato un pannello in cui è possibile eseguire la procedura per finalizzare la configurazione del Web Application Firewall. Completare la procedura e fare clic su **Limita il traffico**. Il Centro sicurezza di Azure eseguirà automaticamente le operazioni di connessione. ![][10]

I log generati dal WAF sono ora completamente integrati. Il Centro sicurezza di Azure è in grado di avviare automaticamente la raccolta e l'analisi dei log, in modo da mettere in evidenza eventuali avvisi di sicurezza importanti.

## Passaggi successivi
Questo documento ha affrontato il tema delle raccomandazioni relative alla sicurezza nel Centro sicurezza di Azure. Per altre informazioni sul Centro sicurezza di Azure, vedere:

- [Impostazione dei criteri di sicurezza nel Centro sicurezza di Azure](security-center-policies.md): come configurare i criteri di sicurezza
- [Monitoraggio dello stato della sicurezza nel Centro sicurezza di Azure](security-center-monitoring.md): informazioni su come monitorare l'integrità delle risorse di Azure
- [Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure](security-center-managing-and-responding-alerts.md): come gestire e rispondere agli avvisi di sicurezza
- [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md): domande frequenti sull'uso del servizio
- [Blog sulla sicurezza di Azure](http://blogs.msdn.com/b/azuresecurity/): post di blog sulla sicurezza e sulla conformità di Azure

<!--Image references-->
[1]: ./media/security-center-recommendations/show-recommendations-for.png
[2]: ./media/security-center-recommendations/recommendations-tile.png
[3]: ./media/security-center-recommendations/filter-recommendations.png
[4]: ./media/security-center-recommendations/dismiss-recommendations.png
[5]: ./media/security-center-recommendations/select-enable-antimalware.png
[6]: ./media/security-center-recommendations/install-antimalware.png
[7]: ./media/security-center-recommendations/secure-web-application.png
[8]: ./media/security-center-recommendations/vm-configuration.png
[9]: ./media/security-center-recommendations/finalize-waf.png
[10]: ./media/security-center-recommendations/restrict-traffic.png

<!---HONumber=AcomDC_1203_2015-->