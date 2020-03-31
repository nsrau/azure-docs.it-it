---
title: Configurare le impostazioni di Diagnostica Vault su larga scala
description: Configurare le impostazioni di diagnostica di Log Analytics per tutti gli insiemi di credenziali in un determinato ambito usando Criteri di AzureConfigure Log Analytics Diagnostics settings for all logs in a given scope using Azure Policy
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: c92957cab3e1ed745e7031e3c6f32e7ecda550a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77584507"
---
# <a name="configure-vault-diagnostics-settings-at-scale"></a>Configurare le impostazioni di Diagnostica Vault su larga scala

La soluzione di creazione di report fornita da Backup di Azure sfrutta Log Analytics (LA). Affinché i dati di un determinato insieme di credenziali vengano inviati a LA, è necessario creare [un'impostazione](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events) di diagnostica per tale insieme di credenziali.

Spesso, l'aggiunta manuale di un'impostazione di diagnostica per vault può essere un'attività complessa. Inoltre, ogni nuovo vault creato deve avere le impostazioni di diagnostica abilitate per poter visualizzare i report per questo vault. 

Per semplificare la creazione di impostazioni di diagnostica su larga scala (con LA come destinazione), Backup di Azure offre un criterio di [Azure](https://docs.microsoft.com/azure/governance/policy/)incorporato. Questo criterio aggiunge un'impostazione di diagnostica LA a tutti gli insiemi di credenziali in una sottoscrizione o in un gruppo di risorse specificato. Nelle sezioni seguenti vengono fornite istruzioni sull'utilizzo di questo criterio.

## <a name="supported-scenarios"></a>Scenari Supportati

* Il criterio può essere applicato contemporaneamente a tutti gli insiemi di credenziali di Servizi di ripristino in una determinata sottoscrizione (o a un gruppo di risorse all'interno della sottoscrizione). L'utente che assegna il criterio deve disporre dell'accesso "Proprietario" alla sottoscrizione a cui è assegnato il criterio.

* L'area di lavoro di LA specificata dall'utente (a cui verranno inviati i dati di diagnostica) può trovarsi in una sottoscrizione diversa dagli insiemi di credenziali a cui è assegnato il criterio. L'utente deve disporre dell'accesso 'Reader', 'Contributor' o 'Owner' alla sottoscrizione in cui è presente l'area di lavoro di LA specificata.

* L'ambito del gruppo di gestione non è attualmente supportato.

* La politica integrata non è attualmente disponibile nei cloud nazionali.

## <a name="assigning-the-built-in-policy-to-a-scope"></a>Assegnazione dei criteri predefiniti a un ambitoAssigning the built-in policy to a scope

Per assegnare il criterio per gli insiemi di credenziali nell'ambito richiesto, attenersi alla seguente procedura:

1. Accedere al portale di Azure e passare al dashboard **dei criteri.**
2. Selezionare **Definizioni** nel menu a sinistra per ottenere un elenco di tutti i criteri predefiniti tra le risorse di Azure.Select Definitions in the left menu to get a list of all built-in policies across Azure Resources.
3. Filtrare l'elenco per **Categoria-Monitoraggio**. Individuare il criterio denominato **[Preview]: Distribuire le impostazioni di diagnostica per**l'insieme di credenziali dei servizi di ripristino nell'area di lavoro di Log Analytics per categorie specifiche delle risorse.

![Blade definizione criteri](./media/backup-azure-policy-configure-diagnostics/policy-definition-blade.png)

4. Fare clic sul nome del criterio. Si verrà reindirizzati alla definizione dettagliata di questo criterio.

![Definizione dettagliata dei criteri](./media/backup-azure-policy-configure-diagnostics/detailed-policy-definition.png)

5. Fare clic sul pulsante **Assegna** nella parte superiore del pannello. In questo modo si reindirizza al pannello **Assegna criterio.**

6. In **Nozioni di base**fare clic sui tre puntini accanto al campo **Ambito.** Verrà aperto un pannello di contesto corretto in cui è possibile selezionare la sottoscrizione per il criterio da applicare. È anche possibile selezionare facoltativamente un gruppo di risorse, in modo che il criterio venga applicato solo per gli insiemi di credenziali in un determinato gruppo di risorse.

![Nozioni di base sulle assegnazioni dei criteri](./media/backup-azure-policy-configure-diagnostics/policy-assignment-basics.png)

7. In **Parametri**immettere le seguenti informazioni:

* **Nome profilo:** nome che verrà assegnato alle impostazioni di diagnostica create dal criterio.
* **Area di lavoro di Log Analytics:** area di lavoro di Log Analytics a cui deve essere associata l'impostazione di diagnostica. I dati di diagnostica di tutti gli insiemi di credenziali nell'ambito dell'assegnazione dei criteri verranno inviati all'area di lavoro DI LA specificata.

* **Nome tag di esclusione (facoltativo) e Valore tag di esclusione (facoltativo):** è possibile scegliere di escludere dall'assegnazione dei criteri i vault contenenti un determinato nome e valore di tag. Ad esempio, se **non** si desidera aggiungere un'impostazione di diagnostica agli insiemi di credenziali con un tag 'isTest' impostato sul valore 'yes', è necessario immettere 'isTest' nel campo Nome tag di **esclusione** e 'yes' nel campo **Valore tag di esclusione.** Se uno o entrambi questi campi vengono lasciati vuoti, il criterio verrà applicato a tutti i vault pertinenti indipendentemente dai tag che contengono.

![Parametri di assegnazione dei criteri](./media/backup-azure-policy-configure-diagnostics/policy-assignment-parameters.png)

8. **Creare un'attività** di correzione: una volta che il criterio è stato assegnato a un ambito, tutti i nuovi insiemi di credenziali creati in tale ambito ottengono automaticamente le impostazioni di diagnostica LA configurate (entro 30 minuti dal momento della creazione dell'insieme di credenziali). Per aggiungere un'impostazione di diagnostica agli insiemi di credenziali esistenti nell'ambito, è possibile attivare un'attività di correzione al momento dell'assegnazione dei criteri. Per attivare un'attività di correzione, selezionare la casella di **controllo Crea un'attività di correzione.** 

![Correzione dell'assegnazione dei criteriPolicy Assignment Remediation](./media/backup-azure-policy-configure-diagnostics/policy-assignment-remediation.png)

9. Passare alla scheda **Revisione/Crea** e fare clic su **Crea**.

## <a name="under-what-conditions-will-the-remediation-task-apply-to-a-vault"></a>In quali condizioni l'attività di correzione verrà applicata a un insieme di credenziali?

L'attività di correzione viene applicata agli insiemi di credenziali non conformi in base alla definizione dei criteri. Un insieme di credenziali non è conforme se soddisfa una delle seguenti condizioni:

* Non è presente alcuna impostazione di diagnostica per il vault.
* Le impostazioni di diagnostica sono presenti per l'insieme di credenziali, ma nessuna delle impostazioni ha **tutti gli** eventi specifici della risorsa abilitati con LA come destinazione e Specifico **risorsa** selezionato nell'interruttore. 

Pertanto, anche se un utente dispone di un insieme di credenziali con l'evento AzureBackupReport abilitato in modalità AzureDiagnostics (supportato dai report di backup), l'attività di correzione verrà comunque applicata a questo insieme di credenziali, poiché la modalità specifica della risorsa è il modo consigliato per creare le impostazioni di diagnostica, [in futuro](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events#legacy-event).

Inoltre, se un utente dispone di un insieme di credenziali con solo un sottoinsieme dei sei eventi specifici della risorsa abilitati, l'attività di correzione verrà applicata per questo insieme di credenziali, poiché i rapporti di backup funzioneranno come previsto solo se tutti i sei eventi specifici della risorsa sono abilitati.

> [!NOTE]
>
> Se un insieme di credenziali dispone di un'impostazione di diagnostica esistente con un **sottoinsieme di** categorie specifiche della risorsa abilitate, configurate per l'invio di dati a un'area di lavoro di La specifica, ad esempio "Workspace X", l'attività di correzione avrà esito negativo (solo per l'insieme di credenziali) se l'area di lavoro LA di destinazione disponibile nell'assegnazione dei criteri è **la stessa** 'X' dell'area di lavoro. 
>
>Questo avviene perché, se gli eventi abilitati da due impostazioni di diagnostica diverse nella stessa risorsa **si sovrappongono** in qualche forma, le impostazioni non possono avere lo stesso spazio di lavoro DI LA della destinazione. Sarà necessario risolvere manualmente questo errore, passando all'insieme di credenziali pertinente e configurando un'impostazione di diagnostica con un'area di lavoro di LA diversa come destinazione.
>
> Si noti che l'attività di correzione **non** avrà esito negativo se l'impostazione di diagnostica esistente solo come AzureBackupReport abilitato con Workspace X come destinazione, poiché in questo caso non vi sarà alcuna sovrapposizione tra gli eventi abilitati dall'impostazione esistente e gli eventi abilitati dall'impostazione creata dall'attività di correzione.

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni sull'utilizzo dei rapporti di backup](https://docs.microsoft.com/azure/backup/configure-reports)
* [Altre informazioni su Criteri di AzureLearn more about Azure Policy](https://docs.microsoft.com/azure/governance/policy/)
* [Usare Criteri di Azure per abilitare automaticamente il backup per tutte le macchine virtuali in un ambito predefinitoUse Azure Policy to auto-enable backup for all VMs in a give scope](https://docs.microsoft.com/azure/backup/backup-azure-auto-enable-backup)
