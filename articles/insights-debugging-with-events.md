<properties title="How to debug with events" pageTitle="How to debug with events" description="Learn how to see events in Azure." authors="hanikn"  />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="hanikn"></tags>

# Monitorare gli eventi che incidono su risorse o gruppi di risorse di Azure

1.  Accedere al [portale di gestione di Azure][portale di gestione di Azure].
2.  Fare clic sul pulsante **Sfoglia** nella barra di spostamento a sinistra (nota anche come **barra di collegamento**).  
    ![Browse Hub][Browse Hub]
3.  Scegliere quindi una risorsa qualsiasi (a seconda degli eventi ai quali si è interessati). A scopo illustrativo, le schermate contenute nel presente documento includono l'evento relativo ai gruppi di risorse.
4.  Nel pannello **Resource groups** fare clic sul nome del gruppo di risorse. Si verrà reindirizzati al pannello del gruppo di risorse.  
    ![Resource groups][Resource groups]
5.  Il pannello del gruppo di risorse include un'area denominata **Events in the past week**. Ciascuna delle barre presenti in quest'area rappresenta il numero di eventi occorsi in ciascuno dei giorni della settimana precedente e può essere di due diversi colori: azzurro e rosa. Il colore rosa indica gli eventi **non riusciti** di quel giorno, mentre il colore azzurro indica tutti gli altri eventi.  
    ![Resource groups][1]
6.  A questo punto, fare clic sull'area **Events in the past week**. Verrà visualizzato un nuovo pannello denominato **Events** in cui sono inclusi tutti gli eventi della settimana precedente che hanno influito sul gruppo di risorse.
    ![Resource groups][2]
7.  Fare clic su uno degli eventi.  
    ![Resource groups][3]  
    Verrà visualizzato un nuovo pannello contenente molte informazioni dettagliate sull'evento. Per gli eventi non riusciti (**Failed**), in questa pagina sono normalmente incluse le sezioni **Substatus** e **Properties** che contengono informazioni dettagliate utili a scopo di debug.

  [portale di gestione di Azure]: https://portal.azure.com/
  [Browse Hub]: ./media/insights-debugging-with-events/Insights_Browse.png
  [Resource groups]: ./media/insights-debugging-with-events/Insights_SelectRG.png
  [1]: ./media/insights-debugging-with-events/Insights_RGBlade.png
  [2]: ./media/insights-debugging-with-events/Insights_AllEvents.png
  [3]: ./media/insights-debugging-with-events/Insights_EventDetails.png
