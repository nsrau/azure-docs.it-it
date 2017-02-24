---
title: "Soluzione Avvio/Arresto di macchine virtuali durante gli orari di minore attività (anteprima) | Documentazione Microsoft"
description: La soluzione Virtual Machine Management permette di avviare e arrestare le macchine virtuali di Azure Resource Manager in base a una pianificazione e di monitorare in modo attivo tramite Log Analytics.
services: automation
documentationCenter: 
authors: mgoedtel
manager: carmonm
editor: 
ms.assetid: 06c27f72-ac4c-4923-90a6-21f46db21883
ms.service: automation
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2017
ms.author: magoedte
translationtype: Human Translation
ms.sourcegitcommit: 5ae60cb8ba3d391d3babd1ab575b4f32e139a185
ms.openlocfilehash: f2c9a5ef2a8f517b9b2072be57f4d8c51b7694c6

---

# <a name="startstop-vms-during-off-hours-preview-solution-in-automation"></a>Soluzione Avvio/Arresto di macchine virtuali durante gli orari di minore attività (anteprima) in Automazione di Azure

La soluzione Avvio/Arresto di macchine virtuali durante gli orari di minore attività (anteprima) permette di avviare e arrestare le macchine virtuali di Azure Resource Manager in base a una pianificazione definita dall'utente e, tramite OMS Log Analytics, fornisce informazioni approfondite sui processi di Automazione completati che avviano e arrestano le macchine virtuali.  

## <a name="prerequisites"></a>Prerequisiti

- I runbook funzionano con un [account RunAs di Azure](automation-sec-configure-azure-runas-account.md).  L'account RunAs è il metodo di autenticazione preferito perché usa l'autenticazione del certificato anziché una password, che può scadere o essere modificata di frequente.  

- Questa soluzione può gestire solo le macchine virtuali che si trovano nella stessa sottoscrizione e nello stesso gruppo di risorse in cui risiede l'account di Automazione.  

- È possibile eseguire la distribuzione solo nelle aree di Azure seguenti: Australia sud-orientale, Stati Uniti orientali, Asia sud-orientale ed Europa occidentale.  I runbook che gestiscono la pianificazione delle macchine virtuali possono essere usati in qualsiasi area.  

- Per l'invio di notifiche tramite posta elettronica al termine dell'esecuzione dei runbook di avvio e arresto delle macchine virtuali, è necessaria una sottoscrizione di Office 365 di classe enterprise.  

## <a name="solution-components"></a>Componenti della soluzione

Questa soluzione è costituita dalle risorse riportate di seguito, che verranno importate e aggiunte all'account di Automazione.

### <a name="runbooks"></a>Runbook

Runbook | Descrizione|
--------|------------|
CleanSolution-MS-Mgmt-VM | Questo runbook rimuove tutte le risorse contenute e pianifica quando sarà necessario eliminare la soluzione dalla sottoscrizione.|  
SendMailO365-MS-Mgmt | Questo runbook invia un messaggio di posta elettronica tramite Office 365 Exchange.|
StartByResourceGroup-MS-Mgmt-VM | Questo runbook avvia le macchine virtuali, sia quelle classiche che quelle basate su Azure Resource Manager, che si trovano in un determinato elenco di gruppi di risorse di Azure.
StopByResourceGroup-MS-Mgmt-VM | Questo runbook arresta le macchine virtuali, sia quelle classiche che quelle basate su Azure Resource Manager, che si trovano in un determinato elenco di gruppi di risorse di Azure.|
<br>

### <a name="variables"></a>Variabili

Variabile | Descrizione|
---------|------------|
Runbook **SendMailO365-MS-Mgmt** ||
SendMailO365-IsSendEmail-MS-Mgmt | Specifica se i runbook StartByResourceGroup-MS-Mgmt-VM e StopByResourceGroup-MS-Mgmt-VM possono inviare notifiche tramite posta elettronica al termine dell'operazione.  Scegliere **True **per abilitare e **False** per disabilitare gli avvisi tramite posta elettronica. Il valore predefinito è **False**.| 
Runbook **StartByResourceGroup-MS-Mgmt-VM** ||
StartByResourceGroup-ExcludeList-MS-Mgmt-VM | Immettere i nomi di macchina virtuale da escludere dall'operazione di gestione e separarli usando un punto e virgola (;). I valori fanno distinzione tra maiuscole e minuscole ed è supportato il carattere jolly asterisco.|
StartByResourceGroup-SendMailO365-EmailBodyPreFix-MS-Mgmt | Testo che è possibile aggiungere all'inizio del corpo del messaggio di posta elettronica.|
StartByResourceGroup-SendMailO365-EmailRunBookAccount-MS-Mgmt | Specifica il nome dell'account di Automazione che contiene il runbook di posta elettronica.  **Non modificare questa variabile.**|
StartByResourceGroup-SendMailO365-EmailRunbookName-MS-Mgmt | Specifica il nome del runbook di posta elettronica.  Questa variabile viene usata dai runbook StartByResourceGroup-MS-Mgmt-VM e StopByResourceGroup-MS-Mgmt-VM per inviare messaggi di posta elettronica.  **Non modificare questa variabile.**|
StartByResourceGroup-SendMailO365-EmailRunbookResourceGroup-MS-Mgmt | Specifica il nome del gruppo di risorse che contiene il runbook di posta elettronica.  **Non modificare questa variabile.**|
StartByResourceGroup-SendMailO365-EmailSubject-MS-Mgmt | Specifica il testo per la riga dell'oggetto del messaggio di posta elettronica.|  
StartByResourceGroup-SendMailO365-EmailToAddress-MS-Mgmt | Specifica i destinatari del messaggio di posta elettronica.  Immettere i nomi separati da un punto e virgola (;).|
StartByResourceGroup-TargetResourceGroups-MS-Mgmt-VM | Immettere i nomi di macchina virtuale da escludere dall'operazione di gestione e separarli usando un punto e virgola (;). I valori fanno distinzione tra maiuscole e minuscole ed è supportato il carattere jolly asterisco.  Il valore predefinito (asterisco) permette di includere tutti i gruppi di risorse nella sottoscrizione.|
StartByResourceGroup-TargetSubscriptionID-MS-Mgmt-VM | Specifica la sottoscrizione che contiene le macchine virtuali che la soluzione deve gestire.  Deve trattarsi della stessa sottoscrizione in cui risiede l'account di Automazione della soluzione.|
Runbook **StopByResourceGroup-MS-Mgmt-VM** ||
StopByResourceGroup-ExcludeList-MS-Mgmt-VM | Immettere i nomi di macchina virtuale da escludere dall'operazione di gestione e separarli usando un punto e virgola (;). I valori fanno distinzione tra maiuscole e minuscole ed è supportato il carattere jolly asterisco.|
StopByResourceGroup-SendMailO365-EmailBodyPreFix-MS-Mgmt | Testo che è possibile aggiungere all'inizio del corpo del messaggio di posta elettronica.|
StopByResourceGroup-SendMailO365-EmailRunBookAccount-MS-Mgmt | Specifica il nome dell'account di Automazione che contiene il runbook di posta elettronica.  **Non modificare questa variabile.**|
StopByResourceGroup-SendMailO365-EmailRunbookResourceGroup-MS-Mgmt | Specifica il nome del gruppo di risorse che contiene il runbook di posta elettronica.  **Non modificare questa variabile.**|
StopByResourceGroup-SendMailO365-EmailSubject-MS-Mgmt | Specifica il testo per la riga dell'oggetto del messaggio di posta elettronica.|  
StopByResourceGroup-SendMailO365-EmailToAddress-MS-Mgmt | Specifica i destinatari del messaggio di posta elettronica.  Immettere i nomi separati da un punto e virgola (;).|
StopByResourceGroup-TargetResourceGroups-MS-Mgmt-VM | Immettere i nomi di macchina virtuale da escludere dall'operazione di gestione e separarli usando un punto e virgola (;). I valori fanno distinzione tra maiuscole e minuscole ed è supportato il carattere jolly asterisco.  Il valore predefinito (asterisco) permette di includere tutti i gruppi di risorse nella sottoscrizione.|
StopByResourceGroup-TargetSubscriptionID-MS-Mgmt-VM | Specifica la sottoscrizione che contiene le macchine virtuali che la soluzione deve gestire.  Deve trattarsi della stessa sottoscrizione in cui risiede l'account di Automazione della soluzione.|  
<br>

### <a name="schedules"></a>Pianificazioni

Pianificazione | Descrizione|
---------|------------|
StartByResourceGroup-Schedule-MS-Mgmt | Permette di pianificare il runbook StartByResourceGroup, che esegue l'avvio delle macchine virtuali gestite dalla soluzione. L'impostazione predefinita al momento della creazione è il fuso orario UTC.|
StopByResourceGroup-Schedule-MS-Mgmt | Permette di pianificare il runbook StopByResourceGroup, che esegue l'arresto delle macchine virtuali gestite dalla soluzione. L'impostazione predefinita al momento della creazione è il fuso orario UTC.|

### <a name="credentials"></a>Credenziali

Credenziali | Descrizione|
-----------|------------|
O365Credential | Specifica un account utente di Office 365 valido per l'invio di posta elettronica.  Sono obbligatorie solo se la variabile SendMailO365-IsSendEmail-MS-Mgmt è impostata su **True**.

## <a name="configuration"></a>Configurazione

Seguire questa procedura per aggiungere la soluzione Avvio/Arresto di macchine virtuali durante gli orari di minore attività (anteprima) all'account di Automazione e quindi configurare le variabili per personalizzarla.

1. Nella schermata iniziale del portale di Azure selezionare il riquadro **Marketplace**.  Se il riquadro non è aggiunto alla schermata iniziale, selezionare **Nuovo** nel riquadro di spostamento a sinistra.  
2. Nel pannello Marketplace digitare **Avvia macchina virtuale** nella casella di ricerca e quindi selezionare la soluzione **Start/Stop VMs during off-hours [Preview]** (Avvio/Arresto di macchine virtuali durante gli orari di minore attività - anteprima) tra i risultati della ricerca.  
3. Nel pannello **Start/Stop VMs during off-hours [Preview]** (Avvio/Arresto di macchine virtuali durante gli orari di minore attività - anteprima) per la soluzione selezionata, esaminare le informazioni di riepilogo e quindi fare clic su **Crea**.  
4. Verrà visualizzato il pannello **Aggiungi soluzione**, in cui viene richiesto di configurare la soluzione prima di importarla nella sottoscrizione di Automazione.<br><br> ![Pannello Aggiungi soluzione di Virtual Machine Management](media/automation-solution-vm-management/vm-management-solution-add-solution-blade.png)<br><br>
5.  Nel pannello **Aggiungi soluzione** selezionare **Area di lavoro**. Qui è possibile selezionare un'area di lavoro OMS collegata alla stessa sottoscrizione di Azure in cui si trova l'account di Automazione o creare una nuova area di lavoro OMS.  Se non è disponibile un'area di lavoro OMS, è possibile selezionare **Crea nuova area di lavoro** e seguire questa procedura nel pannello **Area di lavoro OMS**: 
   - Specificare un nome per la nuova **area di lavoro OMS**.
   - Selezionare una **sottoscrizione** a cui collegarsi. Se la sottoscrizione selezionata per impostazione predefinita non è appropriata, è possibile sceglierne una dall'elenco a discesa.
   - Per il **gruppo di risorse**, è possibile selezionare un gruppo di risorse esistente o crearne uno nuovo.  
   - Selezionare un **percorso**.  Le uniche località attualmente selezionabili sono l'**Australia sud-orientale**, gli **Stati Uniti orientali**, l'**Asia sud-orientale** e l'**Europa occidentale**.
   - Selezionare un **Piano tariffario**.  Per la soluzione sono disponibili due livelli, quello gratuito e il livello OMS a pagamento.  Il livello gratuito presenta un limite per la quantità di dati raccolti al giorno, il periodo di memorizzazione e i minuti di esecuzione dei processi dei runbook.  Il livello OMS a pagamento non ha limiti per la quantità di dati raccolti al giorno.  

        > [!NOTE]
        > Nonostante sia visualizzato come opzione, il livello a pagamento Autonomo non è applicabile.  Se si seleziona e si procede con la creazione della soluzione nella sottoscrizione, l'operazione avrà esito negativo.  Questo problema sarà risolto al momento del rilascio ufficiale della soluzione.<br>Se si usa la soluzione, questa usa solo l'inserimento di log e i minuti di esecuzione dei processi di automazione.  Non aggiunge altri nodi OMS all'ambiente in uso.  

6. Dopo aver specificato le informazioni necessarie nel pannello **Area di lavoro OMS**, fare clic su **Crea**.  Per tenere traccia dello stato di avanzamento della verifica delle informazioni e della creazione dell'area di lavoro, è possibile usare la voce **Notifiche** nel menu.  Verrà visualizzato nuovamente il pannello **Aggiungi soluzione**.  
7. Nel pannello **Aggiungi soluzione** selezionare **Account di Automazione**.  Se si sta creando una nuova area di lavoro OMS, viene richiesto di creare anche un nuovo account di Automazione da associare alla nuova area di lavoro OMS specificata in precedenza, nonché l'area, il gruppo di risorse e la sottoscrizione di Azure.  È possibile selezionare **Crea un account di Automazione** e specificare le informazioni seguenti nel pannello **Aggiungi account di Automazione**: 
  - Nel campo **Nome** immettere il nome dell'account di Automazione.

    Tutte le altre opzioni vengono popolate automaticamente in base all'area di lavoro OMS selezionata e non è possibile modificarle.  Il metodo di autenticazione predefinito per i runbook inclusi nella soluzione è un account RunAs di Azure.  Dopo aver fatto clic su **OK**, le opzioni di configurazione vengono convalidate e viene creato l'account di Automazione.  Per tenere traccia dello stato di avanzamento, è possibile usare la voce **Notifiche** nel menu. 

    In alternativa, è possibile selezionare un account RunAs di Automazione esistente.  Si noti che l'account selezionato non può essere già collegato a un'altra area di lavoro OMS, in caso contrario nel pannello verrà visualizzato un messaggio informativo.  Se è già collegato, è necessario selezionare un account RunAs di Automazione diverso o crearne uno nuovo.<br><br> ![Account di Automazione già collegato all'area di lavoro OMS](media/automation-solution-vm-management/vm-management-solution-add-solution-blade-autoacct-warning.png)<br>

8. Infine, nel pannello **Aggiungi soluzione** selezionare **Configurazione**. Verrà visualizzato il pannello **Parametri**.  Nel pannello **Parametri** viene richiesto di:  
   - Specificare i **nomi dei gruppi di risorse di destinazione** contenenti le macchine virtuali che devono essere gestite dalla soluzione.  È possibile immettere più nomi, separati da un punto e virgola. I valori fanno distinzione tra maiuscole e minuscole.  Per specificare come destinazione le macchine virtuali in tutti i gruppi di risorse della sottoscrizione, è possibile usare un carattere jolly.
   - Selezionare una **pianificazione**, ovvero una data e un'ora ricorrenti per l'avvio e l'arresto delle macchine virtuali nei gruppi di risorse di destinazione.  Per impostazione predefinita, la pianificazione è configurata per il fuso orario UTC e non è prevista la selezione di un'area diversa.  Se si desidera configurare la pianificazione per il fuso orario specifico dopo aver configurato la soluzione, vedere la sezione seguente [Modifica della pianificazione di avvio e arresto](#modifying-the-startup-and-shutdown-schedule).    

10. Dopo aver configurato le impostazioni iniziali necessarie per la soluzione, selezionare **Crea**.  Tutte le impostazioni verranno convalidate e verrà eseguito un tentativo di distribuzione della soluzione nella sottoscrizione.  Questo processo può richiedere alcuni secondi. Per tenere traccia dello stato di avanzamento, è possibile usare la voce **Notifiche** nel menu. 

## <a name="collection-frequency"></a>Frequenza della raccolta

Il log del processo di Automazione e i dati del flusso del processo vengono inseriti nel repository OMS ogni cinque minuti.  

## <a name="using-the-solution"></a>Uso della soluzione

Quando si aggiunge la soluzione Virtual Machine Management all'area di lavoro OMS, il riquadro **StartStopVM View** (Visualizzazione di avvio e arresto di VM) viene aggiunto al dashboard OMS.  Questo riquadro mostra un conteggio e la rappresentazione grafica dei processi di runbook della soluzione che sono stati avviati e completati.<br><br> ![Riquadro StartStopVM View (Visualizzazione di avvio e arresto di VM) di Virtual Machine Management](media/automation-solution-vm-management/vm-management-solution-startstopvm-view-tile.png)  

Nell'account di Automazione è possibile accedere alla soluzione e gestirla selezionando il riquadro **Soluzioni** e scegliendo quindi la soluzione **Start-Stop-VM[Workspace]** (Avvio-Arresto-VM - Area di lavoro) dall'elenco nel pannello **Soluzioni**.<br><br> ![Elenco delle soluzioni di automazione](media/automation-solution-vm-management/vm-management-solution-autoaccount-solution-list.png)  

Verrà visualizzato il pannello della soluzione **Start-Stop-VM[Workspace]** (Avvio-Arresto-VM - Area di lavoro), in cui è possibile rivedere informazioni importanti come il riquadro **StartStopVM** (Avvio e arresto di VM) nell'area di lavoro OMS, contenente un conteggio e la rappresentazione grafica dei processi di runbook della soluzione che sono stati avviati e completati.<br><br> ![Pannello delle soluzioni di automazione delle macchine virtuali](media/automation-solution-vm-management/vm-management-solution-solution-blade.png)  

Da qui è anche possibile aprire l'area di lavoro OMS ed eseguire un'analisi aggiuntiva dei record dei processi.  È sufficiente fare clic su **Tutte le impostazioni** e quindi selezionare **Avvio rapido** nel pannello **Impostazioni** e infine **Portale di OMS** nel pannello **Avvio rapido**.   Verrà aperta una nuova scheda o una nuova sessione del browser e verrà visualizzata l'area di lavoro OMS associata alla sottoscrizione e all'account di Automazione.  


### <a name="configuring-e-mail-notifications"></a>Configurazione delle notifiche tramite posta elettronica

Per abilitare le notifiche tramite posta elettronica al termine dell'esecuzione dei runbook di avvio e arresto di macchine virtuali, è necessario modificare le credenziali **O365Credential** e almeno le variabili seguenti:

 - SendMailO365-IsSendEmail-MS-Mgmt
 - StartByResourceGroup-SendMailO365-EmailToAddress-MS-Mgmt
 - StopByResourceGroup-SendMailO365-EmailToAddress-MS-Mgmt

Per configurare le credenziali **O365Credential**, seguire questa procedura:

1. Dall'account di automazione fare clic su **Tutte le impostazioni** nella parte superiore della finestra. 
2. Nel pannello **Impostazioni** selezionare **Asset**nella sezione **Risorse di automazione**. 
3. Nel pannello **Asset** selezionare il riquadro **Credenziali** e nel pannello **Credenziali** selezionare **O365Credential**.  
4. Immettere un nome utente di Office 365 valido e la relativa password e quindi fare clic su **Salva** per salvare le modifiche.  

Per configurare le variabili evidenziate in precedenza, seguire questa procedura:

1. Dall'account di automazione fare clic su **Tutte le impostazioni** nella parte superiore della finestra. 
2. Nel pannello **Impostazioni** selezionare **Asset**nella sezione **Risorse di automazione**. 
3. Nel pannello **Asset** selezionare il riquadro **Variabili**. Nel pannello **Variabili** selezionare la variabile elencata in precedenza e modificarne il valore in base alla relativa descrizione specificata nella sezione [Variabili](##variables) di questo articolo.  
4. Fare clic su **Salva** per salvare le modifiche alla variabile.   

### <a name="modifying-the-startup-and-shutdown-schedule"></a>Modifica della pianificazione di avvio e arresto

Per gestire la pianificazione relativa all'avvio e all'arresto in questa soluzione viene seguita la stessa procedura illustrata in [Pianificazione di un runbook in Automazione di Azure](automation-schedules.md).  Tenere presente che non è possibile modificare la configurazione della pianificazione.  È necessario disabilitare la pianificazione esistente e crearne una nuova e quindi eseguire il collegamento al runbook **StartByResourceGroup-MS-Mgmt-VM** o **StopByResourceGroup-MS-Mgmt-VM** a cui si vuole applicare la pianificazione.   

## <a name="log-analytics-records"></a>Record di Log Analytics

Automazione crea due tipi di record nel repository OMS.

### <a name="job-logs"></a>Log di processo

Proprietà | Descrizione|
----------|----------|
Chiamante |  Chi ha avviato l'operazione.  I valori possibili sono un indirizzo di posta elettronica o il sistema per i processi pianificati.|
Categoria | La classificazione del tipo di dati.  Per Automazione, il valore è JobLogs.|
CorrelationId | Il GUID che rappresenta l'ID di correlazione del processo di runbook.|
JobId | Il GUID che rappresenta l'ID del processo del runbook.|
operationName | Specifica il tipo di operazione eseguita in Azure.  Per Automazione, il valore sarà Job.|
resourceId | Specifica il tipo di risorsa in Azure.  Per Automazione, il valore è l'account di Automazione associato al runbook.|
ResourceGroup | Specifica il nome del gruppo di risorse del processo del runbook.|
ResourceProvider | Specifica il servizio di Azure che fornisce le risorse da distribuire e gestire.  Per Automazione, il valore è Automazione di Azure.|
ResourceType | Specifica il tipo di risorsa in Azure.  Per Automazione, il valore è l'account di Automazione associato al runbook.|
resultType | Lo stato del processo di runbook.  I valori possibili sono:<br>- Avviato<br>- Interrotto<br>- Sospeso<br>- Non riuscito<br>- Completato|
resultDescription | Descrive lo stato del risultato del processo di runbook.  I valori possibili sono:<br>- Processo avviato<br>- Processo non riuscito<br>- Processo completato|
RunbookName | Specifica il nome del runbook.|
SourceSystem | Specifica il sistema di origine per i dati inviati.  Per Automazione, il valore è OpsManager.|
StreamType | Specifica il tipo di evento. I valori possibili sono:<br>- Dettagliato<br>- Output<br>- Errore<br>- Avviso|
SubscriptionId | Specifica l'ID sottoscrizione del processo.
Time | Data e ora di esecuzione del processo del runbook.|


### <a name="job-streams"></a>Flussi di processo

Proprietà | Descrizione|
----------|----------|
Chiamante |  Chi ha avviato l'operazione.  I valori possibili sono un indirizzo di posta elettronica o il sistema per i processi pianificati.|
Categoria | La classificazione del tipo di dati.  Per Automazione, il valore è JobStreams.|
JobId | Il GUID che rappresenta l'ID del processo del runbook.|
operationName | Specifica il tipo di operazione eseguita in Azure.  Per Automazione, il valore sarà Job.|
ResourceGroup | Specifica il nome del gruppo di risorse del processo del runbook.|
resourceId | Specifica l'ID risorsa in Azure.  Per Automazione, il valore è l'account di Automazione associato al runbook.|
ResourceProvider | Specifica il servizio di Azure che fornisce le risorse da distribuire e gestire.  Per Automazione, il valore è Automazione di Azure.|
ResourceType | Specifica il tipo di risorsa in Azure.  Per Automazione, il valore è l'account di Automazione associato al runbook.|
resultType | Risultato del processo del runbook al momento in cui è stato generato l'evento.  I valori possibili sono:<br>- InProgress|
resultDescription | Include il flusso di output dal runbook.|
RunbookName | Il nome del runbook.|
SourceSystem | Specifica il sistema di origine per i dati inviati.  Per Automazione, il valore è OpsManager.|
StreamType | Il tipo di flusso del processo. I valori possibili sono:<br>- Avanzamento<br>- Output<br>- Avviso<br>- Errore<br>- Debug<br>- Dettagliato|
Time | Data e ora di esecuzione del processo del runbook.|

Quando si esegue una ricerca di log che restituisce record di categoria di **JobLogs** o **JobStreams**, è possibile selezionare la visualizzazione **JobLogs** o **JobStreams** che presenta una serie di riquadri di riepilogo degli aggiornamenti restituiti dalla ricerca.

## <a name="sample-log-searches"></a>Ricerche di log di esempio

La tabella seguente contiene esempi di ricerche nei log per i record dei processi raccolti da questa soluzione. 

Query | Descrizione|
----------|----------|
Trova i processi completati per il runbook StartVM | Category=JobLogs RunbookName_s="StartByResourceGroup-MS-Mgmt-VM" ResultType=Succeeded &#124; measure count() by JobId_g|
Trova i processi completati per il runbook StopVM | Category=JobLogs RunbookName_s="StartByResourceGroup-MS-Mgmt-VM" ResultType=Failed &#124; measure count() by JobId_g
Visualizza lo stato dei processi nel tempo per i runbook StartVM e StopVM | Category=JobLogs RunbookName_s="StartByResourceGroup-MS-Mgmt-VM" OR "StopByResourceGroup-MS-Mgmt-VM" NOT(ResultType="started") | measure Count() by ResultType interval 1day|

## <a name="removing-the-solution"></a>Rimuovere la soluzione

Se non c'è più bisogno di usare la soluzione, è possibile eliminarla dall'account di Automazione.  Eliminando la soluzione verranno rimossi soltanto i runbook, mentre le pianificazioni e le variabili create quando è stata aggiunta la soluzione non saranno eliminate.  Tali asset dovranno essere eliminati manualmente se non vengono usati con altri runbook.  

Per eliminare la soluzione, attenersi alla procedura seguente:

1.  Dall'account di Automazione, selezionare il riquadro **Soluzioni**.  
2.  Nel pannello**Soluzioni** selezionare la soluzione **Start-Stop-VM[Workspace]**.  Nel pannello **VMManagementSolution[Workspace]** fare clic su **Elimina** dal menu.<br><br> ![Eliminare una soluzione di gestione di macchine virtuali](media/automation-solution-vm-management/vm-management-solution-delete.png)
3.  Nella finestra **Elimina soluzione** confermare di voler eliminare la soluzione.
4.  Per tenere traccia dello stato di avanzamento della verifica delle informazioni e della rimozione della soluzione, è possibile usare la voce **Notifiche** nel menu.  Quando l'operazione di rimozione della soluzione inizia, l'utente viene rimandato al pannello **VMManagementSolution[Workspace]**.  

L'account di Automazione e l'area di lavoro OMS non vengono eliminati.  Se non si desidera conservare l'area di lavoro OMS, è necessario eliminarla manualmente.  Tale operazione può essere eseguita anche dal portale di Azure.   Nella schermata iniziale del portale di Azure selezionare **Log Analytics** per aprire il pannello **Log Analytics** in cui selezionare l'area di lavoro e fare clic su **Elimina** nel menu del pannello impostazioni dell'area di lavoro.  
      
## <a name="next-steps"></a>Passaggi successivi

- Per alte informazioni su come creare query di ricerca diverso ed esaminare i log del processo di automazione con Log Analytics, vedere [Ricerche nei log in Log Analytics](../log-analytics/log-analytics-log-searches.md)
- Per maggiori informazioni sull'esecuzione dei runbook, su come monitorare i processi dei runbook e su altri dettagli tecnici, vedere come tenere traccia del processo di un runbook in [Esecuzione di runbook in Automazione di Azure](automation-runbook-execution.md)
- Per altre informazioni su Log Analytics di OMS e sulle origini di raccolta dati, vedere la [panoramica della raccolta dati di Archiviazione di Azure in Log Analytics](../log-analytics/log-analytics-azure-storage.md)






   




<!--HONumber=Feb17_HO3-->


