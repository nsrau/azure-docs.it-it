---
title: Come correggere le regole predefinite modificate - Azure AD Connect | Microsoft Docs
description: Informazioni su come correggere le regole predefinite modificate forniti con Azure AD Connect.
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
ms.openlocfilehash: f524e8cef3878816cec53575217bdb6d0fd9be7b
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2019
ms.locfileid: "58501257"
---
# <a name="fix-modified-default-rules-in-azure-ad-connect"></a>Correggere le regole predefinite modificate in Azure AD Connect

Azure AD Connect viene fornito con le regole predefinite per la sincronizzazione.  Sfortunatamente, queste regole non esistono universalmente per tutte le organizzazioni e potrebbe capitare, in base alle esigenze, quando è necessario modificarle.

 Se si sono modificate le regole predefinite o si prevede di modificarli, quindi, si consiglia di leggere questo documento.

Questo documento vengono presentati esempi di 2 delle personalizzazioni più comuni eseguite dagli utenti e viene spiegato il modo corretto per ottenere queste personalizzazioni.

>[!NOTE] 
> Modifica di regole predefinite esistenti per ottenere una personalizzazione necessaria non è supportata, in questo modo che si impedirà l'aggiornamento di queste regole per la versione più recente nelle versioni future. Ciò impedirà di introduzione di nuove funzionalità e correzioni di bug necessarie.  Questo documento verrà illustrato come ottenere lo stesso risultato senza modificare le regole predefinite esistenti. 

## <a name="how-to-identify-modified-default-rules"></a>Come identificare le regole predefinite modificate?
A partire dalla versione 1.3.7.0 della **Azure AD Connect**, ora è facile identificare la regola predefinita modificata. È possibile passare alle App Desktop e fare clic su **Editor regole di sincronizzazione**.

![Editor](media/how-to-connect-fix-default-rules/default1.png)

Nell'Editor, tutte le regole predefinite modificate verranno visualizzate con un'icona davanti al nome come mostrato di seguito:

![icon](media/how-to-connect-fix-default-rules/default2.png)

 Si noterà anche una regola disabilitata con lo stesso nome accanto a esso, ovvero la regola predefinita standard:

![regole predefinite](media/how-to-connect-fix-default-rules/default2a.png)

## <a name="common-customizations"></a>Personalizzazioni comuni
Ecco le personalizzazioni più comuni per le regole predefinite:

- [La modifica del flusso degli attributi](#changing-attribute-flow)
- [Modifica filtro di ambito](#changing-scoping-filter)
- [Modifica condizione di join](#changing-join-condition)

## <a name="before-changing-any-rules"></a>Prima di modificare le regole
- Disabilitare l'utilità di pianificazione di sincronizzazione.  L'utilità di pianificazione viene eseguita ogni 30 minuti per impostazione predefinita. Accertarsi che non venga avviata mentre si apportano modifiche e si risolvono i problemi delle nuove regole. Per disabilitare temporaneamente l'utilità di pianificazione, avviare PowerShell ed eseguire `Set-ADSyncScheduler -SyncCycleEnabled $false`.
 ![regole predefinite](media/how-to-connect-fix-default-rules/default3.png)

- La modifica nel filtro di ambito può comportare l'eliminazione di oggetti nella directory di destinazione. Prestare attenzione prima di apportare modifiche nell'ambito di oggetti. È consigliabile apportare modifiche a un server di gestione temporanea prima di apportare modifiche sul server attivo.
- Eseguire un'anteprima su un singolo oggetto, come indicato nella [convalidare regola di sincronizzazione](#validate-sync-rule) sezione, dopo l'aggiunta di qualsiasi nuova regola.
- Eseguire una sincronizzazione completa dopo l'aggiunta di una nuova regola o la modifica di qualsiasi regola di sincronizzazione personalizzate. Questa sincronizzazione applicherà le nuove regole per tutti gli oggetti.

## <a name="changing-attribute-flow"></a>La modifica del flusso degli attributi
Esistono 3 diversi scenari di flusso di attributi, diamo un'occhiata a come ottenere questo risultato senza alterare le regole predefinite standard.
- Aggiunta di nuovo attributo
- Sostituzione del valore di attributo esistente
- Non sincronizzare attributo esistente

### <a name="adding-new-attribute"></a>Aggiunta di nuovi attributi:
Se si trova un attributo non viene trasmesso dalla directory di origine nella directory di destinazione, quindi è possibile usare il [di sincronizzazione Azure AD Connect: Estensioni della directory](how-to-connect-sync-feature-directory-extensions.md) per propagare i nuovi attributi.

Si noti che deve essere il metodo preferito da usare [di sincronizzazione Azure AD Connect: Estensioni della directory](how-to-connect-sync-feature-directory-extensions.md), un oggetto dalla funzionalità predefinita fornita da Azure AD Connect. Tuttavia, se non adatti a te quindi passano attraverso seguire questa procedura per trasmettere un attributo senza modificare la regola di sincronizzazione predefinita standard esistente, è possibile farlo mediante l'aggiunta di due nuove regole di sincronizzazione.


#### <a name="add-an-inbound-sync-rule"></a>Aggiungere una regola di sincronizzazione in ingresso:
Regola di sincronizzazione in entrata significa che l'origine per l'attributo è uno spazio connettore e di destinazione è metaverse. Ad esempio, per trasmettere un nuovo attributo da Active Directory locale ad Azure Active Directory, creare una nuova regola di sincronizzazione in entrata avviando il **Synchronization Rules Editor**, quindi selezionare direzione **Inbound** e fare clic su **Aggiungi nuova regola**. 

 ![regole predefinite](media/how-to-connect-fix-default-rules/default3a.png)

Seguire una convenzione di denominazione per denominare la regola, qui abbiamo utilizzato **personalizzate In from AD - utente**, ciò significa che la regola sia una regola personalizzata e una regola in ingresso dallo spazio connettore di Active Directory al Metaverse. 

 ![regole predefinite](media/how-to-connect-fix-default-rules/default3b.png)

Fornire una propria descrizione della regola in modo che sia semplice, ad esempio qual è l'obiettivo di questa regola e perché è stata necessaria la manutenzione futura della regola.
Sistema connesso (la foresta) selezionato l'origine dell'attributo. Quindi il tipo di oggetto sistema connesso e il tipo di oggetto Metaverse.

Specificare il valore di priorità compreso tra 0 a 99 (ridurre il numero, maggiore precedenza). Mantenere gli altri campi, ad esempio 'Tag', 'Enable Password Sync' e 'Disabled' come valore predefinito.

Keep 'Scoping filter' è vuoto, questo significa che la regola verrà applicata a tutti gli oggetti aggiunti tra sistema connesso AD e Metaverse.

Mantieni la condizione di join definita nella regola predefinita standard verrà occuparsi vuoto "Regole di unione" che significa che questa regola. Si tratta di un altro motivo per non disabilitare o eliminare la regola predefinita standard poiché se è presente alcuna condizione di join occuparsi quindi l'attributo non verrà propagati. 

Aggiungere transformation appropriato per l'attributo, è possibile assegnare costante a un valore costante per l'attributo di destinazione del flusso, o mapping tra l'attributo di origine o destinazione o un'espressione per l'attributo diretto. Ecco vari [funzioni di espressione](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-sync-functions-reference) è possibile usare.

#### <a name="add-an-outbound-sync-rule"></a>Aggiungere una regola di sincronizzazione in uscita:
Finora aggiungendo una regola di sincronizzazione in entrata è stato eseguito la metà del lavoro, perché l'attributo non risulta ancora collegato alla directory di destinazione. Per creare un collegamento dell'attributo al direttore di destinazione è necessario creare una regola in uscita, ovvero l'origine è metaverse e la destinazione è il sistema connesso. Per creare una regola in uscita, avviare **Synchronization Rules Editor**, modificare il **direzione** al **in uscita** e fare clic su **Aggiungi nuova regola**. 

![regole predefinite](media/how-to-connect-fix-default-rules/default3c.png)

Come la regola in ingresso, è possibile usare una convenzione di denominazione per **nome** la regola. Selezionare il **Connected System** come tenant di Azure AD, selezionare l'oggetto sistema connesso a cui si desidera impostare il valore dell'attributo. Impostare la priorità compreso tra 0 - 99. 

![regole predefinite](media/how-to-connect-fix-default-rules/default3d.png)

Mantieni **Scoping filter** vuoto, keep **regole di unione** vuoto, inserire la trasformazione come costante, diretto o un'espressione. 

In questo esempio si è illustrato come propagare di nuovo attributo per un oggetto utente da Active Directory ad Azure Active Directory. È possibile usare questi passaggi per eseguire il mapping di qualsiasi attributo da qualsiasi oggetto all'origine e destinazione.  Per altre informazioni, vedere [creazione di regole di sincronizzazione personalizzate](how-to-connect-create-custom-sync-rule.md) e [preparazione per il provisioning degli utenti](https://docs.microsoft.com/office365/enterprise/prepare-for-directory-synchronization).

### <a name="overriding-value-of-existing-attribute"></a>Sostituzione del valore di attributo esistente
È possibile che si vuole sostituire il valore dell'attributo già connessa, ad esempio si desidera sempre impostato il valore Null a un attributo in Azure AD, è possibile farlo creando semplicemente solo una regola in ingresso, come indicato nel passaggio precedente e il flusso  **AuthoritativeNull** valore costante per l'attributo di destinazione. Si noti che abbiamo utilizzato AuthoritativeNulll anziché Null in questo caso. Questo avviene perché il valore non null sostituirà il valore Null, anche se ha la precedenza più bassa (valore numerico più alto nella regola). Tuttavia, il AuthoritativeNull verrà considerato come Null e non verranno sostituiti con valori non null da altre regole. 

### <a name="dont-sync-existing-attribute"></a>Non sincronizzare attributo esistente
Se si desidera impedire la sincronizzazione di un attributo, è possibile usare l'attributo di filtro fornite in Azure AD Connect. Avvio veloce **Azure AD Connect** dall'icona sul desktop e quindi selezionare **personalizzazione delle opzioni di sincronizzazione**.

![regole predefinite](media/how-to-connect-fix-default-rules/default4.png)

 Assicurarsi che **filtro attributi e app di Azure AD** sia selezionata e fare clic su **successivo**.

![regole predefinite](media/how-to-connect-fix-default-rules/default5.png)

Deselezionare gli attributi che si desidera escludere dalla sincronizzazione.

![regole predefinite](media/how-to-connect-fix-default-rules/default6a.png)

## <a name="changing-scoping-filter"></a>Modifica filtro di ambito
Servizio di sincronizzazione Azure AD si occupa della maggior parte degli oggetti, è possibile ridurre l'ambito degli oggetti e ridurlo meno oggetti da esportare in modo supportato senza dover modificare le regole di sincronizzazione predefinite standard. Nel caso in cui si vuole aumentare l'ambito degli oggetti, quindi potrai **modifica** la regola esistente, clonarlo e disabilitare la regola originale. Microsoft consiglia di non aumentare l'ambito configurato da Azure AD Connect. Incremento nell'ambito degli oggetti in modo rigido per il team di supporto comprendere le personalizzazioni e il supporto del prodotto.

Ecco come si può ridurre l'ambito degli oggetti sincronizzati con Azure AD. Si noti che se si riduce l'ambito del **utenti** sincronizzati con la sincronizzazione hash password verrà interrotta anche per gli utenti filtrato orizzontale. Se gli oggetti sono già sincronizzato, quindi dopo aver ridotto l'ambito, gli oggetti filtrati orizzontale verrà eliminato dalla directory di destinazione, collaborare in ambito con estrema cautela.
Ecco i modi per ridurre l'ambito degli oggetti che si intende sincronizzare.

- [attributo cloudFiltered](#cloudfiltered-attribute)
- [Filtro dell'unità Organizzativa](#ou-filtering)

### <a name="cloudfiltered-attribute"></a>attributo cloudFiltered
Si noti che questo non è un attributo che può essere impostato in Active Directory. È necessario impostare il valore di questo attributo aggiungendo una nuova regola in ingresso, come indicato nella **sostituzione del valore di attributo esistente** sezione. È quindi possibile usare la **Transformation** e usare **espressione** per impostare questo attributo nel Metaverse. Di seguito è riportato un esempio che non si vuole sincronizzare tutti dell'utente il cui nome del reparto inizia con maiuscole e minuscole **HRD**:

`cloudFiltered <= IIF(Left(LCase([department]), 3) = "hrd", True, NULL)`

Prima di tutto conversione il reparto di origine (Active Directory) in caratteri minuscoli. Usando la funzione Left, abbiamo impiegato solo 3 caratteri iniziali e viene confrontato con hrd. Se trovata una corrispondenza, quindi impostare il valore su True in caso contrario, NULL. Si noti che viene impostato il valore NULL, in modo che altre regole con priorità più basse (valore di numero più alto) accessibile in scrittura con una condizione diversa. Eseguire preview in un oggetto da convalidare regola di sincronizzazione, come indicato nella [convalidare regola di sincronizzazione](#validate-sync-rule) sezione.

![regole predefinite](media/how-to-connect-fix-default-rules/default7a.png)



### <a name="ou-filtering"></a>Filtro dell'unità Organizzativa
È possibile creare uno o più unità organizzative e spostare gli oggetti che non si vuole sincronizzare con queste unità organizzative. Quindi configurare il filtro in Azure AD Connect tramite l'avvio dell'unità Organizzativa **Azure AD Connect** dall'icona sul desktop e selezionare le opzioni come illustrato di seguito. È anche possibile configurare il filtro al momento dell'installazione di Azure AD Connect dell'unità Organizzativa. 

![regole predefinite](media/how-to-connect-fix-default-rules/default8.png)

Seguire la procedura guidata e deselezionare le unità organizzative che non si vuole sincronizzare.

![regole predefinite](media/how-to-connect-fix-default-rules/default9.png)

## <a name="changing-join-condition"></a>Modifica condizione di join
Microsoft consiglia di usare il valore predefinito join le condizioni configurati da Azure AD Connect. Modifica le condizioni di join predefinito in modo rigido per il team di supporto comprendere le personalizzazioni e il supporto del prodotto.

## <a name="validate-sync-rule"></a>Convalida regola di sincronizzazione
È possibile convalidare la regola di sincronizzazione appena aggiunto usando la funzionalità di anteprima senza eseguire il ciclo di sincronizzazione completa. Avvio veloce **servizio di sincronizzazione** dell'interfaccia utente.

![regole predefinite](media/how-to-connect-fix-default-rules/default10.png)

Fare clic sui **ricerca Metaverse**, selezionare come oggetto con ambito **persona**, **Aggiungi clausola** e indicare i criteri di ricerca. Fare clic su **ricerca** pulsante e fare doppio clic sull'oggetto nel **i risultati della ricerca** si noti che si esegue l'importazione / sincronizzazione dell'insieme di strutture prima di eseguire questo passaggio, si tratta di garantire che i dati in Azure AD Connect viene aggiornato per quell'oggetto.

![regole predefinite](media/how-to-connect-fix-default-rules/default11.png)



Selezionare **connettori**, selezionare l'oggetto in connector(forest) corrispondente, fare clic su **proprietà...** .

![regole predefinite](media/how-to-connect-fix-default-rules/default12.png)

Fare clic su di **anteprima...**

![regole predefinite](media/how-to-connect-fix-default-rules/default13a.png)

Fare clic su **Genera anteprima** e **flusso attributi importazione** nel riquadro sinistro.

![regole predefinite](media/how-to-connect-fix-default-rules/default14.png)
 
Di seguito si noterà che la regola appena aggiunta viene eseguita sull'oggetto e ha impostato l'attributo cloudFiltered su True.

![regole predefinite](media/how-to-connect-fix-default-rules/default15a.png)
 
Come regola di confronto modificato con regola predefinita?
È possibile esportare entrambe le regole separatamente come file di testo. Queste regole verranno esportate come file di script di powershell. È possibile confrontarli con qualsiasi strumento di confronto di file per visualizzare il tipo di modifiche vengono eseguite. Di seguito in questo esempio ho utilizzato windiff per confrontare due file.
 
È possibile notare che in utente regola modificata, msExchMailboxGuid attributo viene modificato in **espressione** invece del tipo **Direct** con valore di **NULL** e  **ExecuteOnce** opzione. È possibile ignorare le differenze di precedenza e identificatore. 

![regole predefinite](media/how-to-connect-fix-default-rules/default17.png)
 
Come risolvere una regola predefinita modificata?
Per correggere le regole per le impostazioni predefinite, è possibile eliminare la regola modificata e abilitare la regola predefinita come illustrato di seguito e quindi eseguire una **sincronizzazione completa**. Prima dell'operazione che adotta misure correttive, come indicato in precedenza, in modo da non perdere la personalizzazione si sta tentando di raggiungere # # passaggi successivi

## <a name="next-steps"></a>Fasi successive
- [Hardware e prerequisiti](how-to-connect-install-prerequisites.md) 
- [Impostazioni rapide](how-to-connect-install-express.md)
- [Impostazioni personalizzate](how-to-connect-install-custom.md)

