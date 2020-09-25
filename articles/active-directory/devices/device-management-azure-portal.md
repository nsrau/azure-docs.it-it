---
title: Come gestire dei dispositivi tramite il portale di Azure | Microsoft Docs
description: Informazioni su come usare il portale di Azure per gestire i dispositivi.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: how-to
ms.date: 09/16/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: hafowler
ms.collection: M365-identity-device-management
ms.openlocfilehash: e76c5f51486f7c1673fb5359eabc99ee41130472
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91278857"
---
# <a name="manage-device-identities-using-the-azure-portal"></a>Gestire le identità dei dispositivi usando il portale di Azure

Azure AD offre una posizione centralizzata per la gestione delle identità dei dispositivi.

La pagina **tutti i dispositivi** consente di:

- Identificare i dispositivi, tra cui:
   - Dispositivi aggiunti o registrati in Azure AD.
   - Dispositivi distribuiti con [Windows Autopilot](/windows/deployment/windows-autopilot/windows-autopilot).
   - Stampanti che usano la [stampa universale](/universal-print/fundamentals/universal-print-getting-started)
- Eseguire attività di gestione delle identità del dispositivo come Enable, Disable, DELETE o Manage.
   - Le [stampanti](/universal-print/fundamentals/) e i dispositivi [Windows Autopilot](/windows/deployment/windows-autopilot/windows-autopilot) hanno opzioni di gestione limitate in Azure ad. Devono essere gestiti dalle rispettive interfacce di amministrazione.
- Configurare le impostazioni di identità del dispositivo.
- Abilitare o disabilitare Enterprise State Roaming.
- Esaminare i log di controllo relativi al dispositivo

[![Visualizzazione tutti i dispositivi nella portale di Azure](./media/device-management-azure-portal/all-devices-azure-portal.png)](./media/device-management-azure-portal/all-devices-azure-portal.png#lightbox)

È possibile accedere al portale per i dispositivi attenendosi alla procedura seguente:

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Passare a **Azure Active Directory**  >  **dispositivi**.

## <a name="manage-devices"></a>Gestire dispositivi

Sono disponibili due posizioni per gestire i dispositivi in Azure AD:

- **Portale di Azure**  >  **Azure Active Directory**  >  **Dispositivi**
- **Portale di Azure**  >  **Azure Active Directory**  >  **Utenti** > selezionare un utente > **dispositivi**

Entrambe le opzioni consentono agli amministratori di:

- Cercare i dispositivi.
- Vedere i dettagli del dispositivo, tra cui:
    - Nome del dispositivo
    - ID dispositivo
    - Sistema operativo e versione
    - Tipo di join
    - Proprietario
    - Conformità e gestione dei dispositivi mobili
    - Chiave di ripristino di BitLocker
- Eseguire attività di gestione delle identità del dispositivo come, abilitare, disabilitare, eliminare o gestire.
   - Le [stampanti](/universal-print/fundamentals/) e i dispositivi [Windows Autopilot](/windows/deployment/windows-autopilot/windows-autopilot) hanno opzioni di gestione limitate in Azure ad. Devono essere gestiti dalle rispettive interfacce di amministrazione.

> [!TIP]
> - I dispositivi Windows 10 aggiunti ad Azure AD ibrido non hanno un proprietario. Se si sta cercando un dispositivo in base al proprietario e non è stato trovato, eseguire una ricerca in base all'ID del dispositivo.
>
> - Se viene visualizzato un dispositivo "Azure AD ibrido aggiunto" con uno stato "in sospeso" nella colonna registrata, significa che il dispositivo è stato sincronizzato da Azure AD connettersi ed è in attesa di completare la registrazione dal client. Ulteriori informazioni su come [pianificare l'implementazione di Azure ad ibrido join](hybrid-azuread-join-plan.md). Ulteriori informazioni sono disponibili nell'articolo [domande frequenti sui dispositivi](faq.md).
>
> - Per alcuni dispositivi iOS, i nomi dei dispositivi contenenti apostrofi possono usare caratteri potenzialmente diversi, simili ad apostrofi. Quindi, la ricerca di tali dispositivi è un po' complicata. se non vengono visualizzati correttamente i risultati della ricerca, assicurarsi che la stringa di ricerca contenga il carattere apostrofo corrispondente.

### <a name="manage-an-intune-device"></a>Gestire un dispositivo Intune

Se si è un amministratore di Intune, è possibile gestire i dispositivi in cui MDM è contrassegnato come **Microsoft Intune**. Se il dispositivo non è registrato con Microsoft Intune, l'opzione "Gestisci" sarà disattivata.

### <a name="enable-or-disable-an-azure-ad-device"></a>Abilitare o disabilitare un dispositivo Azure AD

Per abilitare o disabilitare i dispositivi, sono disponibili due opzioni:

- Barra degli strumenti nella pagina **tutti i dispositivi** dopo aver selezionato uno o più dispositivi.
- Barra degli strumenti dopo il drill-down in un dispositivo specifico.

> [!IMPORTANT]
> - Per abilitare o disabilitare un dispositivo, è necessario essere un amministratore globale o un amministratore di dispositivi cloud in Azure AD. 
> - La disabilitazione di un dispositivo impedisce l'autenticazione del dispositivo con Azure AD, impedendo così al dispositivo di accedere alle risorse Azure AD protette dall'accesso condizionale basato su dispositivo o usando le credenziali di Windows Hello for business.
> - La disabilitazione di un dispositivo revoca sia il token di aggiornamento primario (PRT) sia i token di aggiornamento (RT) nel dispositivo.
> - Non è possibile abilitare o disabilitare le stampanti in Azure AD.

### <a name="delete-an-azure-ad-device"></a>Eliminare un dispositivo Azure AD

Per eliminare un dispositivo, le opzioni disponibili sono due:

- Barra degli strumenti nella pagina **tutti i dispositivi** dopo aver selezionato uno o più dispositivi.
- Barra degli strumenti dopo il drill-down in un dispositivo specifico.

> [!IMPORTANT]
> - Per eliminare un dispositivo, è necessario disporre dell'amministratore del dispositivo cloud, dell'amministratore di Intune o del ruolo di amministratore globale in Azure AD.
> - Non è possibile eliminare le stampanti e i dispositivi di Windows Autopilot in Azure AD
> - L'eliminazione di un dispositivo comporta quanto segue:
>    - Impedisce l'accesso del dispositivo alle risorse di Azure AD.
>    - Rimuove tutti i dettagli collegati al dispositivo, ad esempio, le chiavi BitLocker per i dispositivi Windows.  
>    - È un'attività non reversibile e non è consigliata, a meno che non sia necessaria.

Se un dispositivo è gestito da un'altra autorità di gestione (ad esempio, Microsoft Intune), assicurarsi che il dispositivo sia stato cancellato/ritirato prima di eliminare il dispositivo Azure AD. Vedere come [gestire i dispositivi non aggiornati](manage-stale-devices.md) prima di eliminare i dispositivi.

### <a name="view-or-copy-device-id"></a>Visualizzare o copiare l'ID dispositivo

È possibile usare l'ID di un dispositivo per verificare i relativi dettagli nel dispositivo o per l'uso in PowerShell in fase di risoluzione dei problemi. Per accedere all'opzione di copia, fare clic sul dispositivo.

![Visualizzare l'ID dispositivo](./media/device-management-azure-portal/35.png)
  
### <a name="view-or-copy-bitlocker-keys"></a>Visualizzare o copiare le chiavi BitLocker

È possibile visualizzare e copiare le chiavi BitLocker per consentire agli utenti di ripristinare le unità crittografate. Queste chiavi sono disponibili solo per i dispositivi Windows crittografati e le cui chiavi sono archiviate in Azure AD. È possibile trovare queste chiavi quando si accede ai dettagli di un dispositivo selezionando **Mostra chiave di ripristino**. Selezionando **Mostra chiave di ripristino** , viene generato un log di controllo, che è possibile trovare nella `KeyManagement` categoria.

![Visualizzare le chiavi BitLocker](./media/device-management-azure-portal/device-details-show-bitlocker-key.png)

Per visualizzare o copiare le chiavi BitLocker, è necessario essere il proprietario del dispositivo o un utente con almeno uno dei ruoli seguenti assegnati:

- Amministratore dispositivo cloud
- Amministratore globale
- Amministratore del supporto tecnico
- Amministratore del servizio Intune
- Amministratore della protezione
- Ruolo con autorizzazioni di lettura per la sicurezza

### <a name="device-list-filtering-preview"></a>Filtro elenco dispositivi (anteprima)

In precedenza era possibile filtrare l'elenco dei dispositivi solo per attività e stato abilitato. Questa anteprima consente ora di filtrare l'elenco di dispositivi in base ai seguenti attributi in un dispositivo:

- Stato abilitato
- Stato conforme
- Tipo di join (Azure AD aggiunto, Azure AD ibrido aggiunto, Azure AD registrato)
- Timestamp dell'attività
- OS
- Tipo di dispositivo (stampanti, VM sicure, dispositivi condivisi, dispositivi registrati)

Per abilitare la funzionalità di filtro anteprima nella visualizzazione **tutti i dispositivi** :

![Abilitare la funzionalità di anteprima del filtro](./media/device-management-azure-portal/device-filter-preview-enable.png)

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Passare a **Azure Active Directory**  >  **dispositivi**.
1. Selezionare il banner che afferma, **provare i nuovi miglioramenti del filtro dei dispositivi. Fare clic per abilitare l'anteprima.**

Sarà ora possibile **aggiungere filtri** alla visualizzazione **tutti i dispositivi** .

## <a name="configure-device-settings"></a>Configura impostazioni dispositivo

Per gestire le identità dei dispositivi tramite il portale di Azure AD, è necessario che i dispositivi siano [registrati o aggiunti](overview.md) a Azure ad. In qualità di amministratore, è possibile controllare il processo di registrazione e aggiunta dei dispositivi configurando le seguenti impostazioni del dispositivo.

È necessario disporre di uno dei ruoli seguenti per visualizzare o gestire le impostazioni del dispositivo nel portale di Azure:

- Amministratore globale
- Amministratore dispositivo cloud
- Ruolo con autorizzazioni di lettura globali
- Lettore di directory

![Impostazioni del dispositivo correlate all'Azure AD](./media/device-management-azure-portal/device-settings-azure-portal.png)

- **Gli utenti possono aggiungere dispositivi a Azure ad** . questa impostazione consente di selezionare gli utenti che possono registrare i propri dispositivi come Azure ad dispositivi aggiunti. Il valore predefinito è **All**.

> [!NOTE]
> **Gli utenti possono aggiungere dispositivi a Azure ad** impostazione è applicabile solo a Azure ad join in Windows 10.

- **Amministratori locali aggiuntivi su dispositivi aggiunti ad Azure AD**: è possibile selezionare gli utenti a cui vengono concessi i diritti di amministratore locale per un dispositivo. Questi utenti vengono aggiunti al ruolo di *amministratore del dispositivo* in Azure ad. Agli amministratori globali di Azure AD e ai proprietari dei dispositivi vengono concessi i diritti di amministratore locale per impostazione predefinita. Questa opzione è una funzionalità dell'edizione Premium disponibile tramite prodotti come Azure AD Premium o Enterprise Mobility Suite (EMS).
- **Gli utenti possono registrare i propri dispositivi con Azure ad** : è necessario configurare questa impostazione per consentire la registrazione dei dispositivi Windows 10 Personal, iOS, Android e macOS con Azure ad. Se si seleziona **nessuno**, i dispositivi non possono eseguire la registrazione con Azure ad. La registrazione con Microsoft Intune o la gestione di dispositivi mobili (MDM) per Microsoft 365 richiede la registrazione. Se è stato configurato uno di questi servizi, è selezionata l'opzione **TUTTI** e l'opzione **NESSUNO** non è disponibile.
- **Richiedi autenticazione a più fattori per aggiungere dispositivi** : è possibile scegliere se gli utenti devono fornire un ulteriore fattore di autenticazione per aggiungere il dispositivo a Azure ad. Il valore predefinito è **No**. Si consiglia di richiedere l'autenticazione a più fattori quando si registra un dispositivo. Per abilitare l'autenticazione a più fattori per questo servizio, è necessario verificare che tale tipo di autenticazione sia configurato per gli utenti che registrano i dispositivi. Per altre informazioni sui diversi servizi di autenticazione a più fattori di Azure, vedere [Introduzione ad Azure Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md). 

> [!NOTE]
> **Richiedi autenticazione a più fattori per aggiungere** l'impostazione dei dispositivi si applica ai dispositivi che sono Azure ad aggiunti o Azure ad registrati. Questa impostazione non si applica ai dispositivi ibridi Azure AD aggiunti.

- **Numero massimo di dispositivi** : questa impostazione consente di selezionare il numero massimo di dispositivi Azure ad aggiunti o Azure ad registrati che un utente può avere in Azure ad. Se un utente raggiunge la quota specificata, non potrà aggiungere altri dispositivi fino a quando non vengono rimossi uno o più dispositivi esistenti. Il valore predefinito è **50**.

> [!NOTE]
> L'impostazione **numero massimo di dispositivi** si applica ai dispositivi Azure ad aggiunti o Azure ad registrati. Questa impostazione non si applica ai dispositivi ibridi Azure AD aggiunti.

- [Enterprise State Roaming](enterprise-state-roaming-overview.md)

## <a name="audit-logs"></a>Log di controllo

Le attività del dispositivo sono disponibili tramite i log attività. Questi log includono le attività avviate dal servizio di registrazione dei dispositivi e dagli utenti:

- Creazione di un dispositivo e aggiunta di proprietari/utenti nel dispositivo
- Modifiche alle impostazioni del dispositivo
- Funzionamento del dispositivo, ad esempio eliminazione o aggiornamento

Il punto di ingresso ai dati di controllo è **Log di controllo** nella sezione **Attività** della pagina **Dispositivi**.

Il log di controllo ha una visualizzazione elenco predefinita che mostra:

- Data e ora dell'occorrenza
- Destinazioni
- Iniziatore/attore di un'attività (chi)
- Attività (cosa)

![Log di controllo](./media/device-management-azure-portal/63.png)

È possibile personalizzare la visualizzazione elenco facendo clic su **colonne** nella barra degli strumenti.

![Log di controllo](./media/device-management-azure-portal/64.png)

Per limitare i dati segnalati in base alle esigenze, è possibile filtrare i dati di controllo usando i campi seguenti:

- Category
- Activity resource type (Tipo di risorsa dell'attività)
- Attività
- Intervallo di date
- Destinazione
- Azione avviata da (attore)

Oltre a usare i filtri, è possibile cercare voci specifiche.

![Log di controllo](./media/device-management-azure-portal/65.png)

## <a name="next-steps"></a>Passaggi successivi

[Come gestire i dispositivi non aggiornati in Azure AD](manage-stale-devices.md)

[Enterprise State Roaming](enterprise-state-roaming-overview.md)