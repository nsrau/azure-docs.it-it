---
title: Domande frequenti Enterprise State Roaming-Azure Active Directory
description: Domande frequenti su ESR
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 02/12/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: na
ms.collection: M365-identity-device-management
ms.openlocfilehash: d9510bd564ced2f458a9a78ff23200bb32358c3e
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/01/2020
ms.locfileid: "89268537"
---
# <a name="settings-and-data-roaming-faq"></a>Domande frequenti su impostazioni e dati in roaming

Questo articolo offre le risposte ad alcune possibili domande degli amministratori IT in merito alle impostazioni e alla sincronizzazione dei dati delle app.

## <a name="what-data-roams"></a>Quali tipi di dati sono disponibili in roaming?

**Impostazioni di Windows**: impostazioni del PC compilate nel sistema operativo Windows. In genere, si tratta delle impostazioni che personalizzano il computer e includono le categorie generiche seguenti:

* *Tema*, che include funzionalità quali il tema del desktop e le impostazioni della barra delle applicazioni.
* *Impostazioni di Internet Explorer*, incluse le schede aperte di recente e i Preferiti.
* *Impostazioni del browser Microsoft Edge*, come i Preferiti e l'elenco di lettura.
* *Password*, incluse le password Internet, i profili Wi-Fi e altro.
* *Preferenze di lingua*, che includono le impostazioni per i layout di tastiera, la lingua di sistema, data/ora e altro ancora.
* *Funzionalità di accessibilità*, ad esempio tema a contrasto elevato, Assistente vocale e Lente di ingrandimento.
* *Altre impostazioni di Windows*, ad esempio le impostazioni del mouse.

> [!NOTE]
> Questo articolo si applica al browser Microsoft Edge legacy basato su HTML avviato con Windows 10 nel 2015 luglio. L'articolo non si applica al nuovo browser Microsoft Edge basato su cromo rilasciato il 15 gennaio 2020. Per altre informazioni sul comportamento di sincronizzazione per il nuovo Microsoft Edge, vedere l'articolo [Microsoft Edge Sync](/deployedge/microsoft-edge-enterprise-sync).

**Dati dell'applicazione**: le app di Windows universale permettono di scrivere i dati delle impostazioni in una cartella di profilo mobile per sincronizzare automaticamente i dati scritti al suo interno. Il singolo sviluppatore di app deve progettare un'app in grado di sfruttare questa funzionalità. Per altre informazioni su come sviluppare un'app di Windows universale che usa il roaming, vedere l' [API di archiviazione AppData](/windows/uwp/design/app-settings/store-and-retrieve-app-data) e il [Blog per sviluppatori roaming di Windows 8 AppData](https://blogs.windows.com/windowsdeveloper/2016/05/04/roaming-app-data-and-the-user-experience/).

## <a name="what-account-is-used-for-settings-sync"></a>Quale account si usa per la sincronizzazione delle impostazioni?

In Windows 8.1, la sincronizzazione delle impostazioni usava sempre gli account Microsoft per utenti. Gli utenti aziendali hanno la possibilità di connettere un account Microsoft all'account di dominio Active Directory per accedere alla sincronizzazione delle impostazioni. In Windows 10, questa funzionalità connessa account Microsoft viene sostituita con un Framework di account primario/secondario.

L'account principale è l'account utilizzato per accedere a Windows. Può essere un account Microsoft, un account Azure Active Directory (Azure AD), un account Active Directory locale oppure un account locale. Oltre all'account primario, gli utenti di Windows 10 possono aggiungere uno o più account cloud secondari al proprio dispositivo. Un account secondario è in genere un account Microsoft, un account Azure AD o un altro account, ad esempio Gmail o Facebook. Questi account secondari permettono di accedere a ulteriori servizi, ad esempio Single Sign-On e Windows Store, ma non consentono di sincronizzare le impostazioni.

In Windows 10 solo l'account primario per il dispositivo può essere usato per la sincronizzazione delle impostazioni. Per informazioni, vedere l'articolo relativo alla [procedura di aggiornamento dalla sincronizzazione delle impostazioni dell'account Microsoft in Windows 8 alla sincronizzazione delle impostazioni di Azure AD in Windows 10](enterprise-state-roaming-faqs.md#how-do-i-upgrade-from-microsoft-account-settings-sync-in-windows-8-to-azure-ad-settings-sync-in-windows-10).

I dati non vengono mai combinati tra i diversi account utente nel dispositivo. Esistono due regole per la sincronizzazione delle impostazioni:

* Per le impostazioni di Windows verrà sempre effettuato il roaming con l'account primario.
* I dati dell'app verranno contrassegnati con l'account usato per acquisire l'app. Vengono sincronizzate solo le app contrassegnate con l'account primario. L'assegnazione di tag di proprietà delle app viene determinata quando un'app viene caricata sul lato tramite Windows Store o gestione di dispositivi mobili (MDM).

Se non è possibile identificare il proprietario di un'app, il roaming dell'app verrà eseguito con l'account primario. Se si aggiorna un dispositivo da Windows 8 o Windows 8.1 a Windows 10, tutte le app verranno contrassegnate come acquisite dall'account Microsoft. Questo perché la maggior parte degli utenti acquisisce le app tramite Windows Store, ma prima di Windows 10 gli account di Azure AD non erano supportati in Windows Store. Se un'app viene installata tramite una licenza offline, l'app verrà contrassegnata usando l'account primario nel dispositivo.

> [!NOTE]
> Nei dispositivi Windows 10 di proprietà dell'azienda e connessi ad Azure AD non è più possibile connettere gli account Microsoft a un account di dominio. La possibilità di connettere un account Microsoft a un account di dominio e di sincronizzare tutti i dati degli utenti con l'account Microsoft (con il roaming dell'account Microsoft tramite l'account Microsoft connesso e la funzionalità di Active Directory) non è più disponibile con i dispositivi Windows 10 aggiunti a un ambiente Active Directory o Azure AD connesso.

## <a name="how-do-i-upgrade-from-microsoft-account-settings-sync-in-windows-8-to-azure-ad-settings-sync-in-windows-10"></a>Come si esegue l'aggiornamento dalla sincronizzazione delle impostazioni dell'account Microsoft in Windows 8 alla sincronizzazione delle impostazioni di Azure AD in Windows 10?

Se l'aggiunta al dominio Active Directory avviene eseguendo Windows 8.1 con un account Microsoft connesso, le impostazioni saranno sincronizzate tramite il proprio account Microsoft. Dopo l'aggiornamento a Windows 10, le impostazioni utente continueranno a essere sincronizzate tramite l'account Microsoft, a condizione che l'utente sia aggiunto a un dominio e che il dominio di Active Directory non si connetta ad Azure AD.

Se invece il dominio di Active Directory locale si connette ad Azure AD, il dispositivo tenterà di sincronizzare le impostazioni utilizzando l'account connesso di Azure AD. Se l'amministratore di Azure AD non abilita Enterprise State Roaming, l'account connesso di Azure AD smetterà di sincronizzare le impostazioni. Se un utente di Windows 10 accede con un'identità di Azure AD, potrà iniziare a sincronizzare le impostazioni di Windows non appena l'amministratore abiliterà questa funzione tramite Azure AD.

Se si archiviano dati personali sul dispositivo aziendale, è opportuno tenere presente che i dati delle applicazioni e del sistema operativo Windows inizieranno a sincronizzarsi con Azure AD. Le implicazioni sono le seguenti:

* Le impostazioni dell'account Microsoft personale non saranno più le impostazioni dell'account aziendale o dell'istituto di istruzione di Azure AD. Questo perché le impostazioni di sincronizzazione dell'account Microsoft e di Azure AD inizieranno a utilizzare account distinti.
* I dati personali come le password Wi-Fi, le credenziali Web e i Preferiti di Internet Explorer in precedenza sincronizzati tramite un account Microsoft connesso ora saranno sincronizzati tramite Azure AD.

## <a name="how-do-microsoft-account-and-azure-ad-enterprise-state-roaming-interoperability-work"></a>Come funziona l'interoperabilità fra l'account Microsoft e l'Enterprise State Roaming di Azure AD?

Nella versione di Windows 10 di novembre 2015 o successiva, l'Enterprise State Roaming è supportato per un solo account alla volta. Se si accede a Windows usando un account aziendale o dell'istituto di istruzione di Azure AD, tutti i dati verranno sincronizzati tramite Azure AD. Se si accede a Windows usando un account Microsoft personale, tutti i dati saranno sincronizzati tramite l'account Microsoft. Per i dati delle app universali, il roaming verrà effettuato usando solo l'account di accesso principale al dispositivo e verrà eseguito solo se la licenza dell'app è di proprietà dell'account principale. I dati di app universali per le app appartenenti ad account secondari non verranno sincronizzati.

## <a name="do-settings-sync-for-azure-ad-accounts-from-multiple-tenants"></a>È possibile sincronizzare le impostazioni per gli account Azure AD da più tenant?

Quando su uno stesso dispositivo sono presenti più account di Azure AD provenienti da tenant diversi, è necessario aggiornare il registro del dispositivo per comunicare con il servizio Azure Rights Management per ogni tenant di Azure AD.  

1. Accedere al GUID di ogni tenant di Azure AD. Aprire il portale di Azure e selezionare un tenant di Azure AD. Il GUID per il tenant si trova nella pagina Proprietà del tenant selezionato (https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties), con etichetta **ID Directory**. 
2. Dopo aver eseguito il GUID, sarà necessario aggiungere la chiave del registro di sistema **HKEY_LOCAL_MACHINE \<tenant ID GUID> \software\microsoft\windows\settingsync\winmsipc**.
   Dalla chiave **tenant ID GUID**, creare un nuovo valore multistringa (REG-MULTI-SZ) denominato **AllowedRMSServerUrls**. Come dati, specificare gli URL del punto di distribuzione delle licenze di altri tenant Azure ai quali accede il dispositivo.
3. Per trovare gli URL del punto di distribuzione delle licenze, è possibile eseguire il cmdlet **Get-AadrmConfiguration** dal modulo AADRM. Se i valori **LicensingIntranetDistributionPointUrl** e **LicensingExtranetDistributionPointUrl** sono diversi, specificarli entrambi. Se i valori sono uguali, specificare il valore solo una volta.

## <a name="what-are-the-roaming-settings-options-for-existing-windows-desktop-applications"></a>Quali sono le possibili impostazioni di roaming per le applicazioni desktop di Windows esistenti?

Il roaming è disponibile solo per le app di Windows universale. Sono disponibili due opzioni per l'abilitazione del roaming in un'applicazione desktop di Windows esistente:

* Tramite il [Desktop Bridge](https://aka.ms/desktopbridge) , le app desktop di Windows esistenti vengono trasferite alla piattaforma UWP (Universal Windows Platform). Da qui, verrà richiesto di modificare lievemente il codice per poter sfruttare il roaming dei dati delle app di Azure AD. Desktop Bridge fornisce alle app dell'utente un'identità di un'app necessaria per abilitare il roaming dei dati dell'app per le app desktop esistenti.
* [User Experience Virtualization (UE-V)](/previous-versions//dn458947(v=vs.85)) consente di creare un modello di impostazioni personalizzate per le app desktop di Windows esistenti e di abilitare il roaming per le applicazioni Win32. Questa opzione non richiede allo sviluppatore di app di modificare il codice dell'app. UE-V è riservato al roaming del servizio Active Directory locale per i clienti che hanno acquistato Microsoft Desktop Optimization Pack.

Gli amministratori possono configurare UE-V per eseguire il roaming dei dati delle app desktop di Windows modificando il roaming delle impostazioni del sistema operativo Windows e dei dati delle app universali tramite i [criteri di gruppo di UE-V](/microsoft-desktop-optimization-pack/uev-v2/configuring-ue-v-2x-with-group-policy-objects-both-uevv2):

* Eseguendo il roaming dei criteri di gruppo delle impostazioni di Windows
* Non sincronizzando i criteri di gruppo delle app Windows
* Eseguendo il roaming nella sezione delle applicazioni di Internet Explorer

In futuro è possibile che Microsoft esamini eventuali soluzioni per integrare strettamente UE-V in Windows ed estendere UE-V per il roaming delle impostazioni tramite il cloud di Azure AD.

## <a name="can-i-store-synced-settings-and-data-on-premises"></a>È possibile archiviare le impostazioni e i dati sincronizzati in locale?

La funzione Enterprise State Roaming permette di archiviare tutti i dati sincronizzati nel cloud di Microsoft. La soluzione di roaming locale è disponibile in UE-V.

## <a name="who-owns-the-data-thats-being-roamed"></a>Chi possiede i dati di cui viene effettuato il roaming?

I dati in roaming sono di proprietà delle aziende tramite Enterprise State Roaming. I dati vengono archiviati in un data center di Azure. Tutti i dati utente in movimento e inattivi vengono crittografati nel cloud usando il servizio Azure Rights Management da Azure Information Protection. Si tratta di un miglioramento rispetto alla sincronizzazione delle impostazioni basata sull'account Microsoft, che consente di crittografare solo alcuni dati sensibili, come le credenziali utente, prima che lascino il dispositivo.

Microsoft si impegna a proteggere i dati dei clienti. I dati delle impostazioni di ogni utente aziendale vengono crittografati automaticamente tramite il servizio Azure Rights Management prima che lascino il dispositivo Windows 10, per impedire agli altri utenti di leggerli. Se l'organizzazione ha una sottoscrizione a pagamento per Azure Rights Management, è possibile usare altre funzionalità di protezione, ad esempio il rilevamento e la revoca di documenti, la protezione automatica di messaggi di posta elettronica che contengono informazioni riservate e la gestione delle proprie chiavi, ovvero la soluzione BYOK ("Bring Your Own Key"). Per altre informazioni su queste funzionalità e sul funzionamento del servizio di protezione, vedere [Informazioni su Microsoft Azure Rights Management](/azure/information-protection/what-is-information-protection).

## <a name="can-i-manage-sync-for-a-specific-app-or-setting"></a>È possibile gestire la sincronizzazione per un'app o un'impostazione specifica?

In Windows 10 non è disponibile alcuna impostazione di Criteri di gruppo o MDM per disabilitare il roaming per una singola applicazione. Gli amministratori del tenant possono disabilitare la sincronizzazione dei dati di tutte le app presenti in un dispositivo gestito, ma non esiste un controllo più dettagliato a livello di singola app o all'interno di ogni app.

## <a name="how-can-i-enable-or-disable-roaming"></a>Come si fa ad abilitare o disabilitare il roaming?

Nell'app **Impostazioni** accedere alla voce **Account** > **Sincronizza le impostazioni**. In questa pagina è possibile visualizzare gli account usati per il roaming delle impostazioni e abilitare o disabilitare singoli gruppi di impostazioni da sottoporre a roaming.

## <a name="what-is-microsofts-recommendation-for-enabling-roaming-in-windows-10"></a>Che cosa consiglia Microsoft per abilitare il roaming in Windows 10?

Microsoft offre alcune soluzioni per il roaming delle impostazioni, inclusi Profili utente mobili, UE-V e Enterprise State Roaming.  Microsoft si impegna a effettuare un investimento in Enterprise stato Roaming nelle future versioni di Windows. Se l'organizzazione non è pronta a trasferire i dati nel cloud o non sa come fare, è consigliabile usare UE-V come tecnologia di roaming principale. Se l'organizzazione è decisa a passare al cloud, ma necessita di supporto per il roaming delle applicazioni desktop di Windows esistenti, si consiglia di usare sia Enterprise State Roaming sia UE-V. Per quanto simili, UE-V ed Enterprise State Roaming non si escludono a vicenda. Si tratta piuttosto di tecnologie complementari, che assicurano che l'organizzazione fornisca i servizi di roaming necessari per gli utenti.  

Quando si usano Enterprise State Roaming e UE-V, vengono applicate le regole seguenti:

* Il servizio Enterprise State Roaming è l'agente di roaming principale nel dispositivo. UE-V viene usato per integrare il "divario Win32".
* Quando si usano i criteri di gruppo UE-V, è necessario disabilitare le impostazioni di roaming di UE-V per Windows e i dati dell'app UWP moderna. poiché già incluso in Enterprise State Roaming.

## <a name="how-does-enterprise-state-roaming-support-virtual-desktop-infrastructure-vdi"></a>In che modo Enterprise State Roaming supporta la Virtual Desktop Infrastructure (VDI)?

Enterprise State Roaming è supportato sulle SKU dei client Windows 10, ma non su quelle dei server. Se una VM client è ospitata in un computer hypervisor e si accede da remoto alla macchina virtuale, verrà effettuato il roaming dei dati. Se più utenti che condividono lo stesso sistema operativo accedono da remoto a un server per un'esperienza desktop completa, il roaming potrebbe non funzionare. Quest'ultimo scenario basato sulla sessione non è ufficialmente supportato.

## <a name="what-happens-when-my-organization-purchases-a-subscription-that-includes-azure-rights-management-after-using-roaming"></a>Che cosa accade quando l'organizzazione acquista una sottoscrizione che include Azure Rights Management dopo aver usato il roaming?

Se l'organizzazione usa già il roaming in Windows 10 con la sottoscrizione gratuita di Azure Rights Management per uso limitato, l'acquisto di una [sottoscrizione a pagamento](https://azure.microsoft.com/pricing/details/information-protection/) che include il servizio di protezione Azure Rights Management non influirà in alcun modo sulla funzionalità di roaming e non saranno necessarie modifiche alla configurazione da parte dell'amministratore IT.

## <a name="known-issues"></a>Problemi noti

Per un elenco dei problemi noti, vedere la documentazione nella sezione relativa alla [risoluzione](enterprise-state-roaming-troubleshooting.md) dei problemi. 

## <a name="next-steps"></a>Passaggi successivi 

Per una panoramica, vedere [Panoramica di Enterprise state roaming](enterprise-state-roaming-overview.md)