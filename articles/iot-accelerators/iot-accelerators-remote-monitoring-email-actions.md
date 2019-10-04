---
title: Azione di posta elettronica nella soluzione di monitoraggio remoto - Azure | Microsoft Docs
description: Questa guida procedurale illustra come aggiungere un'azione di posta elettronica a una regola nuova o esistente.
author: asdonald
manager: hegate
ms.author: asdonald
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/12/2018
ms.topic: conceptual
ms.openlocfilehash: fbb5f92258ff31dd7077bb1ade7fa7e5644c8bac
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65466896"
---
# <a name="add-an-email-action"></a>Aggiungere un'azione di posta elettronica

Le azioni di posta elettronica consentono di non perdersi mai un avviso. È possibile aggiungere un'azione di posta elettronica a una regola esistente o quando si crea una nuova regola.

Per completare i passaggi descritti in questa guida procedurale, è necessaria un'istanza distribuita dell'acceleratore della soluzione di monitoraggio remoto nella sottoscrizione di Azure.

Per creare o modificare una regola, è necessario essere un [**Amministratore** o avere le autorizzazioni corrette](iot-accelerators-remote-monitoring-rbac.md).

## <a name="edit-an-existing-rule"></a>Modificare una regola esistente

Seguire questa procedura per aggiungere un'azione di posta elettronica a una regola esistente:

1. Accedere alla soluzione di monitoraggio remoto.

1. Dal **Dashboard** passare alla pagina **Regole**:

    ![Pagina delle regole](./media/iot-accelerators-remote-monitoring-email-actions/rules-email.png)

1. Selezionare la casella di controllo accanto alla regola esistente da modificare e quindi fare clic su **Modifica** nella parte superiore. Viene visualizzato un pannello **Regola** modificabile.

1. Nella sezione **Azione** impostare **Email enabled** (Abilitato per posta elettronica) su **On** (Sì).

1. La prima volta che si abilita un'azione di posta elettronica nell'acceleratore della soluzione, è necessario [accedere ad Outlook](#outlook).

1. Immettere un indirizzo di posta elettronica nella casella del destinatario e premere il tasto **Invio** per ogni indirizzo di posta elettronica da aggiungere:

    ![Voce della rubrica](./media/iot-accelerators-remote-monitoring-email-actions/address-email.png)

1. Immettere l'oggetto del messaggio di posta elettronica.

1. Immettere eventuali note aggiuntive per i destinatari di posta elettronica come testo normale. È possibile usare la formattazione HTML se si [modifica il modello di messaggio di posta elettronica](#htmledit).

1. Verificare che l'opzione **Stato regola** sia impostata su **Abilitato**.

1. Fare clic su **Apply**.

## <a name="create-a-new-rule"></a>Creare una nuova regola

Seguire questa procedura per aggiungere un'azione di posta elettronica quando si crea una nuova regola:

1. Accedere alla soluzione di monitoraggio remoto.

1. Dal **Dashboard** passare alla pagina **Regole**:

    ![Pagina delle regole](./media/iot-accelerators-remote-monitoring-email-actions/rules-email.png)

1. Seguire la procedura descritta nella sezione relativa alla [creazione di una regola](iot-accelerators-remote-monitoring-automate.md#create-a-rule). Seguire la procedura descritta nella sezione relativa alla [creazione di una regola avanzata](iot-accelerators-remote-monitoring-automate.md#create-an-advanced-rule) fino al punto in cui si imposta un **Livello di gravità**. Non fare ancora clic su **Applica**.

1. Nella sezione **Azione** impostare **Email enabled** (Abilitato per posta elettronica) su **On** (Sì).

1. La prima volta che si abilita un'azione di posta elettronica nell'acceleratore della soluzione, è necessario [accedere ad Outlook](#outlook).

1. Immettere un indirizzo di posta elettronica nella casella del destinatario e premere il tasto **Invio** per ogni indirizzo di posta elettronica da aggiungere:

    ![Voce della rubrica](./media/iot-accelerators-remote-monitoring-email-actions/address-email.png)

1. Immettere l'oggetto del messaggio di posta elettronica.

1. Immettere eventuali note aggiuntive per i destinatari di posta elettronica come testo normale. È possibile usare la formattazione HTML se si [modifica il modello di messaggio di posta elettronica](#htmledit).

1. Verificare che l'opzione **Stato regola** sia impostata su **Abilitato**.

1. Fare clic su **Apply**.

La regola con un'azione di posta elettronica è ora abilitata. Ogni volta che viene attivata l'azione, ai destinatari viene inviato un nuovo messaggio di posta elettronica.

## Accedere ad Outlook <a name="outlook"></a>

La prima volta che si abilita un'azione di posta elettronica nell'acceleratore della soluzione, è necessario accedere ad Outlook. Con questa azione si configura l'account di posta elettronica che invia le notifiche tramite posta elettronica.

> [!NOTE]
> È opportuno creare un account Outlook specifico per le notifiche dell'acceleratore della soluzione e usarlo quando si abilita la prima azione di posta elettronica.

### <a name="contributor-role-outlook-setup"></a>Configurazione del ruolo di collaboratore in Outlook

Se l'acceleratore della soluzione è stato distribuito da un utente con il ruolo di **collaboratore** nella sottoscrizione, l'applicazione non ha autorizzazioni sufficienti per configurare e verificare le azioni di posta elettronica tramite l'interfaccia utente Web.

Prima di iniziare, creare un account di Outlook da usare per inviare notifiche tramite posta elettronica dall'acceleratore della soluzione.

La procedura seguente mostra come configurare e verificare le azioni di posta elettronica manualmente:

1. Passare al [portale di Azure](https://portal.azure.com).

1. Passare al gruppo di risorse dell'acceleratore della soluzione.

1. Fare clic su **office365-connector**:

    ![Connessione API](./media/iot-accelerators-remote-monitoring-email-actions/apiconnector1.png)

1. Fare clic sul banner per iniziare il processo di autorizzazione:

    ![Autorizza](./media/iot-accelerators-remote-monitoring-email-actions/connector1.png)

1. Fare clic su **Autorizza**. Viene richiesto di accedere. L'account usato per l'accesso deve corrispondere all'indirizzo di posta elettronica usato dall'applicazione per inviare notifiche tramite posta elettronica:

    ![Pulsante Autorizza](./media/iot-accelerators-remote-monitoring-email-actions/authorize.png)

1. Fare clic su **Salva** nella parte inferiore. Se il banner scompare, l'autorizzazione ha avuto esito positivo.

1. Per modificare l'indirizzo di posta elettronica da cui vengono inviate le notifiche, fare clic su **Modifica connessione API**.

    ![Modificare l'indirizzo di posta elettronica](./media/iot-accelerators-remote-monitoring-email-actions/editemail1.png)

### <a name="owner-role-outlook-setup"></a>Configurazione del ruolo di proprietario in Outlook

Se l'acceleratore della soluzione è stato distribuito da un utente con il ruolo di **proprietario** nella sottoscrizione, l'applicazione può verificare che le azioni di posta elettronica siano state configurate correttamente tramite l'interfaccia utente Web.

Prima di iniziare, creare un account di Outlook da usare per inviare notifiche tramite posta elettronica dall'acceleratore della soluzione.

Seguire questa procedura per accedere e configurare le azioni di posta elettronica:

1. Fare clic per accedere ad Outlook. Viene visualizzato il portale di Azure:

   ![Accedere ad Outlook](./media/iot-accelerators-remote-monitoring-email-actions/owneroutlook-email.png)

1. Fare clic su **Autorizza**. Viene richiesto di accedere. L'account usato per l'accesso deve corrispondere all'indirizzo di posta elettronica usato dall'applicazione per inviare notifiche tramite posta elettronica:

1. Fare clic su **Save**. Tornare all'acceleratore della soluzione e aggiornare la pagina.

1. Se le notifiche tramite posta elettronica sono state configurate correttamente, viene visualizzato questo messaggio:

   ![Accesso ad Outlook riuscito](./media/iot-accelerators-remote-monitoring-email-actions/success-email.png)

## Personalizzare il modello HTML dei messaggi di posta elettronica <a name="htmledit"></a>

Per impostazione predefinita, l'acceleratore della soluzione di monitoraggio remoto fornisce un modello HTML di base per i messaggi di azione. Il modello di messaggio di posta elettronica usa i valori delle impostazioni dell'azione di posta elettronica. Di seguito è illustrato un messaggio di posta elettronica di esempio:

![messaggio di posta elettronica di esempio](./media/iot-accelerators-remote-monitoring-email-actions/emailtemplate1.png)

La procedura seguente mostra come modificare il modello di messaggio di posta elettronica HTML. È possibile, ad esempio, includere altre informazioni o aggiungere immagini personalizzate:

1. Clonare il repository GitHub .NET o Java della soluzione di monitoraggio remoto:

1. Passare al percorso del modello di messaggio di posta elettronica:
  
    `Dotnet: device-telemetry\ActionsAgent\data\EmailTemplate.html`
  
    `Java device-telemetry/app/resources/data/EmailTemplate.html`

1. Per personalizzare il messaggio, è possibile aggiungere o rimuovere i parametri desiderati dal modello. È anche possibile aggiungere, rimuovere o sostituire le chiamate, in base alle esigenze:

    Ad esempio, nel codice .NET:  `emailTemplate = emailTemplate.Replace("${subject}", emailAction.GetSubject());`

    Ad esempio, nel codice Java:  `this.emailTemplate.replace("${subject}", emailAction.GetSubject());`

1. I parametri nel modello presentano questo formato: `${...}`. Per eliminare un parametro, eliminare la riga richiesta. Per aggiungere un parametro, aggiungere una riga con il valore da inserire.

1. Per aggiungere immagini o testo personalizzato, aggiornare direttamente il file EmailTemplate.HTML.

## <a name="throttling"></a>Limitazione

L'acceleratore della soluzione di monitoraggio remoto usa Outlook per inviare notifiche tramite posta elettronica. Outlook limita il numero di messaggi di posta elettronica inviati a [30 messaggi al minuto](https://docs.microsoft.com/office365/servicedescriptions/exchange-online-service-description/exchange-online-limits#receiving-and-sending-limits). Anche i client di posta elettronica che ricevono i messaggi possono limitare il numero di messaggi ricevuti al minuto. Controllare quindi le limitazioni imposte dal client di posta elettronica. Quando si configura una notifica tramite posta elettronica per una regola, la regola deve calcolare i valori medi su un periodo di almeno un minuto e non usare valori immediati:

![Calcolo medio](./media/iot-accelerators-remote-monitoring-email-actions/calculation-email.png)

## <a name="next-steps"></a>Passaggi successivi

Questa guida ha descritto la procedura per aggiungere un'azione di posta elettronica a una regola nuova o esistente all'interno di una soluzione di monitoraggio remoto. Ha inoltre illustrato come modificare il codice HTML che definisce il formato del messaggio.

Il passaggio successivo consigliato prevedere di imparare a [usare gli avvisi e correggere i problemi dei dispositivi](iot-accelerators-remote-monitoring-maintain.md).
