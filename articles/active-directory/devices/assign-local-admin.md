---
title: Come gestire gli amministratori locali nei dispositivi Azure AD aggiunti
description: Informazioni su come assegnare i ruoli di Azure al gruppo di amministratori locale di un dispositivo Windows.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: how-to
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: ravenn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0903828b04922104a9dd93ac79459bf73644f35c
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92365834"
---
# <a name="how-to-manage-the-local-administrators-group-on-azure-ad-joined-devices"></a>Come gestire il gruppo di amministratori locale nei dispositivi aggiunti ad Azure AD

Per gestire un dispositivo Windows, è necessario essere un membro del gruppo di amministratori locale. Durante il processo di aggiunta ad Azure Active Directory (Azure AD), Azure AD aggiorna l'appartenenza di questo gruppo in un dispositivo. È possibile personalizzare l'aggiornamento dell'appartenenza per soddisfare i requisiti aziendali. L'aggiornamento di un'appartenenza è, ad esempio, utile se si vuole consentire al personale del supporto tecnico di eseguire le attività che richiedono diritti di amministratore in un dispositivo.

Questo articolo illustra il funzionamento dell'aggiornamento dell'appartenenza degli amministratori locali e il modo in cui è possibile personalizzarlo durante un Azure AD join. Il contenuto di questo articolo non si applica a dispositivi **ibridi Azure ad aggiunti** .

## <a name="how-it-works"></a>Funzionamento

Quando si connette un dispositivo Windows con Azure AD usando un join Azure AD, Azure AD aggiunge le entità di sicurezza seguenti al gruppo Administrators locale nel dispositivo:

- Ruolo Amministratore globale di Azure AD
- Ruolo Amministratore dispositivo di Azure AD 
- Utente che esegue l'aggiunta ad Azure AD   

Aggiungendo i ruoli di Azure AD al gruppo di amministratori locale, è possibile aggiornare gli utenti che possono gestire un dispositivo in qualsiasi momento in Azure AD senza modificare nulla sul dispositivo. Non è attualmente possibile assegnare gruppi a un ruolo di amministratore.
Azure AD aggiunge anche il ruolo Amministratore dispositivo di Azure AD al gruppo di amministratori locale per supportare il principio dei privilegi minimi necessari. Oltre che agli amministratori globali, è possibile consentire di gestire un dispositivo anche agli utenti a cui è stato assegnato *solo* il ruolo Amministratore dispositivo. 

## <a name="manage-the-global-administrators-role"></a>Gestire il ruolo degli amministratori globali

Per visualizzare e aggiornare l'appartenenza al ruolo Amministratore globale, vedere:

- [Visualizzare tutti i membri di un ruolo di amministratore in Azure Active Directory](../roles/manage-roles-portal.md)
- [Assegnare un utente ai ruoli di amministratore in Azure Active Directory](../fundamentals/active-directory-users-assign-role-azure-portal.md)


## <a name="manage-the-device-administrator-role"></a>Gestire il ruolo Amministratore dispositivo 

Nel portale di Azure è possibile gestire il ruolo Amministratore dispositivo nella pagina **Dispositivi**. Per aprire la pagina **Dispositivi**:

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore globale.
1. Cercare e selezionare *Azure Active Directory*.
1. Nella sezione **Gestisci** fare clic su **Dispositivi**.
1. Nella pagina **Dispositivi** fare clic su **Impostazioni del dispositivo**.

Per modificare il ruolo Amministratore dispositivo, configurare **Amministratori locali aggiuntivi su dispositivi aggiunti ad Azure AD**.  

![Amministratori locali aggiuntivi](./media/assign-local-admin/10.png)

>[!NOTE]
> Questa opzione richiede un tenant Azure AD Premium. 

Gli amministratori dispositivo vengono assegnati a tutti i dispositivi aggiunti ad Azure AD. Non è possibile includere gli amministratori dispositivo nell'ambito di un set specifico di dispositivi. L'aggiornamento del ruolo Amministratore dispositivo non ha necessariamente un impatto immediato sugli utenti interessati. Nei dispositivi in cui un utente è già connesso, l'elevazione dei privilegi viene eseguita quando si verificano *entrambe* le azioni seguenti:

- Sono trascorse fino a 4 ore per Azure AD per emettere un nuovo token di aggiornamento primario con i privilegi appropriati. 
- L'utente si disconnette e torna indietro, non blocca/sblocca, per aggiornare il proprio profilo.

>[!NOTE]
> Le azioni precedenti non sono applicabili agli utenti che non hanno eseguito l'accesso al dispositivo pertinente in precedenza. In questo caso, i privilegi di amministratore vengono applicati subito dopo il primo accesso al dispositivo. 

## <a name="manage-administrator-privileges-using-azure-ad-groups-preview"></a>Gestire i privilegi di amministratore usando gruppi di Azure AD (anteprima)

>[!NOTE]
> Questa funzionalità è attualmente in anteprima.

A partire dall'aggiornamento di Windows 10 2004, è possibile usare i gruppi di Azure AD per gestire i privilegi di amministratore nei dispositivi Azure AD aggiunti con i criteri MDM dei [gruppi limitati](/windows/client-management/mdm/policy-csp-restrictedgroups) . Questo criterio consente di assegnare singoli utenti o gruppi di Azure AD al gruppo Administrators locale in un dispositivo Azure AD aggiunto, offrendo la granularità per configurare amministratori distinti per gruppi diversi di dispositivi. 

Attualmente non è disponibile alcuna interfaccia utente in Intune per gestire questi criteri e deve essere configurata usando [impostazioni URI OMA personalizzate](/mem/intune/configuration/custom-settings-windows-10). Alcune considerazioni relative a questo criterio: 

- L'aggiunta di Azure AD gruppi tramite il criterio richiede il SID del gruppo che può essere ottenuto eseguendo l'API dei gruppi. Il SID è definito dalla proprietà `securityIdentifier` nell'API dei gruppi.
- Quando viene applicato un criterio di gruppi limitati, viene rimosso qualsiasi membro corrente del gruppo che non si trova nell'elenco dei membri. L'applicazione di questi criteri con nuovi membri o gruppi eliminerà quindi gli amministratori esistenti, ovvero l'utente che ha aggiunto il dispositivo, il ruolo di amministratore del dispositivo e il ruolo di amministratore globale dal dispositivo. Per evitare di rimuovere membri esistenti, è necessario configurarli come parte dell'elenco dei membri nel criterio gruppi limitati. 
- Questo criterio è applicabile solo ai gruppi noti seguenti in un dispositivo Windows 10: amministratori, utenti, Guest, Power Users, Desktop remoto utenti e utenti di gestione remota. 
- La gestione degli amministratori locali con i criteri di gruppi limitati non è applicabile ai dispositivi Azure AD ibrido aggiunti o Azure AD registrati.
- Mentre i criteri dei gruppi limitati erano precedenti a Windows 10 2004 Update, non supportava i gruppi di Azure AD come membri del gruppo Administrators locale del dispositivo. 

## <a name="manage-regular-users"></a>Gestire utenti normali

Per impostazione predefinita, Azure AD aggiunge l'utente che esegue l'aggiunta ad Azure AD al gruppo Amministratori sul dispositivo. Per impedire agli utenti normali di diventare amministratori locali, sono disponibili le opzioni seguenti:

- [Windows Autopilot](/windows/deployment/windows-autopilot/windows-10-autopilot): Windows Autopilot offre un'opzione per impedire all'utente primario che esegue l'aggiunta di diventare un amministratore locale. A questo scopo, è possibile [creare un profilo di Autopilot](/intune/enrollment-autopilot#create-an-autopilot-deployment-profile).
- [Registrazione in blocco](/intune/windows-bulk-enroll): un'aggiunta ad Azure AD eseguita nell'ambito di una registrazione in blocco avviene nel contesto di un utente creato automaticamente. Gli utenti che eseguono l'accesso dopo che un dispositivo è stato aggiunto non vengono aggiunti al gruppo Amministratori.   

## <a name="manually-elevate-a-user-on-a-device"></a>Elevare manualmente un utente su un dispositivo 

Oltre a usare il processo di aggiunta AD Azure, è anche possibile elevare manualmente un utente normale in modo che diventi un amministratore locale su un dispositivo specifico. Per eseguire questo passaggio, è necessario già essere un membro del gruppo Amministratori locale. 

A partire dalla versione di **Windows 10 1709** , è possibile eseguire questa attività da **Settings-> accounts-> altri utenti**. Selezionare **Aggiungere un utente aziendale o dell'istituto di istruzione**, immettere l'UPN dell'utente in **Account utente** e selezionare *Amministratore* in **Tipo di account**  
 
È anche possibile aggiungere gli utenti usando il prompt dei comandi:

- Se gli utenti del tenant vengono sincronizzati da Active Directory locale, usare `net localgroup administrators /add "Contoso\username"`.
- Se gli utenti del tenant vengono creati in Azure AD, usare `net localgroup administrators /add "AzureAD\UserUpn"`

## <a name="considerations"></a>Considerazioni 

Non è possibile assegnare gruppi al ruolo Amministratore dispositivo. Sono consentiti solo singoli utenti.

Gli amministratori dispositivo vengono assegnati a tutti i dispositivi aggiunti ad Azure AD. Non possono essere inclusi nell'ambito di un set specifico di dispositivi.

Quando si rimuovono gli utenti dal ruolo Amministratore dispositivo, questi hanno ancora il privilegio di amministratore locale su un dispositivo, purché abbiano effettuato l'accesso. Il privilegio viene revocato durante il successivo accesso quando viene emesso un nuovo token di aggiornamento primario. Questa revoca, simile all'elevazione dei privilegi, potrebbe richiedere fino a 4 ore.

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica sulla gestione del dispositivo nel portale di Azure AD, vedere [Gestione dei dispositivi tramite il portale di Azure](device-management-azure-portal.md)
- Per altre informazioni sull'accesso condizionale basato su dispositivo, vedere [configurare Azure Active Directory Criteri di accesso condizionale basato su dispositivo](../conditional-access/require-managed-devices.md).
