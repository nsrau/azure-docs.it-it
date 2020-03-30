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
ms.openlocfilehash: e09de5911ca0946bfcbcb77d1ad4131c8feac9f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262243"
---
# <a name="manage-device-identities-using-the-azure-portal"></a>Gestire le identità dei dispositivi tramite il portale di AzureManage device identities using the Azure portal

Con la gestione delle identità dei dispositivi in Azure Active Directory (Azure AD), è possibile garantire che gli utenti accedano alle risorse da dispositivi che soddisfano gli standard di sicurezza e conformità.

Questo articolo:

- Si presuppone che si abbia familiarità con [l'introduzione alla gestione delle identità](overview.md) dei dispositivi in Azure Active Directory
- Fornisce informazioni sulla gestione delle identità del dispositivo tramite il portale di Azure AD

## <a name="manage-device-identities"></a>Gestire le identità dei dispositivi

Il portale di Azure AD offre una posizione centrale per gestire le identità del dispositivo. È possibile raggiungere questo luogo utilizzando un [link diretto](https://portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/Devices) o:

1. Accedere al [portale](https://portal.azure.com)di Azure .
1. Passare a**Dispositivi** **di Azure Active Directory** > .

La pagina **Dispositivi** consente di:

- Configurare le impostazioni dei dispositivi
- Individuare i dispositivi
- Eseguire attività di gestione delle identità dei dispositivi
- Esaminare i log di controllo correlati al dispositivoReview device-related audit logs  
  
## <a name="configure-device-settings"></a>Configurare le impostazioni dei dispositivi

Per gestire le identità dei dispositivi tramite il portale di Azure AD, i dispositivi devono essere [registrati o aggiunti](overview.md) ad Azure AD. Un amministratore può ottimizzare il processo di registrazione e aggiunta dei dispositivi configurando le relative impostazioni.

La pagina delle impostazioni del dispositivo consente di configurare le impostazioni relative alle identità del dispositivo:The device settings page enables you to configure settings related to device identities:

![Gestire un dispositivo Intune](./media/device-management-azure-portal/21.png)

- **Gli utenti possono aggiungere dispositivi ad Azure AD:** questa impostazione consente di selezionare gli utenti che possono registrare i dispositivi come dispositivi aggiunti ad Azure AD. Il valore predefinito è **All**.

> [!NOTE]
> **Gli utenti possono aggiungere dispositivi** all'impostazione di Azure AD è applicabile solo all'aggiunta ad Azure AD in Windows 10.Users may join devices to Azure AD setting is only applicable to Azure AD join on Windows 10.

- **Amministratori locali aggiuntivi su dispositivi aggiunti ad Azure AD**: è possibile selezionare gli utenti a cui vengono concessi i diritti di amministratore locale per un dispositivo. Gli utenti aggiunti in questa posizione vengono aggiunti al ruolo *Amministratori di dispositivi* in Azure AD. Agli amministratori globali di Azure AD e ai proprietari dei dispositivi vengono concessi i diritti di amministratore locale per impostazione predefinita. Questa opzione è una funzionalità dell'edizione Premium disponibile tramite prodotti come Azure AD Premium o Enterprise Mobility Suite (EMS).
- **Gli utenti possono registrare** i propri dispositivi con Azure AD: è necessario configurare questa impostazione per consentire ai dispositivi personali, iOS, Android e macOs di Windows 10 di essere registrati con Azure AD. Se si seleziona **Nessuno**, i dispositivi non possono registrarsi con Azure AD. L'iscrizione a Microsoft Intune o Gestione dispositivi mobili per Office 365 richiede la registrazione. Se è stato configurato uno di questi servizi, è selezionata l'opzione **TUTTI** e l'opzione **NESSUNO** non è disponibile.
- **Richiedi autenticazione a più fattori per l'aggiunta** di dispositivi: è possibile scegliere se gli utenti devono fornire un fattore di autenticazione aggiuntivo per aggiungere il dispositivo ad Azure AD. Il valore predefinito è **No**. Si consiglia di richiedere l'autenticazione a più fattori quando si registra un dispositivo. Per abilitare l'autenticazione a più fattori per questo servizio, è necessario verificare che tale tipo di autenticazione sia configurato per gli utenti che registrano i dispositivi. Per altre informazioni sui diversi servizi di autenticazione a più fattori di Azure, vedere [Introduzione ad Azure Multi-Factor Authentication](../authentication/concept-mfa-whichversion.md). 

> [!NOTE]
> **L'impostazione Richiedi autenticazione a più fattori per l'aggiunta** di dispositivi si applica ai dispositivi aggiunti ad Azure AD o registrati in Azure AD. Questa impostazione non si applica ai dispositivi ibridi aggiunti ad Azure AD.

- **Numero massimo di dispositivi:** questa impostazione consente di selezionare il numero massimo di dispositivi registrati di Azure AD o Azure AD che un utente può avere in Azure AD. Se un utente raggiunge la quota specificata, non potrà aggiungere altri dispositivi fino a quando non vengono rimossi uno o più dispositivi esistenti. Il valore predefinito è **20**.

> [!NOTE]
> **L'impostazione Numero massimo di dispositivi** si applica ai dispositivi aggiunti ad Azure AD o registrati in Azure AD. Questa impostazione non si applica ai dispositivi ibridi aggiunti ad Azure AD.

- **Gli utenti possono sincronizzare le impostazioni e i dati delle app su tutti i dispositivi**: per impostazione predefinita, il valore di questa opzione è **Nessuno**. Se si selezionano utenti o gruppi specifici oppure l'opzione TUTTI, si consente la sincronizzazione delle impostazioni dell'utente e dei dati delle app tra i dispositivi Windows 10. Leggere altre informazioni sul funzionamento della sincronizzazione in Windows 10.
Questa opzione è una funzionalità Premium disponibile tramite prodotti come Azure AD Premium o Enterprise Mobility Suite (EMS).

## <a name="locate-devices"></a>Individuare i dispositivi

Sono disponibili due opzioni per individuare i dispositivi registrati e aggiunti:

- **Tutti i dispositivi** nella sezione **Gestisci** della pagina **Dispositivi**  
- **Dispositivi** nella sezione **Gestisci** di una pagina **Utente**

Con entrambe le opzioni, è possibile ottenere una vista che:

- Consente di cercare i dispositivi utilizzando il nome visualizzato o l'ID del dispositivo come filtro.
- Fornisce una panoramica dettagliata dei dispositivi registrati e aggiunti
- Consente di eseguire attività comuni di gestione dei dispositivi

![Tutti i dispositivi](./media/device-management-azure-portal/51.png)

>[!TIP]
>
>* Se viene visualizzato un dispositivo "Aggiunta ad Azure AD ibrido" con lo stato "In sospeso" nella colonna REGISTRO, indica che il dispositivo è stato sincronizzato da Azure AD Connect ed è in attesa di completare la registrazione dal client. Altre informazioni su come [pianificare l'implementazione](hybrid-azuread-join-plan.md)di join di Azure AD ibrido. Ulteriori informazioni possono essere trovate nell'articolo, [Dispositivi domande frequenti](faq.md).
>
>   ![Dispositivi in sospeso](./media/device-management-azure-portal/75.png)
>
>* Per alcuni dispositivi iOS, i nomi dei dispositivi contenenti apostrofi possono usare caratteri potenzialmente diversi, simili ad apostrofi. Quindi la ricerca di tali dispositivi è un po 'complicato - se non vedi correttamente i risultati della ricerca, assicurati che la stringa di ricerca contenga un carattere apostrofo corrispondente.

## <a name="device-identity-management-tasks"></a>Attività di gestione delle identità dei dispositiviDevice identity management tasks

In qualità di amministratore globale o amministratore di dispositivi cloud, puoi gestire i dispositivi registrati o aggiunti. Gli amministratori del servizio Intune possono:

- Aggiornare i dispositivi: esempi sono operazioni quotidiane, quali abilitazione/disabilitazione di dispositivi
- Eliminare i dispositivi: quando un dispositivo viene ritirato e deve essere eliminato in Azure AD

In questa sezione vengono fornite informazioni sulle attività comuni di gestione delle identità dei dispositivi.

### <a name="manage-an-intune-device"></a>Gestire un dispositivo Intune

Un amministratore di Intune può gestire i dispositivi contrassegnati come **Microsoft Intune**. Se il dispositivo non è registrato con Microsoft Intune, l'opzione "Gestisci" verrà visualizzata in grigio.

![Gestire un dispositivo Intune](./media/device-management-azure-portal/31.png)

### <a name="enable--disable-an-azure-ad-device"></a>Abilitare o disabilitare un dispositivo Azure AD

Per abilitare o disabilitare un dispositivo, le opzioni disponibili sono due:

- Il menu Attività ("...") nella pagina **Tutti i dispositivi**

   ![Gestire un dispositivo Intune](./media/device-management-azure-portal/71.png)

- La barra degli strumenti nella pagina **Dispositivi**

   ![Gestire un dispositivo Intune](./media/device-management-azure-portal/32.png)

**Note:**

- È necessario essere un amministratore globale o un amministratore di dispositivi cloud in Azure AD per abilitare/disabilitare un dispositivo. 
- La disabilitazione di un dispositivo impedisce a un dispositivo di eseguire correttamente l'autenticazione con Azure AD, impedendo in tal modo al dispositivo di accedere alle risorse di Azure AD che sono sorvegliate dalla CA del dispositivo o che usano le credenziali WH4B.
- La disabilitazione del dispositivo revocherà sia il token di aggiornamento primario (PRT) che qualsiasi token di aggiornamento (RT) nel dispositivo.

### <a name="delete-an-azure-ad-device"></a>Eliminare un dispositivo Azure AD

Per eliminare un dispositivo, le opzioni disponibili sono due:

- Il menu Attività ("...") nella pagina **Tutti i dispositivi**

   ![Gestire un dispositivo Intune](./media/device-management-azure-portal/72.png)

- La barra degli strumenti nella pagina **Dispositivi**

   ![Eliminare un dispositivo](./media/device-management-azure-portal/34.png)

**Note:**

- Per eliminare un dispositivo, è necessario essere un amministratore globale o un amministratore di Intune in Azure AD.
- L'eliminazione di un dispositivo comporta quanto segue:
   - Impedisce l'accesso del dispositivo alle risorse di Azure AD.
   - Rimuove tutti i dettagli collegati al dispositivo, ad esempio, le chiavi BitLocker per i dispositivi Windows.  
   - È un'attività non reversibile e non è consigliata, a meno che non sia necessaria.

Se un dispositivo è gestito da un'altra autorità di gestione (ad esempio, Microsoft Intune), assicurarsi che il dispositivo sia stato cancellato/ritirato prima di eliminare il dispositivo in Azure AD. Esaminare come gestire i [dispositivi obsoleti](device-management-azure-portal.md) prima di eliminarli.

### <a name="view-or-copy-device-id"></a>Visualizzare o copiare l'ID dispositivo

È possibile usare l'ID di un dispositivo per verificare i relativi dettagli nel dispositivo o per l'uso in PowerShell in fase di risoluzione dei problemi. Per accedere all'opzione di copia, fare clic sul dispositivo.

![Visualizzare l'ID dispositivo](./media/device-management-azure-portal/35.png)
  
### <a name="view-or-copy-bitlocker-keys"></a>Visualizzare o copiare le chiavi BitLocker

È possibile visualizzare e copiare le chiavi BitLocker per aiutare gli utenti a recuperare l'unità crittografata. Queste chiavi sono disponibili solo per i dispositivi Windows crittografati e le cui chiavi sono archiviate in Azure AD. È possibile copiare queste chiavi quando si accede ai dettagli del dispositivo.

![Visualizzare le chiavi BitLocker](./media/device-management-azure-portal/36.png)

Per visualizzare o copiare le chiavi BitLocker, è necessario essere il proprietario del dispositivo o un utente con almeno uno dei ruoli seguenti assegnati:

- Amministratore dispositivo cloud
- Amministratore globale
- Amministratore del supporto tecnico
- Amministratore del servizio Intune
- Amministratore della sicurezza
- Ruolo con autorizzazioni di lettura per la sicurezza

> [!NOTE]
> I dispositivi Windows 10 aggiunti ad Azure AD ibrido non hanno un proprietario. Quindi, se si sta cercando un dispositivo in base al proprietario e non lo si trova, eseguire la ricerca in base all'ID del dispositivo.

## <a name="audit-logs"></a>Log di controllo

Le attività del dispositivo sono disponibili tramite i log attività. Questi log includono le attività attivate dal servizio di registrazione del dispositivo e dagli utenti:These logs include activities triggered by the device registration service and by users:

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

È possibile personalizzare la visualizzazione elenco facendo clic su **Colonne** nella barra degli strumenti.

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

[Come gestire i dispositivi non aggiornati in Azure ADHow to manage stale devices in Azure AD](manage-stale-devices.md)
