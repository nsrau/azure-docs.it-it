---
title: Creazione di report sul provisioning automatico dell&quot;account utente di Azure Active Directory per le applicazioni SaaS | Microsoft Docs
description: Informazioni su come controllare lo stato dei processi di provisioning automatico dell&quot;account utente e risolvere i problemi di provisioning di singoli utenti.
services: active-directory
documentationcenter: 
author: asmalser-msft
writer: asmalser-msft
manager: stevenpo
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/12/2017
ms.author: asmalser-msft
ms.translationtype: Human Translation
ms.sourcegitcommit: 9568210d4df6cfcf5b89ba8154a11ad9322fa9cc
ms.openlocfilehash: 86b9a3d93745045904c6038583b9bc6ebac5667e
ms.contentlocale: it-it
ms.lasthandoff: 05/15/2017

---

# <a name="tutorial-reporting-on-automatic-user-account-provisioning"></a>Esercitazione: creazione di report sul provisioning automatico degli account utente


Azure Active Directory include un [servizio di provisioning dell'account utente](active-directory-saas-app-provisioning.md) che consente di automatizzare il provisioning del deprovisioning degli account utente nelle app SaaS e in altri sistemi, ai fini della gestione del ciclo di vita delle identità end-to-end. Azure AD supporta i connettori preintegrati di provisioning dell'utente per tutte le applicazioni e i sistemi nella sezione "In primo piano" della [raccolta delle applicazioni di Azure AD](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/azure-active-directory-apps?page=1&subcategories=featured).

In questo articolo viene descritto come controllare lo stato dei processi di provisioning in seguito alla loro configurazione e come risolvere i problemi di provisioning di singoli utenti e gruppi.

## <a name="overview"></a>Panoramica

I connettori di provisioning sono impostati e configurati principalmente tramite il [portale di gestione di Azure](https://portal.azure.com), seguendo la [documentazione presente](active-directory-saas-tutorial-list.md) per l'applicazione in cui si desidera eseguire il provisioning dell'account utente. Dopo averli configurati e averne avviata l'esecuzione, i processi di provisioning per un'applicazione possono essere segnalati tramite uno dei due metodi indicati:

* **Portale di gestione di Azure**: questo articolo descrive principalmente il recupero delle informazioni del report dal [portale di gestione di Azure](https://portal.azure.com), che consente di creare i report di riepilogo del provisioning, nonché i log di controllo dettagliati sul provisioning per una determinata applicazione.

* **API di controllo**: Azure Active Directory offre anche un'API di controllo che consente di abilitare il recupero programmatico dei log di controllo dettagliati sul provisioning. Vedere [Informazioni di riferimento sull'API di controllo di Azure Active Directory](active-directory-reporting-api-audit-reference.md) per la documentazione specifica sull'uso di questa API. Sebbene questo articolo non si concentri in particolare sull'uso dell'API, contiene informazioni dettagliate sui tipi di eventi di provisioning registrati nel log di controllo.

### <a name="definitions"></a>Definizioni

Questo articolo usa i termini seguenti, qui definiti:

* **Sistema di origine**: il repository degli utenti da cui il servizio di provisioning di Azure AD esegue la sincronizzazione. Azure Active Directory è il sistema di origine per la maggior parte dei connettori di provisioning preintegrati; esistono tuttavia alcune eccezioni, ad esempio la sincronizzazione in ingresso di Workday.

* **Sistema di destinazione**: il repository di utenti con cui il servizio di provisioning di Azure AD esegue la sincronizzazione. In genere si tratta di un'applicazione SaaS, ad esempio Salesforce, ServiceNow, Google Apps, Dropbox for Business, ma in alcuni casi può essere un sistema locale come Active Directory, ad esempio la sincronizzazione in ingresso di Workday ad Active Directory.


## <a name="getting-provisioning-reports-from-the-azure-management-portal"></a>Ottenere i report sul provisioning dal portale di gestione di Azure

Per ottenere le informazioni sui report del provisioning per una determinata applicazione, iniziare avviando il [portale di gestione di Azure](https://portal.azure.com) e passare all'applicazione aziendale per cui è configurato il provisioning. Ad esempio, se si esegue il provisioning di utenti in LinkedIn Elevate, il percorso di navigazione per i dettagli dell'applicazione è:

**Azure Active Directory > Applicazioni aziendali > All applications (Tutte le applicazioni) > LinkedIn Elevate**

A questo punto, è possibile accedere al report di riepilogo del provisioning e ai log di controllo del provisioning, entrambi descritti di seguito.


### <a name="provisioning-summary-report"></a>Report di riepilogo del provisioning

Il report di riepilogo del provisioning è visibile nella scheda **Provisioning** di una determinata applicazione. Si trova nella sezione Dettagli sincronizzazione in **Impostazioni** e contiene le informazioni seguenti:

* Il numero totale di utenti e gruppi che sono stati sincronizzati e che attualmente si trovano nell'ambito per il provisioning tra il sistema di origine e il sistema di destinazione.

* Ora dell'ultima sincronizzazione. Le sincronizzazioni in genere vengono eseguite ogni 20-40 minuti, in seguito al totale completamento di una sincronizzazione.

* Eventuale completamento di una sincronizzazione iniziale.

* Eventuale quarantena applicata a un processo di provisioning e il motivo dello stato di quarantena, ad esempio errore di comunicazione con il sistema di destinazione a causa di credenziali di amministratore non valide

Il report di riepilogo del provisioning deve essere il primo documento in cui gli amministratori devono controllare l'integrità operativa del processo di provisioning.

 ![Report di riepilogo](./media/active-directory-saas-provisioning-reporting/summary_report.PNG)

### <a name="provisioning-audit-logs"></a>Log di controllo del provisioning
Tutte le attività eseguite dal servizio di provisioning vengono registrate nei log di controllo di Azure AD, che possono essere visualizzati nella scheda **Log di controllo** della categoria **Provisioning account**. I tipi di eventi dell'attività registrati includono:

* **Eventi di importazione**: ogni volta che il servizio di provisioning di Azure AD recupera informazioni su un singolo utente o gruppo da un sistema di origine o destinazione si registra un evento di "importazione". Durante la sincronizzazione, gli utenti vengono recuperati prima dal sistema di origine, con i risultati registrati come eventi di "importazione". Gli ID corrispondenti degli utenti recuperati sono quindi sottoposti a query dal sistema di destinazione per verificarne l'esistenza: anche i risultati vengono registrati come eventi di "importazione". Questi eventi registrano tutti gli attributi utente mappati e i relativi valori osservati dal servizio di provisioning di Azure Active Directory al momento dell'evento. 

* **Eventi della regola di sincronizzazione**: questi eventi segnalano i risultati delle regole di mapping degli attributi ed eventuali filtri di ambito configurati, dopo aver importato e valutato i dati utente dai sistemi di origine e destinazione. Ad esempio, se un utente in un sistema di origine è considerato nell'ambito per il provisioning e inesistente nel sistema di destinazione, questo evento registra che l'utente verrà sottoposto a provisioning nel sistema di destinazione. 

* **Eventi di esportazione**: ogni volta che il servizio di provisioning di Azure AD scrive un oggetto account utente o gruppo in un sistema di destinazione, viene registrato un evento di "esportazione". Questi eventi registrano tutti gli attributi utente e i relativi valori scritti dal servizio di provisioning di Azure AD al momento dell'evento. Se si è verificato un errore durante la scrittura dell'oggetto account utente o gruppo nel sistema di destinazione, verrà visualizzato qui.

* **Eventi di deposito dei processi**: i depositi dei processi si verificano quando il servizio di provisioning rileva un errore durante il tentativo di eseguire un'operazione e inizia a ripetere l'operazione su un intervallo di tempo di interruzione temporanea. Ogni volta che un'operazione di provisioning viene ritirata viene registrato un evento "deposito".

Se si esaminano gli eventi di provisioning per un singolo utente si nota che in genere tali eventi si verificano nell'ordine indicato:

1. Evento di importazione: l'utente viene recuperato dal sistema di origine.

2. Evento di importazione: il sistema di destinazione viene sottoposto a query per verificare l'esistenza dell'utente recuperato.

3. Evento della regola di sincronizzazione: i dati utente dei sistemi di origine e di destinazione vengono valutati rispetto alle regole di mapping degli attributi configurati a i filtri di ambito per determinare le azioni, se possibili, da eseguire.

4. Evento di esportazione: se l'evento della regola di sincronizzazione determina l'esecuzione di un'azione, ad esempio Aggiungi, Aggiorna, Elimina, i risultati dell'azione vengono registrati in un evento di esportazione.

![Creazione di un utente test di Azure AD](./media/active-directory-saas-provisioning-reporting/audit_logs.PNG)


### <a name="looking-up-provisioning-events-for-a-specific-user"></a>Cercare un utente specifico negli eventi di provisioning

Il caso di uso più comune per i log di controllo del provisioning consiste nel verificare lo stato di provisioning di un singolo account utente. Per cercare gli ultimi eventi di provisioning per un utente specifico:

1. Andare nella sezione **Log di controllo**.

2. Dal menu **Categoria** selezionare **Provisioning account**.

3. Nel menu **Intervallo di date** selezionare l'intervallo di date in cui si desidera cercare.

4. Nella barra **Ricerca** immettere l'ID utente dell'utente che si desidera cercare. Il formato del valore dell'ID deve corrispondere all'ID selezionato dall'utente come ID primario corrispondente nella configurazione di mapping dell'attributo, ad esempio userPrincipalName o numero ID dipendente. Il valore ID richiesto sarà visibile nella colonna Target(s) (Destinazioni).

5. Premere Invio per eseguire la ricerca. Verranno restituiti prima gli eventi più recenti di provisioning.

6. Se vengono restituiti degli eventi, annotare i tipi di attività e il relativo esito. Se non viene restituito alcun risultato, significa che l'utente non esiste oppure non è stato ancora rilevato dal processo di provisioning se la sincronizzazione completa non è stata ancora portata a termine.

7. Fare clic su singoli eventi per visualizzare maggiori dettagli, comprese tutte le proprietà utente recuperate, valutate o scritte come parte dell'evento.


### <a name="tips-for-viewing-the-provisioning-audit-logs"></a>Suggerimenti per la visualizzazione dei log di controllo del provisioning

Per una migliore leggibilità nel portale di Azure selezionare il pulsante **Colonne** e scegliere queste colonne:

* **Data**: mostra la data in cui si è verificato l'evento.
* **Target(s)** (Destinazioni): mostra l'ID utente e il nome dell'app oggetto dell'evento.
* **Attività**: il tipo di attività, come descritto in precedenza.
* **Stato**: specifica se l'evento è riuscito o meno.
* **Motivo stato** : un riepilogo delle operazioni eseguite nell'evento di provisioning.


## <a name="troubleshooting"></a>Risoluzione dei problemi

Il report di riepilogo del provisioning e i log di controllo svolgono un ruolo chiave nell'aiutare gli amministratori a risolvere i diversi problemi di provisioning dell'account utente.

Per informazioni aggiuntive su come risolvere i problemi di provisioning automatico dell'utente in base agli scenari, vedere [Problemi di configurazione e provisioning degli utenti in un'applicazione](active-directory-application-provisioning-content-map.md).


## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](active-directory-enterprise-apps-manage-provisioning.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

