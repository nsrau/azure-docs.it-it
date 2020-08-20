---
title: Configurare le impostazioni di diagnostica dell'insieme di credenziali su larga scala
description: Configurare le impostazioni di diagnostica Log Analytics per tutti gli insiemi di credenziali in un determinato ambito usando criteri di Azure
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 58ef8af56bb3f44664ffaec6a17bab5f5e92808e
ms.sourcegitcommit: cd0a1ae644b95dbd3aac4be295eb4ef811be9aaa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2020
ms.locfileid: "88612504"
---
# <a name="configure-vault-diagnostics-settings-at-scale"></a>Configurare le impostazioni di diagnostica dell'insieme di credenziali su larga scala

La soluzione per la creazione di report fornita da backup di Azure sfrutta Log Analytics (LA). Per i dati di un determinato insieme di credenziali da inviare a LA, è necessario creare un' [impostazione di diagnostica](./backup-azure-diagnostic-events.md) per l'insieme di credenziali.

Spesso, l'aggiunta manuale di un'impostazione di diagnostica per ogni insieme di credenziali può essere un'attività complessa. Inoltre, per poter visualizzare i report per questo insieme di credenziali, è necessario che per tutti i nuovi insiemi di credenziali creati siano abilitate anche le impostazioni di diagnostica.

Per semplificare la creazione di impostazioni di diagnostica su larga scala (con LA come destinazione), backup di Azure fornisce criteri predefiniti di [Azure](../governance/policy/index.yml). Questo criterio aggiunge un'impostazione di diagnostica a tutti gli insiemi di credenziali in una sottoscrizione o in un gruppo di risorse specifico. Nelle sezioni seguenti vengono fornite istruzioni su come utilizzare questo criterio.

## <a name="supported-scenarios"></a>Scenari Supportati

* I criteri possono essere applicati contemporaneamente a tutti gli insiemi di credenziali di servizi di ripristino in una sottoscrizione specifica o a un gruppo di risorse all'interno della sottoscrizione. L'utente che assegna i criteri deve avere l'accesso "Owner" alla sottoscrizione a cui è assegnato il criterio.

* L'area di lavoro LA come specificato dall'utente (a cui verranno inviati i dati di diagnostica) può trovarsi in una sottoscrizione diversa dagli insiemi di credenziali a cui è assegnato il criterio. L'utente deve disporre dell'accesso ' Reader ',' collaboratore ' o ' Owner ' alla sottoscrizione in cui è presente l'area di lavoro LA specificata.

* Ambito del gruppo di gestione attualmente non supportato.

* Il criterio predefinito non è attualmente disponibile nei cloud nazionali.

## <a name="assigning-the-built-in-policy-to-a-scope"></a>Assegnazione dei criteri predefiniti a un ambito

Per assegnare i criteri per gli insiemi di credenziali nell'ambito necessario, attenersi alla procedura seguente:

1. Accedere al portale di Azure e passare al dashboard dei **criteri** .
2. Selezionare **definizioni** nel menu a sinistra per ottenere un elenco di tutti i criteri predefiniti tra le risorse di Azure.
3. Filtrare l'elenco per **Category = Monitoring**. Individuare il criterio denominato **[Anteprima]: Distribuisci impostazioni di diagnostica per l'insieme di credenziali di servizi di ripristino nell'area di lavoro log Analytics per categorie specifiche delle risorse**.

    ![Riquadro definizione criteri](./media/backup-azure-policy-configure-diagnostics/policy-definition-blade.png)

4. Selezionare il nome del criterio. Si verrà reindirizzati alla definizione dettagliata per questo criterio.

    ![Definizione dettagliata dei criteri](./media/backup-azure-policy-configure-diagnostics/detailed-policy-definition.png)

5. Selezionare il pulsante **assegna** nella parte superiore del riquadro. Viene quindi reindirizzati al riquadro **assegna criterio** .

6. In **nozioni di base**selezionare i tre puntini di sospensione accanto al campo **ambito** . Verrà aperto un riquadro di contesto a destra in cui è possibile selezionare la sottoscrizione per il criterio da applicare. Facoltativamente, è anche possibile selezionare un gruppo di risorse, in modo che i criteri vengano applicati solo per gli insiemi di credenziali in un determinato gruppo di risorse.

    ![Nozioni fondamentali sull'assegnazione di criteri](./media/backup-azure-policy-configure-diagnostics/policy-assignment-basics.png)

7. In **parametri**immettere le informazioni seguenti:

    * **Nome profilo** : il nome che verrà assegnato alle impostazioni di diagnostica create dal criterio.
    * **Log Analytics area di lavoro** : l'area di lavoro log Analytics a cui deve essere associata l'impostazione di diagnostica. I dati di diagnostica di tutti gli insiemi di credenziali nell'ambito dell'assegnazione di criteri verranno inseriti nell'area di lavoro di LA specificata.

    * **Nome del tag di esclusione (facoltativo) e valore del tag di esclusione (facoltativo)** : è possibile scegliere di escludere gli insiemi di credenziali contenenti un determinato nome di tag e un valore dall'assegnazione dei criteri. Ad esempio, se **non** si vuole aggiungere un'impostazione di diagnostica agli insiemi di credenziali che hanno un tag ' test ' impostato sul valore ' Yes ', è necessario immettere ' test ' nel campo del nome del tag di **esclusione** è Yes ' nel campo del valore del **tag di esclusione** . Se uno o entrambi i due campi vengono lasciati vuoti, i criteri verranno applicati a tutti gli insiemi di credenziali rilevanti indipendentemente dai tag in essi contenuti.

    ![Parametri di assegnazione dei criteri](./media/backup-azure-policy-configure-diagnostics/policy-assignment-parameters.png)

8. **Creare un'attività di monitoraggio e aggiornamento** : quando il criterio viene assegnato a un ambito, i nuovi insiemi di credenziali creati in tale ambito ottengono automaticamente le impostazioni di diagnostica della la configurazione (entro 30 minuti dal momento della creazione dell'insieme di credenziali). Per aggiungere un'impostazione di diagnostica agli insiemi di credenziali esistenti nell'ambito, è possibile attivare un'attività di monitoraggio e aggiornamento al momento dell'assegnazione dei criteri. Per attivare un'attività di monitoraggio e aggiornamento, selezionare la casella di controllo **Crea un'attività di correzione**.

    ![Monitoraggio e aggiornamento dell'assegnazione dei criteri](./media/backup-azure-policy-configure-diagnostics/policy-assignment-remediation.png)

9. Passare alla scheda **Verifica e crea** e selezionare **Crea**.

## <a name="under-what-conditions-will-the-remediation-task-apply-to-a-vault"></a>In quali condizioni l'attività di monitoraggio e aggiornamento viene applicata a un insieme di credenziali?

L'attività di monitoraggio e aggiornamento viene applicata agli insiemi di credenziali che non sono conformi in base alla definizione del criterio. Un insieme di credenziali non è conforme se soddisfa una delle condizioni seguenti:

* Non è presente alcuna impostazione di diagnostica per l'insieme di credenziali.
* Sono presenti impostazioni di diagnostica per l'insieme di credenziali, ma nessuna delle impostazioni include **tutti** gli eventi specifici della risorsa abilitati con la come destinazione e la **risorsa specifica** selezionata nell'interruttore.

Quindi, anche se un utente dispone di un insieme di credenziali con l'evento AzureBackupReport abilitato in modalità AzureDiagnostics (supportato dai report di backup), l'attività di correzione sarà ancora applicabile a questo insieme di credenziali, poiché la modalità specifica della risorsa è il modo consigliato per creare le impostazioni di diagnostica, [in futuro](./backup-azure-diagnostic-events.md#legacy-event).

Inoltre, se un utente dispone di un insieme di credenziali con solo un subset dei sei eventi specifici per le risorse abilitati, l'attività di monitoraggio e aggiornamento verrà applicata a questo insieme di credenziali, poiché i report di backup funzioneranno come previsto solo se tutti e sei gli eventi specifici delle risorse sono abilitati.

> [!NOTE]
>
> Se un insieme di credenziali dispone di un'impostazione di diagnostica esistente con un **subset di categorie specifiche delle risorse** abilitata, configurata per l'invio di dati a una determinata area di lavoro, ad indicare "area di lavoro x", l'attività di correzione avrà esito negativo (solo per tale insieme di credenziali) se l'area di lavoro di destinazione specificata nell'assegnazione dei criteri è la **stessa** "area
>
>Questo perché, se gli eventi abilitati da due diverse impostazioni di diagnostica sulla stessa risorsa si **sovrappongono** in qualche forma, le impostazioni non possono avere la stessa area di lavoro della destinazione. Sarà necessario risolvere manualmente l'errore, passando all'insieme di credenziali pertinente e configurando un'impostazione di diagnostica con un'area di lavoro di LA diversa come destinazione.
>
> Si noti che l'attività di correzione **non** avrà esito negativo se l'impostazione di diagnostica esistente è abilitata solo per AzureBackupReport con l'area di lavoro X come destinazione, poiché in questo caso non vi sarà sovrapposizione tra gli eventi abilitati dall'impostazione esistente e gli eventi abilitati dall'impostazione creata dall'attività di correzione.

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come usare i report di backup](./configure-reports.md)
* [Altre informazioni su criteri di Azure](../governance/policy/index.yml)
* [Usare i criteri di Azure per abilitare automaticamente il backup per tutte le macchine virtuali in un ambito](./backup-azure-auto-enable-backup.md)
