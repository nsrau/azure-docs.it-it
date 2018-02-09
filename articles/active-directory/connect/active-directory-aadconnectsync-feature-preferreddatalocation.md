---
title: 'Servizio di sincronizzazione Azure AD Connect: configurare il percorso dati preferito per Multi-Geo Capabilities in Office 365 | Microsoft Docs'
description: "Descrive come posizionare le risorse utente di Office 365 in prossimità dell'utente con il servizio di sincronizzazione Azure AD Connect."
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/30/2018
ms.author: billmath
ms.openlocfilehash: 8a36fc45334a2f1d12e6eabbfb16731ccc9998bf
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/01/2018
---
# <a name="azure-ad-connect-sync-configure-preferred-data-location-for-office-365-resources"></a>Servizio di sincronizzazione Azure AD Connect: configurare il percorso dati preferito per le risorse di Office 365
Questo argomento illustra in dettaglio come configurare l'attributo PreferredDataLocation nel servizio di sincronizzazione Azure AD Connect. Quando un cliente usa Multi-Geo Capabilities in Office 365, questo attributo viene usato per definire la posizione geografica dei dati di Office 365 dell'utente.

> [!IMPORTANT]
> Multi-Geo Capabilities è attualmente in fase di anteprima. Se si desidera partecipare al programma di anteprima, contattare il proprio rappresentante Microsoft.
>
>

## <a name="enable-synchronization-of-preferreddatalocation"></a>Abilitare la sincronizzazione di PreferredDataLocation
Per impostazione predefinita, le risorse di Office 365 per gli utenti si trovano nella stessa area del tenant di Azure AD. Ad esempio, se il tenant si trova in America del Nord, anche le cassette postali di Exchange degli utenti si trovano in America del Nord. In un'organizzazione multinazionale questo aspetto può creare problemi. Impostando l'attributo preferredDataLocation è possibile definire l'area dell'utente.

Se si imposta questo attributo, è possibile disporre delle risorse di Office 365 dell'utente, ad esempio la cassetta postale e OneDrive, nella stessa area dell'utente e continuare a disporre di un tenant per l'intera organizzazione.

> [!IMPORTANT]
> Per essere idonei per la funzionalità Multi-Geo Capabilities, è necessario disporre di almeno 5000 postazioni nella sottoscrizione di Office 365.
>
>

Le aree di Office 365 disponibili per Multi-Geo Capabilities sono:

| Region | DESCRIZIONE |
| --- | --- |
| NAM | America del Nord |
| EUR | Europa |
| APC | Asia/Pacifico |
| JPN | Giappone |
| AUS | Australia |
| CAN | Canada |
| GBR | Regno Unito |
| LAM | America Latina |

Non tutti i carichi di lavoro di Office 365 supportano l'uso dell'impostazione di un'area per l'utente.

Azure AD Connect supporta la sincronizzazione dell'attributo **PreferredDataLocation** per gli oggetti **Utente** nella versione 1.1.524.0 e successive. In particolare, sono state introdotte le seguenti modifiche:

* Lo schema del tipo di oggetto **Utente** in Azure AD Connector è stato esteso per poter includere l'attributo PreferredDataLocation, che è di tipo stringa a valore singolo.
* Lo schema del tipo di oggetto **Persona** in Metaverse è stato esteso per poter includere l'attributo PreferredDataLocation, che ha un valore singolo ed è di tipo stringa.

Per impostazione predefinita, l'attributo PreferredDataLocation non è abilitato per la sincronizzazione. Questa funzionalità è destinata a organizzazioni più grandi e non risulta vantaggiosa per tutti gli utenti. È anche necessario definire un attributo per contenere l'area di Office 365 per gli utenti, poiché non esiste alcun attributo PreferredDataLocation in Active Directory locale. Si tratta di un attributo diverso per ogni organizzazione.

> [!IMPORTANT]
> Attualmente, Azure AD consente la configurazione diretta dell'attributo PreferredDataLocation sia sugli oggetti Utente che sugli oggetti Utente cloud sincronizzati con Azure AD PowerShell. Dopo aver attivato la sincronizzazione dell'attributo PreferredDataLocation, è necessario interrompere l'uso di Azure AD PowerShell per configurare l'attributo su **oggetti Utente sincronizzati**, poiché Azure AD Connect eseguirà l'override su questi oggetti in base ai valori dell'attributo di origine in Active Directory locale.

> [!IMPORTANT]
> Dal 1° settembre 2017 Azure AD non consente più la configurazione diretta dell'attributo PreferredDataLocation sugli **oggetti Utente sincronizzati** con Azure AD PowerShell. Per configurare l'attributo PreferredDataLocation sugli oggetti Utente sincronizzati, è necessario usare Azure AD Connect.

Prima di abilitare la sincronizzazione dell'attributo PreferredDataLocation, è necessario:

* Innanzitutto, decidere quale attributo di Active Directory locale usare come attributo di origine. Deve essere di tipo **stringa a valore singolo**. Nella procedura illustrata di seguito viene usato uno degli attributi di extensionAttributes.
* Se in precedenza è stato configurato l'attributo PreferredDataLocation sugli oggetti Utente sincronizzati esistenti in Azure AD con Azure AD PowerShell, è necessario **eseguire il backporting** dei valori dell'attributo per gli oggetti Utente corrispondenti in Active Directory locale.

    > [!IMPORTANT]
    > Se non si esegue il backporting dei valori dell'attributo per gli oggetti Utente corrispondenti in Active Directory locale, Azure AD Connect rimuoverà i valori dell'attributo esistente in Azure AD quando è abilitata la sincronizzazione per l'attributo PreferredDataLocation.

* A questo punto è consigliabile configurare l'attributo di origine in almeno un paio di oggetti Utente di AD locale, che può essere usato per la verifica in un secondo momento.

La procedura per abilitare la sincronizzazione dell'attributo PreferredDataLocation può essere riepilogata come segue:

1. Disabilitare l'utilità di pianificazione della sincronizzazione e verificare che non ci sia alcuna sincronizzazione in corso
2. Aggiungere l'attributo di origine allo schema del connettore ADDS locale
3. Aggiungere PreferredDataLocation allo schema di Azure AD Connector
4. Creare una regola di sincronizzazione in entrata per trasmettere il valore dell'attributo da Active Directory locale
5. Creare una regola di sincronizzazione in uscita per trasmettere il valore dell'attributo da Azure AD
6. Eseguire un ciclo di sincronizzazione completo
7. Abilitare l'utilità di pianificazione della sincronizzazione
8. Verificare il risultato

> [!NOTE]
> La parte restante di questa sezione illustra la procedura in dettaglio nel contesto di una distribuzione di Azure AD con topologia a foresta singola e senza regole di sincronizzazione personalizzate. Se sono stati configurati una topologia a più foreste e regole di sincronizzazione personalizzate o si dispone di un server di gestione temporanea, è necessario modificare di conseguenza i passaggi.

## <a name="step-1-disable-sync-scheduler-and-verify-there-is-no-synchronization-in-progress"></a>Passaggio 1: Disabilitare l'utilità di pianificazione della sincronizzazione e verificare che non ci sia alcuna sincronizzazione in corso
Verificare che non venga eseguita alcuna sincronizzazione durante l'aggiornamento delle regole di sincronizzazione per evitare l'esportazione di modifiche accidentali in Azure AD. Per disabilitare l'utilità di pianificazione della sincronizzazione predefinita:

1. Avviare una sessione di PowerShell nel server di Azure AD Connect.
2. Disabilitare la sincronizzazione pianificata eseguendo il cmdlet: `Set-ADSyncScheduler -SyncCycleEnabled $false`.
3. Avviare **Synchronization Service Manager** passando a **START** > **Synchronization Service** (Servizio di sincronizzazione).
4. Andare alla scheda **Operazioni** e verificare che per nessuna operazione lo stato sia *In corso*.

![Synchronization Service Manager: verificare che non ci siano operazioni in corso](./media/active-directory-aadconnectsync-feature-preferreddatalocation/preferreddatalocation-step1.png)

## <a name="step-2-add-the-source-attribute-to-the-on-premises-adds-connector-schema"></a>Passaggio 2: Aggiungere l'attributo di origine allo schema del connettore ADDS locale
Non tutti gli attributi di AD vengono importati in locale nello spazio di AD Connector locale. Se si è scelto di usare un attributo non sincronizzato per impostazione predefinita, è necessario importarlo. Per aggiungere l'attributo di origine all'elenco degli attributi importati:

1. Passare alla scheda **Connettori** in Synchronization Service Manager.
2. Fare clic con il pulsante destro del mouse su **AD Connector locale** e selezionare **Proprietà**.
3. Nella finestra di dialogo popup passare alla scheda **Seleziona attributi**.
4. Verificare che l'attributo di origine che si è scelto di usare sia selezionato nell'elenco degli attributi. Se l'attributo non viene visualizzato, fare clic sulla casella di controllo "Mostra tutto".
5. Fare clic su **OK** per salvare.

![Aggiungere l'attributo di origine allo schema di AD Connector locale](./media/active-directory-aadconnectsync-feature-preferreddatalocation/preferreddatalocation-step2.png)

## <a name="step-3-add-preferreddatalocation-to-the-azure-ad-connector-schema"></a>Passaggio 3: Aggiungere PreferredDataLocation allo schema di Azure AD Connector
Per impostazione predefinita, l'attributo PreferredDataLocation non viene importato nello spazio connettore di Azure AD. Per aggiungere l'attributo PreferredDataLocation all'elenco di attributi importati:

1. Passare alla scheda **Connettori** in Synchronization Service Manager.
2. Fare clic con il pulsante destro del mouse su **Azure AD Connector** e selezionare **Proprietà**.
3. Nella finestra di dialogo popup passare alla scheda **Seleziona attributi**.
4. Selezionare l'attributo preferredDataLocation nell'elenco degli attributi.
5. Fare clic su **OK** per salvare.

![Aggiungere l'attributo di origine allo schema di AD Connector](./media/active-directory-aadconnectsync-feature-preferreddatalocation/preferreddatalocation-step3.png)

## <a name="step-4-create-an-inbound-synchronization-rule-to-flow-the-attribute-value-from-on-premises-active-directory"></a>Passaggio 4: Creare una regola di sincronizzazione in entrata per trasmettere il valore dell'attributo da Active Directory locale
La regola di sincronizzazione in entrata consente la trasmissione del valore dell'attributo dall'attributo di origine di Active Directory locale a Metaverse:

1. Avviare **Synchronization Rules Editor** (Editor delle regole di sincronizzazione) passando a **START** > **Synchronization Rules Editor** (Editor delle regole di sincronizzazione).
2. Impostare il filtro di ricerca **Direzione** su **In arrivo**.
3. Fare clic sul pulsante **Aggiungi nuova regola** per creare una nuova regola in entrata.
4. Nella scheda **Descrizione** inserire la configurazione seguente:

    | Attributo | Valore | Dettagli |
    | --- | --- | --- |
    | NOME | *Specificare un nome* | Ad esempio, *"In entrata da AD - Utente PreferredDataLocation"* |
    | DESCRIZIONE | *Fornire una descrizione personalizzata* |  |
    | Connected System | *Scegliere il connettore di AD locale* |  |
    | Connected System Object Type | **Utente** |  |
    | Metaverse Object Type | **Person** |  |
    | Tipo di collegamento | **Join** |  |
    | Precedenza | *Scegliere un numero compreso tra 1 e 99* | I numeri compresi tra 1 e 99 sono riservati alle regole di sincronizzazione personalizzate. Non scegliere un valore usato da un'altra regola di sincronizzazione. |

5. Lasciare vuoto il campo **Scoping filter** (Filtro di ambito) per includere tutti gli oggetti. Potrebbe essere necessario perfezionare il filtro ambito in base alla distribuzione di Azure AD Connect.
6. Passare alla **scheda Trasformazione** e implementare la regola di trasformazione seguente:

    | Flow Type | Target Attribute | Sorgente | Applicare una sola volta | Tipi di unione |
    | --- | --- | --- | --- | --- |
    |Diretto | PreferredDataLocation | Selezionare l'attributo di origine | Non selezionato | Aggiornamento |

7. Fare clic su **Aggiungi** per creare la regola in entrata.

![Creare una regola di sincronizzazione in ingresso](./media/active-directory-aadconnectsync-feature-preferreddatalocation/preferreddatalocation-step4.png)

## <a name="step-5-create-an-outbound-synchronization-rule-to-flow-the-attribute-value-to-azure-ad"></a>Passaggio 5: Creare una regola di sincronizzazione in uscita per trasmettere il valore dell'attributo da Azure AD
La regola di sincronizzazione in uscita consente la trasmissione del valore dell'attributo da Metaverse all'attributo PreferredDataLocation in Azure AD:

1. Passare all'editor **Regole di sincronizzazione**.
2. Impostare il filtro di ricerca **Direzione** da **In uscita**.
3. Fare clic sul pulsante **Aggiungi nuova regola**.
4. Nella scheda **Descrizione** inserire la configurazione seguente:

    | Attributo | Valore | Dettagli |
    | ----- | ------ | --- |
    | NOME | *Specificare un nome* | Ad esempio, "In uscita verso AAD - Utente PreferredDataLocation" |
    | DESCRIZIONE | *Inserire una descrizione* ||
    | Connected System | *Selezionare il connettore di AAD* ||
    | Connected System Object Type | Utente ||
    | Metaverse Object Type | **Person** ||
    | Tipo di collegamento | **Join** ||
    | Precedenza | *Scegliere un numero compreso tra 1 e 99* | I numeri compresi tra 1 e 99 sono riservati alle regole di sincronizzazione personalizzate. Non scegliere un valore usato da un'altra regola di sincronizzazione. |

5. Passare alla scheda **Scoping filter** (Ambito filtro) e aggiungere un **gruppo dell'ambito filtro singolo con le due clausole**:

    | Attributo | Operatore | Valore |
    | --- | --- | --- |
    | sourceObjectType | EQUAL | Utente |
    | cloudMastered | NOTEQUAL | True  |

    L'ambito del filtro determina a quali oggetti di Azure AD viene applicata la regola di sincronizzazione in uscita. In questo esempio, viene usato lo stesso ambito filtro della regola di sincronizzazione OOB "In uscita verso Ad - Identità utente". Impedisce l'applicazione della regola di sincronizzazione agli oggetti Utente che non vengono sincronizzati da Active Directory locale. Potrebbe essere necessario perfezionare il filtro ambito in base alla distribuzione di Azure AD Connect.

6. Andare alla scheda **Trasformazione** e implementare la regola di trasformazione seguente:

    | Flow Type | Target Attribute | Sorgente | Applicare una sola volta | Tipi di unione |
    | --- | --- | --- | --- | --- |
    | Diretto | PreferredDataLocation | PreferredDataLocation | Non selezionato | Aggiornamento |

7. Fare clic su **Aggiungi** per creare la regola in uscita.

![Creare una regola di sincronizzazione in uscita](./media/active-directory-aadconnectsync-feature-preferreddatalocation/preferreddatalocation-step5.png)

## <a name="step-6-run-full-synchronization-cycle"></a>Passaggio 6: Eseguire un ciclo di sincronizzazione completo
In generale, il ciclo di sincronizzazione completo è necessario perché sono stati aggiunti nuovi attributi per gli schemi di AD e Azure AD Connector e sono state introdotte regole di sincronizzazione personalizzate. È consigliabile controllare le modifiche prima di esportarle in Azure AD. È possibile usare la procedura seguente per controllare le modifiche, eseguendo al contempo manualmente i passaggi che compongono il ciclo di sincronizzazione completo.

1. Eseguire il passaggio **Importazione completa** nel **connettore di Active Directory locale**:

   1. Passare alla scheda **Operazioni** in Synchronization Service Manager.
   2. Fare clic con il pulsante destro del mouse su **AD Connector locale** e selezionare **Esegui**.
   3. Nella finestra di dialogo popup selezionare **Importazione completa** e fare clic su **OK**.
   4. Attendere il completamento dell'operazione.

    > [!NOTE]
    > È possibile ignorare l'importazione completa in AD Connector locale se l'attributo di origine è già incluso nell'elenco degli attributi importati. In altre parole, non è necessario apportare modifiche durante il [passaggio 2: Aggiungere l'attributo di origine allo schema di AD Connector locale](#step-2-add-the-source-attribute-to-the-on-premises-adds-connector-schema).

2. Eseguire il passaggio **Importazione completa** in **Azure AD Connector**:

   1. Fare clic con il tasto destro del mouse sul connettore di **Azure AD** e selezionare **Esegui...**
   2. Nella finestra di dialogo popup selezionare **Importazione completa** e fare clic su **OK**.
   3. Attendere il completamento dell'operazione.

3. Controllare le modifiche alle regole di sincronizzazione in un oggetto Utente esistente:

L'attributo di origine di Active Directory locale e PreferredDataLocation di Azure AD sono stati importati nel rispettivo spazio connettore. Prima di procedere con il passaggio Sincronizzazione completa, è consigliabile eseguire un'operazione **Anteprima** su un oggetto Utente esistente nello spazio connettore AD locale. L'attributo di origine dell'oggetto selezionato deve essere popolato. Un'**anteprima** corretta che mostra PreferredDataLocation popolato in Metaverse indica che l'utente ha configurato correttamente le regole di sincronizzazione. Per informazioni sull'esecuzione dell'**anteprima**, fare riferimento alla sezione [Verify the change](active-directory-aadconnectsync-change-the-configuration.md#verify-the-change) (Verificare le modifiche).

4. Eseguire il passaggio **Sincronizzazione completa** nel **connettore di Active Directory locale**:

   1. Fare clic con il pulsante destro del mouse su **AD Connector locale** e selezionare **Esegui**.
   2. Nella finestra di dialogo popup selezionare **Sincronizzazione completa** e fare clic su **OK**.
   3. Attendere il completamento dell'operazione.

5. Verificare le **esportazioni in sospeso** per Azure AD:

   1. Fare clic con il pulsante destro del mouse su **Azure AD Connector** e selezionare **Search Connector Space** (Cerca spazio connettore).
   2. Nella finestra popup Search Connector Space (Spazio connettore di ricerca):

      1. Impostare **Ambito** su **Pending Export** (Esportazione in sospeso).
      2. Selezionare tutte e 3 le caselle di controllo, tra cui **Aggiungi, Modifica ed Elimina**.
      3. Fare clic sul pulsante **Ricerca** per ottenere l'elenco degli oggetti con le modifiche da esportare. Per esaminare le modifiche per un determinato oggetto, fare doppio clic sull'oggetto.
      4. Verificare le modifiche previste.

6. Eseguire il passaggio **Esportazione** in **Azure AD Connector**

   1. Fare clic con il pulsante destro del mouse su **Azure AD Connector** e selezionare **Esegui**.
   2. Nella finestra popup Run Connector (Esegui connettore) selezionare **Esporta** e fare clic su **OK**.
   3. Attendere il completamento dell'esportazione in Azure AD.

> [!NOTE]
> È possibile notare che la procedura non include il passaggio di sincronizzazione completa e quello di esportazione in Azure AD Connector. Questi passaggi non sono necessari perché i valori dell'attributo si trasmettono solo da Active Directory locale ad Azure AD.

## <a name="step-7-re-enable-sync-scheduler"></a>Passaggio 7: Riattivare l'utilità di pianificazione della sincronizzazione
Riabilitare l'utilità di pianificazione della sincronizzazione predefinita:

1. Avviare una sessione di PowerShell.
2. Riabilitare la sincronizzazione pianificata eseguendo di cmdlet: `Set-ADSyncScheduler -SyncCycleEnabled $true`

## <a name="step-8-verify-the-result"></a>Passaggio 8: Verificare il risultato
A questo punto è necessario verificare la configurazione e abilitarla per gli utenti.

1. Aggiungere l'area per l'attributo selezionato per un utente. L'elenco delle aree disponibili è riportato in [questa tabella](#enable-synchronization-of-preferreddatalocation).  
![Attributo AD aggiunto a un utente](./media/active-directory-aadconnectsync-feature-preferreddatalocation/preferreddatalocation-adattribute.png)
2. Attendere che l'attributo sia sincronizzato con Azure AD.
3. Usare il servizio PowerShell di Exchange Online per verificare che l'area della cassetta postale sia stata impostata correttamente.  
![Area della cassetta postale impostata per un utente in Exchange Online](./media/active-directory-aadconnectsync-feature-preferreddatalocation/preferreddatalocation-mailboxregion.png)  
Supponendo che il tenant sia stato contrassegnato per essere in grado di usare questa funzionalità, la cassetta postale viene spostata nell'area corretta. Per verificare che ciò avvenga, esaminare il nome del server in cui si trova la cassetta postale.
4. Per verificare che questa impostazione sia valida per più cassette postali, usare lo script disponibile nella [raccolta Technet](https://gallery.technet.microsoft.com/office/PowerShell-Script-to-a6bbfc2e). Questo script include inoltre un elenco di tutti i prefissi dei server dei data center di Office 365 e delle aree in cui si trovano. Può essere usato come riferimento nel passaggio precedente per verificare la posizione della cassetta postale.

## <a name="next-steps"></a>Passaggi successivi

**Altre informazioni su Multi-Geo Capabilities in Office 365:**

* Sessioni con posizioni geografiche diverse in Ignite: https://aka.ms/MultiGeoIgnite
* Sessioni con posizioni geografiche diverse in OneDrive: https://aka.ms/OneDriveMultiGeo
* Sessioni con posizioni geografiche diverse in SharePoint Online: https://aka.ms/SharePointMultiGeo

**Altre informazioni sul modello di configurazione nel motore di sincronizzazione:**

* Per altre informazioni sul modello di configurazione, vedere [Servizio di sincronizzazione Azure AD Connect: Informazioni sul provisioning dichiarativo](active-directory-aadconnectsync-understanding-declarative-provisioning.md).
* Per altre informazioni sul linguaggio delle espressioni, vedere [Servizio di sincronizzazione Azure AD Connect: Informazioni sulle espressioni di provisioning dichiarativo](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md).

**Argomenti generali**

* [Servizio di sincronizzazione Azure AD Connect: Comprendere e personalizzare la sincronizzazione](active-directory-aadconnectsync-whatis.md)
* [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md)
