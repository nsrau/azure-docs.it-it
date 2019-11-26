---
title: Come gestire i dispositivi non aggiornati in Azure AD | Microsoft Docs
description: Informazioni su come rimuovere i dispositivi non aggiornati dal database dei dispositivi registrati in Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1829c56f9804c5aa808461db98a5048d63f55446
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74207288"
---
# <a name="how-to-manage-stale-devices-in-azure-ad"></a>Procedura: gestire dispositivi non aggiornati in Azure AD

In teoria, per completare il ciclo di vita, è necessario annullare la registrazione dei dispositivi registrati quando non sono più necessari. Tuttavia, in seguito ad esempio allo smarrimento o al furto di dispositivi, alla presenza di dispositivi guasti o alle reinstallazioni del sistema operativo, nell'ambiente in uso potrebbero essere presenti dispositivi non aggiornati. Gli amministratori IT hanno bisogno di un metodo per rimuovere i dispositivi non aggiornati, in modo da concentrare le risorse sulla gestione di quelli che necessitano effettivamente di gestione.

In questo articolo si imparerà a gestire in modo efficiente i dispositivi non aggiornati nell'ambiente in uso.
  

## <a name="what-is-a-stale-device"></a>Che cos'è un dispositivo non aggiornato?

Un dispositivo non aggiornato è un dispositivo che è stato registrato con Azure AD, ma che non è stato usato per accedere ad app cloud per un intervallo di tempo specifico. La presenza di dispositivi non aggiornati si ripercuote sulla possibilità di gestire e supportare i dispositivi e gli utenti nel tenant perché: 

- I dispositivi duplicati possono rendere difficile l'identificazione del dispositivo attualmente attivo da parte del personale del supporto tecnico.
- Un numero maggiore di dispositivi crea writeback dei dispositivi non necessari che aumentano il tempo per Azure AD la sincronizzazione della connessione.
- Per ragioni di ordine e conformità, è preferibile avere uno stato pulito dei dispositivi. 

I dispositivi non aggiornati in Azure AD possono interferire con i criteri generali per il ciclo di vita adottati dall'organizzazione.

## <a name="detect-stale-devices"></a>Rilevare i dispositivi non aggiornati

Dal momento che un dispositivo non aggiornato è definito come dispositivo registrato che non è stato usato per accedere ad app cloud per uno specifico intervallo di tempo, il rilevamento dei dispositivi non aggiornati richiede una proprietà correlata al timestamp. In Azure AD questa proprietà è chiamata **ApproximateLastLogonTimestamp** o **timestamp dell'attività**. Se il delta tra il momento attuale e il valore del **timestamp dell'attività** supera l'intervallo di tempo definito per i dispositivi attivi, un dispositivo viene considerato non aggiornato. Il **timestamp dell'attività** è ora disponibile in anteprima pubblica.

## <a name="how-is-the-value-of-the-activity-timestamp-managed"></a>Come viene gestito il valore del timestamp dell'attività?  

La valutazione del timestamp dell'attività viene attivata da un tentativo di autenticazione di un dispositivo. Azure AD valuta il timestamp dell'attività quando:

- È stato attivato un criterio di accesso condizionale che richiede [dispositivi gestiti](../conditional-access/require-managed-devices.md) o [app client approvate](../conditional-access/app-based-conditional-access.md) .
- I dispositivi Windows 10 che sono aggiunti ad Azure AD o aggiunti ad Azure AD ibrido sono attivi nella rete. 
- I dispositivi gestiti da Intune sono stati archiviati nel servizio.

Se il delta tra il valore esistente del timestamp dell'attività e il valore corrente è maggiore di 14 giorni (varianza +/-5 giorni), il valore esistente viene sostituito con il nuovo valore.

## <a name="how-do-i-get-the-activity-timestamp"></a>Come si ottiene il timestamp dell'attività?

Sono disponibili due opzioni per recuperare il valore del timestamp dell'attività:

- La colonna **Attività** nella [pagina Dispositivi](https://portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/Devices) nel portale di Azure

    ![Timestamp dell'attività](./media/manage-stale-devices/01.png)

- Il cmdlet [Get-MsolDevice](https://docs.microsoft.com/powershell/module/msonline/get-msoldevice?view=azureadps-1.0)

    ![Timestamp dell'attività](./media/manage-stale-devices/02.png)

## <a name="plan-the-cleanup-of-your-stale-devices"></a>Pianificare la pulizia dei dispositivi non aggiornati

Per eliminare in modo efficiente i dispositivi non aggiornati nell'ambiente in uso, è necessario definire criteri correlati. Questi criteri consentono di assicurarsi che siano state prese in esame tutte le considerazioni relative ai dispositivi non aggiornati. Le sezioni seguenti offrono esempi per le considerazioni sui criteri più comuni. 

### <a name="cleanup-account"></a>Account di pulizia

Per aggiornare un dispositivo di Azure AD, è necessario un account con assegnato uno dei ruoli seguenti:

- Amministratore globale
- Amministratore dispositivo cloud
- Amministratore del servizio Intune

Nel criterio di pulizia selezionare gli account con i ruoli richiesti assegnati. 

### <a name="timeframe"></a>Intervallo di tempo

Definire un intervallo di tempo che è l'indicatore per un dispositivo non aggiornato. Quando si definisce l'intervallo di tempo, fattorizzare la finestra indicata per l'aggiornamento del timestamp dell'attività nel valore. Ad esempio, non è consigliabile considerare un timestamp minore di 21 giorni (inclusa la varianza) come indicatore per un dispositivo non aggiornato. Esistono scenari in cui un dispositivo può sembrare non aggiornato ma non lo è. Ad esempio, il proprietario del dispositivo interessato potrebbe essere in vacanza o in malattia.  che supera l'intervallo di tempo per i dispositivi non aggiornati.

### <a name="disable-devices"></a>Disabilitare i dispositivi

Non è consigliabile eliminare immediatamente un dispositivo che sembra non aggiornato, perché non è possibile annullare un'operazione di eliminazione in caso di falsi positivi. Come procedura consigliata, disabilitare un dispositivo per un periodo di tolleranza prima di eliminarlo. Nel criterio definire un intervallo di tempo per disabilitare un dispositivo prima di eliminarlo.

### <a name="mdm-controlled-devices"></a>Dispositivi controllati tramite MDM

Se il dispositivo è sotto il controllo di Intune o di qualsiasi altra soluzione MDM, ritirare il dispositivo nel sistema di gestione prima di disabilitarlo o eliminarlo.

### <a name="system-managed-devices"></a>Dispositivi gestiti dal sistema

Non eliminare i dispositivi gestiti dal sistema. Si tratta in genere di dispositivi come il pilota automatico e, Una volta eliminati, non è possibile effettuare nuovamente il provisioning di questi dispositivi. Per impostazione predefinita, il nuovo cmdlet `get-msoldevice` esclude i dispositivi gestiti dal sistema. 

### <a name="hybrid-azure-ad-joined-devices"></a>Dispositivi aggiunti all'identità ibrida di Azure AD

I dispositivi aggiunti ad Azure AD ibrido devono seguire i criteri per la gestione dei dispositivi non aggiornati in locale. 

Per eseguire la pulizia di Azure AD:

- **Dispositivi Windows 10**: disabilitare o eliminare i dispositivi Windows 10 nell'istanza di AD locale e consentire ad Azure AD Connect di sincronizzare lo stato del dispositivo modificato in Azure AD.
- **Windows 7/8** : disabilitare o eliminare prima i dispositivi Windows 7/8 in Active Directory locale. Non è possibile usare Azure AD Connect per disabilitare o eliminare i dispositivi Windows 7/8 in Azure AD. Al contrario, quando si apportano le modifiche in locale, è necessario disabilitare/eliminare in Azure AD.

> [!NOTE]
>* L'eliminazione dei dispositivi nell'istanza locale di AD o Azure AD non rimuove la registrazione nel client. L'accesso alle risorse verrà impedito solo usando il dispositivo come identità, ad esempio l'accesso condizionale. Leggere altre informazioni su come [rimuovere la registrazione nel client](faq.md#hybrid-azure-ad-join-faq).
>* L'eliminazione di un dispositivo Windows 10 solo in Azure AD sincronizza nuovamente il dispositivo dal locale usando Azure AD Connect ma come nuovo oggetto in stato "in sospeso". Nel dispositivo è necessaria una nuova registrazione.
>* La rimozione del dispositivo dall'ambito di sincronizzazione per i dispositivi Windows 10/server 2016 eliminerà il dispositivo Azure AD. Se si aggiunge di nuovo all'ambito di sincronizzazione, verrà inserito un nuovo oggetto in stato "in sospeso". È necessaria una nuova registrazione del dispositivo.
>* Se non si usa Azure AD Connect per sincronizzare i dispositivi Windows 10, ad esempio usando solo AD FS per la registrazione, è necessario gestire il ciclo di vita in modo analogo ai dispositivi Windows 7/8.


### <a name="azure-ad-joined-devices"></a>Dispositivi aggiunti ad Azure AD

Disabilitare o eliminare i dispositivi aggiunti ad Azure AD in Azure AD.

> [!NOTE]
>* L'eliminazione di un dispositivo Azure AD non comporta la rimozione della registrazione nel client. L'accesso alle risorse verrà impedito solo usando il dispositivo come identità, ad esempio l'accesso condizionale. 
>* Ulteriori informazioni su [come separare il Azure ad](faq.md#azure-ad-join-faq) 

### <a name="azure-ad-registered-devices"></a>Dispositivi registrati in Azure AD

Disabilitare o eliminare i dispositivi registrati in Azure AD in Azure AD.

> [!NOTE]
>* L'eliminazione di un dispositivo registrato Azure AD in Azure AD non comporta la rimozione della registrazione nel client. L'accesso alle risorse verrà impedito solo usando il dispositivo come identità, ad esempio l'accesso condizionale.
>* Altre informazioni su [come rimuovere una registrazione nel client](faq.md#azure-ad-register-faq)

## <a name="clean-up-stale-devices-in-the-azure-portal"></a>Eseguire la pulizia dei dispositivi non aggiornati nel portale di Azure  

Anche se è possibile eliminare i dispositivi non aggiornati nel portale di Azure, è più efficiente gestire questo processo tramite uno script di PowerShell. Usare il modulo PowerShell V1 più recente per usare il filtro timestamp ed escludere tramite filtro i dispositivi gestiti dal sistema come il pilota automatico. A questo punto, non è consigliabile usare PowerShell V2.

Una tipica routine comprende i passaggi seguenti:

1. Connettersi ad Azure Active Directory usando il cmdlet [Connect-MsolService](https://docs.microsoft.com/powershell/module/msonline/connect-msolservice?view=azureadps-1.0).
1. Ottenere l'elenco dei dispositivi.
1. Disabilitare il dispositivo usando il cmdlet [Disable-MsolDevice](https://docs.microsoft.com/powershell/module/msonline/disable-msoldevice?view=azureadps-1.0). 
1. Attendere il periodo di tolleranza del numero di giorni specificati prima di eliminare il dispositivo.
1. Rimuovere il dispositivo usando il cmdlet [Remove-MsolDevice](https://docs.microsoft.com/powershell/module/msonline/remove-msoldevice?view=azureadps-1.0).

### <a name="get-the-list-of-devices"></a>Ottenere l'elenco dei dispositivi.

Per ottenere tutti i dispositivi e archiviare i dati restituiti in un file CSV:

```PowerShell
Get-MsolDevice -all | select-object -Property Enabled, DeviceId, DisplayName, DeviceTrustType, Approxi
mateLastLogonTimestamp | export-csv devicelist-summary.csv
```

Se nella directory è presente un numero elevato di dispositivi, usare il filtro timestamp per limitare il numero di dispositivi restituiti. Per ottenere tutti i dispositivi con un timestamp precedente a una data specifica e archiviare i dati restituiti in un file CSV: 

```PowerShell
$dt = [datetime]’2017/01/01’
Get-MsolDevice -all -LogonTimeBefore $dt | select-object -Property Enabled, DeviceId, DisplayName, DeviceTrustType, ApproximateLastLogonTimestamp | export-csv devicelist-olderthan-Jan-1-2017-summary.csv
```

## <a name="what-you-should-know"></a>Informazioni utili

### <a name="why-is-the-timestamp-not-updated-more-frequently"></a>Perché il timestamp non viene aggiornato più frequentemente?

Il timestamp viene aggiornato per supportare scenari del ciclo di vita dei dispositivi. Non si tratta di un controllo. Usare i log di controllo di accesso per aggiornamenti più frequenti nel dispositivo.

### <a name="why-should-i-worry-about-my-bitlocker-keys"></a>Perché occorre preoccuparsi delle chiavi BitLocker?

Quando sono configurate, le chiavi BitLocker per i dispositivi Windows 10 vengono archiviate nell'oggetto dispositivo in Azure AD. Se si elimina un dispositivo non aggiornato, vengono eliminate anche le chiavi BitLocker archiviate nel dispositivo. È necessario determinare se i criteri di pulizia sono allineati con l'effettivo ciclo di vita del dispositivo prima di eliminare un dispositivo non aggiornato. 

### <a name="why-should-i-worry-about-windows-autopilot-devices"></a>Perché è necessario preoccuparsi dei dispositivi Windows Autopilot?

Quando un dispositivo Azure AD è stato associato a un oggetto di Windows Autopilot, possono verificarsi i tre scenari seguenti se il dispositivo verrà reimpiegato in futuro:
- Con le distribuzioni basate sugli utenti di Windows Autopilot senza usare il guanto bianco, viene creato un nuovo dispositivo Azure AD, che però non verrà contrassegnato con ZTDID.
- Con le distribuzioni in modalità self-Deploying di Windows Autopilot, avranno esito negativo perché non è possibile trovare un dispositivo associato Azure AD.  (Si tratta di un meccanismo di sicurezza per assicurarsi che nessun dispositivo "imposto" provi a partecipare Azure AD senza credenziali). L'errore indicherà una mancata corrispondenza ZTDID.
- Con le distribuzioni del guanto bianco di Windows Autopilot, non riusciranno perché non è possibile trovare un dispositivo Azure AD associato. (In background, le distribuzioni di guanti bianchi usano lo stesso processo di distribuzione automatica, quindi applicano gli stessi meccanismi di sicurezza).

### <a name="how-do-i-know-all-the-type-of-devices-joined"></a>Come si riconoscono tutti i tipi di dispositivi aggiunti?

Per saperne di più sui diversi tipi, vedere la [panoramica sulla gestione dei dispositivi](overview.md).

### <a name="what-happens-when-i-disable-a-device"></a>Cosa accade quando si disabilita un dispositivo?

Viene negata qualsiasi autenticazione in cui viene usato un dispositivo per eseguire l'autenticazione in Azure AD. Esempi comuni:

- **Dispositivo aggiunto ad Azure AD ibrido**: gli utenti potrebbero essere in grado di usare il dispositivo per accedere al dominio locale. Tuttavia, non riescono ad accedere alle risorse di Azure AD come Office 365.
- **Dispositivo aggiunto ad Azure AD**: gli utenti non possono usare il dispositivo per eseguire l'accesso. 
- **Utenti di dispositivi mobili**: gli utenti non riescono ad accedere alle risorse di Azure AD come Office 365. 

## <a name="next-steps"></a>Passaggi successivi

Per una panoramica sulla gestione del dispositivo nel portale di Azure AD, vedere [Gestione dei dispositivi tramite il portale di Azure](device-management-azure-portal.md)
