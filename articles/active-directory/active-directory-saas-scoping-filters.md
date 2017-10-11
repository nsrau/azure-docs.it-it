---
title: Eseguire il provisioning delle app con filtri per la definizione dell'ambito | Documentazione Microsoft
description: Informazioni su come usare i filtri per la definizione dell'ambito per evitare che venga eseguito il provisioning degli oggetti inclusi nelle app che supportano il provisioning automatico degli utenti se un oggetto non soddisfa i requisiti aziendali.
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: bcfbda74-e4d4-4859-83bc-06b104df3918
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/02/2017
ms.author: markvi
ms.custom: H1Hack27Feb2017
ms.translationtype: HT
ms.sourcegitcommit: 469246d6cb64d6aaf995ef3b7c4070f8d24372b1
ms.openlocfilehash: e40ba3d3035d5c04017a8ed558981b01fae40a13
ms.contentlocale: it-it
ms.lasthandoff: 09/27/2017

---
# <a name="attribute-based-application-provisioning-with-scoping-filters"></a>Provisioning dell'applicazione basato su attributi con filtri per la definizione dell'ambito
Questa sezione spiega come usare i filtri per la definizione dell'ambito per definire regole basate su attributi per determinare gli utenti per i quali viene eseguito il provisioning nell'applicazione.

## <a name="scoping-filter-use-cases"></a>Casi d'uso dei filtri di ambito

Un filtro di ambito consente ad Azure AD Azure di eseguire il provisioning del servizio per includere o escludere gli utenti che dispongono di un attributo che corrisponde a un valore specifico. Ad esempio, quando si esegue il provisioning di utenti di Azure AD in un'applicazione SaaS usata da un team di vendita, è possibile specificare che solo gli utenti con un attributo "Reparto" di "Vendite" devono essere inclusi nell'ambito del provisioning.

I filtri di ambito possono essere usati in modo diverso a seconda del tipo di connettore di provisioning:

* **Provisioning in uscita da Azure AD verso applicazioni SaaS**: quando Azure AD è il sistema di origine, [le assegnazioni di utenti e gruppi](active-directory-coreapps-assign-user-azure-portal.md) sono il metodo più comune per determinare quali utenti sono inclusi nell'ambito del provisioning. Queste assegnazioni vengono usate anche per l'abilitazione dell'accesso Single Sign-On e offrono un unico metodo per gestire l'accesso e il provisioning. I filtri di ambito possono essere usati facoltativamente, oltre o in sostituzione delle assegnazioni, per filtrare gli utenti in base ai valori di attributo.

>[!TIP]
> È possibile disabilitare il provisioning in base alle assegnazioni per un'applicazione aziendale impostando il menu **[Ambito](active-directory-saas-app-provisioning.md#how-do-i-set-up-automatic-provisioning-to-an-application)** nelle impostazioni di provisioning su **Sincronizza tutti gli utenti e i gruppi**. L'uso di questa opzione insieme ai filtri di ambito basati su attributi offre prestazioni più veloci rispetto all'uso di assegnazioni basate su gruppo.  

* **Provisioning in ingresso dalle applicazioni Gestione connessione ibrida ad Azure AD e Active Directory**: se un'[applicazione Gestione connessione ibrida, ad esempio Workday](active-directory-saas-workday-tutorial.md) è il sistema di origine, i filtri di ambito sono il metodo principale per determinare gli utenti che devono eseguire il provisioning dall'applicazione Gestione connessione ibrida ad Active Directory o Azure Active Directory.

Per impostazione predefinita, i connettori di provisioning di Azure AD non dispongono di filtri di ambito basati su attributi configurati. 

## <a name="scoping-filter-construction"></a>Creazione di un filtro di ambito

Un filtro di ambito è costituito da uno o più **clausole**. Le clausole determinano gli utenti che verranno restituiti dal filtro di ambito valutando gli attributi di ogni utente. Ad esempio, se una clausola richiede che l'attributo "Stato" di un utente sia uguale a "New York", nell'applicazione viene eseguito il provisioning solo degli utenti di New York. 

Una sola clausola definisce una sola condizione per un singolo valore dell'attributo. Se vengono create più clausole in un solo filtro di ambito, queste vengono valutate insieme con la logica "AND". Ciò significa che tutte le clausole devono restituire "true" affinché si esegua il provisioning di un utente.

Infine, è possibile creare più filtri di ambito per una singola applicazione. Se sono presenti più filtri di ambito, questi vengono valutati insieme con la logica "OR". Ciò significa che se tutte le clausole in un filtro di ambito configurato restituiscono "true", sull'utente è stato eseguito il provisioning.

Ogni utente o gruppo elaborato dal servizio di provisioning di Azure Active Directory viene sempre valutato singolarmente per ogni filtro di ambito.

Vedere ad esempio il filtro di ambito seguente:

![Filtro per la definizione dell’ambito](./media/active-directory-saas-scoping-filters/scoping-filter.PNG) 

In base a questo filtro per la definizione dell'ambito, affinché sia possibile eseguire il provisioning degli utenti nell'applicazione, questi devono soddisfare i criteri seguenti:

1. Devono trovarsi a New York
2. Devono lavorare nel reparto Engineering.
3. L'ID del dipendente aziendale deve essere compreso tra 1.000.000 e 2.000.000
4. La posizione non deve essere null o vuota

## <a name="creating-scoping-filters"></a>Creare i filtri di ambito
I filtri di ambito sono configurati come parte dei mapping degli attributi per ogni connettore di provisioning dell'utente di Azure AD. La procedura seguente presuppone che l'utente abbia già configurato il provisioning automatico per [una delle applicazioni supportate](active-directory-saas-tutorial-list.md) e che a questa vengano aggiunti i filtri di ambito.

### <a name="to-create-a-scoping-filter"></a>Per creare un filtro di ambito:
1. Nel [portale di Azure](https://portal.azure.com) passare alla sezione **Azure Active Directory > Applicazioni aziendali > Tutte le applicazioni**.
2. Selezionare l'applicazione per cui è stato configurato il provisioning automatico. Esempio: "ServiceNow"
3. Selezionare la scheda **Provisioning**.
4. Nella sezione **Mapping** selezionare il mapping per cui si desidera configurare un filtro di ambito. Ad esempio "Synchronize Azure Active Directory Users to ServiceNow" (Sincronizza utenti di Azure Active Directory in ServiceNow)
5. Selezionare il menu **Ambito dell'oggetto di origine**.
6. Selezionare **Aggiungi filtro di ambito**.
7. Definire una clausola selezionando un'origine **Nome attributo**, un **Operatore** e un **Valore dell'attributo** da confrontare. Di seguito sono elencati gli operatori supportati:
   * **È UGUALE A**: la clausola restituisce "vero" se l'attributo valutato corrisponde esattamente al valore della stringa di input, che distingue tra maiuscole e minuscole.
   * **DIVERSO DA**: la clausola restituisce "vero" se l'attributo valutato non corrisponde al valore della stringa di input, che distingue tra maiuscole e minuscole.
   * **IS TRUE** (È VERO): la clausola restituisce "vero" se l'attributo valutato contiene un valore booleano true.
   * **IS FALSE** (È FALSO): la clausola restituisce "vero" se l'attributo valutato contiene un valore booleano false.
   * **IS NULL** (È NULL): la clausola restituisce "vero" se l'attributo valutato è vuoto.
   * **IS NOT NULL** (NON È NULL): la clausola restituisce "vero" se l'attributo valutato non è vuoto.
   * **REGEX MATCH** (CORRISPONDENZA REGEX): la clausola restituisce "vero" se l'attributo valutato corrisponde a un modello di espressione regolare. Esempio: ([1-9][0-9]) corrisponde a qualsiasi numero compreso tra 10 e 99
   * **NOT REGEX MATCH** (NESSUNA CORRISPONDENZA REGEX): la clausola restituisce "vero" se l'attributo valutato non corrisponde a un modello di espressione regolare.
8. Selezionare **Aggiungi nuova clausola di ambito**.
9. Facoltativamente, ripetere i passaggi 7 e 8 per aggiungere altre clausole di ambito.
10. In **Titolo filtro di ambito** aggiungere un nome per il filtro di ambito.
11. Selezionare **OK**.
12. Selezionare di nuovo **OK** nella schermata Filtri di ambito o ripetere i passaggi da 6 a 11 per aggiungere un altro filtro di ambito.
13. Selezionare **Salva** nella schermata Mapping attributi. 

>[!IMPORTANT] 
> Il salvataggio di un nuovo filtro di ambito attiva una nuova sincronizzazione completa per l'applicazione, in cui tutti gli utenti del sistema di origine vengono nuovamente valutati rispetto al nuovo filtro di ambito. Se un utente nell'applicazione è stato precedentemente nell'ambito per il provisioning, ma non rientra nell'ambito, l'account verrà disabilitato o verrà eseguito il deprovisioning nell'applicazione.


## <a name="related-articles"></a>Articoli correlati
* [Indice di articoli per la gestione di applicazioni in Azure Active Directory](active-directory-apps-index.md)
* [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS](active-directory-saas-app-provisioning.md)
* [Personalizzazione dei mapping degli attributi per il Provisioning dell’utente](active-directory-saas-customizing-attribute-mappings.md)
* [Scrittura di espressioni per i mapping degli attributi](active-directory-saas-writing-expressions-for-attribute-mappings.md)
* [Notifiche relative al provisioning dell'account](active-directory-saas-account-provisioning-notifications.md)
* [Uso di SCIM per abilitare il provisioning automatico di utenti e gruppi da Azure Active Directory alle applicazioni](active-directory-scim-provisioning.md)
* [Elenco di esercitazioni pratiche sulla procedura di integrazione delle applicazioni SaaS](active-directory-saas-tutorial-list.md)


