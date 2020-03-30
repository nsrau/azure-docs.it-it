---
title: 'Azure AD Connect: Configurare la posizione dei dati preferiti per le risorse di Office 365Azure AD Connect: Configure preferred data location for Office 365 resources'
description: Descrive come posizionare le risorse utente di Office 365 in prossimità dell'utente con il servizio di sincronizzazione Azure Active Directory Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/11/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2a71c5328c6fa85f85db4bd7e6103f6470b86d99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80258329"
---
# <a name="azure-active-directory-connect-sync-configure-preferred-data-location-for-office-365-resources"></a>Servizio di sincronizzazione Azure Active Directory Connect: configurare il percorso dati preferito per le risorse di Office 365
Lo scopo di questo argomento è illustrare come configurare l'attributo per la posizione dati preferita nella sincronizzazione di Azure Active Directory (Azure AD) Connect. Quando un utente utilizza funzionalità multi-Geo in Office 365, si utilizza questo attributo per designare la posizione geografica dei dati di Office 365 dell'utente. I termini *area* e *area geografica* vengono usati in modo intercambiabile.

## <a name="enable-synchronization-of-preferred-data-location"></a>Abilitare la sincronizzazione del percorso dati preferito
Per impostazione predefinita, le risorse di Office 365 per gli utenti si trovano nella stessa area geografica del tenant di Azure AD. Ad esempio, se il tenant si trova in America del Nord, anche le cassette postali di Exchange degli utenti si trovano in America del Nord. In un'organizzazione multinazionale questo aspetto può creare problemi.

Impostando l'attributo **preferredDataLocation** è possibile definire l'area geografica dell'utente. È possibile avere le risorse di Office 365 dell'utente, ad esempio la cassetta postale e OneDrive, nella stessa area geografica dell'utente e continuare ad avere un tenant per l'intera organizzazione.

> [!IMPORTANT]
> Multi-Geo è attualmente disponibile per i clienti con un contratto Enterprise Agreement attivo e un minimo di 500 abbonamenti a Office 365 Services. Per informazioni dettagliate, contattare il rappresentante Microsoft.
>
>

Un elenco di tutti i geos per Office 365 è disponibile in [Dove si trovano i dati?](https://aka.ms/datamaps).

Le aree geografiche di Office 365 disponibili per Multi-Geo Capabilities sono:

| Area geografica | Valore preferredDataLocation |
| --- | --- |
| Asia Pacifico | APC |
| Australia | AUS |
| Canada | CAN |
| Unione Europa | EUR |
| Francia | FRA |
| India | IND |
| Giappone | JPN |
| Corea del Sud | KOR |
| Sud Africa | ZAF |
| Emirati Arabi Uniti | ARE |
| Regno Unito | GBR |
| Stati Uniti | NAM |

* Se un'area geografica non è elencata nella tabella, ad esempio l'America del Sud, significa che non è possibile usarla per Multi-Geo Capabilities.

* Non tutti i carichi di lavoro di Office 365 supportano l'impostazione di un'area geografica per l'utente.

### <a name="azure-ad-connect-support-for-synchronization"></a>Supporto di Azure AD Connect per la sincronizzazione

Azure AD Connect supporta la sincronizzazione dell'attributo **preferredDataLocation** per gli oggetti **Utente** nella versione 1.1.524.0 e successive. In particolare:

* Lo schema del tipo di oggetto **Utente** in Azure AD Connector è stato esteso per poter includere l'attributo **preferredDataLocation**. L'attributo è di tipo stringa a valore singolo.
* Lo schema del tipo di oggetto **Persona** nel metaverse è stato esteso per poter includere l'attributo **preferredDataLocation**. L'attributo è di tipo stringa a valore singolo.

Per impostazione predefinita, **preferredDataLocation** non è abilitato per la sincronizzazione. Questa funzionalità è destinata alle organizzazioni di grandi dimensioni. Lo schema di Active Directory in Windows Server 2019 ha un attributo **msDS-preferredDataLocation** è necessario utilizzare a questo scopo. Se non è stato aggiornato lo schema di Active Directory e non è possibile farlo, è necessario identificare un attributo per contenere l'area geografica di Office 365 per gli utenti. Si tratta di un attributo diverso per ogni organizzazione.

> [!IMPORTANT]
> Azure AD consente la configurazione diretta dell'attributo **preferredDataLocation** sugli **oggetti Utente cloud** con Azure AD PowerShell. Per configurare questo attributo sugli **oggetti Utente sincronizzati**, è necessario usare Azure AD Connect.

Prima di abilitare la sincronizzazione:

* Se lo schema di Active Directory non è stato aggiornato a 2019, decidere quale attributo di Active Directory locale utilizzare come attributo di origine. Deve essere di tipo **stringa a valore singolo**.
* Se in precedenza è stato configurato l'attributo **preferredDataLocation** sugli **oggetti Utente sincronizzati** esistenti in Azure AD con Azure AD PowerShell, è necessario eseguire il backporting dei valori dell'attributo per gli oggetti **Utente** corrispondenti in Active Directory locale.

    > [!IMPORTANT]
    > Se non si esegue il backporting di questi valori, Azure AD Connect rimuove i valori dell'attributo esistente in Azure AD quando è abilitata la sincronizzazione per l'attributo **preferredDataLocation**.

* Configurare ora l'attributo di origine in almeno due oggetti Utente di Active Directory locale. Lo si potrà usare più avanti per la verifica.

Le sezioni seguenti illustrano la procedura per abilitare la sincronizzazione dell'attributo **preferredDataLocation**.

> [!NOTE]
> I passaggi sono descritti nel contesto di una distribuzione di Azure AD con topologia a foresta singola e senza regole di sincronizzazione personalizzate. Se sono stati configurati una topologia a più foreste e regole di sincronizzazione personalizzate o si dispone di un server di gestione temporanea, modificare i passaggi di conseguenza.

## <a name="step-1-disable-sync-scheduler-and-verify-there-is-no-synchronization-in-progress"></a>Passaggio 1: Disabilitare l'utilità di pianificazione della sincronizzazione e verificare che non ci sia alcuna sincronizzazione in corso
Per evitare l'esportazione di modifiche accidentali in Azure AD, verificare che non venga eseguita alcuna sincronizzazione durante l'aggiornamento delle regole di sincronizzazione. Per disabilitare l'utilità di pianificazione della sincronizzazione predefinita:

1. Avviare una sessione di PowerShell nel server di Azure AD Connect.
2. Disabilitare la sincronizzazione pianificata eseguendo questo cmdlet: `Set-ADSyncScheduler -SyncCycleEnabled $false`.
3. Avviare **Synchronization Service Manager** passando a **START** > **Synchronization Service** (Servizio di sincronizzazione).
4. Selezionare scheda **Operazioni** e verificare che per nessuna operazione lo stato sia *In corso*.

![Screenshot di Synchronization Service Manager](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step1.png)

## <a name="step-2-refresh-the-schema-for-active-directory"></a>Passaggio 2: Aggiornare lo schema per Active Directory
Se è stato aggiornato lo schema di Active Directory a 2019 e Connect è stato installato prima dell'estensione dello schema, quindi la cache dello schema di connessione non dispone dello schema aggiornato. È quindi necessario aggiornare lo schema dalla procedura guidata per visualizzarlo nell'interfaccia utente.

1. Avviare la procedura guidata di Azure AD Connect dal desktop.
2. Selezionare l'opzione **Aggiorna schema directory** e fare clic su **Avanti**.
3. Immettere le credenziali di Azure AD e fare clic su **Avanti**.
4. Nella pagina **Aggiorna schema directory** verificare che tutte le foreste siano selezionate e fare clic su **Avanti**.
5. Al termine, chiudere la procedura guidata.

![Screenshot di Aggiorna schema directory nella procedura guidata Connetti](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-refreshschema.png)

## <a name="step-3-add-the-source-attribute-to-the-on-premises-active-directory-connector-schema"></a>Passaggio 3: Aggiungere l'attributo di origine allo schema di Active Directory Connector localeStep 3: Add the source attribute to the on-premises Active Directory Connector schema
**Questo passaggio è necessario solo se si esegue Connect versione 1.3.21 o precedente. Se si utilizza la versione 1.4.18 o più recente, andare al passaggio 5.**  
Non tutti gli attributi di Azure AD vengono importati nello spazio connettore di Active Directory locale. Se si è scelto di usare un attributo non sincronizzato per impostazione predefinita, è necessario importarlo. Per aggiungere l'attributo di origine all'elenco degli attributi importati:

1. Selezionare la scheda **Connettori** in Synchronization Service Manager.
2. Fare clic con il pulsante destro del mouse sull'istanza di Active Directory Connector locale e scegliere **Proprietà**.
3. Nella finestra di dialogo popup passare alla scheda **Seleziona attributi**.
4. Verificare che l'attributo di origine che si è scelto di usare sia selezionato nell'elenco degli attributi. Se l'attributo non viene visualizzato, selezionare la casella di controllo **Mostra tutto**.
5. Per salvare, selezionare **OK**.

![Screenshot di Synchronization Service Manager e della finestra di dialogo Proprietà](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step2.png)

## <a name="step-4-add-preferreddatalocation-to-the-azure-ad-connector-schema"></a>Passaggio 4: Aggiungere preferredDataLocation allo schema del connettore di Azure ADStep 4: Add **preferredDataLocation** to the Azure AD Connector schema
**Questo passaggio è necessario solo se si esegue Connect versione 1.3.21 o precedente. Se si utilizza la versione 1.4.18 o più recente, andare al passaggio 5.**  
Per impostazione predefinita, l'attributo **preferredDataLocation** non viene importato nello spazio connettore di Azure AD. Per aggiungerlo all'elenco di attributi importati:

1. Selezionare la scheda **Connettori** in Synchronization Service Manager.
2. Fare clic con il pulsante destro del mouse sul connettore di Azure AD e scegliere **Proprietà**.
3. Nella finestra di dialogo popup passare alla scheda **Seleziona attributi**.
4. Selezionare l'attributo **preferredDataLocation** nell'elenco.
5. Per salvare, selezionare **OK**.

![Screenshot di Synchronization Service Manager e della finestra di dialogo Proprietà](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step3.png)

## <a name="step-5-create-an-inbound-synchronization-rule"></a>Passaggio 5: Creare una regola di sincronizzazione in ingressoStep 5: Create an inbound synchronization rule
La regola di sincronizzazione in ingresso consente la trasmissione del valore dell'attributo dall'attributo di origine di Active Directory locale al metaverse.

1. Avviare **Synchronization Rules Editor** (Editor delle regole di sincronizzazione) passando a **START** > **Synchronization Rules Editor** (Editor delle regole di sincronizzazione).
2. Impostare il filtro di ricerca **Direzione** su **In arrivo**.
3. Per creare una nuova regola in entrata, fare clic sul pulsante **Aggiungi nuova regola**.
4. Nella scheda **Descrizione** specificare la configurazione seguente:

    | Attributo | valore | Dettagli |
    | --- | --- | --- |
    | Nome | *Specificare un nome* | Ad esempio, "In entrata da AD - Utente PreferredDataLocation" |
    | Descrizione | *Fornire una descrizione personalizzata* |  |
    | Connected System | *Selezionare l'istanza di Active Directory Connector locale* |  |
    | Connected System Object Type | **Utente** |  |
    | Metaverse Object Type | **Persona** |  |
    | Tipo di collegamento | **Aggiungi** |  |
    | Precedenza | *Scegliere un numero compreso tra 1 e 99* | I numeri compresi tra 1 e 99 sono riservati alle regole di sincronizzazione personalizzate. Non scegliere un valore usato da un'altra regola di sincronizzazione. |

5. Mantenere vuoto il **filtro Ambito,** per includere tutti gli oggetti. Potrebbe essere necessario perfezionare il filtro di ambito in base alla distribuzione di Azure AD Connect.
6. Passare alla **scheda Trasformazione**e implementare la regola di trasformazione seguente:

    | Tipo di flusso | Attributo di destinazione | Source (Sorgente) | Applicare una sola volta | Tipi di unione |
    | --- | --- | --- | --- | --- |
    |Connessione diretta | preferredDataLocation | Selezionare l'attributo di origine | Non selezionato | Aggiornamento |

7. Per creare la regola in entrata, selezionare **Aggiungi**.

![Screenshot della pagina di creazione regola di sincronizzazione in ingresso](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step4.png)

## <a name="step-6-create-an-outbound-synchronization-rule"></a>Passaggio 6: Creare una regola di sincronizzazione in uscitaStep 6: Create an outbound synchronization rule
La regola di sincronizzazione in uscita consente al valore dell'attributo di passare dal metaverse all'attributo preferredDataLocation in Azure AD:The outbound synchronization rule permits the attribute value to flow from the metaverse to the **preferredDataLocation** attribute in Azure AD:

1. Passare all'Editor regole di **sincronizzazione**.
2. Impostare il filtro di ricerca **Direzione** da **In uscita**.
3. Selezionare **Aggiungi nuova regola**.
4. Nella scheda **Descrizione** specificare la configurazione seguente:

    | Attributo | valore | Dettagli |
    | ----- | ------ | --- |
    | Nome | *Specificare un nome* | Ad esempio, "In uscita verso AAD - Utente PreferredDataLocation" |
    | Descrizione | *Fornire una descrizione* ||
    | Connected System | *Selezionare il connettore di Azure AD* ||
    | Connected System Object Type | **Utente** ||
    | Metaverse Object Type | **Persona** ||
    | Tipo di collegamento | **Aggiungi** ||
    | Precedenza | *Scegliere un numero compreso tra 1 e 99* | I numeri compresi tra 1 e 99 sono riservati alle regole di sincronizzazione personalizzate. Non scegliere un valore usato da un'altra regola di sincronizzazione. |

5. Passare alla scheda **Filtro ambito** e aggiungere un singolo gruppo di filtri di ambito con due clausole:

    | Attributo | Operatore | valore |
    | --- | --- | --- |
    | sourceObjectType | EQUAL | Utente |
    | cloudMastered | NOTEQUAL | True |

    L'ambito del filtro determina a quali oggetti di Azure AD viene applicata la regola di sincronizzazione in uscita. In questo esempio viene usato lo stesso filtro di ambito da "Out to Azure AD – User Identity" regola di sincronizzazione OOB (out-of-box). Impedisce che la regola di sincronizzazione venga applicata agli oggetti **utente** non sincronizzati da Active Directory locale. Potrebbe essere necessario perfezionare il filtro di ambito in base alla distribuzione di Azure AD Connect.

6. Passare alla scheda **Trasformazione** e implementare la regola di trasformazione seguente:

    | Tipo di flusso | Attributo di destinazione | Source (Sorgente) | Applicare una sola volta | Tipi di unione |
    | --- | --- | --- | --- | --- |
    | Connessione diretta | preferredDataLocation | preferredDataLocation | Non selezionato | Aggiornamento |

7. Fare clic su **Aggiungi** per creare la regola in uscita.

![Screenshot della pagina di creazione regola di sincronizzazione in uscita](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step5.png)

## <a name="step-7-run-full-synchronization-cycle"></a>Passaggio 7: Eseguire il ciclo di sincronizzazione completoStep 7: Run full synchronization cycle
In generale, un ciclo di sincronizzazione completo è necessario. Il motivo è che sono stati aggiunti nuovi attributi per gli schemi di Active Directory e Azure AD Connector e sono state introdotte regole di sincronizzazione personalizzate. Verificare le modifiche prima di esportarle in Azure AD. È possibile usare la procedura seguente per controllare le modifiche, eseguendo al contempo manualmente i passaggi che compongono il ciclo di sincronizzazione completo.

1. Eseguire un'**importazione completa** nell'istanza di Active Directory Connector locale:

   1. Passare alla scheda **Operazioni** in Synchronization Service Manager.
   2. Fare clic con il pulsante destro del mouse sull'istanza di **Active Directory Connector locale** e scegliere **Esegui**.
   3. Nella finestra di dialogo selezionare **Importazione completa** e fare clic su **OK**.
   4. Attendere il completamento dell'operazione.

      > [!NOTE]
      > È possibile ignorare l'importazione completa in Active Directory Connector locale se l'attributo di origine è già incluso nell'elenco degli attributi importati. In altre parole, non è necessario apportare modifiche durante il passaggio 2 in precedenza in questo articolo.

2. Eseguire un'**importazione completa** in Azure AD Connector:

   1. Fare clic con il pulsante destro del mouse su **Azure AD Connector**e scegliere **Esegui**.
   2. Nella finestra di dialogo selezionare **Importazione completa** e fare clic su **OK**.
   3. Attendere il completamento dell'operazione.

3. Verificare le modifiche alle regole di sincronizzazione in un oggetto **utente** esistente.

   L'attributo di origine di Active Directory locale e **preferredDataLocation** di Azure AD sono stati importati nel rispettivo spazio connettore. Prima di procedere con il passaggio di sincronizzazione completa, è consigliabile eseguire un'operazione di anteprima su un oggetto **Utente** esistente nello spazio Active Directory Connector locale. L'attributo di origine dell'oggetto selezionato deve essere popolato. Un'anteprima corretta che mostra **preferredDataLocation** popolato nel metaverse indica che le regole di sincronizzazione sono state configurate correttamente. Per informazioni sull'esecuzione dell'anteprima, vedere [Verificare la modifica](how-to-connect-sync-change-the-configuration.md#verify-the-change).

4. Eseguire una **sincronizzazione completa** nell'istanza di Active Directory Connector locale:

   1. Fare clic con il pulsante destro del mouse sull'istanza di **Active Directory Connector locale** e scegliere **Esegui**.
   2. Nella finestra di dialogo selezionare **Sincronizzazione completa** e fare clic su **OK**.
   3. Attendere il completamento dell'operazione.

5. Verificare le **esportazioni in sospeso** per Azure AD:

   1. Fare clic con il pulsante destro del mouse su **Connettore Azure AD**e scegliere **Spazio connettore di ricerca**.
   2. Nella finestra di dialogo **Search Connector Space** (Cerca spazio connettore:

        a. Impostare **Ambito** su **Pending Export** (Esportazione in sospeso).<br>
        b. Selezionare tutte e tre le caselle di controllo, tra cui **Aggiungi, Modifica ed Elimina**.<br>
        c. Selezionare **Ricerca** per ottenere l'elenco degli oggetti con le modifiche da esportare. Per esaminare le modifiche per un determinato oggetto, fare doppio clic sull'oggetto.<br>
        d. Verificare le modifiche previste.

6. Eseguire esportazione nel **connettore di Azure ADRun** **Export** on the Azure AD Connector

   1. Fare clic con il pulsante destro del mouse su **Azure AD Connector**e scegliere **Esegui**.
   2. Nella finestra di dialogo **Run Connector** (Esegui connettore) selezionare **Esporta** e fare clic su **OK**.
   3. Attendere il completamento dell'operazione.

> [!NOTE]
> Come si può notare, la procedura non include il passaggio di sincronizzazione completa in Azure AD Connector e quello di esportazione in Active Directory Connector. Questi passaggi non sono necessari perché i valori degli attributi si trasmettono solo da Active Directory locale ad Azure AD.

## <a name="step-8-re-enable-sync-scheduler"></a>Passaggio 8: Riattivare l'utilità di pianificazione della sincronizzazioneStep 8: Re-enable sync scheduler
Riabilitare l'utilità di pianificazione della sincronizzazione predefinita:

1. Avviare una sessione di PowerShell.
2. Riabilitare la sincronizzazione pianificata eseguendo questo cmdlet: `Set-ADSyncScheduler -SyncCycleEnabled $true`

## <a name="step-9-verify-the-result"></a>Passaggio 9: Verificare il risultatoStep 9: Verify the result
A questo punto è necessario verificare la configurazione e abilitarla per gli utenti.

1. Aggiungere l'area all'attributo selezionato per un utente. L'elenco di aree geografiche disponibili è indicato in questa tabella.  
![Screenshot dell'attributo AD aggiunto a un utente](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-adattribute.png)
2. Attendere che l'attributo sia sincronizzato con Azure AD.
3. Usare il servizio PowerShell di Exchange Online per verificare che l'area della cassetta postale sia stata impostata correttamente.  
![Screenshot di PowerShell per Exchange Online](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-mailboxregion.png)  
Supponendo che il tenant sia stato contrassegnato come in grado di usare questa funzionalità, la cassetta postale viene spostata nell'area geografica corretta. Per verificare che ciò avvenga, esaminare il nome del server in cui si trova la cassetta postale.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su Multi-Geo Capabilities in Office 365:

* [Sessioni su Multi-Geo Capabilities in Ignite](https://aka.ms/MultiGeoIgnite)
* [Multi-Geo Capabilities in OneDrive](https://aka.ms/OneDriveMultiGeo)
* [Multi-Geo Capabilities in SharePoint Online](https://aka.ms/SharePointMultiGeo)

Altre informazioni sul modello di configurazione nel motore di sincronizzazione:

* Per altre informazioni sul modello di configurazione, vedere [Servizio di sincronizzazione Azure AD Connect: Informazioni sul provisioning dichiarativo](concept-azure-ad-connect-sync-declarative-provisioning.md).
* Per altre informazioni sul linguaggio delle espressioni, vedere [Servizio di sincronizzazione Azure AD Connect: Informazioni sulle espressioni di provisioning dichiarativo](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md).

Argomenti generali:

* [Servizio di sincronizzazione Azure AD Connect: Comprendere e personalizzare la sincronizzazione](how-to-connect-sync-whatis.md)
* [Integrazione delle identità locali con Azure Active Directory](whatis-hybrid-identity.md)
