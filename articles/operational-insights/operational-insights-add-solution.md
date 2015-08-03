<properties
    pageTitle="Soluzioni di Operational Insights"
    description="Con le soluzioni è possibile aggiungere altre funzionalità a Operational Insights."
    services="operational-insights"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="operational-insights"
    ms.workload="operational-insights"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="07/02/2015"
    ms.author="banders"/>

# Soluzioni di Operational Insights

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

Microsoft Azure Operational Insights include il modulo di valutazione della configurazione di base. È tuttavia possibile ottenere altre funzionalità aggiungendovi soluzioni dalla pagina Overview.

![immagine dell'icona delle soluzioni](./media/operational-insights-add-solution/sol-gallery.png)

Dopo aver aggiunto una soluzione, i dati vengono raccolti dai server dell'infrastruttura e inviati al servizio Operational Insights. L'elaborazione da parte del servizio Operational Insights può richiedere da alcuni minuti a diverse ore. Dopo che il servizio ha elaborato i dati, è possibile visualizzarli in Operational Insights.

È possibile rimuovere facilmente una soluzione quando non è più necessaria. Quando si rimuove una soluzione, i relativi dati non vengono inviati a Operational Insights, pertanto la quantità di dati usati per la quota giornaliera si riduce.

## Soluzioni supportate da Microsoft Monitoring Agent

I server connessi direttamente a Microsoft Azure Operational Insights tramite Microsoft Monitoring Agent possono attualmente usare la maggior parte delle soluzioni disponibili, tra cui:

- [System Updates](operational-insights-updates.md)

- [Log Management](operational-insights-log-collection.md)

- [Antimalware](operational-insights-antimalware.md)

- [Change Tracking](operational-insights-change-tracking.md)

- [SQL and Active Directory Assessment](operational-insights-assessment.md)

Le soluzioni seguenti, tuttavia , *non* sono supportate con Microsoft Monitoring Agent e richiedono System Center Operations Manager (SCOM).

- [Capacity Management](operational-insights-capacity.md)

- [Gestione degli avvisi](operational-insights-alerts.md)

- [Configuration Assessment](operational-insights-solutions.md#configuration-assessment)

Fare riferimento a [Considerazioni su Operations Manager con Operational Insights](operational-insights-operations-manager.md) per istruzioni sull'utilizzo di queste soluzioni con Operations Manager.

La raccolta di log di IIS è supportata nei computer con i sistemi operativi seguenti:

- Windows Server 2012

- Windows Server 2012 R2

### Per aggiungere una soluzione


1. Nella pagina Overview di Operational Insights fare clic sul riquadro **Solutions Gallery**.


2. Nella pagina Solutions Gallery di Operational Insights è possibile ottenere informazioni su ogni soluzione disponibile. Fare clic sul nome della soluzione da aggiungere a Operational Insights.


3. Nella pagina relativa alla soluzione scelta vengono visualizzate informazioni dettagliate sulla soluzione visualizzata. Fare clic su **Add**.


4. Nella pagina di conferma, fare clic su **Accept** per accettare le condizioni per l'utilizzo e l'informativa sulla privacy.


5. Nella pagina Overview in Operational Insights viene visualizzato un nuovo riquadro per la soluzione aggiunta ed è possibile iniziare a usarla dopo che il servizio Operational Insights ha elaborato i dati.




### Per rimuovere una soluzione



1. Nella pagina Overview di Operational Insights fare clic sul riquadro **Solutions Gallery**.


2. Nella pagina Solutions Gallery di Operational Insights fare clic su **Remove** sotto la soluzione da rimuovere.


3. Nella pagina di conferma, fare clic su **Yes** per rimuovere la soluzione

<!---HONumber=July15_HO4-->