---
title: Condizioni per l'utilizzo di Azure Active Directory| Microsoft Docs
description: Illustra come iniziare a usare le Condizioni per l'utilizzo di Microsoft Azure AD per presentare le informazioni ai dipendenti o ai guest prima di ottenere l'accesso.
services: active-directory
author: rolyon
manager: mtillman
editor: ''
ms.assetid: d55872ef-7e45-4de5-a9a0-3298e3de3565
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 02/09/2019
ms.author: rolyon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8f2de9274e1b6c996e75148d1a1f018d73a721bb
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57443454"
---
# <a name="azure-active-directory-terms-of-use-feature"></a>Funzionalità Condizioni per l'utilizzo di Azure Active Directory
Condizioni per l'utilizzo di Azure Active Directory offre un sistema semplice che le organizzazioni possono usare per presentare le informazioni agli utenti finali. In questo modo si garantisce che gli utenti vedano le dichiarazioni rilevanti di non responsabilità che si riferiscono ai requisiti legali o di conformità. Questo articolo descrive come iniziare con Condizioni per l'utilizzo.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="overview-videos"></a>Panoramica video

Il video seguente presenta una rapida panoramica di Condizioni per l'utilizzo.

>[!VIDEO https://www.youtube.com/embed/tj-LK0abNao]

Per altri video, vedere:
- [Come distribuire Condizioni per l'utilizzo in Azure Active Directory](https://www.youtube.com/embed/N4vgqHO2tgY)
- [Come distribuire Condizioni per l'utilizzo in Azure Active Directory](https://www.youtube.com/embed/t_hA4y9luCY)

## <a name="what-can-i-do-with-terms-of-use"></a>Quali operazioni si possono eseguire con Condizioni per l'utilizzo?
Condizioni per l'utilizzo di Azure AD ha le funzionalità seguenti:
- Fare accettare le condizioni per l'utilizzo ai dipendenti o agli ospiti prima dell'accesso.
- Fare accettare le condizioni per l'utilizzo di ogni dispositivo ai dipendenti o ai guest prima dell'accesso.
- Fare accettare le condizioni per l'utilizzo ai dipendenti o ai guest in una pianificazione ricorrente.
- Presentare condizioni per l'utilizzo generali per tutti gli utenti dell'organizzazione.
- Presentare condizioni per l'utilizzo specifiche basate sugli attributi di un utente (ad esempio, medici o infermieri oppure dipendenti nazionali o internazionali, tramite [gruppi dinamici](../users-groups-roles/groups-dynamic-membership.md)).
- Presentare condizioni per l'utilizzo specifiche al momento dell'accesso ad applicazioni a elevato impatto aziendale, come Salesforce.
- Presentare condizioni per l'utilizzo in lingue diverse.
- Elencare gli utenti che hanno accettato o rifiutato le condizioni per l'utilizzo.
- Contribuire a rispettare le normative sulla privacy.
- Visualizzare un log dell'attività di Condizioni per l'utilizzo per conformità e controllo.
- Creare e gestire Condizioni per l'utilizzo con [API Microsoft Graph](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/agreement) (attualmente in anteprima).

## <a name="prerequisites"></a>Prerequisiti
Per usare e configurare Condizioni per l'utilizzo di Azure AD, è necessario disporre di:

- Una sottoscrizione di Azure AD Premium P1, P2, EMS E3 o EMS E5.
    - Se non si dispone di una di queste sottoscrizioni, è possibile [ottenere Azure AD Premium](../fundamentals/active-directory-get-started-premium.md) oppure [abilitare la versione di valutazione di Azure AD Premium](https://azure.microsoft.com/trial/get-started-active-directory/).
- Uno dei seguenti account di amministratore per la directory da configurare:
    - Amministratore globale
    - Amministratore della sicurezza
    - Amministratore di accesso condizionale

## <a name="terms-of-use-document"></a>Documento sulle condizioni per l'utilizzo

Il contenuto di Condizioni per l'utilizzo di Azure AD viene presentato in formato PDF. Il contenuto del file PDF può essere di qualsiasi tipo, ad esempio documenti di contratti esistenti. Questo consente di acquisire il consenso degli utenti finali durante l'accesso. Per supportare gli utenti sui dispositivi mobili, le dimensioni del carattere consigliato nel file PDF sono 24 punti.

## <a name="add-terms-of-use"></a>Aggiungere le condizioni per l'utilizzo
Dopo avere completato le condizioni per l'utilizzo, seguire questa procedura per aggiungerle.

1. Accedere ad Azure come amministratore globale, amministratore della sicurezza o amministratore di accesso condizionale.

1. Passare a **Condizioni per l'utilizzo** all'indirizzo [https://aka.ms/catou](https://aka.ms/catou).

    ![Pannello di Condizioni per l'utilizzo](./media/active-directory-tou/tou-blade.png)

1. Fare clic su **Nuove condizioni**.

    ![Aggiungere le condizioni per l'utilizzo](./media/active-directory-tou/new-tou.png)

1. Nella casella **Nome** immettere un nome per le Condizioni per l'utilizzo che verranno usate nel portale di Azure.

1. Nella casella **Nome visualizzato** immettere un titolo visualizzato dagli utenti quando eseguono l'accesso.

1. Per il **documento Condizioni per l'utilizzo**, sfogliare per trovare il PDF delle condizioni per l'utilizzo completato e selezionarlo.

1. Selezionare la lingua per il documento Condizioni per l'utilizzo. L'opzione relativa alla lingua consente di caricare più versioni delle condizioni per l'utilizzo, ognuna in una lingua diversa. La versione visualizzata all'utente finale dipenderà dalle preferenze del browser.

1. Per richiedere agli utenti finali di visualizzare le Condizioni per l'utilizzo prima di accettarle, impostare **Richiedere agli utenti di espandere le condizioni d'utilizzo** su **On**.

1. Per richiedere agli utenti finali di accettare le Condizioni per l'utilizzo in ogni dispositivo da cui accedono, impostare **Richiedere agli utenti di fornire il consenso in tutti i dispositivi** su **On**. Per altre informazioni, vedere le [Condizioni per l'utilizzo per ogni dispositivo](#per-device-terms-of-use).

1. Se si desidera impostare come scaduti i consensi alle Condizioni per l'utilizzo in una pianificazione, impostare **Scadenza consensi** su **On**. Se si imposta su On, vengono visualizzate due impostazioni aggiuntive di pianificazione.

    ![Scadenza consensi](./media/active-directory-tou/expire-consents.png)

1. Usare le impostazioni **Scadenza a partire da** e **Frequenza** per specificare la pianificazione per la scadenza delle Condizioni per l'utilizzo. La tabella seguente illustra il risultato di un paio di impostazioni di esempio:

    | Scadenza a partire da | Frequenza | Risultato |
    | --- | --- | --- |
    | Data odierna  | Mensile | A partire da oggi, gli utenti devono accettare le Condizioni per l'utilizzo e quindi riaccettarle ogni mese. |
    | Data nel futuro  | Mensile | A partire da oggi, gli utenti devono accettare le Condizioni per l'utilizzo. Quando si verifica la data futura, scadono i consensi e quindi gli utenti devono riaccettare ogni mese.  |

    Ad esempio, se si imposta la scadenza a partire dalla data **1 gen** e la frequenza su **Mensile**, ecco come le scadenze potrebbero verificarsi per i due utenti:

    | Utente | Prima data di accettazione | Prima data di scadenza | Seconda data di scadenza | Terza data di scadenza |
    | --- | --- | --- | --- | --- |
    | Alice | 1 gen | 1 feb | 1 mar | 1 apr |
    | Bob | 15 gen | 1 feb | 1 mar | 1 apr |

1. Usare l'impostazione **Durata prima di una nuova accettazione richiede (giorni)** per specificare il numero di giorni che deve passare prima che l'utente debba accettare di nuovo le Condizioni per l'utilizzo. Ciò consente agli utenti di seguire una pianificazione personalizzata. Ad esempio, se si imposta la durata su **30** giorni, ecco come le scadenze potrebbero verificarsi per due utenti:

    | Utente | Prima data di accettazione | Prima data di scadenza | Seconda data di scadenza | Terza data di scadenza |
    | --- | --- | --- | --- | --- |
    | Alice | 1 gen | 31 gen | 2 mar | 1 apr |
    | Bob | 15 gen | 14 feb | 16 mar | 15 apr |

    È possibile usare le impostazioni **Scadenza consensi** e **Durata prima di una nuova accettazione richiede (giorni)** insieme, ma in genere si usa o l'una o l'altra.

1. In **Accesso condizionale**, usare l'elenco **Applica con modello di criteri di accesso condizionale** per selezionare il modello per applicare le Condizioni per l'utilizzo.

    ![Modelli di accesso condizionale](./media/active-directory-tou/conditional-access-templates.png)

    | Modello | DESCRIZIONE |
    | --- | --- |
    | **Accesso alle app cloud per tutti i guest** | Verrà creato un criterio di accesso condizionale per tutti i guest e tutte le app cloud. Questi criteri influiscono sul portale di Azure. Dopo la creazione, potrebbe essere necessario disconnettersi e accedere. |
    | **Accesso alle app cloud per tutti gli utenti** | Verrà creato un criterio di accesso condizionale per tutti gli utenti e tutte le app cloud. Questi criteri influiscono sul portale di Azure. Dopo la creazione, sarà necessario disconnettersi e accedere. |
    | **Criteri personalizzati** | Selezionare gli utenti, i gruppi e le app a cui verranno applicate queste Condizioni per l'utilizzo. |
    | **Creare criteri di accesso condizionale in seguito** | Queste condizioni per l'utilizzo verranno visualizzate nell'elenco di concessione del controllo durante la creazione di un criterio di accesso condizionale. |

    >[!IMPORTANT]
    >L'applicazione dei controlli dei criteri di accesso condizionale (incluse le condizioni per l'utilizzo) non è supportata per gli account di servizio. È consigliabile escludere tutti gli account di servizio dai criteri di accesso condizionale.

     I criteri personalizzati di accesso condizionale consentono condizioni per l'utilizzo granulari, fino a un'applicazione cloud o a un gruppo di utenti specifici. Per ulteriori informazioni, consultare [Avvio rapido: Richiedere l'accettazione delle condizioni per l'utilizzo prima dell'accesso alle app cloud](../conditional-access/require-tou.md).

1. Fare clic su **Create**(Crea).

    Se è stato selezionato un modello personalizzato di accesso condizionale, verrà visualizzata una nuova schermata che consente di creare il criterio personalizzato di accesso condizionale.

    ![Criteri personalizzati](./media/active-directory-tou/custom-policy.png)

    A questo punto dovrebbero essere visualizzate le nuove condizioni per l'utilizzo.

    ![Aggiungere le condizioni per l'utilizzo](./media/active-directory-tou/create-tou.png)

## <a name="view-report-of-who-has-accepted-and-declined"></a>Visualizzare il report degli utenti che hanno accettato e rifiutato
Nel pannello delle condizioni per l'utilizzo è visualizzato il numero di utenti che hanno accettato e rifiutato. Questi conteggi e gli utenti che hanno accettato o rifiutato vengono archiviati per tutta la durata delle condizioni per l'utilizzo.

1. Accedere ad Azure e passare a **Condizioni per l'utilizzo** all'indirizzo [ https://aka.ms/catou ](https://aka.ms/catou).

    ![Pannello di Condizioni per l'utilizzo](./media/active-directory-tou/view-tou.png)

1. Per le Condizioni per l'utilizzo, fare clic sui numeri sotto **Accettato** o **Rifiutato** per visualizzare lo stato corrente degli utenti.

    ![Accettazione delle condizioni per l'utilizzo](./media/active-directory-tou/accepted-tou.png)

1. Per visualizzare la cronologia per un singolo utente, fare clic sui puntini di sospensione (**...** ) e quindi **Visualizza cronologia**.

    ![Visualizza il menu cronologia](./media/active-directory-tou/view-history-menu.png)

    Nel riquadro di visualizzazione della cronologia, si visualizza una cronologia di tutte le accettazioni, i rifiuti e le scadenze.

    ![Riquadro di visualizzazione della cronologia](./media/active-directory-tou/view-history-pane.png)

## <a name="view-azure-ad-audit-logs"></a>Visualizzare i log di controllo di Azure AD
Se si vuole visualizzare attività aggiuntive, Condizioni per l'utilizzo di Azure Active Directory include log di controllo. Ogni consenso dell'utente genera un evento nei log di controllo che viene conservato per **30 giorni**. È possibile visualizzare questi log nel portale o scaricarli come file CSV.

Per iniziare a usare i log di controllo di Azure AD, seguire questa procedura:

1. Accedere ad Azure e passare a **Condizioni per l'utilizzo** all'indirizzo [ https://aka.ms/catou ](https://aka.ms/catou).

1. Selezionare le condizioni per l'utilizzo.

1. Fare clic su **Visualizza log di controllo**.

    ![Pannello di Condizioni per l'utilizzo](./media/active-directory-tou/audit-tou.png)

1. Nella schermata dei log di controllo di Azure AD è possibile filtrare le informazioni usando i menu a discesa per visualizzare informazioni specifiche dei log di controllo.

    È inoltre possibile fare clic su **Download** per scaricare le informazioni in un file con estensione CSV per l'uso in locale.

    ![Log di controllo](./media/active-directory-tou/audit-logs-tou.png)

    Se si fa clic su un log, viene visualizzato un riquadro con i dettagli delle attività aggiuntive.

    ![Dettagli dell'attività](./media/active-directory-tou/audit-log-activity-details.png)

## <a name="what-terms-of-use-looks-like-for-users"></a>Presentazione agli utenti delle condizioni per l'utilizzo
Una volta create e applicate le condizioni per l'utilizzo, gli utenti inclusi nell'ambito visualizzeranno la schermata seguente durante l'accesso.

![Accesso web dell'utente](./media/active-directory-tou/user-tou.png)

La schermata seguente illustra l'aspetto delle condizioni per l'utilizzo nei dispositivi mobili.

![Accesso da dispositivo mobile dell'utente](./media/active-directory-tou/mobile-tou.png)

Gli utenti devono solamente accettare le Condizioni per l'utilizzo una sola volta e non vedranno nuovamente le Condizioni per l'utilizzo agli accessi successivi.

### <a name="how-users-can-review-their-terms-of-use"></a>Visualizzazione delle condizioni per l'utilizzo da parte degli utenti
Gli utenti possono visualizzare e verificare le condizioni per l'utilizzo accettate usando la procedura seguente.

1. Accedere a [https://myapps.microsoft.com](https://myapps.microsoft.com).

1. Nell'angolo superiore destro fare clic sul proprio nome e selezionare **Profilo**.

    ![Profilo](./media/active-directory-tou/tou14.png)

1. Nella pagina Profilo fare clic su **Verificare le condizioni d'uso**.

    ![Profilo - Verificare le condizioni d'uso](./media/active-directory-tou/tou13a.png)

1. Sarà quindi possibile verificare le condizioni per l'utilizzo accettate.

## <a name="edit-terms-of-use-details"></a>Modificare i dettagli delle condizioni per l'utilizzo
È possibile modificare alcuni dettagli delle condizioni per l'utilizzo, ma non è possibile modificare un documento esistente. La procedura seguente descrive come modificare i dettagli.

1. Accedere ad Azure e passare a **Condizioni per l'utilizzo** all'indirizzo [ https://aka.ms/catou ](https://aka.ms/catou).

1. Selezionare le condizioni per l'utilizzo da modificare.

1. Fare clic su **Modifica le condizioni**.

1. Nel riquadro della modifica delle condizioni per l'utilizzo, modificare il nome, il nome visualizzato o richiedere che gli utenti espandano i valori.

    Se sono presenti altre impostazioni che si desidera modificare, ad esempio documenti PDF, richiedere agli utenti di fornire il consenso in tutti i dispositivi, la scadenza consensi, la durata prima della nuova accettazione o i criteri di accesso condizionale, è necessario creare nuove Condizioni per l'utilizzo.

    ![Modificare le condizioni per l'utilizzo](./media/active-directory-tou/edit-tou.png)

1. Fare clic su **Salva** per salvare le modifiche.

    Dopo aver salvato le modifiche, gli utenti non dovranno accettare nuovamente le condizioni nuove.

## <a name="add-a-terms-of-use-language"></a>Aggiungere una lingua delle condizioni per l'utilizzo
La procedura seguente descrive come aggiungere una lingua delle condizioni per l'utilizzo.

1. Accedere ad Azure e passare a **Condizioni per l'utilizzo** all'indirizzo [ https://aka.ms/catou ](https://aka.ms/catou).

1. Selezionare le condizioni per l'utilizzo da modificare.

1. Nel riquadro dei dettagli fare clic sulla scheda **Lingue**.

    ![Aggiungere le condizioni per l'utilizzo](./media/active-directory-tou/languages-tou.png)

1. Fare clic su **Aggiungi lingua**.

1. Nel riquadro Aggiungi la lingua delle condizioni per l'utilizzo caricare il PDF localizzato e selezionare la lingua.

    ![Aggiungere le condizioni per l'utilizzo](./media/active-directory-tou/language-add-tou.png)

1. Fare clic su **Aggiungi** per aggiungere la lingua.

## <a name="per-device-terms-of-use"></a>Condizioni per l'utilizzo per ogni dispositivo

L'impostazione **Richiedere agli utenti di fornire il consenso in tutti i dispositivi** consente di richiedere agli utenti finali di accettare le Condizioni per l'utilizzo in ogni dispositivo da cui accedono. All'utente finale verrà richiesto di aggiungere il dispositivo in Azure AD. Quando il dispositivo è stato aggiunto, l'ID del dispositivo consente di applicare le Condizioni per l'utilizzo in ogni dispositivo.

Ecco un elenco dei software e delle piattaforme supportate.

> [!div class="mx-tableFixed"]
> |  | iOS | Android | Windows 10 | Altri |
> | --- | --- | --- | --- | --- |
> | **App nativa** | Sì | Sì | Sì |  |
> | **Microsoft Edge** | Sì | Sì | Sì |  |
> | **Internet Explorer** | Sì | Sì | Sì |  |
> | **Chrome (con estensione)** | Sì | Sì | Sì |  |

Le Condizioni per l'utilizzo per ogni dispositivo presenta i vincoli seguenti:

- Un dispositivo può essere aggiunto a un solo tenant.
- Un utente deve disporre delle autorizzazioni per aggiungere il dispositivo.
- L'app di registrazione di Intune non è supportata.

Se il dispositivo dell'utente non è stato aggiunto, riceveranno un messaggio che indica che devono aggiungere il dispositivo. L'esperienza sarà dipendente dalla piattaforma e dal software.

### <a name="join-a-windows-10-device"></a>Aggiungere un dispositivo Windows 10

Se un utente sta usando Windows 10 e Microsoft Edge, riceverà un messaggio simile al seguente per [aggiungere il dispositivo](../user-help/user-help-join-device-on-network.md#to-join-an-already-configured-windows-10-device).

![Windows 10 e Microsoft Edge - prompt di aggiunta del dispositivo](./media/active-directory-tou/per-device-win10-edge.png)

Se si usa Chrome, verrà richiesto di installare l'[estensione account di Windows 10](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji).

### <a name="browsers"></a>Browser

Se un utente sta usando un browser che non è supportato, si dovrà usare un browser diverso.

![Browser non supportato](./media/active-directory-tou/per-device-browser-unsupported.png)

## <a name="delete-terms-of-use"></a>Eliminare le condizioni per l'utilizzo
È possibile eliminare le condizioni per l'utilizzo obsolete con la procedura seguente.

1. Accedere ad Azure e passare a **Condizioni per l'utilizzo** all'indirizzo [ https://aka.ms/catou ](https://aka.ms/catou).

1. Selezionare le condizioni per l'utilizzo da rimuovere.

1. Fare clic su **Elimina le condizioni**.

1. Nel messaggio visualizzato in cui viene chiesto se si vuole continuare fare clic su **Sì**.

    ![Eliminare le condizioni per l'utilizzo](./media/active-directory-tou/delete-tou.png)

    A questo punto le condizioni per l'utilizzo non dovrebbero più essere visualizzate.

## <a name="deleted-users-and-active-terms-of-use"></a>Utenti eliminati e condizioni per l'utilizzo attive
Per impostazione predefinita, un utente eliminato rimane comunque in Azure AD per 30 giorni, periodo durante il quale un amministratore potrà eseguirne il ripristino se necessario. Dopo 30 giorni l'utente verrà eliminato definitivamente. Inoltre, tramite il portale di Azure Active Directory, un Amministratore globale può in modo esplicito [eliminare definitivamente un utente eliminato di recente](../fundamentals/active-directory-users-restore.md) prima della scadenza di tale periodo. Dopo l'eliminazione definitiva di un utente, i dati riguardanti tale utente verranno rimossi dalle condizioni per l'utilizzo attive. Le informazioni di controllo sugli utenti eliminati restano nel log di controllo.

## <a name="policy-changes"></a>Modifiche dei criteri
I criteri di accesso condizionale diventano effettivi immediatamente. In questo caso l'amministratore inizierà a vedere nuvolette "tristi" o messaggi relativi a problemi di token di Azure AD. Dovrà quindi disconnettersi e accedere nuovamente per soddisfare il nuovo criterio.

>[!IMPORTANT]
> Gli utenti inclusi nell'ambito devono disconnettersi e accedere per soddisfare un nuovo criterio se:
> - è abilitato un criterio di accesso condizionale per una condizione per l'utilizzo
> - o viene creata una seconda condizione

## <a name="b2b-guests-preview"></a>Guest B2B (anteprima)

La maggior parte delle organizzazioni prevede un processo per i propri dipendenti volto a fornire il consenso alle Condizioni per l'utilizzo e alle informative sulla privacy della propria organizzazione. Ma come è possibile applicare gli stessi consensi per i guest di Azure AD business-to-business (B2B) quando vengono aggiunti tramite SharePoint o i team? Tramite l'accesso condizionale e le Condizioni per l'utilizzo, è possibile applicare un criterio direttamente nei confronti degli utenti guest B2B. Durante il flusso di riscatto dell'invito, all'utente vengono presentate le Condizioni per l'utilizzo. Questo supporto è attualmente disponibile in versione di anteprima.

Le Condizioni per l'utilizzo verranno visualizzate solo quando l'utente dispone di un account guest in Azure AD. SharePoint Online è attualmente un' [esperienza di destinatario condivisione esterna ad hoc](/sharepoint/what-s-new-in-sharing-in-targeted-release) per condividere un documento o una cartella che non richiede all'utente di disporre di un account guest. In questo caso, le Condizioni per l'utilizzo non vengono visualizzate.

![Tutti gli utenti guest](./media/active-directory-tou/b2b-guests.png)

## <a name="support-for-cloud-apps-preview"></a>Supporto per le app cloud (anteprima)

Le Condizioni per l'utilizzo possono essere usate per diverse app cloud, ad esempio Azure Information Protection e Microsoft Intune. Questo supporto è attualmente disponibile in versione di anteprima.

### <a name="azure-information-protection"></a>Azure Information Protection

È possibile configurare un criterio di accesso condizionale per l'app Azure Information Protection e richiedere le Condizioni per l'utilizzo quando un utente accede a un documento protetto. Questo attiverà le Condizioni per l'utilizzo prima che un utente acceda a un documento protetto per la prima volta.

![App cloud di Azure Information Protection](./media/active-directory-tou/cloud-app-info-protection.png)

### <a name="microsoft-intune-enrollment"></a>Registrazione di Microsoft Intune

È possibile configurare un criterio di accesso condizionale per l'app di registrazione di Microsoft Intune e richiedere le Condizioni per l'utilizzo prima della registrazione di un dispositivo in Intune. Per altre informazioni, vedere il Leggi il [post di blog sulla scelta della soluzione di Condizioni di utilizzo più adatta per l'organizzazione](https://go.microsoft.com/fwlink/?linkid=2010506&clcid=0x409).

![App cloud di Microsoft Intune](./media/active-directory-tou/cloud-app-intune.png)

> [!NOTE]
> L'app di registrazione di Intune non è supportata per le [condizioni per l'uso](#per-device-terms-of-use).

## <a name="frequently-asked-questions"></a>Domande frequenti

**D: Come posso sapere se un utente ha accettato le condizioni per l'utilizzo?**<br />
R: Nel pannello Condizioni per l'utilizzo fare clic sul numero sotto **Accettato**. È anche possibile visualizzare o cercare l'attività accettata nei log di controllo di Azure AD. Per altre informazioni, vedere Visualizzare il report degli utenti che hanno accettato e rifiutato e [Visualizzare i log di controllo di Azure AD](#view-azure-ad-audit-logs).

**D: Per quanto tempo sono archiviate le informazioni?**<br />
R: I conteggi inclusi nel report delle Condizioni per l'utilizzo e gli utenti che hanno accettato o rifiutato vengono archiviati per tutta la durata delle Condizioni per l'utilizzo. I log di controllo di Azure AD vengono archiviati per 30 giorni.

**D: Perché viene visualizzato un numero diverso di consensi nel report delle Condizioni per l'utilizzo rispetto ai log di controllo di Azure AD?**<br />
R: Il report delle Condizioni per l'utilizzo viene archiviato per la durata di tale Condizioni per l'utilizzo, mentre i log di controllo di Azure AD vengono archiviati per 30 giorni. Inoltre, i report delle Condizioni per l'utilizzo mostrano solo lo stato di consenso corrente degli utenti. Ad esempio, se un utente rifiuta e in seguito accetta, il report delle Condizioni per l'utilizzo mostrerà solo quest'ultima azione dell'utente. Se è necessario visualizzare la cronologia, è possibile usare i log di controllo di Azure AD.

**D: Se i termini delle condizioni per l'utilizzo vengono modificati, è necessario che gli utenti le accettino di nuovo?**<br />
R: No, se un amministratore modifica i dettagli delle Condizioni per l'utilizzo (nome, nome visualizzato, richiedere agli utenti di espandere o aggiungere un linguaggio), non richiede che gli utenti riaccettino i nuovi termini.

**D: È possibile aggiornare un documento esistente sulle Condizioni per l'utilizzo?**<br />
R: Al momento non è possibile aggiornare un documento esistente sulle condizioni per l'utilizzo. Per modificare un documento sulle condizioni per l'utilizzo, è necessario creare una nuova istanza delle condizioni per l'utilizzo.

**D: Se sono presenti collegamenti ipertestuali nel documento PDF delle Condizioni per l'utilizzo, gli utenti finali possono selezionarli?**<br />
R: Viene eseguito il rendering predefinito del file PDF in formato JPEG, in modo che i collegamenti ipertestuali non siano selezionabili. Gli utenti hanno la possibilità di selezionare **In caso di problemi di visualizzazione fare clic qui** per eseguire il rendering PDF in modo nativo in cui sono supportati i collegamenti ipertestuali.

**D: Le condizioni per l'utilizzo possono supportare più lingue?**<br />
R: Sì. L'amministratore può attualmente configurare 108 lingue diverse per singole condizioni per l'utilizzo. Un amministratore può caricare più documenti PDF e contrassegnare i documenti con una lingua corrispondente (fino a 108). Quando gli utenti finali accedono, vengono esaminate le preferenze della lingua del browser e viene visualizzato il documento corrispondente. Se non viene trovata alcuna corrispondenza, verrà visualizzato il documento predefinito, ovvero il primo documento che viene caricato.

**D: Quando vengono attivate le condizioni per l'utilizzo?**<br />
R: Le condizioni per l'utilizzo vengono attivate durante l'accesso.

**D: A quali altre applicazioni è possibile applicare le condizioni per l'utilizzo?**<br />
R: È possibile creare un criterio di accesso condizionale per le applicazioni aziendali utilizzando l'autenticazione moderna. Per altre informazioni, vedere le [applicazioni aziendali](./../manage-apps/view-applications-portal.md).

**D: È possibile aggiungere più condizioni per l'utilizzo a un utente o a un'app specifici?**<br />
R: Sì, mediante la creazione di più criteri di accesso condizionale destinati a tali gruppi o applicazioni. Se un utente rientra nell'ambito di più condizioni per l'utilizzo, accetta una condizione alla volta.

**D: Cosa accade se un utente rifiuta le condizioni per l'utilizzo?**<br />
R: Ne viene bloccato l'accesso all'applicazione. L'utente deve accedere nuovamente e accettare le condizioni.

**D: È possibile annullare l'accettazione di Condizioni per l'utilizzo accettate in precedenza?**<br />
R: È possibile [annullare l'accettazione di Condizioni per l'utilizzo accettate in precedenza](#how-users-can-review-their-terms-of-use), ma attualmente non esiste un modo per farlo.

**D: Cosa succede se si usano anche termini e condizioni di Intune?**<br />
R: Se sono stati configurati sia Condizioni per l'utilizzo di Azure AD sia [termini e condizioni di Intune](/intune/terms-and-conditions-create), l'utente dovrà accettarli entrambi. Per altre informazioni, vedere il [post di blog sulla scelta della soluzione di Condizioni di utilizzo più adatta per l'organizzazione](https://go.microsoft.com/fwlink/?linkid=2010506&clcid=0x409).

## <a name="next-steps"></a>Passaggi successivi

- [Avvio rapido: Richiedere l'accettazione delle condizioni per l'utilizzo prima dell'accesso alle app cloud](../conditional-access/require-tou.md)
- [Procedure consigliate per l'accesso condizionale in Azure Active Directory](../conditional-access/best-practices.md)
