<properties
   pageTitle="Gestire i dati relativi ai server e all'utilizzo"
   description="Informazioni sulla quantità di dati inviata al servizio Operational Insights dal server"
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

# Gestire i dati relativi ai server e all'utilizzo

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

Operational Insights raccoglie i dati e li invia periodicamente al servizio Operational Insights. È possibile usare il dashboard **Usage** per visualizzare la quantità di dati inviata al servizio Operational Insights. Il dashboard **Usage** mostra anche la quantità di dati inviata ogni giorno dalle soluzioni e la frequenza dell'invio di dati da parte dei gruppi di gestione.

>[AZURE.NOTE]Se si dispone di un account gratuito, è possibile inviare solo 500 MB di dati al giorno al servizio Operational Insights. Se si raggiunge il limite giornaliero, l'analisi dei dati verrà interrotta e riprenderà all'inizio del giorno successivo.

È possibile visualizzare l'utilizzo mediante il riquadro **Servers and Usage** nel dashboard **Overview** in Operational Insights.

![image of Servers and Usage tile](./media/operational-insights-usage/overview-servers-usage.png)

Se è stato superato o si sta per raggiungere il limite di utilizzo giornaliero, facoltativamente è possibile rimuovere una soluzione per ridurre la quantità di dati inviati al servizio Operational Insights. Per altre informazioni sulla rimozione di soluzioni, vedere [Usare la raccolta di soluzioni per aggiungere o rimuovere soluzioni](operational-insights-setup-workspace.md).

In caso di problemi durante l'invio di dati al servizio Operational Insights da parte di un gruppo di gestione di Operations Manager, è possibile risolvere il problema oppure rimuovere il gruppo da Operational Insights, se necessario.

![image of Usage dashboard](./media/operational-insights-usage/usage-dash.png)

Nel dashboard **Usage** sono visualizzate le informazioni seguenti:

- Utilizzo medio al giorno.

- Utilizzo dei dati per ogni soluzione odierna

- Frequenza con la quale i server di ogni gruppo di gestione inviano dati al servizio Operational Insights

## Per gestire i dati di utilizzo

1. Nella pagina **Overview** fare clic sul riquadro **Servers and Usage**.

2. Nel dashboard **Usage** visualizzare le categorie di utilizzo con aree che presentano potenziali problemi.

3. In caso di dati relativi a una soluzione che usa inutilmente una quantità elevata della quota assegnata, è possibile considerarne la rimozione.

## Per risolvere i problemi dei gruppi di gestione o rimuoverli

1. Nella pagina **Overview** fare clic sul riquadro **Servers and Usage**.

2. Nel dashboard **Usage** visualizzare informazioni sui gruppi di gestione che non inviano dati.

3. Se un gruppo di gestione non invia dati, è possibile fare clic su **Troubleshoot** per ottenere informazioni dettagliate sulla risoluzione dei problemi. Se non si vuole più mantenere un gruppo di gestione e tutti gli agenti che vi fanno riferimento, fare clic su **Remove**.

[AZURE.INCLUDE [operational-insights-export](../../includes/operational-insights-export.md)]

<!---HONumber=August15_HO6-->