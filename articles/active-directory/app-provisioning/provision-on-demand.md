---
title: Effettuare il provisioning di un utente su richiesta usando Azure Active Directory
description: Forza sincronizzazione
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: how-to
ms.date: 10/01/2020
ms.author: mimart
ms.reviewer: arvinh
ms.openlocfilehash: f7cbe9e9f81b3b71ee7da2feac2908c36f1777e5
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/01/2020
ms.locfileid: "91629013"
---
# <a name="on-demand-provisioning"></a>Provisioning su richiesta
Usare il provisioning su richiesta per eseguire il provisioning di un utente in un'applicazione in pochi secondi. Tra le altre cose, è possibile usare questa funzionalità per:

* Risolvere rapidamente i problemi di configurazione.
* Convalidare le espressioni definite.
* Filtri di ambito dei test.

## <a name="how-to-use-on-demand-provisioning"></a>Come usare il provisioning su richiesta

1. Accedere al **portale di Azure**.
1. Passare a **tutti i servizi**  >  **applicazioni aziendali**.
1. Selezionare l'applicazione, quindi passare alla pagina di configurazione del provisioning.
1. Configurare il provisioning fornendo le credenziali di amministratore.
1. Selezionare **il provisioning su richiesta**.
1. Cercare un utente per nome, cognome, nome visualizzato, nome dell'entità utente o indirizzo di posta elettronica.
   > [!NOTE]
   > Per l'app di provisioning HR cloud (giorno lavorativo/SuccessFactors ad ad/Azure AD), il valore di input è diverso. Per lo scenario di giornata lavorativa, fornire "WID" dell'utente nella giornata lavorativa. Per lo scenario SuccessFactors, immettere "personIdExternal" dell'utente in SuccessFactors. 
 
1. Selezionare **provision (provisioning** ) nella parte inferiore della pagina.

:::image type="content" source="media/provision-on-demand/on-demand-provision-user.jpg" alt-text="Screenshot che mostra l'interfaccia utente di portale di Azure per il provisioning di un utente su richiesta.":::

## <a name="understand-the-provisioning-steps"></a>Informazioni sui passaggi del provisioning

Il processo di provisioning su richiesta tenta di visualizzare i passaggi eseguiti dal servizio di provisioning quando si effettua il provisioning di un utente. Per eseguire il provisioning di un utente sono in genere disponibili cinque passaggi. Uno o più di questi passaggi, descritti nelle sezioni seguenti, verranno visualizzati durante l'esperienza di provisioning su richiesta.

### <a name="step-1-test-connection"></a>Passaggio 1: testare la connessione

Il servizio di provisioning tenta di autorizzare l'accesso all'applicazione di destinazione effettuando una richiesta di "utente test". Il servizio di provisioning prevede una risposta che indica che il servizio è autorizzato a continuare con la procedura di provisioning. Questo passaggio viene visualizzato solo quando si verifica un errore. Non viene visualizzato durante l'esperienza di provisioning su richiesta quando il passaggio ha esito positivo.

#### <a name="troubleshooting-tips"></a>Suggerimenti per la risoluzione dei problemi

* Assicurarsi di aver fornito le credenziali valide, ad esempio il token segreto e l'URL del tenant, all'applicazione di destinazione. Le credenziali necessarie variano in base all'applicazione. Per esercitazioni dettagliate sulla configurazione, vedere l' [elenco](../saas-apps/tutorial-list.md)delle esercitazioni. 
* Verificare che l'applicazione di destinazione supporti il filtro sugli attributi corrispondenti definiti nel riquadro **mapping attributi** . Potrebbe essere necessario controllare la documentazione dell'API fornita dallo sviluppatore di applicazioni per comprendere i filtri supportati.
* Per il sistema per le applicazioni di gestione delle identità tra domini (SCIM), è possibile usare uno strumento come il post. Tali strumenti consentono di garantire che l'applicazione risponda alle richieste di autorizzazione nel modo previsto per il servizio di provisioning Azure Active Directory (Azure AD). Esaminare una [richiesta di esempio](./use-scim-to-provision-users-and-groups.md#request-3).

### <a name="step-2-import-user"></a>Passaggio 2: importare un utente

Il servizio di provisioning recupera quindi l'utente dal sistema di origine. Gli attributi utente recuperati dal servizio vengono usati in un secondo momento per:

* Valutare se l'utente è nell'ambito del provisioning.
* Controllare il sistema di destinazione per un utente esistente.
* Determinare gli attributi utente da esportare nel sistema di destinazione.

#### <a name="view-details"></a>Visualizza i dettagli


La sezione **Visualizza dettagli Mostra** le proprietà dell'utente che sono state importate dal sistema di origine, ad esempio Azure ad.

#### <a name="troubleshooting-tips"></a>Suggerimenti per la risoluzione dei problemi

* L'importazione dell'utente può avere esito negativo quando manca l'attributo corrispondente nell'oggetto utente nel sistema di origine. Per risolvere l'errore, provare uno di questi approcci:

  * Aggiornare l'oggetto utente con un valore per l'attributo corrispondente.
  * Modificare l'attributo corrispondente nella configurazione del provisioning.

* Se nell'elenco importato manca un attributo previsto, assicurarsi che l'attributo abbia un valore nell'oggetto utente nel sistema di origine. Il servizio di provisioning non supporta attualmente gli attributi null di provisioning.
* Assicurarsi che la pagina **mapping attributi** della configurazione di provisioning contenga l'attributo previsto.

### <a name="step-3-determine-if-user-is-in-scope"></a>Passaggio 3: determinare se l'utente è nell'ambito

Successivamente, il servizio di provisioning determina se l'utente è nell' [ambito](./how-provisioning-works.md#scoping) per il provisioning. Il servizio considera gli aspetti seguenti:

* Indica se l'utente è assegnato all'applicazione.
* Indica se l'ambito è impostato su **sincronizzazione assegnata** o **Sincronizza tutto**.
* I filtri di ambito definiti nella configurazione del provisioning.  

#### <a name="view-details"></a>Visualizza i dettagli

La sezione **Visualizza dettagli Mostra** le condizioni di ambito valutate. È possibile che vengano visualizzate una o più delle proprietà seguenti:

* **Attivo nel sistema di origine** indica che la proprietà dell'utente è `IsActive` impostata su **true** in Azure ad.
* **Assegnato all'applicazione** indica che l'utente è assegnato all'applicazione in Azure ad.
* La **sincronizzazione dell'ambito** indica che l'impostazione dell'ambito consente tutti gli utenti e i gruppi nel tenant.
* L' **utente ha il ruolo obbligatorio** indica che l'utente ha i ruoli necessari per eseguire il provisioning nell'applicazione. 
* I **filtri di ambito** vengono visualizzati anche se sono stati definiti filtri di ambito per l'applicazione. Il filtro viene visualizzato con il formato seguente: {ambito filtro ambito} {ambito filtro ambito} {ambito filtro operatore} {ambito filtro ambito}.

#### <a name="troubleshooting-tips"></a>Suggerimenti per la risoluzione dei problemi

* Assicurarsi di aver definito un ruolo di ambito valido. Ad esempio, evitare di usare l' [operatore Greater_Than](./define-conditional-rules-for-provisioning-user-accounts.md#create-a-scoping-filter) con un valore non integer.
* Se l'utente non ha il ruolo necessario, rivedere i [Suggerimenti per il provisioning degli utenti assegnati al ruolo di accesso predefinito](./application-provisioning-config-problem-no-users-provisioned.md#provisioning-users-assigned-to-the-default-access-role).

### <a name="step-4-match-user-between-source-and-target"></a>Passaggio 4: associare l'utente tra l'origine e la destinazione

In questo passaggio il servizio tenta di trovare una corrispondenza tra l'utente recuperato nel passaggio di importazione e un utente nel sistema di destinazione.

#### <a name="view-details"></a>Visualizza i dettagli

La pagina **Visualizza dettagli** Mostra le proprietà degli utenti che corrispondono al sistema di destinazione. Le proprietà visualizzate nel riquadro contesto variano come segue:

* Se nel sistema di destinazione non sono presenti utenti corrispondenti, non verranno visualizzate proprietà.
* Se nel sistema di destinazione è presente un utente corrispondente, verranno visualizzate le proprietà di tale utente corrispondente dal sistema di destinazione.
* Se sono presenti più utenti corrispondenti, verranno visualizzate le proprietà di entrambi gli utenti.
* Se più attributi corrispondenti fanno parte dei mapping degli attributi, ogni attributo corrispondente viene valutato in sequenza e vengono visualizzati gli utenti corrispondenti per tale attributo.

#### <a name="troubleshooting-tips"></a>Suggerimenti per la risoluzione dei problemi

* Il servizio di provisioning potrebbe non essere in grado di associare un utente del sistema di origine in modo univoco a un utente nella destinazione. Risolvere questo problema assicurandosi che l'attributo corrispondente sia univoco.
* Assicurarsi che l'applicazione di destinazione supporti il filtro sull'attributo definito come attributo corrispondente.  

### <a name="step-5-perform-action"></a>Passaggio 5: eseguire l'azione

Infine, il servizio di provisioning esegue un'azione, ad esempio la creazione, l'aggiornamento, l'eliminazione o l'omissione dell'utente.

Di seguito è riportato un esempio di ciò che è possibile vedere dopo il completamento del provisioning su richiesta di un utente:

:::image type="content" source="media/provision-on-demand/success-on-demand-provision.jpg" alt-text="Screenshot che mostra l'interfaccia utente di portale di Azure per il provisioning di un utente su richiesta.":::

#### <a name="view-details"></a>Visualizza i dettagli

La sezione **Visualizza dettagli** Visualizza gli attributi che sono stati modificati nell'applicazione di destinazione. Questa visualizzazione rappresenta l'output finale dell'attività del servizio di provisioning e gli attributi che sono stati esportati. Se questo passaggio ha esito negativo, gli attributi visualizzati rappresentano gli attributi che il servizio di provisioning ha tentato di modificare.

#### <a name="troubleshooting-tips"></a>Suggerimenti per la risoluzione dei problemi

* Gli errori di esportazione delle modifiche possono variare significativamente. Controllare la [documentazione relativa ai log di provisioning](../reports-monitoring/concept-provisioning-logs.md#error-codes) per gli errori comuni.

## <a name="frequently-asked-questions"></a>Domande frequenti

* **È necessario disattivare il provisioning per usare il provisioning su richiesta?** Per le applicazioni che usano un bearer token di lunga durata o un nome utente e una password per l'autorizzazione, non sono necessari passaggi aggiuntivi. Per le applicazioni che usano OAuth per l'autorizzazione è attualmente necessario arrestare il processo di provisioning prima di usare il provisioning su richiesta. Le applicazioni come G Suite, box, area di lavoro di Facebook e slack rientrano in questa categoria. Il lavoro è in corso per supportare il provisioning su richiesta per tutte le applicazioni senza dover arrestare il provisioning dei processi.

* **Quanto tempo richiede il provisioning su richiesta?** Il provisioning su richiesta richiede in genere meno di 30 secondi.

## <a name="known-limitations"></a>Limitazioni note

Esistono attualmente alcune limitazioni note per il provisioning su richiesta. Pubblica i tuoi [suggerimenti e](https://aka.ms/appprovisioningfeaturerequest) i tuoi commenti per poter determinare meglio i miglioramenti da apportare.

> [!NOTE]
> Le limitazioni seguenti sono specifiche per la funzionalità di provisioning su richiesta. Per informazioni sull'eventuale supporto di gruppi, eliminazioni o altre funzionalità da parte di un'applicazione, vedere l'esercitazione relativa a tale applicazione.

* L'applicazione Amazon Web Services (AWS) non supporta il provisioning su richiesta. 
* Il provisioning su richiesta di gruppi e ruoli non è supportato.
* Il provisioning su richiesta supporta la disabilitazione degli utenti che non sono stati assegnati dall'applicazione. Tuttavia, non supporta la disabilitazione o l'eliminazione di utenti che sono stati disabilitati o eliminati da Azure AD. Questi utenti non verranno visualizzati quando si cerca un utente.

## <a name="next-steps"></a>Passaggi successivi

* [Risoluzione dei problemi di provisioning](./application-provisioning-config-problem.md)