---
title: Come creare avvisi per l'inventario e il Rilevamento modifiche di automazione di Azure
description: Questo articolo descrive come configurare gli avvisi di Azure per notificare lo stato delle modifiche rilevate da Rilevamento modifiche e dall'inventario.
services: automation
ms.subservice: change-inventory-management
ms.date: 10/15/2020
ms.topic: conceptual
ms.openlocfilehash: 275e57e5dcf173e8d5f30f262641b02698910422
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/19/2020
ms.locfileid: "92210084"
---
# <a name="how-to-create-alerts-for-change-tracking-and-inventory"></a>Come creare avvisi per Rilevamento modifiche e inventario

Gli avvisi in Azure notificano in modo proattivo i risultati dei processi Runbook, dei problemi di integrità del servizio o di altri scenari correlati all'account di automazione. Automazione di Azure non include regole di avviso preconfigurate, ma è possibile crearne di personalizzate in base ai dati generati. Questo articolo fornisce indicazioni sulla creazione di regole di avviso in base alle modifiche identificate da Rilevamento modifiche e dall'inventario.

Se non si ha familiarità con gli avvisi di monitoraggio di Azure, vedere [Panoramica degli avvisi in Microsoft Azure prima di](../../azure-monitor/platform/alerts-overview.md) iniziare. Per altre informazioni sugli avvisi che usano le query di log, vedere [avvisi del log in monitoraggio di Azure](../../azure-monitor/platform/alerts-unified-log.md).

## <a name="create-alert"></a>Creare un avviso

L'esempio seguente mostra che il file **c:\windows\system32\drivers\etc\hosts** è stato modificato in un computer. Questo file è importante perché viene usato da Windows per risolvere i nomi host in indirizzi IP. Questa operazione ha la precedenza rispetto a DNS e potrebbe causare problemi di connettività. Può anche causare il reindirizzamento del traffico a siti Web dannosi o altrimenti pericolosi.

![Grafico che mostra la modifica del file hosts](./media/configure-alerts/changes.png)

Si userà questo esempio per illustrare i passaggi per la creazione di avvisi in una modifica.

1. Nella pagina **rilevamento modifiche** dell'account di automazione selezionare **log Analytics**.

2. In Ricerca log cercare le modifiche al contenuto nel file **hosts** file con la query `ConfigurationChange | where FieldsChanged contains "FileContentChecksum" and FileSystemPath contains "hosts"`. Questa query cerca le modifiche al contenuto per i file con nomi di percorso completi che contengono la parola `hosts` . È anche possibile cercare un file specifico usando il formato completo del percorso anziché una sola parte, ad esempio `FileSystemPath == "c:\windows\system32\drivers\etc\hosts"`.

3. Dopo che la query ha restituito i risultati, selezionare **nuova regola di avviso** nella ricerca log per aprire la pagina **creazione avviso** . È possibile accedere a questa pagina anche tramite **Monitoraggio di Azure** nel portale di Azure.

4. Controllare di nuovo la query e modificare la logica di avviso. In questo caso, si vuole che l'avviso venga attivato anche se viene rilevata una sola modifica in tutti i computer nell'ambiente.

    ![Modificare la query per tenere traccia delle modifiche apportate al file hosts](./media/configure-alerts/change-query.png)

5. Dopo aver impostato la logica di avviso, assegnare i gruppi di azioni per eseguire azioni in risposta all'attivazione dell'avviso. In questo caso vengono configurati messaggi di posta elettronica da inviare e un ticket di Gestione dei servizi IT (ITSM) da creare.

Attenersi alla procedura seguente per configurare gli avvisi per indicare lo stato di una distribuzione degli aggiornamenti. Se non si ha familiarità con gli avvisi di Azure, vedere [Panoramica degli avvisi di Azure](../../azure-monitor/platform/alerts-overview.md).

## <a name="configure-action-groups-for-your-alerts"></a>Configurare gruppi di azioni per gli avvisi

Dopo aver configurato gli avvisi, è possibile configurare un gruppo di azioni da usare in più avvisi. Le azioni possono includere notifiche tramite posta elettronica, manuali operativi, webhook e molto altro ancora. Per altre informazioni sui gruppi di azioni, vedere [Creare e gestire gruppi di azioni](../../azure-monitor/platform/action-groups.md).

1. Selezionare un avviso e quindi selezionare **Crea nuovo** in **Gruppi di azioni**.

2. Immettere un nome completo e un nome breve per il gruppo di azioni. Gestione aggiornamenti usa il nome breve quando invia notifiche usando il gruppo specificato.

3. In **Azioni** immettere un nome che specifichi l'azione, ad esempio **Notifica via posta elettronica**.

4. Per **Tipo azione** selezionare il tipo appropriato, ad esempio **Email/SMS/Push/Voice** (Posta elettronica/SMS/Push/Voce).

5. Selezionare **Modifica dettagli**.

6. Compilare il riquadro per il tipo di azione. Ad esempio, se si usa **posta elettronica/SMS/push/voce**, immettere un nome di azione, selezionare la casella di controllo **posta elettronica** , immettere un indirizzo di posta elettronica valido e quindi fare clic su **OK**.

    ![Configurare un gruppo di azioni di posta elettronica](./media/configure-alerts/configure-email-action-group.png)

7. Nel riquadro Aggiungi gruppo di azioni selezionare **OK**.

8. Per i messaggi di posta elettronica di avviso è possibile personalizzare l'oggetto. Selezionare **Personalizza azioni** in **Crea regola** e quindi selezionare **Oggetto messaggio di posta elettronica**.

9. Al termine, selezionare **Crea regola di avviso**.

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sugli [avvisi in monitoraggio di Azure](../../azure-monitor/platform/alerts-overview.md).

* Informazioni sulle [query di log](../../azure-monitor/log-query/log-query-overview.md) per recuperare e analizzare i dati da un'area di lavoro log Analytics.

* Gestire l' [utilizzo e i costi con i log di monitoraggio di Azure](../../azure-monitor/platform/manage-cost-storage.md) descrive come controllare i costi modificando il periodo di conservazione dei dati e come analizzare e inviare avvisi sull'utilizzo dei dati.