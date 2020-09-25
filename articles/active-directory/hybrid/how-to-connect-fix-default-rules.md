---
title: Come correggere le regole predefinite modificate-Azure AD Connect | Microsoft Docs
description: Informazioni su come correggere le regole predefinite modificate fornite con Azure AD Connect.
services: active-directory
author: billmath
manager: daveba
editor: curtand
ms.reviewer: darora10
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/21/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a0fc1bc3158e04c9b1f677af7ef2375ac3ed2ce7
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91320048"
---
# <a name="fix-modified-default-rules-in-azure-ad-connect"></a>Correzione delle regole predefinite modificate in Azure AD Connect

Azure Active Directory (Azure AD) Connect usa le regole predefinite per la sincronizzazione.  Sfortunatamente, queste regole non vengono applicate in modo universale a tutte le organizzazioni. In base ai requisiti, potrebbe essere necessario modificarli. Questo articolo illustra due esempi delle personalizzazioni più comuni e illustra il modo corretto per realizzare queste personalizzazioni.

>[!NOTE] 
> La modifica delle regole predefinite esistenti per ottenere una personalizzazione necessaria non è supportata. In tal caso, impedisce l'aggiornamento di queste regole alla versione più recente nelle versioni future. Non è possibile ottenere le correzioni di bug necessarie o nuove funzionalità. In questo documento viene illustrato come ottenere lo stesso risultato senza modificare le regole predefinite esistenti. 

## <a name="how-to-identify-modified-default-rules"></a>Come identificare le regole predefinite modificate
A partire dalla versione 1.3.7.0 di Azure AD Connect, è facile identificare la regola predefinita modificata. Passare ad **app sul desktop**e selezionare **Editor regole di sincronizzazione**.

![Azure AD Connect, con l'editor delle regole di sincronizzazione evidenziato](media/how-to-connect-fix-default-rules/default1.png)

Nell'editor, qualsiasi regola predefinita modificata viene visualizzata con un'icona di avviso davanti al nome.

![Icona avviso](media/how-to-connect-fix-default-rules/default2.png)

 Viene visualizzata anche una regola disabilitata con lo stesso nome, ovvero la regola predefinita standard.

![Editor delle regole di sincronizzazione, che mostra la regola predefinita standard e la regola predefinita modificata](media/how-to-connect-fix-default-rules/default2a.png)

## <a name="common-customizations"></a>Personalizzazioni comuni
Di seguito sono riportate le personalizzazioni comuni alle regole predefinite:

- Modificare il flusso di attributi
- Modifica filtro ambito
- Modificare la condizione di join

Prima di modificare le regole:

- Disabilitare l'utilità di pianificazione della sincronizzazione. Per impostazione predefinita, l' utilità di pianificazione viene eseguita ogni 30 minuti. Assicurarsi che non venga avviata mentre si apportano modifiche e si risolvono i problemi delle nuove regole. Per disabilitare temporaneamente l'utilità di pianificazione, avviare PowerShell ed eseguire `Set-ADSyncScheduler -SyncCycleEnabled $false` .
 ![Comandi di PowerShell per disabilitare l'utilità di pianificazione della sincronizzazione](media/how-to-connect-fix-default-rules/default3.png)

- La modifica del filtro di ambito può causare l'eliminazione di oggetti nella directory di destinazione. Prestare attenzione prima di apportare qualsiasi modifica nell'ambito degli oggetti. Si consiglia di apportare modifiche a un server di staging prima di apportare modifiche al server attivo.
- Eseguire un'anteprima su un singolo oggetto, come indicato nella sezione [Validate Sync Rule](#validate-sync-rule) , dopo l'aggiunta di una nuova regola.
- Eseguire una sincronizzazione completa dopo l'aggiunta di una nuova regola o la modifica di una regola di sincronizzazione personalizzata. Questa sincronizzazione applica le nuove regole a tutti gli oggetti.

## <a name="change-attribute-flow"></a>Modificare il flusso di attributi
Esistono tre diversi scenari per modificare il flusso di attributi:
- Aggiunta di un nuovo attributo.
- Override del valore di un attributo esistente.
- Scelta di non sincronizzare un attributo esistente.

È possibile eseguire queste operazioni senza modificare le regole predefinite standard.

### <a name="add-a-new-attribute"></a>Aggiungere un nuovo attributo
Se si rileva che un attributo non viene propagato dalla directory di origine alla directory di destinazione, usare le [estensioni della directory Azure ad Connect Sync:](how-to-connect-sync-feature-directory-extensions.md) per risolvere il problema.

Se le estensioni non funzionano, provare ad aggiungere due nuove regole di sincronizzazione, descritte nelle sezioni riportate di seguito.


#### <a name="add-an-inbound-sync-rule"></a>Aggiungere una regola di sincronizzazione in ingresso
Una regola di sincronizzazione in ingresso indica che l'origine dell'attributo è uno spazio connettore e la destinazione è il metaverse. Ad esempio, per avere un nuovo flusso di attributi da Active Directory locali a Azure Active Directory, creare una nuova regola di sincronizzazione in ingresso. Avviare l' **Editor delle regole di sincronizzazione**, selezionare in **ingresso** come direzione e selezionare **Aggiungi nuova regola**. 

 ![Screenshot che mostra "Editor delle regole di sincronizzazione" con "in ingresso" e "Aggiungi nuova regola" selezionati.](media/how-to-connect-fix-default-rules/default3a.png)

Seguire una convenzione di denominazione personalizzata per assegnare un nome alla regola. Qui viene usato **Custom in from ad-User**. Ciò significa che la regola è una regola personalizzata ed è una regola in ingresso dallo spazio del connettore Active Directory al metaverse.   

 ![Creare una regola di sincronizzazione in ingresso](media/how-to-connect-fix-default-rules/default3b.png)

Fornire una descrizione personalizzata della regola, in modo da semplificare la manutenzione futura della regola. La descrizione, ad esempio, può essere basata sull'obiettivo della regola e sul motivo per cui è necessaria.

Effettuare le selezioni per il **sistema connesso**, il **tipo di oggetto di sistema connesso**e i campi del **tipo di oggetto metaverse** .

Specificare il valore di precedenza compreso tra 0 e 99 (più basso è il numero, più alta è la precedenza). Per il **tag**, **abilitare la sincronizzazione password**e i campi **disabilitati** , usare le selezioni predefinite.

Mantieni **filtro ambito** vuoto. Ciò significa che la regola si applica a tutti gli oggetti Uniti tra il sistema Active Directory connesso e il metaverse.

Mantiene vuote **le regole di join** . Questo significa che questa regola utilizza la condizione di join definita nella regola predefinita standard. Questo è un altro motivo per non disabilitare o eliminare la regola predefinita standard. Se non è presente alcuna condizione di join, l'attributo non verrà propagata. 

Aggiungere le trasformazioni appropriate per l'attributo. È possibile assegnare una costante per far passare un valore costante all'attributo di destinazione. È possibile utilizzare il mapping diretto tra l'attributo di origine o di destinazione. In alternativa, è possibile usare un'espressione per l'attributo. Di seguito sono riportate varie [funzioni di espressione](./reference-connect-sync-functions-reference.md) che è possibile usare.

#### <a name="add-an-outbound-sync-rule"></a>Aggiungere una regola di sincronizzazione in uscita
Per collegare l'attributo alla directory di destinazione, è necessario creare una regola in uscita. Ciò significa che l'origine è il metaverse e la destinazione è il sistema connesso. Per creare una regola in uscita, avviare l' **Editor delle regole di sincronizzazione**, modificare la **direzione** in **uscita**e selezionare **Aggiungi nuova regola**. 

![Editor regole di sincronizzazione](media/how-to-connect-fix-default-rules/default3c.png)

Come per la regola in ingresso, è possibile usare una convenzione di denominazione personalizzata per assegnare un nome alla regola. Selezionare il **sistema connesso** come tenant Azure ad e selezionare l'oggetto di sistema connesso al quale si desidera impostare il valore dell'attributo. Impostare la precedenza da 0 a 99. 

![Creare una regola di sincronizzazione in uscita](media/how-to-connect-fix-default-rules/default3d.png)

Mantieni il **filtro di ambito** e **le regole di join** vuote. Compilare la trasformazione come costante, diretta o espressione. 

A questo punto è possibile creare un nuovo attributo per un flusso di oggetti utente da Active Directory a Azure Active Directory. È possibile usare questi passaggi per eseguire il mapping di qualsiasi attributo da qualsiasi oggetto a origine e destinazione. Per altre informazioni, vedere [creazione di regole di sincronizzazione personalizzate](how-to-connect-create-custom-sync-rule.md) e [preparare il provisioning degli utenti](/office365/enterprise/prepare-for-directory-synchronization).

### <a name="override-the-value-of-an-existing-attribute"></a>Eseguire l'override del valore di un attributo esistente
Potrebbe essere necessario eseguire l'override del valore di un attributo di cui è già stato eseguito il mapping. Se, ad esempio, si desidera impostare sempre un valore null su un attributo in Azure AD, è sufficiente creare solo una regola in ingresso. Fare in modo che il valore della costante, `AuthoritativeNull` , fluisca all'attributo di destinazione. 

>[!NOTE] 
> Usare `AuthoritativeNull` anziché `Null` in questo caso. Questo è dovuto al fatto che il valore non null sostituisce il valore null, anche se ha una precedenza più bassa (un valore di numero più alto nella regola). `AuthoritativeNull`d'altra parte, non viene sostituito con un valore non null da altre regole. 

### <a name="dont-sync-existing-attribute"></a>Non sincronizzare l'attributo esistente
Se si desidera escludere un attributo dalla sincronizzazione, utilizzare la funzionalità di filtro degli attributi disponibile in Azure AD Connect. Avviare **Azure ad Connect** dall'icona del desktop, quindi selezionare **Personalizza opzioni di sincronizzazione**.

![Azure AD Connect opzioni aggiuntive per le attività](media/how-to-connect-fix-default-rules/default4.png)

 Assicurarsi che sia selezionata l'opzione **Azure ad filtro attributi e app** , quindi fare clic su **Avanti**.

![Azure AD Connect funzionalità facoltative](media/how-to-connect-fix-default-rules/default5.png)

Cancellare gli attributi che si desidera escludere dalla sincronizzazione.

![Attributi di Azure AD Connect](media/how-to-connect-fix-default-rules/default6a.png)

## <a name="change-scoping-filter"></a>Modifica filtro ambito
Azure AD Sync occupa la maggior parte degli oggetti. È possibile ridurre l'ambito degli oggetti e ridurre il numero di oggetti da esportare, senza modificare le regole di sincronizzazione predefinite standard. 

Usare uno dei metodi seguenti per ridurre l'ambito degli oggetti da sincronizzare:

- attributo cloudFiltered
- Filtro unità organizzativa

Se si riduce l'ambito degli utenti sincronizzati, la sincronizzazione dell'hash delle password viene arrestata anche per gli utenti con filtro in uscita. Se gli oggetti sono già sincronizzati, dopo aver ridotto l'ambito, gli oggetti filtrati vengono eliminati dalla directory di destinazione. Per questo motivo, assicurarsi di avere un ambito molto accurato.

>[!IMPORTANT] 
> Non è consigliabile aumentare l'ambito degli oggetti configurati da Azure AD Connect. Questa operazione rende difficile per il team di supporto Microsoft comprendere le personalizzazioni. Se è necessario aumentare l'ambito degli oggetti, modificare la regola esistente, clonarla e disabilitare la regola originale. 

### <a name="cloudfiltered-attribute"></a>attributo cloudFiltered
Non è possibile impostare questo attributo in Active Directory. Impostare il valore di questo attributo aggiungendo una nuova regola in ingresso. È quindi possibile usare **Transformation** ed **Expression** per impostare questo attributo nel metaverse. L'esempio seguente mostra che non si vuole sincronizzare tutti gli utenti il cui nome del reparto inizia con **HRD** (senza distinzione tra maiuscole e minuscole):

`cloudFiltered <= IIF(Left(LCase([department]), 3) = "hrd", True, NULL)`

Il reparto è stato prima convertito da Source (Active Directory) a minuscole. Quindi, usando la `Left` funzione, abbiamo accettato solo i primi tre caratteri e confrontato con `hrd` . Se corrisponde, il valore viene impostato su `True` ; in caso contrario, `NULL` . Quando si imposta il valore su null, un'altra regola con precedenza inferiore (un valore di numero più alto) può scrivere su di essa con una condizione diversa. Eseguire l'anteprima su un oggetto per convalidare la regola di sincronizzazione, come indicato nella sezione [convalida della regola di sincronizzazione](#validate-sync-rule) .

![Crea opzioni regola di sincronizzazione in ingresso](media/how-to-connect-fix-default-rules/default7a.png)

### <a name="organizational-unit-filtering"></a>Filtro unità organizzativa
È possibile creare una o più unità organizzative (OU) e spostare gli oggetti che non si desidera sincronizzare con queste unità organizzative. Quindi, configurare il filtro dell'unità organizzativa in Azure AD Connect. Avviare **Azure ad Connect** dall'icona del desktop e selezionare le opzioni seguenti. È inoltre possibile configurare il filtro dell'unità organizzativa al momento dell'installazione di Azure AD Connect. 

![Azure AD Connect attività aggiuntive](media/how-to-connect-fix-default-rules/default8.png)

Eseguire la procedura guidata e deselezionare le unità organizzative che non si desidera sincronizzare.

![Opzioni di filtro dell'unità organizzativa e del dominio Azure AD Connect](media/how-to-connect-fix-default-rules/default9.png)

## <a name="change-join-condition"></a>Modificare la condizione di join
Utilizzare le condizioni di join predefinite configurate da Azure AD Connect. La modifica delle condizioni di join predefinite rende difficile per il supporto tecnico Microsoft comprendere le personalizzazioni e supportare il prodotto.

## <a name="validate-sync-rule"></a>Convalida regola di sincronizzazione
È possibile convalidare la regola di sincronizzazione appena aggiunta usando la funzionalità di anteprima, senza eseguire il ciclo di sincronizzazione completo. In Azure AD Connect selezionare **servizio di sincronizzazione**.

![Azure AD Connect, con il servizio di sincronizzazione evidenziato](media/how-to-connect-fix-default-rules/default10.png)

Selezionare **ricerca metaverse**. Selezionare l'oggetto ambito come **Person**, selezionare **Aggiungi clausola**e specificare i criteri di ricerca. Selezionare quindi **Cerca**e fare doppio clic sull'oggetto nei risultati della ricerca. Assicurarsi che i dati in Azure AD Connect siano aggiornati per l'oggetto, eseguendo l'importazione e la sincronizzazione nella foresta prima di eseguire questo passaggio.

![Synchronization Service Manager.](media/how-to-connect-fix-default-rules/default11.png)

In **Proprietà oggetto metaverse**selezionare **connettori**, selezionare l'oggetto nel connettore corrispondente (foresta), quindi selezionare **Proprietà.**

![Metaverse Object Properties](media/how-to-connect-fix-default-rules/default12.png)

Seleziona **anteprima...**

![Proprietà dell’oggetto spazio connettore](media/how-to-connect-fix-default-rules/default13a.png)

Nella finestra di anteprima selezionare **genera anteprima** e **Importa flusso attributi** nel riquadro sinistro.

![Screenshot che mostra la finestra "anteprima" con "Importa flusso attributi" e "genera anteprima" selezionato.](media/how-to-connect-fix-default-rules/default14.png)
 
Si noti che la regola appena aggiunta viene eseguita sull'oggetto e l'attributo è impostato `cloudFiltered` su true.

![Anteprima](media/how-to-connect-fix-default-rules/default15a.png)
 
Per confrontare la regola modificata con la regola predefinita, esportare entrambe le regole separatamente, come file di testo. Queste regole vengono esportate come file di script di PowerShell. È possibile confrontarli usando qualsiasi strumento di confronto dei file, ad esempio WinDiff, per visualizzare le modifiche. 
 
Si noti che nella regola modificata l' `msExchMailboxGuid` attributo viene modificato nel tipo di **espressione** , anziché **Direct**. Inoltre, il valore viene modificato in **null** e l'opzione **ExecuteOnce** . È possibile ignorare le differenze identificate e di precedenza. 

![output dello strumento WinDiff](media/how-to-connect-fix-default-rules/default17.png)
 
Per correggere le regole per ripristinare le impostazioni predefinite, eliminare la regola modificata e abilitare la regola predefinita. Assicurarsi di non perdere la personalizzazione che si sta tentando di realizzare. Quando si è pronti, eseguire la **sincronizzazione completa**.

## <a name="next-steps"></a>Passaggi successivi
- [Hardware e prerequisiti](how-to-connect-install-prerequisites.md) 
- [Impostazioni rapide](how-to-connect-install-express.md)
- [Impostazioni personalizzate](how-to-connect-install-custom.md)