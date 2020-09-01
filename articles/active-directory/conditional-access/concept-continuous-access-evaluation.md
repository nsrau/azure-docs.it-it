---
title: Valutazione dell'accesso continuo in Azure AD
description: Risposta alle modifiche nello stato utente più velocemente con la valutazione dell'accesso continuo in Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jlu
ms.collection: M365-identity-device-management
ms.openlocfilehash: 39736f0a369064e1a825ba3f6975a01c5e9ecc40
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/30/2020
ms.locfileid: "89147515"
---
# <a name="continuous-access-evaluation"></a>Valutazione continua dell'accesso

La scadenza e l'aggiornamento dei token è un meccanismo standard nel settore. Quando un'applicazione client come Outlook si connette a un servizio come Exchange Online, le richieste API vengono autorizzate usando i token di accesso OAuth 2,0. Per impostazione predefinita, i token di accesso sono validi per un'ora, alla scadenza, il client viene reindirizzato di nuovo a Azure AD per aggiornarli. Tale periodo di aggiornamento offre l'opportunità di rivalutare i criteri per l'accesso degli utenti. Ad esempio: è possibile scegliere di non aggiornare il token a causa di un criterio di accesso condizionale o perché l'utente è stato disabilitato nella directory. 

I clienti hanno espresso dubbi sul ritardo tra le condizioni in cui cambiano le condizioni per l'utente, ad esempio il percorso di rete o il furto di credenziali, e quando i criteri possono essere applicati correlati a tale modifica. È stato sperimentato l'approccio "oggetto smussato" per la durata dei token ridotta, ma è stato rilevato che possono compromettere l'esperienza utente e l'affidabilità senza eliminare i rischi.

Una risposta tempestiva alle violazioni dei criteri o ai problemi di sicurezza richiede effettivamente una "conversazione" tra l'emittente del token, ad esempio Azure AD e il relying party, ad esempio Exchange Online. Questa conversazione bidirezionale ci offre due importanti funzionalità. Il relying party può rilevare le modifiche apportate, ad esempio un client proveniente da una nuova posizione, e indicare all'emittente del token. Fornisce inoltre all'emittente del token un modo per indicare al relying party di arrestare il rispetto dei token per un determinato utente a causa di compromissione dell'account, disabilitazione o altre problematiche. Il meccanismo per questa conversazione è il CAE (Continuous Access Evaluation). L'obiettivo è quello di rispondere quasi in tempo reale, ma in alcuni casi è possibile che venga osservata una latenza di un massimo di 15 minuti a causa del tempo di propagazione dell'evento.

L'implementazione iniziale della valutazione dell'accesso continuo è incentrata su Exchange, teams e SharePoint Online. 

### <a name="key-benefits"></a>Vantaggi principali

- Terminazione utente o modifica/reimpostazione della password: la revoca della sessione utente verrà applicata quasi in tempo reale.
- Modifica del percorso di rete: i criteri di posizione dell'accesso condizionale verranno applicati quasi in tempo reale.
- L'esportazione di token in un computer all'esterno di una rete attendibile può essere impedita con i criteri di posizione dell'accesso condizionale.

## <a name="scenarios"></a>Scenari 

Esistono due scenari che costituiscono la valutazione dell'accesso continuo, la valutazione di eventi critici e la valutazione dei criteri di accesso condizionale.

### <a name="critical-event-evaluation"></a>Valutazione evento critico

La valutazione dell'accesso continuo viene implementata abilitando i servizi, ad esempio Exchange Online, SharePoint Online e i team, per sottoscrivere eventi critici in Azure AD in modo che tali eventi possano essere valutati e applicati quasi in tempo reale. La valutazione di eventi critici non si basa sui criteri di accesso condizionale, pertanto è disponibile in qualsiasi tenant. Attualmente vengono valutati gli eventi seguenti:

- L'account utente è stato eliminato o disabilitato
- La password per un utente è stata modificata o reimpostata
- Autenticazione a più fattori abilitata per l'utente
- L'amministratore revoca in modo esplicito tutti i token di aggiornamento per un utente
- Rischi utente elevati rilevati da Azure AD Identity Protection

Questo processo Abilita lo scenario in cui gli utenti perdono l'accesso ai file, alla posta elettronica, al calendario o alle attività di SharePoint Online, nonché ai team da app client O365 entro pochi minuti da uno di questi eventi critici. 

### <a name="conditional-access-policy-evaluation-preview"></a>Valutazione dei criteri di accesso condizionale (anteprima)

Exchange e SharePoint sono in grado di sincronizzare i criteri di accesso condizionale chiave in modo che possano essere valutati all'interno del servizio stesso.

Questo processo Abilita lo scenario in cui gli utenti perdono l'accesso ai file dell'organizzazione, alla posta elettronica, al calendario o alle attività dalle app client O365 o SharePoint Online immediatamente dopo le modifiche del percorso di rete.

> [!NOTE]
> Non tutte le combinazioni di provider di risorse e app sono supportate. Vedere la tabella seguente. Office fa riferimento a Word, Excel e PowerPoint

| | Outlook Web | Outlook Win32 | Outlook iOS | Outlook Android | Outlook Mac |
| :--- | :---: | :---: | :---: | :---: | :---: |
| SharePoint Online | Supportato | Supportato | Non supportato | Non supportato | Supportato |
| Exchange Online | Supportato | Supportato | Supportato | Supportato | Supportato |

| | App Web di Office | App di Office Win32 | Office per iOS | Office per Android | Office per Mac |
| :--- | :---: | :---: | :---: | :---: | :---: |
| SharePoint Online | Supportato | Supportato | Non supportato | Supportato | Supportato |
| Exchange Online | Supportato | Supportato | Non supportato | Supportato | Supportato |

### <a name="client-side-claim-challenge"></a>Richiesta di attestazione lato client

Prima della valutazione dell'accesso continuo, i client tentavano sempre di riprodurre il token di accesso dalla cache, purché non fosse scaduto. Con CAE, viene introdotto un nuovo caso che un provider di risorse può rifiutare un token anche quando non è scaduto. Per informare i client di ignorare la cache anche se i token memorizzati nella cache non sono scaduti, viene introdotto un meccanismo denominato **Claim Challenge** per indicare che il token è stato rifiutato ed è necessario emettere un nuovo token di accesso Azure ad. CAE richiede un aggiornamento del client per comprendere la richiesta di attestazione. La versione più recente delle seguenti applicazioni supporta la richiesta di attestazione:

- Finestre di Outlook
- Outlook iOS
- Outlook Android
- Outlook Mac
- Outlook Web App
- Team per Windows (solo per le risorse team)
- Teams iOS (solo per la risorsa Teams)
- Teams Android (solo per la risorsa Teams)
- Mac Teams (solo per la risorsa Teams)
- Word/Excel/PowerPoint per Windows
- Word/Excel/PowerPoint per iOS
- Word/Excel/PowerPoint per Android
- Word/Excel/PowerPoint per Mac

### <a name="token-lifetime"></a>Durata dei token

Poiché i rischi e i criteri vengono valutati in tempo reale, i client che negoziano sessioni compatibili con la valutazione dell'accesso continuo si basano su CAE anziché sui criteri di durata dei token di accesso statici esistenti, il che significa che i criteri di durata dei token configurabili non verranno più rispettati per i client con supporto per CAE che negoziano sessioni compatibili con CAE

La durata del token è aumentata in modo da essere di lunga durata, fino a 28 ore nelle sessioni CAE. La revoca è determinata da eventi critici e da una valutazione dei criteri, non solo da un periodo di tempo arbitrario. Questa modifica aumenta la stabilità delle applicazioni senza influire sul comportamento di sicurezza. 

Se non si usano client con supporto per CAE, la durata dei token di accesso predefinita rimarrà 1 ora, a meno che non sia stata configurata la durata del token di accesso con la funzionalità di anteprima della [durata del token configurabile (CTL)](../develop/active-directory-configurable-token-lifetimes.md) .

## <a name="example-flows"></a>Flussi di esempio

### <a name="user-revocation-event-flow"></a>Flusso eventi di revoca utente:

![Flusso eventi di revoca utente](./media/concept-continuous-access-evaluation/user-revocation-event-flow.png)

1. Un client in grado di supportare CAE presenta le credenziali o un token di aggiornamento per Azure AD richiedere un token di accesso per una risorsa.
1. Viene restituito un token di accesso insieme ad altri artefatti al client.
1. Un amministratore revoca in modo esplicito [tutti i token di aggiornamento per l'utente](https://docs.microsoft.com/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0). Un evento di revoca verrà inviato al provider di risorse da Azure AD.
1. Un token di accesso viene presentato al provider di risorse. Il provider di risorse valuta la validità del token e controlla se è presente un evento di revoca per l'utente. Il provider di risorse utilizza queste informazioni per decidere di concedere l'accesso alla risorsa.
1. In questo caso, il provider di risorse nega l'accesso e invia una richiesta di attestazione 401 + al client.
1. Il client in grado di supportare CAE riconosce la richiesta di attestazione 401 +. Ignora le cache e torna al passaggio 1, inviando il token di aggiornamento insieme alla richiesta di attestazione al Azure AD. Azure AD valuterà quindi tutte le condizioni e chiederà all'utente di eseguire nuovamente l'autenticazione in questo caso.

### <a name="user-condition-change-flow-public-preview"></a>Flusso di modifica della condizione utente (anteprima pubblica):

Nell'esempio seguente, un amministratore dell'accesso condizionale ha configurato un criterio di accesso condizionale basato sulla posizione per consentire l'accesso solo da intervalli IP specifici:

![Flusso eventi condizione utente](./media/concept-continuous-access-evaluation/user-condition-change-flow.png)

1. Un client in grado di supportare CAE presenta le credenziali o un token di aggiornamento per Azure AD richiedere un token di accesso per una risorsa.
1. Azure AD valuta tutti i criteri di accesso condizionale per verificare se l'utente e il client soddisfano le condizioni.
1. Viene restituito un token di accesso insieme ad altri artefatti al client.
1. L'utente si sposta da un intervallo di indirizzi IP consentiti
1. Il client presenta un token di accesso al provider di risorse dall'esterno di un intervallo di indirizzi IP consentiti.
1. Il provider di risorse valuta la validità del token e controlla i criteri di posizione sincronizzati da Azure AD.
1. In questo caso, il provider di risorse nega l'accesso e invia una richiesta di attestazione 401 + al client perché non è proveniente dall'intervallo di indirizzi IP consentiti.
1. Il client in grado di supportare CAE riconosce la richiesta di attestazione 401 +. Ignora le cache e torna al passaggio 1, inviando il token di aggiornamento insieme alla richiesta di attestazione al Azure AD. Azure AD rivaluta tutte le condizioni e nega l'accesso in questo caso.

## <a name="enable-or-disable-cae-preview"></a>Abilitare o disabilitare CAE (anteprima)

1. Accedere al **portale di Azure** come amministratore dell'accesso condizionale, amministratore della sicurezza o amministratore globale.
1. Passare a **Azure Active Directory**  >  **Security**  >  **valutazione dell'accesso continuo**alla sicurezza.
1. Scegliere **Abilita anteprima**.

Da questa pagina, facoltativamente è possibile limitare gli utenti e i gruppi che saranno soggetti all'anteprima.

![Abilitazione dell'anteprima CAE nella portale di Azure](./media/concept-continuous-access-evaluation/enable-cae-preview.png)

## <a name="troubleshooting"></a>Risoluzione dei problemi

### <a name="ip-address-configuration"></a>Configurazione dell'indirizzo IP

Il provider di identità e i provider di risorse possono visualizzare indirizzi IP diversi. Questa mancata corrispondenza può verificarsi a causa di implementazioni di proxy di rete nell'organizzazione o di configurazioni IPv4/IPv6 non corrette tra il provider di identità e il provider di risorse. Ad esempio:

- Il provider di identità vede un indirizzo IP del client.
- Il provider di risorse rileva un indirizzo IP diverso dal client dopo aver passato un proxy.
- L'indirizzo IP visualizzato dal provider di identità fa parte di un intervallo di indirizzi IP consentiti nel criterio, ma l'indirizzo IP del provider di risorse non lo è.

Se questo scenario è presente nell'ambiente per evitare cicli infiniti, Azure AD emetterà un token CAE di un'ora e non imporrà la modifica della posizione del client. Anche in questo caso, la sicurezza è migliorata rispetto ai token di un'ora tradizionale, perché stiamo ancora valutando gli [altri eventi](#critical-event-evaluation) oltre agli eventi di modifica della posizione del client.

> [!IMPORTANT]
> Quando si configurano i percorsi per la valutazione di accesso continuo, usare solo la [condizione di percorso di accesso condizionale basato su IP](../conditional-access/location-condition.md) Non usare le condizioni di località del paese o la funzionalità indirizzi IP attendibili disponibile nella pagina delle impostazioni del servizio Multi-Factor Authentication di Azure.

### <a name="office-and-web-account-manager-settings"></a>Impostazioni di gestione account Office e Web

| Canale di aggiornamento di Office | DisableADALatopWAMOverride | DisableAADWAM |
| --- | --- | --- |
| Canale Enterprise semestrale | Se impostato su abilitato o 1, CAE non è supportato. | Se impostato su abilitato o 1, CAE non è supportato. |
| Canale corrente <br> oppure <br> Canale Enterprise mensile | CAE è supportato indipendentemente dall'impostazione | CAE è supportato indipendentemente dall'impostazione |

Per una spiegazione dei canali di Office Update, vedere [Panoramica dei canali di aggiornamento per le app Microsoft 365](https://docs.microsoft.com/deployoffice/overview-update-channels). Si consiglia alle organizzazioni di non disabilitare Web Account Manager (WAM).

### <a name="policy-change-timing"></a>Temporizzazione delle modifiche dei criteri

A causa del potenziale ritardo di replica tra Azure AD e i provider di risorse, le modifiche ai criteri apportate dagli amministratori potrebbero richiedere fino a 2 ore per essere effettive per Exchange Online.

Esempio: l'amministratore aggiunge un criterio per impedire a un intervallo di indirizzi IP di accedere alla posta elettronica alle 11:00 AM, un utente che proviene da tale intervallo di indirizzi IP prima di poter continuare ad accedere alla posta elettronica fino a 1:00 PM.

### <a name="coauthoring-in-office-apps"></a>Creazione di una coautorizzazione nelle app di Office

Quando più utenti collaborano nello stesso documento nello stesso momento, l'accesso dell'utente al documento potrebbe non essere immediatamente revocato da CAE in base agli eventi di revoca degli utenti o di modifica dei criteri. In questo caso, l'utente perde l'accesso completamente dopo, chiudendo il documento, chiudendo Word, Excel o PowerPoint o dopo un periodo di 10 ore.

Per ridurre questa durata, un amministratore di SharePoint può facoltativamente ridurre la durata massima delle sessioni di coautorizzazione per i documenti archiviati in SharePoint Online e OneDrive for business, [configurando i criteri del percorso di rete in SharePoint Online](/sharepoint/control-access-based-on-network-location). Una volta modificata questa configurazione, la durata massima delle sessioni di coautorizzazione verrà ridotta a 15 minuti e potrà essere modificata ulteriormente usando il comando di PowerShell per SharePoint Online "set-SPOTenant – IPAddressWACTokenLifetime"

### <a name="enable-after-a-user-is-disabled"></a>Abilita dopo la disabilitazione di un utente

Se si Abilita un utente subito dopo che è stato disabilitato. Prima di poter abilitare l'account, si verifica una certa latenza. SPO e i team avranno un ritardo di 15 minuti. Il ritardo è di 35-40 minuti per EXO.

## <a name="faqs"></a>Domande frequenti

### <a name="how-will-cae-work-with-sign-in-frequency"></a>Come funziona CAE con la frequenza di accesso?

La frequenza di accesso verrà rispettata con o senza CAE.

## <a name="next-steps"></a>Passaggi successivi

[Annuncio della valutazione di accesso continuo](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/moving-towards-real-time-policy-and-security-enforcement/ba-p/1276933)
