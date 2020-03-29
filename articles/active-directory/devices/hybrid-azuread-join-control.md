---
title: Convalida controllata del join ibrido di Azure AD - Azure ADControlled validation of hybrid Azure AD join - Azure AD
description: Informazioni su come eseguire una convalida controllata dell'aggiunta ibrida ad Azure AD prima di abilitarla nell'intera organizzazione contemporaneamente
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: f43db805ccbb7d4e546c51bbe39350f4bbba2efb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80049987"
---
# <a name="controlled-validation-of-hybrid-azure-ad-join"></a>Convalida controllata dell'aggiunta ad Azure AD ibrido

Quando tutti i prerequisiti sono presenti, i dispositivi Windows verranno registrati automaticamente come dispositivi nel tenant di Azure AD. Lo stato di queste identità del dispositivo in Azure AD viene definito aggiunta ibrida ad Azure AD. Ulteriori informazioni sui concetti illustrati in questo articolo sono disponibili negli articoli Introduzione alla gestione dei [dispositivi in Azure Active Directory](overview.md) e Pianificare l'implementazione di aggiunta ad Azure Active Directory [ibrido.](hybrid-azuread-join-plan.md)

Le organizzazioni potrebbero voler eseguire una convalida controllata della partecipazione ibrida ad Azure AD prima di abilitarla nell'intera organizzazione contemporaneamente. Questo articolo illustra come eseguire una convalida controllata del join ibrido di Azure AD.

## <a name="controlled-validation-of-hybrid-azure-ad-join-on-windows-current-devices"></a>Convalida controllata dell'aggiunta ibrida ad Azure AD nei dispositivi Windows correnti

Per i dispositivi che eseguono il sistema operativo desktop Windows, è supportata la versione 1607 (Aggiornamento dell'anniversario di Windows 10) o successiva. Come procedura consigliata, eseguire l'aggiornamento alla versione più recente di Windows 10.

Per eseguire una convalida controllata dell'aggiunta ibrida ad Azure AD nei dispositivi Windows correnti, è necessario:To do a controlled validation of hybrid Azure AD join on Windows current devices, you need to:

1. Cancellare la voce del punto di connessione del servizio (SCP) da Active Directory (AD) se esistente
1. Configurare l'impostazione del Registro di sistema sul lato client per SCP nei computer appartenenti a un dominio utilizzando un oggetto Criteri di gruppo (GPO)
1. Se si utilizza ADFS, è inoltre necessario configurare l'impostazione del Registro di sistema lato client per SCP sul server ADFS utilizzando un oggetto Criteri di gruppo  
1. Potrebbe anche essere necessario personalizzare le opzioni di [sincronizzazione](../hybrid/how-to-connect-post-installation.md#additional-tasks-available-in-azure-ad-connect) in Azure AD Connect per abilitare la sincronizzazione dei dispositivi. 


### <a name="clear-the-scp-from-ad"></a>Cancellare l'SCP da ACTIVE Directory

Utilizzare Active Directory Services Interfaces Editor (ADSI Edit) per modificare gli oggetti SCP in Active Directory.

1. Avviare l'applicazione desktop **ADSI Edit** da e workstation amministrativa o un controller di dominio come amministratore dell'organizzazione.
1. Connettersi al **contesto** dei nomi di configurazione del dominio.
1. Passare a **CN , Configuration, DC , contoso, DC , com** > **CN , Servizi** > **CN , Configurazione registrazione dispositivi**
1. Fare clic con il pulsante destro del mouse sull'oggetto foglia **CN 62a0ff2e-97b9-4513-943f-0d221bd30080** e selezionare **Proprietà**
   1. Selezionare **le parole chiave** dalla finestra Editor **attributi** e fare clic su **Modifica**
   1. Selezionare i valori di **azureADId** e **azureADName** (uno alla volta) e fare clic su **Rimuovi**
1. Chiudi **modifica ADSI**


### <a name="configure-client-side-registry-setting-for-scp"></a>Configurare l'impostazione del Registro di sistema sul lato client per SCP

Utilizzare l'esempio seguente per creare un oggetto Criteri di gruppo (GPO) per distribuire un'impostazione del Registro di sistema che configura una voce SCP nel Registro di sistema dei dispositivi.

1. Aprire una console Gestione Criteri di gruppo e creare un nuovo oggetto Criteri di gruppo nel dominio.
   1. Specificare un nome all'oggetto Criteri di gruppo appena creato, ad esempio ClientSideSCP.
1. Modificare l'oggetto Criteri di gruppo e individuare il seguente percorso: **Preferenze di configurazione** > **del** > Computer**Impostazioni** > **di** Windows
1. Fare clic con il pulsante destro del mouse sul Registro di sistema e selezionare **Nuovo** > **elemento del Registro di sistema**
   1. Nella scheda **Generale,** configurare i seguenti
      1. Azione: **Aggiornamento**
      1. Hive: **HKEY_LOCAL_MACHINE**
      1. Percorso della chiave: **SOFTWARE**
      1. Nome valore: **TenantId**
      1. Tipo di valore: **REG_SZ**
      1. Dati valore: GUID o **ID directory** dell'istanza di Azure AD (questo valore è disponibile nel **portale** > di Azure**Active Directory** > **Properties** > **Directory ID**)
   1. Fare clic su **OK**.
1. Fare clic con il pulsante destro del mouse sul Registro di sistema e selezionare **Nuovo** > **elemento del Registro di sistema**
   1. Nella scheda **Generale,** configurare i seguenti
      1. Azione: **Aggiornamento**
      1. Hive: **HKEY_LOCAL_MACHINE**
      1. Percorso della chiave: **SOFTWARE**
      1. Nome valore: **TenantName**
      1. Tipo di valore: **REG_SZ**
      1. Dati valore: **il nome** di dominio verificato se si utilizza l'ambiente federato, ad esempio ADFS. Il **nome di dominio** verificato o `contoso.onmicrosoft.com` il nome di dominio onmicrosoft.com, ad esempio, se si utilizza un ambiente gestito
   1. Fare clic su **OK**.
1. Chiudere l'editor per l'oggetto Criteri di gruppo appena creato
1. Collegare l'oggetto Criteri di gruppo appena creato all'unità organizzativa desiderata contenente i computer aggiunti al dominio che appartengono al popolamento controllato dell'implementazione

### <a name="configure-ad-fs-settings"></a>Configurare le impostazioni di ADFSConfigure AD FS settings

Se si utilizza ADFS, è innanzitutto necessario configurare SCP lato client utilizzando le istruzioni menzionate in precedenza collegando l'oggetto Criteri di gruppo ai server ADFS. L'oggetto SCP definisce l'origine dell'autorità per gli oggetti dispositivo. Può essere locale o Azure AD. Quando SCP sul lato client è configurato per ADFS, l'origine per gli oggetti dispositivo viene stabilita come Azure AD.

> [!NOTE]
> Se non è stato possibile configurare SCP sul lato client nei server ADFS, l'origine per le identità dei dispositivi verrà considerata come locale. ADFS inizierà quindi a eliminare gli oggetti dispositivo dalla directory locale dopo il periodo stabilito nell'attributo di ADFS Device Registration "MaximumInactiveDays". Gli oggetti Di registrazione dispositivi ADFS sono disponibili utilizzando il [cmdlet Get-AdfsDeviceRegistration](/powershell/module/adfs/get-adfsdeviceregistration?view=win10-ps).

## <a name="controlled-validation-of-hybrid-azure-ad-join-on-windows-down-level-devices"></a>Convalida controllata dell'aggiunta ibrida ad Azure AD nei dispositivi di livello inferiore di Windows

Per registrare i dispositivi Windows di livello inferiore, le organizzazioni devono installare [Microsoft Workplace Join per computer non Windows 10](https://www.microsoft.com/download/details.aspx?id=53554), disponibile nell'Area download Microsoft.

È possibile distribuire il pacchetto usando un sistema di distribuzione software come  [Microsoft Endpoint Configuration Manager](/configmgr/). Il pacchetto supporta le opzioni standard di installazione invisibile all'utente con il parametro quiet. Configuration Manager Current Branch offre vantaggi aggiuntivi rispetto alle versioni precedenti, come la possibilità di tenere traccia delle registrazioni completate.

Il programma di installazione crea nel sistema un'attività pianificata che viene eseguita nel contesto utente. e attivata nel momento in cui l'utente accede a Windows. L'attività aggiunge automaticamente il dispositivo con Azure AD con le credenziali dell'utente dopo l'autenticazione con Azure AD.

Per controllare la registrazione del dispositivo, è necessario distribuire il pacchetto di Windows Installer al gruppo selezionato di dispositivi di livello inferiore di Windows.To control the device registration, you should deploy the Windows Installer package to your selected group of Windows down-level devices.

> [!NOTE]
> Se un SCP non è configurato in Active Directory, è necessario seguire lo stesso approccio descritto in [Configure client-side registry setting for SCP](#configure-client-side-registry-setting-for-scp)) nei computer appartenenti al dominio che utilizzano un oggetto Criteri di gruppo (GPO).


Dopo aver verificato che tutto funzioni come previsto, è possibile registrare automaticamente il resto dei dispositivi Windows correnti e di livello inferiore con Azure AD [configurando SCP tramite Azure AD Connect](hybrid-azuread-join-managed-domains.md#configure-hybrid-azure-ad-join).

## <a name="next-steps"></a>Passaggi successivi

[Pianificare l'implementazione dell'aggiunta ad Azure Active Directory ibrido](hybrid-azuread-join-plan.md)
