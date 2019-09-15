---
title: Condizioni per l'utilizzo Azure Active Directory | Microsoft Docs
description: Iniziare a usare Azure Active Directory condizioni per l'utilizzo per presentare informazioni ai dipendenti o ai guest prima di ottenere l'accesso.
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
ms.openlocfilehash: 31d84d5bf43bac55769a6479917794a51c1ccd0c
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/15/2019
ms.locfileid: "70999109"
---
# <a name="azure-active-directory-terms-of-use"></a>Azure Active Directory le condizioni per l'utilizzo

Azure AD condizioni per l'utilizzo fornisce un metodo semplice che le organizzazioni possono utilizzare per presentare le informazioni agli utenti finali. In questo modo si garantisce che gli utenti vedano le dichiarazioni rilevanti di non responsabilità che si riferiscono ai requisiti legali o di conformità. Questo articolo descrive come iniziare a usare le condizioni per l'utilizzo.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="overview-videos"></a>Panoramica video

Il video seguente offre una rapida panoramica delle condizioni per l'utilizzo.

>[!VIDEO https://www.youtube.com/embed/tj-LK0abNao]

Per altri video, vedere:
- [Come distribuire le condizioni per l'utilizzo in Azure Active Directory](https://www.youtube.com/embed/N4vgqHO2tgY)
- [Come implementare le condizioni per l'utilizzo in Azure Active Directory](https://www.youtube.com/embed/t_hA4y9luCY)

## <a name="what-can-i-do-with-terms-of-use"></a>Cosa è possibile fare con le condizioni per l'utilizzo?

Azure AD condizioni per l'utilizzo sono disponibili le seguenti funzionalità:

- Richiedere ai dipendenti o ai guest di accettare le condizioni per l'utilizzo prima di ottenere l'accesso.
- Richiedere ai dipendenti o ai guest di accettare le condizioni per l'utilizzo in ogni dispositivo prima di ottenere l'accesso.
- Richiedere ai dipendenti o ai guest di accettare le condizioni per l'utilizzo in base a una pianificazione ricorrente.
- Richiedere a dipendenti o Guest di accettare le condizioni per l'utilizzo prima di registrare le informazioni di sicurezza in Azure Multi-Factor Authentication (multi-factor authentication).
- Richiedere ai dipendenti di accettare le condizioni per l'utilizzo prima di registrare le informazioni di sicurezza in Azure AD la reimpostazione della password self-service (SSPR).
- Presentare le condizioni per l'utilizzo generali per tutti gli utenti dell'organizzazione.
- Presentare le condizioni per l'utilizzo specifiche in base a attributi utente (ad esempio medici o infermieri oppure dipendenti nazionali o internazionali, tramite [gruppi dinamici](../users-groups-roles/groups-dynamic-membership.md)).
- Presentare condizioni per l'utilizzo specifiche per l'accesso ad applicazioni a elevato utilizzo aziendale, ad esempio Salesforce.
- Presentare le condizioni per l'utilizzo in lingue diverse.
- Elenco che ha o non ha accettato le condizioni per l'utilizzo.
- Contribuire a rispettare le normative sulla privacy.
- Visualizza un log delle condizioni per l'utilizzo dell'attività per conformità e controllo.
- Crea e Gestisci le condizioni per l'utilizzo usando le [api Microsoft Graph](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/agreement) (attualmente in anteprima).

## <a name="prerequisites"></a>Prerequisiti

Per usare e configurare Azure AD condizioni per l'utilizzo, è necessario disporre di:

- Una sottoscrizione di Azure AD Premium P1, P2, EMS E3 o EMS E5.
   - Se non si dispone di una di queste sottoscrizioni, è possibile [ottenere Azure AD Premium](../fundamentals/active-directory-get-started-premium.md) oppure [abilitare la versione di valutazione di Azure AD Premium](https://azure.microsoft.com/trial/get-started-active-directory/).
- Uno dei seguenti account di amministratore per la directory da configurare:
   - Amministratore globale
   - Amministratore della protezione
   - Amministratore accesso condizionale

## <a name="terms-of-use-document"></a>Documento sulle Condizioni per l'utilizzo

Azure AD le condizioni per l'utilizzo usano il formato PDF per presentare il contenuto. Il contenuto del file PDF può essere di qualsiasi tipo, ad esempio documenti di contratti esistenti. Questo consente di acquisire il consenso degli utenti finali durante l'accesso. Per supportare gli utenti sui dispositivi mobili, le dimensioni del carattere consigliato nel file PDF sono 24 punti.

## <a name="add-terms-of-use"></a>Aggiungere le condizioni per l'utilizzo

Dopo aver completato il documento relativo alle condizioni per l'utilizzo, attenersi alla procedura seguente per aggiungerlo.

1. Accedere ad Azure come amministratore globale, amministratore della sicurezza o amministratore di accesso condizionale.
1. Passare a **Condizioni per l'utilizzo** all'indirizzo [https://aka.ms/catou](https://aka.ms/catou).

   ![Accesso condizionale-pannello Condizioni per l'utilizzo](./media/terms-of-use/tou-blade.png)

1. Fare clic su **Nuove condizioni**.

   ![Nuovo termine del riquadro utilizzo per specificare le impostazioni delle condizioni per l'utilizzo](./media/terms-of-use/new-tou.png)

1. Nella casella **nome** immettere un nome per le condizioni per l'utilizzo che verranno utilizzate nel portale di Azure.
1. Nella casella **Nome visualizzato** immettere un titolo visualizzato dagli utenti quando eseguono l'accesso.
1. Per **condizioni per l'utilizzo documento**, passare alla pagina relativa alle condizioni per l'utilizzo in formato PDF finali e selezionarla.
1. Selezionare la lingua per il documento sulle condizioni d'uso. L'opzione relativa alla lingua consente di caricare più versioni delle condizioni d'uso, ognuna in una lingua diversa. La versione visualizzata all'utente finale dipenderà dalle preferenze del browser.
1. Per richiedere agli utenti finali di visualizzare le condizioni per l'utilizzo prima di accettarle, impostare **Richiedi agli utenti di espandere le condizioni per l'utilizzo** **su on**.
1. Per richiedere agli utenti finali di accettare le condizioni per l'utilizzo in ogni dispositivo da cui accedono, impostare **Richiedi agli utenti di fornire il consenso su ogni dispositivo** **.** Per altre informazioni, vedere [condizioni per l'utilizzo per ogni dispositivo](#per-device-terms-of-use).
1. Se si desidera impostare come scaduti le condizioni per l'utilizzo in base a una pianificazione, impostare la **scadenza consentita** su **on**. Se si imposta su On, vengono visualizzate due impostazioni aggiuntive di pianificazione.

   ![Impostazioni di scadenza consentite per impostare la data di inizio, la frequenza e la durata](./media/terms-of-use/expire-consents.png)

1. Utilizzare le impostazioni **scadenza inizio** e **frequenza** per specificare la pianificazione per le condizioni per l'utilizzo delle scadenze. La tabella seguente illustra il risultato di un paio di impostazioni di esempio:

   | Scadenza a partire da | Frequenza | Risultato |
   | --- | --- | --- |
   | Data odierna  | Mensile | A partire da oggi, gli utenti devono accettare le condizioni per l'utilizzo e quindi riaccettarle ogni mese. |
   | Data nel futuro  | Mensile | A partire da oggi, gli utenti devono accettare le condizioni per l'utilizzo. Quando si verifica la data futura, scadono i consensi e quindi gli utenti devono riaccettare ogni mese.  |

   Ad esempio, se si imposta la scadenza a partire dalla data **1 gen** e la frequenza su **Mensile**, ecco come le scadenze potrebbero verificarsi per i due utenti:

   | Utente | Prima data di accettazione | Prima data di scadenza | Seconda data di scadenza | Terza data di scadenza |
   | --- | --- | --- | --- | --- |
   | Alice | 1 gen | 1 feb | 1 mar | 1 apr |
   | Bob | 15 gen | 1 feb | 1 mar | 1 apr |

1. Per specificare il numero di giorni prima che l'utente debba accettare di nuovo le condizioni per l'utilizzo, utilizzare l'impostazione **durata prima di riaccettazione richiede (giorni)** . Ciò consente agli utenti di seguire una pianificazione personalizzata. Ad esempio, se si imposta la durata su **30** giorni, ecco come le scadenze potrebbero verificarsi per due utenti:

   | Utente | Prima data di accettazione | Prima data di scadenza | Seconda data di scadenza | Terza data di scadenza |
   | --- | --- | --- | --- | --- |
   | Alice | 1 gen | 31 gen | 2 mar | 1 apr |
   | Bob | 15 gen | 14 feb | 16 mar | 15 apr |

   È possibile usare le impostazioni **Scadenza consensi** e **Durata prima di una nuova accettazione richiede (giorni)** insieme, ma in genere si usa o l'una o l'altra.

1. In **accesso condizionale**usare l'elenco **applica con i modelli di criteri di accesso condizionale** per selezionare il modello per applicare le condizioni per l'utilizzo.

   ![Elenco a discesa accesso condizionale per selezionare un modello di criteri](./media/terms-of-use/conditional-access-templates.png)

   | Modello | Descrizione |
   | --- | --- |
   | **Accesso alle app cloud per tutti i guest** | Verranno creati criteri di accesso condizionale per tutti i guest e tutte le app cloud. Questi criteri influiscono sul portale di Azure. Dopo la creazione, potrebbe essere necessario disconnettersi e accedere. |
   | **Accesso alle app cloud per tutti gli utenti** | Verranno creati criteri di accesso condizionale per tutti gli utenti e tutte le app cloud. Questi criteri influiscono sul portale di Azure. Dopo la creazione, sarà necessario disconnettersi e accedere. |
   | **Criteri personalizzati** | Selezionare gli utenti, i gruppi e le app a cui verranno applicate le condizioni per l'utilizzo. |
   | **Crea criteri di accesso condizionale in un secondo momento** | Queste condizioni per l'utilizzo verranno visualizzate nell'elenco di controllo di concessione quando si crea un criterio di accesso condizionale. |

   >[!IMPORTANT]
   >I controlli dei criteri di accesso condizionale (incluse le condizioni per l'utilizzo) non supportano l'imposizione degli account del servizio. È consigliabile escludere tutti gli account di servizio dai criteri di accesso condizionale.

    I criteri di accesso condizionale personalizzati consentono condizioni di utilizzo granulari, fino a un'applicazione cloud o a un gruppo di utenti specifici. Per altre informazioni, vedere [Avvio rapido: Richiedere l'accettazione delle condizioni per l'utilizzo prima dell'accesso alle app cloud](require-tou.md).

1. Fare clic su **Create**(Crea).

   Se è stato selezionato un modello di accesso condizionale personalizzato, viene visualizzata una nuova schermata che consente di creare i criteri di accesso condizionale personalizzato.

   ![Nuovo riquadro accesso condizionale se è stato scelto il modello di criteri di accesso condizionale personalizzato](./media/terms-of-use/custom-policy.png)

   Verranno ora visualizzate le nuove condizioni per l'utilizzo.

   ![Nuove condizioni per l'utilizzo elencate nel pannello condizioni per l'utilizzo](./media/terms-of-use/create-tou.png)

## <a name="view-report-of-who-has-accepted-and-declined"></a>Visualizzare il report degli utenti che hanno accettato e rifiutato

Nel pannello delle condizioni per l'utilizzo è visualizzato il numero di utenti che hanno accettato e rifiutato. Questi conteggi e i destinatari accettati/rifiutati vengono archiviati per la durata delle condizioni per l'utilizzo.

1. Accedere ad Azure e passare a **Condizioni per l'utilizzo** all'indirizzo [ https://aka.ms/catou ](https://aka.ms/catou).

   ![Pannello Condizioni per l'utilizzo che elenca il numero di show utente accettati e rifiutati](./media/terms-of-use/view-tou.png)

1. Per le condizioni per l'utilizzo, fare clic sui numeri in **accettato** o **rifiutato** per visualizzare lo stato corrente degli utenti.

   ![Condizioni per l'utilizzo riquadro consentiti in cui sono elencati gli utenti che hanno accettato](./media/terms-of-use/accepted-tou.png)

1. Per visualizzare la cronologia per un singolo utente, fare clic sui puntini di sospensione ( **...** ) e quindi **Visualizza cronologia**.

   ![Menu di scelta rapida Visualizza cronologia per un utente](./media/terms-of-use/view-history-menu.png)

   Nel riquadro di visualizzazione della cronologia, si visualizza una cronologia di tutte le accettazioni, i rifiuti e le scadenze.

   ![Visualizza riquadro cronologia elenca la cronologia accetta, declina e scade per un utente](./media/terms-of-use/view-history-pane.png)

## <a name="view-azure-ad-audit-logs"></a>Visualizzare i log di controllo di Azure AD

Se si desidera visualizzare ulteriori attività, Azure AD le condizioni per l'utilizzo includono i log di controllo. Ogni consenso dell'utente genera un evento nei log di controllo che viene conservato per **30 giorni**. È possibile visualizzare questi log nel portale o scaricarli come file CSV.

Per iniziare a usare i log di controllo di Azure AD, seguire questa procedura:

1. Accedere ad Azure e passare a **Condizioni per l'utilizzo** all'indirizzo [ https://aka.ms/catou ](https://aka.ms/catou).
1. Selezionare le condizioni per l'utilizzo.
1. Fare clic su **Visualizza log di controllo**.

   ![Pannello Condizioni per l'utilizzo con l'opzione Visualizza log di controllo evidenziata](./media/terms-of-use/audit-tou.png)

1. Nella schermata dei log di controllo di Azure AD è possibile filtrare le informazioni usando i menu a discesa per visualizzare informazioni specifiche dei log di controllo.

   È inoltre possibile fare clic su **Download** per scaricare le informazioni in un file con estensione CSV per l'uso in locale.

   ![Schermata dei log di controllo Azure AD elenco di date, criteri di destinazione, avviati da e attività](./media/terms-of-use/audit-logs-tou.png)

   Se si fa clic su un log, viene visualizzato un riquadro con i dettagli delle attività aggiuntive.

   ![Dettagli attività per un log che mostra l'attività, lo stato dell'attività, avviato da, i criteri di destinazione](./media/terms-of-use/audit-log-activity-details.png)

## <a name="what-terms-of-use-looks-like-for-users"></a>Quali sono le condizioni per l'utilizzo per gli utenti

Dopo aver creato e applicato le condizioni per l'utilizzo, gli utenti, che rientrano nell'ambito, visualizzeranno la schermata seguente durante l'accesso.

![Condizioni d'uso di esempio visualizzate quando un utente accede](./media/terms-of-use/user-tou.png)

Gli utenti possono visualizzare le condizioni d'uso e, se necessario, usare i pulsanti per eseguire lo zoom avanti e indietro.

![Visualizzazione delle condizioni per l'utilizzo con i pulsanti zoom](./media/terms-of-use/zoom-buttons.png)

Nella schermata seguente viene illustrato il modo in cui le condizioni per l'utilizzo vengono esaminate sui dispositivi mobili.

![Condizioni d'uso di esempio visualizzate quando un utente accede a un dispositivo mobile](./media/terms-of-use/mobile-tou.png)

È necessario che gli utenti accettino le condizioni per l'utilizzo una sola volta e non visualizzeranno più le condizioni per l'utilizzo negli accessi successivi.

### <a name="how-users-can-review-their-terms-of-use"></a>Come gli utenti possono esaminare le condizioni per l'utilizzo

Gli utenti possono esaminare e visualizzare le condizioni per l'utilizzo accettate mediante la procedura riportata di seguito.

1. Accedere a [https://myapps.microsoft.com](https://myapps.microsoft.com).
1. Nell'angolo superiore destro fare clic sul proprio nome e selezionare **Profilo**.

   ![Sito app con il riquadro dell'utente aperto](./media/terms-of-use/tou14.png)

1. Nella pagina Profilo fare clic su **Verificare le condizioni d'uso**.

   ![Pagina del profilo per un utente che Visualizza il collegamento controlla le condizioni per l'utilizzo](./media/terms-of-use/tou13a.png)

1. Sarà quindi possibile verificare le condizioni per l'utilizzo accettate.

## <a name="edit-terms-of-use-details"></a>Modifica i dettagli delle Condizioni per l'utilizzo

È possibile modificare alcuni dettagli delle condizioni per l'utilizzo, ma non è possibile modificare un documento esistente. La procedura seguente descrive come modificare i dettagli.

1. Accedere ad Azure e passare a **Condizioni per l'utilizzo** all'indirizzo [ https://aka.ms/catou ](https://aka.ms/catou).
1. Selezionare le condizioni per l'utilizzo che si desidera modificare.
1. Fare clic su **Modifica le condizioni**.
1. Nel riquadro della modifica delle condizioni per l'utilizzo, modificare il nome, il nome visualizzato o richiedere che gli utenti espandano i valori.

   Se sono presenti altre impostazioni che si desidera modificare, ad esempio il documento PDF, richiedere agli utenti di fornire il consenso su ogni dispositivo, scadenza dei consensi, durata prima della riaccettazione o criteri di accesso condizionale, è necessario creare nuove condizioni per l'utilizzo.

   ![Modificare il riquadro Condizioni per l'utilizzo mostrando le opzioni nome ed Espandi](./media/terms-of-use/edit-tou.png)

1. Fare clic su **Salva** per salvare le modifiche.

   Dopo aver salvato le modifiche, gli utenti non dovranno accettare nuovamente le condizioni nuove.

## <a name="add-a-terms-of-use-language"></a>Aggiungere una lingua per le condizioni per l'utilizzo

Nella procedura riportata di seguito viene descritto come aggiungere un linguaggio di condizioni per l'utilizzo.

1. Accedere ad Azure e passare a **Condizioni per l'utilizzo** all'indirizzo [ https://aka.ms/catou ](https://aka.ms/catou).
1. Selezionare le condizioni per l'utilizzo che si desidera modificare.
1. Nel riquadro dei dettagli fare clic sulla scheda **Lingue**.

   ![Condizioni per l'utilizzo selezionata e visualizzata la scheda lingue nel riquadro dei dettagli](./media/terms-of-use/languages-tou.png)

1. Fare clic su **Aggiungi lingua**.
1. Nel riquadro Aggiungi la lingua delle condizioni per l'utilizzo caricare il PDF localizzato e selezionare la lingua.

   ![Aggiungere il riquadro dei termini del linguaggio di utilizzo con le opzioni per caricare i file PDF localizzati](./media/terms-of-use/language-add-tou.png)

1. Fare clic su **Aggiungi** per aggiungere la lingua.

## <a name="per-device-terms-of-use"></a>Condizioni per l'utilizzo per dispositivo

L'impostazione **Richiedi agli utenti di acconsentire su ogni dispositivo** consente di richiedere agli utenti finali di accettare le condizioni per l'utilizzo in ogni dispositivo da cui accedono. All'utente finale verrà richiesto di aggiungere il dispositivo in Azure AD. Quando il dispositivo viene aggiunto, viene usato l'ID del dispositivo per applicare le condizioni per l'utilizzo in ogni dispositivo.

Ecco un elenco dei software e delle piattaforme supportate.

> [!div class="mx-tableFixed"]
> |  | iOS | Android | Windows 10 | Altro |
> | --- | --- | --- | --- | --- |
> | **App nativa** | Yes | Sì | Sì |  |
> | **Microsoft Edge** | Sì | Sì | Sì |  |
> | **Internet Explorer** | Sì | Sì | Sì |  |
> | **Chrome (con estensione)** | Sì | Sì | Sì |  |

Le condizioni per l'utilizzo per dispositivo hanno i vincoli seguenti:

- Un dispositivo può essere aggiunto a un solo tenant.
- Un utente deve disporre delle autorizzazioni per aggiungere il dispositivo.
- L'app di registrazione di Intune non è supportata.
- Azure AD utenti B2B non sono supportati.

Se il dispositivo dell'utente non è stato aggiunto, riceveranno un messaggio che indica che devono aggiungere il dispositivo. L'esperienza sarà dipendente dalla piattaforma e dal software.

### <a name="join-a-windows-10-device"></a>Aggiungere un dispositivo Windows 10

Se un utente sta usando Windows 10 e Microsoft Edge, riceverà un messaggio simile al seguente per [aggiungere il dispositivo](../user-help/user-help-join-device-on-network.md#to-join-an-already-configured-windows-10-device).

![Windows 10 e Microsoft Edge-messaggio che indica che il dispositivo deve essere registrato](./media/terms-of-use/per-device-win10-edge.png)

Se si usa Chrome, verrà richiesto di installare l'[estensione account di Windows 10](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji).

### <a name="browsers"></a>Browser

Se un utente sta usando un browser che non è supportato, si dovrà usare un browser diverso.

![Messaggio che indica che il dispositivo deve essere registrato, ma il browser non è supportato](./media/terms-of-use/per-device-browser-unsupported.png)

## <a name="delete-terms-of-use"></a>Eliminare le condizioni per l'utilizzo

Per eliminare le condizioni per l'utilizzo precedenti, è possibile utilizzare la procedura riportata di seguito.

1. Accedere ad Azure e passare a **Condizioni per l'utilizzo** all'indirizzo [ https://aka.ms/catou ](https://aka.ms/catou).
1. Selezionare le condizioni per l'utilizzo che si desidera rimuovere.
1. Fare clic su **Elimina le condizioni**.
1. Nel messaggio visualizzato in cui viene chiesto se si vuole continuare fare clic su **Sì**.

   ![Messaggio in cui viene chiesto di confermare l'eliminazione delle condizioni per l'utilizzo](./media/terms-of-use/delete-tou.png)

   Non è più possibile visualizzare le condizioni per l'utilizzo.

## <a name="deleted-users-and-active-terms-of-use"></a>Utenti eliminati e condizioni per l'utilizzo attive

Per impostazione predefinita, un utente eliminato rimane comunque in Azure AD per 30 giorni, periodo durante il quale un amministratore potrà eseguirne il ripristino se necessario. Dopo 30 giorni l'utente verrà eliminato definitivamente. Inoltre, tramite il portale di Azure Active Directory, un Amministratore globale può in modo esplicito [eliminare definitivamente un utente eliminato di recente](../fundamentals/active-directory-users-restore.md) prima della scadenza di tale periodo. Un utente è stato eliminato definitivamente. i dati successivi relativi a tale utente verranno rimossi dalle condizioni per l'utilizzo attive. Le informazioni di controllo sugli utenti eliminati restano nel log di controllo.

## <a name="policy-changes"></a>Modifiche dei criteri

I criteri di accesso condizionale hanno effetto immediato. In questo caso l'amministratore inizierà a vedere nuvolette "tristi" o messaggi relativi a problemi di token di Azure AD. Dovrà quindi disconnettersi e accedere nuovamente per soddisfare il nuovo criterio.

> [!IMPORTANT]
> Gli utenti inclusi nell'ambito devono disconnettersi e accedere per soddisfare un nuovo criterio se:
>
> - un criterio di accesso condizionale è abilitato per le condizioni per l'utilizzo
> - o viene creata una seconda condizione

## <a name="b2b-guests-preview"></a>Guest B2B (anteprima)

La maggior parte delle organizzazioni dispone di un processo per consentire ai dipendenti di fornire il consenso alle condizioni per l'utilizzo e alle informative sulla privacy dell'organizzazione. Ma come è possibile applicare gli stessi consensi per i guest di Azure AD business-to-business (B2B) quando vengono aggiunti tramite SharePoint o i team? Usando l'accesso condizionale e le condizioni per l'utilizzo, è possibile applicare un criterio direttamente agli utenti Guest B2B. Durante il flusso di riscatto dell'invito, l'utente viene visualizzato con le condizioni per l'utilizzo. Questo supporto è attualmente disponibile in versione di anteprima.

Le Condizioni per l'utilizzo verranno visualizzate solo quando l'utente dispone di un account guest in Azure AD. SharePoint Online dispone attualmente di un' [esperienza di destinatario di condivisione esterna ad hoc](/sharepoint/what-s-new-in-sharing-in-targeted-release) per condividere un documento o una cartella che non richiede che l'utente disponga di un account Guest. In questo caso, le condizioni per l'utilizzo non vengono visualizzate.

![Riquadro utenti e gruppi-scheda Includi con l'opzione tutti gli utenti Guest selezionata](./media/terms-of-use/b2b-guests.png)

## <a name="support-for-cloud-apps-preview"></a>Supporto per le app cloud (anteprima)

Le Condizioni per l'utilizzo possono essere usate per diverse app cloud, ad esempio Azure Information Protection e Microsoft Intune. Questo supporto è attualmente disponibile in versione di anteprima.

### <a name="azure-information-protection"></a>Azure Information Protection

È possibile configurare un criterio di accesso condizionale per l'app Azure Information Protection e richiedere le condizioni per l'utilizzo quando un utente accede a un documento protetto. In questo modo si attiveranno le condizioni per l'utilizzo prima che un utente acceda a un documento protetto per la prima volta.

![Riquadro app cloud con Microsoft Azure Information Protection app selezionata](./media/terms-of-use/cloud-app-info-protection.png)

### <a name="microsoft-intune-enrollment"></a>Registrazione di Microsoft Intune

È possibile configurare un criterio di accesso condizionale per l'app di registrazione Microsoft Intune e richiedere le condizioni per l'utilizzo prima della registrazione di un dispositivo in Intune. Per altre informazioni, vedere il Leggi il [post di blog sulla scelta della soluzione di Condizioni di utilizzo più adatta per l'organizzazione](https://go.microsoft.com/fwlink/?linkid=2010506&clcid=0x409).

![Riquadro app cloud con Microsoft Intune app selezionata](./media/terms-of-use/cloud-app-intune.png)

> [!NOTE]
> L'app di registrazione di Intune non è supportata per le condizioni per l' [utilizzo per dispositivo](#per-device-terms-of-use).

## <a name="frequently-asked-questions"></a>Domande frequenti

**D: Ricerca per categorie vedere quando/se un utente ha accettato le condizioni per l'utilizzo?**<br />
R: Nel pannello Condizioni per l'utilizzo fare clic sul numero sotto **Accettato**. È anche possibile visualizzare o cercare l'attività accettata nei log di controllo di Azure AD. Per altre informazioni, vedere Visualizzare il report degli utenti che hanno accettato e rifiutato e [Visualizzare i log di controllo di Azure AD](#view-azure-ad-audit-logs).

**D: Per quanto tempo sono archiviate le informazioni?**<br />
R: Il numero di utenti viene conteggiato nel rapporto sulle condizioni per l'utilizzo e le persone accettate/rifiutate vengono archiviate per la durata delle condizioni per l'utilizzo. I log di controllo di Azure AD vengono archiviati per 30 giorni.

**D: Perché viene visualizzato un numero diverso di consensi nei report sulle condizioni per l'utilizzo e i log di controllo Azure AD?**<br />
R: Il report delle condizioni per l'utilizzo viene archiviato per la durata delle condizioni per l'utilizzo, mentre i log di controllo Azure AD vengono archiviati per 30 giorni. Inoltre, il report condizioni per l'utilizzo Visualizza solo lo stato di consenso dell'utente corrente. Se, ad esempio, un utente rifiuta e quindi accetta, il report sulle condizioni per l'utilizzo indicherà solo l'accettazione dell'utente. Se è necessario visualizzare la cronologia, è possibile usare i log di controllo di Azure AD.

**D: Se si modificano i dettagli per le condizioni per l'utilizzo, è necessario che gli utenti accettino di nuovo?**<br />
R: No, se un amministratore modifica i dettagli per le condizioni per l'utilizzo (nome, nome visualizzato, richiedere agli utenti di espandere o aggiungere una lingua), non richiede che gli utenti riaccettino le nuove condizioni.

**D: È possibile aggiornare un documento di condizioni per l'utilizzo esistente?**<br />
R: Attualmente, non è possibile aggiornare un documento di condizioni per l'utilizzo esistente. Per modificare un documento relativo alle condizioni per l'utilizzo, è necessario creare una nuova istanza di condizioni per l'utilizzo.

**D: Se i collegamenti ipertestuali sono presenti nel documento PDF sulle condizioni per l'utilizzo, gli utenti finali potranno fare clic su di essi?**<br />
R: Sì, gli utenti finali possono selezionare collegamenti ipertestuali a pagine aggiuntive, ma non sono supportati collegamenti a sezioni all'interno del documento.

**D: Le condizioni per l'utilizzo possono supportare più lingue?**<br />
R: Sì. Attualmente sono disponibili 108 lingue diverse che possono essere configurate da un amministratore per le singole condizioni per l'utilizzo. Un amministratore può caricare più documenti PDF e contrassegnare i documenti con una lingua corrispondente (fino a 108). Quando gli utenti finali accedono, vengono esaminate le preferenze della lingua del browser e viene visualizzato il documento corrispondente. Se non viene trovata alcuna corrispondenza, verrà visualizzato il documento predefinito, ovvero il primo documento che viene caricato.

**D: Quando vengono attivate le condizioni per l'utilizzo?**<br />
R: Le condizioni per l'utilizzo vengono attivate durante l'esperienza di accesso.

**D: Quali applicazioni è possibile usare come destinazione le condizioni per l'utilizzo?**<br />
R: È possibile creare un criterio di accesso condizionale per le applicazioni aziendali usando l'autenticazione moderna. Per altre informazioni, vedere le [applicazioni aziendali](./../manage-apps/view-applications-portal.md).

**D: È possibile aggiungere più condizioni per l'utilizzo a un determinato utente o app?**<br />
R: Sì, creando più criteri di accesso condizionale destinati a tali gruppi o applicazioni. Se un utente rientra nell'ambito di più condizioni per l'utilizzo, accetta una condizione per l'utilizzo alla volta.

**D: Cosa accade se un utente rifiuta le condizioni per l'utilizzo?**<br />
R: Ne viene bloccato l'accesso all'applicazione. L'utente deve accedere nuovamente e accettare le condizioni.

**D: È possibile che non si accettino le condizioni per l'utilizzo accettate in precedenza?**<br />
R: È possibile [esaminare le condizioni per l'utilizzo precedentemente accettate](#how-users-can-review-their-terms-of-use), ma al momento non esiste un modo per non accettarle.

**D: Cosa succede se si usano anche termini e condizioni di Intune?**<br />
R: Se sono state configurate sia Azure AD condizioni per l'utilizzo che i [termini e le condizioni di Intune](/intune/terms-and-conditions-create), all'utente verrà richiesto di accettare entrambe. Per altre informazioni, vedere il [post di blog sulla scelta della soluzione di Condizioni di utilizzo più adatta per l'organizzazione](https://go.microsoft.com/fwlink/?linkid=2010506&clcid=0x409).

**D: Quali endpoint utilizzano le condizioni per l'utilizzo del servizio per l'autenticazione?**<br />
R: Condizioni per l'utilizzo utilizza gli endpoint seguenti per l'autenticazione: https://tokenprovider.termsofuse.identitygovernance.azure.com e. https://account.activedirectory.windowsazure.com Se l'organizzazione ha un elenco di URL consentiti per la registrazione, è necessario aggiungere questi endpoint all'elenco Consenti insieme agli endpoint Azure AD per l'accesso.

## <a name="next-steps"></a>Passaggi successivi

- [Avvio rapido: Richiedere l'accettazione delle condizioni per l'utilizzo prima dell'accesso alle app cloud](require-tou.md)
- [Procedure consigliate per l'accesso condizionale in Azure Active Directory](best-practices.md)
