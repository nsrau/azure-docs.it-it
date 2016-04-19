<properties
   pageTitle="Ottenere informazioni dettagliate sui dati del Centro sicurezza di Azure con Power BI| Microsoft Azure"
   description="Il pacchetto di contenuto di Power BI nel Centro sicurezza di Azure rende più semplice trovare gli avvisi e le indicazioni di sicurezza, le risorse che hanno subito attacchi e le tendenze sulla base di un set di dati creato per il report."
   services="security-center"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/11/2016"
   ms.author="yurid"/>

# Ottenere informazioni dettagliate sui dati del Centro sicurezza di Azure con Power BI
Il [dashboard di Power BI](http://aka.ms/azure-security-center-power-bi) nel Centro sicurezza di Azure consente di visualizzare, analizzare e filtrare indicazioni e avvisi di sicurezza da qualunque origine, inclusi i dispositivi mobili. Usare il dashboard di Power BI per rivelare tendenze e modelli di attacco, visualizzare avvisi di sicurezza per ogni risorsa o indirizzo IP di origine, nonché i rischi per la sicurezza non risolti per risorsa ed età. È anche possibile eseguire il mashup delle indicazioni e degli avvisi di sicurezza del Centro sicurezza con altri dati in modi interessanti, ad esempio con i [log di controllo di Azure ](https://powerbi.microsoft.com/blog/monitor-azure-audit-logs-with-power-bi/) e il [servizio di controllo del database SQL di Azure](https://powerbi.microsoft.com/blog/monitor-your-azure-sql-database-auditing-activity-with-power-bi/), che offrono entrambi dashboard di Power BI, oppure è possibile esportare questi dati in Excel per creare facilmente report sullo stato della sicurezza delle risorse cloud.

> [AZURE.NOTE] Le informazioni contenute in questo documento si applicano alla versione di anteprima del Centro sicurezza di Azure.

## Esplorazione dei dati del Centro sicurezza di Azure con i servizi Power BI
Connettersi al [pacchetto di contenuto del Centro sicurezza di Azure](https://app.powerbi.com/groups/me/getdata/services/azure-security-center) in Power BI e seguire questa procedura:

1\. Fare clic su **Connetti** nel riquadro Centro sicurezza di Azure per continuare.

![Connetti a Centro sicurezza di Azure con Power BI](./media/security-center-powerbi/security-center-powerbi-fig1.png)

2\. Verrà visualizzata la finestra **Connetti a Centro sicurezza di Azure**. Nel campo **ID sottoscrizione di Azure** digitare la sottoscrizione di Azure e fare clic su **Avanti**.

![Connetti a Centro sicurezza di Azure con Power BI](./media/security-center-powerbi/security-center-powerbi-fig2.png)

3\. Nell'elenco a discesa **Metodo di autenticazione** selezionare **oAuth2** e fare clic su **Accedi**.

![Connetti a Centro sicurezza di Azure con Power BI](./media/security-center-powerbi/security-center-powerbi-fig3.png)

4\. Si verrà reindirizzati a una pagina di autenticazione in cui è necessario digitare le credenziali usate per connettersi al Centro sicurezza di Azure. Al termine del processo di autenticazione, Power BI inizierà a importare dati per compilare i report. Durante questa fase potrebbe essere visualizzato il messaggio seguente nell'angolo destro del browser:

![Connetti a Centro sicurezza di Azure con Power BI](./media/security-center-powerbi/security-center-powerbi-fig4.png)

5\. Una volta completato il processo, il dashboard di Power BI nel Centro sicurezza di Azure verrà caricato con i report, come illustrato di seguito:

![Connetti a Centro sicurezza di Azure con Power BI](./media/security-center-powerbi/security-center-powerbi-fig5.png)

È possibile visualizzare riepilogati il numero di indicazioni e avvisi di sicurezza, nonché il numero di macchine virtuali, i database SQL di Azure e le risorse di rete monitorate dal Centro sicurezza di Azure.

Un collegamento al Centro sicurezza di Azure reindirizza al portale di Azure. I grafici rendono più semplice visualizzare le informazioni su indicazioni e avvisi relativi alla sicurezza, ad esempio:

- Integrità della sicurezza delle risorse
- Indicazioni in sospeso in generale
- Raccomandazioni per le VM
- Avvisi nel tempo
- Risorse che hanno subito attacchi
- IP utenti malintenzionati

Per ogni grafico sono disponibili informazioni aggiuntive. Selezionare un riquadro per visualizzare altre informazioni, ad esempio il riquadro relativo all'integrità della sicurezza delle risorse, che fornisce dettagli aggiuntivi sulle indicazioni in sospeso, per ogni risorsa, come illustrato di seguito:

![Connetti a Centro sicurezza di Azure con Power BI](./media/security-center-powerbi/security-center-powerbi-fig6.png)

Se si fa clic su una riga qualsiasi di questo grafico, le altre saranno visualizzate in grigio permettendo di concentrarsi solo su quella selezionata. Per tornare al dashboard, fare clic su **Centro sicurezza di Azure** sotto l'opzione **Dashboard** nel riquadro sinistro della pagina.

> [AZURE.NOTE] I report possono essere modificati personalizzandoli con l'aggiunta di altri campi o la modifica degli oggetti visivi esistenti. Per altre informazioni, vedere [Interagire con un report nella Visualizzazione di modifica in Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-interact-with-a-report-in-editing-view/).

L'output dei riquadri **Avvisi nel tempo**, **Risorse che hanno subito attacchi** e **IP utenti malintenzionati** sarà simile al seguente quando si fa clic su ognuno di essi. Ciò accade perché il report aggrega le informazioni relative a tutte e tre le variabili e le definisce **Risorse sotto attacco** come illustrato di seguito:

![Connetti a Centro sicurezza di Azure con Power BI](./media/security-center-powerbi/security-center-powerbi-fig7.png)

A questo punto è anche possibile salvare una copia di questo report, stamparlo o pubblicarlo sul Web usando le opzioni disponibili nel menu **File**.

![Connetti a Centro sicurezza di Azure con Power BI](./media/security-center-powerbi/security-center-powerbi-fig8.png)


##Uso del dashboard del Centro sicurezza di Azure per accedere a Power BI
È anche possibile usare il dashboard del Centro sicurezza di Azure per accedere ai report di Power BI. Seguire questa procedura per eseguire questa attività:

1\. Nel dashboard **Centro sicurezza di Azure** fare clic sul pulsante **Esplora in Power BI**.

![Connetti a Centro sicurezza di Azure con Power BI](./media/security-center-powerbi/security-center-powerbi-fig9-new.png)

2\. Sul lato destro verrà visualizzato il pannello **Esplora in Power BI**, come illustrato di seguito:

![Connetti a Centro sicurezza di Azure con Power BI](./media/security-center-powerbi/security-center-powerbi-fig10-1.png)

3\. Nell'elenco a discesa **Scegliere una sottoscrizione da esplorare in Power BI** selezionare la sottoscrizione che si vuole usare.

4\. Nel campo **Copia ID sottoscrizione** fare clic sul pulsante Copia. 5 Fare clic sul pulsante **Vai a Power BI**. 6. Verrà visualizzata la finestra **Connetti a Centro sicurezza di Azure**. Nel campo **ID sottoscrizione di Azure** digitare la sottoscrizione di Azure e fare clic su **Avanti**.

![Connetti a Centro sicurezza di Azure con Power BI](./media/security-center-powerbi/security-center-powerbi-fig2.png)

7\. Nell'elenco a discesa **Metodo di autenticazione** selezionare **oAuth2** e fare clic su **Accedi**.

![Connetti a Centro sicurezza di Azure con Power BI](./media/security-center-powerbi/security-center-powerbi-fig3.png)

8\. Si verrà reindirizzati a una pagina di autenticazione in cui è necessario digitare le credenziali usate per connettersi al Centro sicurezza di Azure. Al termine del processo di autenticazione, Power BI inizierà a importare dati per compilare i report.

> [AZURE.NOTE] Un aggiornamento del report è pianificato su base giornaliera. In caso di errore di questo aggiornamento, leggere il blog relativo ai [potenziali problemi di aggiornamento con Power BI nel Centro sicurezza di Azure](https://blogs.msdn.microsoft.com/azuresecurity/2016/04/07/azure-security-center-power-bi-refresh-fails/), per altre informazioni su come risolverli.

## Passaggi successivi
In questo documento è stato descritto come usare Power BI nel Centro sicurezza di Azure. Per ulteriori informazioni sul Centro sicurezza di Azure, vedere gli argomenti seguenti:

- [Impostazione dei criteri di sicurezza nel Centro sicurezza di Azure](security-center-policies.md): informazioni su come configurare le impostazioni di sicurezza nel Centro sicurezza di Azure
- [Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure](security-center-managing-and-responding-alerts.md): informazioni su come gestire e rispondere agli avvisi di sicurezza
- [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md): domande frequenti sull'utilizzo del servizio
- [Blog sulla sicurezza di Azure](http://blogs.msdn.com/b/azuresecurity/): post di blog sulla sicurezza e sulla conformità di Azure

<!---HONumber=AcomDC_0413_2016-->