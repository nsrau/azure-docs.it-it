---
title: Abilitare la protezione Azure AD password locale
description: Informazioni su come abilitare la protezione Azure AD password per un ambiente Active Directory Domain Services locale
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 03/05/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4e4797c3e089e0ae9d15cc604e9f93605978b36c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87419768"
---
# <a name="enable-on-premises-azure-active-directory-password-protection"></a>Abilitare la protezione Azure Active Directory password locale

Gli utenti creano spesso password che usano parole locali comuni, ad esempio il nome di una scuola, una squadra sportiva o un personaggio famoso. Queste password sono facili da indovinare e vulnerabili ad attacchi basati su dizionario. Per applicare password complesse nell'organizzazione, la protezione con password Azure Active Directory (Azure AD) fornisce un elenco di password vietate globali e personalizzate. Una richiesta di modifica della password ha esito negativo se è presente una corrispondenza nell'elenco delle password escluse.

Per proteggere l'ambiente di Active Directory Domain Services locale (AD DS), è possibile installare e configurare Azure AD la protezione delle password per l'uso con il controller di dominio locale. Questo articolo illustra come abilitare la protezione Azure AD password per l'ambiente locale.

Per ulteriori informazioni sul funzionamento di Azure AD la protezione delle password in un ambiente locale, vedere [come applicare Azure ad la protezione con password per Windows Server Active Directory](concept-password-ban-bad-on-premises.md).

## <a name="before-you-begin"></a>Prima di iniziare

Questo articolo illustra come abilitare la protezione Azure AD password per l'ambiente locale. Prima di completare questo articolo, [installare e registrare il servizio proxy Azure ad Password Protection e gli agenti del controller](howto-password-ban-bad-on-premises-deploy.md) di dominio nell'ambiente di servizi di dominio Active Directory locale.

## <a name="enable-on-premises-password-protection"></a>Abilitare la protezione delle password locale

1. Accedere al [portale di Azure](https://portal.azure.com) e passare a **Azure Active Directory**  >  metodi di autenticazione della**sicurezza**  >  **Authentication methods**  >  **password protezione**.
1. Impostare l'opzione **Abilita protezione password su Windows Server Active Directory su** *Sì*.

    Quando questa impostazione è impostata su *No*, tutti gli agenti del controller di dominio di Azure ad della password distribuiti passano a una modalità di riposo, in cui tutte le password vengono accettate così come sono. Non vengono eseguite attività di convalida e non vengono generati eventi di controllo.

1. È consigliabile impostare inizialmente la **modalità** di *controllo*. Una volta che si ha familiarità con la funzionalità e l'effetto sugli utenti dell'organizzazione, è possibile passare alla **modalità** *imposta*. Per ulteriori informazioni, vedere la sezione seguente sulle [modalità operative](#modes-of-operation).
1. Al termine, selezionare **Salva**.

    [![Abilitare la protezione delle password locale in metodi di autenticazione nel portale di Azure](media/howto-password-ban-bad-on-premises-operations/enable-configure-custom-banned-passwords-cropped.png)](media/howto-password-ban-bad-on-premises-operations/enable-configure-custom-banned-passwords.png#lightbox)

## <a name="modes-of-operation"></a>Modalità di funzionamento

Quando si Abilita la protezione Azure AD password locale, è possibile usare *la modalità di* *controllo* o l'imposizione. È consigliabile che la distribuzione iniziale e i test vengano sempre avviati in modalità di controllo. Le voci nel registro eventi dovrebbero quindi essere monitorate per prevedere se eventuali processi operativi esistenti verranno disturbati dopo l'abilitazione della modalità di *applicazione* .

### <a name="audit-mode"></a>Modalità di controllo

La modalità di *controllo* è concepita come un modo per eseguire il software in una modalità di simulazione. Ogni servizio di Azure AD Password Protection Agent valuta una password in ingresso in base ai criteri attualmente attivi.

Se il criterio corrente è configurato per essere in modalità di controllo, le password "negative" generano messaggi nel registro eventi, ma vengono elaborati e aggiornati. Questo comportamento è l'unica differenza tra la modalità di controllo e di applicazione. Tutte le altre operazioni eseguono lo stesso.

### <a name="enforced-mode"></a>Modalità applicata

La modalità *applicata* è la configurazione finale. Analogamente a quanto avviene in modalità di controllo, ogni servizio Azure AD Password Protection Agent esegue la valutazione delle password in entrata in base ai criteri attualmente attivi. Quando è abilitata la modalità imposta, tuttavia, una password considerata non sicura in base ai criteri viene rifiutata.

Quando una password viene rifiutata in modalità imposta dall'agente del controller di dominio Azure AD Password Protection, un utente finale Visualizza un errore simile a quello che vedrebbe se la password è stata rifiutata dall'imposizione tradizionale di complessità delle password locali. Ad esempio, un utente potrebbe visualizzare il seguente messaggio di errore tradizionale alla schermata di accesso di Windows o modificare la password:

*"Impossibile aggiornare la password. Il valore specificato per la nuova password non soddisfa i requisiti di lunghezza, complessità o cronologia del dominio. "*

Questo messaggio è solo un esempio dei possibili risultati. Il messaggio di errore specifico può variare a seconda del software o dello scenario effettivo che tenta di impostare una password non protetta.

Gli utenti finali interessati potrebbero dover collaborare con il personale IT per comprendere i nuovi requisiti e per scegliere password sicure.

> [!NOTE]
> Azure AD Password Protection non ha alcun controllo sul messaggio di errore specifico visualizzato dal computer client quando viene rifiutata una password vulnerabile.

## <a name="next-steps"></a>Passaggi successivi

Per personalizzare l'elenco delle password escluse per la propria organizzazione, vedere [configurare l'elenco di password per la protezione da Azure ad password Banned personalizzato](tutorial-configure-custom-password-protection.md).

Per monitorare gli eventi locali, vedere [Monitoring on-premid Azure ad Password Protection](howto-password-ban-bad-on-premises-monitor.md).
