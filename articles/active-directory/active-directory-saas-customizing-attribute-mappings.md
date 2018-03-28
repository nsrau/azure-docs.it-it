---
title: Personalizzazione dei mapping degli attributi di Azure AD | Documentazione Microsoft
description: Informazioni sui mapping degli attributi per app SaaS in Azure Active Directory e su come è possibile modificarli in base alle esigenze aziendali.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 549e0b8c-87ce-4c9b-b487-b7bf0155dc77
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/13/2018
ms.author: markvi
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 78d971b47ffceb8d845f21a731176834f004f12c
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
# <a name="customizing-user-provisioning-attribute-mappings-for-saas-applications-in-azure-active-directory"></a>Personalizzazione dei mapping degli attributi del provisioning degli utenti per le applicazioni SaaS in Azure Active Directory
Microsoft Azure AD fornisce supporto per il provisioning degli utenti in applicazioni SaaS di terze parti, ad esempio Salesforce, Google Apps e altre. Se il provisioning degli utenti è abilitato per un'applicazione SaaS di terze parti, il portale di Azure ne controlla i valori degli attributi tramite una configurazione denominata "mapping degli attributi".

Esiste un set preconfigurato di attributi e mapping degli attributi tra gli oggetti utente di Azure AD e gli oggetti utente di ogni app SaaS. Alcune app gestiscono altri tipi di oggetti oltre agli oggetti utente, ad esempio gli oggetti gruppo. <br> 
 È possibile personalizzare i mapping degli attributi predefiniti in base alle esigenze aziendali. Questo significa che è possibile modificare o eliminare i mapping degli attributi esistenti oppure crearne di nuovi.
 
## <a name="editing-user-attribute-mappings"></a>Modifica dei mapping degli attributi utente

Nel portale di Azure AD è possibile accedere a questa funzionalità facendo clic su una configurazione **Mapping** in **Provisioning** nella sezione **Gestione** di un'**applicazione aziendale**.


![Salesforce][5] 

Se si fa clic su una configurazione **Mapping**, viene visualizzata la schermata **Mapping attributi** corrispondente. Alcuni mapping di attributi sono obbligatori per il corretto funzionamento di un'applicazione SaaS. Per gli attributi obbligatori la funzionalità **Elimina** non è disponibile.


![Salesforce][6]  

Nell'esempio riportato sopra si può notare che l'attributo **Username** di un oggetto gestito in Salesforce è popolato con il valore **userPrincipalName** dell'oggetto di Azure Active Directory collegato.

È possibile personalizzare i **Mapping degli attributi** esistenti facendo clic su un mapping. Viene visualizzata la schermata **Modifica attributo**.

![Salesforce][7]  


### <a name="understanding-attribute-mapping-types"></a>Informazioni sui tipi di mapping degli attributi
I mapping degli attributi permettono di controllare il modo in cui gli attribuiti vengono popolati in un'applicazione SaaS di terze parti. Sono supportati quattro diversi tipi di mapping:

* **Diretto:** l'attributo di destinazione viene popolato con il valore di un attributo dell'oggetto collegato in Azure AD.
* **Costante:** l'attributo di destinazione viene popolato con una stringa specificata.
* **Espressione:** l'attributo di destinazione viene popolato in base al risultato di un'espressione simile a uno script. 
  Per altre informazioni, vedere [Scrittura di espressioni per il mapping degli attributi in Azure Active Directory](active-directory-saas-writing-expressions-for-attribute-mappings.md).
* **Nessuno:** l'attributo di destinazione viene lasciato invariato. Se l'attributo di destinazione viene lasciato vuoto, viene popolato con il valore predefinito specificato.

Oltre a questi quattro tipi base di mapping, i mapping degli attributi personalizzati supportano il concetto di assegnazione di un valore **predefinito** facoltativo. L'assegnazione di un valore predefinito garantisce che un attributo di destinazione venga popolato con un valore nel caso in cui non sia presente un valore né in Azure AD, né nell'oggetto di destinazione. Nella maggior parte delle configurazioni questo campo viene lasciato vuoto.


### <a name="understanding-attribute-mapping-properties"></a>Informazioni sulle proprietà di mapping degli attributi

Nella sezione precedente è già stata presentata la proprietà del tipo di mapping degli attributi.
Oltre a questa proprietà i mapping degli attributi supportano i seguenti attributi:

- **Attributo di origine**: attributo utente nel sistema di origine, ad esempio Azure Active Directory.
- **Attributo di destinazione**: attributo utente nel sistema di destinazione, ad esempio ServiceNow.
- **Abbina gli oggetti in base a questo attributo**: specifica se questo mapping va usato per l'identificazione univoca degli utenti tra il sistema di origine e il sistema di destinazione. Questa opzione è in genere impostata sull'attributo userPrincipalName o mail in Azure AD, che a sua volta è in genere mappato su un campo username in un'applicazione di destinazione.
- **Precedenza abbinamento**: è possibile impostare più attributi corrispondenti. Se sono presenti più attributi, vengono valutati nell'ordine definito da questo campo. Quando viene rilevata una corrispondenza la valutazione degli attributi corrispondenti termina.
- **Applica questo mapping**
    - **Sempre**: applica il mapping sia all'azione di creazione che all'azione di aggiornamento dell'utente
    - **Only during creation** (Solo durante la creazione): applica il mapping solo alle azioni di creazione dell'utente


## <a name="editing-group-attribute-mappings"></a>Modifica dei mapping degli attributi gruppo

Un numero selezionato di applicazioni, tra cui ServiceNow, Box e Google Apps, supporta la possibilità di effettuare il provisioning di oggetti gruppo oltre che di oggetti utente. Oltre ai membri del gruppo, gli oggetti gruppo possono contenere proprietà di gruppo come i nomi visualizzati e gli alias di posta elettronica.

![ServiceNow][8]  

Il provisioning dei gruppi può essere facoltativamente abilitato o disabilitato selezionando il mapping dei gruppi in **Mapping** e impostando **Enabled** (Abilitato) sul valore desiderato nella schermata **Mapping attributi**.

Gli attributi di cui viene effettuato il provisioning come parte di oggetti gruppo possono essere personalizzati allo stesso modo degli oggetti utente, descritto in precedenza. 

>[!TIP]
>Il provisioning di oggetti gruppo (proprietà e membri) è un concetto distinto dall'[assegnazione di gruppi](active-directory-coreapps-assign-user-azure-portal.md) a un'applicazione. Benché sia possibile assegnare un gruppo a un'applicazione, è possibile effettuare il provisioning solo degli oggetti utente contenuti nel gruppo. Il provisioning di oggetti gruppo completi non è necessario per usare i gruppi nelle assegnazioni.


## <a name="editing-the-list-of-supported-attributes"></a>Modifica dell'elenco degli attributi supportati

Gli attributi utente supportati per un'applicazione specifica sono preconfigurati. Poiché la maggior parte delle API di gestione degli utenti non supporta l'individuazione dello schema, il servizio di provisioning di Azure AD non è in grado di generare dinamicamente l'elenco degli attributi supportati effettuando chiamate all'applicazione. 

Tuttavia, alcune applicazioni supportano gli attributi personalizzati. Perché il servizio di provisioning di Azure AD sia in grado di leggere e scrivere in attributi personalizzati, le definizioni degli attributi devono essere immesse nel portale di Azure usando la casella di controllo **Mostra opzioni avanzate** nella parte inferiore della schermata **Mapping attributi**.

Alcuni sistemi e applicazioni che supportano la personalizzazione dell'elenco di attributi:

* Salesforce
* ServiceNow
* Workday
* Azure Active Directory
* Active Directory locale (come parte del connettore di provisioning utenti Workday)
* App che supportano [SCIM 2.0](https://tools.ietf.org/html/rfc7643), in cui devono essere aggiunti gli attributi definiti nello [schema di base](https://tools.ietf.org/html/rfc7643)

>[!NOTE]
>La modifica dell'elenco degli attributi supportati è consigliata solo agli amministratori che hanno personalizzato lo schema dei propri sistemi e applicazioni e che hanno una conoscenza diretta del modo in cui sono stati definiti gli attributi personalizzati. Questa operazione richiede a volte familiarità con le API e gli strumenti per sviluppatori forniti da un'applicazione o un sistema. 

![Editor][9]  

Quando si modifica l'elenco degli attributi supportati, vengono fornite le proprietà seguenti:

* **Nome**: nome di sistema dell'attributo, definito nello schema dell'oggetto di destinazione. 
* **Tipo**: tipo di dati archiviati dall'attributo, definito nello schema dell'oggetto di destinazione. Può essere uno dei valori seguenti:
   * *Binario*: l'attributo contiene dati binari.
   * *Booleano*: l'attributo contiene un valore True o False.
   * *DateTime*: l'attributo contiene una stringa di data.
   * *Integer*: l'attributo contiene un numero intero.
   * *Riferimento*: l'attributo contiene un ID che fa riferimento a un valore archiviato in un'altra tabella nell'applicazione di destinazione.
   * *String*: l'attributo contiene una stringa di testo. 
* **Chiave primaria?**: specifica se l'attributo è o meno definito come campo di chiave primaria nello schema dell'oggetto di destinazione.
* **Obbligatorio?**: specifica se l'attributo deve o meno essere immesso nell'applicazione o nel sistema di destinazione.
* **Multivalore?**: specifica se l'attributo supporta o meno più valori.
* **Maiuscole/minuscole esatte?**: specifica se i valori degli attributi vengono o meno valutati facendo distinzione tra maiuscole e minuscole.
* **API Expression** (Espressione API): non usare questa opzione a meno che non sia specificato nella documentazione per un determinato connettore di provisioning, ad esempio Workday.
* **Attributo oggetto di riferimento**: se l'attributo è di tipo Riferimento, in questo menu è possibile selezionare la tabella e l'attributo nell'applicazione di destinazione che contiene il valore associato all'attributo. Ad esempio, in presenza di un attributo denominato "Reparto" il cui valore archiviato fa riferimento a un oggetto in una tabella "Reparti" separata, sarà necessario selezionare "Reparti.Nome". Le tabelle di riferimento e i campi degli ID primari supportati per un'applicazione specifica sono preconfigurati e attualmente non possono essere modificati tramite il portale di Azure, ma possono essere modificati usando l'[API Graph](https://developer.microsoft.com/en-us/graph/docs/api-reference/beta/resources/synchronization-configure-with-custom-target-attributes).

Per aggiungere un nuovo attributo, scorrere fino alla fine dell'elenco degli attributi supportati, completare i campi sopra con gli input indicati e quindi selezionare **Aggiungi attributo**. Selezionare **Salva** dopo aver aggiunto gli attributi. Sarà quindi necessario ricaricare la scheda **Provisioning** perché i nuovi attributi diventino disponibili nell'editor dei mapping degli attributi.

## <a name="restoring-the-default-attributes-and-attribute-mappings"></a>Ripristino degli attributi e dei mapping degli attributi predefiniti

Se è necessario ricominciare da capo e reimpostare i mapping esistenti sul rispettivo stato predefinito, è possibile selezionare la casella di controllo **Ripristina mapping predefiniti** e salvare la configurazione. Questa opzione imposterà tutti i mapping come se l'applicazione fosse stata appena aggiunta al tenant di Azure AD dalla raccolta di applicazioni. 

La selezione di questa opzione forza una nuova sincronizzazione di tutti gli utenti durante l'esecuzione del servizio di provisioning. 

>[!IMPORTANT]
>È assolutamente consigliabile impostare **Stato provisioning** su **No** prima di richiamare questa opzione.


## <a name="what-you-should-know"></a>Informazioni utili

* Microsoft Azure AD offre un'implementazione efficiente di un processo di sincronizzazione. In un ambiente inizializzato, durante un ciclo di sincronizzazione vengono elaborati solo gli oggetti che richiedono aggiornamenti. 

* L'aggiornamento dei mapping degli attributi influisce negativamente sulle prestazioni di un ciclo di sincronizzazione. Un aggiornamento della configurazione del mapping degli attributi richiede la rivalutazione di tutti gli oggetti gestiti. 

* È consigliabile ridurre al minimo il numero di modifiche consecutive ai mapping degli attributi.


## <a name="next-steps"></a>Passaggi successivi

* [Automatizzare il provisioning e il deprovisioning utenti in app SaaS](active-directory-saas-app-provisioning.md)
* [Scrittura di espressioni per i mapping degli attributi](active-directory-saas-writing-expressions-for-attribute-mappings.md)
* [Ambito dei filtri per il Provisioning utente](active-directory-saas-scoping-filters.md)
* [Uso di SCIM per abilitare il provisioning automatico di utenti e gruppi da Azure Active Directory alle applicazioni](active-directory-scim-provisioning.md)
* [Elenco di esercitazioni pratiche sulla procedura di integrazione delle applicazioni SaaS](active-directory-saas-tutorial-list.md)

<!--Image references-->
[5]: ./media/active-directory-saas-customizing-attribute-mappings/21.png
[6]: ./media/active-directory-saas-customizing-attribute-mappings/22.png
[7]: ./media/active-directory-saas-customizing-attribute-mappings/23.png
[8]: ./media/active-directory-saas-customizing-attribute-mappings/24.png
[9]: ./media/active-directory-saas-customizing-attribute-mappings/25.PNG

