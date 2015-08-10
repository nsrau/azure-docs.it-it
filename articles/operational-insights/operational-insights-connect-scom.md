<properties
   pageTitle="Connettersi a Operational Insights da System Center Operations Manager"
   description="Informazioni su come connettersi a Operational Insights tramite Operations Manager."
   services="operational-insights"
   documentationCenter=""
   authors="lauracr"
   manager="jwhit"
   editor=""/>

<tags
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/02/2015"
   ms.author="lauracr"/>

# Connettersi a Operational Insights da System Center Operations Manager


[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

È possibile connettere Operational Insights a un ambiente esistente di System Center Operations Manager. Ciò permetterà di usare gli agenti esistenti di Operations Manager per la raccolta dati. Per altre informazioni sull'uso di Operations Manager con Operational Insights, vedere [Considerazioni su Operations Manager con Operational Insights](operational-insights-operations-manager.md).

Se si usa Operations Manager per monitorare uno dei carichi di lavoro seguenti, sarà necessario configurare account RunAs di Operations Manager corrispondenti. Per altre informazioni sull’impostazione degli account vedere [Considerazioni su Operations Manager con Operational Insights](operational-insights-operations-manager.md).

- SQL Assessment
- Virtual Machine Manager
- Lync Server
- SharePoint

 >[AZURE.NOTE]Il supporto per Operational Insights è disponibile a partire dalle versioni Operations Manager 2012 SP1 UR6 e Operations Manager 2012 R2 UR2. Il supporto per il proxy è stato aggiunto in System Center Operations Manager 2012 SP1 UR7 e System Center Operations Manager 2012 R2 UR3.

## Per connettere Operations Manager a Operational Insights e aggiungere agenti

1. Nella console di Operations Manager fare clic su **Amministrazione**.

2. Espandere il nodo **Operational Insights**, quindi selezionare **Operational Insights Connection**.

3. Fare clic sul collegamento **Register to Operational Insights** e seguire le istruzioni visualizzate sullo schermo.

4. Dopo il completamento della registrazione guidata, fare clic su **Aggiungi computer/gruppo**. ![Aggiunta di un computer o di un gruppo in Operations Manager](./media/operational-insights-connect-scom/om01.png)
5. Nella finestra di dialogo **Ricerca computer** è possibile cercare i computer o i gruppi monitorati da Operations Manager. Selezionare i computer o i gruppi da caricare in Operational Insights, fare clic su **Aggiungi** e quindi su **OK**. ![Aggiunta di computer in Operations Manager](./media/operational-insights-connect-scom/om02.png)
## Passaggi successivi

[Configurare le impostazioni di proxy e firewall (facoltativo)](operational-insights-proxy-firewall.md)

<!---HONumber=July15_HO5-->