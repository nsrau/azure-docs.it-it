---
title: Abilitare la soluzione avvio/arresto di automazione di Azure per le macchine virtuali durante le ore di
description: Questo articolo descrive come abilitare la soluzione di avvio/arresto di automazione di Azure per le macchine virtuali di Azure.
services: automation
ms.subservice: process-automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: d47daa29c65f847fdeb33b9e24a892ac1f31b52a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82096963"
---
# <a name="enable-azure-automation-startstop-vms-solution"></a>Abilitare la soluzione di avvio/arresto delle macchine virtuali di automazione di Azure

Eseguire la procedura seguente per aggiungere la soluzione **avvio/arresto di macchine virtuali durante gli orari di minore attività** a un account di automazione nuovo o esistente e a un'area di lavoro log Analytics collegata. Dopo aver completato il processo di onboarding, configurare le variabili per personalizzare la soluzione.

>[!NOTE]
>Per usare questa soluzione con le macchine virtuali classiche, è necessario un account RunAs classico, che non viene creato per impostazione predefinita. Per istruzioni sulla creazione di un account RunAs classico, vedere [creare un account RunAs classico](automation-create-standalone-account.md#create-a-classic-run-as-account).
>

## <a name="enable-solution"></a>Abilitare la soluzione

1. Accedere al [portale](https://portal.azure.com)di Azure.

2. Cercare e selezionare gli **account di automazione**.

3. Nella pagina account di automazione selezionare l'account di automazione dall'elenco.

4. Dall'account di automazione selezionare **Avvia/Arresta macchina virtuale** in **risorse correlate**. Da qui è possibile fare clic su **Learn more about and enable the solution** (Scopri di più e abilita la soluzione). Se si è già distribuita una soluzione Avvio/Arresto di macchine virtuali, è possibile selezionarla facendo clic su **Gestisci la soluzione** e cercarla nell'elenco.

   ![Abilitare dall'account di automazione](./media/automation-solution-vm-management/enable-from-automation-account.png)

   > [!NOTE]
   > È possibile anche crearla da qualsiasi posizione nel portale di Azure, facendo clic su **Crea una risorsa**. Nella pagina del Marketplace digitare una parola chiave, ad esempio **Avvio** o **Avvio/Arresto**. Non appena si inizia a digitare, l'elenco viene filtrato in base all'input. In alternativa, è possibile digitare una o più parole chiave contenute nel nome completo della soluzione e quindi premere INVIO. Selezionare **Avvio/Arresto di macchine virtuali durante gli orari di minore attività** dai risultati della ricerca.

5. Nella pagina **Avvio/Arresto di macchine virtuali durante gli orari di minore attività** per la soluzione selezionata esaminare le informazioni di riepilogo e quindi fare clic su **Crea**.

   ![Portale di Azure](media/automation-solution-vm-management/azure-portal-01.png)

6. Viene visualizzata la pagina Aggiungi soluzione. Viene richiesto di configurare la soluzione prima che sia possibile importarla nella sottoscrizione di automazione.

   ![Pagina Aggiungi soluzione di Virtual Machine Management](media/automation-solution-vm-management/azure-portal-add-solution-01.png)

7. Nella pagina Aggiungi soluzione selezionare **Area di lavoro**. Selezionare un'area di lavoro Log Analytics collegata alla stessa sottoscrizione di Azure in cui è incluso l'account di Automazione. Se non è disponibile un'area di lavoro, selezionare **Crea una nuova area di lavoro**. Nella pagina area di lavoro Log Analytics seguire questa procedura:

   - Specificare un nome per la nuova area di lavoro Log Analytics, ad esempio **ContosoLAWorkspace**.
   - Selezionare una **sottoscrizione** a cui collegarsi selezionando l'elenco a discesa, se il valore predefinito selezionato non è appropriato.
   - Per il **gruppo di risorse**, è possibile selezionare un gruppo di risorse esistente o crearne uno nuovo.
   - Selezionare una **località**.
   - Selezionare un piano **tariffario**. Scegliere l'opzione **Per GB (autonomo)**. I log di monitoraggio di Azure hanno aggiornato i [prezzi](https://azure.microsoft.com/pricing/details/log-analytics/) e il livello per GB è l'unica opzione.

   > [!NOTE]
   > Quando si abilitano le soluzioni, sono supportate solo alcune aree per collegare un'area di lavoro Log Analytics e un account di automazione.
   >
   > Per un elenco delle coppie di mapping supportate, vedere [mapping delle aree per l'account di automazione e l'area di lavoro log Analytics](how-to/region-mappings.md).

8. Dopo aver specificato le informazioni necessarie nella pagina area di lavoro Log Analytics, fare clic su **Crea**. È possibile monitorarne lo stato scegliendo **Notifiche** dal menu. Al termine, si tornerà alla pagina Aggiungi soluzione.

9. Nella pagina Aggiungi soluzione selezionare **Account di Automazione**. Se si sta creando una nuova area di lavoro Log Analytics, è possibile creare un nuovo account di automazione da associare o selezionare un account di automazione esistente che non sia già collegato a un'area di lavoro di Log Analytics. Selezionare un account di automazione esistente o fare clic su **Crea un account di automazione**e nella pagina Aggiungi account di automazione specificare le informazioni seguenti:
 
   - Nel campo **Nome** immettere il nome dell'account di Automazione.

     Tutte le altre opzioni vengono popolate automaticamente in base all'area di lavoro Log Analytics selezionata. Queste opzioni non possono essere modificate. Il metodo di autenticazione predefinito per i runbook inclusi nella soluzione è un account RunAs di Azure. Dopo aver fatto clic su **OK**, le opzioni di configurazione vengono convalidate e viene creato l'account di Automazione. Per tenere traccia dello stato di avanzamento, è possibile usare la voce **Notifiche** nel menu.

10. Nella pagina Aggiungi soluzione selezionare infine **Configurazione**. Viene visualizzata la pagina Parametri.

    ![Pagina dei parametri per la soluzione](media/automation-solution-vm-management/azure-portal-add-solution-02.png)

   In questo caso, viene chiesto di:
  
   - Specificare i **nomi dei gruppi di risorse di destinazione**. Questi valori sono nomi di gruppi di risorse contenenti le macchine virtuali che devono essere gestite da questa soluzione. È possibile immettere più nomi, separati da una virgola. I valori non fanno distinzione tra maiuscole e minuscole. Per specificare come destinazione le macchine virtuali in tutti i gruppi di risorse della sottoscrizione, è possibile usare un carattere jolly. Questo valore viene archiviato nelle variabili **External_Start_ResourceGroupNames** ed **External_Stop_ResourceGroupnames**.
  
   - Specificare l'**elenco di esclusione delle VM (stringa)**. Questo valore è il nome di una o più macchine virtuali del gruppo di risorse di destinazione. È possibile immettere più nomi, separati da una virgola. I valori non fanno distinzione tra maiuscole e minuscole. I caratteri jolly sono supportati. Questo valore viene archiviato nella variabile **External_ExcludeVMNames**.
  
   - Selezionare una **pianificazione**. Selezionare una data e un'ora per la pianificazione. Verrà creata una pianificazione giornaliera ricorrente a partire dall'ora selezionata. Non è possibile selezionare un'altra area. Per configurare la pianificazione per il fuso orario specifico dopo aver configurato la soluzione, vedere [Modifica della pianificazione di avvio e arresto](automation-solution-vm-management-config.md#modify-the-startup-and-shutdown-schedules).
  
   - Per ricevere **notifiche tramite posta elettronica** da un gruppo di azione, accettare il valore predefinito **Sì** e fornire un indirizzo di posta elettronica valido. Se si seleziona **No** ma in un secondo momento si decide che si desidera ricevere notifiche tramite posta elettronica, è possibile aggiornare il [gruppo di azioni](../azure-monitor/platform/action-groups.md) che viene creato con gli indirizzi di posta elettronica validi separati da una virgola. È anche necessario abilitare le regole di avviso seguenti:

     - AutoStop_VM_Child
     - Scheduled_StartStop_Parent
     - Sequenced_StartStop_Parent

     > [!IMPORTANT]
     > Il valore predefinito per i **nomi dei gruppi di risorse di destinazione** è **&ast;**, destinato a tutte le macchine virtuali in una sottoscrizione. Se non si vuole che la soluzione interessi tutte le macchine virtuali nella sottoscrizione, questo valore deve essere aggiornato con un elenco di nomi di gruppi di risorse prima di abilitare le pianificazioni.

11. Dopo aver configurato le impostazioni iniziali necessarie per la soluzione, fare clic su **OK** per chiudere la pagina Parametri e selezionare **Crea**. 

Dopo la convalida di tutte le impostazioni, la soluzione viene distribuita nella sottoscrizione. Questo processo può richiedere alcuni secondi. Per tenere traccia dello stato di avanzamento, è possibile usare la voce **Notifiche** nel menu.

> [!NOTE]
> Se si dispone di una sottoscrizione di Azure Cloud Solution Provider (Azure CSP), al termine della distribuzione, nell'account di automazione passare a **variabili** in **risorse condivise** e impostare la variabile [External_EnableClassicVMs](automation-solution-vm-management.md#variables) su **false**. In questo modo, si impedisce alla soluzione di cercare risorse della macchina virtuale classica.

## <a name="next-steps"></a>Passaggi successivi

Ora che la soluzione è abilitata, è possibile [configurarla](automation-solution-vm-management-config.md) per supportare i requisiti di gestione delle macchine virtuali.