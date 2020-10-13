---
title: Modificare le impostazioni di approvazione per un pacchetto di accesso in Azure AD gestione dei diritti-Azure Active Directory
description: Informazioni su come modificare le impostazioni di approvazione e richiedente per un pacchetto di accesso in Azure Active Directory gestione dei diritti.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 09/16/2020
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2e36b2d4576b43544bec89efd326363344b35be9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90997085"
---
# <a name="change-approval-and-requestor-information-preview-settings-for-an-access-package-in-azure-ad-entitlement-management"></a>Modificare le impostazioni di approvazione e richiedente (anteprima) per un pacchetto di accesso in Azure AD gestione dei diritti

Come gestione pacchetti di accesso, è possibile modificare le impostazioni delle informazioni di approvazione e richiedente per un pacchetto di accesso in qualsiasi momento modificando un criterio esistente o aggiungendo un nuovo criterio.

Questo articolo descrive come modificare le impostazioni delle informazioni di approvazione e richiedente per un pacchetto di accesso esistente.

## <a name="approval"></a>Approvazione

Nella sezione approvazione specificare se è necessaria un'approvazione quando gli utenti richiedono questo pacchetto di accesso. Le impostazioni di approvazione funzionano nel modo seguente:

- Solo uno dei responsabili approvazione o i responsabili approvazione del fallback selezionati deve approvare una richiesta per l'approvazione a fase singola. 
- Solo uno dei responsabili approvazione selezionati da ogni fase deve approvare una richiesta per l'approvazione a 2 fasi.
- Il responsabile approvazione può essere un responsabile, uno sponsor interno o un sponsor esterno, a seconda dell'utente che governa l'accesso.
- L'approvazione di ogni responsabile approvazione selezionato non è necessaria per l'approvazione a una o due fasi.
- La decisione di approvazione si basa su qualsiasi revisore che esamina prima la richiesta.

Per una dimostrazione di come aggiungere responsabili approvazione a un criterio di richiesta, guardare il video seguente:

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE4cZfg]

Per una dimostrazione di come aggiungere un'approvazione in più fasi a un criterio di richiesta, guardare il video seguente:

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE4d1Jw]


## <a name="change-approval-settings-of-an-existing-access-package"></a>Modificare le impostazioni di approvazione di un pacchetto di accesso esistente

Per specificare le impostazioni di approvazione per le richieste per il pacchetto di accesso, attenersi alla procedura seguente:

**Ruolo prerequisito:** amministratore globale, amministratore utenti, proprietario del catalogo o responsabile dei pacchetti di accesso

1. Nel portale di Azure fare clic su **Azure Active Directory** e quindi su **Identity Governance**.

1. Nel menu a sinistra fare clic su **Access Packages** , quindi aprire il pacchetto Access.

1. Selezionare un criterio per modificare o aggiungere un nuovo criterio al pacchetto di accesso
    1. Fare clic su **criteri** e quindi su **Aggiungi criterio** se si desidera creare un nuovo criterio.
    1. Fare clic sul criterio che si desidera modificare e quindi fare clic su **modifica**.

1. Passare alla scheda **richiesta** .

1. Per richiedere l'approvazione per le richieste degli utenti selezionati, impostare l'interruttore **Richiedi approvazione** su **Sì**. In alternativa, per fare in modo che le richieste vengano approvate automaticamente, impostare l'interruttore su **No**.

1. Per richiedere agli utenti di fornire una giustificazione per richiedere il pacchetto di accesso, impostare l'opzione **Richiedi giustificazione** del richiedente su **Sì**.
    
1. Determinare ora se le richieste richiedono l'approvazione a una o due fasi. Impostare il **numero di fasi** su **1** per l'approvazione della fase singola o impostare l'interruttore su **2** per l'approvazione in due fasi.

    ![Access Package-requests-impostazioni di approvazione](./media/entitlement-management-access-package-approval-policy/approval.png)


Usare la procedura seguente per aggiungere responsabili approvazione dopo aver selezionato il numero di fasi necessarie: 

### <a name="single-stage-approval"></a>Approvazione a fase singola

1. Aggiungere il **primo responsabile approvazione**:
    
    Se il criterio è impostato per gestire l'accesso per gli utenti nella directory, è possibile selezionare **Manager come responsabile approvazione**. In alternativa, aggiungere un utente specifico facendo clic su **Aggiungi responsabili approvazione** dopo aver selezionato Scegli responsabili approvazione specifici dal menu a discesa.
    
    ![Accedere ai pacchetti-richieste-per gli utenti nel responsabile dell'approvazione della directory](./media/entitlement-management-access-package-approval-policy/approval-single-stage-first-approver-manager.png)

    Se questo criterio è impostato per gestire l'accesso per gli utenti che non si trova nella directory, è possibile selezionare **sponsor esterno** o **sponsor interno**. In alternativa, aggiungere un utente specifico facendo clic su **Aggiungi responsabili approvazione** o gruppi in Scegli responsabili approvazione specifici.
    
    ![Accedere al pacchetto-richieste-per gli utenti fuori dalla directory-primo responsabile approvazione](./media/entitlement-management-access-package-approval-policy/out-directory-first-approver.png)
    
1. Se è stata selezionata l'opzione **Manager** come primo responsabile approvazione, fare clic su **Aggiungi fallback** per selezionare uno o più utenti o gruppi nella directory come responsabile approvazione del fallback. I responsabili approvazione fallback ricevono la richiesta se la gestione dei diritti non riesce a trovare il responsabile per l'utente che richiede l'accesso.

    Il Manager è stato rilevato dalla gestione dei diritti utilizzando l'attributo **Manager** . L'attributo si trova nel profilo dell'utente in Azure AD. Per altre informazioni, vedere [aggiungere o aggiornare le informazioni sul profilo di un utente usando Azure Active Directory](../fundamentals/active-directory-users-profile-azure-portal.md).

1. Se è stata selezionata l'opzione **Scegli responsabili approvazione specifici**, fare clic su **Aggiungi responsabili approvazione** per selezionare uno o più utenti o gruppi nella directory da approvatori.

1. Nella casella in base alle **decisioni è necessario**specificare il numero di giorni per cui un responsabile approvazione deve esaminare una richiesta di questo pacchetto di accesso.

    Se una richiesta non viene approvata entro questo periodo di tempo, verrà negata automaticamente. L'utente dovrà inviare un'altra richiesta per il pacchetto di accesso.

1. Per richiedere agli approvatori di fornire una giustificazione per la loro decisione, impostare Richiedi giustificazione del responsabile approvazione su **Sì**.

    La giustificazione è visibile ad altri responsabili approvazione e al richiedente.

### <a name="2-stage-approval"></a>approvazione a 2 fasi

Se è stata selezionata un'approvazione a 2 fasi, sarà necessario aggiungere un secondo responsabile approvazione.

1. Aggiungere il **secondo responsabile approvazione**: 
    
    Se gli utenti si trovano nella directory, aggiungere un utente specifico come secondo responsabile approvazione facendo clic su **Aggiungi responsabili approvazione** in Scegli responsabili approvazione specifici.

    ![Accedere ai pacchetti-richieste-per gli utenti nel responsabile approvazione directory-secondo](./media/entitlement-management-access-package-approval-policy/in-directory-second-approver.png)

    Se gli utenti non sono presenti nella directory, selezionare **sponsor interno** o **sponsor esterno** come secondo responsabile approvazione. Dopo aver selezionato il responsabile approvazione, aggiungere i responsabili approvazione fallback.

    ![Accedere al pacchetto-richieste-per gli utenti fuori dalla directory-secondo responsabile approvazione](./media/entitlement-management-access-package-approval-policy/out-directory-second-approver.png) 

1. Specificare il numero di giorni per cui il secondo responsabile approvazione deve approvare la richiesta nella casella in base alla **decisione deve essere effettuata**per il numero di giorni. 

1. Impostare l'opzione Richiedi giustificazione del responsabile approvazione su **Sì** o **No**.

### <a name="alternate-approvers"></a>Responsabili approvazione alternativi

È possibile specificare responsabili approvazione alternativi, in modo analogo alla specifica del primo e del secondo approvatori che possono approvare le richieste. Con responsabili approvazione alternativi, è possibile garantire che le richieste vengano approvate o negate prima della scadenza (timeout). È possibile elencare gli approvatori alternativi il primo responsabile approvazione e il secondo responsabile dell'approvazione per 2 fasi. 

Specificando responsabili approvazione alternativi, nel caso in cui il primo o il secondo approvatori non fosse in grado di approvare o rifiutare la richiesta, la richiesta in sospeso viene trasmessa ai responsabili approvazione alternativi, in base alla pianificazione di invio specificata durante l'installazione dei criteri. Ricevono un messaggio di posta elettronica per approvare o negare la richiesta in sospeso.

Dopo l'invio della richiesta ai responsabili approvazione alternativi, il primo o il secondo approvatori può comunque approvare o rifiutare la richiesta. Gli approvatori alternativi utilizzano lo stesso sito di accesso personale per approvare o negare la richiesta in sospeso.

È possibile elencare persone o gruppi di persone come responsabili approvazione e responsabili approvazione alternativi. Assicurarsi di elencare set diversi di persone come primo, secondo e responsabili approvazione alternativi.
Se ad esempio sono stati elencati Alice e Bob come primo responsabile approvazione, elencare Carol e Dave come approvatori alternativi. Usare la procedura seguente per aggiungere responsabili approvazione alternativi a un pacchetto di accesso:

1. Sotto il primo responsabile approvazione, secondo responsabile approvazione o entrambi, fare clic su **Mostra impostazioni richieste avanzate**.

    ![Accedi ai criteri di pacchetto-Mostra impostazioni avanzate delle richieste](./media/entitlement-management-access-package-approval-policy/alternate-approvers-click-advanced-request.png)

1. Impostare **se non viene eseguita alcuna azione, procedere con l'invio a responsabili approvazione alternativi?** impostare su **Sì**.

1. Fare clic su **Aggiungi responsabili approvazione alternativi** e selezionare gli approvatori alternativi dall'elenco.

    ![Accedere ai criteri dei pacchetti-aggiungere responsabili approvazione alternativi](./media/entitlement-management-access-package-approval-policy/alternate-approvers-add.png)

1. In **futuro per i responsabili approvazione alternativi dopo** il numero di giorni, inserire il numero di giorni per cui i responsabili approvazione devono approvare o rifiutare una richiesta. Se nessun responsabile approvazione ha approvato o negato la richiesta prima della durata della richiesta, la richiesta scade (timeout) e l'utente dovrà inviare un'altra richiesta per il pacchetto di accesso. 

    Le richieste possono essere inviate solo a responsabili approvazione alternativi al giorno dopo che la durata della richiesta raggiunge la metà della durata e la decisione dei responsabili approvazione principali deve eseguire il timeout dopo almeno 4 giorni. Se il timeout della richiesta è minore o uguale a 3, non c'è tempo sufficiente per l'invio della richiesta a responsabili approvazione alternativi. In questo esempio, la durata della richiesta è di 14 giorni. Quindi, la durata della richiesta raggiunge la metà della vita del giorno 7. Quindi, la richiesta non può essere trasmessa prima del giorno 8. Non è inoltre possibile inviare le richieste l'ultimo giorno della durata della richiesta. Nell'esempio, l'ultima richiesta che è possibile trasmettere è il giorno 13.

## <a name="enable-requests"></a>Abilita richieste

1. Se si vuole che il pacchetto di accesso venga reso immediatamente disponibile per gli utenti del criterio di richiesta da richiedere, spostare l'interruttore Abilita su **Sì**.

    È sempre possibile abilitarla in futuro dopo aver completato la creazione del pacchetto di accesso.

    Se è stata selezionata l'opzione **Nessuna (solo assegnazione diretta amministratore)** e si imposta Abilita su **No**, gli amministratori non potranno assegnare direttamente questo pacchetto di accesso.

    ![Pacchetto di accesso-criteri-Abilita impostazione dei criteri](./media/entitlement-management-access-package-approval-policy/enable-requests.png)

1. Fare clic su **Avanti**.

## <a name="collect-additional-requestor-information-for-approval-preview"></a>Raccogli informazioni sul richiedente aggiuntive per l'approvazione (anteprima)

Per assicurarsi che gli utenti abbiano accesso ai pacchetti di accesso corretti, è possibile richiedere ai richiedenti di rispondere a un campo di testo personalizzato o a più domande scelte al momento della richiesta. È previsto un limite di 20 domande per ogni criterio e un limite di 25 risposte per le domande più scelte. Le domande verranno quindi visualizzate agli approvatori per consentire loro di prendere una decisione.

1. Passare alla scheda **informazioni sul richiedente** e fare clic sulla scheda sottocartella **domande** .
 
1. Digitare ciò che si vuole chiedere al richiedente, noto anche come stringa di visualizzazione, per la domanda nella casella della **domanda** .

    ![Accesso al pacchetto-criteri-Abilita impostazione informazioni sul richiedente](./media/entitlement-management-access-package-approval-policy/add-requestor-info-question.png)

1. Se la community di utenti che necessitano dell'accesso al pacchetto di accesso non dispone di un linguaggio preferito comune, è possibile migliorare l'esperienza per gli utenti che richiedono l'accesso a myaccess.microsoft.com. Per migliorare l'esperienza, è possibile fornire stringhe di visualizzazione alternative per diverse lingue. Se, ad esempio, il Web browser di un utente è impostato su spagnolo e sono state configurate stringhe di visualizzazione in spagnolo, tali stringhe verranno visualizzate all'utente richiedente. Per configurare la localizzazione per le richieste, fare clic su **Aggiungi localizzazione**.
    1. Nel riquadro **Aggiungi localizzazioni per la domanda** selezionare il codice della **lingua** per la lingua in cui si sta localizzando la domanda.
    1. Nella lingua configurata digitare la domanda nella casella di **testo localizzata** .
    1. Dopo aver aggiunto tutte le localizzazioni necessarie, fare clic su **Salva**.

    ![Accedere ai criteri di pacchetto-configurare il testo localizzato](./media/entitlement-management-access-package-approval-policy/add-localization-question.png)

1. Selezionare il **formato di risposta** a cui si vogliono rispondere i richiedenti. I formati di risposta includono: *testo breve*, *scelta multipla*e *testo lungo*.
 
    ![Accedere al pacchetto-criteri-selezionare Visualizza e modifica il formato di risposta a scelta multipla](./media/entitlement-management-access-package-approval-policy/answer-format-view-edit.png)
 
1. Se si seleziona scelta multipla, fare clic sul pulsante **Visualizza e modifica** per configurare le opzioni di risposta.
    1. Dopo aver selezionato Visualizza e modifica, viene aperto il riquadro **Visualizza/modifica domanda** .
    1. Digitare le opzioni di risposta che si desidera assegnare al richiedente quando si risponde alla domanda nelle caselle **valori di risposta** .
    1. Digitare il numero di risposte necessario, quindi fare clic su **Salva**.
    
    ![Access Package-policy-immettere le opzioni di scelta multipla](./media/entitlement-management-access-package-approval-policy/answer-multiple-choice.png)
  
1. Per richiedere ai richiedenti di rispondere a questa domanda quando si richiede l'accesso a un pacchetto di accesso, fare clic sulla casella di controllo in **required**.

1. Compilare le schede rimanenti, ad esempio il ciclo di vita, in base alle esigenze.

Dopo aver configurato le informazioni sul richiedente nei criteri del pacchetto di accesso, può visualizzare le risposte del richiedente alle domande. Per istruzioni sulla visualizzazione delle informazioni sul richiedente, vedere [visualizzare le risposte dei richiedenti alle domande (anteprima)](entitlement-management-request-approve.md#view-requestors-answers-to-questions-preview).

## <a name="next-steps"></a>Passaggi successivi
- [Modificare le impostazioni del ciclo di vita per un pacchetto di accesso](entitlement-management-access-package-lifecycle-policy.md)
- [Visualizzare le richieste per un pacchetto di accesso](entitlement-management-access-package-requests.md)
