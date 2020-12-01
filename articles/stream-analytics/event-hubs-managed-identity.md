---
title: Usare identità gestite per accedere a hub eventi da un processo di analisi di flusso di Azure (anteprima)
description: Questo articolo descrive come usare identità gestite per autenticare il processo di analisi di flusso di Azure nell'input e nell'output di hub eventi di Azure.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 11/30/2020
ms.openlocfilehash: c65833e5bf581c6326bf9362c7e5fc00a985d301
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96355027"
---
# <a name="use-managed-identities-to-access-event-hubfrom-an-azure-stream-analytics-job-preview"></a>Usare identità gestite per accedere a hub eventi da un processo di analisi di flusso di Azure (anteprima)

Analisi di flusso di Azure supporta l'autenticazione dell'identità gestita per l'input e l'output di hub eventi di Azure. Le identità gestite eliminano le limitazioni dei metodi di autenticazione basati su utente, ad esempio la necessità di eseguire nuovamente l'autenticazione a causa di modifiche della password o di scadenze dei token utente che si verificano ogni 90 giorni. Quando si elimina la necessità di eseguire l'autenticazione manualmente, le distribuzioni di Analisi di flusso possono essere completamente automatizzate.  

Un'identità gestita è un'applicazione gestita registrata in Azure Active Directory che rappresenta un determinato processo di Analisi di flusso. L'applicazione gestita viene usata per eseguire l'autenticazione a una risorsa di destinazione, inclusi gli hub eventi protetti da un firewall o una rete virtuale (VNet). Per altre informazioni su come ignorare i firewall, vedere [consentire l'accesso agli spazi dei nomi di hub eventi di Azure tramite endpoint privati](../event-hubs/private-link-service.md#trusted-microsoft-services).

Questo articolo illustra come abilitare l'identità gestita per l'input o l'output di un hub eventi di un processo di analisi di flusso tramite la portale di Azure.Prima di abilitare l'identità gestita, è necessario avere prima di tutto un processo di analisi di flusso e una risorsa Hub eventi.

## <a name="create-a-managedidentity"></a>Creare un'identità gestita  

Per prima cosa, si crea un'identità gestita per il processo di Analisi di flusso di Azure.  

1. Nel portale di Azure aprire il processo di analisi di flusso di Azure.  

1. Nel menu di spostamento a sinistra selezionare **identità gestita**   situata in *Configura*. Selezionare quindi la casella accanto a **Usa identità gestita assegnata dal sistema**   e selezionare **Salva**.

   :::image type="content" source="media/event-hubs-managed-identity/system-assigned-managed-identity.png" alt-text="Identità gestita assegnata dal sistema":::  

1. Viene creata un'entità servizio per l'identità del processo di analisi di flusso in Azure Active Directory. Il ciclo di vita dell'identità appena creata è gestito da Azure. Quando si elimina il processo di Analisi di flusso, l'identità associata (ovvero, l'entità servizio) viene eliminata automaticamente da Azure.  

   Quando si salva la configurazione, l'ID oggetto (OID) dell'entità servizio viene indicato come ID entità, come illustrato di seguito:  

   :::image type="content" source="media/event-hubs-managed-identity/principal-id.png" alt-text="ID entità di sicurezza":::

   L'entità servizio ha lo stesso nome del processo di Analisi di flusso. Se, ad esempio, il nome del processo è  `MyASAJob` , anche il nome dell'entità servizio è  `MyASAJob` .  

## <a name="grant-the-stream-analytics-job-permissionsto-access-the-event-hub"></a>Concedere le autorizzazioni del processo di analisi di flusso per accedere all'hub eventi

Per il processo di analisi di flusso per accedere all'hub eventi tramite identità gestita, l'entità servizio creata deve avere autorizzazioni speciali per l'hub eventi.

1. Passare a **controllo di accesso (IAM)** nell'hub eventi.

1. Selezionare **+ Aggiungi** e **aggiungere assegnazione di ruolo**.

1. Nella pagina **Aggiungi assegnazione ruolo** immettere le opzioni seguenti:

   |Parametro|valore|
   |---------|-----|
   |Ruolo|Proprietario dei dati di Hub eventi di Azure|
   |Assegna accesso a|Utente, gruppo o entità servizio|
   |Select|Immettere il nome del processo di analisi di flusso|

   :::image type="content" source="media/event-hubs-managed-identity/add-role-assignment.png" alt-text="Aggiungere un'assegnazione di ruolo":::

1. Selezionare **Save (Salva** ) e attendere un minuto per la propagazione delle modifiche.

È anche possibile concedere questo ruolo a livello di spazio dei nomi dell'hub eventi, in modo da propagare naturalmente le autorizzazioni a tutti gli hub eventi creati al suo interno. Ovvero, tutti gli hub eventi in uno spazio dei nomi possono essere usati come risorsa di autenticazione gestita di identità nel processo di analisi di flusso.

## <a name="create-anevent-hub-input-or-output"></a>Creare un input o un output dell'hub eventi  

Ora che l'identità gestita è configurata, si è pronti per aggiungere la risorsa Hub eventi come input o output al processo di analisi di flusso.  

### <a name="add-the-event-hub-as-an-input"></a>Aggiungere l'hub eventi come input 

1. Passare al processo di analisi di flusso e passare alla pagina **input** in **topologia processo**.

1. Selezionare **Aggiungi input flusso > Hub eventi**. Nella finestra Proprietà input cercare e selezionare l'hub eventi e selezionare **identità gestita** dal menu a discesa *modalità di autenticazione* .

1. Compilare il resto delle proprietà e selezionare **Salva**.

### <a name="add-the-event-hub-as-an-output"></a>Aggiungere l'hub eventi come output

1. Passare al processo di analisi di flusso e passare alla pagina **output** in **topologia processo**.

1. Selezionare **aggiungi > Hub eventi**. Nella finestra Proprietà output cercare e selezionare l'hub eventi e selezionare **identità gestita** dal menu a discesa *modalità di autenticazione* .

1. Compilare il resto delle proprietà e selezionare **Salva**.

## <a name="next-steps"></a>Passaggi successivi

* [Output di hub eventi da analisi di flusso di Azure](event-hubs-output.md)
* [Trasmettere dati da Hub eventi](stream-analytics-define-inputs.md#stream-data-from-event-hubs)