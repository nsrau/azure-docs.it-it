---
title: 'Sincronizzazione di Azure AD Connect: gestione degli errori LargeObject causati dall''attributo userCertificate | Microsoft Docs'
description: In questo argomento vengono illustrati i passaggi per la correzione degli errori LargeObject causati dall'attributo userCertificate.
services: active-directory
documentationcenter: 
author: cychua
manager: femila
editor: 
ms.assetid: 146ad5b3-74d9-4a83-b9e8-0973a19828d9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: billmath
ms.translationtype: Human Translation
ms.sourcegitcommit: a1ba750d2be1969bfcd4085a24b0469f72a357ad
ms.openlocfilehash: d15855bb05666961da56ff2dd5e0e473e7f7b123
ms.contentlocale: it-it
ms.lasthandoff: 06/20/2017

---

# <a name="azure-ad-connect-sync-handling-largeobject-errors-caused-by-usercertificate-attribute"></a>Sincronizzazione di Azure AD Connect: gestione degli errori LargeObject causati dall'attributo userCertificate

Azure AD applica un limite massimo di **15** valori del certificato nell'attributo **userCertificate**. Se Azure AD Connect esporta un oggetto con più di 15 valori in Azure AD, Azure AD restituisce un errore **LargeObject** con il messaggio:

>*"L'oggetto di cui è stato eseguito il provisioning presenta dimensioni troppo elevate. Ridurre il numero di valori attributo dell'oggetto. L'operazione sarà ritentata durante il successivo ciclo di sincronizzazione...."*

L'errore LargeObject può essere causato da altri attributi di AD. Per verificare che sia effettivamente causato dall'attributo userCertificate, è necessario confrontarlo con l'oggetto in AD locale o in [Ricerca metaverse di Synchronization Service Manager](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-service-manager-ui-mvsearch).

Per ottenere l'elenco di oggetti nel tenant con gli errori LargeObject, usare uno dei metodi seguenti:

 * Se il tenant è abilitato per la sincronizzazione di Azure AD Connect Health, è possibile fare riferimento al [Report degli errori di sincronizzazione](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health-sync#object-level-synchronization-error-report-preview) indicato.
 
 * La notifica tramite posta elettronica relativa agli errori di sincronizzazione della directory inviata alla fine di ogni ciclo di sincronizzazione contiene l'elenco di oggetti con errori LargeObject. 
 * La [scheda Synchronization Service Manager Operations](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-service-manager-ui-operations) (Operazioni di Synchronization Service Manager) mostra l'elenco di oggetti con gli errori LargeObject se si sceglie l'esportazione più recente per il funzionamento di Azure AD.
 
## <a name="mitigation-options"></a>Opzioni di mitigazione
Finché l'errore LargeObject non viene risolto, non è possibile esportare altre modifiche dell'attributo allo stesso oggetto in Azure AD. Per risolvere l'errore, è possibile considerare le seguenti opzioni:

 * Aggiornare Azure AD Connect alla build 1.1.524.0 o successiva. Nella build 1.1.524.0 di Azure AD Connect, le regole di sincronizzazione predefinite sono state aggiornate in modo da non esportare gli attributi userCertificate e userSMIMECertificate se gli attributi hanno più di 15 valori. Per informazioni dettagliate sull'aggiornamento di Azure AD Connect, vedere l'articolo [Azure AD Connect: Eseguire l'aggiornamento da una versione precedente alla versione più recente](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-upgrade-previous-version).

 * Implementare una **regola di sincronizzazione in uscita** in Azure AD Connect che esporta un **valore null anziché i valori effettivi per gli oggetti con più di 15 valori per certificato**. Questa opzione è appropriata se non è necessario esportare uno dei valori del certificato in Azure AD per gli oggetti con più di 15 valori. Per informazioni dettagliate su come implementare questa regola di sincronizzazione, fare riferimento alla sezione successiva [Implementing sync rule to limit export of userCertificate attribute](#implementing-sync-rule-to-limit-export-of-usercertificate-attribute) (Implementare la regola di sincronizzazione per limitare l'esportazione dell'attributo userCertificate).

 * Ridurre il numero di valori del certificato nell'oggetto AD locale, a 15 o a un numero inferiore, rimuovendo i valori che non sono usati dall'organizzazione. Questa operazione è adeguata se il software boat dell'attributo è dovuto a certificati scaduti o non usati. È possibile usare lo [script PowerShell disponibile qui](https://gallery.technet.microsoft.com/Remove-Expired-Certificates-0517e34f) per trovare, eseguire il backup ed eliminare i certificati scaduti in AD locale. Prima di eliminare i certificati, è consigliabile confrontarsi con gli amministratori dell'infrastruttura a chiave pubblica dell'organizzazione.

 * Configurare Azure AD Connect per escludere l'attributo userCertificate dall'esportazione in Azure AD. In generale, si sconsiglia questa opzione perché l'attributo potrebbe essere usato da Microsoft Online Services per abilitare scenari specifici. In particolare:
    * L'attributo userCertificate nell'oggetto Utente viene usato da Exchange Online e dai client di Outlook per la crittografia e la firma dei messaggi. Per altre informazioni su questa funzionalità, vedere l'articolo [S/MIME per la crittografia e firma dei messaggi](https://technet.microsoft.com/library/dn626158(v=exchg.150).aspx).

    * L'attributo userCertificate sull'oggetto Computer viene usato da Azure AD per consentire ai dispositivi locali aggiunti a un dominio di Windows 10 di connettersi ad Azure AD. Per altre informazioni su questa funzionalità, fare riferimento all'articolo [Connettere dispositivi aggiunti a un dominio ad Azure AD in ambiente Windows 10](https://docs.microsoft.com/azure/active-directory/active-directory-azureadjoin-devices-group-policy).

## <a name="implementing-sync-rule-to-limit-export-of-usercertificate-attribute"></a>Implementazione della regola di sincronizzazione per limitare l'esportazione dell'attributo userCertificate
Per risolvere l'errore LargeObject causato dall'attributo userCertificate, è possibile implementare una regola di sincronizzazione in uscita in Azure AD Connect che esporta un **valore null anziché i valori effettivi per gli oggetti con più di 15 valori per certificato**. In questa sezione viene descritta la procedura necessaria per implementare la regola di sincronizzazione per l'oggetto **Utente**. La procedura può essere adattata per gli oggetti **Contatto** e **Computer**.

> [!IMPORTANT]
> L'esportazione di un valore null rimuove i valori del certificato esportati precedentemente in modo corretto in Azure AD.

La procedura può essere riepilogata nel modo seguente:

1. Disabilitare l'utilità di pianificazione della sincronizzazione e verificare che non ci sia alcuna sincronizzazione in corso.
3. Trovare la regola di sincronizzazione in uscita esistente per l'attributo userCertificate.
4. Creare la regola di sincronizzazione in uscita necessaria.
5. Verificare la nuova regola di sincronizzazione in un oggetto esistente con errore LargeObject.
6. Applicare la nuova regola di sincronizzazione agli oggetti restanti con errore LargeObject.
7. Verificare che non siano presenti modifiche impreviste in attesa di esportazione in Azure AD.
8. Esportare le modifiche in Azure AD.
9. Riattivare l'utilità di pianificazione della sincronizzazione.

### <a name="step-1-disable-sync-scheduler-and-verify-there-is-no-synchronization-in-progress"></a>Passaggio 1. Disabilitare l'utilità di pianificazione della sincronizzazione e verificare che non ci sia alcuna sincronizzazione in corso
Verificare che non venga eseguita alcuna sincronizzazione durante l'implementazione di una nuova regola di sincronizzazione per evitare l'esportazione di modifiche accidentali in Azure AD. Per disabilitare l'utilità di pianificazione della sincronizzazione predefinita:
1. Avviare una sessione di PowerShell nel server di Azure AD Connect.

2. Disabilitare la sincronizzazione pianificata eseguendo di cmdlet: `Set-ADSyncScheduler -SyncCycleEnabled $false`

> [!Note]
> I passaggi precedenti sono applicabili solo alle versioni più recenti (1.1.xxx.x) di Azure AD Connect con l'utilità di pianificazione integrata. Se si usano versioni precedenti (1.0.xxx.x) di Azure AD Connect che usano il servizio Utilità di pianificazione di Windows o se si usa l'utilità di pianificazione personalizzata, non comune, per attivare la sincronizzazione periodica, è necessario disabilitarle di conseguenza.

3. Avviare **Synchronization Service Manager** passando ad AVVIA → Servizio di sincronizzazione.

4. Andare nella scheda **Operazioni** e verificare che nessuna operazione presenti lo stato *"in corso"*.

### <a name="step-2-find-the-existing-outbound-sync-rule-for-usercertificate-attribute"></a>Passaggio 2. Trovare la regola di sincronizzazione in uscita esistente per l'attributo userCertificate
Una regola di sincronizzazione esistente dovrebbe essere abilitata e configurata per l'esportazione dell'attributo userCertificate per gli oggetti Utente in Azure AD. Individuare questa regola di sincronizzazione per scoprirne la configurazione **precedente** e il **filtro ambito**:

1. Avviare l'**editor per le regole di sincronizzazione** passando ad AVVIA → Synchronization Rules Editor (Editor per le regole di sincronizzazione).

2. Configurare i filtri della ricerca con i valori seguenti:

    | Attributo | Valore |
    | --- | --- |
    | Direzione |**Outbound** |
    | MV Object Type |**Person** |
    | Connettore |*nome del connettore Azure AD* |
    | Connector Object Type |**user** |
    | MV attribute |**userCertificate** |

3. Se si usano le regole di sincronizzazione predefinite in Azure AD Connector per esportare l'attributo userCertficiate per gli oggetti Utente, è necessario ritornare alla regola *"Out to AAD – User ExchangeOnline"* ("Per AAD: utente ExchangeOnline").
4. Annotare il valore **precedence** della regola di sincronizzazione.
5. Selezionare la regola di sincronizzazione e fare clic su **Modifica**.
6. Nel finestra di dialogo popup *"Edit Reserved Rule Confirmation"* (Modifica la conferma di regola riservata) fare clic su **No**. Non apportata alcuna modifica a questa regola di sincronizzazione.
7. Nella schermata di modifica, selezionare la scheda **Scoping filter** (Filtro ambito).
8. Annotare la configurazione del filtro ambito. Se si usa la regola di sincronizzazione predefinita, devono essere presenti esattamente **un gruppo di filtri ambito contenente due clausole**, tra cui:

    | Attributo | operatore | Valore |
    | --- | --- | --- |
    | sourceObjectType | EQUAL | Utente |
    | cloudMastered | NOTEQUAL | True  |

### <a name="step-3-create-the-outbound-sync-rule-required"></a>Passaggio 3. Creare la regola di sincronizzazione in uscita necessaria
La nuova regola di sincronizzazione deve avere lo stesso **filtro ambito** e la stessa **priorità elevata** della regola di sincronizzazione esistente. Ciò garantisce che la nuova regola di sincronizzazione si applichi allo stesso set di oggetti della regola di sincronizzazione esistente e sostituisca la regola di sincronizzazione esistente per l'attributo userCertificate. Per creare la regola di sincronizzazione:
1. Nell'editor per le regole di sincronizzazione, fare clic sul pulsante **Aggiungi nuova regola**.
2. Nella **scheda Descrizione**, inserire la configurazione seguente:

    | Attributo | Valore | Dettagli |
    | --- | --- | --- |
    | Nome | *Specificare un nome* | Ad esempio *"Per AAD: esegue l'override personalizzato per userCertificate"* |
    | Descrizione | *Inserire una descrizione* | Ad esempio *"Se l'attributo userCertificate contiene più di 15 valori, esportare NULL".* |
    | Connected System | *Selezionare il connettore di Azure AD* |
    | Connected System Object Type | **user** | |
    | Metaverse Object Type | **person** | |
    | Tipo di collegamento | **Join** | |
    | Precedenza | *Scegliere un numero compreso tra 1 e 99* | Il numero scelto non deve essere usato da una regola di sincronizzazione esistente e deve avere un valore inferiore, e pertanto priorità più alta, rispetto alla regola di sincronizzazione esistente. |

3. Andare nella scheda **Filtro ambito** e implementare lo stesso filtro ambito che usa la regola di sincronizzazione esistente.
4. Ignora la scheda **Join rules** (Regole di unione).
5. Andare nella scheda **Trasformazioni** per aggiungere una nuova trasformazione tramite la configurazione seguente:

    | Attributo | Valore |
    | --- | --- |
    | Flow Type |**Expression** |
    | Target Attribute |**userCertificate** |
    | Source Attribute |*usare l'espressione seguente*: `IIF(IsNullOrEmpty([userCertificate]), NULL, IIF((Count([userCertificate])> 15),AuthoritativeNull,[userCertificate]))` |
    
6. Per creare la regola di sincronizzazione, fare clic sul pulsante **Aggiungi**.

### <a name="step-4-verify-the-new-sync-rule-on-an-existing-object-with-largeobject-error"></a>Passaggio 4. Verificare la nuova regola di sincronizzazione in un oggetto esistente con errore LargeObject
Si tratta di verificare che la regola di sincronizzazione creata funzioni correttamente in un oggetto di AD esistente con errore LargeObject prima di applicarla ad altri oggetti:
1. Passare alla scheda **Operazioni** in Synchronization Service Manager.
2. Selezionare l'operazione di esportazione più recente in Azure AD e fare clic su uno degli oggetti con errori LargeObject.
3.  Nella schermata di popup Connector Space Object Properties (Proprietà dell'oggetto spazio connettore) fare clic sul pulsante **Anteprima**.
4. Nella schermata di popup Anteprima selezionare **Sincronizzazione completa** e fare clic su **Anteprima commit**.
5. Chiudere la schermata Anteprima e la schermata Connector Space Object Properties (Proprietà dell'oggetto spazio connettore).
6. Passare alla scheda **Connettori** in Synchronization Service Manager.
7. Fare clic con il tasto destro del mouse sul connettore di **Azure AD** e selezionare **Esegui...**
8. Nella finestra popup Run Connector (Esegui connettore) selezionare il passaggio **Esporta** e fare clic su **OK**.
9. Attendere il completamento dell'esportazione in Azure AD e confermare che non siano presenti altri errori LargeObject in questo oggetto specifico.

### <a name="step-5-apply-the-new-sync-rule-to-remaining-objects-with-largeobject-error"></a>Passaggio 5. Applicare la nuova regola di sincronizzazione agli oggetti restanti con errore LargeObject
Dopo aver aggiunto la regola di sincronizzazione, è necessario eseguire un passaggio di sincronizzazione completa per il connettore AD:
1. Passare alla scheda **Connettori** in Synchronization Service Manager.
2. Fare clic con il tasto destro del mouse sul connettore **AD** e selezionare **Esegui...**
3. Nella finestra popup Run Connector (Esegui connettore) selezionare il passaggio **Sincronizzazione completa** e fare clic su **OK**.
4. Attendere il completamento del passaggio Sincronizzazione completa.
5. Ripetere i passaggi precedenti per i connettori AD restanti se si dispone di più connettori AD. In genere, sono necessari più connettori se si dispone di più directory locali.

### <a name="step-6-verify-there-are-no-unexpected-changes-waiting-to-be-exported-to-azure-ad"></a>Passaggio 6. Verificare che non siano presenti modifiche impreviste in attesa di esportazione in Azure AD
1. Passare alla scheda **Connettori** in Synchronization Service Manager.
2. Fare clic con il tasto destro del mouse sul connettore di **Azure AD** e selezionare **Search Connector Space** (Spazio connettore di ricerca).
3. Nella finestra popup Search Connector Space (Spazio connettore di ricerca):
    1. Impostare Ambito su **Pending Export** (Esportazione in sospeso).
    2. Selezionare tutte e 3 le caselle di controllo, tra cui **Aggiungi**, **Modifica** ed **Elimina**.
    3. Fare clic sul pulsante **Cerca** per restituire tutti gli oggetti con modifiche in attesa di essere esportati in Azure AD.
    4. Verificare che non siano presenti modifiche impreviste. Per esaminare le modifiche per un determinato oggetto, fare doppio clic sull'oggetto.

### <a name="step-7-export-the-changes-to-azure-ad"></a>Passaggio 7. Esportare le modifiche in Azure AD
Per esportare le modifiche in Azure AD:
1. Passare alla scheda **Connettori** in Synchronization Service Manager.
2. Fare clic con il tasto destro del mouse sul connettore di **Azure AD** e selezionare **Esegui...**
4. Nella finestra popup Run Connector (Esegui connettore) selezionare il passaggio **Esporta** e fare clic su **OK**.
5. Attendere il completamento dell'esportazione in Azure AD e confermare che non siano presenti altri errori LargeObject.

### <a name="step-8-re-enable-sync-scheduler"></a>Passaggio 8. Riattivare l'utilità di pianificazione della sincronizzazione
Dopo aver risolto il problema, abilitare nuovamente l'utilità di pianificazione della sincronizzazione predefinita:
1. Avviare una sessione di PowerShell.
2. Riabilitare la sincronizzazione pianificata eseguendo di cmdlet: `Set-ADSyncScheduler -SyncCycleEnabled $true`

> [!Note]
> I passaggi precedenti sono applicabili solo alle versioni più recenti (1.1.xxx.x) di Azure AD Connect con l'utilità di pianificazione integrata. Se si usano versioni precedenti (1.0.xxx.x) di Azure AD Connect che usano il servizio Utilità di pianificazione di Windows o se si usa l'utilità di pianificazione personalizzata, non comune, per attivare la sincronizzazione periodica, è necessario disabilitarle di conseguenza.

## <a name="next-steps"></a>Passaggi successivi
Altre informazioni su [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md).


