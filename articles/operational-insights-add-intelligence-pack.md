<properties 
    pageTitle="Intelligence Pack di Operational Insights" 
    description="Con gli Intelligence Pack è possibile aggiungere altre funzionalità a Operational Insights." 
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
    ms.topic="article" 
    ms.date="03/20/2015" 
    ms.author="banders"/>

# Intelligence Pack di Operational Insights

Microsoft Azure Operational Insights include il modulo di valutazione della configurazione di base. È tuttavia possibile ottenere altre funzionalità aggiungendo Intelligence Pack dalla pagina Overview.

![image of intelligence packs icon](./media/operational-insights-add-intelligence-pack/overview-intelpacks.png)

Dopo aver aggiunto un Intelligence Pack, i dati vengono raccolti dai server dell'infrastruttura e inviati al servizio Operational Insights. L'elaborazione da parte del servizio Operational Insights può richiedere da alcuni minuti a diverse ore. Dopo che il servizio ha elaborato i dati, è possibile visualizzarli in Operational Insights.

È possibile rimuovere facilmente un Intelligence Pack quando non è più necessario. Quando si rimuove un Intelligence Pack, i relativi dati non vengono inviati a Operational Insights, pertanto la quantità di dati usati per la quota giornaliera si riduce.

## Intelligence Pack supportati da Microsoft Monitoring Agent

I server connessi direttamente a Microsoft Azure Operational Insights tramite Microsoft Monitoring Agent possono attualmente usare la maggior parte degli Intelligence Pack disponibili, tra cui:

- [System Updates](operational-insights-updates.md)

- [Log Management](operational-insights-log-collection.md)

- [Antimalware](operational-insights-antimalware.md)

- [Change Tracking](operational-insights-change-tracking.md)

- [SQL and Active Directory Assessment](operational-insights-assessment.md)

Gli Intelligence Pack seguenti, invece, *non* sono supportati con Microsoft Monitoring Agent

- [Capacity Management](operational-insights-capacity.md)

- [Configuration Assessment](operational-insights-configuration-assessment.md)

La raccolta di log di IIS è supportata nei computer con i sistemi operativi seguenti:

- Windows Server 2012

- Windows Server 2012 R2

### Per aggiungere un Intelligence Pack


1. Nella pagina Overview in Operational Insights fare clic sul riquadro **Intelligence Packs**.


2. Nella pagina Intelligence Packs Gallery di Operational Insights è possibile ottenere informazioni su ogni Intelligence Pack disponibile. Fare clic sul nome dell'Intelligence Pack da aggiungere a Operational Insights.


3. Nella pagina relativa all'Intelligence Pack scelto, vengono visualizzate informazioni dettagliate sull'Intelligence Pack visualizzato. Fare clic su **Aggiungi**.


4. Nella pagina di conferma, fare clic su **Accept** per accettare le condizioni per l'uso e l'informativa sulla privacy.


5. Nella pagina Overview in Operational Insights viene visualizzato un nuovo riquadro per l'Intelligence Pack aggiunto ed è possibile iniziare a usarlo dopo che il servizio Operational Insights ha elaborato i dati.




### Per rimuovere un Intelligence Pack



1. Nella pagina Overview in Operational Insights fare clic sul riquadro **Intelligence Packs**.


2. Nella pagina Intelligence Packs Gallery di Operational Insights fare clic su **Remove** sotto l'Intelligence Pack da rimuovere.


3. Nella pagina di conferma, fare clic su **Yes** per rimuovere l'Intelligence Pack.




<!--HONumber=52-->