<properties 
	pageTitle="Come eseguire il debug con eventi" 
	description="Informazioni su come visualizzare gli eventi in Azure." 
	authors="HaniKN-MSFT" 
	manager="kamrani" 
	editor="" 
	services="application-insights" 
	documentationCenter=""/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/24/2014" 
	ms.author="hanikn"/>

# Monitorare gli eventi che incidono su risorse o gruppi di risorse di Azure

1. Accedere al [portale di anteprima di Azure](https://portal.azure.com/).
2. Fare clic sul pulsante **Sfoglia** nella barra di spostamento a sinistra (nota anche come **barra di collegamento**).  
    ![Browse Hub](./media/insights-debugging-with-events/Insights_Browse.png)
3. Scegliere quindi una qualsiasi risorsa (a seconda degli eventi ai quali si è interessati). A scopo illustrativo, le schermate contenute nel presente documento includono l'evento relativo ai gruppi di risorse.
4. Nel pannello **Gruppi di risorse** fare clic sul nome del gruppo di risorse. Si verrà reindirizzati al pannello del gruppo di risorse.  
    ![Resource groups](./media/insights-debugging-with-events/Insights_SelectRG.png)
5. Il pannello del gruppo di risorse include un'area denominata **Eventi nella settimana precedente**. Ciascuna delle barre presenti in quest'area rappresenta il numero di eventi occorsi in ciascuno dei giorni della settimana precedente e può essere di due diversi colori: azzurro e rosa. Il colore rosa indica gli eventi **non riusciti** di quel giorno, mentre il colore azzurro indica tutti gli altri eventi.  
    ![Resource groups](./media/insights-debugging-with-events/Insights_RGBlade.png)
6. A questo punto, fare clic sull'area **Eventi nella settimana precedente**. Verrà visualizzato un nuovo pannello denominato **Eventi** in cui sono inclusi tutti gli eventi della settimana precedente che hanno influito sul gruppo di risorse.
    ![Resource groups](./media/insights-debugging-with-events/Insights_AllEvents.png)
7. Fare clic su uno degli eventi.  
    ![Resource groups](./media/insights-debugging-with-events/Insights_EventDetails.png)  
    Verrà visualizzato un nuovo pannello contenente molte informazioni dettagliate sull'evento. Per gli eventi **non riusciti** in questa pagina sono in genere incluse le sezioni **SubStatus** **Proprietà**, che contengono informazioni dettagliate utili per il debug.


<!--HONumber=46--> 

<!--HONumber=46--> 
 