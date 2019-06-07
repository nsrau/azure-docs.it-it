---
title: Convalida controllata di aggiunta ad Azure AD ibrido - Azure AD
description: Informazioni su come eseguire una convalida controllata di aggiunta ad Azure AD ibrido prima di abilitarlo in un'unica dell'intera organizzazione
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: article
ms.date: 05/30/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: cd5b388f92a875fb2635037a6eae3ff3b6a95793
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66513294"
---
# <a name="controlled-validation-of-hybrid-azure-ad-join"></a>Convalida controllata dell'aggiunta ad Azure AD ibrido

Quando tutti i prerequisiti sono presenti, i dispositivi di Windows verranno automaticamente registrati come dispositivi nel tenant di Azure AD. Lo stato di queste identità dei dispositivi in Azure AD è definito come l'aggiunta ad Azure AD ibrido. Altre informazioni sui concetti illustrati in questo articolo sono disponibili negli articoli [Introduzione alla gestione dei dispositivi in Azure Active Directory](overview.md) e [pianificare l'implementazione di join di Azure Active Directory ibrido ](hybrid-azuread-join-plan.md).

Le organizzazioni potrebbe essere necessario eseguire una convalida controllata di aggiunta ad Azure AD ibrido prima di abilitarlo per tutte contemporaneamente l'intera organizzazione. Questo articolo verrà illustrate le procedure eseguire una convalida controllata di aggiunta ad Azure AD ibrido.

## <a name="controlled-validation-of-hybrid-azure-ad-join-on-windows-current-devices"></a>Convalida controllata di aggiunta ad Azure AD ibrido nei dispositivi di Windows correnti

Per i dispositivi che eseguono il sistema operativo desktop Windows, è supportata la versione 1607 (Aggiornamento dell'anniversario di Windows 10) o successiva. Come procedura consigliata, eseguire l'aggiornamento alla versione più recente di Windows 10.

Per eseguire una convalida controllata di aggiunta ad Azure AD ibrido nei dispositivi di Windows correnti, è necessario:

1. Eliminare la voce Service Connection Point (SCP) da Active Directory (AD), se presente
1. Configurare l'impostazione del Registro di sistema lato client per SCP nei computer aggiunti al dominio usando un oggetto Criteri di gruppo (GPO)
1. Se si usa AD FS, è necessario configurare anche l'impostazione del Registro di sistema lato client per SCP nel server AD FS usando un oggetto Criteri di gruppo  



### <a name="clear-the-scp-from-ad"></a>Cancellare il SCP da Active Directory

Usare il Active di Directory Services Interfaces Editor (ADSI Edit) per modificare gli oggetti di SCP in Active Directory.

1. Avviare il **ADSI Edit** applicazione desktop da e workstation amministrativa o un controller di dominio come amministratore dell'organizzazione.
1. Connettere il **contesto nomi configurazione** del dominio.
1. Passare a **CN = Configuration, DC = contoso, DC = com** > **CN = Services** > **CN = Device Registration Configuration**
1. Fare clic con il pulsante destro sull'oggetto foglia sotto **CN = Device Registration Configuration** e selezionare **proprietà**
   1. Selezionare **parole chiave** dalle **Editor attributi** finestra e fare clic su **modifica**
   1. Selezionare i valori della **azureADId** e **azureADName** (una alla volta) e fare clic su **rimuovere**
1. Chiudi **Modifica ADSI**


### <a name="configure-client-side-registry-setting-for-scp"></a>Configurare l'impostazione del Registro di sistema lato client per SCP

Usare l'esempio seguente per creare un oggetto Criteri di gruppo (GPO) per distribuire un'impostazione del Registro di sistema la configurazione di una voce di SCP nel Registro di sistema dei dispositivi.

1. Aprire una console Gestione criteri di gruppo e creare un nuovo oggetto Criteri di gruppo nel dominio.
   1. Specificare l'oggetto Criteri di gruppo appena creato un nome (ad esempio, ClientSideSCP).
1. Modificare l'oggetto Criteri di gruppo e individuare il percorso seguente: **Configurazione computer** > **preferenze** > **le impostazioni di Windows** > **Registro di sistema**
1. Fare doppio clic sul Registro di sistema e scegliere **New** > **elemento Registro di sistema**
   1. Nel **generale** scheda, configurare quanto segue
      1. Azione: **Aggiornamento**
      1. Hive: **HKEY_LOCAL_MACHINE**
      1. Percorso della chiave: **SOFTWARE\Microsoft\Windows\CurrentVersion\CDJ\AAD**
      1. Nome valore: **TenantId**
      1. Tipo di valore: **REG_SZ**
      1. Dati valore: Il GUID o **ID Directory** dell'istanza di Azure AD (questo valore è reperibile nella **portale di Azure** > **Azure Active Directory**  >   **Le proprietà** > **ID Directory**)
   1. Fare clic su **OK**.
1. Fare doppio clic sul Registro di sistema e scegliere **New** > **elemento Registro di sistema**
   1. Nel **generale** scheda, configurare quanto segue
      1. Azione: **Aggiornamento**
      1. Hive: **HKEY_LOCAL_MACHINE**
      1. Percorso della chiave: **SOFTWARE\Microsoft\Windows\CurrentVersion\CDJ\AAD**
      1. Nome valore: **TenantName**
      1. Tipo di valore: **REG_SZ**
      1. Dati valore: La verifica **nome di dominio** in Azure AD (ad esempio, `contoso.onmicrosoft.com` o qualsiasi altro nome di dominio verificati nella directory)
   1. Fare clic su **OK**.
1. Chiudere l'editor per l'oggetto Criteri di gruppo appena creato
1. Collegamento oggetto Criteri di gruppo appena creato nell'unità organizzativa desiderata contenente i computer aggiunti a un dominio che appartengono al popolamento di implementazione controllata

### <a name="configure-ad-fs-settings"></a>Configurare le impostazioni di ADFS

Se si usa AD FS, è necessario innanzitutto configurare lato client SCP procedendo come indicato in precedenza, ma il collegamento oggetto Criteri di gruppo ai server AD FS. Questa configurazione è necessaria per AD FS stabilire l'origine per le identità dei dispositivi come Azure AD.

## <a name="controlled-validation-of-hybrid-azure-ad-join-on-windows-down-level-devices"></a>Convalida controllata di aggiunta ad Azure AD ibrido nei dispositivi di livello inferiore di Windows

Per registrare i dispositivi legacy di Windows, è necessario installare le organizzazioni [Microsoft Workplace Join per i computer non Windows 10](https://www.microsoft.com/download/details.aspx?id=53554) disponibili nel Microsoft Download Center.

È possibile distribuire il pacchetto usando un sistema di distribuzione software, ad esempio [System Center Configuration Manager](https://www.microsoft.com/cloud-platform/system-center-configuration-manager). Il pacchetto supporta le opzioni standard di installazione invisibile all'utente con il parametro quiet. Configuration Manager Current Branch offre vantaggi aggiuntivi rispetto alle versioni precedenti, come la possibilità di tenere traccia delle registrazioni completate.

Il programma di installazione crea un'attività pianificata nel sistema che viene eseguito nel contesto dell'utente. e attivata nel momento in cui l'utente accede a Windows. L'attività aggiunge automaticamente il dispositivo con Azure AD con le credenziali dell'utente dopo l'autenticazione con Azure AD.

Per controllare la registrazione del dispositivo, è consigliabile distribuire il pacchetto Windows Installer per il gruppo selezionato di dispositivi di livello inferiore di Windows.

> [!NOTE]
> Se un SCP non è configurata in Active Directory, quindi è necessario seguire lo stesso approccio descritto da [configurare le impostazioni del Registro di sistema lato client delle SCP](#configure-client-side-registry-setting-for-scp)) nei computer aggiunti al dominio usando un oggetto Criteri di gruppo (GPO).


Dopo aver verificato che tutto funzioni come previsto, è possibile registrare automaticamente il resto dei dispositivi Windows correnti e legacy con Azure AD dal [configurazione SCP con Azure AD Connect](hybrid-azuread-join-managed-domains.md#configure-hybrid-azure-ad-join).

## <a name="next-steps"></a>Passaggi successivi

[Pianificare l'implementazione dell'aggiunta ad Azure Active Directory ibrido](hybrid-azuread-join-plan.md)
