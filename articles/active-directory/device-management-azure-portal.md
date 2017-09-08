---
title: Gestione dei dispositivi tramite il portale di Azure - Anteprima | Microsoft Docs
description: Informazioni su come usare il portale di Azure per gestire i dispositivi.
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/24/2017
ms.author: markvi
ms.reviewer: jairoc
ms.translationtype: HT
ms.sourcegitcommit: 646886ad82d47162a62835e343fcaa7dadfaa311
ms.openlocfilehash: 4b46e1627a229b0649d9ccd2550cd28fda9849f8
ms.contentlocale: it-it
ms.lasthandoff: 08/24/2017

---
# <a name="managing-devices-using-the-azure-portal---preview"></a>Gestione dei dispositivi tramite il portale di Azure - Anteprima

>[!NOTE]
>Questa funzionalità è attualmente disponibile in anteprima pubblica. Potrebbe essere necessario ripristinare o rimuovere eventuali modifiche. La funzionalità è disponibile in tutte le sottoscrizioni di Azure Active Directory (Azure AD) durante l'anteprima pubblica. Quando la funzionalità sarà disponibile a livello generale, alcuni aspetti potrebbero tuttavia richiedere una sottoscrizione Azure Active Directory Premium.


Tramite la gestione dei dispositivi in Azure Active Directory (Azure AD) è possibile garantire che gli utenti accedano alle risorse da dispositivi che soddisfano gli standard di sicurezza e conformità. 

In questo argomento:

- Si presuppone che l'utente abbia familiarità con quanto descritto in [Introduzione alla gestione dei dispositivi in Azure Active Directory](device-management-introduction.md)

- Vengono fornite informazioni sulla gestione dei dispositivi tramite il portale di Azure


Per gestire i dispositivi nel portale di Azure, è necessario fare clic su **Dispositivi** nella sezione **Gestisci** del pannello **Azure Active Directory**.

![Gestire un dispositivo Intune](./media/device-management-azure-portal/11.png)




## <a name="configure-device-settings"></a>Configurare le impostazioni dei dispositivi

Per gestire i dispositivi tramite il portale di Azure, è necessario che i dispositivi siano registrati o aggiunti in Azure AD. Un amministratore può ottimizzare il processo di registrazione e aggiunta dei dispositivi configurando le relative impostazioni.

![Gestire un dispositivo Intune](./media/device-management-azure-portal/22.png)


Il pannello delle impostazioni dei dispositivi consente di configurare:

- **Gli utenti possono aggiungere dispositivi ad Azure AD**: questa impostazione consente di selezionare gli utenti che possono aggiungere dispositivi ad Azure AD. L'impostazione predefinita è **Tutti**.

- **Amministratori locali aggiuntivi su dispositivi aggiunti ad Azure AD**: è possibile selezionare gli utenti a cui vengono concessi i diritti di amministratore locale per un dispositivo. Gli utenti aggiunti in questa posizione vengono aggiunti al ruolo *Amministratori di dispositivi* in Azure AD. Agli amministratori globali di Azure AD e ai proprietari dei dispositivi vengono concessi i diritti di amministratore locale per impostazione predefinita. Questa opzione è una funzionalità dell'edizione Premium disponibile tramite prodotti come Azure AD Premium o Enterprise Mobility Suite (EMS). 

- **Gli utenti possono registrare i propri dispositivi in Azure AD**: è necessario configurare questa impostazione per consentire la registrazione dei dispositivi con Azure AD. Se si seleziona **Nessuno**, ai dispositivi non è consentito eseguire la registrazione se non sono aggiunti ad Azure AD o all'identità ibrida di AD Azure. L'iscrizione a Microsoft Intune o Gestione dispositivi mobili per Office 365 richiede la registrazione. Se è stato configurato uno di questi servizi, sarà selezionata l'opzione **TUTTI**, mentre l'opzione **NESSUNO** sarà disabilitata.

- **Richiedi Multi-factor Auth per aggiungere i dispositivi**: è possibile scegliere se richiedere agli utenti di fornire un secondo fattore di autenticazione per aggiungere il dispositivo ad Azure AD. Il valore predefinito è **No**. Si consiglia di richiedere l'autenticazione a più fattori quando si registra un dispositivo. Per abilitare l'autenticazione a più fattori per questo servizio, è necessario verificare che tale tipo di autenticazione sia configurato per gli utenti che registrano i dispositivi. Per altre informazioni sui diversi servizi di autenticazione a più fattori di Azure, vedere [Introduzione ad Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication-get-started.md). 

- **Numero massimo di dispositivi per utente**: questa impostazione consente di selezionare il numero massimo di dispositivi che un utente può avere in Azure AD. Se un utente raggiunge la quota specificata, non potrà aggiungere altri dispositivi fino a quando non vengono rimossi uno o più dispositivi esistenti. La quota dei dispositivi viene calcolata considerando tutti i dispositivi attualmente aggiunti ad Azure AD o registrati in Azure AD. Il valore predefinito è **20**.

- **Gli utenti possono sincronizzare le impostazioni e i dati delle app su tutti i dispositivi**: per impostazione predefinita, il valore di questa opzione è **Nessuno**. Se si selezionano utenti o gruppi specifici oppure l'opzione TUTTI, si consente la sincronizzazione delle impostazioni dell'utente e dei dati delle app tra i dispositivi Windows 10. Leggere altre informazioni sul funzionamento della sincronizzazione in Windows 10.
Questa opzione è una funzionalità Premium disponibile tramite prodotti come Azure AD Premium o Enterprise Mobility Suite (EMS).
 
    ![Gestire un dispositivo Intune](./media/device-management-azure-portal/21.png)




## <a name="locate-devices"></a>Individuare i dispositivi

Un amministratore nel portale di Azure dispone di due opzioni per individuare i dispositivi registrati e aggiunti:

- **Tutti i dispositivi** nella sezione **Gestisci** del pannello **Dispositivi**  

    ![Tutti i dispositivi](./media/device-management-azure-portal/41.png)


- **Dispositivi** nella sezione **Gestisci** di un pannello **Utente**
 
    ![Tutti i dispositivi](./media/device-management-azure-portal/43.png)



Con entrambe le opzioni, è possibile ottenere una vista che:


- Consente di cercare i dispositivi usando il nome visualizzato come filtro.

- Fornisce una panoramica dettagliata dei dispositivi registrati e aggiunti

- Consente di eseguire attività comuni di gestione dei dispositivi
   

![Tutti i dispositivi](./media/device-management-azure-portal/51.png)


## <a name="device-management-tasks"></a>Attività di gestione dei dispositivi

L'amministratore può gestire i dispositivi registrati o aggiunti. Questa sezione fornisce informazioni sulle attività comuni di gestione dei dispositivi.


**Gestire un dispositivo Intune**: un amministratore di Intune può gestire i dispositivi contrassegnati come **Microsoft Intune**. L'amministratore può visualizzare un dispositivo aggiuntivo 

![Gestire un dispositivo Intune](./media/device-management-azure-portal/31.png)


**Abilitare o disabilitare un dispositivo Azure AD**

Per abilitare o disabilitare un dispositivo, è necessario essere un amministratore globale in Azure AD. La disabilitazione di un dispositivo ne impedisce l'accesso alle risorse di Azure AD.  Per disabilitare il dispositivo, è possibile fare clic su *...* o fare clic sul dispositivo per altri dettagli.

 
![Gestire un dispositivo Intune](./media/device-management-azure-portal/33.png)

Se un dispositivo viene disabilitato, lo stato nella colonna **ABILITATO** viene impostato su **No**.

![Disabilitare un dispositivo](./media/device-management-azure-portal/32.png)


**Eliminare un dispositivo Azure AD**: per eliminare un dispositivo, è necessario essere un amministratore globale in Azure AD.  
L'eliminazione di un dispositivo comporta quanto segue:
 
- Impedisce l'accesso del dispositivo alle risorse di Azure AD 

- Rimuove tutti i dettagli collegati al dispositivo, ad esempio, le chiavi BitLocker per i dispositivi Windows  

- È un'attività non reversibile e non è consigliata, a meno che non sia necessaria

Se un dispositivo è gestito da un'altra autorità di gestione (ad esempio Microsoft Intune), assicurarsi che sia stato cancellato o disattivato prima di eliminarlo in Azure AD.

È possibile selezionare "…" per eliminare il dispositivo oppure fare clic sul dispositivo per altri dettagli
 
![Eliminare un dispositivo](./media/device-management-azure-portal/34.png)


**Visualizzare o copiare l'ID dispositivo**: è possibile usare l'ID di un dispositivo per verificare i relativi dettagli nel dispositivo o per l'uso in PowerShell in fase di risoluzione dei problemi. Per accedere all'opzione di copia, fare clic sul dispositivo.

![Visualizzare l'ID dispositivo](./media/device-management-azure-portal/35.png)
  

**Visualizzare o copiare le chiavi BitLocker**: un amministratore può visualizzare e copiare le chiavi BitLocker per aiutare gli utenti a recuperare l'unità crittografata. Queste chiavi sono disponibili solo per i dispositivi Windows crittografati e le cui chiavi sono archiviate in Azure AD. È possibile copiare queste chiavi quando si accede ai dettagli del dispositivo.
 
![Visualizzare le chiavi BitLocker](./media/device-management-azure-portal/36.png)



## <a name="audit-logs"></a>Log di controllo


Le attività del dispositivo sono disponibili tramite i log attività. I log includono le attività avviate dal servizio di registrazione dispositivo o dall'utente:

- Creazione di un dispositivo e aggiunta di proprietari/utenti nel dispositivo

- Modifiche alle impostazioni del dispositivo

- Funzionamento del dispositivo, ad esempio eliminazione o aggiornamento
 
Il punto di ingresso ai dati di controllo è **Log di controllo** nella sezione **Attività** del pannello **Dispositivi*.

![Log di controllo](./media/device-management-azure-portal/61.png)


Un log di controllo è una visualizzazione elenco predefinita che include:

- Data e ora dell'occorrenza.

- Destinazioni

- Iniziatore/attore di un'attività (chi)

- Attività (cosa)

![Log di controllo](./media/device-management-azure-portal/63.png)

Per personalizzare la visualizzazione elenco, fare clic su **Colonne** nella barra degli strumenti.
 
![Log di controllo](./media/device-management-azure-portal/64.png)


Per limitare i dati segnalati in base alle esigenze, è possibile filtrare i dati di controllo usando i campi seguenti:

- Categoria
- Activity resource type (Tipo di risorsa dell'attività)
- Attività
- Intervallo di date
- Destinazione
- Azione avviata da (attore)

Oltre a usare i filtri, è possibile cercare voci specifiche.

![Log di controllo](./media/device-management-azure-portal/65.png)

## <a name="next-steps"></a>Passaggi successivi

* [Introduction to device management in Azure Active Directory](device-management-introduction.md) (Introduzione alla gestione dei dispositivi in Azure Active Directory)




