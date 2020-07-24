---
title: Come importare ed esportare le impostazioni di configurazione Azure AD Connect
description: Questo documento include le domande frequenti per il provisioning cloud.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/13/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a94d356cb3c0345f575b4b5a44aa7f228535e66d
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87019880"
---
# <a name="importing-and-exporting-azure-ad-connect-configuration-settings-public-preview"></a>Importazione ed esportazione delle impostazioni di configurazione Azure AD Connect (anteprima pubblica) 

Azure AD Connect le distribuzioni variano da un'installazione in modalità Express a foresta singola, a distribuzioni complesse che si sincronizzano tra più foreste usando regole di sincronizzazione personalizzate. A causa dell'elevato numero di opzioni e meccanismi di configurazione, è essenziale comprendere quali impostazioni sono attive ed essere in grado di distribuire rapidamente un server con una configurazione identica. Questa funzionalità introduce la possibilità di catalogare la configurazione di un determinato server di sincronizzazione e di importare le impostazioni in una nuova distribuzione. È possibile confrontare snapshot di impostazioni di sincronizzazione diversi per visualizzare facilmente le differenze tra due server o lo stesso server nel tempo. 

Ogni volta che la configurazione viene modificata dalla procedura guidata Azure AD Connect, un nuovo file di impostazioni JSON timestamp viene esportato automaticamente in **%ProgramData%\AADConnect**. Il formato del nome del file delle impostazioni è **Applied-SynchronizationPolicy-*. JSON** in cui l'ultima parte del nome file è un timestamp. 

> [!IMPORTANT]
> Vengono esportate automaticamente solo le modifiche apportate da Azure AD Connect. Tutte le modifiche apportate tramite PowerShell, il Synchronization Service Manager o l'editor delle regole di sincronizzazione devono essere esportate su richiesta, in base alle esigenze, per mantenere una copia aggiornata. L'esportazione su richiesta può essere usata anche per inserire una copia delle impostazioni in un luogo sicuro a scopo di ripristino di emergenza. 

## <a name="exporting-azure-ad-connect-settings"></a>Esportazione delle impostazioni di Azure AD Connect 

Per visualizzare un riepilogo delle impostazioni di configurazione, aprire lo strumento Azure AD Connect e scegliere l'attività aggiuntiva denominata: Visualizza o Esporta configurazione corrente. Viene visualizzato un breve riepilogo delle impostazioni insieme alla possibilità di esportare la configurazione completa del server. 

Per impostazione predefinita, le impostazioni verranno esportate in **%ProgramData%\AADConnect**, ma è possibile scegliere di salvare le impostazioni in un percorso protetto per garantire la disponibilità in caso di emergenza. Le impostazioni vengono esportate usando il formato di file JSON e non devono essere create o modificate manualmente per garantire la coerenza logica. L'importazione del file creato o modificato manualmente non è supportata e può causare risultati imprevisti. 

## <a name="importing-azure-ad-connect-settings"></a>Importazione delle impostazioni di Azure AD Connect 

Per importare le impostazioni esportate in precedenza, eseguire le operazioni seguenti:
 
1. Installare **Azure ad Connect** in un nuovo server. 
2. Selezionare **Personalizza** opzione dopo la pagina **iniziale** . 
3. Fare clic su **Importa impostazioni di sincronizzazione**. Cercare il file di impostazioni JSON esportato in precedenza.  
4. Fare clic su **Installa**.

![Installazione dei componenti](media/how-to-connect-import-export-config/import1.png)

> [!NOTE]
> Eseguire l'override delle impostazioni in questa pagina, ad esempio l'uso di SQL Server anziché il database locale o l'uso di un account del servizio esistente anziché di un VSA predefinito e così via. Queste impostazioni non vengono importate dal file delle impostazioni di configurazione, ma sono disponibili a scopo informativo e di confronto.

### <a name="import-installation-experience"></a>Importa l'esperienza di installazione 

L'esperienza di installazione dell'importazione viene intenzionalmente mantenuta semplice con input minimi dall'utente per semplificare la riproducibilità di un server esistente.  

Ecco le uniche modifiche che possono essere apportate durante l'esperienza di installazione. Tutte le altre modifiche possono essere apportate dopo l'installazione dalla procedura guidata Azure AD Connect.: 
- **Credenziali Azure Active Directory**   : il nome dell'account per l'amministratore globale di Azure usato per configurare il server originale è suggerito per impostazione predefinita e **deve**   essere modificato se si vuole sincronizzare le informazioni in una nuova directory.
- **Accesso utente**   : le opzioni di accesso configurate per il server originale sono selezionate per impostazione predefinita e richiederanno automaticamente le credenziali o altre informazioni necessarie durante la configurazione. In rari casi, potrebbe essere necessario configurare un server con diverse opzioni per evitare di modificare il comportamento del server attivo. In caso contrario, è sufficiente fare clic su Avanti per usare le stesse impostazioni. 
- Credenziali della directory **locale**   -per ogni directory locale inclusa nelle impostazioni di sincronizzazione, è necessario fornire le credenziali per creare un account di sincronizzazione o fornire un account di sincronizzazione personalizzato creato in precedenza. Questa procedura è identica all'esperienza di installazione pulita, con l'eccezione che non è possibile aggiungere o rimuovere directory. 
- **Opzioni**   di configurazione -come con un'installazione pulita, è possibile scegliere di configurare le impostazioni iniziali per l'avvio della sincronizzazione automatica o l'abilitazione della modalità di staging. La differenza principale è che la modalità di gestione temporanea è intenzionalmente abilitata per impostazione predefinita per consentire il confronto dei risultati della configurazione e della sincronizzazione prima di esportare attivamente i risultati in Azure. 

![Connessione delle directory](media/how-to-connect-import-export-config/import2.png)

> [!NOTE]
> Solo un server di sincronizzazione può trovarsi nel ruolo primario ed esportare attivamente le modifiche di configurazione in Azure. Tutti gli altri server devono essere posizionati in modalità di staging. 

## <a name="migrating-settings-from-an-existing-server"></a>Migrazione delle impostazioni da un server esistente 

Se un server esistente non supporta la gestione delle impostazioni, è possibile scegliere di aggiornare il server sul posto o eseguire la migrazione delle impostazioni per l'utilizzo in un nuovo server di staging.  

Per la migrazione è necessario eseguire uno script di PowerShell che estrae le impostazioni esistenti per l'utilizzo in una nuova installazione.Questo metodo è consigliato per catalogare le impostazioni del server esistente, quindi applicarle a un sever di gestione temporanea appena installato.Il confronto tra le impostazioni per il server originale e il server appena creato consente di visualizzare rapidamente le modifiche tra i server.Come sempre, seguire il processo di certificazione dell'organizzazione per assicurarsi che non sia necessaria alcuna configurazione aggiuntiva.  

### <a name="migration-process"></a>Processo di migrazione 
Per eseguire la migrazione delle impostazioni, eseguire le operazioni seguenti:

1. Avviare **AzureADConnect.msi** nel nuovo server di staging e arrestare la pagina iniziale di Azure ad Connect.

2. Copiare **MigrateSettings.ps1** dalla directory Microsoft Azure ad Connect\Tools in un percorso nel server esistente.  Ad esempio, C:\setup.  Dove il programma di installazione è una directory creata nel server esistente.

   ![Connessione delle directory](media/how-to-connect-import-export-config/migrate1.png)

3. Eseguire lo script come illustrato di seguito e salvare l'intera directory di configurazione del server di livello inferiore. Copiare questa directory nel nuovo server di gestione temporanea. Si noti che è necessario copiare l'intera cartella **esportata-ServerConfiguration-*** nel nuovo server.

   ![](media/how-to-connect-import-export-config/migrate2.png)
   Directory Connect directory Connect ![](media/how-to-connect-import-export-config/migrate3.png)

5. Avviare **Azure ad Connect** facendo doppio clic sull'icona sul desktop. Accettare il contratto di licenza, nella pagina successiva fare clic sul pulsante **Personalizza** . 
6. Selezionare la casella di controllo **Importa impostazioni di sincronizzazione** e fare clic sul pulsante **Sfoglia** per visualizzare la cartella copiato over esportated-ServerConfiguration-* e selezionare il MigratedPolicy.jsin per importare le impostazioni migrate.

   ![Connessione delle directory](media/how-to-connect-import-export-config/migrate4.png)

7. Per confrontare le impostazioni migrate con quelle delle impostazioni applicate, cercare la versione più recente di **migrated-SynchronizationPolicy-*. JSON** e **Applied-SynchronizationPolicy-*. JSON** (* è il timestamp) in **%ProgramData%\AADConnect**. Usare lo strumento di confronto dei file preferito per confrontare la parità. 

## <a name="post-installation-verification"></a>Verifica post-installazione 

Il confronto tra il file di impostazioni originariamente importato, con il file di impostazioni esportato, del server appena distribuito è un passaggio essenziale per comprendere le differenze tra il previsto, rispetto alla distribuzione risultante. L'uso dell'applicazione di confronto di testo affiancata preferita produce una visualizzazione immediata che evidenzia rapidamente eventuali modifiche desiderate o accidentali. Sebbene molti passaggi di configurazione precedentemente manuali vengano eliminati, è comunque necessario seguire il processo di certificazione dell'organizzazione per assicurarsi che non siano necessarie configurazioni aggiuntive. Questa configurazione può verificarsi se si sfruttano le impostazioni avanzate, che non sono attualmente acquisite nella versione di anteprima pubblica di gestione impostazioni. 

Le limitazioni note includono quanto segue: 
- **Regole**   di sincronizzazione -la precedenza per una regola personalizzata deve essere compresa nell'intervallo riservato di 0-99 per evitare conflitti con le regole standard di Microsoft. Quando si inserisce una regola personalizzata al di fuori dell'intervallo riservato, è possibile che la regola personalizzata venga spostata in base alle regole standard aggiunte alla configurazione. Se la configurazione contiene regole standard modificate, si verificherà un problema simile. La modifica di una regola standard è fortemente sconsigliata e il posizionamento delle regole potrebbe non essere corretto. 
- **Writeback**   dei dispositivi : queste impostazioni sono catalogate, ma non sono attualmente applicate durante la configurazione. Se il writeback dei dispositivi è stato abilitato per il server originale, è necessario configurare manualmente la funzionalità nel server appena distribuito. 
- Tipi di oggetti **sincronizzati**   -Sebbene sia possibile vincolare l'elenco di tipi di oggetti sincronizzati (utenti, contatti, gruppi e così via) usando il Synchronization Service Manager, questa funzionalità non è attualmente supportata tramite le impostazioni di sincronizzazione. Al termine dell'installazione, è necessario riapplicare manualmente la configurazione avanzata. 
- Profili di esecuzione **personalizzati**   -Sebbene sia possibile modificare il set predefinito di profili di esecuzione usando il Synchronization Service Manager, questa funzionalità non è attualmente supportata tramite le impostazioni di sincronizzazione. Al termine dell'installazione, è necessario riapplicare manualmente la configurazione avanzata. 
- **Configurazione della gerarchia**   di provisioning : questa funzionalità avanzata del Synchronization Service Manager non è supportata tramite le impostazioni di sincronizzazione e deve essere riconfigurata manualmente dopo aver completato la distribuzione iniziale. 
- Autenticazione AD FS e **PingFederate**   -i metodi di accesso associati a queste funzionalità di autenticazione verranno automaticamente preselezionati, tuttavia è necessario fornire in modo interattivo tutti gli altri parametri di configurazione necessari. 
- **Una regola di sincronizzazione personalizzata disabilitata verrà importata come abilitata** Una regola di sincronizzazione personalizzata disabilitata verrà importata come abilitata. Assicurarsi di disabilitarlo anche nel nuovo server.

 ## <a name="next-steps"></a>Passaggi successivi

- [Hardware e prerequisiti](how-to-connect-install-prerequisites.md) 
- [Impostazioni rapide](how-to-connect-install-express.md)
- [Impostazioni personalizzate](how-to-connect-install-custom.md)
- [Installare gli agenti di Azure AD Connect Health](how-to-connect-health-agent-install.md) 
