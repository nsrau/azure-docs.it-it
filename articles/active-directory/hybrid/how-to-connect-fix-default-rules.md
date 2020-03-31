---
title: Come correggere le regole predefinite modificate - Azure AD Connect Documenti Microsoft
description: Informazioni su come correggere le regole predefinite modificate con Azure AD Connect.Learn how to fix modified default rules that come con Azure AD Connect.
services: active-directory
author: billmath
manager: daveba
editor: curtand
ms.reviewer: darora10
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/21/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4626e0149028a140d143fb8d0969a03b732201fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79036986"
---
# <a name="fix-modified-default-rules-in-azure-ad-connect"></a>Correggere le regole predefinite modificate in Azure AD ConnectFix modified default rules in Azure AD Connect

Azure Active Directory (Azure AD) Connect usa le regole predefinite per la sincronizzazione.  Sfortunatamente, queste regole non si applicano universalmente a tutte le organizzazioni. In base alle proprie esigenze, potrebbe essere necessario modificarli. In questo articolo vengono illustrati due esempi delle personalizzazioni più comuni e viene illustrato il modo corretto per ottenere tali personalizzazioni.

>[!NOTE] 
> La modifica delle regole predefinite esistenti per ottenere una personalizzazione necessaria non è supportata. In questo caso, impedisce l'aggiornamento di queste regole alla versione più recente nelle versioni future. Non otterrai le correzioni di bug di cui hai bisogno o le nuove funzionalità. In questo documento viene illustrato come ottenere lo stesso risultato senza modificare le regole predefinite esistenti. 

## <a name="how-to-identify-modified-default-rules"></a>Come identificare le regole predefinite modificate
A partire dalla versione 1.3.7.0 di Azure AD Connect, è facile identificare la regola predefinita modificata. Passare ad **App sul desktop**e selezionare Editor regole di **sincronizzazione**.

![Azure AD Connect, con l'editor delle regole di sincronizzazione evidenziatoAzure AD Connect, with Synchronization Rules Editor highlighted](media/how-to-connect-fix-default-rules/default1.png)

Nell'Editor, tutte le regole predefinite modificate vengono visualizzate con un'icona di avviso davanti al nome.

![Icona avviso](media/how-to-connect-fix-default-rules/default2.png)

 Viene visualizzata anche una regola disabilitata con lo stesso nome (questa è la regola predefinita standard).

![Editor regole di sincronizzazione, con la regola predefinita standard e la regola predefinita modificata](media/how-to-connect-fix-default-rules/default2a.png)

## <a name="common-customizations"></a>Personalizzazioni comuni
Di seguito sono riportate le personalizzazioni comuni alle regole predefinite:

- Modificare il flusso degli attributi
- Modificare il filtro dell'ambito
- Modificare la condizione di join

Prima di modificare le regole:

- Disabilitare l'utilità di pianificazione della sincronizzazione. Per impostazione predefinita, l' utilità di pianificazione viene eseguita ogni 30 minuti. Assicurati che non inizi mentre apprima le modifiche e la risoluzione dei problemi relativi alle nuove regole. Per disabilitare temporaneamente l'utilità di `Set-ADSyncScheduler -SyncCycleEnabled $false`pianificazione, avviare PowerShell ed eseguire .
 ![Comandi di PowerShell per disabilitare l'utilità di pianificazione della sincronizzazionePowerShell commands to disable the sync scheduler](media/how-to-connect-fix-default-rules/default3.png)

- La modifica del filtro di ambito può comportare l'eliminazione di oggetti nella directory di destinazione. Prestare attenzione prima di apportare modifiche all'ambito degli oggetti. Si consiglia di apportare modifiche a un server di gestione temporanea prima di apportare modifiche nel server attivo.
- Eseguire un'anteprima su un singolo oggetto, come indicato nella sezione Convalida regola di [sincronizzazione,](#validate-sync-rule) dopo aver aggiunto una nuova regola.
- Eseguire una sincronizzazione completa dopo l'aggiunta di una nuova regola o la modifica di qualsiasi regola di sincronizzazione personalizzata. Questa sincronizzazione applica nuove regole a tutti gli oggetti.

## <a name="change-attribute-flow"></a>Modificare il flusso degli attributi
Esistono tre scenari diversi per la modifica del flusso di attributi:There are three different scenarios for changing the attribute flow:
- Aggiunta di un nuovo attributo.
- Override del valore di un attributo esistente.
- Scelta di non sincronizzare un attributo esistente.

È possibile eseguire queste operazioni senza modificare le regole predefinite standard.

### <a name="add-a-new-attribute"></a>Aggiungere un nuovo attributo
Se si rileva che un attributo non scorre dalla directory di origine alla directory di destinazione, usare la sincronizzazione di [Azure AD Connect: estensioni della directory](how-to-connect-sync-feature-directory-extensions.md) per risolvere il problema.

Se le estensioni non funzionano, prova ad aggiungere due nuove regole di sincronizzazione, descritte nelle sezioni seguenti.


#### <a name="add-an-inbound-sync-rule"></a>Aggiungere una regola di sincronizzazione in ingressoAdd an inbound sync rule
Una regola di sincronizzazione in ingresso indica che l'origine per l'attributo è uno spazio connettore e la destinazione è il metaverse. Ad esempio, per fare in modo che un nuovo flusso di attributi da Active Directory locale ad Azure Active Directory, creare una nuova regola di sincronizzazione in ingresso. Avviare l'Editor regole di **sincronizzazione**, selezionare **In ingresso** come direzione e selezionare **Aggiungi nuova regola**. 

 ![Editor regole di sincronizzazione](media/how-to-connect-fix-default-rules/default3a.png)

Seguire la propria convenzione di denominazione per assegnare un nome alla regola. Qui, usiamo **Custom In da AD - Utente**. Ciò significa che la regola è una regola personalizzata ed è una regola in ingresso dallo spazio connettore di Active Directory al metaverse.   

 ![Creare una regola di sincronizzazione in ingresso](media/how-to-connect-fix-default-rules/default3b.png)

Fornire la propria descrizione della regola, in modo che la manutenzione futura della regola sia semplice. Ad esempio, la descrizione può essere basata su quale sia l'obiettivo della regola e perché sia necessaria.

Effettuare le selezioni per i campi **Sistema connesso**, Tipo di oggetto di **sistema connesso**e Tipo di **oggetto metaverse.**

Specificare il valore di precedenza da 0 a 99 (minore è il numero, maggiore è la precedenza). Per i campi **Tag**, **Abilita sincronizzazione password**e **Disabilitato** , utilizzare le selezioni predefinite.

Mantenere vuoto **il filtro Ambitong.** Ciò significa che la regola si applica a tutti gli oggetti uniti tra il sistema connesso ad Active Directory e il metaverse.

Mantenere vuoto **le regole di join.** Ciò significa che questa regola utilizza la condizione di join definita nella regola predefinita standard. Questo è un altro motivo per non disabilitare o eliminare la regola predefinita standard. Se non è presente alcuna condizione di join, l'attributo non scorrerà. 

Aggiungere le trasformazioni appropriate per l'attributo. È possibile assegnare una costante per creare un flusso di valori costante all'attributo di destinazione. È possibile utilizzare il mapping diretto tra l'attributo di origine o di destinazione. In alternativa, è possibile utilizzare un'espressione per l'attributo. Di seguito sono riportate le varie funzioni di [espressione](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-sync-functions-reference) che è possibile utilizzare.

#### <a name="add-an-outbound-sync-rule"></a>Aggiungere una regola di sincronizzazione in uscitaAdd an outbound sync rule
Per collegare l'attributo alla directory di destinazione, è necessario creare una regola in uscita. Ciò significa che l'origine è il metaverse e la destinazione è il sistema connesso. Per creare una regola in uscita, avviare l'Editor regole di **sincronizzazione**, modificare la **direzione** in **In uscita**e selezionare Aggiungi **nuova regola**. 

![Editor regole di sincronizzazione](media/how-to-connect-fix-default-rules/default3c.png)

Come per la regola in ingresso, è possibile utilizzare una convenzione di denominazione personalizzata per assegnare un nome alla regola. Selezionare **Sistema connesso** come tenant di Azure AD e selezionare l'oggetto di sistema connesso su cui si vuole impostare il valore dell'attributo. Impostare la precedenza da 0 a 99. 

![Creare una regola di sincronizzazione in uscita](media/how-to-connect-fix-default-rules/default3d.png)

Mantenere vuote **Filtro ambito** e Regole **di join.** Compilare la trasformazione come costante, diretta o espressione. 

Ora sai come fare in modo che un nuovo attributo per un oggetto utente scorra da Active Directory ad Azure Active Directory. È possibile utilizzare questi passaggi per eseguire il mapping di qualsiasi attributo da qualsiasi oggetto all'origine e alla destinazione. Per ulteriori informazioni, vedere Creazione di regole di [sincronizzazione personalizzate](how-to-connect-create-custom-sync-rule.md) e [Preparare il provisioning degli utenti](https://docs.microsoft.com/office365/enterprise/prepare-for-directory-synchronization).

### <a name="override-the-value-of-an-existing-attribute"></a>Eseguire l'override del valore di un attributo esistente
È possibile eseguire l'override del valore di un attributo di cui è già stato eseguito il mapping. Ad esempio, se si vuole sempre impostare un valore null su un attributo in Azure AD, è sufficiente creare solo una regola in ingresso. Rendere il valore `AuthoritativeNull`costante, , passare all'attributo di destinazione. 

>[!NOTE] 
> Utilizzare `AuthoritativeNull` invece di `Null` in questo caso. Ciò è dovuto al fatto che il valore non null sostituisce il valore null, anche se ha una precedenza inferiore (un valore numerico più alto nella regola). `AuthoritativeNull`, d'altra parte, non viene sostituito con un valore non null da altre regole. 

### <a name="dont-sync-existing-attribute"></a>Non sincronizzare gli attributi esistenti
Se si vuole escludere un attributo dalla sincronizzazione, usare la funzionalità di filtro degli attributi fornita in Azure AD Connect.If you want to exclude an attribute from syncing, use the attribute filtering feature provided in Azure AD Connect. Avviare **Azure AD Connect** dall'icona del desktop e quindi selezionare Personalizza opzioni di **sincronizzazione**.

![Opzioni attività aggiuntive di Azure AD ConnectAzure AD Connect additional tasks options](media/how-to-connect-fix-default-rules/default4.png)

 Verificare che **l'opzione Filtro attributi e app di Azure AD** sia selezionata e selezionare **Avanti**.

![Funzionalità facoltative di Azure AD ConnectAzure AD Connect optional features](media/how-to-connect-fix-default-rules/default5.png)

Deselezionare gli attributi che si desidera escludere dalla sincronizzazione.

![Attributi di Azure AD ConnectAzure AD Connect attributes](media/how-to-connect-fix-default-rules/default6a.png)

## <a name="change-scoping-filter"></a>Modificare il filtro dell'ambito
Azure AD Sync si occupa della maggior parte degli oggetti. È possibile ridurre l'ambito degli oggetti e ridurre il numero di oggetti da esportare, senza modificare le regole di sincronizzazione standard predefinite. 

Utilizzare uno dei seguenti metodi per ridurre l'ambito degli oggetti che si sta sincronizzando:

- attributo cloudFiltered
- Filtro unità organizzativa

Se si riduce l'ambito degli utenti sincronizzati, anche la sincronizzazione dell'hash delle password si interrompe per gli utenti filtrati. Se gli oggetti sono già in sincronizzazione, dopo aver ridotto l'ambito, gli oggetti filtrati vengono eliminati dalla directory di destinazione. Per questo motivo, assicurarsi di definire l'ambito con molta attenzione.

>[!IMPORTANT] 
> Non è consigliabile aumentare l'ambito degli oggetti configurati da Azure AD Connect.Increasing the scope of objects configured by Azure AD Connect is isn't recommended. In questo modo è difficile per il team di supporto Microsoft comprendere le personalizzazioni. Se è necessario aumentare l'ambito degli oggetti, modificare la regola esistente, clonarla e disabilitare la regola originale. 

### <a name="cloudfiltered-attribute"></a>attributo cloudFiltered
Non è possibile impostare questo attributo in Active Directory. Impostare il valore di questo attributo aggiungendo una nuova regola in ingresso. È quindi possibile utilizzare **Transformation** ed **Expression** per impostare questo attributo nel metaverse. L'esempio seguente mostra che non si desidera sincronizzare tutti gli utenti il cui nome di reparto inizia con **HRD** (senza distinzione tra maiuscole e minuscole):

`cloudFiltered <= IIF(Left(LCase([department]), 3) = "hrd", True, NULL)`

Per prima cosa abbiamo convertito il reparto dall'origine (Active Directory) in lettere minuscole. Quindi, utilizzando `Left` la funzione, abbiamo preso solo i `hrd`primi tre caratteri e confrontato con . Se corrisponde, il valore è `True`impostato `NULL`su , in caso contrario. Nell'impostazione del valore su null, un'altra regola con precedenza inferiore (un valore numerico più alto) può scrivere con una condizione diversa. Eseguire l'anteprima su un oggetto per convalidare la regola di sincronizzazione, come indicato nella sezione Convalida regola di [sincronizzazione.](#validate-sync-rule)

![Creare le opzioni delle regole di sincronizzazione in ingresso](media/how-to-connect-fix-default-rules/default7a.png)

### <a name="organizational-unit-filtering"></a>Filtro unità organizzativa
È possibile creare una o più unità organizzative e spostare gli oggetti che non si desidera sincronizzare con queste unità organizzative. Configurare quindi il filtro dell'unità organizzativa in Azure AD Connect.Then, configure the OU filtering in Azure AD Connect. Avviare **Azure AD Connect** dall'icona del desktop e selezionare le opzioni seguenti. È anche possibile configurare il filtro dell'unità organizzativa al momento dell'installazione di Azure AD Connect.You can also configure the OU filtering at the time of installation of Azure AD Connect. 

![Attività aggiuntive di Azure AD ConnectAzure AD Connect additional tasks](media/how-to-connect-fix-default-rules/default8.png)

Seguire la procedura guidata e deselezionare le aree utente che non si desidera sincronizzare.

![Opzioni di filtro del dominio e dell'unità organizzativa di Azure AD ConnectAzure AD Connect Domain and OU filtering options](media/how-to-connect-fix-default-rules/default9.png)

## <a name="change-join-condition"></a>Modificare la condizione di join
Usare le condizioni di join predefinite configurate da Azure AD Connect.Use the default join conditions configured by Azure AD Connect. La modifica delle condizioni di join predefinite rende difficile per il supporto Microsoft comprendere le personalizzazioni e supportare il prodotto.

## <a name="validate-sync-rule"></a>Convalida regola di sincronizzazione
È possibile convalidare la regola di sincronizzazione appena aggiunta utilizzando la funzionalità di anteprima, senza eseguire il ciclo di sincronizzazione completo. In Azure AD Connect selezionare **Servizio di sincronizzazione.**

![Azure AD Connect, con il servizio di sincronizzazione evidenziatoAzure AD Connect, with Synchronization Service highlighted](media/how-to-connect-fix-default-rules/default10.png)

Selezionare **Metaverse Search**. Selezionare l'oggetto ambito come **persona**, selezionare **Aggiungi clausola**e indicare i criteri di ricerca. Selezionare quindi **Cerca**e fare doppio clic sull'oggetto nei risultati della ricerca. Assicurarsi che i dati in Azure AD Connect siano aggiornati per l'oggetto eseguendo l'importazione e la sincronizzazione nella foresta prima di eseguire questo passaggio.

![Synchronization Service Manager.](media/how-to-connect-fix-default-rules/default11.png)

In **Proprietà oggetto metaverse**selezionare **Connettori**, selezionare l'oggetto nel connettore corrispondente (foresta) e scegliere **Proprietà...**.

![Metaverse Object Properties](media/how-to-connect-fix-default-rules/default12.png)

Selezionare **Anteprima...**

![Proprietà dell’oggetto spazio connettore](media/how-to-connect-fix-default-rules/default13a.png)

Nella finestra Anteprima, selezionare **Genera anteprima** e Importa **flusso attributi** nel riquadro sinistro.

![Anteprima](media/how-to-connect-fix-default-rules/default14.png)
 
In questo caso, si noti che la regola `cloudFiltered` appena aggiunta viene eseguita sull'oggetto e ha impostato l'attributo su true.

![Anteprima](media/how-to-connect-fix-default-rules/default15a.png)
 
Per confrontare la regola modificata con la regola predefinita, esportare entrambe le regole separatamente, come file di testo. Queste regole vengono esportate come file di script di PowerShell.These rules are exported as a PowerShell script file. È possibile confrontarli utilizzando qualsiasi strumento di confronto file (ad esempio, windiff) per visualizzare le modifiche. 
 
Si noti che nella `msExchMailboxGuid` regola modificata l'attributo viene modificato nel tipo **Espressione,** anziché in **Diretto**. Inoltre, il valore viene modificato in **NULL** e **ExecuteOnce** opzione. È possibile ignorare le differenze Identificate e Precedenza.You can ignore Identified and Precedence differences. 

![uscita dell'utensile windiff](media/how-to-connect-fix-default-rules/default17.png)
 
Per correggere le regole per ripristinarne le impostazioni predefinite, eliminare la regola modificata e abilitare la regola predefinita. Assicurati di non perdere la personalizzazione che stai cercando di ottenere. Quando si è pronti, eseguire **La sincronizzazione completa**.

## <a name="next-steps"></a>Passaggi successivi
- [Hardware e prerequisiti](how-to-connect-install-prerequisites.md) 
- [Impostazioni rapide](how-to-connect-install-express.md)
- [Impostazioni personalizzate](how-to-connect-install-custom.md)



