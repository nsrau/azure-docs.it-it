<properties
   pageTitle="Aggiornare i server con gli aggiornamenti del sistema"
   description="Informazioni su come usare la soluzione System Updates in Microsoft Azure Operational Insights per applicare gli aggiornamenti mancanti ai server dell'infrastruttura."
   services="operational-insights"
   documentationCenter=""
   authors="bandersmsft"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/05/2015"
   ms.author="banders" />

# Aggiornare i server con gli aggiornamenti del sistema

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

È possibile usare la soluzione System Updates in Microsoft Azure Operational Insights per applicare gli aggiornamenti mancanti ai server dell'infrastruttura. Installare la soluzione per aggiornare l'agente Operations Manager e il modulo di configurazione di base per Operational Insights. Le informazioni di aggiornamento vengono lette sui server monitorati e i dati di aggiornamento vengono quindi inviati al servizio Operational Insights nel cloud per l'elaborazione. Viene applicata la logica ai dati di aggiornamento, che vengono quindi registrati dal servizio cloud. Gli eventuali aggiornamenti mancanti rilevati vengono visualizzati nel dashboard **Updates**. È possibile usare il dashboard **Updates** per elaborare gli aggiornamenti mancanti e sviluppare un piano per applicarli ai server su cui sono necessari.

## Usare gli aggiornamenti del sistema per aggiornare i server

Prima di poter usare gli aggiornamenti di sistema in Microsoft Azure Operational Insights, è necessario disporre della soluzione installata. Per altre informazioni sull'installazione di soluzioni, vedere [Usare la raccolta di soluzioni per aggiungere o rimuovere soluzioni](operational-insights-setup-workspace.md). Dopo l'installazione, è possibile visualizzare gli aggiornamenti mancanti nei server monitorati usando il riquadro **System Update Assessment** nella pagina **Overview** in Operational Insights.

### Per usare gli aggiornamenti

1. Nella pagina **Overview** fare clic sul riquadro **System Update Assessment**. ![immagine della pagina Overview](./media/operational-insights-updates/updates01.png)
2. Nel dashboard **Updates** visualizzare le categorie degli aggiornamenti. ![immagine della pagina Updates](./media/operational-insights-updates/updates02.png)
3. Scorrere verso destra la pagina per visualizzare il pannello **Type of Updates Missing** e quindi fare clic su **Security Updates**. ![immagine della pagina Updates](./media/operational-insights-updates/updates03.png)
4. Nella pagina di ricerca, viene visualizzato un elenco di aggiornamenti di sicurezza mancante nei server dell'infrastruttura. Fare clic sull'ID di un articolo della Knowledge Base (KBID) per visualizzare altre informazioni sull'aggiornamento mancante. In questo esempio, *KBID 3032323*. ![immagine della pagina Updates](./media/operational-insights-updates/updates04.png)
5. Il browser Web apre l'articolo della Knowledge Base che descrive l'aggiornamento. ![immagine della pagina Updates](./media/operational-insights-updates/updates05.png)
6. Con le informazioni trovate, è possibile creare un piano per applicare gli aggiornamenti mancanti.

[AZURE.INCLUDE [operational-insights-export](../../includes/operational-insights-export.md)]

<!---HONumber=August15_HO6-->