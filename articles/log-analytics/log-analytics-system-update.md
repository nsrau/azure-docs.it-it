<properties
    pageTitle="Soluzione di valutazione dell'aggiornamento del sistema in Log Analytics | Microsoft Azure"
    description="È possibile usare la soluzione System Update Assessment (Valutazione aggiornamento del sistema) in Log Analytics per applicare gli aggiornamenti mancanti ai server dell'infrastruttura."
    services="log-analytics"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="banders"/>


# <a name="system-update-assessment-solution-in-log-analytics"></a>Soluzione di valutazione dell'aggiornamento del sistema in Log Analytics

È possibile usare la soluzione System Update Assessment (Valutazione aggiornamento del sistema) in Log Analytics per applicare gli aggiornamenti mancanti ai server dell'infrastruttura. Dopo l'installazione della soluzione, è possibile visualizzare gli aggiornamenti mancanti nei server monitorati usando il riquadro **Valutazione aggiornamento del sistema** nella pagina **Panoramica** in OMS.

Gli eventuali aggiornamenti mancanti rilevati sono descritti nel dashboard **Updates** (Aggiornamenti). È possibile usare il dashboard **Updates** per elaborare gli aggiornamenti mancanti e sviluppare un piano per applicarli ai server su cui sono necessari.

## <a name="installing-and-configuring-the-solution"></a>Installazione e configurazione della soluzione
Usare le informazioni seguenti per installare e configurare la soluzione.

- Aggiungere la soluzione di valutazione dell'aggiornamento del sistema all'area di lavoro OMS usando la procedura descritta nell'articolo sull' [aggiunta di soluzioni di Log Analytics dalla raccolta soluzioni](log-analytics-add-solutions.md).  Non è richiesta alcuna ulteriore configurazione.

## <a name="system-update-data-collection-details"></a>Informazioni dettagliate sulla raccolta dei dati di aggiornamento del sistema

System Update Assessment raccoglie metadati e dati dello stato tramite gli agenti abilitati.

La tabella seguente mostra i metodi di raccolta dei dati e altre informazioni dettagliate sul modo in cui vengono raccolti i dati per la valutazione dell'aggiornamento del sistema.

| piattaforma | Agente diretto | Agente SCOM | Archiviazione di Azure | SCOM obbligatorio? | Dati dell'agente SCOM inviati con il gruppo di gestione | frequenza della raccolta |
|---|---|---|---|---|---|---|
|Windows|![Sì](./media/log-analytics-system-update/oms-bullet-green.png)|![Sì](./media/log-analytics-system-update/oms-bullet-green.png)|![No](./media/log-analytics-system-update/oms-bullet-red.png)|            ![No](./media/log-analytics-system-update/oms-bullet-red.png)|![Sì](./media/log-analytics-system-update/oms-bullet-green.png)| Almeno 2 volte al giorno e 15 minuti dopo l'installazione di un aggiornamento|

La tabella seguente illustra esempi di tipi di dati raccolti da System Update Assessment:

|**Tipo di dati**|**Fields**|
|---|---|
|Metadata|BaseManagedEntityId, ObjectStatus, OrganizationalUnit, ActiveDirectoryObjectSid, PhysicalProcessors, NetworkName, IPAddress, ForestDNSName, NetbiosComputerName, VirtualMachineName, LastInventoryDate, HostServerNameIsVirtualMachine, IP Address, NetbiosDomainName, LogicalProcessors, DNSName, DisplayName, DomainDnsName, ActiveDirectorySite, PrincipalName, OffsetInMinuteFromGreenwichTime|
|Stato|StateChangeEventId, StateId, NewHealthState, OldHealthState, Context, TimeGenerated, TimeAdded, StateId2, BaseManagedEntityId, MonitorId, HealthState, LastModified, LastGreenAlertGenerated, DatabaseTimeModified|


### <a name="to-work-with-updates"></a>Per usare gli aggiornamenti

1. Nella pagina **Panoramica** fare clic sul riquadro **Valutazione aggiornamento del sistema**.  
    ![Riquadro Valutazione aggiornamento del sistema](./media/log-analytics-system-update/sys-update-tile.png)
2. Nel dashboard **Updates** visualizzare le categorie degli aggiornamenti.  
    ![Dashboard Aggiornamenti](./media/log-analytics-system-update/sys-updates02.png)
3. Scorrere la pagina verso destra per visualizzare il pannello **Aggiornamenti critici o della sicurezza di Windows** e in **Classificazione** fare clic su **Aggiornamenti della sicurezza**.  
    ![Aggiornamenti della sicurezza](./media/log-analytics-system-update/sys-updates03.png)
4. Nella pagina Ricerca log vengono visualizzate varie informazioni relative agli aggiornamenti di sicurezza mancanti dai server dell'infrastruttura. Fare clic su **Elenco** per visualizzare le informazioni dettagliate sugli aggiornamenti.  
    ![Risultati della ricerca - Elenco](./media/log-analytics-system-update/sys-updates04.png)
5. Nella pagina Ricerca log vengono visualizzate informazioni dettagliate su ogni aggiornamento. Accanto a numero KBID, fare clic su **Visualizza** per visualizzare l'articolo corrispondente sul sito Web del supporto tecnico Microsoft.  
    ![Risultati di Ricerca log - KB visualizzati](./media/log-analytics-system-update/sys-updates05.png)
6. Il browser Web apre la pagina Web del supporto tecnico Microsoft per l'aggiornamento in una nuova scheda. Visualizzare le informazioni sull'aggiornamento mancante.  
    ![Pagina Web del supporto tecnico Microsoft](./media/log-analytics-system-update/sys-updates06.png)
7. Con le informazioni ottenute è possibile creare un piano per applicare manualmente l'aggiornamento mancante, oppure si può continuare con i passaggi rimanenti per l'applicazione automatica dell'aggiornamento.
8. Se si desidera applicare automaticamente l'aggiornamento mancante, tornare al dashboard **Aggiornamenti** e in **Operazioni di aggiornamento** fare clic su **Fare clic per pianificare un'operazione di aggiornamento**.  
    ![Operazioni di aggiornamento - Scheda Pianificate](./media/log-analytics-system-update/sys-updates07.png)
9. Nella pagina **Operazioni di aggiornamento** della scheda **Pianificate**, fare clic su **Aggiungi** per creare una nuova operazione di aggiornamento.  
    ![Scheda Pianificate - Aggiungere](./media/log-analytics-system-update/sys-updates08.png)
10. Nella pagina **Nuova operazione di aggiornamento**, immettere un nome per l'operazione di aggiornamento, aggiungere singoli computer o gruppi di computer, definire una pianificazione e fare clic su **Salva**.  
    ![Nuova operazione di aggiornamento](./media/log-analytics-system-update/sys-updates09.png)
11. La scheda **Pianificate** della pagina **Operazioni di aggiornamento** mostra la nuova operazione di aggiornamento pianificata.  
    ![Scheda Pianificate](./media/log-analytics-system-update/sys-updates10.png)
12. Quando si avvia l'operazione di aggiornamento, le relative informazioni verranno visualizzate nella scheda **In esecuzione**.  
    ![Scheda In esecuzione](./media/log-analytics-system-update/sys-updates11.png)
13. Al termine dell'operazione di aggiornamento, lo stato viene visualizzato nella scheda **Completate**.
14. Se gli aggiornamenti sono stati applicati dall'operazione di aggiornamento, nel pannello **Aggiornamenti critici o della sicurezza Windows** verrà indicato un numero di aggiornamenti più basso.  
    ![Pannello Aggiornamenti critici o della sicurezza Windows: conteggio aggiornamenti ridotto](./media/log-analytics-system-update/sys-updates12.png)



## <a name="next-steps"></a>Passaggi successivi

- [Eseguire ricerche nei log](log-analytics-log-searches.md) per visualizzare dati dettagliati di aggiornamento del sistema.



<!--HONumber=Oct16_HO2-->


