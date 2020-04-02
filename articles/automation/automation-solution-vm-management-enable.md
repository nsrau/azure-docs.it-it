---
title: Abilitare la soluzione Start/Stop VMs durante le ore non lavorativeEnable Start/Stop VMs during off hours solution
description: Questo articolo descrive come abilitare la soluzione Start/Stop VM di Automazione di Azure per le macchine virtuali di Azure.This article describes how to enable the Azure Automation Start/Stop VM solution for your Azure virtual machines.
services: automation
ms.subservice: process-automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: 4f4b0805e2a9247aff881a9e34b0bd1cd4c6ca27
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80550409"
---
# <a name="enable-azure-startstop-vms-solution"></a>Abilitare la soluzione Start/Stop VMs di AzureEnable Azure Start/Stop VMs solution

Eseguire la procedura seguente per aggiungere la soluzione Start/Stop VMs durante le ore non orari a un account di automazione nuovo o esistente e all'area di lavoro di Log Analytics collegata. Dopo aver completato il processo di onboarding, configurare le variabili per personalizzare la soluzione.

>[!NOTE]
>Per usare questa soluzione con le macchine virtuali classiche, è necessario un account RunAs classico, che non viene creato per impostazione predefinita. Per istruzioni sulla creazione di un account RunAs classico, vedere [Account RunAs classici](automation-create-standalone-account.md#classic-run-as-accounts).
>

## <a name="enable-solution"></a>Abilita soluzione

1. Accedere al [portale](https://portal.azure.com)di Azure .

2. Cercare e selezionare **Account di automazione**.

3. Nella pagina Account di **automazione** selezionare l'account di automazione dall'elenco.

4. Nell'account di automazione selezionare **Avvia/Arresta macchina virtuale** in **Risorse correlate**. Da qui è possibile fare clic su **Learn more about and enable the solution** (Scopri di più e abilita la soluzione). Se si è già distribuita una soluzione Avvio/Arresto di macchine virtuali, è possibile selezionarla facendo clic su **Gestisci la soluzione** e cercarla nell'elenco.

   ![Abilitare dall'account di automazione](./media/automation-solution-vm-management/enable-from-automation-account.png)

   > [!NOTE]
   > È possibile anche crearla da qualsiasi posizione nel portale di Azure, facendo clic su **Crea una risorsa**. Nella pagina del Marketplace digitare una parola chiave, ad esempio **Avvio** o **Avvio/Arresto**. Non appena si inizia a digitare, l'elenco viene filtrato in base all'input. In alternativa, è possibile digitare una o più parole chiave contenute nel nome completo della soluzione e quindi premere INVIO. Selezionare **Avvio/Arresto di macchine virtuali durante gli orari di minore attività** dai risultati della ricerca.

5. Nella pagina **Avvio/Arresto di macchine virtuali durante gli orari di minore attività** per la soluzione selezionata esaminare le informazioni di riepilogo e quindi fare clic su **Crea**.

   ![Portale di Azure](media/automation-solution-vm-management/azure-portal-01.png)

6. Viene visualizzata la pagina **Aggiungi soluzione**. Viene richiesto di configurare la soluzione prima di importarla nella sottoscrizione di Automazione.

   ![Pagina Aggiungi soluzione di Virtual Machine Management](media/automation-solution-vm-management/azure-portal-add-solution-01.png)

7. Nella pagina **Aggiungi soluzione** selezionare **Area di lavoro**. Selezionare un'area di lavoro Log Analytics collegata alla stessa sottoscrizione di Azure in cui è incluso l'account di Automazione. Se non è disponibile un'area di lavoro, selezionare **Crea una nuova area di lavoro**. Nella pagina **Area di lavoro di Log Analytics** eseguire la procedura seguente:On the Log Analytics workspace page, perform the following steps:

   - Specificare un nome per la nuova area di lavoro di **Log Analytics,** ad esempio "ContosoLAWorkspace".
   - Selezionare una **sottoscrizione** a cui collegarsi. Se la sottoscrizione selezionata per impostazione predefinita non è appropriata, è possibile sceglierne una dall'elenco a discesa.
   - Per il **gruppo di risorse**, è possibile selezionare un gruppo di risorse esistente o crearne uno nuovo.
   - Selezionare una **posizione**.
   - Selezionare un **piano tariffario**. Scegliere l'opzione **Per GB (autonomo)**. I log di Monitoraggio di Azure hanno [prezzi](https://azure.microsoft.com/pricing/details/log-analytics/) aggiornati e il livello Per GB è l'unica opzione.

   > [!NOTE]
   > Quando si abilitano soluzioni, sono supportate solo determinate aree per il collegamento a un'area di lavoro Log Analytics e un account di Automazione.
   >
   > Per un elenco delle coppie di mapping supportate, vedere Mapping delle aree per l'area di [lavoro Account di automazione e Log Analytics](how-to/region-mappings.md).

8. Dopo aver specificato le informazioni necessarie nella pagina **area di lavoro Log Analytics**, fare clic su **Crea**. È possibile monitorarne lo stato scegliendo **Notifiche** dal menu. Al termine, si tornerà alla pagina **Aggiungi soluzione**.

9. Nella pagina **Aggiungi soluzione** selezionare **Account di Automazione**. Se si sta creando una nuova area di lavoro Log Analytics, è possibile creare un nuovo account di Automazione da associarvi oppure selezionare un Account di automazione esistente che non sia già collegato a un'area di lavoro Log Analytics. Selezionare un account di Automazione esistente o fare clic su **Crea un account di Automazione** e specificare le informazioni seguenti nella pagina **Aggiungi account di Automazione**:
 
   - Nel campo **Nome** immettere il nome dell'account di Automazione.

     Tutte le altre opzioni vengono popolate automaticamente in base all'area di lavoro Log Analytics selezionata. Queste opzioni non possono essere modificate. Il metodo di autenticazione predefinito per i runbook inclusi nella soluzione è un account RunAs di Azure. Dopo aver fatto clic su **OK**, le opzioni di configurazione vengono convalidate e viene creato l'account di Automazione. Per tenere traccia dello stato di avanzamento, è possibile usare la voce **Notifiche** nel menu.

10. Nella pagina **Aggiungi soluzione** selezionare infine **Configurazione**. Viene visualizzata la pagina **Parametri**.

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

11. Dopo aver configurato le impostazioni iniziali necessarie per la soluzione, fare clic su **OK** per chiudere la pagina **Parametri** e selezionare **Crea**. 

Dopo la convalida di tutte le impostazioni, la soluzione viene distribuita nella sottoscrizione. Questo processo può richiedere alcuni secondi. Per tenere traccia dello stato di avanzamento, è possibile usare la voce **Notifiche** nel menu.

> [!NOTE]
> Se si dispone di una sottoscrizione di Azure Cloud Solution Provider (Azure CSP), al termine della distribuzione passare all'account di automazione passare a **Variabili** in **Risorse condivise** e impostare la variabile [**External_EnableClassicVMs**](automation-solution-vm-management.md#variables) su **False**. In questo modo, si impedisce alla soluzione di cercare risorse della macchina virtuale classica.

## <a name="next-steps"></a>Passaggi successivi

Ora che la soluzione è abilitata, è possibile [configurarla](automation-solution-vm-management-config.md) per supportare i requisiti di gestione delle macchine virtuali.