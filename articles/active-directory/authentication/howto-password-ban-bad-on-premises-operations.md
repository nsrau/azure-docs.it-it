---
title: Abilitare la protezione con password di Azure AD localeEnable on-premises Azure AD Password Protection
description: Informazioni su come abilitare La protezione con password di Azure AD per un ambiente Servizi di dominio Active Directory locale
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
ms.openlocfilehash: 4ee0f3d89d48b23db48e3bf4b78203b09fbcbdbd
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80652627"
---
# <a name="enable-on-premises-azure-active-directory-password-protection"></a>Abilitare la protezione con password di Azure Active Directory localeEnable on-premises Azure Active Directory Password Protection

Gli utenti spesso creano password che utilizzano parole locali comuni, ad esempio una scuola, una squadra sportiva o una persona famosa. Queste password sono facili da indovinare e deboli contro gli attacchi basati sul dizionario. Per applicare password complesse nell'organizzazione, Azure Active Directory (Azure AD) Password Protection fornisce un elenco di password non crittografate globali e personalizzate. Una richiesta di modifica della password ha esito negativo se è presente una corrispondenza in questi elenco di password non soddisfatte.

Per proteggere l'ambiente Servizi di dominio Active Directory locale, è possibile installare e configurare Azure AD Password Protection per l'utilizzo con il controller di dominio locale. Questo articolo illustra come abilitare Azure AD Password Protection per l'ambiente locale.

Per altre informazioni sul funzionamento di Azure AD Password Protection in un ambiente locale, vedere [Come applicare Azure AD Password Protection per Windows Server Active Directory.](concept-password-ban-bad-on-premises.md)

## <a name="before-you-begin"></a>Prima di iniziare

Questo articolo illustra come abilitare Azure AD Password Protection per l'ambiente locale. Prima di completare questo articolo, [installare e registrare il servizio proxy di Azure AD Password Protection e](howto-password-ban-bad-on-premises-deploy.md) gli agenti controller di dominio nell'ambiente di Servizi di dominio Active Directory locale.

## <a name="enable-on-premises-password-protection"></a>Abilitare la protezione con password localeEnable on-premises password protection

1. Accedere al [portale](https://portal.azure.com) di Azure e passare a Metodi > di**autenticazione**di**sicurezza** > di **Azure Active Directory** > **Protezione con password**.
1. Impostare l'opzione **Abilita protezione con password in Windows Server Active Directory** su *Sì*.

    Quando questa impostazione è impostata su *No*, tutti gli agenti del controller di dominio di Protezione password di Azure AD distribuiti passano in modalità non interattiva in cui tutte le password vengono accettate così come sono. Non viene eseguita alcuna attività di convalida e gli eventi di controllo non vengono generati.

1. Si consiglia di impostare inizialmente la **modalità** *di controllo*. Dopo aver familiarità con la funzionalità e l'impatto sugli utenti dell'organizzazione, è possibile impostare la **modalità** su *Applicato*. Per ulteriori informazioni, vedere la sezione seguente sulle [modalità di funzionamento](#modes-of-operation).
1. Al termine, selezionare **Salva**.

    [![](media/howto-password-ban-bad-on-premises-operations/enable-configure-custom-banned-passwords-cropped.png "Enable on-premises password protection under Authentication Methods in the Azure portal")](media/howto-password-ban-bad-on-premises-operations/enable-configure-custom-banned-passwords.png#lightbox)

## <a name="modes-of-operation"></a>Modalità di funzionamento

Quando si abilita Protezione password di Azure AD locale, è possibile usare la modalità di *controllo* o *applicare la* modalità. È consigliabile che la distribuzione iniziale e il test inizino sempre in modalità di controllo. Le voci nel registro eventi devono quindi essere monitorate per prevedere se eventuali processi operativi esistenti verrebbero disturbati una volta attivata la modalità *di imposizione.*

### <a name="audit-mode"></a>Modalità di controllo

*La* modalità di controllo è intesa come un modo per eseguire il software in modalità "what if". Ogni servizio agente azure AD Password Protection CONTROLLER valuta una password in ingresso in base ai criteri attualmente attivi.

Se il criterio corrente è configurato per essere in modalità di controllo, le password "cattive" generano messaggi del registro eventi, ma vengono elaborate e aggiornate. Questo comportamento è l'unica differenza tra la modalità di controllo e la modalità di imposizione. Tutte le altre operazioni vengono eseguite allo stesso modo.

### <a name="enforced-mode"></a>Modalità applicata

La modalità *applicata* è intesa come configurazione finale. Come in modalità di controllo, ogni servizio agente Azure AD Password Protection DC valuta le password in ingresso in base ai criteri attualmente attivi. Quando la modalità applicata è abilitata, tuttavia, una password considerata non sicura in base al criterio viene rifiutata.

Quando una password viene rifiutata in modalità applicata dall'agente del controller di dominio di Azure AD Password Protection, un utente finale visualizza un errore simile a quello che vedrebbe se la password è stata rifiutata dall'imposizione della complessità della password locale tradizionale. Ad esempio, un utente potrebbe visualizzare il seguente messaggio di errore tradizionale nella schermata di accesso o di modifica della password di Windows:

*"Impossibile aggiornare la password. Il valore fornito per la nuova password non soddisfa i requisiti di lunghezza, complessità o cronologia del dominio."*

Questo messaggio è solo un esempio dei possibili risultati. Il messaggio di errore specifico può variare a seconda del software o dello scenario effettivo che sta tentando di impostare una password non sicura.

Gli utenti finali interessati potrebbero dover collaborare con il personale IT per comprendere i nuovi requisiti e scegliere password sicure.

> [!NOTE]
> Azure AD Password Protection non ha alcun controllo sul messaggio di errore specifico visualizzato dal computer client quando viene rifiutata una password debole.

## <a name="next-steps"></a>Passaggi successivi

Per personalizzare l'elenco delle password non in vita, vedere [Configurare l'elenco di password escluse personalizzato](tutorial-configure-custom-password-protection.md)di Azure AD Password Protection .

Per monitorare gli eventi puntoi, vedere Monitoraggio in base [alla protezione password](howto-password-ban-bad-on-premises-monitor.md)di Azure AD in un server precedente .
