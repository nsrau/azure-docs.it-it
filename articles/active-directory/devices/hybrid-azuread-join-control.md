---
title: Convalida controllata del join di Azure AD ibrido-Azure AD
description: Informazioni su come eseguire una convalida controllata del join ibrido di Azure AD prima di abilitarlo in tutta l'organizzazione contemporaneamente
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
ms.openlocfilehash: dc5c85aaa3c2128b10ba2e6f9c45a66b44593202
ms.sourcegitcommit: ec2b75b1fc667c4e893686dbd8e119e7c757333a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72809218"
---
# <a name="controlled-validation-of-hybrid-azure-ad-join"></a>Convalida controllata dell'aggiunta ad Azure AD ibrido

Quando tutti i prerequisiti sono soddisfatti, i dispositivi Windows verranno registrati automaticamente come dispositivi nel tenant del Azure AD. Lo stato di queste identità del dispositivo in Azure AD viene definito ibrido Azure AD join. Altre informazioni sui concetti trattati in questo articolo sono disponibili negli articoli [Introduzione alla gestione dei dispositivi in Azure Active Directory](overview.md) e [pianificare l'implementazione di Azure Active Directory join ibrida](hybrid-azuread-join-plan.md).

È possibile che le organizzazioni desiderino eseguire una convalida controllata del join ibrido di Azure AD prima di abilitarlo nell'intera organizzazione. In questo articolo viene illustrato come eseguire una convalida controllata del join di Azure AD ibrido.

## <a name="controlled-validation-of-hybrid-azure-ad-join-on-windows-current-devices"></a>Convalida controllata del join Azure AD ibrido nei dispositivi Windows correnti

Per i dispositivi che eseguono il sistema operativo desktop Windows, è supportata la versione 1607 (Aggiornamento dell'anniversario di Windows 10) o successiva. Come procedura consigliata, eseguire l'aggiornamento alla versione più recente di Windows 10.

Per eseguire una convalida controllata del join Azure AD ibrido nei dispositivi Windows correnti, è necessario:

1. Cancellare la voce del punto di connessione del servizio (SCP) da Active Directory (AD) se esistente
1. Configurare l'impostazione del registro di sistema sul lato client per SCP nei computer aggiunti a un dominio usando un oggetto Criteri di gruppo (GPO)
1. Se si utilizza AD FS, è inoltre necessario configurare l'impostazione del registro di sistema sul lato client per SCP nel server AD FS utilizzando un oggetto Criteri di gruppo  



### <a name="clear-the-scp-from-ad"></a>Cancellare SCP da AD

Utilizzare l'editor interfacce Active Directory Services (ADSI Edit) per modificare gli oggetti SCP in Active Directory.

1. Avviare l'applicazione desktop **ADSI Edit** da e dalla workstation amministrativa o da un controller di dominio come amministratore dell'organizzazione.
1. Connettersi al **contesto dei nomi di configurazione** del dominio.
1. Passare a **CN = Configuration, DC = contoso, DC = com** > **cn = Services** > **CN = Device Registration Configuration**
1. Fare clic con il pulsante destro del mouse sull'oggetto foglia in **CN = Device Registration Configuration** e selezionare **Properties**
   1. Selezionare **parole chiave** dalla finestra **Editor attributi** e fare clic su **modifica** .
   1. Selezionare i valori di **azureADId** e **azureADName** (uno alla volta) e fare clic su **Rimuovi** .
1. Chiudi **ADSI Edit**


### <a name="configure-client-side-registry-setting-for-scp"></a>Configurare l'impostazione del registro di sistema sul lato client per SCP

Usare l'esempio seguente per creare un oggetto Criteri di gruppo (GPO) per distribuire un'impostazione del registro di sistema configurando una voce SCP nel registro dei dispositivi.

1. Aprire una console di gestione di Criteri di gruppo e creare un nuovo oggetto Criteri di gruppo nel dominio.
   1. Specificare un nome per l'oggetto Criteri di gruppo appena creato, ad esempio ClientSideSCP.
1. Modificare l'oggetto Criteri di gruppo e individuare il percorso seguente: **Configurazione Computer** > **Preferenze** > **impostazioni di Windows** > **Registro di sistema**
1. Fare clic con il pulsante destro del mouse sul registro e scegliere **nuovo** > **elemento del registro di sistema**
   1. Nella scheda **generale** configurare quanto segue:
      1. Azione: **aggiornamento**
      1. Hive: **HKEY_LOCAL_MACHINE**
      1. Percorso della chiave: **SOFTWARE\Microsoft\Windows\CurrentVersion\CDJ\AAD**
      1. Nome valore: **TenantId**
      1. Tipo di valore: **REG_SZ**
      1. Dati valore: il GUID o **l'ID di directory** dell'istanza di Azure ad (questo valore è disponibile nella **portale di Azure** > **Azure Active Directory** **Proprietà** >  > **ID directory**)
   1. Fare clic su **OK**.
1. Fare clic con il pulsante destro del mouse sul registro e scegliere **nuovo** > **elemento del registro di sistema**
   1. Nella scheda **generale** configurare quanto segue:
      1. Azione: **aggiornamento**
      1. Hive: **HKEY_LOCAL_MACHINE**
      1. Percorso della chiave: **SOFTWARE\Microsoft\Windows\CurrentVersion\CDJ\AAD**
      1. Nome valore: **TenantName**
      1. Tipo di valore: **REG_SZ**
      1. Dati valore: il **nome di dominio** verificato se si usa un ambiente federato, ad esempio ad FS. Il **nome di dominio** verificato o il nome di dominio onmicrosoft.com, ad esempio `contoso.onmicrosoft.com` se si usa l'ambiente gestito
   1. Fare clic su **OK**.
1. Chiudere l'editor per l'oggetto Criteri di gruppo appena creato
1. Collegare l'oggetto Criteri di gruppo appena creato all'unità organizzativa desiderata contenente i computer aggiunti a un dominio che appartengono al popolamento di implementazione controllato

### <a name="configure-ad-fs-settings"></a>Configurare le impostazioni di AD FS

Se si utilizza AD FS, è necessario innanzitutto configurare SCP sul lato client utilizzando le istruzioni indicate sopra, ma collegando l'oggetto Criteri di gruppo ai server di AD FS. L'oggetto SCP definisce l'origine dell'autorità per gli oggetti dispositivo. Può essere locale o Azure AD. Quando viene configurato per AD FS, l'origine per gli oggetti dispositivo viene stabilita come Azure AD.

> [!NOTE]
> Se non è stato possibile configurare SCP sul lato client nei server AD FS, l'origine per le identità del dispositivo verrebbe considerata locale e, in caso di writeback dei dispositivi, AD FS inizierà a eliminare gli oggetti dispositivo dal contenitore di dispositivi registrati in locale dopo un periodo stabilito.

## <a name="controlled-validation-of-hybrid-azure-ad-join-on-windows-down-level-devices"></a>Convalida controllata del join di Azure AD ibrido nei dispositivi Windows di livello inferiore

Per registrare i dispositivi Windows di livello inferiore, le organizzazioni devono installare [Microsoft Workplace Join per computer non Windows 10](https://www.microsoft.com/download/details.aspx?id=53554), disponibile nell'Area download Microsoft.

È possibile distribuire il pacchetto usando un sistema di distribuzione software come  [System Center Configuration Manager](https://www.microsoft.com/cloud-platform/system-center-configuration-manager). Il pacchetto supporta le opzioni standard di installazione invisibile all'utente con il parametro quiet. Configuration Manager Current Branch offre vantaggi aggiuntivi rispetto alle versioni precedenti, come la possibilità di tenere traccia delle registrazioni completate.

Il programma di installazione crea nel sistema un'attività pianificata che viene eseguita nel contesto utente. e attivata nel momento in cui l'utente accede a Windows. L'attività aggiunge automaticamente il dispositivo con Azure AD con le credenziali dell'utente dopo l'autenticazione con Azure AD.

Per controllare la registrazione del dispositivo, è necessario distribuire il pacchetto di Windows Installer al gruppo selezionato di dispositivi Windows di livello inferiore.

> [!NOTE]
> Se una SCP non è configurata in Active Directory, è necessario seguire lo stesso approccio descritto in [configurare l'impostazione del registro di sistema sul lato client per SCP](#configure-client-side-registry-setting-for-scp)sui computer aggiunti a un dominio usando un oggetto Criteri di gruppo (GPO).


Dopo aver verificato che tutto funzioni come previsto, è possibile registrare automaticamente il resto dei dispositivi Windows correnti e di livello inferiore con Azure AD [configurando SCP usando Azure ad Connect](hybrid-azuread-join-managed-domains.md#configure-hybrid-azure-ad-join).

## <a name="next-steps"></a>Passaggi successivi

[Pianificare l'implementazione dell'aggiunta ad Azure Active Directory ibrido](hybrid-azuread-join-plan.md)
