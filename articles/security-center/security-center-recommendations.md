<properties
   pageTitle="Gestione delle raccomandazioni di sicurezza nel Centro sicurezza di Azure | Microsoft Azure"
   description="Questo documento dimostra come le raccomandazioni presenti nel Centro sicurezza di Azure facilitino la protezione delle risorse di Azure e garantiscano la conformità ai criteri di sicurezza."
   services="security-center"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="MBaldwin"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/25/2016"
   ms.author="terrylan"/>

# Gestione delle raccomandazioni di sicurezza nel Centro sicurezza di Azure

Questo documento illustra come usare le raccomandazioni presenti nel Centro sicurezza di Azure per la protezione delle risorse di Azure.

> [AZURE.NOTE] Il documento introduce il servizio usando una distribuzione di esempio. Questa non è una guida dettagliata.

## Informazioni sulle raccomandazioni di sicurezza
Il Centro sicurezza analizza periodicamente lo stato di sicurezza delle risorse di Azure. Quando identifica potenziali vulnerabilità della sicurezza, crea raccomandazioni. Queste raccomandazioni illustrano in dettaglio il processo di configurazione dei controlli necessari.

## Implementare le raccomandazioni di sicurezza

### Impostare le raccomandazioni

In [Impostazione dei criteri di sicurezza nel Centro sicurezza di Azure](security-center-policies.md) si apprende come:

- Configurare i criteri di sicurezza.
- Attivare la raccolta dei dati.
- Scegliere le raccomandazioni da visualizzare nell'ambito dei criteri di sicurezza.

Le raccomandazioni relative ai criteri di sicurezza si basano attualmente su aggiornamenti di sistema, regole di base, programmi antimalware, [gruppi di sicurezza di rete](../virtual-network/virtual-networks-nsg.md) per subnet e interfacce di rete, controllo del database SQL, Transparent Data Encryption per il database SQL e web application firewall. L'articolo [Impostazione dei criteri di sicurezza](security-center-policies.md) fornisce una descrizione di ogni opzione relativa alle raccomandazioni.

### Monitorare le raccomandazioni
Una volta impostato un criterio di sicurezza, il Centro sicurezza analizza lo stato di sicurezza delle risorse per identificare le potenziali vulnerabilità. Il riquadro **Raccomandazioni** nel pannello **Centro sicurezza** consente di conoscere il numero totale di raccomandazioni identificate dal Centro sicurezza.

![Riquadro Raccomandazioni][1]

Per visualizzare i dettagli di ogni raccomandazione:

1. Fare clic sul riquadro **Raccomandazioni** del pannello **Centro sicurezza PC**. Viene visualizzato il pannello **Raccomandazioni**.

Le raccomandazioni vengono visualizzate sotto forma di tabella, in cui ogni riga rappresenta una particolare raccomandazione. Le colonne della tabella sono:

- **DESCRIZIONE**: spiega la raccomandazione e cosa occorre fare per metterla in pratica.
- **RISORSA**: elenca le risorse a cui si applica questa raccomandazione.
- **STATO**: descrive lo stato attuale della raccomandazione:
    - **Aperta**: la raccomandazione non è ancora stata applicata.
    - **In corso**: la raccomandazione è stata applicata alle risorse e non è richiesta alcuna azione da parte dell'utente.
    - **Risolta**: la raccomandazione è già stata completata. In questo caso, la riga sarà visualizzata in grigio.
- **GRAVITÀ**: descrive il livello di gravità della raccomandazione:
    - **Alta**: una vulnerabilità associata a una risorsa significativa, ad esempio un'applicazione, una macchina virtuale o un gruppo di sicurezza di rete, richiede attenzione.
    - **Media**: per eliminare una vulnerabilità o per completare un processo sono necessari passaggi aggiuntivi o non critici.
    - **Bassa**: una vulnerabilità che è opportuno risolvere, non richiede attenzione immediata. Per impostazione predefinita, le raccomandazioni con gravità bassa non appaiono, ma è possibile visualizzarle applicando il filtro corrispondente.

Usare la tabella seguente come riferimento per conoscere le raccomandazioni disponibili e gli effetti che producono se si decide di metterle in pratica.

> [AZURE.NOTE] È necessario conoscere i [modelli di distribuzione di Resource Manager e classici](../azure-classic-rm.md) per le risorse di Azure.

|Raccomandazione|Descrizione|
|-----|-----|
|[Abilita la raccolta di dati per le sottoscrizioni](security-center-enable-data-collection.md)|Consiglia di attivare la raccolta dati nei criteri di sicurezza per ogni sottoscrizione e per tutte le macchine virtuali nelle sottoscrizioni.|
|[Remediate OS vulnerabilities (Risolvi vulnerabilità del sistema operativo)](security-center-remediate-os-vulnerabilities.md)|Consiglia di allineare le configurazioni dei sistemi operativi alle regole di configurazione raccomandate, ad esempio non consentire il salvataggio delle password.|
|[Applicare gli aggiornamenti di sistema](security-center-apply-system-updates.md)|Consiglia di distribuire gli aggiornamenti critici e della sicurezza di sistema mancanti nelle macchine virtuali.|
|[Riavvia dopo gli aggiornamenti del sistema](security-center-apply-system-updates.md#reboot-after-system-updates)|Consiglia di riavviare una macchina virtuale per completare il processo di applicazione degli aggiornamenti del sistema.|
|[Aggiungere un Web Application Firewall](security-center-add-web-application-firewall.md)|Suggerisce di distribuire un Web application firewall (WAF) per gli endpoint Web. Per proteggere più applicazioni Web in Centro sicurezza, è possibile aggiungerle alle distribuzioni WAF esistenti. Le appliance WAF (create con il modello di distribuzione di Resource Manager) devono essere distribuite in una rete virtuale separata. Le appliance WAF (create con il modello di distribuzione classica) sono limitate all'uso di un gruppo di sicurezza di rete. In futuro tale supporto verrà esteso a una distribuzione completamente personalizzata di un'appliance WAF (versione classica). Centro sicurezza consiglierà di effettuare il provisioning di un WAF per consentire la protezione da attacchi contro le applicazioni Web nelle VM e nell'ambiente del servizio app. Per altre informazioni sull'ambiente del servizio app, vedere [Documentazione relativa agli ambienti del servizio app](../app-service/app-service-app-service-environments-readme.md). |
|[Finalizza la protezione dell'applicazione](security-center-add-web-application-firewall.md#finalize-application-protection)|Per completare la configurazione di un Web Application Firewall, il traffico deve essere reindirizzato al dispositivo WAF. Seguendo questa raccomandazione si completeranno le modifiche di configurazione necessarie.|
|[Aggiungi un firewall di nuova generazione](security-center-add-next-generation-firewall.md)|Il Centro sicurezza di Azure consiglia di aggiungere un firewall di nuova generazione di un partner Microsoft per aumentare la protezione delle applicazioni Web.|
|[Indirizza il traffico solo tramite il firewall di nuova generazione](security-center-add-next-generation-firewall.md#route-traffic-through-ngfw-only)|Consiglia di configurare le regole del gruppo di sicurezza di rete che forzano il traffico in ingresso alla VM tramite il firewall di nuova generazione.|
|[Installa Endpoint Protection](security-center-install-endpoint-protection.md)|Suggerisce di effettuare il provisioning dei programmi antimalware nelle macchine virtuali (solo VM Windows).|
|[Risolvi gli avvisi sull'integrità di Endpoint Protection](security-center-resolve-endpoint-protection-health-alerts.md)|Si consiglia di correggere gli errori relativi alla protezione degli endpoint.|
|[Abilita i gruppi di sicurezza di rete sulle subnet o sulle macchine virtuali](security-center-enable-network-security-groups.md)|Consiglia di attivare i gruppo di sicurezza di rete sulle subnet o sulle macchine virtuali.|
|[Limita l'accesso tramite un endpoint con connessione Internet](security-center-restrict-access-through-internet-facing-endpoints.md)|Consiglia di configurare le regole del traffico in ingresso per i gruppi di sicurezza di rete.|
|[Abilitare il servizio di controllo SQL per i server](security-center-enable-auditing-on-sql-servers.md)|Suggerisce di attivare il controllo per i server SQL di Azure. Solo il servizio SQL di Azure, non SQL in esecuzione nelle macchine virtuali.|
|[Abilitare il servizio di controllo SQL per i database](security-center-enable-auditing-on-sql-databases.md)|Suggerisce di attivare il controllo per i database SQL di Azure. Solo il servizio SQL di Azure, non SQL in esecuzione nelle macchine virtuali.|
|[Abilitare Transparent Data Encryption sui database SQL](security-center-enable-transparent-data-encryption.md)|Consiglia di abilitare la crittografia per i database SQL (solo il servizio di SQL Azure).|
|[Abilita l'agente di macchine virtuali](security-center-enable-vm-agent.md)|Consente di identificare le macchine virtuali per le quali è necessario l'agente di macchine virtuali, che deve essere installato nelle macchine virtuali per poter effettuare il provisioning dei programmi di analisi delle patch, analisi della baseline e antimalware. Per impostazione predefinita, l'agente di macchine virtuali è installato nelle macchine virtuali distribuite da Azure Marketplace. L'articolo relativo all'[agente di macchine virtuali e relative estensioni, parte 2](http://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/) fornisce informazioni su come installare l'agente di macchine virtuali.|
| [Applicare Crittografia dischi](security-center-apply-disk-encryption.md) |Suggerisce di crittografare i dischi delle macchine virtuali con Crittografia dischi di Azure (VM Windows e Linux). La crittografia è consigliabile sia per il sistema operativo sia per i volumi di dati della macchina virtuale.|
|[Specificare dettagli del contatto per la sicurezza](security-center-provide-security-contact-details.md) | Suggerisce di specificare le informazioni di contatto per la sicurezza per ogni sottoscrizione. Le informazioni di contatto sono un indirizzo di posta elettronica e un numero di telefono. Le informazioni vengono usate per contattare l'utente se il team di sicurezza rileva risorse compromesse. |
| [Aggiornare la versione sistema operativo](security-center-update-os-version.md) | Suggerisce di aggiornare la versione del sistema operativo per il servizio cloud alla versione più recente disponibile per la famiglia di sistemi operativi. Per altre informazioni sul servizio cloud, vedere [Perché scegliere Servizi cloud](../cloud-services/cloud-services-choose-me.md). |
| [La valutazione della vulnerabilità non è installata](security-center-vulnerability-assessment-recommendations.md) | Consiglia di installare una soluzione di valutazione della vulnerabilità nella VM. |
| [Correggi le vulnerabilità](security-center-vulnerability-assessment-recommendations.md#review-recommendation) | Consente di visualizzare le vulnerabilità del sistema e delle applicazioni rilevate dalla soluzione di valutazione delle vulnerabilità installata nella VM. |

È possibile filtrare ed eventualmente ignorare le raccomandazioni.

1. Nel pannello **Raccomandazioni** fare clic su **Filtro**. Viene visualizzato il pannello **Filtro** in cui è possibile selezionare i valori relativi a gravità e stato da visualizzare.

    ![Filtrare le raccomandazioni][2]

2. Se si ritiene che una delle raccomandazioni non sia applicabile, è possibile ignorarla ed escluderla dalla visualizzazione. Esistono due modi per ignorare una raccomandazione. Uno consiste nel fare clic con il pulsante destro del mouse su un elemento e quindi scegliere **Ignora**. L'altro nel passare il puntatore del mouse su un elemento, fare clic sui tre punti che appaiono a destra e selezionare **Ignora**. È possibile visualizzare le raccomandazioni ignorate facendo clic su **Filtro** e scegliendo **Ignorata**.

    ![Ignorare una raccomandazione][3]

### Applicare le raccomandazioni
Dopo aver esaminato tutte le raccomandazioni, decidere quale applicare per prima. È consigliabile usare la classificazione di gravità come parametro principale per valutare quali raccomandazioni applicare per prime.

Nella tabella delle raccomandazioni precedente selezionare una raccomandazione e usarla come esempio di come applicare una raccomandazione.

## Vedere anche
Questo documento ha introdotto le raccomandazioni relative alla sicurezza nel Centro sicurezza. Per altre informazioni sul Centro sicurezza, vedere gli argomenti seguenti:

- [Impostazione dei criteri di sicurezza nel Centro sicurezza di Azure](security-center-policies.md): informazioni su come configurare i criteri di sicurezza per le sottoscrizioni e i gruppi di risorse di Azure.
- [Monitoraggio dell'integrità della sicurezza nel Centro sicurezza di Azure](security-center-monitoring.md): informazioni su come monitorare l'integrità delle risorse di Azure.
- [Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure](security-center-managing-and-responding-alerts.md): informazioni su come gestire e rispondere agli avvisi di sicurezza.
- [Monitoraggio delle soluzioni dei partner con il Centro sicurezza di Azure](security-center-partner-solutions.md): informazioni su come monitorare l'integrità delle soluzioni dei partner.
- [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md): domande frequenti sull'uso del servizio.
- [Blog sulla sicurezza di Azure](http://blogs.msdn.com/b/azuresecurity/): post di blog sulla sicurezza e sulla conformità di Azure.

<!--Image references-->  
[1]: ./media/security-center-recommendations/recommendations-tile.png
[2]: ./media/security-center-recommendations/filter-recommendations.png
[3]: ./media/security-center-recommendations/dismiss-recommendations.png

<!---HONumber=AcomDC_0928_2016-->