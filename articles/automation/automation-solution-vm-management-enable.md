---
title: Abilitare la funzionalità Avvio/Arresto di macchine virtuali durante gli orari di minore attività con Automazione di Azure
description: Questo articolo descrive come abilitare la funzionalità Avvio/Arresto di macchine virtuali durante gli orari di minore attività per le macchine virtuali di Azure.
services: automation
ms.subservice: process-automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: dde2c3e4cf496bb15ca91c72d9a41936af7051c5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "83743747"
---
# <a name="enable-startstop-vms-during-off-hours"></a>Abilitare Avvio/Arresto di macchine virtuali durante gli orari di minore attività

Eseguire in sequenza i passaggi descritti in questo argomento per abilitare la funzionalità Avvio/Arresto di macchine virtuali durante gli orari di minore attività usando un account di Automazione nuovo o esistente e un area di lavoro Log Analytics collegata. Al termine del processo di installazione, configurare le variabili per personalizzare la funzionalità.

>[!NOTE]
>Per usare questa funzionalità con le VM classiche, è necessario un account RunAs classico, che non viene creato per impostazione predefinita. Vedere [Creare un account RunAs classico](automation-create-standalone-account.md#create-a-classic-run-as-account).
>

## <a name="create-resources-for-the-feature"></a>Creare le risorse per la funzionalità

1. Accedere al [portale](https://portal.azure.com) di Azure.
2. Cercare e selezionare **Account di Automazione**.
3. Nella pagina Account di automazione selezionare l'account di Automazione dall'elenco.
4. Dall'account di Automazione selezionare **Avvio/Arresto di macchine virtuali** in **Risorse correlate**. Da qui è possibile fare clic su **Learn more about and enable the solution** (Scopri di più e abilita la soluzione). Se la funzionalità è già stata distribuita, è possibile fare clic su **Gestisci la soluzione** e trovarla nell'elenco.

   ![Abilitare dall'account di automazione](./media/automation-solution-vm-management/enable-from-automation-account.png)

   > [!NOTE]
   > È anche possibile creare la risorsa da qualsiasi posizione del portale di Azure, facendo clic su **Crea una risorsa**. Nella pagina del Marketplace digitare una parola chiave, ad esempio **Avvio** o **Avvio/Arresto**. Non appena si inizia a digitare, l'elenco viene filtrato in base all'input. In alternativa, è possibile digitare una o più parole chiave contenute nel nome completo della funzionalità e quindi premere **INVIO**. Selezionare **Avvio/Arresto di macchine virtuali durante gli orari di minore attività** dai risultati della ricerca.

5. Nella pagina Avvio/Arresto di macchine virtuali durante gli orari di minore attività per la distribuzione selezionata esaminare le informazioni riepilogative e quindi fare clic su **Crea**.

   ![Portale di Azure](media/automation-solution-vm-management/azure-portal-01.png)

## <a name="configure-the-feature"></a>Configurare la funzionalità

Una volta creata la risorsa, viene visualizzata la pagina Aggiungi soluzione. Viene chiesto di configurare la funzionalità prima di importarla nella sottoscrizione di Automazione. Vedere [Configurare Avvio/Arresto di macchine virtuali durante gli orari di minore attività](automation-solution-vm-management-config.md).

   ![Pagina Aggiungi soluzioni per la gestione di VM](media/automation-solution-vm-management/azure-portal-add-solution-01.png)

## <a name="select-a-log-analytics-workspace"></a>Selezionare un'area di lavoro Log Analytics

1. Nella pagina Aggiungi soluzione selezionare **Area di lavoro**. Selezionare un'area di lavoro Log Analytics collegata alla sottoscrizione usata dall'account di Automazione. 

2. Se non è disponibile un'area di lavoro, selezionare **Crea una nuova area di lavoro**. Nella pagina Area di lavoro Log Analytics seguire questa procedura:

   - Specificare un nome per la nuova area di lavoro Log Analytics, ad esempio **ContosoLAWorkspace**.
   - Nell'elenco a discesa **Sottoscrizione** selezionare una sottoscrizione da collegare, se quella predefinita non è appropriata.
   - Per il **gruppo di risorse**, è possibile selezionare un gruppo di risorse esistente o crearne uno nuovo.
   - Selezionare un **percorso**.
   - Selezionare un **Piano tariffario**. Scegliere l'opzione **Per GB (autonomo)** . I [prezzi](https://azure.microsoft.com/pricing/details/log-analytics/) dei log di Monitoraggio di Azure sono stati aggiornati e il livello Per GB è l'unica opzione disponibile.

   > [!NOTE]
   > Quando si abilitano le funzionalità, sono supportate solo determinate aree per il collegamento di un'area di lavoro Log Analytics e un account di Automazione. Per un elenco delle coppie di mapping supportate, vedere [Mapping delle aree per l'account di Automazione e l'area di lavoro Log Analytics](how-to/region-mappings.md).

3. Dopo aver specificato le informazioni necessarie nella pagina Area di lavoro Log Analytics, fare clic su **Crea**. È possibile monitorarne lo stato scegliendo **Notifiche** dal menu. Al termine, si tornerà nella pagina Aggiungi soluzione.

## <a name="add-automation-account"></a>Aggiungi account di Automazione

Accedere di nuovo alla pagina Aggiungi soluzione selezionare **Account di Automazione**. È possibile selezionare un account di Automazione esistente che non sia già collegato a un'area di lavoro Log Analytics. Se si crea una nuova area di lavoro Log Analytics, è possibile creare un nuovo account di Automazione da associare. Selezionare un account di Automazione esistente o fare clic su **Crea un account di Automazione** e specificare il nome dell'account di Automazione nel campo **Nome** della pagina Aggiungi account di Automazione.

Tutte le altre opzioni vengono popolate automaticamente in base all'area di lavoro Log Analytics selezionata e non è possibile modificarle. L'account RunAs è il metodo di autenticazione predefinito per i runbook inclusi nella funzionalità. 

Dopo aver fatto clic su **OK**, le opzioni di configurazione vengono convalidate e viene creato l'account di Automazione. Per tenere traccia dello stato di avanzamento, è possibile usare la voce **Notifiche** nel menu.

## <a name="define-feature-parameters"></a>Definire i parametri della funzionalità

1. Nella pagina Aggiungi soluzione selezionare **Configurazione**. Viene visualizzata la pagina Parametri.

    ![Pagina dei parametri per la soluzione](media/automation-solution-vm-management/azure-portal-add-solution-02.png)

2. Specificare un valore per il campo **Target ResourceGroup Names** (Nomi dei gruppi di risorse di destinazione). Il campo definisce i nomi dei gruppi contenenti le macchine virtuali che dovranno essere gestite dalla funzionalità. È possibile immettere più nomi separati da virgole senza distinzione tra maiuscole e minuscole. Per specificare come destinazione le macchine virtuali in tutti i gruppi di risorse della sottoscrizione, è possibile usare un carattere jolly. I valori vengono archiviati nelle variabili `External_Start_ResourceGroupNames` e `External_Stop_ResourceGroupNames`.

    > [!IMPORTANT]
    > Il valore predefinito per i **nomi dei gruppi di risorse di destinazione** è **&ast;** , destinato a tutte le macchine virtuali in una sottoscrizione. Per evitare che la funzionalità sia destinata a tutte le macchine virtuali nella sottoscrizione, è necessario specificare un elenco di nomi di gruppi di risorse prima di selezionare una pianificazione.
  
3. Specificare un valore per il campo **VM Exclude List (string)** (Elenco di esclusione VM). Questo valore è il nome di una o più macchine virtuali del gruppo di risorse di destinazione. È possibile immettere più nomi separati da virgole senza distinzione tra maiuscole e minuscole. I caratteri jolly sono supportati. Questo valore viene archiviato nella variabile `External_ExcludeVMNames`.
  
4. Usare il campo **Schedule** (Pianificazione) per selezionare una pianificazione per la gestione delle VM da parte della funzionalità. Selezionare una data e un'ora di inizio per creare una pianificazione giornaliera ricorrente a partire dall'ora scelta. Non è possibile selezionare un'altra area. Per configurare la pianificazione per un fuso orario specifico dopo aver configurato la funzionalità, vedere [Modificare le pianificazioni di avvio e arresto](automation-solution-vm-management-config.md#modify-the-startup-and-shutdown-schedules).

5. Per ricevere notifiche tramite posta elettronica da un [gruppo di azioni](../azure-monitor/platform/action-groups.md), accettare il valore predefinito **Sì** e specificare un indirizzo di posta elettronica valido nel campo **Notifiche di posta elettronica**. Se si seleziona **No** ma in un secondo momento si decide di voler ricevere notifiche tramite posta elettronica, è possibile aggiornare il gruppo di azioni che viene creato con gli indirizzi di posta elettronica validi separati da virgole. 

6. Abilitare le regole di avviso seguenti:

   - `AutoStop_VM_Child`
   - `Scheduled_StartStop_Parent`
   - `Sequenced_StartStop_Parent`

## <a name="create-alerts"></a>Creare avvisi

La funzionalità Avvio/Arresto di macchine virtuali durante gli orari di minore attività non include un set predefinito di avvisi. Vedere [Creare avvisi dei log con Monitoraggio di Azure](../azure-monitor/platform/alerts-log.md) per informazioni su come creare avvisi per processi non riusciti per supportare le procedure e i processi operativi o DevOps.

## <a name="deploy-the-feature"></a>Distribuire la funzionalità

1. Dopo aver configurato le impostazioni iniziali necessarie per la funzionalità, fare clic su **OK** per chiudere la pagina Parametri.

2. Fare clic su **Crea**. Dopo la convalida di tutte le impostazioni, la funzionalità viene distribuita nella sottoscrizione. Questo processo può richiedere alcuni secondi. Per tenere traccia dello stato di avanzamento, è possibile usare la voce **Notifiche** nel menu.

    > [!NOTE]
    > Se si ha una sottoscrizione di Azure Cloud Solution Provider (Azure CSP), una volta completata la distribuzione passare a **Variabili** in **Risorse condivise** nell'account di Automazione, quindi impostare la variabile [External_EnableClassicVMs](automation-solution-vm-management.md#variables) su **False**. In questo modo, si impedisce alla soluzione di cercare risorse della macchina virtuale classica.

## <a name="next-steps"></a>Passaggi successivi

* Per configurare la funzionalità, vedere [Configurare Avvio/Arresto di macchine virtuali durante gli orari di minore attività](automation-solution-vm-management-config.md).
* Per risolvere gli errori della funzionalità, vedere [Risolvere i problemi relativi ad Avvio/Arresto di macchine virtuali durante gli orari di minore attività](troubleshoot/start-stop-vm.md).