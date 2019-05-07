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
ms.openlocfilehash: d2f0956b44d6df64fb73e5eee7844574237d8755
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65067642"
---
# <a name="fix-modified-default-rules-in-azure-ad-connect"></a>Correggere le regole predefinite modificate in Azure AD Connect

Azure Active Directory (Azure AD) Connect utilizza le regole predefinite per la sincronizzazione.  Sfortunatamente, queste regole non si applicano universalmente per tutte le organizzazioni. In base alle esigenze, è necessario modificarle. Questo articolo illustra due esempi delle personalizzazioni più comuni e illustra il modo corretto per ottenere queste personalizzazioni.

>[!NOTE] 
> Modifica le regole predefinite esistenti per ottenere una personalizzazione necessaria non è supportata. Se si esegue questa operazione, impedisce l'aggiornamento di queste regole per la versione più recente nelle future versioni. Si otterranno le correzioni di bug, che è necessario o nuove funzionalità. Questo documento illustra come ottenere lo stesso risultato senza modificare le regole predefinite esistenti. 

## <a name="how-to-identify-modified-default-rules"></a>Come identificare le regole predefinite modificate
A partire dalla versione 1.3.7.0 di Azure AD Connect, è facile identificare la regola predefinita modificata. Passare a **le app desktop**e selezionare **Editor regole di sincronizzazione**.

![Azure AD Connect, con evidenziati Editor regole di sincronizzazione](media/how-to-connect-fix-default-rules/default1.png)

Nell'Editor, vengono visualizzate tutte le regole predefinite modificate con un'icona di avviso davanti al nome.

![Icona di avviso](media/how-to-connect-fix-default-rules/default2.png)

 Disabled regola con lo stesso nome accanto a esso viene visualizzato anche (questa è la regola predefinita standard).

![Editor regole di sincronizzazione, che mostra la regola predefinita standard e regola predefinita modificata](media/how-to-connect-fix-default-rules/default2a.png)

## <a name="common-customizations"></a>Personalizzazioni comuni
Ecco le personalizzazioni più comuni per le regole predefinite:

- Modificare il flusso di attributi
- Filtro di ambito di modifica
- Modifica condizione di join

Prima di modificare le regole:

- Disabilitare l'utilità di pianificazione di sincronizzazione. L'utilità di pianificazione viene eseguita ogni 30 minuti per impostazione predefinita. Assicurarsi che non venga avviata mentre si apportano modifiche e le nuove regole di risoluzione dei problemi. Per disabilitare temporaneamente l'utilità di pianificazione, avviare PowerShell ed eseguire `Set-ADSyncScheduler -SyncCycleEnabled $false`.
 ![Comandi di PowerShell per disabilitare l'utilità di pianificazione di sincronizzazione](media/how-to-connect-fix-default-rules/default3.png)

- La modifica nel filtro di ambito può comportare l'eliminazione di oggetti nella directory di destinazione. Prestare attenzione prima di apportare modifiche nell'ambito di oggetti. È consigliabile apportare modifiche a un server di gestione temporanea prima di apportare modifiche sul server attivo.
- Eseguire un'anteprima in un singolo oggetto, come indicato nella [convalidare regola di sincronizzazione](#validate-sync-rule) sezione, dopo l'aggiunta di qualsiasi nuova regola.
- Eseguire una sincronizzazione completa dopo l'aggiunta di una nuova regola o la modifica di qualsiasi regola di sincronizzazione personalizzate. Questa sincronizzazione si applica le nuove regole per tutti gli oggetti.

## <a name="change-attribute-flow"></a>Modificare il flusso di attributi
Esistono tre scenari diversi per modificare il flusso di attributi:
- Aggiunta di un nuovo attributo.
- Ignorare il valore di un attributo esistente.
- Scelta di non sincronizzazione un attributo esistente.

È possibile eseguire queste senza alterare le regole predefinite standard.

### <a name="add-a-new-attribute"></a>Aggiungere un nuovo attributo
Se si ritiene che un attributo non viene trasmesso dalla directory di origine nella directory di destinazione, usare il [di sincronizzazione Azure AD Connect: Estensioni della directory](how-to-connect-sync-feature-directory-extensions.md) per risolvere il problema.

Se le estensioni non risolvere il problema, provare ad aggiungere due nuove regole di sincronizzazione, descritte nelle sezioni seguenti.


#### <a name="add-an-inbound-sync-rule"></a>Aggiungere una regola di sincronizzazione in ingresso
Una regola di sincronizzazione in entrata significa che l'origine per l'attributo è uno spazio connettore e la destinazione è il metaverse. Ad esempio, per disporre di un nuovo attributo di un flusso da Active Directory locale ad Azure Active Directory, creare una nuova regola di sincronizzazione in entrata. Avviare il **Editor regole di sincronizzazione**, selezionare **Inbound** come la direzione e selezionare **Aggiungi nuova regola**. 

 ! Sincronizzazione delle regole Editor](media/how-to-connect-fix-default-rules/default3a.png)

Seguire una convenzione di denominazione per denominare la regola. In questo caso, usiamo **personalizzato In from AD - utente**. Ciò significa che la regola è una regola personalizzata e una regola in ingresso dallo spazio connettore di Active Directory al metaverse.   

 ![Creare una regola di sincronizzazione in ingresso](media/how-to-connect-fix-default-rules/default3b.png)

Fornire una propria descrizione della regola, in modo che sia facile manutenzione futura della regola. Ad esempio, la descrizione può basarsi su che cos'è l'obiettivo della regola e perché è necessaria.

Effettuare le selezioni desiderate il **Connected System**, **Connected System Object Type**, e **tipo di oggetto Metaverse** campi.

Specificare il valore di priorità compreso tra 0 e 99 (minore è il numero, una precedenza superiore). Per il **Tag**, **Enable Password Sync**, e **disabilitato** campi, usare le selezioni predefinite.

Mantieni **definizione ambito filtro** vuoto. Ciò significa che la regola si applica a tutti gli oggetti aggiunti tra il sistema connesso di Active Directory e il metaverse.

Mantieni **regole di unione** vuoto. Ciò significa che questa regola viene utilizzata la condizione di join definita nella regola predefinita standard. Si tratta di un altro motivo per non disabilitare o eliminare la regola predefinita standard. Se è presente alcuna condizione di join, l'attributo non verrà trasferiti. 

Aggiungere le trasformazioni appropriate per l'attributo. È possibile assegnare una costante, per rendere una costante valore di flusso per l'attributo di destinazione. È possibile usare il mapping diretto tra l'attributo di origine o destinazione. In alternativa, è possibile usare un'espressione per l'attributo. Ecco vari [funzioni di espressione](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-sync-functions-reference) è possibile usare.

#### <a name="add-an-outbound-sync-rule"></a>Aggiungere una regola di sincronizzazione in uscita
Per collegare l'attributo nella directory di destinazione, è necessario creare una regola in uscita. Ciò significa che l'origine è il metaverse e la destinazione è il sistema connesso. Per creare una regola in uscita, avviare il **Editor regole di sincronizzazione**, modificare il **direzione** al **in uscita**e selezionare **Aggiungi nuova regola**. 

![Editor regole di sincronizzazione](media/how-to-connect-fix-default-rules/default3c.png)

Come con la regola in ingresso, è possibile usare una convenzione di denominazione per denominare la regola. Selezionare il **Connected System** come il tenant di Azure AD e selezionare il sistema connesso oggetto a cui si desidera imposta il valore dell'attributo. Impostare la priorità compreso tra 0 e 99. 

![Creare una regola di sincronizzazione in uscita](media/how-to-connect-fix-default-rules/default3d.png)

Mantieni **Scoping filter** e **regole di unione** vuoto. Compilare la trasformazione come costante, diretto o espressione. 

Ora sapete come effettuare un nuovo attributo per un flusso oggetto utente da Active Directory ad Azure Active Directory. È possibile usare questi passaggi per eseguire il mapping di qualsiasi attributo da qualsiasi oggetto all'origine e destinazione. Per altre informazioni, vedere [creazione di regole di sincronizzazione personalizzate](how-to-connect-create-custom-sync-rule.md) e [preparazione per il provisioning degli utenti](https://docs.microsoft.com/office365/enterprise/prepare-for-directory-synchronization).

### <a name="override-the-value-of-an-existing-attribute"></a>Sostituire il valore di un attributo esistente
È possibile sostituire il valore di un attributo che è già stato mappato. Ad esempio, se si vuole sempre impostato un valore null a un attributo in Azure AD, è sufficiente creare solo una regola in ingresso. Verificare il valore costante, `AuthoritativeNull`, l'attributo di destinazione del flusso. 

>[!NOTE] 
> Uso `AuthoritativeNull` invece di `Null` in questo caso. Infatti, il valore non null sostituirà il valore null, anche se ha la precedenza più bassa (valore numerico maggiore nella regola). `AuthoritativeNull`, d'altra parte, non viene sostituito con un valore non null da altre regole. 

### <a name="dont-sync-existing-attribute"></a>Non sincronizzare attributo esistente
Se si desidera impedire la sincronizzazione di un attributo, usare l'attributo di filtro fornite in Azure AD Connect. Avvio veloce **Azure AD Connect** dall'icona sul desktop, quindi selezionare **personalizzazione delle opzioni di sincronizzazione**.

![Opzioni di attività aggiuntive di Azure AD Connect](media/how-to-connect-fix-default-rules/default4.png)

 Assicurarsi che **filtro attributi e app di Azure AD** sia selezionata, quindi selezionare **successivo**.

![Funzionalità facoltativa di Azure AD Connect](media/how-to-connect-fix-default-rules/default5.png)

Cancellare gli attributi che si desidera escludere dalla sincronizzazione.

![Attributi di Azure AD Connect](media/how-to-connect-fix-default-rules/default6a.png)

## <a name="change-scoping-filter"></a>Filtro di ambito di modifica
Servizio di sincronizzazione Azure AD si occupa della maggior parte degli oggetti. È possibile ridurre l'ambito degli oggetti e ridurre il numero di oggetti da esportare, senza modificare le regole di sincronizzazione predefinite standard. 

Usare uno dei metodi seguenti per ridurre l'ambito degli oggetti che si sincronizzano:

- attributo cloudFiltered
- Filtro unità organizzativa

Se si riduce l'ambito degli utenti sincronizzati, la sincronizzazione hash password si interrompe anche per gli utenti filtrato orizzontale. Se gli oggetti sono già sincronizzato, dopo aver ridotto l'ambito, gli oggetti filtrati orizzontale vengono eliminati dalla directory di destinazione. Per questo motivo, assicurarsi che si definisce l'ambito con estrema cautela.

>[!IMPORTANT] 
> Espandendo l'ambito degli oggetti configurati da Azure AD Connect non è consigliato. In questo modo sarà più difficile per il team di supporto Microsoft comprendere le personalizzazioni. Se è necessario aumentare l'ambito degli oggetti, modificare la regola esistente, clonarlo e disabilitare la regola originale. 

### <a name="cloudfiltered-attribute"></a>attributo cloudFiltered
È possibile impostare questo attributo in Active Directory. Impostare il valore di questo attributo, aggiungere una nuova regola in ingresso. È quindi possibile usare **Transformation** e **espressione** per impostare questo attributo nel metaverse. L'esempio seguente mostra che non si vuole sincronizzare tutti gli utenti il cui nome del reparto inizia con **HRD** (maiuscole e minuscole):

`cloudFiltered <= IIF(Left(LCase([department]), 3) = "hrd", True, NULL)`

Il reparto viene prima convertito dall'origine (Active Directory) in caratteri minuscoli. Quindi, usando il `Left` funzione, abbiamo impiegato solo i primi tre caratteri e confrontati con `hrd`. Se trovata una corrispondenza, il valore è impostato su `True`; in caso contrario `NULL`. Nell'impostazione del valore su null, altre regole con precedenza inferiore (un valore numerico maggiore) accessibile in scrittura con una condizione diversa. Anteprima di esecuzione per un oggetto per convalidare la regola di sincronizzazione, come indicato nella [regola di sincronizzazione convalida](#validate-sync-rule) sezione.

![Creare opzioni delle regole di sincronizzazione in ingresso](media/how-to-connect-fix-default-rules/default7a.png)

### <a name="organizational-unit-filtering"></a>Filtro unità organizzativa
È possibile creare uno o più unità organizzative (OU) e spostare gli oggetti che non si vuole sincronizzare con queste unità organizzative. Quindi, configurare il filtro in Azure AD Connect dell'unità Organizzativa. Avvio veloce **Azure AD Connect** dall'icona sul desktop, selezionare le opzioni seguenti. È anche possibile configurare il filtro al momento dell'installazione di Azure AD Connect dell'unità Organizzativa. 

![Attività aggiuntive di Azure AD Connect](media/how-to-connect-fix-default-rules/default8.png)

Seguire la procedura guidata e deselezionare le unità organizzative che non si vuole sincronizzare.

![Opzioni di filtro dell'unità Organizzativa e dominio di connettersi AD Azure](media/how-to-connect-fix-default-rules/default9.png)

## <a name="change-join-condition"></a>Modifica condizione di join
Usare le condizioni di join predefinito configurate da Azure AD Connect. Modifica le condizioni di join predefinito rende più difficile per il supporto Microsoft comprendere le personalizzazioni e il supporto del prodotto.

## <a name="validate-sync-rule"></a>Convalida regola di sincronizzazione
È possibile convalidare la regola di sincronizzazione appena aggiunto usando la funzionalità di anteprima, senza eseguire il ciclo di sincronizzazione completa. In Azure AD Connect, selezionare **servizio di sincronizzazione**.

![Azure AD Connect, con evidenziati il servizio di sincronizzazione](media/how-to-connect-fix-default-rules/default10.png)

Selezionare **ricerca Metaverse**. Selezionare l'oggetto ambito come **person**, selezionare **Aggiungi clausola**e specificare i criteri di ricerca. Successivamente, selezionare **ricerca**, fare doppio clic su oggetto nei risultati della ricerca. Verificare che i dati in Azure AD Connect siano aggiornati per l'oggetto, tramite l'esecuzione di importazione / sincronizzazione dell'insieme di strutture prima di eseguire questo passaggio.

![Synchronization Service Manager.](media/how-to-connect-fix-default-rules/default11.png)

Sul **proprietà dell'oggetto Metaverse**, selezionare **connettori**, selezionare l'oggetto nel connettore corrispondente (foresta) e selezionare **proprietà...** .

![Metaverse Object Properties](media/how-to-connect-fix-default-rules/default12.png)

Selezionare **anteprima...**

![Proprietà dell’oggetto spazio connettore](media/how-to-connect-fix-default-rules/default13a.png)

Nella finestra di anteprima, selezionare **Genera anteprima** e **flusso attributi importazione** nel riquadro sinistro.

![Anteprima](media/how-to-connect-fix-default-rules/default14.png)
 
In questo caso, si noti che la regola appena aggiunta viene eseguita sull'oggetto e ha impostato il `cloudFiltered` attributo su true.

![Anteprima](media/how-to-connect-fix-default-rules/default15a.png)
 
Per confrontare la regola modificata con la regola predefinita, esportare le regole separatamente, come i file di testo. Queste regole vengono esportate come file di script di PowerShell. È possibile confrontarli con qualsiasi strumento di confronto di file (ad esempio windiff) per visualizzare le modifiche. 
 
Si noti che nella regola modificata, il `msExchMailboxGuid` attributo viene modificato nel **espressione** tipo, anziché **diretta**. Inoltre, il valore viene modificato in **NULL** e **ExecuteOnce** opzione. È possibile ignorare le differenze di precedenza e identificatore. 

![output dello strumento di WinDiff](media/how-to-connect-fix-default-rules/default17.png)
 
Per correggere le regole per modificare le impostazioni predefinite, eliminare la regola modificata e abilitare la regola predefinita. Assicurarsi di non perdere la personalizzazione che si sta tentando di raggiungere. Quando si è pronti, eseguire **sincronizzazione completa**.

## <a name="next-steps"></a>Passaggi successivi
- [Hardware e prerequisiti](how-to-connect-install-prerequisites.md) 
- [Impostazioni rapide](how-to-connect-install-express.md)
- [Impostazioni personalizzate](how-to-connect-install-custom.md)



