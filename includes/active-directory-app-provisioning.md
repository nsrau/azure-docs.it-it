---
ms.openlocfilehash: c400856546142353a7294a03fce6bbff1c258cc0
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95563988"
---
In Azure Active Directory (Azure AD) il termine **provisioning di app** si riferisce alla creazione automatica di identità e ruoli utente nelle applicazioni cloud ([SaaS](https://azure.microsoft.com/overview/what-is-saas/)) a cui gli utenti devono accedere. Oltre a creare le identità utente, il provisioning automatico include la manutenzione e la rimozione delle identità utente quando lo stato o i ruoli cambiano. Gli scenari comuni includono il provisioning di un utente di Azure AD in applicazioni come [Dropbox](../articles/active-directory/saas-apps/dropboxforbusiness-provisioning-tutorial.md), [Salesforce](../articles/active-directory/saas-apps/salesforce-provisioning-tutorial.md), [ServiceNow](../articles/active-directory/saas-apps/servicenow-provisioning-tutorial.md) e altre ancora.

![Diagramma che illustra una panoramica del provisioning](./media/active-directory-app-provisioning/provisioning-overview.png)

Questa funzionalità consente di:

- **Automatizzare il provisioning:** creare automaticamente nuovi account nei sistemi appropriati per i membri che si uniscono al team o all'organizzazione.
- **Automatizzare il deprovisioning:** disattivare automaticamente nuovi account nei sistemi appropriati per i membri che lasciano il team o l'organizzazione.
- **Sincronizzare i dati tra i sistemi:** assicurarsi che le identità nelle app e nei sistemi siano sempre aggiornate in base alle modifiche nella directory o nel sistema di risorse umane.
- **Effettuare il provisioning dei gruppi:** effettuare il provisioning di gruppi in applicazioni che li supportano.
- **Gestire l'accesso:** monitorare e controllare gli utenti di cui è stato effettuato il provisioning nelle applicazioni.
- **Eseguire facilmente la distribuzione in scenari brownfield:** abbinare le identità esistenti tra i diversi sistemi per facilitare l'integrazione, anche quando gli utenti sono già presenti nel sistema di destinazione.
- **Usare la personalizzazione avanzata:** sfruttare i mapping personalizzabili degli attributi che definiscono quali dati degli utenti devono essere trasferiti dal sistema di origine a quello di destinazione.
- **Ricevere avvisi per gli eventi critici:** il servizio di provisioning fornisce avvisi per gli eventi critici e supporta l'integrazione di Log Analytics, in cui è possibile definire avvisi personalizzati per soddisfare le esigenze aziendali.

## <a name="benefits-of-automatic-provisioning"></a>Vantaggi del provisioning automatico

Dal momento che il numero di applicazioni usate nelle organizzazioni moderne continua a crescere, gli amministratori IT si trovano a dover gestire gli accessi su larga scala. Con l'aiuto di standard come SAML (Security Assertions Markup Language) o OIDC (Open ID Connect) possono configurare rapidamente il Single Sign-On (SSO), ma occorre anche effettuare il provisioning degli utenti nell'app per consentire loro l'accesso. Molti amministratori effettuano il provisioning creando manualmente ogni account utente o caricando file CSV ogni settimana, ma questi processi sono dispendiosi in termini di tempo e denaro, oltre a essere soggetti a errori. Per automatizzare il provisioning sono state adottate soluzioni come SAML Just-In-Time (JIT), ma le aziende necessitano anche di una soluzione per effettuare il deprovisioning degli utenti che lasciano l'organizzazione o non hanno più bisogno di accedere a determinate app per via di un cambio di ruolo.

Alcune motivazioni comuni per l'uso del provisioning automatico sono:

- Ottimizzare l'efficienza e l'accuratezza dei processi di provisioning.
- Risparmiare sui costi associati all'hosting e alla gestione di soluzioni e script di provisioning sviluppati in modo personalizzato.
- Proteggere l'organizzazione rimuovendo immediatamente dalle app SaaS principali le identità degli utenti che lasciano l'organizzazione.
- Importare facilmente un numero elevato di utenti in un'applicazione o un sistema SaaS specifico.
- Usare un unico set di criteri per determinare gli utenti di cui è stato effettuato il provisioning e che possono accedere a un'app.

Il provisioning utenti di Azure AD può aiutare a realizzare questi obiettivi. Per altre informazioni sul modo in cui le aziende clienti usano il provisioning utenti di Azure AD, vedere il [case study su ASOS](https://aka.ms/asoscasestudy). Il video seguente offre una panoramica del provisioning utenti in Azure AD:

> [!VIDEO https://www.youtube.com/embed/_ZjARPpI6NI]

## <a name="what-applications-and-systems-can-i-use-with-azure-ad-automatic-user-provisioning"></a>Quali applicazioni e sistemi è possibile usare con il provisioning utenti automatico di Azure AD?

Azure AD offre il supporto preintegrato per numerosi sistemi di risorse umane e app SaaS comuni, oltre al supporto generico per le app che implementano parti specifiche dello [standard SCIM 2.0](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/Provisioning-with-SCIM-getting-started/ba-p/880010).

* **Applicazioni preintegrate (app SaaS della raccolta)** . È possibile trovare tutte le applicazioni per cui Azure AD supporta un connettore di provisioning preintegrato nell'[elenco delle esercitazioni sulle applicazioni per il provisioning utenti](../articles/active-directory/saas-apps/tutorial-list.md). Le applicazioni preintegrate elencate nella raccolta usano in genere le API di gestione utenti basate su SCIM 2.0 per il provisioning. 

   ![Logo Salesforce](./media/active-directory-app-provisioning/gallery-app-logos.png)

   Se si vuole richiedere una nuova applicazione per il provisioning, è possibile inviare una [richiesta di integrazione dell'applicazione nella raccolta di app](../articles/active-directory/develop/v2-howto-app-gallery-listing.md). Affinché la richiesta di provisioning venga accettata, l'applicazione deve avere un endpoint conforme a SCIM. Chiedere al fornitore dell'applicazione di seguire lo standard SCIM per consentire un rapido onboarding dell'app sulla piattaforma.

* **Applicazioni che supportano SCIM 2.0**. Per informazioni su come connettere in modo generico le applicazioni che implementano le API di gestione utenti basate su SCIM 2.0, vedere [Creare un endpoint SCIM e configurare il provisioning utenti](../articles/active-directory/app-provisioning/use-scim-to-provision-users-and-groups.md).

## <a name="what-is-system-for-cross-domain-identity-management-scim"></a>Che cos'è SCIM (System for Cross-domain Identity Management)?

Per automatizzare il provisioning e il deprovisioning, le app espongono API proprietarie per utenti e gruppi. Tuttavia, chiunque abbia provato a gestire gli utenti in più app sa che ogni app tenta di eseguire le stesse semplici azioni, come la creazione o l'aggiornamento di utenti, l'aggiunta di utenti a gruppi o il deprovisioning degli utenti. Tutte queste semplici azioni vengono però implementate in modo leggermente diverso, usando percorsi di endpoint diversi, metodi diversi per specificare le informazioni sugli utenti e uno schema diverso per rappresentare ogni elemento di informazioni.

Per risolvere questi problemi, la specifica SCIM fornisce uno schema utente comune che facilita il provisioning e il deprovisioning degli utenti nelle app. SCIM sta diventando lo standard più comune per il provisioning e, in combinazione con standard di federazione come OpenID Connect o SAML, offre agli amministratori una soluzione end-to-end basata su standard per la gestione degli accessi.

Per istruzioni dettagliate sullo sviluppo di un endpoint SCIM per automatizzare il provisioning e il deprovisioning di utenti e gruppi in un'applicazione, vedere [Creare un endpoint SCIM e configurare il provisioning utenti](../articles/active-directory/app-provisioning/use-scim-to-provision-users-and-groups.md). Per le applicazioni preintegrate nella raccolta (Slack, Azure Databricks, Snowflake e altre), è possibile ignorare la documentazione per gli sviluppatori e usare le esercitazioni disponibili [qui](../articles/active-directory/saas-apps/tutorial-list.md).

## <a name="manual-vs-automatic-provisioning"></a>Provisioning manuale o automatico

Le applicazioni nella raccolta di Azure AD supportano una delle due modalità di provisioning seguenti:

* Il provisioning **manuale** viene effettuato quando non è ancora disponibile un connettore di provisioning automatico di Azure AD per l'app. Gli account utente devono essere creati manualmente, ad esempio aggiungendo gli utenti direttamente nel portale di amministrazione dell'app o caricando un foglio di calcolo con i dettagli degli account utente. Per determinare i meccanismi disponibili, consultare la documentazione o contattare lo sviluppatore dell'app.

* **Automatico** significa che è stato sviluppato un connettore di provisioning Azure AD per l'applicazione. È consigliabile seguire l'esercitazione di configurazione specifica per il provisioning dell'applicazione. Le esercitazioni per le applicazioni si trovano nell'[Elenco di esercitazioni sulla procedura di integrazione delle applicazioni SaaS con Azure Active Directory](../articles/active-directory/saas-apps/tutorial-list.md).

Nella raccolta di Azure AD le applicazioni che supportano il provisioning automatico sono identificate da un'icona **Provisioning**. Passare alla nuova esperienza di anteprima della raccolta per vedere queste icone: nel banner nella parte superiore della **pagina Aggiungi applicazione** selezionare il collegamento **Fare clic qui per provare la nuova raccolta di app migliorata**.

![Icona Provisioning nella raccolta di applicazioni](./media/active-directory-app-provisioning/browse-gallery.png)

La modalità di provisioning supportata da un'applicazione è visibile anche nella scheda **Provisioning** dopo l'aggiunta dell'applicazione in **App aziendali**.

## <a name="how-do-i-set-up-automatic-provisioning-to-an-application"></a>Come è possibile configurare il provisioning automatico in un'applicazione?

Per le applicazioni preintegrate elencate nella raccolta sono disponibili istruzioni dettagliate per la configurazione del provisioning automatico. Vedere l'[elenco delle esercitazioni per le app della raccolta integrate](../articles/active-directory/saas-apps/tutorial-list.md). Il video seguente illustra come configurare il provisioning utenti automatico per SalesForce.

> [!VIDEO https://www.youtube.com/embed/pKzyts6kfrw]

Per altre applicazioni che supportano SCIM 2.0, seguire i passaggi descritti nell'articolo [Creare un endpoint SCIM e configurare il provisioning utenti](../articles/active-directory/app-provisioning/use-scim-to-provision-users-and-groups.md).