---
title: Come gestire dei dispositivi tramite il portale di Azure | Microsoft Docs
description: Informazioni su come usare il portale di Azure per gestire i dispositivi.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: 03c9f0908d8b5290dc4585a330a7ea78a6577ab9
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/10/2019
ms.locfileid: "68942962"
---
# <a name="manage-device-identities-using-the-azure-portal"></a>Gestire le identità dei dispositivi usando il portale di Azure

Con la gestione delle identità del dispositivo in Azure Active Directory (Azure AD), è possibile assicurarsi che gli utenti accedano alle risorse da dispositivi che soddisfano gli standard di sicurezza e conformità.

Questo articolo:

- Si presuppone che l'utente abbia familiarità con l' [Introduzione alla gestione delle identità dei dispositivi in Azure Active Directory](overview.md)
- Fornisce informazioni sulla gestione delle identità del dispositivo tramite il portale di Azure AD

## <a name="manage-device-identities"></a>Gestire le identità dei dispositivi

Il portale di Azure AD offre una posizione centralizzata per la gestione delle identità dei dispositivi. È possibile accedere a questa posizione usando un [collegamento diretto](https://portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/Devices) o eseguendo la procedura manuale seguente:

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore.
2. Sulla barra di spostamento a sinistra fare clic su **Active Directory**.

   ![Configura impostazioni dispositivo](./media/device-management-azure-portal/01.png)

3. Nella sezione **Gestisci** fare clic su **Dispositivi**.

   ![Configura impostazioni dispositivo](./media/device-management-azure-portal/74.png)

La pagina **Dispositivi** consente di:

- Configurare le impostazioni dei dispositivi
- Individuare i dispositivi
- Eseguire attività di gestione delle identità del dispositivo
- Esaminare i log di controllo relativi al dispositivo  
  
## <a name="configure-device-settings"></a>Configura impostazioni dispositivo

Per gestire le identità del dispositivo tramite il portale di Azure AD, è necessario che i dispositivi siano [registrati o aggiunti](overview.md) a Azure ad. Un amministratore può ottimizzare il processo di registrazione e aggiunta dei dispositivi configurando le relative impostazioni.

![Configura impostazioni dispositivo](./media/device-management-azure-portal/22.png)

La pagina delle impostazioni dei dispositivi consente di configurare:

![Gestire un dispositivo Intune](./media/device-management-azure-portal/21.png)

- **Gli utenti possono aggiungere dispositivi a Azure ad** . questa impostazione consente di selezionare gli utenti che possono registrare i propri dispositivi come Azure ad dispositivi aggiunti. L'impostazione predefinita è **Tutti**.

> [!NOTE]
> **Gli utenti possono aggiungere dispositivi a Azure ad** impostazione è applicabile solo a Azure ad join in Windows 10.

- **Amministratori locali aggiuntivi su dispositivi aggiunti ad Azure AD**: è possibile selezionare gli utenti a cui vengono concessi i diritti di amministratore locale per un dispositivo. Gli utenti aggiunti in questa posizione vengono aggiunti al ruolo *Amministratori di dispositivi* in Azure AD. Agli amministratori globali di Azure AD e ai proprietari dei dispositivi vengono concessi i diritti di amministratore locale per impostazione predefinita. Questa opzione è una funzionalità dell'edizione Premium disponibile tramite prodotti come Azure AD Premium o Enterprise Mobility Suite (EMS).
- **Gli utenti possono registrare i propri dispositivi con Azure ad** : è necessario configurare questa impostazione per consentire la registrazione dei dispositivi Windows 10 Personal, iOS, Android e macOs con Azure ad. Se si seleziona **nessuno**, i dispositivi non possono eseguire la registrazione con Azure ad. L'iscrizione a Microsoft Intune o Gestione dispositivi mobili per Office 365 richiede la registrazione. Se è stato configurato uno di questi servizi, è selezionata l'opzione **TUTTI** e l'opzione **NESSUNO** non è disponibile.
- **Richiedi autenticazione a più fattori per aggiungere dispositivi** : è possibile scegliere se gli utenti devono fornire un ulteriore fattore di autenticazione per aggiungere il dispositivo a Azure ad. Il valore predefinito è **No**. Si consiglia di richiedere l'autenticazione a più fattori quando si registra un dispositivo. Per abilitare l'autenticazione a più fattori per questo servizio, è necessario verificare che tale tipo di autenticazione sia configurato per gli utenti che registrano i dispositivi. Per altre informazioni sui diversi servizi di autenticazione a più fattori di Azure, vedere [Introduzione ad Azure Multi-Factor Authentication](../authentication/concept-mfa-whichversion.md). 

> [!NOTE]
> **Richiedi autenticazione a più fattori per aggiungere** l'impostazione dispositivi non si applica ai dispositivi ibridi Azure ad aggiunti.

- **Numero massimo di dispositivi per utente**: questa impostazione consente di selezionare il numero massimo di dispositivi che un utente può avere in Azure AD. Se un utente raggiunge la quota specificata, non potrà aggiungere altri dispositivi fino a quando non vengono rimossi uno o più dispositivi esistenti. La quota del dispositivo viene conteggiata per tutti i dispositivi Azure AD aggiunti o Azure AD registrati oggi. Il valore predefinito è **20**.

> [!NOTE]
> L'impostazione **numero massimo di dispositivi** non si applica ai dispositivi ibridi Azure ad aggiunti.

- **Gli utenti possono sincronizzare le impostazioni e i dati delle app su tutti i dispositivi**: per impostazione predefinita, il valore di questa opzione è **Nessuno**. Se si selezionano utenti o gruppi specifici oppure l'opzione TUTTI, si consente la sincronizzazione delle impostazioni dell'utente e dei dati delle app tra i dispositivi Windows 10. Leggere altre informazioni sul funzionamento della sincronizzazione in Windows 10.
Questa opzione è una funzionalità Premium disponibile tramite prodotti come Azure AD Premium o Enterprise Mobility Suite (EMS).

## <a name="locate-devices"></a>Individuare i dispositivi

Sono disponibili due opzioni per individuare i dispositivi registrati e aggiunti:

- **Tutti i dispositivi** nella sezione **Gestisci** della pagina **Dispositivi**  

   ![Tutti i dispositivi](./media/device-management-azure-portal/41.png)

- **Dispositivi** nella sezione **Gestisci** di una pagina **Utente**

   ![Tutti i dispositivi](./media/device-management-azure-portal/43.png)

Con entrambe le opzioni, è possibile ottenere una vista che:

- Consente di cercare i dispositivi usando il nome visualizzato o l'ID dispositivo come filtro.
- Fornisce una panoramica dettagliata dei dispositivi registrati e aggiunti
- Consente di eseguire attività comuni di gestione dei dispositivi

![Tutti i dispositivi](./media/device-management-azure-portal/51.png)

>[!TIP]
>
>* Se viene visualizzato un dispositivo "Azure AD ibrido aggiunto" con uno stato "in sospeso" nella colonna registrata, significa che il dispositivo è stato sincronizzato da Azure AD connettersi ed è in attesa di completare la registrazione dal client. Ulteriori informazioni su come [pianificare l'implementazione di Azure ad ibrido join](hybrid-azuread-join-plan.md). Ulteriori informazioni sono disponibili nell'articolo [domande frequenti sui dispositivi](faq.md).
>
>   ![Dispositivi in sospeso](./media/device-management-azure-portal/75.png)
>
>* Per alcuni dispositivi iOS, i nomi dei dispositivi contenenti apostrofi possono usare caratteri potenzialmente diversi, simili ad apostrofi. Quindi, la ricerca di tali dispositivi è un po' complicata. se non vengono visualizzati correttamente i risultati della ricerca, assicurarsi che la stringa di ricerca contenga il carattere apostrofo corrispondente.

## <a name="device-identity-management-tasks"></a>Attività di gestione delle identità dei dispositivi

In qualità di amministratore globale o amministratore di dispositivi cloud, è possibile gestire i dispositivi registrati o aggiunti. Gli amministratori del servizio Intune possono:

- Aggiornare i dispositivi: esempi sono operazioni quotidiane, quali abilitazione/disabilitazione di dispositivi
- Eliminare i dispositivi: quando un dispositivo viene ritirato e deve essere eliminato in Azure AD

Questa sezione fornisce informazioni sulle attività comuni di gestione delle identità dei dispositivi.

### <a name="manage-an-intune-device"></a>Gestire un dispositivo Intune

Un amministratore di Intune può gestire i dispositivi contrassegnati come **Microsoft Intune**. Se il dispositivo non è registrato con Microsoft Intune l'opzione "Gestisci" sarà disattivata.

![Gestire un dispositivo Intune](./media/device-management-azure-portal/31.png)

### <a name="enable--disable-an-azure-ad-device"></a>Abilitare o disabilitare un dispositivo Azure AD

Per abilitare o disabilitare un dispositivo, le opzioni disponibili sono due:

- Il menu Attività ("...") nella pagina **Tutti i dispositivi**

   ![Gestire un dispositivo Intune](./media/device-management-azure-portal/71.png)

- La barra degli strumenti nella pagina **Dispositivi**

   ![Gestire un dispositivo Intune](./media/device-management-azure-portal/32.png)

**Osservazioni:**

- Per abilitare o disabilitare un dispositivo, è necessario essere un amministratore globale o un amministratore di dispositivi cloud in Azure AD. 
- La disabilitazione di un dispositivo impedisce l'autenticazione del dispositivo con Azure AD, impedendo così al dispositivo di accedere alle risorse Azure AD sorvegliate dalla CA del dispositivo o usando le credenziali di WH4B.

### <a name="delete-an-azure-ad-device"></a>Eliminare un dispositivo Azure AD

Per eliminare un dispositivo, le opzioni disponibili sono due:

- Il menu Attività ("...") nella pagina **Tutti i dispositivi**

   ![Gestire un dispositivo Intune](./media/device-management-azure-portal/72.png)

- La barra degli strumenti nella pagina **Dispositivi**

   ![Eliminare un dispositivo](./media/device-management-azure-portal/34.png)

**Osservazioni:**

- Per eliminare un dispositivo, è necessario essere un amministratore globale o un amministratore di Intune in Azure AD.
- L'eliminazione di un dispositivo comporta quanto segue:
   - Impedisce l'accesso del dispositivo alle risorse di Azure AD.
   - Rimuove tutti i dettagli collegati al dispositivo, ad esempio, le chiavi BitLocker per i dispositivi Windows.  
   - È un'attività non reversibile e non è consigliata, a meno che non sia necessaria.

Se un dispositivo è gestito da un'altra autorità di gestione (ad esempio, Microsoft Intune), assicurarsi che il dispositivo sia stato cancellato/ritirato prima di eliminare il dispositivo Azure AD. Vedere come [gestire i dispositivi non aggiornati](device-management-azure-portal.md) prima di eliminare i dispositivi.

### <a name="view-or-copy-device-id"></a>Visualizzare o copiare l'ID dispositivo

È possibile usare l'ID di un dispositivo per verificare i relativi dettagli nel dispositivo o per l'uso in PowerShell in fase di risoluzione dei problemi. Per accedere all'opzione di copia, fare clic sul dispositivo.

![Visualizzare l'ID dispositivo](./media/device-management-azure-portal/35.png)
  
### <a name="view-or-copy-bitlocker-keys"></a>Visualizzare o copiare le chiavi BitLocker

È possibile visualizzare e copiare le chiavi BitLocker per aiutare gli utenti a recuperare l'unità crittografata. Queste chiavi sono disponibili solo per i dispositivi Windows crittografati e le cui chiavi sono archiviate in Azure AD. È possibile copiare queste chiavi quando si accede ai dettagli del dispositivo.

![Visualizzare le chiavi BitLocker](./media/device-management-azure-portal/36.png)

Per visualizzare o copiare le chiavi BitLocker, è necessario essere il proprietario del dispositivo o un utente con almeno uno dei ruoli seguenti assegnati:

- Amministratore dispositivo cloud
- Amministratore globale
- Amministratore supporto tecnico
- Amministratore del servizio Intune
- Amministratore della protezione
- Ruolo con autorizzazioni di lettura per la sicurezza

> [!NOTE]
> I dispositivi Windows 10 aggiunti ad Azure AD ibrido non hanno un proprietario. Quindi, se si sta cercando un dispositivo in base al proprietario e non lo si trova, eseguire la ricerca in base all'ID del dispositivo.

## <a name="audit-logs"></a>Log di controllo

Le attività del dispositivo sono disponibili tramite i log attività. Questi log includono le attività avviate dal servizio di registrazione dei dispositivi e dagli utenti:

- Creazione di un dispositivo e aggiunta di proprietari/utenti nel dispositivo
- Modifiche alle impostazioni del dispositivo
- Funzionamento del dispositivo, ad esempio eliminazione o aggiornamento

Il punto di ingresso ai dati di controllo è **Log di controllo** nella sezione **Attività** della pagina **Dispositivi**.

![Log di controllo](./media/device-management-azure-portal/61.png)

Un log di controllo è una visualizzazione elenco predefinita che include:

- Data e ora dell'occorrenza
- Destinazioni
- Iniziatore/attore di un'attività (chi)
- Attività (cosa)

![Log di controllo](./media/device-management-azure-portal/63.png)

Per personalizzare la visualizzazione elenco, fare clic su **Colonne** nella barra degli strumenti.

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
