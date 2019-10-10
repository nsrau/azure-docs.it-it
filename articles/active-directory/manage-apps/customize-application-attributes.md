---
title: Personalizzazione dei mapping degli attributi di Azure AD | Documentazione Microsoft
description: Informazioni sui mapping degli attributi per app SaaS in Azure Active Directory e su come è possibile modificarli in base alle esigenze aziendali.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: ef3d6a47986056925f9964638c9c7192341ca5f9
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72241003"
---
# <a name="customizing-user-provisioning-attribute-mappings-for-saas-applications-in-azure-active-directory"></a>Personalizzazione dei mapping degli attributi del provisioning degli utenti per le applicazioni SaaS in Azure Active Directory

Microsoft Azure AD fornisce il supporto per il provisioning degli utenti in applicazioni SaaS di terze parti, ad esempio Salesforce, G Suite e altri. Se si Abilita il provisioning utenti per un'applicazione SaaS di terze parti, il portale di Azure controlla i valori degli attributi tramite mapping degli attributi.

Esiste un set preconfigurato di attributi e mapping di attributi tra Azure AD oggetti utente e gli oggetti utente di ogni app SaaS. Alcune app gestiscono altri tipi di oggetti insieme agli utenti, ad esempio i gruppi.

È possibile personalizzare i mapping degli attributi predefiniti in base alle esigenze aziendali. È quindi possibile modificare o eliminare i mapping degli attributi esistenti o creare nuovi mapping degli attributi.

## <a name="editing-user-attribute-mappings"></a>Modifica dei mapping degli attributi utente

Per accedere alla funzionalità **mapping** del provisioning utenti, attenersi alla procedura seguente:

1. Accedere al portale di [Azure Active Directory](https://aad.portal.azure.com).
1. Selezionare **applicazioni aziendali** dal riquadro sinistro. Viene visualizzato un elenco di tutte le app configurate, incluse le app aggiunte dalla raccolta.
1. Selezionare un'app per caricare il riquadro di gestione delle app, in cui è possibile visualizzare i report e gestire le impostazioni dell'app.
1. Selezionare il **provisioning** per gestire le impostazioni di provisioning degli account utente per l'app selezionata.
1. Espandere **mapping** per visualizzare e modificare gli attributi utente che scorrono tra Azure ad e l'applicazione di destinazione. Se l'applicazione di destinazione la supporta, questa sezione consente di configurare facoltativamente il provisioning di gruppi e account utente.

   ![Usare i mapping per visualizzare e modificare gli attributi utente](./media/customize-application-attributes/21.png)

1. Selezionare una configurazione di **mapping** per aprire la schermata **mapping attributi** correlati. Alcuni mapping di attributi sono necessari per il corretto funzionamento di un'applicazione SaaS. Per gli attributi obbligatori la funzionalità **Elimina** non è disponibile.

   ![Usare il mapping degli attributi per configurare i mapping degli attributi per le app](./media/customize-application-attributes/22.png)

   In questa schermata è possibile vedere che l'attributo **username** di un oggetto gestito in Salesforce è popolato con il valore **userPrincipalName** dell'oggetto Azure Active Directory collegato.

1. Selezionare un **mapping di attributi** esistente per aprire la schermata **modifica attributo** . Qui è possibile modificare gli attributi utente che scorrono tra Azure AD e l'applicazione di destinazione.

   ![Usare modifica attributo per modificare gli attributi utente](./media/customize-application-attributes/23.png)

### <a name="understanding-attribute-mapping-types"></a>Informazioni sui tipi di mapping degli attributi

I mapping degli attributi permettono di controllare il modo in cui gli attribuiti vengono popolati in un'applicazione SaaS di terze parti.
Sono supportati quattro diversi tipi di mapping:

- **Diretto:** l'attributo di destinazione viene popolato con il valore di un attributo dell'oggetto collegato in Azure AD.
- **Costante** : l'attributo di destinazione viene popolato con una stringa specifica specificata.
- **Espressione:** l'attributo di destinazione viene popolato in base al risultato di un'espressione simile a uno script.
  Per altre informazioni, vedere [Scrittura di espressioni per il mapping degli attributi in Azure Active Directory](functions-for-customizing-application-data.md).
- **Nessuno:** l'attributo di destinazione viene lasciato invariato. Tuttavia, se l'attributo di destinazione è sempre vuoto, viene popolato con il valore predefinito specificato.

Insieme a questi quattro tipi di base, i mapping di attributi personalizzati supportano il concetto di assegnazione di un valore **predefinito** facoltativo. L'assegnazione di valori predefiniti garantisce che un attributo di destinazione venga popolato con un valore se non è presente un valore in Azure AD o nell'oggetto di destinazione. Nella maggior parte delle configurazioni questo campo viene lasciato vuoto.

### <a name="understanding-attribute-mapping-properties"></a>Informazioni sulle proprietà di mapping degli attributi

Nella sezione precedente è già stata introdotta la proprietà tipo mapping attributi.
Insieme a questa proprietà, i mapping degli attributi supportano anche gli attributi seguenti:

- **Attributo di origine**: attributo utente nel sistema di origine, ad esempio Azure Active Directory.
- **Attributo di destinazione**: attributo utente nel sistema di destinazione, ad esempio ServiceNow.
- Trova la **corrispondenza con gli oggetti che usano questo attributo** : indica se questo mapping deve essere usato per identificare in modo univoco gli utenti tra i sistemi di origine e di destinazione. Viene in genere impostato sull'attributo userPrincipalName o mail in Azure AD, che in genere viene mappato a un campo username in un'applicazione di destinazione.
- **Precedenza abbinamento**: è possibile impostare più attributi corrispondenti. Quando sono presenti più, vengono valutati nell'ordine definito da questo campo. Quando viene rilevata una corrispondenza la valutazione degli attributi corrispondenti termina.
- **Applica questo mapping**
  - **Sempre** : applica il mapping sia alle azioni di creazione che di aggiornamento dell'utente.
  - **Solo durante la creazione** : applicare questo mapping solo alle azioni di creazione dell'utente.

## <a name="editing-group-attribute-mappings"></a>Modifica dei mapping degli attributi gruppo

Un numero selezionato di applicazioni, ad esempio ServiceNow, box e G Suite, supporta la possibilità di effettuare il provisioning di oggetti gruppo e oggetti utente. Gli oggetti Group possono contenere proprietà del gruppo, ad esempio i nomi visualizzati e gli alias di posta elettronica, insieme ai membri del gruppo.

![Esempio Mostra ServiceNow con gli oggetti gruppo e utente di cui è stato effettuato il provisioning](./media/customize-application-attributes/24.png)

Facoltativamente, è possibile abilitare o disabilitare il provisioning dei gruppi selezionando il mapping del gruppo in **mapping**e impostando **abilitato** sull'opzione desiderata nella schermata **mapping attributi** .

Gli attributi di cui viene effettuato il provisioning come parte di oggetti gruppo possono essere personalizzati allo stesso modo degli oggetti utente, descritto in precedenza. 

> [!TIP]
> Il provisioning di oggetti gruppo (proprietà e membri) è un concetto distinto dall'[assegnazione di gruppi](assign-user-or-group-access-portal.md) a un'applicazione. Benché sia possibile assegnare un gruppo a un'applicazione, è possibile effettuare il provisioning solo degli oggetti utente contenuti nel gruppo. Il provisioning di oggetti gruppo completi non è necessario per usare i gruppi nelle assegnazioni.

## <a name="editing-the-list-of-supported-attributes"></a>Modifica dell'elenco degli attributi supportati

Gli attributi utente supportati per un'applicazione specifica sono preconfigurati. La maggior parte delle API di gestione utente dell'applicazione non supporta l'individuazione dello schema. Il servizio di provisioning Azure AD non è quindi in grado di generare dinamicamente l'elenco degli attributi supportati effettuando chiamate all'applicazione.

Tuttavia, alcune applicazioni supportano attributi personalizzati e il servizio di provisioning Azure AD può leggere e scrivere in attributi personalizzati. Per immettere le definizioni nella portale di Azure, selezionare la casella di controllo **Mostra opzioni avanzate** nella parte inferiore della schermata **mapping attributi** , quindi selezionare **modifica elenco attributi per** l'app.

Alcuni sistemi e applicazioni che supportano la personalizzazione dell'elenco di attributi:

- Salesforce
- ServiceNow
- Workday
- Azure Active Directory (sono supportati gli [attributi predefiniti dell'API Graph di Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#user-entity) e le estensione della directory personalizzate)
- App che supportano [SCIM 2.0](https://tools.ietf.org/html/rfc7643), in cui devono essere aggiunti gli attributi definiti nello [schema di base](https://tools.ietf.org/html/rfc7643)

> [!NOTE]
> La modifica dell'elenco degli attributi supportati è consigliata solo agli amministratori che hanno personalizzato lo schema dei propri sistemi e applicazioni e che hanno una conoscenza diretta del modo in cui sono stati definiti gli attributi personalizzati. Questa operazione richiede a volte familiarità con le API e gli strumenti per sviluppatori forniti da un'applicazione o un sistema.

Quando si modifica l'elenco degli attributi supportati, vengono fornite le proprietà seguenti:

- **Nome**: nome di sistema dell'attributo, definito nello schema dell'oggetto di destinazione.
- **Tipo** : tipo di dati archiviato dall'attributo, come definito nello schema dell'oggetto di destinazione, che può essere uno dei tipi seguenti:
  - *Binario*: l'attributo contiene dati binari.
  - *Booleano*: l'attributo contiene un valore True o False.
  - *DateTime*: l'attributo contiene una stringa di data.
  - *Integer*: l'attributo contiene un numero intero.
  - *Riferimento*: l'attributo contiene un ID che fa riferimento a un valore archiviato in un'altra tabella nell'applicazione di destinazione.
  - *String*: l'attributo contiene una stringa di testo.
- **Chiave primaria?** : -Indica se l'attributo è definito come campo di chiave primaria nello schema dell'oggetto di destinazione.
- **Obbligatorio?** -Indica se è necessario che l'attributo venga popolato nell'applicazione o nel sistema di destinazione.
- **Multivalore?** : Indica se l'attributo supporta più valori.
- **Maiuscole/minuscole esatte?** : Indica se i valori degli attributi vengono valutati con distinzione tra maiuscole e minuscole.
- **Espressione API** : non usare, a meno che non sia stato richiesto dalla documentazione per un connettore di provisioning specifico, ad esempio la giornata lavorativa.
- **Attributo oggetto a cui si fa riferimento** : se è un attributo di tipo riferimento, questo menu consente di selezionare la tabella e l'attributo nell'applicazione di destinazione che contiene il valore associato all'attributo. Ad esempio, in presenza di un attributo denominato "Reparto" il cui valore archiviato fa riferimento a un oggetto in una tabella "Reparti" separata, sarà necessario selezionare "Reparti.Nome". Le tabelle di riferimento e i campi ID primari supportati per un'applicazione specifica sono preconfigurati e attualmente non possono essere modificati usando il portale di Azure, ma possono essere modificati usando il [API Graph](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-configure-with-custom-target-attributes).

Per aggiungere un nuovo attributo, scorrere fino alla fine dell'elenco degli attributi supportati, completare i campi sopra con gli input indicati e quindi selezionare **Aggiungi attributo**. Selezionare **Salva** dopo aver aggiunto gli attributi. Sarà quindi necessario ricaricare la scheda **provisioning** per rendere disponibili i nuovi attributi nell'editor mapping attributi.

## <a name="restoring-the-default-attributes-and-attribute-mappings"></a>Ripristino degli attributi e dei mapping degli attributi predefiniti

Se è necessario ricominciare e reimpostare i mapping esistenti sullo stato predefinito, è possibile selezionare la casella di controllo **Ripristina mapping predefiniti** e salvare la configurazione. In questo modo, tutti i mapping vengono impostati come se l'applicazione venisse appena aggiunta al tenant di Azure AD dalla raccolta di applicazioni.

Se si seleziona questa opzione, verrà forzata una risincronizzazione di tutti gli utenti durante l'esecuzione del servizio di provisioning.

> [!IMPORTANT]
> È consigliabile impostare **lo stato di provisioning** su **disattivato** prima di richiamare questa opzione.

## <a name="what-you-should-know"></a>Informazioni utili

- Microsoft Azure AD offre un'implementazione efficiente di un processo di sincronizzazione. In un ambiente inizializzato, durante un ciclo di sincronizzazione vengono elaborati solo gli oggetti che richiedono aggiornamenti.
- L'aggiornamento dei mapping degli attributi influisce negativamente sulle prestazioni di un ciclo di sincronizzazione. Un aggiornamento della configurazione del mapping degli attributi richiede la rivalutazione di tutti gli oggetti gestiti.
- Una procedura consigliata consiste nel limitare al minimo il numero di modifiche consecutive ai mapping degli attributi.
- L'aggiunta di un attributo Photo per eseguire il provisioning in un'app non è attualmente supportata perché non è possibile specificare il formato per sincronizzare la foto. È possibile richiedere la funzionalità nella [voce utente](https://feedback.azure.com/forums/169401-azure-active-directory)
- L'attributo IsSoftDeleted fa spesso parte dei mapping predefiniti per un'applicazione. IsSoftdeleted può essere true in uno dei quattro scenari (l'utente è esterno all'ambito perché non è stato assegnato dall'applicazione, l'utente è esterno all'ambito a causa del mancato rispetto di un filtro di ambito, l'utente è stato eliminato temporaneamente in Azure AD o la proprietà AccountEnabled è impostata su false  utente). 
- Il servizio di provisioning Azure AD non supporta il provisioning di valori null

## <a name="next-steps"></a>Passaggi successivi

- [Automatizzare il provisioning e il deprovisioning utenti in app SaaS](user-provisioning.md)
- [Scrittura di espressioni per i mapping degli attributi](functions-for-customizing-application-data.md)
- [Ambito dei filtri per il Provisioning utente](define-conditional-rules-for-provisioning-user-accounts.md)
- [Uso di SCIM per abilitare il provisioning automatico di utenti e gruppi da Azure Active Directory alle applicazioni](use-scim-to-provision-users-and-groups.md)
- [Elenco di esercitazioni pratiche sulla procedura di integrazione delle applicazioni SaaS](../saas-apps/tutorial-list.md)
