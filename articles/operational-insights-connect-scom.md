<properties 
   pageTitle="Connettersi a Operational Insights da System Center Operations Manager" 
   description="Informazioni su come connettersi a Operational Insights tramite Operations Manager." 
   services="operational-insights" 
   documentationCenter="" 
   authors="lauracr" 
   manager="jwhit" 
   editor="bandersmsft"/>

<tags
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="02/20/2015"
   ms.author="lauracr"/>

# Connettersi a Operational Insights da System Center Operations Manager 

È possibile connettere Operational Insights a un ambiente esistente di System Center Operations Manager. Ciò permetterà di usare gli agenti esistenti di Operations Manager come agenti di Operational Insights.

 >[AZURE.NOTE] Il supporto per Operational Insights è disponibile a partire dalle versioni Operations Manager 2012 SP1 UR6 e Operations Manager 2012 R2 UR2. Il supporto per il proxy è stato aggiunto in SCOM 2012 SP1 UR7 e SCOM 2012 R2 UR3.

## Connettere SCOM a Operational Insights e aggiungere agenti

1. Nella console di Operations Manager fare clic su **Amministrazione**.

2. Espandere il nodo **Operational Insights**, quindi selezionare **Operational Insights Connection**.

3. Fare clic sul collegamento **Register to Operational Insights** e seguire le istruzioni visualizzate sullo schermo. 

4. Dopo il completamento della registrazione guidata, fare clic su **Aggiungi computer/gruppo**.

5. Nella finestra di dialogo **Ricerca computer** è possibile cercare i computer o i gruppi monitorati da Operations Manager. Selezionare i computer o i gruppi da caricare in Operational Insights, fare clic su **Aggiungi** e quindi su **OK**.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Passaggi successivi

[Configurare le impostazioni di proxy e firewall (facoltativo)](https://msdn.microsoft.com/library/azure/dn884643.aspx)

<!--HONumber=52-->