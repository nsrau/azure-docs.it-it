---
title: Personalizzazione dei mapping degli attributi di Azure AD | Documentazione Microsoft
description: Informazioni sui mapping degli attributi per app SaaS in Azure Active Directory e su come è possibile modificarli in base alle esigenze aziendali.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: celested
ms.custom: H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: b63118a7c5fae49edebe4ae4976a1362781ae8cf
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64712856"
---
# <a name="customizing-user-provisioning-attribute-mappings-for-saas-applications-in-azure-active-directory"></a>Personalizzazione dei mapping degli attributi del provisioning degli utenti per le applicazioni SaaS in Azure Active Directory
Microsoft Azure AD offre supporto per il provisioning utenti in applicazioni SaaS di terze parti, ad esempio Salesforce, G Suite e ad altri utenti. Se si abilita il provisioning utenti per un'applicazione SaaS di terze parti, il portale di Azure controlla i relativi valori di attributo tramite i mapping degli attributi.

È presente un set preconfigurato di attributi e mapping degli attributi tra gli oggetti utente di ogni app SaaS e gli oggetti utente di Azure AD. Alcune App gestiscono altri tipi di oggetti insieme a utenti, ad esempio i gruppi.

È possibile personalizzare i mapping degli attributi predefiniti in base alle esigenze aziendali. Pertanto, è possibile modificare eliminare mapping degli attributi esistenti o crearne di nuovi.
 
## <a name="editing-user-attribute-mappings"></a>Modifica dei mapping degli attributi utente

Seguire questi passaggi per accedere la **mapping** funzionalità del provisioning degli utenti:

1. Accedi per il [portale di Azure Active Directory](https://aad.portal.azure.com).

1. Selezionare **applicazioni aziendali** nel riquadro sinistro. Viene visualizzato un elenco di tutte le app configurate, incluse le app che sono state aggiunte dalla raccolta.

1. Selezionare un'app per caricare il riquadro di gestione di app, in cui è possibile visualizzare i report e gestire le impostazioni dell'app.

1. Selezionare **Provisioning** per gestire le impostazioni per l'app selezionata di provisioning dell'account utente.

1. Espandere **mapping** per visualizzare e modificare gli attributi utente trasmessi tra Azure AD e l'applicazione di destinazione. Se l'applicazione di destinazione lo supporta, in questa sezione consente, facoltativamente, configurare il provisioning di gruppi e account utente.

   ![Salesforce](./media/customize-application-attributes/21.png) 

1. Selezionare una **mapping** configuration per aprire i relativi **Mapping degli attributi** dello schermo. Alcuni mapping di attributi sono necessarie per una corretto funzionamento dell'applicazione SaaS. Per gli attributi obbligatori la funzionalità **Elimina** non è disponibile.

   ![Salesforce](./media/customize-application-attributes/22.png)

   In questa schermata, è possibile osservare che il **nomeutente** attributo di un oggetto gestito in Salesforce è popolato con i **userPrincipalName** valore dell'oggetto Directory attiva di Azure collegato.

1. Selezionare un oggetto esistente **Mapping attributi** per aprire il **Modifica attributo** dello schermo. Qui è possibile modificare gli attributi utente trasmessi tra Azure AD e l'applicazione di destinazione.

   ![Salesforce](./media/customize-application-attributes/23.png)


### <a name="understanding-attribute-mapping-types"></a>Informazioni sui tipi di mapping degli attributi
I mapping degli attributi permettono di controllare il modo in cui gli attribuiti vengono popolati in un'applicazione SaaS di terze parti. Sono supportati quattro diversi tipi di mapping:

* **Diretto:** l'attributo di destinazione viene popolato con il valore di un attributo dell'oggetto collegato in Azure AD.
* **Costante** : l'attributo di destinazione viene popolato con una stringa specificata.
* **Espressione:** l'attributo di destinazione viene popolato in base al risultato di un'espressione simile a uno script. 
  Per altre informazioni, vedere [Scrittura di espressioni per il mapping degli attributi in Azure Active Directory](functions-for-customizing-application-data.md).
* **Nessuno:** l'attributo di destinazione viene lasciato invariato. Tuttavia, se l'attributo di destinazione viene lasciato vuoto, viene popolato con il valore predefinito specificato.

Insieme a questi quattro tipi di base, mapping di attributi personalizzati supportano il concetto di facoltativo **predefinito** valore assegnazione. L'assegnazione del valore predefinito assicura che un attributo di destinazione viene popolato con un valore se non esiste un valore in Azure AD o nell'oggetto di destinazione. Nella maggior parte delle configurazioni questo campo viene lasciato vuoto.


### <a name="understanding-attribute-mapping-properties"></a>Informazioni sulle proprietà di mapping degli attributi

Nella sezione precedente, già sono state introdotte per la proprietà type mapping degli attributi.
Insieme a questa proprietà, i mapping degli attributi supportano anche gli attributi seguenti:

- **Attributo di origine**: attributo utente nel sistema di origine, ad esempio Azure Active Directory.
- **Attributo di destinazione**: attributo utente nel sistema di destinazione, ad esempio ServiceNow.
- **Associare gli oggetti di questo attributo** : indica se questo mapping deve essere usato per l'identificazione univoca degli utenti tra i sistemi di origine e destinazione. In genere è impostato sull'attributo userPrincipalName o mail in Azure AD, che solitamente è associato a un campo username in un'applicazione di destinazione.
- **Precedenza abbinamento**: è possibile impostare più attributi corrispondenti. Quando esistono più, questi si valutati nell'ordine definito da questo campo. Quando viene rilevata una corrispondenza la valutazione degli attributi corrispondenti termina.
- **Applica questo mapping**
    - **Sempre** : applica il mapping sia la creazione dell'utente e aggiornare azioni.
    - **Solo durante la creazione** -applica il mapping solo alle azioni di creazione dell'utente.


## <a name="editing-group-attribute-mappings"></a>Modifica dei mapping degli attributi gruppo

Un numero selezionato di applicazioni, ad esempio ServiceNow, Box e G Suite, supporta la possibilità di effettuare il provisioning di oggetti di gruppo e gli oggetti utente. Gli oggetti gruppo possono contenere proprietà di gruppo, ad esempio i nomi visualizzati e gli alias, insieme ai membri del gruppo di posta elettronica.

![ServiceNow](./media/customize-application-attributes/24.png)

Il provisioning di gruppi può essere facoltativamente abilitato o disabilitato selezionando il mapping dei gruppi in **mapping**e l'impostazione **Enabled** per l'opzione desiderata nel **Mappingdegliattributi** dello schermo.

Gli attributi di cui viene effettuato il provisioning come parte di oggetti gruppo possono essere personalizzati allo stesso modo degli oggetti utente, descritto in precedenza. 

>[!TIP]
>Il provisioning di oggetti gruppo (proprietà e membri) è un concetto distinto dall'[assegnazione di gruppi](assign-user-or-group-access-portal.md) a un'applicazione. Benché sia possibile assegnare un gruppo a un'applicazione, è possibile effettuare il provisioning solo degli oggetti utente contenuti nel gruppo. Il provisioning di oggetti gruppo completi non è necessario per usare i gruppi nelle assegnazioni.


## <a name="editing-the-list-of-supported-attributes"></a>Modifica dell'elenco degli attributi supportati

Gli attributi utente supportati per un'applicazione specifica sono preconfigurati. Gestione degli utenti dell'applicazione la maggior parte delle API non supporta l'individuazione dello schema. Pertanto, il servizio di provisioning di Azure AD non è in grado di generare dinamicamente l'elenco degli attributi supportati effettuando chiamate all'applicazione. 

Tuttavia, alcune applicazioni supportano gli attributi personalizzati e il servizio di provisioning di Azure AD possono leggere e scrivere attributi personalizzati. Per immettere le relative definizioni nel portale di Azure, selezionare la **Mostra opzioni avanzate** nella parte inferiore della casella di controllo la **Mapping degli attributi** schermata e quindi selezionare **Modifica elenco attributi per** l'app.

Alcuni sistemi e applicazioni che supportano la personalizzazione dell'elenco di attributi:

* Salesforce
* ServiceNow
* Workday
* Azure Active Directory (sono supportati gli [attributi predefiniti dell'API Graph di Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#user-entity) e le estensione della directory personalizzate)
* App che supportano [SCIM 2.0](https://tools.ietf.org/html/rfc7643), in cui devono essere aggiunti gli attributi definiti nello [schema di base](https://tools.ietf.org/html/rfc7643)

>[!NOTE]
>La modifica dell'elenco degli attributi supportati è consigliata solo agli amministratori che hanno personalizzato lo schema dei propri sistemi e applicazioni e che hanno una conoscenza diretta del modo in cui sono stati definiti gli attributi personalizzati. Questa operazione richiede a volte familiarità con le API e gli strumenti per sviluppatori forniti da un'applicazione o un sistema. 

Quando si modifica l'elenco degli attributi supportati, vengono fornite le proprietà seguenti:

* **Nome**: nome di sistema dell'attributo, definito nello schema dell'oggetto di destinazione. 
* **Tipo** -il tipo di dati archiviati dall'attributo, come definito nello schema dell'oggetto di destinazione, che può essere uno dei tipi seguenti:
   * *Binario*: l'attributo contiene dati binari.
   * *Booleano*: l'attributo contiene un valore True o False.
   * *DateTime*: l'attributo contiene una stringa di data.
   * *Integer*: l'attributo contiene un numero intero.
   * *Riferimento*: l'attributo contiene un ID che fa riferimento a un valore archiviato in un'altra tabella nell'applicazione di destinazione.
   * *String*: l'attributo contiene una stringa di testo. 
* **Chiave primaria?**: : Indica se l'attributo è definito come campo di chiave primaria nello schema dell'oggetto di destinazione.
* **Obbligatorio?** : Indica se l'attributo è necessario popolare nel sistema o applicazione di destinazione.
* **Multivalore?**: : Indica se l'attributo supporta più valori.
* **Maiuscole/minuscole esatte?**: : Indica se i valori degli attributi vengono valutati in modo distinzione maiuscole/minuscole.
* **Espressione API** -non usare, se non diversamente indicato a tale scopo, la documentazione relativa a un connettore di provisioning specifico (ad esempio Workday).
* **Attributo oggetto di riferimento** : se è un attributo di tipo riferimento, quindi questo menu consente di selezionare la tabella e l'attributo nell'applicazione di destinazione che contiene il valore associato all'attributo. Ad esempio, in presenza di un attributo denominato "Reparto" il cui valore archiviato fa riferimento a un oggetto in una tabella "Reparti" separata, sarà necessario selezionare "Reparti.Nome". Le tabelle di riferimento e i campi degli ID primari supportati per una determinata applicazione sono preconfigurati e attualmente non può essere modificati usando il portale di Azure, ma possono essere modificati utilizzando il [API Graph](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-configure-with-custom-target-attributes).

Per aggiungere un nuovo attributo, scorrere fino alla fine dell'elenco degli attributi supportati, completare i campi sopra con gli input indicati e quindi selezionare **Aggiungi attributo**. Selezionare **Salva** dopo aver aggiunto gli attributi. È quindi necessario ricaricare la **Provisioning** scheda per i nuovi attributi diventino disponibili nell'editor di mapping degli attributi.

## <a name="restoring-the-default-attributes-and-attribute-mappings"></a>Ripristino degli attributi e dei mapping degli attributi predefiniti

È necessario ricominciare da capo e reimpostare i mapping esistenti sul rispettivo stato predefinito, è possibile selezionare i **Ripristina mapping predefiniti** casella di controllo e salvare la configurazione. Questa operazione imposta tutti i mapping come se l'applicazione è stata appena aggiunta al tenant di Azure AD dalla raccolta di applicazioni. 

Se si seleziona questa opzione Forza una risincronizzazione forzata di tutti gli utenti durante l'esecuzione del servizio di provisioning. 

>[!IMPORTANT]
>Si consiglia vivamente **lo stato di Provisioning** essere impostata su **Off** prima di richiamare questa opzione.


## <a name="what-you-should-know"></a>Informazioni utili

* Microsoft Azure AD offre un'implementazione efficiente di un processo di sincronizzazione. In un ambiente inizializzato, durante un ciclo di sincronizzazione vengono elaborati solo gli oggetti che richiedono aggiornamenti. 

* L'aggiornamento dei mapping degli attributi influisce negativamente sulle prestazioni di un ciclo di sincronizzazione. Un aggiornamento della configurazione del mapping degli attributi richiede la rivalutazione di tutti gli oggetti gestiti. 

* Una procedura consigliata è di mantenere il numero di modifiche consecutive ai mapping degli attributi come minimo.


## <a name="next-steps"></a>Passaggi successivi

* [Automatizzare il provisioning e il deprovisioning utenti in app SaaS](user-provisioning.md)
* [Scrittura di espressioni per i mapping degli attributi](functions-for-customizing-application-data.md)
* [Ambito dei filtri per il Provisioning utente](define-conditional-rules-for-provisioning-user-accounts.md)
* [Uso di SCIM per abilitare il provisioning automatico di utenti e gruppi da Azure Active Directory alle applicazioni](use-scim-to-provision-users-and-groups.md)
* [Elenco di esercitazioni pratiche sulla procedura di integrazione delle applicazioni SaaS](../saas-apps/tutorial-list.md)


