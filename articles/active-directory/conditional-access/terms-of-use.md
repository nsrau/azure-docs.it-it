---
title: Le condizioni d'uso - Azure Active Directory | Microsoft Docs
description: Introduzione all'uso di condizioni di Azure Active Directory per l'utilizzo per presentare le informazioni per i dipendenti o Guest prima di ottenere l'accesso.
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
ms.openlocfilehash: 0534613a9df3177290e9b4b57e9830fe62f9741a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67112121"
---
# <a name="azure-active-directory-terms-of-use"></a>Condizioni d'uso per Azure Active Directory

Condizioni d'uso di Azure AD fornisce un metodo semplice che le organizzazioni possono usare per presentare le informazioni per gli utenti finali. In questo modo si garantisce che gli utenti vedano le dichiarazioni rilevanti di non responsabilità che si riferiscono ai requisiti legali o di conformità. Questo articolo descrive come iniziare a usare le condizioni d'uso.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="overview-videos"></a>Panoramica video

Il video seguente offre una rapida panoramica delle condizioni di utilizzo.

>[!VIDEO https://www.youtube.com/embed/tj-LK0abNao]

Per altri video, vedere:
- [Come distribuire le condizioni d'uso in Azure Active Directory](https://www.youtube.com/embed/N4vgqHO2tgY)
- [Come implementare le condizioni d'uso in Azure Active Directory](https://www.youtube.com/embed/t_hA4y9luCY)

## <a name="what-can-i-do-with-terms-of-use"></a>Che cosa può fare con le condizioni d'uso?

Condizioni d'uso di Azure AD offre le funzionalità seguenti:

- Richiedere ai dipendenti o gli utenti guest accettare le condizioni d'uso prima di ottenere l'accesso.
- Richiedere ai dipendenti o gli utenti guest accettare le condizioni d'uso in tutti i dispositivi prima di ottenere l'accesso.
- Richiedere ai dipendenti o gli utenti guest accettare le condizioni d'uso in una pianificazione ricorrente.
- Richiedere ai dipendenti o gli utenti guest accettare le condizioni d'uso prima di registrare le informazioni di sicurezza in Azure multi-Factor Authentication (MFA).
- Richiedere ai dipendenti di accettare le condizioni d'uso prima di registrare le informazioni di sicurezza in Azure AD self-service della password (SSPR).
- Presentano condizioni generali di utilizzo per tutti gli utenti nell'organizzazione.
- Presentare specifiche condizioni d'uso basato su attributi di un utente (ad esempio medici o infermieri oppure dipendenti nazionali o internazionali, tramite [gruppi dinamici](../users-groups-roles/groups-dynamic-membership.md)).
- Presenta specifiche condizioni d'uso quando si accede alle applicazioni di impatto aziendale elevato, come Salesforce.
- Presente condizioni d'uso in lingue diverse.
- Elenco che è o non ha accettato le condizioni d'uso.
- Contribuire a rispettare le normative sulla privacy.
- Visualizzare un elenco delle condizioni dell'impegno di utilizzo per la conformità e controllo.
- Creare e gestire condizioni per l'uso con [le API Microsoft Graph](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/agreement) (attualmente in anteprima).

## <a name="prerequisites"></a>Prerequisiti

Per usare e configurare le condizioni di Azure AD di usare, è necessario disporre di:

- Una sottoscrizione di Azure AD Premium P1, P2, EMS E3 o EMS E5.
   - Se non si dispone di una di queste sottoscrizioni, è possibile [ottenere Azure AD Premium](../fundamentals/active-directory-get-started-premium.md) oppure [abilitare la versione di valutazione di Azure AD Premium](https://azure.microsoft.com/trial/get-started-active-directory/).
- Uno dei seguenti account di amministratore per la directory da configurare:
   - Amministratore globale
   - Amministratore della sicurezza
   - Amministratore di accesso condizionale

## <a name="terms-of-use-document"></a>Documento sulle condizioni per l'utilizzo

Condizioni d'uso di Azure AD Usa il formato PDF per presentare il contenuto. Il contenuto del file PDF può essere di qualsiasi tipo, ad esempio documenti di contratti esistenti. Questo consente di acquisire il consenso degli utenti finali durante l'accesso. Per supportare gli utenti sui dispositivi mobili, le dimensioni del carattere consigliato nel file PDF sono 24 punti.

## <a name="add-terms-of-use"></a>Aggiungere le condizioni per l'utilizzo

Dopo avere completato le condizioni per usare documenti, utilizzare la procedura seguente per aggiungerlo.

1. Accedere ad Azure come amministratore globale, amministratore della sicurezza o amministratore di accesso condizionale.
1. Passare a **Condizioni per l'utilizzo** all'indirizzo [https://aka.ms/catou](https://aka.ms/catou).

   ![Pannello di Condizioni per l'utilizzo](./media/terms-of-use/tou-blade.png)

1. Fare clic su **Nuove condizioni**.

   ![Aggiungere le condizioni per l'utilizzo](./media/terms-of-use/new-tou.png)

1. Nel **nome** immettere un nome per le condizioni d'uso che verrà usato nel portale di Azure.
1. Nella casella **Nome visualizzato** immettere un titolo visualizzato dagli utenti quando eseguono l'accesso.
1. Per la **condizioni per usare documenti**passare finalizzate alle condizioni per l'uso PDF e selezionarlo.
1. Selezionare la lingua per le condizioni per usare documenti. L'opzione relativa alla lingua consente di caricare più versioni delle condizioni d'uso, ognuna in una lingua diversa. La versione visualizzata all'utente finale dipenderà dalle preferenze del browser.
1. Per richiedere agli utenti finali di visualizzare le condizioni d'uso prima di accettarle, impostare **richiedono che gli utenti espandano le condizioni d'uso** al **su**.
1. Per richiedere agli utenti finali di accettare le condizioni d'uso in ogni dispositivo si accede, impostare **richiedere agli utenti di fornire il consenso in tutti i dispositivi** al **su**. Per altre informazioni, vedere [condizioni per ogni dispositivo per l'utilizzo](#per-device-terms-of-use).
1. Se si desidera impostare come scaduti condizioni d'uso consensi in una pianificazione, impostare **scadono consensi** al **su**. Se si imposta su On, vengono visualizzate due impostazioni aggiuntive di pianificazione.

   ![Scadenza consensi](./media/terms-of-use/expire-consents.png)

1. Usare la **Expire a partire** e **frequenza** impostazioni per specificare la pianificazione per le condizioni per usano le scadenze. La tabella seguente illustra il risultato di un paio di impostazioni di esempio:

   | Scadenza a partire da | Frequenza | Risultato |
   | --- | --- | --- |
   | Data odierna  | Mensile | A partire da oggi, gli utenti devono accettare le condizioni d'uso e quindi riaccettino ogni mese. |
   | Data nel futuro  | Mensile | A partire da oggi, gli utenti devono accettare le condizioni d'uso. Quando si verifica la data futura, scadono i consensi e quindi gli utenti devono riaccettare ogni mese.  |

   Ad esempio, se si imposta la scadenza a partire dalla data **1 gen** e la frequenza su **Mensile**, ecco come le scadenze potrebbero verificarsi per i due utenti:

   | Utente | Prima data di accettazione | Prima data di scadenza | Seconda data di scadenza | Terza data di scadenza |
   | --- | --- | --- | --- | --- |
   | Alice | 1 gen | 1 feb | 1 mar | 1 apr |
   | Bob | 15 gen | 1 feb | 1 mar | 1 apr |

1. Usare la **durata prima di (giorni) è necessaria l'accettazione di re** impostazione per specificare il numero di giorni prima che l'utente deve accettare le condizioni d'uso. Ciò consente agli utenti di seguire una pianificazione personalizzata. Ad esempio, se si imposta la durata su **30** giorni, ecco come le scadenze potrebbero verificarsi per due utenti:

   | Utente | Prima data di accettazione | Prima data di scadenza | Seconda data di scadenza | Terza data di scadenza |
   | --- | --- | --- | --- | --- |
   | Alice | 1 gen | 31 gen | 2 mar | 1 apr |
   | Bob | 15 gen | 14 feb | 16 mar | 15 apr |

   È possibile usare le impostazioni **Scadenza consensi** e **Durata prima di una nuova accettazione richiede (giorni)** insieme, ma in genere si usa o l'una o l'altra.

1. Sotto **accesso condizionale**, utilizzare il **Imponi con modello di criteri di accesso condizionale** elenco per selezionare il modello per applicare le condizioni d'uso.

   ![Modelli di accesso condizionali](./media/terms-of-use/conditional-access-templates.png)

   | Modello | Descrizione |
   | --- | --- |
   | **Accesso alle app cloud per tutti i guest** | Verrà creato un criterio di accesso condizionale per tutti i guest e tutte le app cloud. Questi criteri influiscono sul portale di Azure. Dopo la creazione, potrebbe essere necessario disconnettersi e accedere. |
   | **Accesso alle app cloud per tutti gli utenti** | Verrà creato un criterio di accesso condizionale per tutti gli utenti e tutte le app cloud. Questi criteri influiscono sul portale di Azure. Dopo la creazione, sarà necessario disconnettersi e accedere. |
   | **Criteri personalizzati** | Selezionare gli utenti, gruppi e le app che verranno applicate a questo condizioni d'uso. |
   | **Creare criteri di accesso condizionale in un secondo momento** | Queste condizioni per l'utilizzo verranno visualizzato nell'elenco di controllo concessione durante la creazione di un criterio di accesso condizionale. |

   >[!IMPORTANT]
   >Controlli condizionali dei criteri di accesso (incluse le condizioni d'uso) non supporta l'imposizione sugli account di servizio. È consigliabile escludere tutti gli account del servizio dai criteri di accesso condizionale.

    I criteri di accesso condizionale personalizzati abilitano granulare condizioni d'uso, fino a un'applicazione cloud specifico o un gruppo di utenti. Per altre informazioni, vedere [Avvio rapido: Richiedere l'accettazione delle condizioni per l'utilizzo prima dell'accesso alle app cloud](require-tou.md).

1. Fare clic su **Create**(Crea).

   Se si seleziona un modello personalizzato di accesso condizionale, quindi una nuova schermata viene visualizzato che consente di creare i criteri di accesso condizionale personalizzati.

   ![Criteri personalizzati](./media/terms-of-use/custom-policy.png)

   Si noterà ora le nuove condizioni d'uso.

   ![Aggiungere le condizioni per l'utilizzo](./media/terms-of-use/create-tou.png)

## <a name="view-report-of-who-has-accepted-and-declined"></a>Visualizzare il report degli utenti che hanno accettato e rifiutato

Nel pannello delle condizioni per l'utilizzo è visualizzato il numero di utenti che hanno accettato e rifiutato. Questi conteggi e che hanno accettato/rifiutato vengono archiviati per la durata delle condizioni d'uso.

1. Accedere ad Azure e passare a **Condizioni per l'utilizzo** all'indirizzo [ https://aka.ms/catou ](https://aka.ms/catou).

   ![Pannello di Condizioni per l'utilizzo](./media/terms-of-use/view-tou.png)

1. Per le condizioni d'uso, fare clic su numeri sotto **Accepted** oppure **rifiutato** per visualizzare lo stato corrente per gli utenti.

   ![Accettazione delle condizioni per l'utilizzo](./media/terms-of-use/accepted-tou.png)

1. Per visualizzare la cronologia per un singolo utente, fare clic sui puntini di sospensione ( **...** ) e quindi **Visualizza cronologia**.

   ![Visualizza il menu cronologia](./media/terms-of-use/view-history-menu.png)

   Nel riquadro di visualizzazione della cronologia, si visualizza una cronologia di tutte le accettazioni, i rifiuti e le scadenze.

   ![Riquadro di visualizzazione della cronologia](./media/terms-of-use/view-history-pane.png)

## <a name="view-azure-ad-audit-logs"></a>Visualizzare i log di controllo di Azure AD

Se si desidera visualizzare le attività aggiuntive, condizioni di Azure AD per l'utilizzo include i log di controllo. Ogni consenso dell'utente genera un evento nei log di controllo che viene conservato per **30 giorni**. È possibile visualizzare questi log nel portale o scaricarli come file CSV.

Per iniziare a usare i log di controllo di Azure AD, seguire questa procedura:

1. Accedere ad Azure e passare a **Condizioni per l'utilizzo** all'indirizzo [ https://aka.ms/catou ](https://aka.ms/catou).
1. Selezionare le condizioni d'uso.
1. Fare clic su **Visualizza log di controllo**.

   ![Pannello di Condizioni per l'utilizzo](./media/terms-of-use/audit-tou.png)

1. Nella schermata dei log di controllo di Azure AD è possibile filtrare le informazioni usando i menu a discesa per visualizzare informazioni specifiche dei log di controllo.

   È inoltre possibile fare clic su **Download** per scaricare le informazioni in un file con estensione CSV per l'uso in locale.

   ![Log di controllo](./media/terms-of-use/audit-logs-tou.png)

   Se si fa clic su un log, viene visualizzato un riquadro con i dettagli delle attività aggiuntive.

   ![Dettagli dell'attività](./media/terms-of-use/audit-log-activity-details.png)

## <a name="what-terms-of-use-looks-like-for-users"></a>Le condizioni d'uso è simile per gli utenti

Dopo aver creato e applicate condizioni d'uso, gli utenti, che sono nell'ambito, verranno visualizzata la schermata seguente durante l'accesso.

![Accesso web dell'utente](./media/terms-of-use/user-tou.png)

Gli utenti possono visualizzare le condizioni d'uso e, se necessario, usare i pulsanti per eseguire lo zoom avanti e indietro.

![Visualizza le condizioni d'uso con i pulsanti dello zoom](./media/terms-of-use/zoom-buttons.png)

La schermata seguente mostra l'aspetto delle condizioni d'uso nei dispositivi mobili.

![Accesso da dispositivo mobile dell'utente](./media/terms-of-use/mobile-tou.png)

Gli utenti sono necessarie solo per accettare le condizioni d'uso una sola volta e non potrà vedere le condizioni d'uso nuovamente su accessi successivi.

### <a name="how-users-can-review-their-terms-of-use"></a>Modo in cui gli utenti possono esaminare le condizioni d'uso

Gli utenti possono esaminare e vedere le condizioni d'uso che invece hanno accettato usando la procedura seguente.

1. Accedere a [https://myapps.microsoft.com](https://myapps.microsoft.com).
1. Nell'angolo superiore destro fare clic sul proprio nome e selezionare **Profilo**.

   ![Profilo](./media/terms-of-use/tou14.png)

1. Nella pagina Profilo fare clic su **Verificare le condizioni d'uso**.

   ![Profilo - Verificare le condizioni d'uso](./media/terms-of-use/tou13a.png)

1. Sarà quindi possibile verificare le condizioni per l'utilizzo accettate.

## <a name="edit-terms-of-use-details"></a>Modifica le condizioni d'uso dettagli

È possibile modificare alcuni dettagli delle condizioni di utilizzo, ma non è possibile modificare un documento esistente. La procedura seguente descrive come modificare i dettagli.

1. Accedere ad Azure e passare a **Condizioni per l'utilizzo** all'indirizzo [ https://aka.ms/catou ](https://aka.ms/catou).
1. Selezionare le condizioni d'uso che si desidera modificare.
1. Fare clic su **Modifica le condizioni**.
1. Nel riquadro della modifica delle condizioni per l'utilizzo, modificare il nome, il nome visualizzato o richiedere che gli utenti espandano i valori.

   Se sono presenti altre impostazioni che si desidera modificare, ad esempio documenti PDF, richiedere agli utenti di fornire il consenso in tutti i dispositivi, di scadenza consensi, durata prima reacceptance o criteri di accesso condizionale, è necessario creare nuove condizioni d'uso.

   ![Modificare le condizioni per l'utilizzo](./media/terms-of-use/edit-tou.png)

1. Fare clic su **Salva** per salvare le modifiche.

   Dopo aver salvato le modifiche, gli utenti non dovranno accettare nuovamente le condizioni nuove.

## <a name="add-a-terms-of-use-language"></a>Aggiungere le condizioni per l'uso language

La procedura seguente viene descritto come aggiungere le condizioni per l'uso language.

1. Accedere ad Azure e passare a **Condizioni per l'utilizzo** all'indirizzo [ https://aka.ms/catou ](https://aka.ms/catou).
1. Selezionare le condizioni d'uso che si desidera modificare.
1. Nel riquadro dei dettagli fare clic sulla scheda **Lingue**.

   ![Aggiungere le condizioni per l'utilizzo](./media/terms-of-use/languages-tou.png)

1. Fare clic su **Aggiungi lingua**.
1. Nel riquadro Aggiungi la lingua delle condizioni per l'utilizzo caricare il PDF localizzato e selezionare la lingua.

   ![Aggiungere le condizioni per l'utilizzo](./media/terms-of-use/language-add-tou.png)

1. Fare clic su **Aggiungi** per aggiungere la lingua.

## <a name="per-device-terms-of-use"></a>Condizioni per ogni dispositivo per l'utilizzo

Il **richiedere agli utenti di fornire il consenso in ogni dispositivo** impostazione consente all'utente di richiedere agli utenti finali di accettare le condizioni d'uso in tutti i dispositivi accedono da. All'utente finale verrà richiesto di aggiungere il dispositivo in Azure AD. Quando il dispositivo è stato aggiunto, l'ID del dispositivo consente di applicare le condizioni d'uso in ogni dispositivo.

Ecco un elenco dei software e delle piattaforme supportate.

> [!div class="mx-tableFixed"]
> |  | iOS | Android | Windows 10 | Altri |
> | --- | --- | --- | --- | --- |
> | **App nativa** | Yes | Sì | Yes |  |
> | **Microsoft Edge** | Yes | Sì | Yes |  |
> | **Internet Explorer** | Yes | Sì | Yes |  |
> | **Chrome (con estensione)** | Yes | Sì | Yes |  |

Note legali per ogni dispositivo presenta i vincoli seguenti:

- Un dispositivo può essere aggiunto a un solo tenant.
- Un utente deve disporre delle autorizzazioni per aggiungere il dispositivo.
- L'app di registrazione di Intune non è supportata.
- Gli utenti di Azure AD B2B non sono supportati.

Se il dispositivo dell'utente non è stato aggiunto, riceveranno un messaggio che indica che devono aggiungere il dispositivo. L'esperienza sarà dipendente dalla piattaforma e dal software.

### <a name="join-a-windows-10-device"></a>Aggiungere un dispositivo Windows 10

Se un utente sta usando Windows 10 e Microsoft Edge, riceverà un messaggio simile al seguente per [aggiungere il dispositivo](../user-help/user-help-join-device-on-network.md#to-join-an-already-configured-windows-10-device).

![Windows 10 e Microsoft Edge - prompt di aggiunta del dispositivo](./media/terms-of-use/per-device-win10-edge.png)

Se si usa Chrome, verrà richiesto di installare l'[estensione account di Windows 10](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji).

### <a name="browsers"></a>Browser

Se un utente sta usando un browser che non è supportato, si dovrà usare un browser diverso.

![Browser non supportato](./media/terms-of-use/per-device-browser-unsupported.png)

## <a name="delete-terms-of-use"></a>Eliminare le condizioni per l'utilizzo

È possibile eliminare le precedenti condizioni d'uso mediante la procedura seguente.

1. Accedere ad Azure e passare a **Condizioni per l'utilizzo** all'indirizzo [ https://aka.ms/catou ](https://aka.ms/catou).
1. Selezionare le condizioni per l'utilizzo che si desidera rimuovere.
1. Fare clic su **Elimina le condizioni**.
1. Nel messaggio visualizzato in cui viene chiesto se si vuole continuare fare clic su **Sì**.

   ![Eliminare le condizioni per l'utilizzo](./media/terms-of-use/delete-tou.png)

   Non si dovrebbe le condizioni d'uso.

## <a name="deleted-users-and-active-terms-of-use"></a>Gli utenti eliminati e active condizioni d'uso

Per impostazione predefinita, un utente eliminato rimane comunque in Azure AD per 30 giorni, periodo durante il quale un amministratore potrà eseguirne il ripristino se necessario. Dopo 30 giorni l'utente verrà eliminato definitivamente. Inoltre, tramite il portale di Azure Active Directory, un Amministratore globale può in modo esplicito [eliminare definitivamente un utente eliminato di recente](../fundamentals/active-directory-users-restore.md) prima della scadenza di tale periodo. Uno a un utente è stato eliminato definitivamente, i dati successivi su tale utente verranno rimosso dalle condizioni d'uso di active. Le informazioni di controllo sugli utenti eliminati restano nel log di controllo.

## <a name="policy-changes"></a>Modifiche dei criteri

Criteri di accesso condizionale vengono applicate immediatamente. In questo caso l'amministratore inizierà a vedere nuvolette "tristi" o messaggi relativi a problemi di token di Azure AD. Dovrà quindi disconnettersi e accedere nuovamente per soddisfare il nuovo criterio.

> [!IMPORTANT]
> Gli utenti inclusi nell'ambito devono disconnettersi e accedere per soddisfare un nuovo criterio se:
>
> - criteri di accesso condizionale sono abilitato in termini di utilizzo
> - o viene creata una seconda condizione

## <a name="b2b-guests-preview"></a>Guest B2B (anteprima)

La maggior parte delle organizzazioni prevede un processo per i propri dipendenti a fornire il consenso alle condizioni della propria organizzazione per l'uso e informative sulla privacy. Ma come è possibile applicare gli stessi consensi per i guest di Azure AD business-to-business (B2B) quando vengono aggiunti tramite SharePoint o i team? Tramite l'accesso condizionale e le condizioni d'uso, è possibile applicare un criterio direttamente nei confronti degli utenti guest B2B. Durante il flusso di riscatto dell'invito, l'utente viene presentato con le condizioni d'uso. Questo supporto è attualmente disponibile in versione di anteprima.

Le Condizioni per l'utilizzo verranno visualizzate solo quando l'utente dispone di un account guest in Azure AD. SharePoint Online è attualmente un' [esperienza di destinatario condivisione esterna ad hoc](/sharepoint/what-s-new-in-sharing-in-targeted-release) per condividere un documento o una cartella che non richiede all'utente di disporre di un account guest. In questo caso, le condizioni per l'uso non viene visualizzato.

![Tutti gli utenti guest](./media/terms-of-use/b2b-guests.png)

## <a name="support-for-cloud-apps-preview"></a>Supporto per le app cloud (anteprima)

Le Condizioni per l'utilizzo possono essere usate per diverse app cloud, ad esempio Azure Information Protection e Microsoft Intune. Questo supporto è attualmente disponibile in versione di anteprima.

### <a name="azure-information-protection"></a>Azure Information Protection

È possibile configurare un criterio di accesso condizionale per l'app Azure Information Protection e richiedere le condizioni per l'uso quando un utente accede a un documento protetto. Questo attiverà le condizioni per l'utilizzo prima di un utente che accede a un documento protetto per la prima volta.

![App cloud di Azure Information Protection](./media/terms-of-use/cloud-app-info-protection.png)

### <a name="microsoft-intune-enrollment"></a>Registrazione di Microsoft Intune

È possibile configurare un criterio di accesso condizionale per l'app di registrazione di Microsoft Intune e richiedere le condizioni d'uso prima della registrazione di un dispositivo in Intune. Per altre informazioni, vedere il Leggi il [post di blog sulla scelta della soluzione di Condizioni di utilizzo più adatta per l'organizzazione](https://go.microsoft.com/fwlink/?linkid=2010506&clcid=0x409).

![App cloud di Microsoft Intune](./media/terms-of-use/cloud-app-intune.png)

> [!NOTE]
> L'app di registrazione di Intune non è supportata per [condizioni per ogni dispositivo per l'utilizzo](#per-device-terms-of-use).

## <a name="frequently-asked-questions"></a>Domande frequenti

**D: Come visualizzare se un utente ha accettato le condizioni d'uso?**<br />
R: Nel pannello Condizioni per l'utilizzo fare clic sul numero sotto **Accettato**. È anche possibile visualizzare o cercare l'attività accettata nei log di controllo di Azure AD. Per altre informazioni, vedere Visualizzare il report degli utenti che hanno accettato e rifiutato e [Visualizzare i log di controllo di Azure AD](#view-azure-ad-audit-logs).

**D: Per quanto tempo sono archiviate le informazioni?**<br />
R: L'utente conta nei termini di report di utilizzo e che hanno accettato/rifiutato vengono archiviato per il ciclo di vita delle condizioni d'uso. I log di controllo di Azure AD vengono archiviati per 30 giorni.

**D: Perché viene visualizzato un numero diverso di consensi in termini di report di utilizzo e di Azure AD i log di controllo?**<br />
R: Le condizioni per i report di utilizzo vengono archiviati per la durata di tale condizioni d'uso, mentre il controllo di Azure AD i log vengono archiviati per 30 giorni. Inoltre, le condizioni per i report di utilizzo visualizza solo lo stato di consenso corrente degli utenti. Ad esempio, se un utente rifiuta, quindi accetta, le condizioni d'uso report visualizzerà solo tale utente accettare. Se è necessario visualizzare la cronologia, è possibile usare i log di controllo di Azure AD.

**D: Se modificano i dettagli per le condizioni d'uso, è necessario agli utenti di accettare nuovamente?**<br />
R: No, se un amministratore modifica i dettagli per le condizioni d'uso (nome, nome visualizzato, richiedere agli utenti di espandere o aggiungere un linguaggio), non richiede agli utenti riaccettino i nuovi termini.

**D: È possibile aggiornare un condizioni esistenti per usare documenti?**<br />
R: Attualmente, non è possibile aggiornare un condizioni esistenti per usare documenti. Per modificare le condizioni per usare documenti, è necessario creare le nuove condizioni per l'istanza Usa.

**D: Se i collegamenti ipertestuali sono in termini di usare documenti PDF, gli utenti finali potranno fare clic su essi?**<br />
R: Viene eseguito il rendering predefinito del file PDF in formato JPEG, in modo che i collegamenti ipertestuali non siano selezionabili. Gli utenti hanno la possibilità di selezionare **In caso di problemi di visualizzazione fare clic qui** per eseguire il rendering PDF in modo nativo in cui sono supportati i collegamenti ipertestuali.

**D: Le condizioni d'uso possono supportare più lingue?**<br />
R: Sì. Attualmente sono presenti diverse 108 lingue un amministratore può configurare per una singola le condizioni d'uso. Un amministratore può caricare più documenti PDF e contrassegnare i documenti con una lingua corrispondente (fino a 108). Quando gli utenti finali accedono, vengono esaminate le preferenze della lingua del browser e viene visualizzato il documento corrispondente. Se non viene trovata alcuna corrispondenza, verrà visualizzato il documento predefinito, ovvero il primo documento che viene caricato.

**D: Quando viene le condizioni d'uso attivato?**<br />
R: Le condizioni di utilizzo viene attivata durante l'esperienza di accesso.

**D: Quali altre applicazioni è possibile applicare le condizioni per l'uso da**<br />
R: È possibile creare un criterio di accesso condizionale per le applicazioni aziendali tramite l'autenticazione moderna. Per altre informazioni, vedere le [applicazioni aziendali](./../manage-apps/view-applications-portal.md).

**D: È possibile aggiungere più condizioni d'uso per un determinato utente o app?**<br />
R: Sì, mediante la creazione di più criteri di accesso condizionale destinate a tali gruppi o applicazioni. Se un utente rientra nell'ambito di più condizioni d'uso, accettano uno condizioni d'uso in un momento.

**D: Cosa accade se un utente rifiuta le condizioni d'uso?**<br />
R: Ne viene bloccato l'accesso all'applicazione. L'utente deve accedere nuovamente e accettare le condizioni.

**D: È possibile unaccept le condizioni per l'utilizzo in cui è stato accettato in precedenza?**<br />
R: È possibile [revisione accettato in precedenza le condizioni d'uso](#how-users-can-review-their-terms-of-use), ma attualmente non esiste un modo per unaccept.

**D: Cosa succede se si usano anche termini e condizioni di Intune?**<br />
R: Se sono stati configurati entrambi i termini di usare Azure AD e [Intune termini e condizioni](/intune/terms-and-conditions-create), l'utente verrà richiesto di accettare entrambi. Per altre informazioni, vedere il [post di blog sulla scelta della soluzione di Condizioni di utilizzo più adatta per l'organizzazione](https://go.microsoft.com/fwlink/?linkid=2010506&clcid=0x409).

**D: Gli endpoint Usa i termini di utilizzo del servizio per l'autenticazione?**<br />
R: Condizioni di utilizzo utilizza i seguenti endpoint per l'autenticazione: https://tokenprovider.termsofuse.identitygovernance.azure.com e https://account.activedirectory.windowsazure.com. Se l'organizzazione ha un elenco Consenti di URL per la registrazione, è necessario aggiungere questi endpoint all'elenco Consenti, con gli endpoint di Azure AD per l'accesso.

## <a name="next-steps"></a>Passaggi successivi

- [Avvio rapido: Richiedere l'accettazione delle condizioni per l'utilizzo prima dell'accesso alle app cloud](require-tou.md)
- [Le procedure consigliate per l'accesso condizionale in Azure Active Directory](best-practices.md)
