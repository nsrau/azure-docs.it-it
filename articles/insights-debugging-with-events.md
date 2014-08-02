<properties title="How to debug with events" pageTitle="How to debug with events" description="Learn how to see events in Azure." authors="hanikn" />

Monitorare gli eventi che incidono sui siti Web e i gruppi di risorse di Azure
==============================================================================

1.  Accedere al [portale di gestione di Azure](https://portal.azure.com/).

2.  Fare clic sul pulsante **Browse** nella barra di spostamento a sinistra (nota anche come **barra di collegamento**).

   ![Hub di esplorazione](./media/insights-debugging-with-events/Insights_Browse.png)

3.  Scegliere quindi **Resource groups** o **Websites** (a seconda degli eventi ai quali si è interessati). A scopo illustrativo, le schermate contenute nel presente documento includono l'evento relativo ai gruppi di risorse.

4.  Nel blade **Resource groups** fare clic sul nome del gruppo di risorse. Si verrà reindirizzati al blade del gruppo di risorse.

    ![Gruppi di risorse](./media/insights-debugging-with-events/Insights_SelectRG.png)

5.  Il blade del gruppo di risorse include un'area denominata **Events in the past week**. Ciascuna delle barre presenti in quest'area rappresenta il numero di eventi occorsi in ciascuno dei giorni della settimana precedente e può essere di due diversi colori: azzurro e rosa. Il colore rosa indica gli eventi **non riusciti** di quel giorno, mentre il colore azzurro indica tutti gli altri eventi.

   ![Gruppi di risorse](./media/insights-debugging-with-events/Insights_RGBlade.png)

6.  A questo punto, fare clic sull'area **Events in the past week**. Verrà visualizzato un nuovo blade denominato **Events** in cui sono inclusi tutti gli eventi della settimana precedente che hanno influito sul gruppo di risorse.

   ![Gruppi di risorse](./media/insights-debugging-with-events/Insights_AllEvents.png)

7.  Fare clic su uno degli eventi.

   ![Gruppi di risorse](./media/insights-debugging-with-events/Insights_EventDetails.png)

Verrà visualizzato un nuovo blade contenente molte informazioni dettagliate sull'evento. Per gli eventi non riusciti (**Failed**), in questa pagina sono normalmente incluse le sezioni **Substatus** e **Properties** che contengono informazioni dettagliate utili a scopo di debug.

