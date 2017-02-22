---
title: 'Azure AD Connect: Eseguire l&quot;aggiornamento da una versione precedente | Documentazione Microsoft'
description: "Illustra i diversi metodi per eseguire l&quot;aggiornamento alla versione più recente di Azure Active Directory Connect, tra cui l&quot;aggiornamento sul posto e la migrazione swing."
services: active-directory
documentationcenter: 
author: AndKjell
manager: femila
editor: 
ms.assetid: 31f084d8-2b89-478c-9079-76cf92e6618f
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Identity
ms.date: 02/08/2017
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: 1f7ec5d53512dcfbff17269802c8889eae0ad744
ms.openlocfilehash: 5dd69a0b9357a601070765817a814dae3e7e5c05


---
# <a name="azure-ad-connect-upgrade-from-a-previous-version-to-the-latest"></a>Azure AD Connect: Eseguire l'aggiornamento da una versione precedente alla versione più recente
Questo argomento descrive i diversi metodi che è possibile usare per aggiornare l'installazione di Azure AD Connect alla versione più recente. È consigliabile mantenersi sempre al passo con le versioni di Azure AD Connect. I passaggi descritti nella sezione [Migrazione swing](#swing-migration) vengono usati anche quando si apportano modifiche sostanziali alla configurazione.

Se si vuole eseguire l'aggiornamento da DirSync, vedere invece l'argomento relativo all'[aggiornamento dallo strumento di sincronizzazione di Azure AD (DirSync)](active-directory-aadconnect-dirsync-upgrade-get-started.md) .

Esistono diverse strategie per eseguire l'aggiornamento di Azure AD Connect.

| Metodo | Descrizione |
| --- | --- |
| [Aggiornamento automatico](active-directory-aadconnect-feature-automatic-upgrade.md) |Si tratta del metodo più semplice per i clienti con installazione rapida. |
| [Aggiornamento sul posto](#in-place-upgrade) |Se si ha un singolo server, eseguire un aggiornamento sul posto dell'installazione nello stesso server. |
| [Migrazione swing](#swing-migration) |Se si usano due server, è possibile preparare uno dei due con la nuova versione o configurazione e, quando si è pronti, modificare il server attivo. |

Per informazioni, vedere l'articolo relativo alle [autorizzazioni necessarie per l'aggiornamento](active-directory-aadconnect-accounts-permissions.md#upgrade).

> [!NOTE]
> Dopo avere consentito al nuovo server Azure AD Connect di avviare la sincronizzazione delle modifiche ad Azure AD, non si deve più usare DirSync o Azure AD Sync. Il downgrade da Azure AD Connect ai client legacy, inclusi DirSync e Azure AD Sync, non è supportato e può causare problemi come la perdita di dati in Azure AD. 
> 
> 

## <a name="in-place-upgrade"></a>Aggiornamento sul posto
Un aggiornamento sul posto è indicato per il passaggio da Azure AD Sync o Azure AD Connect. L'aggiornamento sul posto non è indicato per DirSync o per una soluzione con FIM + Azure AD Connector.

Questo metodo è preferibile quando sono presenti un singolo server e meno di 100.000 oggetti. Se sono state apportate modifiche alle regole di sincronizzazione predefinite, vengono eseguite un'importazione e una sincronizzazione complete. In questo modo, la nuova configurazione viene applicata a tutti gli oggetti presenti nel sistema. Questa operazione può richiedere alcune ore, a seconda del numero di oggetti nell'ambito del motore di sincronizzazione. Viene sospesa la normale sincronizzazione delta pianificata, per impostazione predefinita ogni 30 minuti, mentre continua la sincronizzazione delle password. È consigliabile eseguire l'aggiornamento sul posto durante il fine settimana. Se sono state apportate modifiche alla configurazione predefinita con la nuova versione di Azure AD Connect, verrà avviata un'importazione/sincronizzazione differenziale normale.  
![Aggiornamento sul posto](./media/active-directory-aadconnect-upgrade-previous-version/inplaceupgrade.png)

Se sono state apportate modifiche alle regole di sincronizzazione predefinite, verranno reimpostate sui valori predefiniti della configurazione al momento dell'aggiornamento. Per assicurarsi che la configurazione venga mantenuta tra un aggiornamento e l'altro, verificare che le modifiche vengano apportate come descritto nell'argomento relativo alle [procedure consigliate per modificare la configurazione predefinita](active-directory-aadconnectsync-best-practices-changing-default-configuration.md).

## <a name="swing-migration"></a>Migrazione swing
Se è presente una distribuzione complessa o molti oggetti, può essere impraticabile eseguire un aggiornamento sul posto nel sistema attivo. Per alcuni clienti questa operazione può richiedere più giorni, durante i quali non verranno elaborate modifiche delta. Questo metodo viene usato anche quando si pianificano modifiche sostanziali della configurazione e si vogliono provare tali modifiche prima di effettuarne il push nel cloud.

Il metodo consigliato per questi scenari è una migrazione swing. Sono necessari almeno due server, uno attivo e uno di gestione temporanea. Il server attivo (linee blu continue nell'immagine seguente) è responsabile del carico di produzione attivo. Il server di gestione temporanea (linee viola tratteggiate nell'immagine seguente) viene preparato con la nuova versione o configurazione e, una volta completata la preparazione, viene reso attivo. Il server precedentemente attivo, su cui ora è installata la versione o configurazione precedente, diventa il server di gestione temporanea e viene aggiornato.

I due server possono usare versioni diverse. Ad esempio, il server attivo per il quale è stata pianificata la rimozione delle autorizzazioni può usare Azure AD Sync, mentre il nuovo server di gestione temporanea può usare Azure AD Connect. Se si usa la migrazione swing per sviluppare una nuova configurazione, è consigliabile usare la stessa versione sui due server.  
![server di gestione temporanea](./media/active-directory-aadconnect-upgrade-previous-version/stagingserver1.png)

Nota: si è notato che, per questo scenario, alcuni clienti preferiscono avere tre o quattro server. Quando il server di gestione temporanea viene aggiornato, in caso di [ripristino di emergenza](active-directory-aadconnectsync-operations.md#disaster-recovery) non è disponibile un server di backup. Con tre o quattro server è possibile preparare un nuovo set di server primari o di standby con la nuova versione, per assicurare che sia sempre presente un server di gestione temporanea pronto a sostituire il server attivo.

Questa procedura è indicata anche per il passaggio da Azure AD Sync o da una soluzione con FIM + Azure AD Connector. Questa procedura non è indicata per DirSync, ma nell'argomento [Aggiornamento del servizio di sincronizzazione di Microsoft Azure Active Directory (DirSync)](active-directory-aadconnect-dirsync-upgrade-get-started.md) è disponibile lo stesso metodo con migrazione swing, chiamato anche distribuzione parallela, con i passaggi per DirSync.

### <a name="swing-migration-steps"></a>Passaggi della migrazione swing
1. Se si usa Azure AD Connect in entrambi i server e si pianifica di eseguire soltanto una modifica della configurazione, assicurarsi che il server attivo e il server di gestione temporanea usino entrambi la stessa versione. Questo accorgimento rende più facile un successivo confronto delle differenze. Se si esegue l'aggiornamento da Azure AD Sync, questi server hanno versioni diverse. Se si esegue l'aggiornamento da una versione di Azure AD Connect, è consigliabile che i due server usino la stessa versione. Non si tratta tuttavia di un requisito obbligatorio.
2. Se è stata eseguita una configurazione personalizzata che non è presente nel server di gestione temporanea, seguire i passaggi descritti in [Spostare la configurazione personalizzata dal server attivo al server di gestione temporanea](#move-custom-configuration-from-active-to-staging-server).
3. Se si esegue l'aggiornamento da una versione precedente di Azure AD Connect, aggiornare il server di gestione temporanea alla versione più recente. Se si esegue lo spostamento da Azure AD Sync, installare Azure AD Connect nel server di gestione temporanea.
4. Consentire al motore di sincronizzazione di eseguire un'importazione completa e una sincronizzazione completa nel server di gestione temporanea.
5. Verificare che la nuova configurazione non abbia causato modifiche impreviste seguendo i passaggi descritti nella sezione **Verificare** di [Verificare la configurazione di un server](active-directory-aadconnectsync-operations.md#verify-the-configuration-of-a-server). Se alcuni elementi non funzionano come previsto, correggerli, eseguire l'importazione e la sincronizzazione, quindi verificare che i dati siano corretti. Questi passaggi sono disponibili nell'argomento collegato.
6. Impostare il server di gestione temporanea come server attivo. Questo è il passaggio finale, dal titolo **Cambiare il server attivo** , di [Verificare la configurazione di un server](active-directory-aadconnectsync-operations.md#verify-the-configuration-of-a-server).
7. Se si esegue l'aggiornamento di Azure AD Connect, eseguire ora l'aggiornamento del server in modalità di gestione temporanea alla versione più recente. Seguire gli stessi passaggi descritti in precedenza per aggiornare i dati e la configurazione. Se si esegue l'aggiornamento da Azure AD Sync, è possibile disattivare e rimuovere le autorizzazioni dal server precedente.

### <a name="move-custom-configuration-from-active-to-staging-server"></a>Spostare la configurazione personalizzata dal server attivo al server di gestione temporanea
Se sono state apportate modifiche di configurazione al server attivo, è necessario assicurarsi che le stesse modifiche vengano applicate al server di gestione temporanea.

Le regole di sincronizzazione personalizzate create possono essere spostate con PowerShell. Qualsiasi altra modifica deve essere applicata nello stesso modo in entrambi i sistemi e non è possibile eseguirne la migrazione.

Elementi che devono essere configurati allo stesso modo in entrambi i server:

* Connessione alle stesse foreste.
* Domini e filtri unità organizzativa.
* Stesse funzionalità facoltative, come la sincronizzazione e il writeback delle password.

**Spostare le regole di sincronizzazione**  
Per spostare una regola di sincronizzazione personalizzata, seguire questa procedura:

1. Aprire l' **editor delle regole di sincronizzazione** nel server attivo.
2. Selezionare la regola personalizzata. Fare clic su **Esporta**. Viene visualizzata una finestra del Blocco note. Salvare il file temporaneo con estensione ps1. In questo modo diventa uno script PowerShell. Copiare il file con estensione ps1 nel server di gestione temporanea.  
   ![Esportazione delle regole di sincronizzazione](./media/active-directory-aadconnect-upgrade-previous-version/exportrule.png)
3. Il GUID del connettore è diverso nel server di gestione temporanea e deve essere modificato. Per ottenere il GUID, avviare l'**editor di sincronizzazione delle regole**, selezionare una delle regole predefinite che rappresentano lo stesso sistema connesso e fare clic sul pulsante per l'**esportazione**. Sostituire il GUID nel file con estensione ps1 con il GUID del server di gestione temporanea.
4. In un prompt dei comandi di PowerShell eseguire il file con estensione ps1. In questo modo verrà creata la regola di sincronizzazione personalizzata nel server di gestione temporanea.
5. Se sono presenti più regole personalizzate, ripetere la procedura per tutte le regole personalizzate.

## <a name="next-steps"></a>Passaggi successivi
Altre informazioni su [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md).




<!--HONumber=Jan17_HO4-->


