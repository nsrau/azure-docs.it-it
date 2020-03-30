---
title: Condizioni per l'utilizzo - Azure Active Directory Documenti Microsoft
description: Inizia a usare le condizioni per l'uso di Azure Active Directory per presentare le informazioni a dipendenti o ospiti prima di ottenere l'accesso.
services: active-directory
ms.service: active-directory
ms.subservice: compliance
ms.topic: conceptual
ms.date: 05/29/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jocastel
ms.collection: M365-identity-device-management
ms.openlocfilehash: a2f06a7c88a7c17f5f93201192664c2d4a97564e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480964"
---
# <a name="azure-active-directory-terms-of-use"></a>Condizioni per l'utilizzo di Azure Active DirectoryAzure Active Directory terms of use

Le condizioni per l'uso di Azure AD offre un metodo semplice che le organizzazioni possono usare per presentare informazioni agli utenti finali. In questo modo si garantisce che gli utenti vedano le dichiarazioni rilevanti di non responsabilità che si riferiscono ai requisiti legali o di conformità. In questo articolo viene descritto come iniziare a utilizzare le condizioni per l'utilizzo.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="overview-videos"></a>Panoramica video

Il video seguente offre una rapida panoramica delle condizioni per l'utilizzo.

>[!VIDEO https://www.youtube.com/embed/tj-LK0abNao]

Per altri video, vedere:
- [Come distribuire le condizioni per l'utilizzo in Azure Active DirectoryHow to deploy terms of use in Azure Active Directory](https://www.youtube.com/embed/N4vgqHO2tgY)
- [Come implementare le condizioni per l'utilizzo in Azure Active DirectoryHow to roll out terms of use in Azure Active Directory](https://www.youtube.com/embed/t_hA4y9luCY)

## <a name="what-can-i-do-with-terms-of-use"></a>Cosa posso fare con le condizioni d'uso?

Le condizioni per l'utilizzo di Azure AD hanno le funzionalità seguenti:Azure AD terms of use has the following capabilities:

- Richiedere ai dipendenti o agli ospiti di accettare le condizioni per l'utilizzo prima di ottenere l'accesso.
- Richiedere ai dipendenti o agli ospiti di accettare le condizioni per l'utilizzo su ogni dispositivo prima di ottenere l'accesso.
- Richiedere ai dipendenti o agli ospiti di accettare le condizioni per l'utilizzo in base a una pianificazione ricorrente.
- Richiedere ai dipendenti o agli ospiti di accettare le condizioni per l'utilizzo prima di registrare le informazioni di sicurezza in Azure Multi-Factor Authentication (MFA).
- Richiedere ai dipendenti di accettare le condizioni per l'utilizzo prima di registrare le informazioni di sicurezza nella reimpostazione della password self-service (SSPR) di Azure AD.
- Presentare le condizioni generali per l'utilizzo per tutti gli utenti dell'organizzazione.
- Presentare condizioni per l'utilizzo specifiche in base agli attributi utente (ad es. medici o infermieri oppure dipendenti nazionali o internazionali, tramite [gruppi dinamici](../users-groups-roles/groups-dynamic-membership.md)).
- Presenta condizioni d'uso specifiche per l'accesso ad applicazioni ad alto impatto aziendale, come Salesforce.
- Presentare le condizioni d'uso in diverse lingue.
- Elenca chi ha o non ha accettato o non ha accettato le tue condizioni d'uso.
- Contribuire a rispettare le normative sulla privacy.
- Visualizzare un registro delle condizioni per l'utilizzo per la conformità e il controllo.
- Creare e gestire le condizioni per l'utilizzo utilizzando le API di [Microsoft Graph](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/agreement) (attualmente in anteprima).

## <a name="prerequisites"></a>Prerequisiti

Per usare e configurare le condizioni per l'uso di Azure AD, è necessario disporre di:To use and configure Azure AD terms of use, you must have:

- Una sottoscrizione di Azure AD Premium P1, P2, EMS E3 o EMS E5.
   - Se non si dispone di una di queste sottoscrizioni, è possibile [ottenere Azure AD Premium](../fundamentals/active-directory-get-started-premium.md) oppure [abilitare la versione di valutazione di Azure AD Premium](https://azure.microsoft.com/trial/get-started-active-directory/).
- Uno dei seguenti account di amministratore per la directory da configurare:
   - Amministratore globale
   - Amministratore della sicurezza
   - Amministratore di accesso condizionale

## <a name="terms-of-use-document"></a>Documento sulle condizioni per l'utilizzo

Le condizioni per l'uso di Azure AD usano il formato PDF per presentare il contenuto. Il contenuto del file PDF può essere di qualsiasi tipo, ad esempio documenti di contratti esistenti. Questo consente di acquisire il consenso degli utenti finali durante l'accesso. Per supportare gli utenti sui dispositivi mobili, le dimensioni del carattere consigliato nel file PDF sono 24 punti.

## <a name="add-terms-of-use"></a>Aggiungere le condizioni per l'utilizzo

Dopo aver finalizzato il documento delle condizioni per l'utilizzo, utilizzare la procedura seguente per aggiungerlo.

1. Accedere ad Azure come amministratore globale, amministratore della sicurezza o amministratore di accesso condizionale.
1. Passare a **Condizioni per l'utilizzo** all'indirizzo [https://aka.ms/catou](https://aka.ms/catou).

   ![Accesso condizionale - Pannello Condizioni per l'usoConditional Access - Terms of use blade](./media/terms-of-use/tou-blade.png)

1. Fare clic su **Nuove condizioni**.

   ![Nuovo riquadro Del termine d'uso per specificare le impostazioni delle condizioni per l'utilizzo](./media/terms-of-use/new-tou.png)

1. Nella casella Nome immettere un nome per le condizioni per l'utilizzo nel portale di Azure.In the **Name** box, enter a name for the terms of use that will be used in the Azure portal.
1. Nella casella **Nome visualizzato** immettere un titolo visualizzato dagli utenti quando eseguono l'accesso.
1. Per **il documento Delle Condizioni per l'utilizzo,** individuare il PDF delle condizioni per l'utilizzo finalizzato e selezionarlo.
1. Selezionare la lingua per il documento delle condizioni per l'utilizzo. L'opzione relativa alla lingua consente di caricare più versioni delle condizioni d'uso, ognuna in una lingua diversa. La versione visualizzata all'utente finale dipenderà dalle preferenze del browser.
1. Per richiedere agli utenti finali di visualizzare le condizioni per l'utilizzo prima di accettarle, impostare **Richiedi agli utenti di espandere le condizioni per l'utilizzo** su **Attivato**.
1. Per richiedere agli utenti finali di accettare le condizioni per l'utilizzo su ogni dispositivo a cui accedono, impostare **Richiedi agli utenti di acconsentire a ogni dispositivo** su **On**. Gli utenti potrebbero essere tenuti a installare applicazioni aggiuntive se questa opzione è abilitata. Per ulteriori informazioni, consultate [Condizioni per l'utilizzo per dispositivo.](#per-device-terms-of-use)
1. Se si desidera far scadere i termini di utilizzo consensi in base a una pianificazione, **impostare Scadenza su** **Attivato**. Se si imposta su On, vengono visualizzate due impostazioni aggiuntive di pianificazione.

   ![Scadenza acconsente le impostazioni per impostare la data di inizio, la frequenza e la durata](./media/terms-of-use/expire-consents.png)

1. Utilizzare le impostazioni Scadenza a partire e **Frequenza** per specificare la pianificazione delle **scadenze** delle condizioni per l'utilizzo. La tabella seguente illustra il risultato di un paio di impostazioni di esempio:

   | Scadenza a partire da | Frequenza | Risultato |
   | --- | --- | --- |
   | Data odierna  | Mensile | A partire da oggi, gli utenti devono accettare le condizioni per l'utilizzo e quindi accettare nuovamente ogni mese. |
   | Data nel futuro  | Mensile | A partire da oggi, gli utenti devono accettare le condizioni per l'utilizzo. Quando si verifica la data futura, scadono i consensi e quindi gli utenti devono riaccettare ogni mese.  |

   Ad esempio, se si imposta la scadenza a partire dalla data **1 gen** e la frequenza su **Mensile**, ecco come le scadenze potrebbero verificarsi per i due utenti:

   | Utente | Prima data di accettazione | Prima data di scadenza | Seconda data di scadenza | Terza data di scadenza |
   | --- | --- | --- | --- | --- |
   | Alice | 1 gen | 1 feb | 1 mar | 1 apr |
   | Bob | 15 gen | 1 feb | 1 mar | 1 apr |

1. Utilizzare l'impostazione Durata prima che **riaccetta (giorni)** per specificare il numero di giorni prima che l'utente debba accettare nuovamente le condizioni per l'utilizzo. Ciò consente agli utenti di seguire una pianificazione personalizzata. Ad esempio, se si imposta la durata su **30** giorni, ecco come le scadenze potrebbero verificarsi per due utenti:

   | Utente | Prima data di accettazione | Prima data di scadenza | Seconda data di scadenza | Terza data di scadenza |
   | --- | --- | --- | --- | --- |
   | Alice | 1 gen | 31 gen | 2 mar | 1 apr |
   | Bob | 15 gen | 14 feb | 16 mar | 15 apr |

   È possibile usare le impostazioni **Scadenza consensi** e **Durata prima di una nuova accettazione richiede (giorni)** insieme, ma in genere si usa o l'una o l'altra.

1. In **Accesso condizionale**utilizzare l'elenco **Imponi con modello** di criteri di accesso condizionale per selezionare il modello per applicare le condizioni per l'utilizzo.

   ![Elenco a discesa Accesso condizionale per selezionare un modello di criteriConditional Access drop-down list to select a policy template](./media/terms-of-use/conditional-access-templates.png)

   | Modello | Descrizione |
   | --- | --- |
   | **Accesso alle app cloud per tutti i guest** | Verrà creato un criterio di accesso condizionale per tutti gli ospiti e tutte le app cloud. Questi criteri influiscono sul portale di Azure. Dopo la creazione, potrebbe essere necessario disconnettersi e accedere. |
   | **Accesso alle app cloud per tutti gli utenti** | Verrà creato un criterio di accesso condizionale per tutti gli utenti e tutte le app cloud. Questi criteri influiscono sul portale di Azure. Dopo la creazione, sarà necessario disconnettersi e accedere. |
   | **Criteri personalizzati** | Selezionare gli utenti, i gruppi e le app a cui verranno applicate le condizioni per l'utilizzo. |
   | **Creare criteri di accesso condizionale in un secondo momentoCreate Conditional Access policy later** | Queste condizioni per l'utilizzo verranno visualizzate nell'elenco di controllo delle sovvenzioni quando si crea un criterio di accesso condizionale. |

   >[!IMPORTANT]
   >I controlli dei criteri di accesso condizionale (incluse le condizioni per l'utilizzo) non supportano l'applicazione negli account di servizio. È consigliabile escludere tutti gli account di servizio dai criteri di accesso condizionale.

    I criteri di accesso condizionale personalizzati consentono condizioni di utilizzo granulari, fino a un'applicazione cloud o a un gruppo di utenti specifico. Per ulteriori informazioni, vedere [Guida introduttiva: Richiedere l'accettazione delle condizioni per l'utilizzo prima](require-tou.md)di accedere alle app cloud.

1. Fare clic su **Crea**.

   Se è stato selezionato un modello di accesso condizionale personalizzato, viene visualizzata una nuova schermata che consente di creare i criteri di accesso condizionale personalizzati.

   ![Nuovo riquadro Accesso condizionale se si sceglie il modello di criteri di accesso condizionale personalizzato](./media/terms-of-use/custom-policy.png)

   Ora dovresti vedere le tue nuove condizioni d'uso.

   ![Nuove condizioni per l'utilizzo elencate nel pannello delle condizioni d'uso](./media/terms-of-use/create-tou.png)

## <a name="view-report-of-who-has-accepted-and-declined"></a>Visualizzare il report degli utenti che hanno accettato e rifiutato

Nel pannello delle condizioni per l'utilizzo è visualizzato il numero di utenti che hanno accettato e rifiutato. Questi conteggi e chi ha accettato/rifiutato vengono archiviati per tutta la durata delle condizioni d'uso.

1. Accedere ad Azure e passare a **Condizioni per l'utilizzo** all'indirizzo [https://aka.ms/catou](https://aka.ms/catou).

   ![Pannello Condizioni per l'uso che elenca il numero di utenti mostra sono accettati e rifiutati](./media/terms-of-use/view-tou.png)

1. Per le condizioni per l'utilizzo, fare clic sui numeri in **Accettato** o **Rifiutato** per visualizzare lo stato corrente per gli utenti.

   ![Il riquadro dei consensi delle condizioni per l'utilizzo elenca gli utenti che hanno accettato](./media/terms-of-use/accepted-tou.png)

1. Per visualizzare la cronologia per un singolo utente, fare clic sui puntini di sospensione (**... **) e quindi **Visualizza cronologia**.

   ![Menu di scelta rapida Visualizza cronologia per un utente](./media/terms-of-use/view-history-menu.png)

   Nel riquadro di visualizzazione della cronologia, si visualizza una cronologia di tutte le accettazioni, i rifiuti e le scadenze.

   ![Riquadro Visualizza cronologia elenca la cronologia accetta, rifiuta e schedi per un utente](./media/terms-of-use/view-history-pane.png)

## <a name="view-azure-ad-audit-logs"></a>Visualizzare i log di controllo di Azure AD

Se si vuole visualizzare attività aggiuntive, le condizioni per l'uso di Azure AD includono i log di controllo. Ogni consenso dell'utente genera un evento nei log di controllo che viene conservato per **30 giorni**. È possibile visualizzare questi log nel portale o scaricarli come file CSV.

Per iniziare a usare i log di controllo di Azure AD, seguire questa procedura:

1. Accedere ad Azure e passare a **Condizioni per l'utilizzo** all'indirizzo [https://aka.ms/catou](https://aka.ms/catou).
1. Selezionare una condizioni per l'utilizzo.
1. Fare clic su **Visualizza log di controllo**.

   ![Pannello Condizioni per l'uso con l'opzione Visualizza log di controllo evidenziata](./media/terms-of-use/audit-tou.png)

1. Nella schermata dei log di controllo di Azure AD è possibile filtrare le informazioni usando i menu a discesa per visualizzare informazioni specifiche dei log di controllo.

   È inoltre possibile fare clic su **Download** per scaricare le informazioni in un file con estensione CSV per l'uso in locale.

   ![Schermata dei log di controllo di Azure AD, data di destinazione, avvio da e attivitàAzure AD audit logs screen listing, target policy, initiated by, and activity](./media/terms-of-use/audit-logs-tou.png)

   Se si fa clic su un log, viene visualizzato un riquadro con i dettagli delle attività aggiuntive.

   ![Dettagli dell'attività per un registro che mostra l'attività, lo stato dell'attività, avviata da, i criteri di destinazione](./media/terms-of-use/audit-log-activity-details.png)

## <a name="what-terms-of-use-looks-like-for-users"></a>Aspetto delle condizioni per l'utilizzo per gli utenti

Una volta create e applicate le condizioni per l'utilizzo, gli utenti che rientrano nell'ambito vedranno la schermata seguente durante l'accesso.

![Condizioni per l'utilizzo di esempio che vengono visualizzate quando un utente](./media/terms-of-use/user-tou.png)

Gli utenti possono visualizzare le condizioni per l'utilizzo e, se necessario, utilizzare i pulsanti per ingrandire e ridurre.

![Visualizzazione delle condizioni per l'utilizzo con i pulsanti di zoom](./media/terms-of-use/zoom-buttons.png)

La schermata seguente mostra l'aspetto delle condizioni per l'utilizzo sui dispositivi mobili.

![Condizioni per l'utilizzo di esempio che viene visualizzato quando un utente accede a un dispositivo mobile](./media/terms-of-use/mobile-tou.png)

Gli utenti sono tenuti ad accettare le condizioni per l'utilizzo una sola volta e non vedranno più le condizioni per l'utilizzo nei successivi accessi.

### <a name="how-users-can-review-their-terms-of-use"></a>Come gli utenti possono rivedere le loro condizioni d'uso

Gli utenti possono esaminare e visualizzare le condizioni per l'utilizzo accettate utilizzando la procedura seguente.

1. Accedere a [https://myapps.microsoft.com](https://myapps.microsoft.com).
1. Nell'angolo superiore destro fare clic sul proprio nome e selezionare **Profilo**.

   ![Sito MyApps con il riquadro dell'utente aperto](./media/terms-of-use/tou14.png)

1. Nella pagina Profilo fare clic su **Verificare le condizioni d'uso**.

   ![Pagina del profilo per un utente che mostra il collegamento Rivedi condizioni per l'utilizzo](./media/terms-of-use/tou13a.png)

1. Sarà quindi possibile verificare le condizioni per l'utilizzo accettate.

## <a name="edit-terms-of-use-details"></a>Modificare i dettagli delle condizioni per l'utilizzo

È possibile modificare alcuni dettagli delle condizioni per l'utilizzo, ma non è possibile modificare un documento esistente. La procedura seguente descrive come modificare i dettagli.

1. Accedere ad Azure e passare a **Condizioni per l'utilizzo** all'indirizzo [https://aka.ms/catou](https://aka.ms/catou).
1. Selezionare le condizioni per l'utilizzo che si desidera modificare.
1. Fare clic su **Modifica le condizioni**.
1. Nel riquadro della modifica delle condizioni per l'utilizzo, modificare il nome, il nome visualizzato o richiedere che gli utenti espandano i valori.

   Se ci sono altre impostazioni che desideri modificare, come il documento PDF, richiedi agli utenti di acconsentire a tutti i dispositivi, scadere i consensi, durata prima della riura o criteri di accesso condizionale, devi creare un nuovo condizioni per l'utilizzo.

   ![Riquadro Delle condizioni per l'utilizzo con il nome e le opzioni di espansione](./media/terms-of-use/edit-tou.png)

1. Fare clic su **Salva** per salvare le modifiche.

   Dopo aver salvato le modifiche, gli utenti non dovranno accettare nuovamente le condizioni nuove.

## <a name="add-a-terms-of-use-language"></a>Aggiungere una lingua per le condizioni d'uso

Nella procedura seguente viene descritto come aggiungere un linguaggio delle condizioni per l'utilizzo.

1. Accedere ad Azure e passare a **Condizioni per l'utilizzo** all'indirizzo [https://aka.ms/catou](https://aka.ms/catou).
1. Selezionare le condizioni per l'utilizzo che si desidera modificare.
1. Nel riquadro dei dettagli fare clic sulla scheda **Lingue**.

   ![Condizioni per l'utilizzo selezionate e visualizzazione della scheda Lingue nel riquadro dei dettagli](./media/terms-of-use/languages-tou.png)

1. Fare clic su **Aggiungi lingua**.
1. Nel riquadro Aggiungi la lingua delle condizioni per l'utilizzo caricare il PDF localizzato e selezionare la lingua.

   ![Aggiungere il riquadro delle condizioni per l'utilizzo della lingua con le opzioni per caricare i PDF localizzati](./media/terms-of-use/language-add-tou.png)

1. Fare clic su **Aggiungi** per aggiungere la lingua.

## <a name="per-device-terms-of-use"></a>Condizioni d'uso per dispositivo

L'impostazione Richiedi agli utenti di **acconsentire a ogni dispositivo** consente di richiedere agli utenti finali di accettare le condizioni per l'utilizzo su ogni dispositivo a cui accedono. All'utente finale verrà richiesto di registrare il dispositivo in Azure AD. Quando il dispositivo viene registrato, l'ID del dispositivo viene utilizzato per applicare le condizioni per l'utilizzo su ogni dispositivo.

Ecco un elenco dei software e delle piattaforme supportate.

> [!div class="mx-tableFixed"]
> |  | iOS | Android | Windows 10 | Altri |
> | --- | --- | --- | --- | --- |
> | **App nativa** | Sì | Sì | Sì |  |
> | **Microsoft Edge** | Sì | Sì | Sì |  |
> | **Internet Explorer** | Sì | Sì | Sì |  |
> | **Chrome (con estensione)** | Sì | Sì | Sì |  |

Le condizioni d'uso per dispositivo hanno i seguenti vincoli:

- Un dispositivo può essere aggiunto a un solo tenant.
- Un utente deve disporre delle autorizzazioni per aggiungere il dispositivo.
- L'app di registrazione di Intune non è supportata.
- Gli utenti di Azure AD B2B non sono supportati.

Se il dispositivo dell'utente non è stato aggiunto, riceveranno un messaggio che indica che devono aggiungere il dispositivo. L'esperienza sarà dipendente dalla piattaforma e dal software.

### <a name="join-a-windows-10-device"></a>Aggiungere un dispositivo Windows 10

Se un utente sta usando Windows 10 e Microsoft Edge, riceverà un messaggio simile al seguente per [aggiungere il dispositivo](../user-help/user-help-join-device-on-network.md#to-join-an-already-configured-windows-10-device).

![Windows 10 e Microsoft Edge - Messaggio che indica che il dispositivo deve essere registrato](./media/terms-of-use/per-device-win10-edge.png)

Se si usa Chrome, verrà richiesto di installare l'[estensione account di Windows 10](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji).

### <a name="register-an-ios-device"></a>Registrare un dispositivo iOSRegister an iOS device

Se un utente utilizza un dispositivo iOS, verrà richiesto di installare [l'app Microsoft Authenticator.](https://apps.apple.com/us/app/microsoft-authenticator/id983156458)

### <a name="register-an-android-device"></a>Registrare un dispositivo AndroidRegister an Android device

Se un utente utilizza un dispositivo Android, verrà richiesto di installare [l'app Microsoft Authenticator.](https://play.google.com/store/apps/details?id=com.azure.authenticator)

### <a name="browsers"></a>Browser

Se un utente sta usando un browser che non è supportato, si dovrà usare un browser diverso.

![Messaggio che indica che il dispositivo deve essere registrato, ma il browser non è supportato](./media/terms-of-use/per-device-browser-unsupported.png)

## <a name="delete-terms-of-use"></a>Eliminare le condizioni per l'utilizzo

È possibile eliminare le vecchie condizioni per l'utilizzo utilizzando la procedura seguente.

1. Accedere ad Azure e passare a **Condizioni per l'utilizzo** all'indirizzo [https://aka.ms/catou](https://aka.ms/catou).
1. Selezionare le condizioni per l'utilizzo che si desidera rimuovere.
1. Fare clic su **Elimina le condizioni**.
1. Nel messaggio visualizzato in cui viene chiesto se si vuole continuare fare clic su **Sì**.

   ![Messaggio che richiede la conferma per l'eliminazione delle condizioni per l'utilizzo](./media/terms-of-use/delete-tou.png)

   Non dovresti più vedere le tue condizioni d'uso.

## <a name="deleted-users-and-active-terms-of-use"></a>Utenti eliminati e condizioni per l'utilizzo attive

Per impostazione predefinita, un utente eliminato rimane comunque in Azure AD per 30 giorni, periodo durante il quale un amministratore potrà eseguirne il ripristino se necessario. Dopo 30 giorni l'utente verrà eliminato definitivamente. Inoltre, tramite il portale di Azure Active Directory, un Amministratore globale può in modo esplicito [eliminare definitivamente un utente eliminato di recente](../fundamentals/active-directory-users-restore.md) prima della scadenza di tale periodo. Se un utente è stato eliminato definitivamente, i dati successivi relativi a tale utente verranno rimossi dalle condizioni per l'utilizzo attive. Le informazioni di controllo sugli utenti eliminati restano nel log di controllo.

## <a name="policy-changes"></a>Modifiche dei criteri

I criteri di accesso condizionale hanno effetto immediato. In questo caso l'amministratore inizierà a vedere nuvolette "tristi" o messaggi relativi a problemi di token di Azure AD. Dovrà quindi disconnettersi e accedere nuovamente per soddisfare il nuovo criterio.

> [!IMPORTANT]
> Gli utenti inclusi nell'ambito devono disconnettersi e accedere per soddisfare un nuovo criterio se:
>
> - un criterio di accesso condizionale è abilitato in base a una condizione di utilizzo
> - o viene creata una seconda condizione

## <a name="b2b-guests-preview"></a>Guest B2B (anteprima)

La maggior parte delle organizzazioni dispone di un processo in atto per i propri dipendenti di acconsentire alle condizioni per l'utilizzo dell'organizzazione e alle informative sulla privacy. Ma come è possibile applicare gli stessi consensi per i guest di Azure AD business-to-business (B2B) quando vengono aggiunti tramite SharePoint o i team? Utilizzando l'accesso condizionale e le condizioni per l'utilizzo, è possibile applicare un criterio direttamente agli utenti guest B2B. Durante il flusso di riscatto dell'invito, all'utente vengono presentate le condizioni per l'utilizzo. Questo supporto è attualmente disponibile in versione di anteprima.

Le Condizioni per l'utilizzo verranno visualizzate solo quando l'utente dispone di un account guest in Azure AD. SharePoint Online dispone attualmente di un'esperienza di destinatario di [condivisione esterna ad hoc](/sharepoint/what-s-new-in-sharing-in-targeted-release) per condividere un documento o una cartella che non richiede all'utente di disporre di un account guest. In questo caso, non viene visualizzata una delle condizioni per l'utilizzo.

![Riquadro Utenti e gruppi - Scheda Includi con l'opzione Tutti gli utenti guest selezionata](./media/terms-of-use/b2b-guests.png)

## <a name="support-for-cloud-apps-preview"></a>Supporto per le app cloud (anteprima)

Le Condizioni per l'utilizzo possono essere usate per diverse app cloud, ad esempio Azure Information Protection e Microsoft Intune. Questo supporto è attualmente disponibile in versione di anteprima.

### <a name="azure-information-protection"></a>Azure Information Protection

È possibile configurare criteri di accesso condizionale per l'app Azure Information Protection e richiedere condizioni per l'utilizzo quando un utente accede a un documento protetto. In questo modo verranno attivate le condizioni per l'utilizzo prima che un utente acceda a un documento protetto per la prima volta.

![Riquadro delle app cloud con l'app Microsoft Azure Information Protection selezionata](./media/terms-of-use/cloud-app-info-protection.png)

### <a name="microsoft-intune-enrollment"></a>Registrazione di Microsoft Intune

È possibile configurare criteri di accesso condizionale per l'app di registrazione di Microsoft Intune e richiedere una condizione per l'utilizzo prima della registrazione di un dispositivo in Intune.You can configure a Conditional Access policy for the Microsoft Intune Enrollment app and require a terms of use prior to the enrollment of a device in Intune. Per altre informazioni, vedere il Leggi il [post di blog sulla scelta della soluzione di Condizioni di utilizzo più adatta per l'organizzazione](https://go.microsoft.com/fwlink/?linkid=2010506&clcid=0x409).

![Riquadro delle app cloud con l'app Microsoft Intune selezionata](./media/terms-of-use/cloud-app-intune.png)

> [!NOTE]
> L'app Di registrazione Intune non è supportata per [le condizioni per uso per dispositivo.](#per-device-terms-of-use)

## <a name="frequently-asked-questions"></a>Domande frequenti

**D: Come faccio a vedere quando/se un utente ha accettato un termine per l'uso?**<br />
R: Nel pannello Condizioni per l'utilizzo fare clic sul numero sotto **Accettato**. È anche possibile visualizzare o cercare l'attività accettata nei log di controllo di Azure AD. Per altre informazioni, vedere Visualizzare il report degli utenti che hanno accettato e rifiutato e [Visualizzare i log di controllo di Azure AD](#view-azure-ad-audit-logs).

**D: per quanto tempo sono archiviate le informazioni?**<br />
R: L'utente conta nel report delle condizioni d'uso e chi ha accettato/rifiutato vengono archiviati per tutta la durata delle condizioni per l'utilizzo. I log di controllo di Azure AD vengono archiviati per 30 giorni.

**D: Perché viene visualizzato un numero diverso di consensi nel report delle condizioni di utilizzo rispetto ai log di controllo di Azure AD?**<br />
R: Il report sulle condizioni per l'uso viene archiviato per la durata di tale durata, mentre i log di controllo di Azure AD vengono archiviati per 30 giorni. Inoltre, il rapporto sulle condizioni per l'utilizzo visualizza solo lo stato di consenso corrente degli utenti. Ad esempio, se un utente rifiuta e quindi accetta, il report delle condizioni per l'utilizzo mostrerà solo l'accettazione dell'utente. Se è necessario visualizzare la cronologia, è possibile usare i log di controllo di Azure AD.

**D: Se si modificano i dettagli relativi a una condizioni per l'utilizzo, è necessario che gli utenti accettino di nuovo?**<br />
R: No, se un amministratore modifica i dettagli per una condizioni per l'utilizzo (nome, nome visualizzato, richiedere agli utenti di espandere o aggiungere una lingua), non richiede agli utenti di accettare nuovamente i nuovi termini.

**D: È possibile aggiornare un documento delle condizioni per l'utilizzo esistente?**<br />
R: Al momento non è possibile aggiornare un documento delle condizioni per l'utilizzo esistente. Per modificare un documento delle condizioni per l'utilizzo, è necessario creare una nuova istanza delle condizioni per l'utilizzo.

**D: Se i collegamenti ipertestuali sono nelle condizioni per l'utilizzo del documento PDF, gli utenti finali saranno in grado di fare clic su di essi?**<br />
R: Sì, gli utenti finali sono in grado di selezionare collegamenti ipertestuali a pagine aggiuntive, ma i collegamenti alle sezioni all'interno del documento non sono supportati.

**D: Le condizioni per l'utilizzo possono supportare più lingue?**<br />
A: Sì. Attualmente ci sono 108 lingue diverse che un amministratore può configurare per una singola condizioni per l'utilizzo. Un amministratore può caricare più documenti PDF e contrassegnare i documenti con una lingua corrispondente (fino a 108). Quando gli utenti finali accedono, vengono esaminate le preferenze della lingua del browser e viene visualizzato il documento corrispondente. Se non viene trovata alcuna corrispondenza, verrà visualizzato il documento predefinito, ovvero il primo documento che viene caricato.

**D: Quando vengono attivate le condizioni d'uso?**<br />
R: le condizioni per l'utilizzo vengono attivate durante l'accesso.

**D: a quali altre applicazioni è possibile applicare le condizioni per l'utilizzo?**<br />
R: È possibile creare un criterio di accesso condizionale nelle applicazioni aziendali utilizzando l'autenticazione moderna. Per altre informazioni, vedere le [applicazioni aziendali](./../manage-apps/view-applications-portal.md).

**D: È possibile aggiungere più condizioni per l'utilizzo a un determinato utente o app?**<br />
R: Sì, creando più criteri di accesso condizionale destinati a tali gruppi o applicazioni. Se un utente rientra nell'ambito di più condizioni per l'utilizzo, accetta una condizioni per l'utilizzo alla volta.

**D: Cosa succede se un utente rifiuta le condizioni per l'utilizzo?**<br />
R: ne viene bloccato l'accesso all'applicazione. L'utente deve accedere nuovamente e accettare le condizioni.

**D: È possibile non accettare un termine d'uso accettato in precedenza?**<br />
R: È possibile [rivedere le condizioni per l'utilizzo accettate in precedenza,](#how-users-can-review-their-terms-of-use)ma attualmente non esiste un modo per annullare l'accettazione.

**D: Cosa succede se si usano anche termini e condizioni di Intune?**<br />
R: Se sono stati configurati sia le condizioni per l'utilizzo di Azure AD che i [termini e condizioni](/intune/terms-and-conditions-create)di Intune, all'utente verrà richiesto di accettarle entrambe. Per altre informazioni, vedere il [post di blog sulla scelta della soluzione di Condizioni di utilizzo più adatta per l'organizzazione](https://go.microsoft.com/fwlink/?linkid=2010506&clcid=0x409).

**D: Quali endpoint vengono utilizzate dalle condizioni per l'utilizzo del servizio per l'autenticazione?**<br />
R: Le condizioni per l'utilizzo https://tokenprovider.termsofuse.identitygovernance.azure.com utilizzano i seguenti endpoint per l'autenticazione: e https://account.activedirectory.windowsazure.com. Se l'organizzazione dispone di un elenco Consenti di URL per la registrazione, sarà necessario aggiungere questi endpoint all'elenco Consenti, insieme agli endpoint di Azure AD per l'accesso.

## <a name="next-steps"></a>Passaggi successivi

- [Guida introduttiva: Richiedere l'accettazione di condizioni per l'utilizzo prima dell'accesso alle app cloud](require-tou.md)
- [Procedure consigliate per l'accesso condizionale in Azure Active DirectoryBest practices for Conditional Access in Azure Active Directory](best-practices.md)
