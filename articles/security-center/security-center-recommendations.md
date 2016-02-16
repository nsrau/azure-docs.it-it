<properties
   pageTitle="Gestione delle raccomandazioni di sicurezza nel Centro sicurezza di Azure | Microsoft Azure"
   description="Questo documento dimostra come le raccomandazioni presenti nel Centro sicurezza di Azure facilitino la protezione delle risorse di Azure e garantiscano la conformità ai criteri di sicurezza."
   services="security-center"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="StevenPo"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/09/2016"
   ms.author="terrylan"/>

# Gestione delle raccomandazioni di sicurezza nel Centro sicurezza di Azure

Questo documento illustra come usare le raccomandazioni presenti nel Centro sicurezza di Azure per la protezione delle risorse di Azure.

> [AZURE.NOTE] Le informazioni contenute in questo documento si applicano alla versione di anteprima del Centro sicurezza di Azure. Il documento introduce il servizio usando una distribuzione di esempio. Questa non è una guida dettagliata.

## Informazioni sul Centro sicurezza PC di Azure
Il Centro sicurezza PC di Azure impedisce, rileva e risponde alle minacce mediante visibilità e controllo avanzati della sicurezza delle risorse di Azure. Offre funzionalità integrate di monitoraggio della sicurezza e gestione dei criteri tra le sottoscrizioni, facilita il rilevamento delle minacce che altrimenti passerebbero inosservate e funziona con un ampio ecosistema di soluzioni di sicurezza.

## Informazioni sulle raccomandazioni di sicurezza
Il Centro sicurezza analizza periodicamente lo stato di sicurezza delle risorse di Azure. Quando identifica potenziali vulnerabilità della sicurezza, crea raccomandazioni. Queste raccomandazioni illustrano in dettaglio il processo di configurazione dei controlli necessari.

## Implementare le raccomandazioni di sicurezza

### Impostare le raccomandazioni

In [Impostazione dei criteri di sicurezza nel Centro sicurezza di Azure](security-center-policies.md) si apprende come:

- Configurare i criteri di sicurezza.
- Attivare la raccolta dei dati.
- Scegliere le raccomandazioni da visualizzare nell'ambito dei criteri di sicurezza.

Le raccomandazioni relative ai criteri di sicurezza si basano attualmente su aggiornamenti di sistema, regole della baseline, programmi antimalware, [ACL per gli endpoint](../virtual-machines/virtual-machines-set-up-endpoints.md), [gruppi di sicurezza di rete](../virtual-networks/virtual-networks-nsg.md) per subnet e interfacce di rete, controllo del database SQL, Transparent Data Encryption per il database SQL e Web application firewall. L'articolo [Impostazione dei criteri di sicurezza](security-center-policies.md) fornisce una descrizione di ogni opzione relativa alle raccomandazioni.

### Monitorare le raccomandazioni
Una volta impostato un criterio di sicurezza, il Centro sicurezza analizza lo stato di sicurezza delle risorse per identificare le potenziali vulnerabilità. Il riquadro **Raccomandazioni** nel pannello **Centro sicurezza** consente di conoscere il numero totale di raccomandazioni identificate dal Centro sicurezza.

![][2]

Per visualizzare i dettagli di ogni raccomandazione:

1. Fare clic sul riquadro **Raccomandazioni** del pannello **Centro sicurezza PC**. Viene visualizzato il pannello **Raccomandazioni**.

Le raccomandazioni vengono visualizzate sotto forma di tabella, in cui ogni riga rappresenta una particolare raccomandazione. Le colonne della tabella sono:

- **DESCRIZIONE**: spiega la raccomandazione e cosa occorre fare per metterla in pratica.
- **RISORSA**: elenca le risorse a cui si applica questa raccomandazione.
- **STATO**: descrive lo stato attuale della raccomandazione:
    - **Aperta**: la raccomandazione non è ancora stata risolta.
    - **In corso**: la raccomandazione è stata applicata alle risorse e non è richiesta alcuna azione da parte dell'utente.
    - **Risolta**: la raccomandazione è già stata completata. In questo caso, la riga sarà visualizzata in grigio.
- **GRAVITÀ**: descrive il livello di gravità della raccomandazione:
    - **Elevata**: esiste una vulnerabilità associata a una risorsa significativa, ad esempio un'applicazione, una macchina virtuale o un gruppo di sicurezza di rete, che richiede attenzione.
    - **Media**: esiste una vulnerabilità e sono necessari passaggi aggiuntivi o non critici per eliminarla o per completare un processo.
    - **Bassa**: esiste una vulnerabilità che è opportuno risolvere, ma che non richiede attenzione immediata. Per impostazione predefinita, le raccomandazioni con gravità bassa non appaiono, ma è possibile visualizzarle applicando il filtro corrispondente.

Usare la tabella seguente come riferimento per conoscere le raccomandazioni disponibili e gli effetti che producono se si decide di metterle in pratica:

|Raccomandazione|Descrizione|
|-----|-----|
|Abilitare la raccolta dati per le sottoscrizioni/macchine virtuali|È consigliabile attivare la raccolta dei dati nei criteri di sicurezza per ogni sottoscrizione o per le VM selezionate.|
|Risolvere la mancata corrispondenza alle regole della linea di base|Consiglia di allineare le configurazioni dei sistemi operativi alle linee di base raccomandate, ad esempio non consentire il salvataggio delle password.|
|Applicare gli aggiornamenti di sistema|Suggerisce di distribuire gli aggiornamenti critici e della sicurezza di sistema mancanti nelle macchine virtuali (solo VM Windows).|
|Configurare gli elenchi di controllo di accesso per gli endpoint|Suggerisce di configurare elenchi di controllo di accesso per limitare l'accesso in ingresso solo alle macchine virtuali (solo VM classiche).|
|Aggiungere un Web Application Firewall|Suggerisce di distribuire un Web application firewall (WAF) per gli endpoint Web (solo VM di Gestione risorse).|
|Finalizzare la configurazione del Web Application Firewall|Per completare la configurazione di un Web Application Firewall, il traffico deve essere reindirizzato al dispositivo WAF. Seguendo questa raccomandazione si completeranno le modifiche di configurazione necessarie.|
|Abilitare il servizio antimalware|Suggerisce di effettuare il provisioning dei programmi antimalware nelle macchine virtuali (solo VM Windows).|
|Abilitare i gruppi di sicurezza di rete nelle subnet/interfacce di rete|Suggerisce di abilitare i gruppi di sicurezza di rete nelle subnet e nelle interfacce di rete (solo VM di Gestione risorse).|
|Limitare l'accesso tramite endpoint esterni pubblici|Consiglia di configurare le regole del traffico in ingresso per i gruppi di sicurezza di rete.|
|Abilitare il servizio di controllo SQL per i server|Suggerisce di attivare il controllo per i server SQL di Azure. Solo il servizio SQL di Azure, non SQL in esecuzione nelle macchine virtuali.|
|Abilitare il servizio di controllo SQL per i database|Suggerisce di attivare il controllo per i database SQL di Azure. Solo il servizio SQL di Azure, non SQL in esecuzione nelle macchine virtuali.|
|Abilitare Transparent Data Encryption sui database SQL|Consiglia di abilitare la crittografia per i database SQL (solo il servizio di SQL Azure).|
|Distribuire l'agente di macchine virtuali|Consente di identificare le macchine virtuali per le quali è necessario l'agente di macchine virtuali, che deve essere installato nelle macchine virtuali per poter effettuare il provisioning dei programmi di analisi delle patch, analisi della baseline e antimalware. Per impostazione predefinita, l'agente di macchine virtuali è installato nelle macchine virtuali distribuite da Azure Marketplace. L'articolo relativo all'[agente di macchine virtuali e relative estensioni, parte 2](http://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/) fornisce informazioni su come installare l'agente di macchine virtuali.|
| [Applicare Crittografia dischi](security-center-apply-disk-encryption.md) |Suggerisce di crittografare i dischi delle macchine virtuali con Crittografia dischi di Azure (VM Windows e Linux). La crittografia è consigliabile sia per il sistema operativo sia per i volumi di dati della macchina virtuale.|

È possibile filtrare ed eventualmente ignorare le raccomandazioni.

1. Nel pannello **Raccomandazioni** fare clic su **Filtro**. Viene visualizzato il pannello **Filtro** in cui è possibile selezionare i valori relativi a gravità e stato da visualizzare.

    ![][3]

2. Se si ritiene che una delle raccomandazioni non sia applicabile, è possibile ignorarla ed escluderla dalla visualizzazione. Esistono due modi per ignorare una raccomandazione. Uno consiste nel fare clic con il pulsante destro del mouse su un elemento e quindi scegliere **Ignora**. L'altro nel passare il puntatore del mouse su un elemento, fare clic sui tre punti che appaiono a destra e selezionare **Ignora**. È possibile visualizzare le raccomandazioni ignorate facendo clic su **Filtro** e scegliendo **Ignorata**.

    ![][4]

### Applicare le raccomandazioni
Dopo aver esaminato tutte le raccomandazioni, decidere quale applicare per prima. È consigliabile usare la classificazione di gravità come parametro principale per valutare quali raccomandazioni applicare per prime. Prendendo come esempio la raccomandazione per **abilitare un antimalware**, viene illustrata di seguito la procedura da seguire per applicarla:

1. Nel pannello **Raccomandazioni** selezionare **Abilitare antimalware**. ![][5]

2. Nel pannello **Installa antimalware** selezionare una delle macchine virtuali senza antimalware abilitato incluse nell'elenco e fare clic su **Installa antimalware**.
3. Viene visualizzato il pannello **Nuova risorsa**, che consente di selezionare la soluzione antimalware da usare. Selezionare **Microsoft Antimalware**.
4. Vengono visualizzate altre informazioni sulla soluzione antimalware selezionata. Selezionare **Create**.
5. Specificare le impostazioni di configurazione necessarie nel pannello **Aggiungi estensione** e selezionare **OK**. ![][6]

[Microsoft Antimalware](../azure-security/azure-security-antimalware.md) è ora attivo nella macchina virtuale selezionata.

### Distribuire soluzioni dei partner raccomandate

Un'altra raccomandazione che può essere visualizzata riguarda la distribuzione di una soluzione di sicurezza integrata di un partner Microsoft. Di seguito è illustrata una procedura di esempio.

1. Tornare al pannello **Raccomandazioni**.
2.	Selezionare la raccomandazione **Proteggere l'applicazione Web tramite Web Application Firewall**. Verrà visualizzato il pannello **Applicazioni Web non protette**. ![][7]
3. Selezionare un'applicazione Web: viene visualizzato il pannello **Aggiungere un Web Application Firewall**.
4. Selezionare **Barracuda Web Application Firewall**. Viene visualizzato un pannello contenente informazioni su **Barracuda Web Application Firewall**.
5. Nel pannello informativo fare clic su **Crea**. Viene visualizzato il pannello **Nuovo Web Application Firewall**, in cui è possibile eseguire la procedura di **Configurazione macchina virtuale** e fornire i dati richiesti in **Informazioni WAF**.
6. Selezionare **Configurazione macchina virtuale**. Nel pannello **Configurazione macchina virtuale** immettere le informazioni necessarie per avviare la macchina virtuale che eseguirà il WAF. ![][8]
7. Tornare al pannello **Nuovo Web Application Firewall** e selezionare **Informazioni WAF**. Nel pannello **Informazioni WAF** è possibile configurare il Web Application Firewall. Il passaggio 6 consente di configurare la macchina virtuale che eseguirà il firewall, mentre con il passaggio 7 si esegue il provisioning del firewall.

8. Tornare al pannello **Raccomandazioni**. Dopo la creazione di WAF viene aggiunta una nuova voce: **Finalizzare la configurazione di Web application firewall**. Questa raccomandazione informa l'utente che è necessario completare il processo di connessione effettiva di WAF all'interno della rete virtuale di Azure in modo da proteggere l'applicazione. ![][9]

9. Selezionare **Finalizzare la configurazione di Web application firewall**. Viene visualizzato un nuovo pannello. Come si può vedere, è presente un'applicazione Web il cui traffico deve essere reindirizzato.
10. Selezionare l'applicazione Web. Viene visualizzato un pannello in cui è possibile eseguire la procedura per finalizzare la configurazione di Web application firewall. Completare i passaggi e quindi fare clic su **Limita il traffico**. Il Centro sicurezza eseguirà quindi automaticamente la connessione. ![][10]

I log generati dal WAF sono ora completamente integrati. Il Centro sicurezza può avviare automaticamente la raccolta e l'analisi dei log, per mettere in evidenza eventuali avvisi di sicurezza importanti.

## Passaggi successivi
Questo documento ha introdotto le raccomandazioni relative alla sicurezza nel Centro sicurezza. Per altre informazioni sul Centro sicurezza, vedere gli argomenti seguenti:

- [Impostazione dei criteri di sicurezza nel Centro sicurezza di Azure](security-center-policies.md): informazioni su come configurare i criteri di sicurezza.
- [Monitoraggio dello stato della sicurezza nel Centro sicurezza di Azure](security-center-monitoring.md): informazioni su come monitorare l'integrità delle risorse di Azure.
- [Come gestire e rispondere agli avvisi di sicurezza nel Centro sicurezza di Azure](security-center-managing-and-responding-alerts.md): informazioni su come gestire e rispondere agli avvisi di sicurezza.
- [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md): domande frequenti sull'uso del servizio.
- [Blog sulla sicurezza di Azure](http://blogs.msdn.com/b/azuresecurity/): post di blog sulla sicurezza e sulla conformità di Azure.

<!--Image references-->
[2]: ./media/security-center-recommendations/recommendations-tile.png
[3]: ./media/security-center-recommendations/filter-recommendations.png
[4]: ./media/security-center-recommendations/dismiss-recommendations.png
[5]: ./media/security-center-recommendations/select-enable-antimalware.png
[6]: ./media/security-center-recommendations/install-antimalware.png
[7]: ./media/security-center-recommendations/secure-web-application.png
[8]: ./media/security-center-recommendations/vm-configuration.png
[9]: ./media/security-center-recommendations/finalize-waf.png
[10]: ./media/security-center-recommendations/restrict-traffic.png

<!---HONumber=AcomDC_0211_2016-->